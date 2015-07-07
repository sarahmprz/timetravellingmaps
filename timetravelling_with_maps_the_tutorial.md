#Timetravelling with maps - the tutorial

##Overview:
Getting to know historical maps are a great way to see how our city has changed and is changing, and to question why these changes happen, and who it affects. Our city’s heritage is deeper and wider than many people realize. Making maps like this allows us to tell a different story of Vancouver.

A brilliant example of this is the [Black Strathcona Project](http://blackstrathcona.com/), an interactive website featuring a map of stories that have often been neglected in the telling of Vancouver. The map we will create in this workshop builds on the same structural principles as this project.

We will go through some webtools that each kinda deserve their own tutorial, cos you can do so soo many cool things with them. Imma try keep it short and sweet, and if you have any more questions, I will do my very best to answer them. If you stick with us here at maptime! we will host several more workshops to get to know these tools more intimately.  

The following tutorial is a slight mod of a workshop I did month ago, by Mishka Vance at the NYPL, and it has leaned on [this stellar workshop](http://www.nypl.org/blog/2015/01/05/web-maps-primer#fnref:magic) by Mauricio Arteaga (NYPL Labs too). Thanks folks!.


##Mapping Historical Vancouver

###By: Sarah MP



##GETTING STARTED


###I GEO-RECTIFICATION

Our first task, after finding a map that we want to work with, is to geo-rectify it. This process means creating an equivalence between the pixels in the image and the geographic location they represent. When we do this, we will distort the image, to adapt it to the projection that mapping providers use, namely the mercator projection. 

The degree of distortion depends on quality of the map survey, the original projection and how well the map has been preserved. 

The Map Warper lets us do this without downloading a single thing to our computer - all the magic happens in the browser!

This webtool lets you upload your scanned maps and then geo rectify them. The Map Warper lets you tell the tool what parts of the scanned map corresponds with points of the mercator projection. 

Below is the link to the map that I have imported into the Map Warper. You can play around with this, or you can find your own map at the Vancouver Archives and upload it to the Warper.

[Vancouver 1937](http://searcharchives.vancouver.ca/index.php/map-of-city-of-vancouver-british-columbia-9)

and at [Map Warper](http://mapwarper.net/maps/10054)

![warper](https://github.com/sarahmprz/timetravellingmaps/blob/master/img/warper.png)

Do you see the pins in the image above? Each pin is numbered, and is the same in both images. The more pins you add, the more precise your rectification will be, though many pins will also slow the image generator. Youll need at least 3 pins.

You can preview your rectified map in the warper and you can export it. However, the coolest thing about the Warper is that it gives you tiles! The tiles are the URL template that you find under the export tab.

The template for our map is	

	http://mapwarper.net/maps/tile/10054/{z}/{x}/{y}.png

Map Warper has a tile-generating engine that uses the geo-rectified image to produce square map tiles at different zoom levels and coordinates so that only the necessary parts of the interactive map get displayed as you use it. Webmaps are made up of millions of tiles like these.

### II DATA EXTRACTION
Okay, we’ve got the map - what are we gonna put into it?

We could of course go back into the Vancouver Archives and dig up something really interesting amongst the dusty pages, but luckily, a team of pretty stellar scholars have done that for us already!

I’ve picked some geographical info from the excellent book, [Vancouver Confidential](http://www.anvilpress.com/Books/vancouver-confidential). Locations mentioned in this book are compiled in a comma-separated value (csv) file. The four locations we will be adding to our map, are 

* Sandy’s Billiard, a local hang spot for "Red agitators"
* The Palomar, legendary nightclub and theatre
* The Shõwa Club, hangout for the Black Dragon Society
* The City Dump/Hobo Jungle, home to 450 of the city’s homeless, in 1931

[Download the CSV list](https://github.com/sarahmprz/timetravellingmaps/blob/master/vanconfidential.csv)

You can create your own list from other data you find more interesting or useful. It can be people, events, buildings, businesses, whatevs. Good data in = good map out.

Make sure to include latitude and longitude columns and save it as a csv.

#####step a: geoJSON
Currently, our data is hanging out in a comma-separated list, but most webmapping tools use the GeoJSON standard. JSON is one of the most popular ways of structuring data online, and the geo part lets us use this for maps too!

GeoJSON uses "features" to describe geographic data, in our case points, but other forms are lines and polygons. Each feature is described by its geometry accompanied by its properties which is whatever extra data you want to associate with it (in our case)

We’re gonna convert our spreadsheet into a GeoJSON object and then update the placeholder latitude and longitude values to the proper values. We will use the map itself to help us figure out those. We’ll need a tool that lets us generate GeoJSON that we can easily manipulate...

Hellooo [GeoJSON.io](geojson.io)! This is “a quick, simple tool for creating, viewing, and sharing maps”. GeoJSON.io has any easy access interface we can use to create the GeoJSON we need.

#####step b do a little hack
Open [GeoJSON.io](GeoJSON.io) in a new browser window. You’ll see the default map at full zoom out. Now we need to do a little hacking. Right-click somewhere on the map and select "Inspect Element"

This opens an advanced developer view that let’s you view and modify the code of the page you are viewing (in this case, the map interface). GeoJSON.io includes a programming interface (API) that lets you control the map being displayed. 

![geojson](https://github.com/sarahmprz/timetravellingmaps/blob/master/img/geojson.png)

In the Console tab you’ll see some text and, at the bottom, a cursor where you can execute JavaScript code. You’ll see some comments from the creator of GeoJSON.io and a row where you can type new JavaScript commands. Type (copy/paste) the coordinates below in that area and press ENTER.

	window.api.map.setView([49.28273, -123.12074],13);

This will center and zoom us in on Vancouver. Now type this:

	window.api.map.addLayer( L.tileLayer( 'http://mapwarper.net/maps/tile/10054/{z}/{x}/{y}.png' ) );

…and press ENTER. This will add the tile layer itself. Notice that line of code includes the URL you copied in step 1. The end result will look something like this:

![oldgeojson](https://github.com/sarahmprz/timetravellingmaps/blob/master/img/oldgeojson.png)

Well done! Have a breather! You can close the development window but *NOT* the browser window!

**Be aware: You’ll have to re-apply this code every time you load GeoJSON.io since it doesn’t save console-made modifications. You can save the data you add to the map by logging in.**

#####step c: Adding data to GeoJSON.io
Now we will use this modified version of the map as a base to properly geo-locate the CSV list of historical Vancouver landmarks.

You do this by dragging the CSV file from your desktop on to the GeoJSON.io map. Lookatthat!

![csvimport](https://github.com/sarahmprz/timetravellingmaps/blob/master/img/csvimport.png)

On the right, you’ll see that the data is converted to GeoJSON, and the map zooms in to show the points that represent each Vancouver location. On left you’ll see a little green box telling you that 4 features have been imported.

But wait! Did your old map disappear? Not a fear - we’ve just zoomed in a bit too close and the tile URL template doesnt have images up to that level. Zoom out a bit and you’ll see our map again. 

Now we must generate the final GeoJSON that we will use to create our interactive map. Simply select Save > GeoJSON in the editor menu. A file called map.geojson will be generated and downloaded to your computer.

###III CREATING A 2015 CUSTOM MAP
We want to be able to compare this old Vancouver map with our present day city so we can see how things have changed over time. We need a “base map” which is more or less what GeoJSON.io has when you load it: a (hopefully accurate) “vanilla” street map of the present day world. You could use the standard [OpenStreetMap tiles](http://wiki.openstreetmap.org/wiki/Tiles) or use a service such as MapBox to produce a completely custom map (MapBox uses OSM data). MapBox is quite powerful: it lets you change colors, customize what gets shown (streets, buildings, parks, etc.) and even use satellite imagery!

You can make your own, following the steps below, 

* 
* 
* 
* 





or, take a shortcut and use mine: 
	
	sarahmprz.ml853h3j 

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
	
	<div id="map"></div>
	
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

--------INSERT PIC

Sure, this is nice, but wouldn’t it be better to actually see the photo? Let’s do just that! Replace the showPopup function with this one:

function showPopup(feature, layer) {
  var key, val;
  var content = [];
  for (key in feature.properties) {
    val = feature.properties[key];
    if (key == "Page") {
      val = '<a href="http://www.bibliotecanacional.gov.co/recursos_user/bookreader/1889_sala3a_12756/#page/' + val + '" target="_blank">See page</a>';
    } else if (key == "Photo") {
      val = '<img src="' + val + '"  height="150" />';
    }
    content.push("<strong>" + key + ":</strong> " + val);
  }
  layer.bindPopup(content.join("<br />"));
}


We just added a check in the loop: if key equals “Page” we build a link to the directory and if key equals “Photo” we build an image tag and constrain the height to 150 pixels (just in case the image is too big).

Now this is what you should get:

INSERT PIC

Eh?

#### step z Wrapping it all up

You will want to compile these three code snippets in an HTML page to publish your new map somewhere. Worry not, below is a code snippet that has the requisite spots for you to paste CSS, HTML and JS. Save all the code as a .html file and publish it somewhere:

<script src="https://gist.github.com/sarahmprz/bc5311e2f3240f69e7b2.js"></script>



### References:
* [The New York Public Library’s Space/Time Directory Project](http://spacetime.nypl.org/)
* [Joey Lee’s super fun webmap tutorial](https://github.com/joeyklee/hellowebmaps/tree/master/Tutorial%20I)
* [this stellar workshop](http://www.nypl.org/blog/2015/01/05/web-maps-primer#fnref:magic) by Mauricio Arteaga 
* [Black Strathcona Project](http://blackstrathcona.com/)