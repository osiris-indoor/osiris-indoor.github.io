---
layout: post
title:  "Osiris blog and documentation available"
date:   2016-2-24 13:39:17
image: SmartUTAD.png
categories: documentation
---
As you can see, we are starting a new blog to support this project. We will try to write some use cases and examples to clarify how we use and how anyone can use Osiris.

But first, let's speak about documentation. Now you have available a link to the [API reference][api-docs] on the top menu of this site. There you will see all the methods you can use to build your applications. To briefly explain what you will find in that page, we are going to see how the methods are organized:

- Features: we use the Features API to store points of interest and then retrieve their information. You can store, update or delete a feature using the appropriate method. Of course you can obtain its data using its identifier. 
- Map: there is am method to obtain the map file you can use with Mapsforge component. In addition there is a second method to get the meta data of the map which is useful to know if there is a newer version uploaded to the server.
- Search: this is an important method to create applications. Using Search you can get the features (points of interest) or the geometries to draw the building parts in your application.


[api-docs]:   /api.html
