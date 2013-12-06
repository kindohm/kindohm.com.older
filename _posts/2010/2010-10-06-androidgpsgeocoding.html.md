---
layout: post
title: "GPS and Geocoding with Android"
---

<p>I did a little development on Android last night and wrote a very simple app that does two things: monitors the current location (latitude and longitude) and gets the street address from the current location data.</p>
<p><img src="/hodsmedia/1255419375_1.jpg" alt=""/></p>
<p>The Android location and geocoding APIs were trivial, but this was my first experience doing any GPS programming on a real, physical mobile device (not an emulator) - so it was pretty fun. Everything I needed to implement the app I got from these two sources:</p>
<ul>
<li><a href="http://developer.android.com/guide/topics/location/obtaining-user-location.html" target="_blank">Obtaining User Location</a></li>
<li><a href="http://stackoverflow.com/questions/2296377/how-to-get-city-name-from-latitude-and-longitude-values-in-google-map-android" target="_blank">how to get city name from latitude and longitude values in google map android</a></li>
</ul>
<p>I was hesitant to post the source code because it&#8217;s just so dang easy&#8230;  but here is the entire app including the layout and activity:</p>

<p>
<pre><code>&lt;?xml version="1.0" encoding="utf-8"?&gt;
&lt;LinearLayout xmlns:android="http://schemas.android.com/apk/res/android"
    android:orientation="vertical"
    android:layout_width="fill_parent"
    android:layout_height="fill_parent"
    &gt;
&lt;TextView  
    android:layout_width="fill_parent" 
    android:layout_height="wrap_content" 
    android:text="@string/hello"
    android:id="@+id/locationText"
    /&gt;
&lt;Button 
  android:layout_width="fill_parent"
  android:layout_height="wrap_content"
  android:id="@+id/addressButton"
  android:text="Get Address"
    /&gt;    
&lt;TextView  
    android:layout_width="fill_parent" 
    android:layout_height="wrap_content" 
    android:text="@string/hello"
    android:id="@+id/addressText"
   /&gt;
&lt;/LinearLayout&gt;</code></pre></p>
<pre><code>import java.io.IOException;
import java.util.*;
import android.widget.*;
import android.view.View;
import android.view.View.OnClickListener;
import android.app.Activity;
import android.os.Bundle;
import android.location.*;
import android.content.*;

public class LocatahActivity extends Activity {
    
    Button addressButton;
    TextView locationText;
    TextView addressText;
    Location currentLocation;
    double currentLatitude;
    double currentLongitude;
    
    @Override
    public void onCreate(Bundle savedInstanceState) {

        super.onCreate(savedInstanceState);
        setContentView(R.layout.main);
        
        addressText = (TextView)findViewById(R.id.addressText);
        locationText = (TextView)findViewById(R.id.locationText);
        addressButton = (Button)findViewById(R.id.addressButton);

        this.addressText.setText("ready");

        LocationManager locationManager = 
            (LocationManager)this.getSystemService(Context.LOCATION_SERVICE);

        LocationListener locationListener = new LocationListener() {
            public void onLocationChanged(Location location) {
                updateLocation(location);
            }
            public void onStatusChanged(
                    String provider, int status, Bundle extras) {}
            public void onProviderEnabled(String provider) {}
            public void onProviderDisabled(String provider) {}
        };

        locationManager.requestLocationUpdates(
                LocationManager.NETWORK_PROVIDER, 0, 0, locationListener);

        this.addressButton.setOnClickListener(new OnClickListener() {
            public void onClick(View v){
                getAddress();
            }
        });
    }
    
    void getAddress(){
        try{
            Geocoder gcd = new Geocoder(this, Locale.getDefault());
            List&lt;Address&gt; addresses = 
                gcd.getFromLocation(currentLatitude, currentLongitude,100);
            if (addresses.size() &gt; 0) {
                StringBuilder result = new StringBuilder();
                for(int i = 0; i &lt; addresses.size(); i++){
                    Address address =  addresses.get(i);
                    int maxIndex = address.getMaxAddressLineIndex();
                    for (int x = 0; x &lt;= maxIndex; x++ ){
                        result.append(address.getAddressLine(x));
                        result.append(",");
                    }               
                    result.append(address.getLocality());
                    result.append(",");
                    result.append(address.getPostalCode());
                    result.append("\n\n");
                }
                addressText.setText(result.toString());
            }
        }
        catch(IOException ex){
            addressText.setText(ex.getMessage().toString());
        }
    }
    
    void updateLocation(Location location){
        currentLocation = location;
        currentLatitude = currentLocation.getLatitude();
        currentLongitude = currentLocation.getLongitude();
        locationText.setText(currentLatitude + ", " + currentLongitude);
    }
}</code></pre>


<p>One thing I learned about the Java language was the ability to implement interfaces anonymously and in-line. When you just need a simple, lightweight type to implement a required interface type, it just keeps things brief.  The LocationListener interface used above was coded in this manner. Handy!</p>

