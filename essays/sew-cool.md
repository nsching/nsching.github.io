---
layout: essay
type: essay
title: "Sew What?"
date: 2024-04-25
published: true
labels:
  - Design Patterns
  - Software Engineering
---
<img width="200px" 
     class="rounded float-start pe-4" 
     src="../img/sewing.png" >

A Seamstress and a Software Engineer might have more in common than you think…

They both use patches to repair things!

They also similarly use design patterns. In sewing, those who have the time and talent to create an original piece from scratch often leave their patterns on the internet for others to use and create their own garments. People are free to use their pattern as an outline and adjust it to their wants, needs, and uses instead of just starting from scratch. It is a template for other sewers to use. This is similar to the concept of design patterns in software engineering because they are also like templates that can be used in many different scenarios.

## Mending the bridge between software and sewing
Design patterns are general reusable solutions to common problems in software design. We use them as a launching pad to give us a head start. Why would we want to reinvent a concept when someone went through all the hard work of doing it for us already? It is a way for us to innovate and go further faster. Design patterns can show relationships and interactions between classes and objects without the trouble of having to specify the final application ones to keep it general and flexible to use. There are different types of patterns. For example, in Object Oriented Design there is creational, structural, and behavioral patterns. However, the influence of design patterns spans much further than just Object Oriented Design. It can be applied in architecture, databases, and user interfaces.

## What's sew interesting?
The Singleton design pattern is a creational pattern that makes sure a class has only one instance and provides a global point of access to that mentioned instance. The class is responsible for creating its own unique instance and making sure no other instance is created. Key features of this design pattern include a private constructor, a static instance, lazy initialization, and thread safety. I’ve used this design pattern before I even knew what a design pattern was or what a Singleton Pattern was! It turns out that in a coding exercise we had to do for homework we used exactly the Singleton Pattern. This project was called BowFolios because at my university we are the Rainbow Warriors and it allowed users to share their profiles, projects, and interests. It used this design pattern where the class “ProfileCollection” manages the access to the MongoDB collection called “ProfileCollection.” Only a single instance of this class is created and exported to the rest of the application. Clients can invoke methods on this instance to change and manipulate the collection. The variable “Profiles” is the singleton instance of the ProfileCollection class in this case.

## Sew what then?
In the end, we can see design patterns in different disciplines, such as sewing and software engineering, highlighting similarities in problem-solving approaches. Just as seamstresses use patterns as templates for creating garments, software engineers leverage design patterns as reusable solutions to common design problems. These patterns not only provide a foundation for innovation but also enhance efficiency by avoiding the need to reinvent the wheel. By recognizing and applying these patterns, developers can streamline their development process and create more robust and flexible software solutions.

