#Timetravelling with Maps - the tutorial I, II, III

###By: Sarah MP

##Overview:
Getting to know historical maps are a great way to see how our city has changed and is changing, and to question why these changes happen, and who it affects. Our city’s heritage is deeper and wider than many people realize. Making maps like this allows us to tell a different story of Vancouver.

A brilliant example of this is the [Black Strathcona Project](http://blackstrathcona.com/), an interactive website featuring a map of stories that have often been neglected in the telling of Vancouver. The map we will create in this workshop builds on the same structural principles as this project.

We will go through some webtools that each kinda deserve their own tutorial, cos you can do so soo many cool things with them. Imma try keep it short and sweet, and if you have any more questions, I will do my very best to answer them. If you stick with us here at maptime! we will host several more workshops to get to know these tools more intimately.  

The following tutorial is a slight mod of a workshop I did month ago, by Mishka Vance at the NYPL, and it has leaned on [this stellar workshop](http://www.nypl.org/blog/2015/01/05/web-maps-primer#fnref:magic) by Mauricio Arteaga (NYPL Labs too). Thanks folks!.


##Let’s Start!



###I GEO-RECTIFICATION

Our first task, after finding a map that we want to work with, is to geo-rectify it. This process means creating an equivalence between the pixels in the image and the geographic location they represent. When we do this, we will distort the image, to adapt it to the projection that mapping providers use, namely the mercator projection. 

The degree of distortion depends on quality of the map survey, the original projection and how well the map has been preserved. 

The Map Warper lets us do this without downloading a single thing to our computer - all the magic happens in the browser!

This webtool lets you upload your scanned maps and then geo rectify them. The Map Warper lets you tell the tool what parts of the scanned map corresponds with points of the mercator projection. 

Below is the link to the map that I have imported into the Map Warper. You can play around with this, or you can find your own map at the Vancouver Archives and upload it to the Warper.

[Vancouver 1937](http://searcharchives.vancouver.ca/index.php/map-of-city-of-vancouver-british-columbia-9)

and imported into the [Map Warper](http://mapwarper.net/maps/10054)

![warper](https://github.com/sarahmprz/timetravellingmaps/blob/master/img/warper.png)

Do you see the pins in the image above? Each pin is numbered, and is the same in both images. The more pins you add, the more precise your rectification will be, though many pins will also slow the image generator. Youll need at least 3 pins to warp a map for the first time, but you can freely add as many or as few as you like to this one.

You can preview your rectified map in the warper and you can export it. However, the coolest thing about the Warper is that it gives you tiles! The tiles are the URL template that you find under the export tab.

The template for our map is	

	http://mapwarper.net/maps/tile/10054/{z}/{x}/{y}.png

Map Warper has a tile-generating engine that uses the geo-rectified image to produce square map tiles at different zoom levels and coordinates so that only the necessary parts of the interactive map get displayed as you use it. Webmaps are made up of millions of tiles like these.

### II DATA EXTRACTION
Okay, we’ve got the map - what are we gonna put into it?

We could of course go back into the Vancouver Archives and dig up something really interesting amongst the dusty pages, but luckily, a team of pretty stellar scholars have done that for us already!

I’ve picked some geographical info from the excellent book, [Vancouver Confidential](http://www.anvilpress.com/Books/vancouver-confidential). Locations mentioned in this book are compiled in a comma-separated value (csv) file. The four no-longer-existing locations we will be adding to our map, are 

* Sandy’s Billiard, a local hang spot for "Red agitators"
* The Palomar, legendary nightclub and theatre
* The Shõwa Club, hangout for the Black Dragon Society
* The City Dump/Hobo Jungle, home to 450 of the city’s homeless, in 1931

[Download the CSV list](https://raw.githubusercontent.com/sarahmprz/timetravellingmaps/master/vanconfidential.csv)

You can also create your own list from other data you find more interesting or useful. It can be people, events, buildings, businesses, whatevs. Good data in = good map out.

Make sure to include latitude and longitude columns and save it as a csv.

#####step a: geoJSON
Currently, our data is hanging out in a comma-separated list, but most webmapping tools use the GeoJSON standard. JSON is one of the most popular ways of structuring data online, and the geo part lets us use this for maps too!

GeoJSON uses "features" to describe geographic data, in our case points, but other forms are lines and polygons. Each feature is described by its geometry accompanied by its properties which is whatever extra data you want to associate with it (in our case)

We’re gonna convert our spreadsheet into a GeoJSON object and then update the placeholder latitude and longitude values to the proper values. We will use the map itself to help us figure out those. We’ll need a tool that lets us generate GeoJSON that we can easily manipulate...

Hellooo [geojson.io](geojson.io)! This is “a quick, simple tool for creating, viewing, and sharing maps”. GeoJSON.io has any easy access interface we can use to create the GeoJSON we need.

#####step b: do a little hack
Open [geojson.io](geojson.io) in a new browser window. You’ll see the default map at full zoom out. Now we need to do a little hacking. Right-click somewhere on the map and select "Inspect Element"

This opens an advanced developer view that let’s you view and modify the code of the page you are viewing (in this case, the map interface). GeoJSON.io includes a programming interface (API) that lets you control the map being displayed. 

![geojson](https://github.com/sarahmprz/timetravellingmaps/blob/master/img/geojson.png)

In the Console tab you’ll see some text and, at the bottom, a cursor where you can execute JavaScript code. You’ll see some comments from the creator of GeoJSON.io and a row where you can type new JavaScript commands. Type (copy/paste) the coordinates below in that area and press ENTER.

	window.api.map.setView([49.28273, -123.12074],13);

This will center and zoom us in on Vancouver. Now type this:

	window.api.map.addLayer( L.tileLayer( 'http://mapwarper.net/maps/tile/10054/{z}/{x}/{y}.png' ) );

…and press ENTER. This will add the tile layer itself. Notice that line of code includes the URL that the Map Warper generated for us in step 1. The end result will look something like this:

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

* Go to mapbox.com and log in/sign up
* Go to Projects and click New Project
* Pan the webmap to Vancouver and zoom in to an appropriate level (Ive zoomed to 13). Notice the long, lat and zoom level in the bottom left corner.
* Play around with the template styles till you find one you like
* Save your map and receive a Map ID, that looks like this:
	
		yournamehere.ml853h3j 
		
		or just use mine: 
		
		sarahmprz.ml853h3j 
		
Great job! If youre ready for more, head over to [part IV]()!
