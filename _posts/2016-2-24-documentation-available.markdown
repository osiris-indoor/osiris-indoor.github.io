---
layout: post
title:  "Osiris blog and documentation available"
date:   2016-2-24 13:39:17
image: SmartUTAD.png
categories: documentation
---
As you can see, we are starting a new blog to support this project. We will try to write some use cases and examples to clarify how we use and how anyone can use Osiris. 

To get an idea of the possibilities offered by Osiris, here you can find a screenshot of one of our applications. On it there are two points of interest outside the building, the indoor layout and the level selector. All of this can be shown due to the information requested from Osiris.

But first, let's speak about documentation. Now you have available a link to the [API reference][api-docs] on the top menu of this site. There you will see all the methods you can use to build your applications. To briefly explain what you will find in that page, we are going to see how the methods are organized:

- Features: we use the Features API to store points of interest and then retrieve their information. You can store, update or delete a feature using the appropriate method. Of course you can obtain its data using its identifier. 
- Map: this API provides a method to obtain the map file that you can use with Mapsforge component. 
- Metadata: Used to get the meta data of the map, which is useful to know if there is a newer version uploaded to the server.
- Search: this is an important method to create applications. Using Search you can get the features (points of interest) or the geometries to draw the building parts in your application.

The list of methods is as follows

API | Method |URL
---|---
METADATA | GET | /osiris/geolocation/territory/map/metadata
MAP | GET | /osiris/geolocation/territory/map/file 
FEATURES | DELETE | /osiris/geolocation/territory/feature/{idFeature} 
FEATURES | GET | /osiris/geolocation/territory/feature/{idFeature} 
FEATURES | POST | /osiris/geolocation/territory/feature 
FEATURES | PUT | /osiris/geolocation/territory/feature/{idFeature} 
SEARCH |POST | /osiris/geolocation/territory/search 

Now is time to try yourself, [download][sandbox-download] the sandbox and follow the [instructions][osiris-readme] to create a sample project.


<center>
<ul class="actions">
    <li><a href=" https://github.com/osiris-indoor/osiris/releases" class="button icon fa-download">Download</a></li>
</ul>
</center>


[api-docs]:   /api.html
[sandbox-download]: https://github.com/osiris-indoor/osiris/releases
[osiris-readme]: https://github.com/osiris-indoor/osiris#osiris
