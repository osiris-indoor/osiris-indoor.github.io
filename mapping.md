---
layout: page
title: Mapping
image: mapping-skyline.jpeg
subtitle: How to map buildings for Osiris
weight: 3
---

To see a complete description of the mapping process, visit the wiki [page](https://github.com/osiris-indoor/sample-maps/wiki/How-to-map-a-building).

This section introduces the generation of all the information concerning a building indoor map. It is  achieved in three steps:

1. First, all spaces and access structures  have to be drawn. 
2. Then, every room, stair, elevator, etc. has to be tagged in order to provide its name and type.
3. Finally the resultant file is uploaded to the server and processed to be stored in a MongoDB storage.

There are several requirements such as taking existing data from Open Street Maps, the ability of manipulate easily GIS data, and the capacity to export the results to an XML file or even a JSON format, that made Java Open Street Map editor (JOSM) the best choice for indoor mapping.  At the moment of needing to export information to Osiris (third step), the format used is GeoJSON due to it is more appropriate for the treatment and storage in MongoDB.

#Joining JOSM and MongoDB
Osiris has  a NoSQL engine (MongoDB), which stores points or areas of interest, allowing to assign properties to those points or areas and perform  searches based on geometry or properties. On the other hand, using JOSM, the data is saved in OSM format (a XML file format that contains all the information needed  by Open Street Map).

In order to convert information between both formats, Osiris executes several processes that transform everything to GeoJSON format. Such  transformation is applied to all the elements of the OSM format: nodes, ways and relations transforming them to GeoJSON geometric elements, which are points, linestrings and polygons. The XML parsing process is necessary to keep in memory the format and then process and transform it to GeoJSON before storing in the database. After that, a geospatial index is generated in order to optimize queries.

In addition to the map data, there is a second collection to keep points of interest; this allows separate searches, as if they were different layers, depending on the data  that we want to get. To sum up, it is used to save and look up points of interest (places) and geometries outside a building.