---
layout: post
title: "Improving 3D performance in WPF"
---

<p>I was able to significantly improve the performance of the 
    <a href="http://kindohm.com/archive/2006/02/02/USAInDDD.aspx">USA 
        3D model</a> by reducing         the number of discrete 3D 
        models being rendered in the vieport.  Originally, I was using         
        the approach in my 
        <a href="http://www.kindohm.com/technical/WPF3DTutorial.htm">3D 
            Tutorial</a>, which involves         
            creating a 3D triangle model for each triangle in the mesh.  
            While this approach makes it very easy to build         
            composite 3D models (by combining many 3D models together), 
            it is very slow for complex models.  For example, the state 
            of Minnesota has         almost 5200 individual latitude and 
            longitude points defined for it, which amounts to 5200 triangles 
            on the top side,         5200 on the bottom side, and 10,400 
            triangles for the north/south/east/west sides.  That's 20,800 
            total 3D models!         With my new approach, I build only 
            <strong>one</strong> 3D model per state, but use a more complex 
            mesh.  The old         approach had one triangle per mesh.  
            Now I'm putting <strong>all</strong> of the triangles in one 
            mesh.  So let's get         into some details.</p>


                  
<p><strong>Disclaimer - </strong>  I'm not going to get into any fundamentals of 3D modeling with WPF in this text.  I'm          purposely going to skip over what things like MeshGeometry3Ds , viewports, normals, right-hand rules, and triangle         indeces are.  The 
    <a href="http://www.kindohm.com/technical/WPF3DTutorial.htm">3D Tutorial</a> will help a little with         those topics.</p>





















                  
<p>A state's cartographic data is defined by some vertices around its perimeter and a center vertex.  Take this         simplified Minnesota for example:</p>





















                  
<p style="text-align: center;">         <img src="http://www.kindohm.com/localimages/3drefactor/MinnesotaPoints.gif" alt="Minnesota Points" /><br />         <em>Minnesota Points</em>         </p>





















                  
<p>In this example, there are six perimeter points and one center point.  The triangle mesh          would look like this:</p>





















         
<p style="text-align: center;">             <img src="http://www.kindohm.com/localimages/3drefactor/MinnesotaTris.gif" alt="Minnesota Triangles" /><br />         <em>Minnesota Triangles</em>         </p>





















                  
<p>The right hand rule idea still applies from the tutorial.  The triangles will need to be defined in         a counter-clockwise direction so that the visible surface faces up:</p>





















                      
<pre><code>
Triangle 1: {pC, p2, p1}
Triangle 2: {pC, p3, p2}
Triangle 3: {pC, p4, p3}
Triangle 4: {pC, p5, p4}
Triangle 5: {pC, p6, p5}</code>
            </pre>
                          
<p>In code, with a simple sorted list of points, it's pretty easy to define these triangles.  The catch         is that I want the mesh to be a bit more complex by adding some thickness in the Y direction:</p>





















                  
<p style="text-align: center;">         <img src="http://www.kindohm.com/localimages/3drefactor/TopbottomPoints.gif" alt="Top and Bottom points" /><br />         <em>Top and Bottom Points</em>         </p>





















         
<p>pCT is the top center point and pCB is the bottom center point.  The bottom points are easy to obtain         by just subtracting a thickness value in the Y direction from the original points.</p>





















         
<p>Now the mesh is looking a little more complex with triangles on the top, bottom, and sides:</p>





















         
<p style="text-align: center;"><img src="http://www.kindohm.com/localimages/3drefactor/MNTrianglesTop.gif" alt="Top Triangles" /><br />         <em>Top Triangles</em></p>





















<hr style="width: 250px;" />         
<p style="text-align: center;"><img src="http://www.kindohm.com/localimages/3drefactor/MNTrianglesBottom.gif" alt="Bottom Triangles" /><br />         <em>Bottom Triangles</em></p>





















<hr style="width: 250px;" />         
<p style="text-align: center;"><img src="http://www.kindohm.com/localimages/3drefactor/MNTrianglesSides.gif" alt="Side Triangles" /><br />         
<em>Side Triangles</em></p>





















                  
<p>The purpose of showing these points and triangles is to help visualize a pattern that can be         used to enumerate through the vertices and create the triangles in the mesh as efficiently as possible.           With each point around the perimeter, four triangles in the mesh can be created.  Take p1 for example.  If we         can safely assume that the vertices are ordered correctly in a collection, the following triangles can be         created from a collection of vertices when we get to the index of p1 during the enumeration:</p>





















<pre><code>Triangle 1 (top): {pCT, p2, p1}
Triangle 2 (bottom): {pCB, p8, p7}
Triangle 3 (side 1): {p1, p2, p7}
Triangle 4 (side 2): {p7, p2, p8}
</code></pre>        
<p>This can be simplified.  We can safely assume that the bottom points are the same as the top points - except they have a different Y axis value.  The bottom points can be notated with a "b" subscript:</p>





















<pre><code>Triangle 1 (top): {pC, p2, p1}
Triangle 2 (bottom): {pC<span style="font-size: 80%; vertical-align: sub;">b</span>, p2<span style="font-size: 80%; vertical-align: sub;">b</span>, p1<span style="font-size: 80%; vertical-align: sub;">b</span>}
Triangle 3 (side 1): {p1, p2, p1<span style="font-size: 80%; vertical-align: sub;">b</span>}
Triangle 4 (side 2): {p1<span style="font-size: 80%; vertical-align: sub;">b</span>, p2, p2<span style="font-size: 80%; vertical-align: sub;">b</span>}
</code></pre>        
<p>We can finally define all four triangles at any index "i" in the enumeration as:</p>





















  
<pre><code>index i
index values: {1, 2, ... n}

When i &lt; n:

    Triangle 1 (top): {pC, p[i+1], p[i]}
    Triangle 2 (bottom): {pC<span style="font-size: 80%; vertical-align: sub;">b</span>, p[i+1]<span style="font-size: 80%; vertical-align: sub;">b</span>, p[i]<span style="font-size: 80%; vertical-align: sub;">b</span>}
    Triangle 3 (side 1): {p[i], p[i+1], p[i]<span style="font-size: 80%; vertical-align: sub;">b</span>}
    Triangle 4 (side 2): {p[i]<span style="font-size: 80%; vertical-align: sub;">b</span>, p[i+1], p[i+1]<span style="font-size: 80%; vertical-align: sub;">b</span>}

When i == n:

    Triangle 1 (top): {pC, p[1], p[i]}
    Triangle 2 (bottom): {pC<span style="font-size: 80%; vertical-align: sub;">b</span>, p[1]<span style="font-size: 80%; vertical-align: sub;">b</span>, p[i]<span style="font-size: 80%; vertical-align: sub;">b</span>}
    Triangle 3 (side 1): {p[i], p[1], p[i]<span style="font-size: 80%; vertical-align: sub;">b</span>}
    Triangle 4 (side 2): {p[i]<span style="font-size: 80%; vertical-align: sub;">b</span>, p[1], p[1]<span style="font-size: 80%; vertical-align: sub;">b</span>}
</code></pre>        
<p>I've chosen to implement the model generation with three methods.  The first adds a single triangle to the mesh using any three points and adds triangle indeces, and normals.  Triangle indeces must be added with consecutively numbered indexes, so the method must be given the starting index count and returns the new index count:</p>





















<pre><code>private int AddSingleMeshTriangle(
    MeshGeometry3D mesh, Point3D p0, Point3D p1, 
    Point3D p2, int currentIndexCount)
    {
        Vector3D normal;
        
        //add mesh positions from the 
        //supplied points
        mesh.Positions.Add(p0);
        mesh.Positions.Add(p1);
        mesh.Positions.Add(p2);

        //define the mesh's triangle
        //indeces from a consecutive
        //index count
        mesh.TriangleIndices.Add(
            currentIndexCount);
        mesh.TriangleIndices.Add(
            currentIndexCount + 1);
        mesh.TriangleIndices.Add(
            currentIndexCount + 2);

        currentIndexCount += 3;

        //calculate and add normals for
        //each of the new triangle points
        normal = CalculateNormal(p0, p1, p2);
        mesh.Normals.Add(normal);
        mesh.Normals.Add(normal);
        mesh.Normals.Add(normal);

        return currentIndexCount;

}
</code></pre>
<p> Next is the method that will add four triangles to the mesh given the current set of center points and perimeter points for the current vertex index.  Basically, the caller provides the top, bottom, and four perimeter points and the method will add the triangles and return the new triangle index count: </p>





















<pre><code>private int AddPointsToMesh(
    MeshGeometry3D mesh, Point3D topCenter, 
    Point3D bottomCenter, Point3D p0, Point3D p1,
    Point3D p2, Point3D p3, int currentIndexCount)
    {
        //top
        currentIndexCount = AddSingleMeshTriangle(
            mesh, topCenter, p1, p0, currentIndexCount);

        //bottom
        currentIndexCount = AddSingleMeshTriangle(
            mesh, bottomCenter, p2, p3, currentIndexCount);

        //side1
        currentIndexCount = AddSingleMeshTriangle(
            mesh, p0, p1, p2, currentIndexCount);

        //side2
        currentIndexCount = AddSingleMeshTriangle(
            mesh, p1, p3, p2, currentIndexCount);
        
        return currentIndexCount;
    }
</code></pre>
<p>Finally, the method that does all the action is <code>LoadModel()</code>.  It's called by specifying a color for the model, the list of vertices to use for the perimeter, and a step value.  The step value indicates how many vertices to skip over during enumeration.  A higher step value will increase performance but lower the model quality:</p>





















<pre><code>public Model3D LoadModel(
    Color color, List&lt;Vertex&gt; vertices, int step)
{
    //the y value for the top surface
    double yValue = 0;
    
    //y axis thickness
    double yThickness = 0.5;
    
    //create a mesh to add triangles to
    MeshGeometry3D mesh = new MeshGeometry3D();

    Point3D p0, p1, p2, p3;
    int indexCount = 0;
    
    //define the top and bottom center points
    Point3D topCenter = new Point3D(
        vertices[0].Latitude, 
        yValue, 
        vertices[0].Longitude * -1);
    Point3D bottomCenter = new Point3D(
        vertices[0].Latitude, 
        yValue - yThickness, 
        vertices[0].Longitude * -1);

    //loop throuh the vertex list
    for (int i = 1; i &lt; vertices.Count; i = i + step)
    {
        p0 = new Point3D(
          vertices[i].Latitude, 
          yValue, 
          vertices[i].Longitude * -1);
        
        //p2 is the same as p0, but with
        //a different y value
        p2 = new Point3D(
            vertices[i].Latitude, 
            yValue - yThickness, 
            vertices[i].Longitude * -1);

        //check to see if we're at the last index.  
        //If so, use index #1 for the second point
        if (i + step &gt;= vertices.Count)
        {
            p1 = new Point3D(
                vertices[1].Latitude, 
                yValue, 
                vertices[1].Longitude * -1);
                
            //p3 is the same as p1, but 
            //with a different y value
            p3 = new Point3D(
                vertices[1].Latitude, 
                yValue - yThickness, 
                vertices[1].Longitude * -1);
        }
        else
        {
            p1 = new Point3D(
                vertices[i + step].Latitude, 
                yValue, 
                vertices[i + step].Longitude * -1);

            //p3 is the same as p1, but 
            //with a different y value
            p3 = new Point3D(
                vertices[i + step].Latitude, 
                yValue - yThickness, 
                vertices[i + step].Longitude * -1);
        }

        indexCount = AddPointsToMesh(
            mesh, topCenter, bottomCenter, 
            p0, p1, p2, p3, indexCount);
  }

  //create a visible surface for the mesh
  Material material = new DiffuseMaterial(
    new SolidColorBrush(color));
    
  //create a new 3D model from the
  //mesh and material
  GeometryModel3D g = new GeometryModel3D(
    mesh, material);
  
  return g;
}
        </code></pre>
        
<p>The result is a model of the state of MN (and the USA) that is of good quality and          performs well when changing the camera position in real time:</p>





















         
<p style="text-align: center;">         <a href="http://www.kindohm.com/localimages/3drefactor/MNFinal.png" target="_blank">             <img style="border: 0px none ;" src="http://www.kindohm.com/localimages/3drefactor/MNFinal.png" alt="Minnesota Final" width="300" /><br />         <em>Final Rendered Model (click to enlarge)</em></a>         </p>





















          
<p style="text-align: center;">         <a href="http://www.kindohm.com/localimages/3drefactor/USAFinal.png" target="_blank">             <img style="border: 0px none ;" src="http://www.kindohm.com/localimages/3drefactor/USAFinal.png" alt="USA Final" width="300" /><br />         <em>Final Rendered Model (click to enlarge)</em></a>         </p>





















                  
<p>I was suprised at first how big of an impact this simple refactoring made, but when I stepped back and realized         exactly how many fewer models I was rendering (on the order of about 20,000 times fewer), it makes sense that the         new way would perform better.</p>





















  
<p class="tags">tags: <a href="http://technorati.com/tag/wpf" target="_blank" rel="tag">wpf</a> <a href="http://technorati.com/tag/performance" target="_blank" rel="tag">performance</a> <a href="http://technorati.com/tag/3d" target="_blank" rel="tag">3d</a> <a href="http://technorati.com/tag/code" target="_blank" rel="tag">code</a> <a href="http://technorati.com/tag/programming" target="_blank" rel="tag">programming</a>  </p>





















  
