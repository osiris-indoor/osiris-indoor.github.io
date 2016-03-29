---
layout: post
title:  "Calling Osiris Indoor API in your Smart City projects"
date:   2016-3-22 13:39:17
image: chromeARC.png
categories: documentation
author: Guillermo Amat
---
This post intends to be an introduction to the use of web services provided by Osiris. These services, are available through REST protocol and provide response data in JSON format. For further details see the [API reference][api-docs] section of this site.

As a tool for making requests to a REST service we will use [Advanced REST Client][arc-plugin] (ARC), which is a plugin for Google Chrome.

For the example calls, all services will be available from our local server using the following address:

{% highlight javascript %}
http://localhost:8020
{% endhighlight %}


Another important point is that  we need an application key that identifies the data set to use in all our requests. This identifier is the same you have used to upload the map. In example, if uploaded your map using a command similar to this one:

{% highlight sh %}
sudo ./import_map.sh Aachen /path_to_my_map/map.osm
{% endhighlight %}

Then, the application identifier will be Aachen.

Now, let's imagine we have our map already uploaded and we want to retrieve some information. In order to do that, we will invoke the search method. The query to perform this operation has the same syntax defined by [MongoDB's reference manual][mongo-reference]. So, to get all the indoor elements which are within a set of coordinates that compound  a polygon and belong to the second level, we can write something like this:

{% highlight JavaScript %}
{
    geometry: {
        $geoWithin: {
            $geometry: {
                type: "Polygon",
                coordinates: [
                    [
                        [6.071222245392562, 50.77815921201758],
                        [6.071222245392562, 50.78107365547241],
                        [6.078904307196694, 50.78107365547241],
                        [6.078904307196694, 50.77815921201758],
                        [6.071222245392562, 50.77815921201758]
                    ]
                ]
            }
        }
    },
    properties.level: '2'
}
{% endhighlight %}

Note that the first and the last point (or coordinates) of the polygon are the same.

At this time we can try this query in ARC. Just translating everything we have shown before, the form can be filled as follows:


![API example call in ARC](/images/api-example1.png "Calling the search method in ARC"){:class="image center"}


Analyzing the image, we can see the following:

The URL of the service is shown below. It is the one corresponding to the search method and we are specifying that we want to look in the MAP layer and limiting the quantity of results to 1000. 
{% highlight JavaScript %}  
http://127.0.0.1:8020/osiris/geolocation/territory/search;pageSize=1000?layer=MAP  
{% endhighlight %}

We are making a POST call using "Aachen" as an application identifier and telling the server that the content type will be json.

{% highlight JavaScript %}
api_key: Aachen
Content-Type: application/json
{% endhighlight %}

Our query is the same that we explained before. 

{% highlight JavaScript %}
{
    geometry: {
        $geoWithin: {
            $geometry: {
                type: "Polygon",
                coordinates: [
                    [
                        [6.071222245392562, 50.77815921201758],
                        [6.071222245392562, 50.78107365547241],
                        [6.078904307196694, 50.78107365547241],
                        [6.078904307196694, 50.77815921201758],
                        [6.071222245392562, 50.77815921201758]
                    ]
                ]
            }
        }
    },
    properties.level: '2'
}
{% endhighlight %}

On the other side, the server has responded with a 200 HTTP code, this means that our petition was well formed and has been processed correctly. Moreover the body of this response is the result of the query in json format.

{% highlight json %}
[{"properties":{"note":"sichtbar im Flur und hier Hilfspositionen für das Haupt-Raumraster","level":"2","@type":"node","indoor":"column","@id":"-8086"},"propertiesRelations":[],"id":"-8086","geometryDTO":{"type":"point","latitude":50.77941974902,"longitude":6.07461458061}},{"properties":{"note":"Treppenhaus","level":"2","@type":"way","stairs":"yes","indoor":"corridor","@id":"-8087"},"propertiesRelations":[],"id":"-8087","geometryDTO":{"type":"lineString","collectionPointDTO":[{"type":"point","latitude":50.77938294902,"longitude":6.07462558061},{"type":"point","latitude":50.77936944902,"longitude":6.07469598061},{"type":"point","latitude":50.77933754902,"longitude":6.07468058061},{"type":"point","latitude":50.77935114902,"longitude":6.07461018061},{"type":"point","latitude":50.77938294902,"longitude":6.07462558061}],"centroidDTO":{"type":"point","latitude":50.779360269108864,"longitude":6.074653096655062}}},{"properties":{"note":"sichtbar im Flur und hier Hilfspositionen für das Haupt-Raumraster","level":"2","@type":"node","indoor":"column","@id":"-8088"},"propertiesRelations":[],"id":"-8088","geometryDTO":{"type":"point","latitude":50.77942654902,"longitude":6.07458018061}},{"properties":{"note":"sichtbar im Flur und hier Hilfspositionen für das Haupt-Raumraster","level":"2","@type":"node","indoor":"column","@id":"-8089"},"propertiesRelations":[],"id":"-8089","geometryDTO":{"type":"point","latitude":50.77945154902,"longitude":6.07462988061}},{"properties":{"level":"2","@type":"way","indoor":"elevator","@id":"-8090"},"propertiesRelations":[],"id":"-8090","geometryDTO":{"type":"lineString","collectionPointDTO":[{"type":"point","latitude":50.77938024902,"longitude":6.07453868061},{"type":"point","latitude":50.77939154902,"longitude":6.07454418061},{"type":"point","latitude":50.77939804902,"longitude":6.07454728061},{"type":"point","latitude":50.77940094902,"longitude":6.07453248061},{"type":"point","latitude":50.77940594902,"longitude":6.07450648061},{"type":"point","latitude":50.77938814902,"longitude":6.07449798061},{"type":"point","latitude":50.77938024902,"longitude":6.07453868061}],"centroidDTO":{"type":"point","latitude":50.77939310780041,"longitude":6.074522625421104}}},{"properties":{"note":"sichtbar im Flur und hier Hilfspositionen für das Haupt-Raumraster","level":"2","@type":"node","indoor":"column","@id":"-8091"},"propertiesRelations":[],"id":"-8091","geometryDTO":{"type":"point","latitude":50.77945844902,"longitude":6.07459548061}},{"properties":{"note":"sichtbar im Flur und hier Hilfspositionen für das Haupt-Raumraster","level":"2","@type":"node","indoor":"column","@id":"-8092"},"propertiesRelations":[],"id":"-8092","geometryDTO":{"type":"point","latitude":50.77935624902,"longitude":6.07458388061}},{"properties":{"note":"sichtbar im Flur und hier Hilfspositionen für das Haupt-Raumraster","level":"2","@type":"node","indoor":"column","@id":"-8093"},"propertiesRelations":[],"id":"-8093","geometryDTO":{"type":"point","latitude":50.77922884902,"longitude":6.07452238061}},{"properties":{"note":"sichtbar im Flur und hier Hilfspositionen für das Haupt-Raumraster","level":"2","@type":"node","indoor":"column","@id":"-8094"},"propertiesRelations":[],"id":"-8094","geometryDTO":{"type":"point","latitude":50.77936274902,"longitude":6.07455028061}},{"properties":{"note":"sichtbar im Flur und hier Hilfspositionen für das Haupt-Raumraster","level":"2","@type":"node","indoor":"column","@id":"-8095"},"propertiesRelations":[],"id":"-8095","geometryDTO":{"type":"point","latitude":50.77923554902,"longitude":6.07448888061}},{"properties":{"note":"sichtbar im Flur und hier Hilfspositionen für das Haupt-Raumraster","level":"2","@type":"node","indoor":"column","@id":"-8096"},"propertiesRelations":[],"id":"-8096","geometryDTO":{"type":"point","latitude":50.77948334902,"longitude":6.07464528061}},{"properties":{"note":"sichtbar im Flur und hier Hilfspositionen für das Haupt-Raumraster","level":"2","@type":"node","indoor":"column","@id":"-8097"},"propertiesRelations":[],"id":"-8097","geometryDTO":{"type":"point","latitude":50.77949014902,"longitude":6.07461088061}},{"properties":{"note":"sichtbar im Flur und hier Hilfspositionen für das Haupt-Raumraster","level":"2","@type":"node","indoor":"column","@id":"-8098"},"propertiesRelations":[],"id":"-8098","geometryDTO":{"type":"point","latitude":50.77926064902,"longitude":6.07453778061}},{"properties":{"note":"sichtbar im Flur und hier Hilfspositionen für das Haupt-Raumraster","level":"2","@type":"node","indoor":"column","@id":"-8099"},"propertiesRelations":[],"id":"-8099","geometryDTO":{"type":"point","latitude":50.77926734902,"longitude":6.07450418061}},{"properties":{"note":"sichtbar im Flur und hier Hilfspositionen für das Haupt-Raumraster","level":"2","@type":"node","indoor":"column","@id":"-8100"},"propertiesRelations":[],"id":"-8100","geometryDTO":{"type":"point","latitude":50.77938814902,"longitude":6.07459918061}},{"properties":{"note":"sichtbar im Flur und hier Hilfspositionen für das Haupt-Raumraster","level":"2","@type":"node","indoor":"column","@id":"-8101"},"propertiesRelations":[],"id":"-8101","geometryDTO":{"type":"point","latitude":50.77929244902,"longitude":6.07455308061}},{"properties":{"note":"sichtbar im Flur und hier Hilfspositionen für das Haupt-Raumraster","level":"2","@type":"node","indoor":"column","@id":"-8102"},"propertiesRelations":[],"id":"-8102","geometryDTO":{"type":"point","latitude":50.77939474902,"longitude":6.07456478061}},{"properties":{"note":"sichtbar im Flur und hier Hilfspositionen für das Haupt-Raumraster","level":"2","@type":"node","indoor":"column","@id":"-8103"},"propertiesRelations":[],"id":"-8103","geometryDTO":{"type":"point","latitude":50.77929914902,"longitude":6.07451958061}},{"properties":{"note":"sichtbar im Flur und hier Hilfspositionen für das Haupt-Raumraster","level":"2","@type":"node","indoor":"column","@id":"-8104"},"propertiesRelations":[],"id":"-8104","geometryDTO":{"type":"point","latitude":50.77932454902,"longitude":6.07456858061}},{"properties":{"note":"sichtbar im Flur und hier Hilfspositionen für das Haupt-Raumraster","level":"2","@type":"node","indoor":"column","@id":"-8105"},"propertiesRelations":[],"id":"-8105","geometryDTO":{"type":"point","latitude":50.77933094902,"longitude":6.07453498061}}]
{% endhighlight %}

Let's go into detail for one of the items of the returned array:

{% highlight json %}
{
	"properties": {
		"note": "Treppenhaus",
		"level": "2",
		"@type": "way",
		"stairs": "yes",
		"indoor": "corridor",
		"@id": "-8087"
	},
	"propertiesRelations": [],
	"id": "-8087",
	"geometryDTO": {
		"type": "lineString",
		"collectionPointDTO": [{
			"type": "point",
			"latitude": 50.77938294902,
			"longitude": 6.07462558061
		}, {
			"type": "point",
			"latitude": 50.77936944902,
			"longitude": 6.07469598061
		}, {
			"type": "point",
			"latitude": 50.77933754902,
			"longitude": 6.07468058061
		}, {
			"type": "point",
			"latitude": 50.77935114902,
			"longitude": 6.07461018061
		}, {
			"type": "point",
			"latitude": 50.77938294902,
			"longitude": 6.07462558061
		}],
		"centroidDTO": {
			"type": "point",
			"latitude": 50.779360269108864,
			"longitude": 6.074653096655062
		}
	}
}
{% endhighlight %}

The structure is quite readable and corresponds to a corridor in the second level that can be drawn using the coordinates from the collectionPointDTO property. Moreover, in your applications you can use different border and fill colors depending on the indoor tag value.

[api-docs]:   /api.html
[mongo-reference]: https://docs.mongodb.org/manual/tutorial/query-documents/
[arc-plugin]: https://chrome.google.com/webstore/detail/advanced-rest-client/hgmloofddffdnphfgcellkdfbfbjeloo
