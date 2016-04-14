---
layout: post
title:  "Using Leaflet to draw your Smart Building"
description: "How to use Leaflet to show our smart city building. Beginning with a common basemap, we will overlay all the indoor spaces  stored previously in Osiris"
date:   2016-4-11 9:39:17
image: leaflet-valenciaconference.png
imagedesc: "Valencia conference building indoor map"
categories: tutorial
author: Guillermo Amat
---

In this article we will learn how to use [Leaflet][leaflet-home]{:target="_blank"} to show  the indoor maps of our smart building. We will take a common basemap and then we will overlay our indoor spaces getting the data stored previously in Osiris. We assume a basic knowledge of Leaflet however, the contents of this blog post are easy to understand. Another component we will need is JQuery, which will be used for makng AJAX requests to our Osiris server.

Leaflet is a JavaScript library for interactive maps. Used in many web sites and mobile friendly, its API Reference is quite well documented and the project is Open Source having its code hosted at [GitHub][github-leaflet]{:target="_blank"}.

The steps we are going to follow are:

1- Upload a map to our server  
2- Display a basemap using Leaflet  
3- Write an AJAX function with JQuery to make petitions to our server  
4- Process the information received and use some Leaflet classes to draw polygons and polylines.  


## Uploading the map

Here the same map and procedure described in the documentation will be used. It is supposed the Osiris installation and running correctly. Otherwise, follow this [instructions][build-instructions]{:target="_blank"}.

Get the example map from the [sample maps repository][sample-map]{:target="_blank"} and use the import command to upload it. Remember that the first parameter is your api_key ("Aachen" in this case) and it will be used for the API calls.

{% highlight sh %}
sudo ./import_map.sh Aachen /path_to_my_map/map.osm
{% endhighlight %}

If everything is right you should be able to make API calls as [explained][calling-osiris]{:target="_blank"} in the previous article.

## Displaying a basemap in Leaflet

Now we are starting to code the client application. Before adding a map, some configuration variables must be explained:

* api_key: as said before, is the identifier of our indoor map within Osiris
* place: the geographical coordinates where our Leaflet map will be centered
* levels: the set of different levels of our building. It will be explained in detail later.

{% highlight js %}
    // Configuration values:
    var api_key= "Aachen"  // The identifier used to create the map in OSIRS
    var place = [50.77939, 6.07471]  // The center of the map. Usually a point within your building. Latitude, Longitude pair, i.e.: [39.49609, -0.4018879]

    //Globals        
    var levels = [] // Array of LayerGroups, one layer group for each building level
{% endhighlight %}

![Comparing black and white OpenStreetMap vs Mapbox](/images/leaflet-compare.png "Black and white OpenStreetMap and Mapbox"){:class="image center"}

In our application a black and white version of OpenStreetMap will be included, just for making clear what is being drawn over the basemap. However, it is possible to choose many other options. You can have a look [to this Leaflet providers page][lf-providers]{:target="_blank"} and paste the code of your preferred map provider. As you can see, most of the listed maps allow a maximum zoom value of 18 or 19. This is a little problem because for indoor maps we need a higher zoom. To override this, Leaflet defines a property called "maxNativeZoom" which determines the maximum zoom of the tiles available from the map provider. In our example we have maxNativeZoom = 18 (limited by the provider) and maxZoom = 22 (maximum zoom in our application defined by us). When the current zoom value is higher than the maxNativeZoom, the tiles of the basemap are auto-scaled.

{% highlight js %}
    // Leaflet map creation
    var mymap = L.map('mapid').setView(place, 19);  
    
    L.tileLayer('http://{s}.tiles.wmflabs.org/bw-mapnik/{z}/{x}/{y}.png', {
    maxZoom: 22,
    maxNativeZoom: 18,
    attribution: '&copy; <a href="http://www.openstreetmap.org/copyright">OpenStreetMap</a>'
    }).addTo(mymap);
{% endhighlight %}

## Writing and AJAX function for our API Calls

The Osiris's method we are interesting in is "search". For using it, we have to include a query in the body and specify that it is a POST petition. So, we have to use JQuery's $.ajax function but we will wrap it in our own function in order to reuse it with different queries:

{% highlight js %}
/**
 * Performs an AJAX call using JQuery
 **/
function queryMap(api_key, query, callbackFunc){
    $.ajax({
        url:"http://localhost:8020/osiris/geolocation/territory/search?layer=MAP&pageSize=1000",   // Osiris server URL
        type:"POST",
        headers:  {"api_key" : api_key},     
        data: query, 
        dataType: "json",
        contentType: 'application/json',              
        success: callbackFunc,
        error:function(jqXHR,textStatus,errorThrown)
        {
            alert("There is an error: "+errorThrown );
        }
    });         
}
{% endhighlight %}


Now we can use "queryMap" to retrieve our building's data. The third parameter is a callback method that will process the received response data. 

In our example, our building will be drawn in two steps:

- First, we will obtain the level outlines as a base shape for all the other spaces in the same floor. 
- Second, the data of all the remaining spaces that are of our interest will be get.

### Obtaining the level outlines 

Following our [mapping procedure][mapping-howto]{:target="_blank"}, levels are tagged as indoor:'level'. Using our function, the query can be written as follows:

{% highlight js %}
queryMap(api_key,"{properties.indoor:'level' }",createLevels);
{% endhighlight %}

The response is a JSON string that is passed as a parameter to createLevels. This function creates the levels of the building in Leaflet. Each level is used to generate a [LayerGroup][lf-layergroup]{:target="_blank"}. Secondly, the same level is assigned as a [Polygon][lf-polygon]{:target="_blank"} layer to the LayerGroup in order to be sure that it will be at the bottom of the group at the time of drawing all the shapes of the same floor.

{% highlight js %}
function createLevels(data){   
    var spaces = data;
    for (i = 0; i < spaces.length; i++){    
        var geometry = createGeometry(spaces[i]);
        if (typeof(geometry)!== 'undefined' && typeof(spaces[i].properties.level) !== 'undefined'){    
            if (typeof(levels[spaces[i].properties.level]) === 'undefined'){ 
                levels[spaces[i].properties.level]= new L.layerGroup;
            }
        }
        levels[spaces[i].properties.level].addLayer(geometry); // We need the level geometry to be draw at the bottom, so it should be the firs layer of its group
    }   
    
    orderLevels(levels); 
    
}
{% endhighlight %}

There is one more thing: we cannot be sure about the order of the results. This means that our code could generate the second level before the first and so on. To fix this issue a function called orderLevels that reorders our groups by their keys is available.  As a result a new object is created ("levelGroups") which is an ordered copy of "levels" with a more friendly key that will be used in the LayerControl to display each floor name. The last two sentences in this function are used to add the default LayerGroup (the one to be shown after loading the page) to the Leaflet map object and to add the layers control at the top right of the map. 

 
{% highlight js %}
function orderLevels(levels){           
    var levelGroups={}
    keys = Object.keys(levels),
    i, len = keys.length;
    
    keys.sort(function(a,b){return a - b});
    
    for (var i in keys) {  
        levelGroups["Level " +keys[i]]= levels[keys[i]];
    }
    levelGroups[Object.keys(levelGroups)[0]].addTo(mymap); // We add the first level found in the group as the default level viewed 
    L.control.layers(levelGroups).addTo(mymap); // levelGroups is an ordered copy of levels
    
}
{% endhighlight %}


![Levels shown in the layer control of our indoor map](/images/leaflet-layers.png "Displaying the layers"){:class="image center"}


### Getting the remaining indoor spaces

Our second query to Osiris will be this one:

{% highlight js %}
queryMap(api_key,"{ $and: [ {properties.indoor:{$exists: true}} , {properties.indoor: {$ne: 'level'}}] }",drawIndoor);
{% endhighlight %}

The query fetches all the items containing the "indoor" tag but excluding those assigned with 'level' as value (the ones we already have). The callback function iterates through the results calling createGeometry which is the responsible of drawing all the polygons or polylines needed, including different colors depending on the space type. When the geometry is created, it is added to its LevelGroup.


{% highlight js %}
function drawIndoor(data){
    var spaces = data
    for (i = 0; i < spaces.length; i++){    
        var geometry = createGeometry(spaces[i]);
        if ( typeof(spaces[i].properties.level) !== 'undefined' && geometry!== null){    
            if (typeof(levels[spaces[i].properties.level]) !== 'undefined'){                         
                levels[spaces[i].properties.level].addLayer(geometry); // The geometry is assigned to its level layer
            }
        }
    }
} 
{% endhighlight %}


As you can see in createGeometry, depending on the type of the space, a polygon or a polyline is generated with different options in terms of style. Moreover, a [Popup][lf-popup]{:target="_blank"} is associated to each space to give some information about its usage.


{% highlight js %}
/**
 * This funcions creates a geometry from a result obtained from Osisris.Depending on the type of the space, generates a polygon or a polyline
 **/        
function createGeometry(space){
    var geometry=null;
    if (space.hasOwnProperty("geometryDTO") && typeof(space.properties) !== 'undefined'){
        if (typeof(space.geometryDTO.collectionPointDTO) !== 'undefined'){
            
            // We need to process the points of this collectionPointDTO and convert them to leaflet LantLng    
            var points=space.geometryDTO.collectionPointDTO;
            var point, pointList=[];
            for (j = 0; j < points.length; j++){
                point = new L.LatLng(points[j].latitude,points[j].longitude);
                pointList[j]=point;
            }
            
            // The label to use in the popup. Preferably the name of the space
            var label = "";
            if (typeof(space.properties.name) !== 'undefined'){                              
                label = space.properties.name;
            }
            else if (typeof(space.properties.ref) !== 'undefined'){ 
                label = space.properties.ref;
            }
            
            // Geometry creation. Depending of its type we will assign a different style                    
            if (typeof(space.properties.stairs) !== 'undefined'){ // Stairs
                var geometry = new L.Polygon(pointList, {color: '#efe3d6',fillColor: '#d6d3d6', opacity: 1, fillOpacity:1, smoothFactor: 1 });
                label = 'stairs'
            }   
            else if (space.properties.indoor==="level" ){ // Level outline                
                var geometry = new L.Polygon(pointList, {stroke: false, fillColor: 'white', opacity: 1, fillOpacity:1 });
            } 
            else if (space.properties.indoor==="room" ){  // Room              
                var geometry = new L.Polygon(pointList, {color: '#efe3d6', weight:2, fillColor: '#fffbef', opacity: 1, fillOpacity:1,  smoothFactor: 1 });
            }   
            else if (space.properties.indoor==="corridor" ){ //Corridor                
                var geometry = new L.Polygon(pointList, {stroke: false, fillColor: 'white', opacity: 1, fillOpacity:1 });
                if (label.length ==0)
                    label='corridor'
            }    
            else if (space.properties.indoor==="elevator" ){ // Elevator              
                var geometry = new L.Polygon(pointList, {color: '#efe3d6',fillColor: '#d6d3d6', opacity: 1, fillOpacity:1,  smoothFactor: 1 });
                label = 'elevator'
            }   
            else if (space.properties.indoor=="wall" ){ // Wall                                    
                var geometry = new L.Polyline(pointList, {color: '#efe3d6', opacity: 1, weight:2, fillOpacity:1, smoothFactor: 1});
            } 
            geometry.bindPopup(label);
        }
    }
    return geometry
}
{% endhighlight %}

![Our Leaflet map showing a popup of a corridor in our Smart Building](/images/leaflet-popup.png "Leaflet popup"){:class="image center"}

And that's all. We have explained a basic usage of our indoor data for displaying maps. Now you can experiment changing styles, adding markers, making custom controls, including more objects from the indoor data such as doors, etc. You can get the full source of this article in the [Osiris's leaflet example repository][examples-repo]{:target="_blank"}.




[leaflet-home]:   http://leafletjs.com/
[github-leaflet]: http://github.com/Leaflet/Leaflet
[map-uploading]: https://github.com/osiris-indoor/osiris#running-osiris
[build-instructions]: https://github.com/osiris-indoor/osiris#building-osiris
[sample-map]: https://github.com/osiris-indoor/sample-maps
[calling-osiris]: http://localhost:4000/documentation/2016/03/22/Calling-Osisris-Indoor-API.html
[lf-providers]: https://leaflet-extras.github.io/leaflet-providers/preview/
[mapping-howto]: https://github.com/osiris-indoor/sample-maps/wiki/How-to-map-a-building
[lf-layergroup]: http://leafletjs.com/reference.html#layergroup
[lf-polygon]: http://leafletjs.com/reference.html#polygon
[lf-popup]: http://leafletjs.com/reference.html#popup
[examples-repo]: https://github.com/osiris-indoor/osiris-examples