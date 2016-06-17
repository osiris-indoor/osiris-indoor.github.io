---
layout: post
title:  "Adding Points of Interest and displaying them in Leaflet"
description: "How to add Points of Interest using CURL and show them in  your Leaflet Map"
date:   2016-6-16 9:39:17
image: osiris-pois.png
imagedesc: "LeafletJS web application showing our indoor map with POIs"
categories: tutorial
author: Guillermo Amat
---

In a previous [previous post][leaflet-map]{:target="_blank"}  we wrote about Leaflet and how to show our maps in it. Now, we will see another interesting feature of Osiris that is the use of Points of Interest. Before that, we should explain what is a POI for us. In Osiris, POIs are called Features and are used to report about interesting services or places, being usually (but not necessary) outdoors. For instance, metro stations, bus stops, restaurants, etc. can be included as a POIs in our application.

## Inserting a POI

Technically, a [Feature][api-doc] includes its coordinates and some more data we want to store as properties. Even more, Features have a geometry but we usually represent them as a simple point. So, if we want to include a bus stop in our map we can build a query like this:

{% highlight json %}
{ "geometryDTO": {"type": "point", "latitude": 39.494143264768105, "longitude": -0.40192766277868941 }, "properties": {"name": "Bus stop", "type": "Transport", "subtype": "Bus", "description": "Bus stop. Lines 62, 63 and N3"}}
{% endhighlight %}

The main concepts here are geometryDTO and properties. The first determines the type of geometry of the feature (a point in this case) and the geographic location of the POI. As this feature is a point, the representation will be only its coordinates.
Secondly, the properties field is a container of data. For our POIs we want to store a name, its type and subtype and a description. Anyway, it is possible to store any other combination of keys an values, it just depends on the application design.

Now we want to send this information to our Osiris instance. To do that we use the previous JSON string in a CURL petition to the territory/feature method:

{% highlight sh %}
curl -i -H "api_key: valenciaconference" -H "Content-Type: application/json" -X POST -d '{ "geometryDTO": {"type": "point", "latitude": 39.494143264768105, "longitude": -0.40192766277868941 }, "properties": {"name": "Bus stop", "type": "Transport", "subtype": "Bus", "description": "Bus stop. Lines 62, 63 and N3"}}' http://localhost:8020/osiris/geolocation/territory/feature
{% endhighlight %}

In order to check we have inserted anything, we can do a generic search like this:

{% highlight sh %}
curl -i -H "api_key: valenciaconference" -H "Content-Type: application/json"  -X POST -d '{}'  http://localhost:8020/osiris/geolocation/territory/search?layer=FEATURES
{% endhighlight %}

And Osiris will return this JSON

{% highlight json %}
[{"properties":{"name":"Bus stop","type":"Transport","subtype":"Bus","description":"Bus stop. Lines 62, 63 and N3"},"propertiesRelations":[],"id":"5762b9482b1803ea835b53d4","geometryDTO":{"type":"point","latitude":39.494143264768105,"longitude":-0.4019276627786894}}]
{% endhighlight %}

## Including the POIs in our map

In our example, we will use type and subtype values to get an icon, using the format type-subtype.png (i.e transport-bus.png). In addition, we will create a [PopUp][leaflet-ref-popup]{:target="_blank"}  for our [Marker][leaflet-ref-marker]{:target="_blank"}   assigning the description data to it.

Having the POI inserted in our database, we wan to show it on our map. We will modify slightly our queryMap function written in the previous tutorial to search in both MAP and FEATURES collections. The only thing we need is to add a parameter to get the layer value for the search.

{% highlight js %}
/**
 * Performs an AJAX call using JQuery
 **/
function queryMap(api, layer, key, query, callbackFunc){
    $.ajax({
        url:"http://localhost:8020/osiris/geolocation/territory/search?layer="+layer+"&pageSize=1000",   // Osiris server URL
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

Of course, the calls to that function in the index.html should include the new parameter:

{% highlight js %}
 // Let's add the POIs
 queryMap(api_key,"FEATURES","{}",drawPOIs);
{% endhighlight %}

Finally, we need a function to draw the markers using the JSON data obtained from Osiris.

{% highlight js %}
/** 
 * This function adds the POIs. It expects points with a description field in the properties
 **/
function drawPOIs(data){
    var pois = data// JSON.parse(data);
    for (i = 0; i < pois.length; i++){    
        if (pois[i].hasOwnProperty("geometryDTO") && typeof(pois[i].properties) !== 'undefined'){            
            if (typeof(pois[i].geometryDTO.type) !== 'undefined'){
                var lat=pois[i].geometryDTO.latitude;
                var long=pois[i].geometryDTO.longitude;
                var iconName = ("img/"+pois[i].properties.type+"-"+pois[i].properties.subtype+".png").toLowerCase()
                var myIcon = L.icon({iconUrl: iconName,iconSize: [64, 64]});
                L.marker([lat,long],{icon: myIcon}).addTo(mymap).bindPopup(pois[i].properties.description).openPopup();
            }
        }
    }
}
{% endhighlight %}


![Our POI displaying its description](/images/osiris-pois-2.png "Creating a 2D model"){:class="image center"}

The source code of this tutorial can be found in our examples [repository][example-repo]{:target="_blank"} at GitHub


[leaflet-map]:  http://osiris-indoor.github.io/tutorial/2016/04/11/Using-Leaflet-to-draw-your-building-in-a-Smart-City-project.html
[leaflet-ref-popup]: http://leafletjs.com/reference.html#popup
[leaflet-ref-marker]: http://leafletjs.com/reference.html#marker
[example-repo]: https://github.com/osiris-indoor/osiris-examples/tree/master/leaflet-pois
[api-dopc]: http://osiris-indoor.github.io/api.html


