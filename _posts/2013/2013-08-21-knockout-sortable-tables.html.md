---
layout: post
title: "Sortable Tables with Knockout.js"
---

<p><img src="http://farm1.staticflickr.com/100/306917951_73094869d0_z.jpg" alt="Tables Outside Dublin Restaurant"></p>






<p>
On my current project we are using knockout.js and displaying large tables
    (20-30 columns) of data. These tables need to be sortable by almost all columns. 
    After not being able to find a good full post or article on how to implement
    such a thing, and after we implemented our own solution, I thought I would
    write my own post on how do do it.
</p>







<h3>Basic Implementation</h3>
<p>It's very simple to implement:</p>







<ol>
    
<li>
<p>Bind an anchor in your column heading to a click function:</p>






        
<pre><code>&lt;th&gt;&lt;a href="#" data-bind="click: sortByName"&gt;Name&lt;/a&gt;&lt;/th&gt;</code></pre></li>
    
<li>
<p>Implement the click function in your knockout view-model:</p>






<pre><code>function ViewModel(){
    this.items = ko.observableArray();

    this.sortByName = function() {
        this.items.sort(function(a, b) {
            if (a.Name() &gt; b.Name()) return 1;
            if (a.Name() &lt; b.Name()) return -1;
            return 0;
        });
    }
}</code></pre>
</li>
</ol>

<p>Of course, you'll probably want to toggle the sort direction each time you
    click on the column heading:</p>







<pre><code>var sortDirection = -1;

function sortByName() {
    sortDirection = -sortDirection;
    this.items.sort(function(a, b) {
        if (a.Name() &gt; b.Name()) return 1 * sortDirection;
        if (a.Name() &lt; b.Name()) return -1 * sortDirection;
        return 0;
    });
}</code></pre>

<h3>Live Example</h3>
<p>Try it out:</p>






<table class="table table-condensed" id="simple-table">
    <thead>
        
<tr>
            <th><a href="#" data-bind="click: sortByName">Name</a></th>
            <th><a href="#" data-bind="click: sortByQty">Qty</a></th>
        </tr>
    </thead>
    <tbody data-bind="foreach: items">
        
<tr>
            
<td data-bind="text: name"></td>
            
<td data-bind="text: qty"></td>
        </tr>
    </tbody>
</table>

<p>The table headings:</p>






<pre><code>&lt;thead&gt;
    &lt;tr&gt;
        &lt;th&gt;&lt;a href="#" data-bind="click: sortByName"&gt;Name&lt;/a&gt;&lt;/th&gt;
        &lt;th&gt;&lt;a href="#" data-bind="click: sortByQty"&gt;Qty&lt;/a&gt;&lt;/th&gt;
    &lt;/tr&gt;
&lt;/thead&gt;</code></pre>

<p>And the full view-model:</p>







<pre><code>var simpleViewModel = function(){
    var self = this;
    var nameDirection = -1;
    var qtyDirection = -1;

    self.items = ko.observableArray();

    self.sortByName = function(){
        nameDirection = -nameDirection;
        self.items.sort(function(a, b){
            if (a.name &gt; b.name) return 1 * nameDirection;
            if (a.name &lt; b.name) return -1 * nameDirection;
            return 0;
        });
    };

    self.sortByQty = function(){
        qtyDirection = -qtyDirection;
        self.items.sort(function(a, b){
            if (a.qty &gt; b.qty) return 1 * qtyDirection;
            if (a.qty &lt; b.qty) return -1 * qtyDirection;
            return 0;
        });
    };

    self.items.push({ name: 'Chaska', qty: 3});
    self.items.push({ name: 'Bloomington', qty: 2});
    self.items.push({ name: 'Chanhassen', qty: 5});
    self.items.push({ name: 'Eden Prairie', qty: 4});
    self.items.push({ name: 'Duluth', qty: 3});
    self.items.push({ name: 'Crystal', qty: 7});
    self.items.push({ name: 'Falcon Heights', qty: 5});
    self.items.push({ name: 'Hermantown', qty: 4});
    self.items.push({ name: 'Proctor', qty: 2});
    self.items.push({ name: 'Superior', qty: 6});
}</code></pre>

<h3>Improvements With Dynamic JavaScript</h3>

<p>This code works, but I find it annoying. Why? Because you need to implement
    a custom sort function for <em>every column you want to sort on</em>. If
    you have 30 sortable columns, that means 30 functions in your view-model.</p>







<p>Since we have the power of JavaScript at our fingertips here, there is 
    absolutely a better way to do this!</p>







<p>Instead, let's implement an <code>addSort</code> method to our view-model
that <em>dynamically adds</em> each sort function that we need:</p>







<pre><code>this.addSort = function(field){
  self['sortBy_' + field] = function(){
    self.items.sort(function(a,b){
      if (a[field] &gt; b[field]) return 1;
      if (a[field] &lt; b[field]) return -1;
      return 0;
    });
  };
};</code></pre>

<p>Calling this:</p>







<pre><code>viewModel.addSort('name');</code></pre>

<p>Creates this function dynamically:</p>







<pre><code>function sortBy_name(){
    self.items.sort(function(a, b){
        if (a.name &gt; b.name) return 1;
        if (a.name &lt; b.name) return -1;
        return 0;
    });
};</code></pre>

<p>And of course our HTML would look like this:</p>






<pre><code>&lt;th&gt;&lt;a href="#" data-bind="click: sortBy_name"&gt;Name&lt;/a&gt;&lt;/th&gt;</code></pre>

<p>Ok, so how about adding the sort direction? We can make that dynamic too:</p>







<pre><code>this.addSort = function(field){
  self['sortDirection_' + field] = -1;
  self['sortBy_' + field] = function(){
    self['sortDirection_' + field] = -this['sortDirection_' + field];
    self.items.sort(function(a,b){
      if (a[field] &gt; b[field]) return 1 * self['sortDirection_' + field];
      if (a[field] &lt; b[field]) return -1 * self['sortDirection_' + field];
      return 0;
    });
  };
};</code></pre>

<p>Now each column has its own sort direction.</p>







<h3>Dynamic version live example:</h3>

<table class="table table-condensed" id="dynamic-table">
    <thead>
        
<tr>
            <th><a href="#" data-bind="click: sortBy_name">Name</a></th>
            <th><a href="#" data-bind="click: sortBy_qty">Qty</a></th>
        </tr>
    </thead>
    <tbody data-bind="foreach: items">
        
<tr>
            
<td data-bind="text: name"></td>
            
<td data-bind="text: qty"></td>
        </tr>
    </tbody>
</table>

<h3>Don't forget the () on dynamic overvables</h3>

<p>If the items you are sorting are observables, then make sure you invoke
    the fields as a function when performing the comparison.</p>







<p>Non-observable version:</p>






<pre><code>if (a[field] &gt; b[field]) return 1;</code></pre>
<p>Observable version:</p>






<pre><code>if (a[field]() &gt; b[field]()) return 1;</code></pre>

<h3>Conclusion</h3>

<p>In conclusion, this is awesome. Happy coding!</p>







<script type="text/javascript" src="http://cdnjs.cloudflare.com/ajax/libs/knockout/2.3.0/knockout-min.js"></script>

<script type="text/javascript">
;(function(){

    var simpleViewModel = function(){
        var self = this;
        var nameDirection = -1;
        var qtyDirection = -1;

        self.items = ko.observableArray();

        self.sortByName = function(){
            nameDirection = -nameDirection;
            self.items.sort(function(a, b){
                if (a.name &gt; b.name) return 1 * nameDirection;
                if (a.name &lt; b.name) return -1 * nameDirection;
                return 0;
            });
        };

        self.sortByQty = function(){
            qtyDirection = -qtyDirection;
            self.items.sort(function(a, b){
                if (a.qty &gt; b.qty) return 1 * qtyDirection;
                if (a.qty &lt; b.qty) return -1 * qtyDirection;
                return 0;
            });
        };

        self.items.push({ name: 'Chaska', qty: 3});
        self.items.push({ name: 'Bloomington', qty: 2});
        self.items.push({ name: 'Chanhassen', qty: 5});
        self.items.push({ name: 'Eden Prairie', qty: 4});
        self.items.push({ name: 'Duluth', qty: 3});
        self.items.push({ name: 'Crystal', qty: 7});
        self.items.push({ name: 'Falcon Heights', qty: 5});
        self.items.push({ name: 'Hermantown', qty: 4});
        self.items.push({ name: 'Proctor', qty: 2});
        self.items.push({ name: 'Superior', qty: 6});
    }

    var dynamicViewModel = function(){
        var self = this;

        self.items = ko.observableArray();

        this.addSort = function(field){
          self['sortDirection_' + field] = -1;
          self['sortBy_' + field] = function(){
            self['sortDirection_' + field] = -this['sortDirection_' + field];
            self.items.sort(function(a,b){
              if (a[field] &gt; b[field]) return 1 * self['sortDirection_' + field];
              if (a[field] &lt; b[field]) return -1 * self['sortDirection_' + field];
              return 0;
            });
          };
        };
      
        self.items.push({ name: 'Chaska', qty: 3});
        self.items.push({ name: 'Bloomington', qty: 2});
        self.items.push({ name: 'Chanhassen', qty: 5});
        self.items.push({ name: 'Eden Prairie', qty: 4});
        self.items.push({ name: 'Duluth', qty: 3});
        self.items.push({ name: 'Crystal', qty: 7});
        self.items.push({ name: 'Falcon Heights', qty: 5});
        self.items.push({ name: 'Hermantown', qty: 4});
        self.items.push({ name: 'Proctor', qty: 2});
        self.items.push({ name: 'Superior', qty: 6});
    }

    ko.applyBindings(new simpleViewModel(), document.getElementById('simple-table'));

    var dynamicVm = new dynamicViewModel();
    dynamicVm.addSort('name');
    dynamicVm.addSort('qty');
    ko.applyBindings(dynamicVm, document.getElementById('dynamic-table'));


})();
</script>
