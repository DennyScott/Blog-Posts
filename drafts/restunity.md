#Connecting Your Unity Project to a REST API

##Introduction
While developing a game, there a number of reasons why you would want to connect to a server. Downloading new assets, such as models, or collecting data from an external source. Downloading bundle assets can be done through your own server, which allows your game to connect to a server and download the most recent versions of bundle assets. Suppose your game also allowed users to see if that item was available at amazon, and for what price? If we had the sku number available, we could connect to Amazon's api, and check the price and availability of that item. The most common way to connect to external API's these days, is through a RESTful API.

##What is a REST API
In simple terms, a RESTful api is a common approach to creating scalable web services. It provides users with endpoints to collect and create data. For example, a url like www.something.com/users could return a JSON of User data. Of course, their is often more security involved with these calls, but this is a good starting point. For a further definition of rest, lets take a look at Wikipedia's definition

> Representational State Transfer (REST) is a software architecture style consisting of guidelines and best practices for creating scalable web services.[1][2] REST is a coordinated set of constraints applied to the design of components in a distributed hypermedia system that can lead to a more performant and maintainable architecture.[3]

>REST has gained widespread acceptance across the Web[citation needed] as a simpler alternative to SOAP and WSDL-based Web services. RESTful systems typically, but not always, communicate over the Hypertext Transfer Protocol with the same HTTP verbs (GET, POST, PUT, DELETE, etc.) used by web browsers to retrieve web pages and send data to remote servers.[3]

##XML or JSON

##Setting up your own server
Out of scope for this writeup, give some small details of things like sails.

##Parsing JSON in Unity

##Conclusion
