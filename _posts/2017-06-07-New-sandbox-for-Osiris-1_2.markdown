---
layout: post
title:  "New release package for Osiris 1.2"
description: "A new binary package for Osiris 1.2 is available for download"
date:   2017-06-07 9:00:00
image: sandbox-by-adam-whitlock.jpg
imagedesc: "Sandbox"
categories: news
author: Guillermo Amat
---

A new release package is available to download from [Github](https://github.com/osiris-indoor/osiris/releases/tag/v1.2.0). The binaries include all the new features belonging to Osiris 1.2 such as the [authentication](http://osiris-indoor.github.io/news/2017/02/06/Osiris-1_2-brings-security.html) method. The objective of this package is to reduce the complexity of the installation process as it it not necessary to build the source code.


To use this software follow these steps:

1. [Download](https://github.com/osiris-indoor/osiris/releases/download/v1.2.0/Osiris.zip) the software
2. Unpack the file somewhere in your computer 
3. cd Osiris
4. Import a map using the command

    {% highlight sh %}
    sudo ./import_map.sh MyMapId /path_to_my_map/map.osm
    {% endhighlight %}

{:start="5"}
5. Create a password for a user

    {% highlight sh %}
    java -jar osiris-encrypt-password.jar your_password
    {% endhighlight %}

    This will output: zZpRQvC79xErr9l0yz8Wwg==

{:start="6"}
6. Create a collection in Mogo for the credentials. Note that we will use MyMapId as the identifier of the map / application:

    {% highlight sh %}
    mongo
    use osirisGeolocation
    db.createCollection("credentials_app_MyMapId", {} );
    db.credentials_app_MyMapId.insert({"_id" : "your_username", "password" : "zZpRQvC79xErr9l0yz8Wwg=="})
    db.commit
    {% endhighlight %}

{:start="7"}
7. [Optional] Add as many users as you want using again the password tool and the db.credentials_app_MyMapId.insert command 

8. Run Osiris 

    {% highlight sh %}
    ./osiris.sh
    {% endhighlight %}



Now you can check if everything is working. Remember that now the services require credentials when are invoked. In order to do this, the user name and password you selected before should be encoded in Base64 (ISO-8895-1) using the format user:password In our example your_username:your_password is  eW91cl91c2VybmFtZTp5b3VyX3Bhc3N3b3Jk and this credential has to be added to the Authorization header: 
  
  Authorization: Basic eW91cl91c2VybmFtZTp5b3VyX3Bhc3N3b3Jk


As an example using curl, you can call the search service this way:

{% highlight sh %}
curl -i -H "api_key: MyMapId" -H "Content-Type: application/json" -H "Authorization: Basic eW91cl91c2VybmFtZTp5b3VyX3Bhc3N3b3Jk"  -X POST -d '{ $and: [ {properties.indoor:{$exists: true}} , {properties.indoor: "level"}] }' http://127.0.0.1:8020/osiris/geolocation/territory/search?layer=MAP&pageSize=2000
{% endhighlight %}
 

If everything is right, you will see a JSON with information of your buildings.

