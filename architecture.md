---
layout: page
title: Architecture
image: pic08.jpg
subtitle: Learn more technical details about Osiris
---

Cities are becoming more intelligent over the time, producing huge amount of data. Citizens living in Smart Cities must have applications that allow access to their services and data. Having them handy, maybe on our smartphones and in the near future accessible in our own wearable technology, is also a challenge.   Osiris is a system capable of serving indoor geolocated information from a NoSQL geospatial database and providing a Restful web interface which is perfect for creating smartphone applications.

The  architecture of Osiris consists of three different elements: a data layer, a set of public REST web services and a client application.

The data layer persistence is relayed to MongoDB.  Nowadays, there are many applications and websites based on geolocation that require infrastructure for storing  and processing geographic information. MongoDB provides this capability and also geospatial queries. In addition, it supports REST and JSON specifications and presents  high performance and good horizontal scalability.

The data layer is accessed by the public REST web services. REST is based on some well know standards (HTTP, JSON, URL), is a lightweight protocol  compared to SOAP and provides easy scalability.

Finally, a sample mobile client is implemented and provided as an Android mobile application. It consumes the REST services and has the feature of  showing the indoor maps and additional information such as points of interest.