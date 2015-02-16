#Connecting Your Unity Project to a REST API

##Introduction
While developing a game, there a number of reasons why you would want to connect to a server. Downloading new assets, such as models, or collecting data from an external source. Downloading bundle assets can be done through your own server, which allows your game to connect to a server and download the most recent versions of bundle assets. Suppose your game also allowed users to see if that item was available at amazon, and for what price? If we had the sku number available, we could connect to Amazon's api, and check the price and availability of that item. The most common way to connect to external API's these days, is through a RESTful API.

##What is a REST API
In simple terms, a RESTful api is a common approach to creating scalable web services. It provides users with endpoints to collect and create data using the same HTTP calls to collect web pages (GET, POST, PUT, DELETE). For example, a url like www.something.com/users could return a JSON of User data. Of course, there is often more security involved with these calls, but this is a good starting point. For a further definition of rest, I recommend you check out the [wikipedia](http://en.wikipedia.org/wiki/Representational_state_transfer) article.

##XML or JSON

##Setting up your own server
Out of scope for this writeup, give some small details of things like sails.

##Parsing JSON in Unity

##Conclusion
