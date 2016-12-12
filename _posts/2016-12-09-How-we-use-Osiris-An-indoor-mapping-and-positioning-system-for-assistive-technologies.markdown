---
layout: post
title:  "How we use Osiris. An indoor mapping and positioning system for assistive technologies"
description: "This article explains our experience in the AIDE project and how the use of new technologies could enhance a robotic system designed to assist people with disabilities."
date:   2016-12-09 9:39:17
image: aide-assistant.png
imagedesc: "Aide assistant listening a user located in the kitchen"
categories: news
author: Guillermo Amat
---

This article explains our experience in the AIDE project and how the use of new technologies could enhance a robotic system designed to assist people with disabilities. Our research group is contributing with Indoor Mapping, Indoor Positioning and Natural Language Processing. Osiris is the component which gives the Indoor Mapping capabilities. Its data  is used as contextual information offering the exact location of a person in a house. 

By modeling (mapping) a house and creating and indoor location system associated to that model, it is possible to give the location and additional contextual information to other software components, enabling for instance to determine the room where the robotic system (or the disabled person) is and also the objects normally found in such place and what actions can be done with them. In addition to this, Automatic Speech Recognition (ASR) and Natural Language Processing (NLP) technologies are useful for some users suffering disabilities that make impossible to use device interfaces we are used to, such as the computer mouse, buttons and tactile surfaces.  

As an example of this, Osiris provides information, to a computer vision component which recognizes objects. Knowing the space where the person is, facilitates the identification of an object by the camera, in other words, there are less items to compare with the one that is being analyzed.

A second example is the human language recognizer that interprets voice commands. The commands and objects are now in relation with the user location so, the system discards actions such as opening a fridge if the current position is any other than the kitchen.

## Architecture design
To do this, a Client/Server architecture is used. The server provides the intelligence and manages the conversation. This means that the semantic analysis of the dialog is carried by this part. The client software connects to the server through web services and sends the sentences recognized from the user.

![Fig 1. Basic architecture](/images/aide-basic-arch.png "Fig 1. Basic architecture"){:class="image center"}


### Natural Language Processing design
While the server contains the Natural Language Processing logic, the client implements the speech recognition and sends a transcription of what the user says to the server. Then, the server analyzes the texts and determines their meaning and tries to match a valid command. Whatever the result is, the server responds to the client with a message to the user. Lastly, the Text-to-Speech (TTS) implementation in the client outputs the responses returned by the server.

### Osiris and Indoor Location design
Osiris and the rest of AIDE project’s software is executed in a computer placed in a wheelchair. Due to this, it is possible to calculate constantly the current position of the user and decrease the number of possibilities of the actions that can be triggered by the user using the voice or sight.

The architecture of both Osiris and the indoor location system is divided in three layers: firstly, the data is stored in a MongoDB database and is accessed using a logic layer written in Java. The logic layer offers public services through RESTful APIs presenting the data in JSON format. These services can be consumed by any client application which in our case is the application executed in the wheelchair and corresponds to the third layer.

## About AIDE project
AIDE Project is creating a system of assistive technologies to increase the quality of the common daily activities that disabled people need to perform. Devices such as robotic exoskeletons, brain interfaces and EOG glasses are present in the project. In addition to this, AIDE will enable a voice interface that will recognize user’s commands and, because of that, it will act as general purpose user interface controlling several devices, for instance home lights, TVs or any other home automation system. The semantic analyzer will take into account the location of the user to extract a contextual meaning of what he or she is saying. 

Thanks to the contextual information retrieved from Osiris, all the components will have a better accuracy when operating, understanding better what the user wants but also having a set of valid actions and objects in relation with the space where the user is. Moreover, we are developing a user-friendly application that can be described as a home assistant for disabled people. The application makes use of a microphone and speakers providing a conversation experience between the user and the computer. So, it works by listening what the user says and interpreting the sentences as commands which are sent to other components of the robotic system (AIDE) to perform operations. 

- [AIDE Project website][aide-website]{:target="_blank"}  
- [AIDE Project facebook][aide-facebook]{:target="_blank"}  
- Aide Assistant in action:

<iframe width="560" height="315" src="https://www.youtube.com/embed/pUPo5jtIcr0" frameborder="0" allowfullscreen></iframe>

### Acknowledgments:  
This article is based on the paper presented to the [International Workshop on Assistive & Rehabilitation Technology(IWART)][iwart]{:target="_blank"}  

[aide-website]: http://aideproject.eu
[aide-facebook]: https://www.facebook.com/AIDE-Project-912826958798372
[iwart]: http://iwart2016.umh.es/
