#Timetravelling with Maps - the tutorial IV

###By: Sarah MP

###IV FINAL ASSEMBLY

We now have all the assets required to assemble our interactive map:

   * map data in GeoJSON format
   * a tile template for the old Vancouver map
   * a tile template or MapBox ID for the 2015 map

We will prototype the interactive map in [JSFiddle](http://jsfiddle.net/), a tool that lets you quickly create and test HTML/JavaScript/CSS code. You can (but you dont need to) check out [this quick tutorial](http://doc.jsfiddle.net/tutorial.html) to familiarize yourself with the interface.

JSFiddle has four main panes:

    HTML code (top left)
    CSS code (top right)
    JavaScript code (bottom left)
    The end result (bottom right)

JSFiddle takes care of assembling the three code components into the result every time you click “Run” (in top, blue bar).

##### step a: HTML & CSS

In this example the HTML and CSS parts are very simple. We only need a rectangular area in the page that will display the map and all its controls.

We need an HTML element where the map will go. Type or copy/paste this in the HTML pane:
	
<script src="https://gist.github.com/sarahmprz/5a0278be557dd6b0d914.js"></script>
	
With this code we create a div element whose identifier is map and, as you can imagine, it will contain the map. We now need to “style” the element (give it a width and a height and, if you want to, borders and other attributes). Styling is controlled with CSS. Type or copy/paste this in the CSS pane:

<script src="https://gist.github.com/sarahmprz/03cf99f7d1d4d4f94c8a.js"></script>
	
This applies a width and a height of 400 pixels to the element whose identifier is `map` (the `#` prefix means "id" in CSS). Of course you can make the rectangle bigger (if your monitor is big enough) and apply other attributes between those `{ }` brackets (e.g.: `background-color: #f00;` for a red background if you want to see the element with no map) but I just wanted to keep it very simple.

If you click "Run" now you won't see much (unless you added a background color or a border to the element). That's all the HTML and CSS you will need for now.

#### step b: Adding MapBoxJS

To present the map and make it interactive we will need some external assets and JavaScript. We are going to need both something called [Leaflet](http://leafletjs.com/) and [Mapbox JS](https://www.mapbox.com/mapbox.js/api/v2.2.1/) in order to present and control the map. Leaflet is included in MapBoxJS so we just need to worry about the latter. MapBoxJS is composed of two separate files: a JS file and a CSS file. You already have an idea of what the CSS file does. The JavaScript file contains all the interactive mapping magic. These are the URLs to the files in question (note that it is not the latest MapBoxJS version but no worries, it will work):

CSS file:
http://api.tiles.mapbox.com/mapbox.js/v1.5.0/mapbox.css

JavaScript file:
http://api.tiles.mapbox.com/mapbox.js/v1.5.0/mapbox.js

In the left column in JSFiddle find the “External Resources” section. You need to copy those URLs and paste each in the JavaScript/CSS URI box and click the + button. You will see something like this after you do it:

![fiddle](https://github.com/sarahmprz/timetravellingmaps/blob/master/img/fiddle.png)

This will make JSFiddle load those files the next time you click “Run” and from then on.

#### step c: mo map mo map mo map
This is the interesting/tricky part - at least for me! We’re gonna write (copy/past is allowed) so we can see our old Vancouver 1937 map:

insert the javascript:

<script src="https://gist.github.com/sarahmprz/2448832a7dd307b2a147.js"></script>

Now, you should be able to see something like this:

![fiddlewow](https://github.com/sarahmprz/timetravellingmaps/blob/master/img/fiddlewow.png)

#### step d: Managing multiple tile sets

You may notice that the map is all white except for the 1937 map and that is what we want. The tile set URL only has the rectified map on it and nothing else. We need to have an additional 2015 tile set to compare (I will use an example MapBox Map ID, in case you did not create your own in step 3 above). We will replace the JS code with new one that will contain:

* some attribution information for the map
* the 2015 tile set
* a control that will let us swap one tile set for another

This code should *replace your previous JS*:

<script src="https://gist.github.com/sarahmprz/f28c387ae0f9be2d2fa9.js"></script>

If you look throught this code you will notice it is quite similar to what we had before. The main differences are the addition of attributions and MapBox tile sets (via the map ID). The control itself is two lines: one to create a baseMaps variable that will hold the tile sets (you can add as many tile sets as you want) and another to create the control and add it to the map. Toggle your heart out.

We’re almost there! We now need to display our data. Leaflet makes this process quite easy since it natively supports GeoJSON. The process is just a few lines, but first remove the map zoom function map.setView([49.28273, -123.12074],13). Now paste this code at the bottom of the JS pane:

<script src="https://gist.github.com/sarahmprz/d819416902b74bc71d53.js"></script>

You need to copy the GeoJSON output from the text file (if you cant open your file, change the name to .txt) you downloaded from GeoJSON.io and paste it where you see 'paste_geojson_here_keep_quotes'. Make sure you keep those quotes! That line should end up looking something like:


![geojsonfiddle](https://github.com/sarahmprz/timetravellingmaps/blob/master/img/geojsonfiddle.png)


We replaced the zoom function with map.fitBounds(geolayer.getBounds()). This makes the map “smarter”: instead of us typing longitude, latitude and zoom level by hand we let Leaflet calculate the bounding area for the set of points provided with getBounds() and pass that as a value to the map’s fitBounds() function. Voilá, the map now zooms to show all the points in the set. If you add more points the bounds will change automatically!

#### step e Making the pins come alive

We want to click on the pins and show a popup box with the data we have associated to it (in the feature’s properties). We need to do two things:

* a function that will build and present the popup for a given feature (point)
* modify the L.geoJson() call to use this function

Leaflet’s bindPopup() layer function does just that: draws a box with text next to a given layer. This text can be marked up with HTML. Copy/paste this code below all you have so far:

<script src="https://gist.github.com/sarahmprz/f7cd4477947dabc5ebcb.js"></script>

This showPopup() function receives a feature, the piece of GeoJSON that contains all the information (geometry and properties), and a layer, the same GeoJSON as displayed by Leaflet (in our case, the blue pin). These two parameters are passed automatically by the L.geoJson() function. showPopup() then loops through each property in the feature (name, address, etc.) and builds an HTML string. This string is used as the markup for the popup.

We have not connected showPopup to anything. Modify your current L.geoJson line as follows:

<script src="https://gist.github.com/sarahmprz/02609cb88bc7d723d441.js"></script>

…you are just adding , {onEachFeature: showPopup} after geodata. This tells Leaflet to apply the showPopup function for each feature in the GeoJSON.

#### step z Wrapping it all up

You will want to compile these three code snippets in an HTML page to publish your new map somewhere. Worry not, below is a code snippet that has the requisite spots for you to paste CSS, HTML and JS. Save all the code as a .html file and publish it somewhere:

’’’HTML
<!DOCTYPE html>
<html>
<head>
 
<meta http-equiv="Content-Type" content="text/html; charset=UTF-8" />
<title>Confidential Vancouver</title>
<script src="http://code.jquery.com/jquery-1.11.0.min.js"></script>
<link href="http://api.tiles.mapbox.com/mapbox.js/v1.5.0/mapbox.css" media="screen, print" rel="stylesheet">
<script src="http://api.tiles.mapbox.com/mapbox.js/v1.5.0/mapbox.js"></script>
<style type="text/css">
/* paste CSS below this line */
 
</style>
</head>
<body>
<!-- paste HTML below this line -->
 
<script type="text/javascript">
// paste JavaScript below this line
 
</script>
</body>
</html>’’’

<script src="https://gist.github.com/sarahmprz/bc5311e2f3240f69e7b2.js"></script>



### References:
* [The New York Public Library’s Space/Time Directory Project](http://spacetime.nypl.org/)
* [Joey Lee’s super fun webmap tutorial](https://github.com/joeyklee/hellowebmaps/tree/master/Tutorial%20I)
* [this stellar workshop](http://www.nypl.org/blog/2015/01/05/web-maps-primer#fnref:magic) by Mauricio Arteaga 
* [Black Strathcona Project](http://blackstrathcona.com/)