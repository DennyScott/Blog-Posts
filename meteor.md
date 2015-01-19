
My Philosophy: Keep it simple. Often times, the best solution to a problem is to keep your solution simple. In that regards, I've noticed a steep learning curve when it comes to new developers using entering web development.
What is it like to 
Installing Node/NPM + packages. Make sure to use sudo for this one! This installs all our dependencies!
Installing and Learning Grunt/Gulp. We used Grunt, got use to it. Suddenly, a project had gulp. Knowledge is transferable, but not directly.
Installing and Learning Cordova → Phonegap → Ionic → Grunt/Gulp Emulation
Learning OAuth. Maintaining Tokens with users is challenging. Keeping this confidential and encrypted is tough. Managing user can be a challenge. Security concerns on doing it correct. Thats why we put our crack members on it!
Or using JSON web tokens, which follows some of the same principal. Difficult to maintain.
Learning OAuth2. Challenge connecting these API’s.
Learning Angular. Angular is the core of many of our client apps. Modularizing apps always us to reuse our rest api, and built multiple front end apps for different purposes. Angular is a different way at investigating data. It’s an MVW (Model-View-Whatever) framework, that allows you to separate the concerns of your app. Its declarative, which means we can collect api calls in our services/factories. We manipulate our dom in our services, build reusable elements in our directives, handle events in our controller, display in our views, handle global assets in our rootscope, and, in general, bind data using the scope of our current view. These are connected using ui router, which connects a state to a given controller/view combo, and are nested to create seamless transitions between pages. Also Ionic uses it, so you know, we had to learn it.
Learning REST. We want to allow multiple apps to often connect to our services, this is the standard way to connect this data. The only problem with rest...
Learning Socket.IO. Users responses are interconnected to one another. I add a file to a folder, I want the other user to see that update. This isn’t used with a rest api. :(
Learning Bootstrap. Cuz you know, bootstrap.
Learning and Installing Mongo. Mongo is also often many of our coops first introduction to document based databasing. This requires a different thinking process than traditional relational databasing. For the first time, data is nested in other data. And database redundancy is redefined. In fact, often time, the best “efficiency” is to heavily nest all data inside each other. Its get confusing, but, you know, fast and stuff.
Learning how to create “fixture” data, allowing users to have a common set of data to test their apps against. The simplest way to handle this is to go to your sails config, configure sails to clear the data on each start, then by  building up data in the bootstrap and wrapping it in logic to detect whether the data already exists. Then on each “lift” command, the fixtures should be reuploaded into the site. Once your site goes into production, or needs to persist some data, altering the configuration will be needed.
Installing and compiling for SASS.
Learning Sails. Scaffolding simplifies tasks. We’ve come up with a habit to horde one person's strength in it. Node JS, Express, and Asynchronous code is a major hurdle. We separate these two area’s for both modularity, and sanity.
Learning Express when Sails doesn’t work just like you want. Things don’t quite work the way you want. Now you gotta rework some of your code and logic within the system.
Learning Bower. Two package management systems is better then one! Except don’t use sudo with this one!
Realizing Bower you didn’t add bower packages correctly, and you now either need to put them all into your index, or run an automated grunt/gulp task to install them
Injecting you Bower dependencies into your angular project
Differentiating calls like:
cordova prepare, cordova emulate ios --target="iPhone (Retina 4-inch)"
phonegap serve
ionic build ios, ionic emulate ios, of course you may still need to run cordova prepare ios depending on your project set up. But sadly, Ionic does not let you decide a target device :(
grunt cordova → grunt emulate ios from a yeoman builder. This rebuilt the cordova package even when there was only html/js/css changed. Wrapped this with a “grunt ios” call, which only did a copy and emulate

Rebuilding our Apps on release/review days… and then again 20 minutes later when one of us REALLY want a fix in.
Running our apps in the web. We often will put up a temporary server so some of the guys here who don’t want to follow all the things listed can just go to a website to see a quick update. This is a simple process. What we often do is spin a new server, go to the server, add any dependencies we may need to run the server, apache, mongo, node, etc. Then we can either pull the project up and build the project over grunt to minify our necessary files. Other time’s we will build the project, and just pull this precompiled project into the server. We then run forever on the project, so any downtimes are reset on their own. Of course, while doing this we also spin up a forever server in the background, which needs to have its CORS cross site allowance working.


Other Things
Compiling and installing for coffeescript. Should be done through gulp or grunt.
Simple structure. Want to easily see the views? Go to the views folder, where more then 20 views can exist. Now we want its style, for that specific page, 20 css files. Controllers as well. What about minifying these for performance, we’re gonna need to do that thorugh minification proccesses that exist in Grunt/Gulp.
Reusability. Building declaritives are some of the best use cases for reusability. Use bower to install some of these great widgets! Thats for client side, awesome sails things? Use NPM. Wanna do a another client side? Ionic plugin? Bower. What about testing? Karma? Thats Npm. What about the front end runner like selenium? Npm. What about Making sure all Bowers are installed? Grunt. Which is an npm module, that is run through grunt. What about Sass? Sorry, NPM. Packages are a little dirty I suppose?
Simple

There was one other project I use to hear about that was “magic”. Developers at the time disliked how much was handled under the hood, out of their site. (Show Rails) If I remember correctly, this technology disappeared pretty quickly.

Things we’d like to use:
Simple Installation
Easy to Get Projects Running
Great Security
Database Schemas
Database Migrations
Real time Data
Simple Phonegap capabilities
Possible Use of Ionic
Use of Systems like Famous
Possible to use Angular?
Simpler Database Connection
Use of Rest API if desired
Don’t need Server
Don’t need Client
Simple Project Uploads to Server
Great Phonegap Capabilities
Analytics System
Ability to Connect to External Systems (PI)
Thorough Package System
Mature Routing System
Hot Code Pushes
Minification
Latency Compensation
Client Side Databasing

http://joshowens.me/how-to-scale-a-meteor-js-app/

Things to Outline
Security : http://pivotallabs.com/meteor-and-web-security/
Scaling : http://joshowens.me/how-to-scale-a-meteor-js-app/
Databases are cleaner since the query is on the client side
There is scaffolding tools to for this, but we’re going to hand code it.
Too Much Magic : http://differential.io/blog/the-not-so-real-problems-of-meteorjs

Order of Speech
Installing Meteor
Creating your first Meteor Project
meteor create chat
Creating a Simple Project Structure
add server, client, client/views, client/lib, client/stylesheets, client/helpers, client/views/home, collections, public.
Adding Bootstrap 3 as a Package
Show atmospherejs.com
meteor add mizzao:bootstrap-3
Clean From End for Designers
Show that we can change the button in home.js from ‘click button’ to ‘click #myClick’
Chat Room
First thing we’re going to do is set up a collection in our database. We have to use Mongo DB at this point, though it is possible to change the database, it’s not supported yet. Lets make our first collection!
	
	Make comment.js in collections
	
	comment.js
	Comments = new Mongo.Collection(‘comments’)
	
Lets try adding entries to our comments collection. 
Go to the chrome console
Comments.insert({content:'hello'})
Comments.find().fetch()
Lets Display our New Comments!
in home.html:
<head>
  <title>Meteor Comments</title>
  <meta name="viewport" content="width=device-width">
</head>

<body>
  <div class="container">
    <header>
      <div class="page-header text-center">
        <h3><span class="visible-lg-inline">Meteor</span> Comments</h3>
        {{> loginButtons}}
      </div>
      {{> commentInput}}
    </header>

    {{> commentBoxes}}

    <footer>
      <hr/>
      <p class="text-muted text-center"><small>Developed by <a href="http://ming.today">Ming</a>.</small></p>
    </footer>
  </div>
</body>

<template name="commentInput">
  <section id="comment-input">
    <p>Hi {{creatorName}}</p>
    <p><textarea rows="1" class="form-control"></textarea></p>
    <p class="text-right">
      <button class="btn btn-primary">Add a comment</button>
    </p>
  </section>
</template>

<template name="commentBoxes">
  <section class="comments">
    {{#each comments}}
      {{> commentBox}}
    {{/each}}
  </section>
</template>

<template name="commentBox">
  <div class="comment">
    <p class="content" >{{content}}</p>
  </div>
</template>


home.css
Copy from Projects/meetups/winnipegjs/client/views/home.css

home.js
Template.commentBoxes.helpers({
	comments : function() {
		return Comments.find({});
	}
})



Lets quickly clean up our files. Move Comment Box to its on file, commentInput to its own, and commentBox to its own.
Before we continue on, perhaps we should add users to this? Node JS has a number of complicated ways to authenticate users. Meteor simplifies that. Lets do this.
meteor add accounts-ui
meteor add accounts-password
The above measures will create a special Users Collection, email verifications methods, password resets. Lets also add Github!
meteor add accounts-github
After filling in some configuration data, we can have users sign in with their github! We won’t do this for now, but works in no time!
Lets create a new User. Create User
Lets see this new user. This can be found in Meteor.user. To see it, go to Meteor.user.find().fetch().
Lets add some extra values to our Data

commentBox.html
<template name="commentBox">
  <div class="comment">
    <p class="content {{mine}}" contenteditable="{{mine}}">{{content}}</p>
    <div class="text-muted text-right">
      <small class="owner text-muted {{mine}}">{{creatorName owner}}</small> ·
      {{#if mine}}
        <small><a href="/" class="remove">Remove</a></small> ·
      {{/if}}
      <small>{{showTime updatedAt}}</small>
    </div>
  </div>
</template>

commentBox.js
Template.commentBox.helpers({
	
	creatorName : function(id){
		var owner = Meteor.users.findOne({_id: id});
		if(owner){
			return owner;
		}else{
			return 'Anonymous'
		}
	},

	mine: function(){
		var id = Meteor.userId();
		if(id === this.owner){
			return true;
		}

		return false;
	},

	showTime: function(time){
		return new Date(time).toLocaleString();
	}

})

Notice in the above we did not wait for success, instead we wrote the code synchronously, and meteor will handle the asynchronous functions.

Look at our test data, we are both anonymous and we have an incorrect time. We need to include this data in our database while running. Well lets try adding it with this new data. But to just clear house, lets do a:
meteor reset.
Check our collection : Comments.find().fetch()
Create a new account, because our users were deleted again.
Test our user id using Meteor.userId() in the chrome console.
Add a new comment : Comments.insert({content:'This is a test', owner: Meteor.userId(), updated_at: new Date()})
Great everything shows up! What happens if we click remove? Nothing because we haven’t hooked it up yet! Lets do that now!
commentBox.js
Template.commentBox.events({
	'click .remove' : function(e){
		e.preventDefault();
		Comments.remove({_id: this._id});
	}
})


Now our remove works! But lets add a little bit of extra security on our server end!

collections/comment.js
Comments.allow({
	remove: function(userId, comment){
		return userId === comment.owner;
	}
});


There is one important issue left with our comment box. We are getting [Object object] instead of the user name. Lets get the users name instead of an object. Up to this point though, we’ve only allowed users to enter a username and password. What does their user data look like, and how can we customize it. Well let’s build out a “profile” for users. It may not come in handy here, but it’s the standard way to store data for users.
	
commentBox.js :
	
creatorName : function(id){
		var owner = Meteor.users.findOne({_id: id});
		if(owner){
			return displayName(owner);
		}else{
			return 'Anonymous'
		}
	},


collections/user.js

displayName = function(user) {
	if(user.profile && user.profile.name){
		return user.profile.name
	}
	console.log("in displayName");
	
	
	return user.emails[0].address
}


Now lets add a schema to our app. This will make sure that data that is passed through is only data that we allow it to. We won’t go to extensive into this, as there is a lot to it. If you wish to look further into it, you can check it out here: https://github.com/aldeed/meteor-collection2

Terminal:
meteor add aldeed:collection2

collections/user.js
var Schemas = {};

Schemas.User = new SimpleSchema({
	_id: {
		type: String,
	},

	emails: {
		type: [Object],
		optional:true
	},

		username: {
		type: String,
	},

	 "emails.$.address": {
        type: String,
        regEx: SimpleSchema.RegEx.Email
    },
    "emails.$.verified": {
        type: Boolean
    },
    createdAt: {
        type: Date,
				optional:true
    },

		profile: {
			type: Object,
			blackbox: true
		},

    services: {
        type: Object,
        optional: true,
        blackbox: true
    }
});

Meteor.users.attachSchema(Schemas.User);


Finally, we need to create the user profile when they create an account. Meteor has a hook in it called Accounts.onCreateUser(), that will let us alter data on the server when a user is created. Lets add that quick!

server/account.js

Accounts.onCreateUser(function(options, user) {
	user.profile = {
		name: options.username
	};

	user.username = options.username;
	user.createdAt = new Date();
	return user;
});


Now a name appears for each comment, despite if a user is logged in or not.

The last thing we need to make our app “runnable” is to allow a user to enter their comment in the comment box. Apparently adding comments through the terminal is a bad “user experience”. Lets do that!
Lets first say hello to our user!
	
commentInput.js:

Template.commentInput.helpers({

	creatorName : function(){
		var currentUser = Meteor.user();

		if(currentUser){
			return displayName(currentUser);
		}else{
			return "Stranger";
		}

	}

});

Now lets allow a user to actually enter their data through the text box.

First lets add a session that will keep track of our input.

commentInput.js

Meteor.startup = function(){
	Session.set('content', '');
}

Template.commentInput.rendered = function(){
	$('textarea').val(Session.get('content'));
}

Template.commentInput.events({
	'keyup textarea' : function(event){
		console.log(event);
		Session.set('content', $(event.currentTarget).val());
	}
});

Why do we do this, now if there was a hot code push on a server while the user was using this app, their current message wouldn’t be destroyed! Lets try it!

Enter some text, then change something in commentInput.html

Now lets add on click for the submit button!

commentInput.js
Template.commentInput.events({
	'keyup textarea' : function(event){
		Session.set('content', $(event.currentTarget).val());
	},

	'click button' : function(event, template){

		var userId = Meteor.userId()?Meteor.userId():'';

		Meteor.call('createComment', Session.get('content'), userId);
		$('comments').scrollTop(0);
		template.$('textarea').val('');
		Session.set('content', '');

	}
});

And now the actual create method. These all already exist, since we’ve been using the terminal, but lets create a more secure way by allowing the database to handle this insertion. We could add a schema to this, to make it more secure and validated, but for the sake of time, lets continue on for now.

collections/comment.js

Meteor.methods({
	createComment : function(commentContent, userId) {
		var timeNow = new Date();
		console.log(timeNow);
		var comment = {
			content: commentContent,
			createdAt: timeNow,
			updatedAt: timeNow
		}

		if(typeof userId !== ''){
			comment.owner = userId;
		}

		var commentId = Comments.insert(comment);

		return commentId;
	}
});



Everything looks great, but theres one issue. I can see everyones data, and they can see mine. We need to close this, and make our application more secure. The reason we can see everyones information is because Meteor places two packages in your system called “insecure” and “autopublish”. This allows us to see everything going on, and the point of it is for development. Once you are far enough along like us, you are expected to remove those packages. Lets try that!
meteor remove autopublish
meteor remove insecure
We’ve now lost all of our data. I can’t see anything, including my own posts. Perhaps things are a little TOO secure now! Lets take this in steps, lets first add back all posts. To do this, we need a Subscripe/Publish system. The server will publish the data it wants to, and the client will ask to see that data. This way, users are only able to get to the data we allow them to. First publishing!

server/publish.js

Meteor.publish('users', function(){
	return Meteor.users.find({});
});

Meteor.publish('comments', function() {
	return Comments.find({}, {sort: {createdAt: -1}, limit: 20});
});




This allows ALL posts to be seen, now lets try subscriptions.

client/helpers/subscriptions.js
Meteor.subscribe('users');
Meteor.subscribe('comments');

But now everyone can see everything again, how is this better? Well now we have a way of defining more rigorous rules on our data. Perhaps we only want users to see their own posts. Lets try that quick.

server/publish.js
Meteor.publish('comments', function() {
	return Comments.find({owner: this.userId});
});

Or maybe we only want users to see comments if they are logged in:

server/publish.js
Meteor.publish('comments', function() {
	if(this.userId){
		return Comments.find({});
	}
});


Last, but most importantly. Everyone can see the messages, but only if its written by someone important:

server/publish.js
Meteor.publish('comments', function() {
		return Comments.find({owner: ‘MyUserID’});
});

Lets take that all out. I want everyone to be able to see each post for now. But we can see how simple it would be to add a flag button. We could allow a button to set the message to flagged, and then the server would return only data that hasn’t been flagged. 

One thing we do want to block off, access to user data. We’ve completely opened that, but we want to close some of it off. Like services, where your password is stored. Lets to that.

server/publish.js
Meteor.publish('users', function(){
	return Meteor.users.find({}, {fields: {services:0}});
});

Perfect, things just got a bit more secure. There is some more awesome security tips and information about Meteor, which can be found here: https://www.discovermeteor.com/blog/meteor-and-security/ and http://pivotallabs.com/meteor-and-web-security/. Meteor has come along way in security, and is being used professinonally around the world. I can’t do enough justice for it’s security, but if your interested, take a look at those links, and find some talks done by Emily Stark, shes one of the core developers on the meteor security team, and was a graduate student at MIT in Computer Security, focusing on client-side cryptography using javascript. She also worked in security for google and microsoft.

Lets talk about Meteor databases for a second. Before we dive in deep, lets get this code online.
meteor deploy winnipegjschat.meteor.com
Alright, while this uploads for us. The “old way” of accessing web applications. A client would contact a server with a request. The server would take that request, and generate HTML to send back to the client. In recent days, we have taken to using REST servers with rich front end clients, with tools like Angular and Backbone. The Server will return data, and the front end is expected do the heavy lifting. Continuing that trend, in Meteor a User will have a set of “subscribed” data they are allowed to see. The Meteor Server will publish a subset of data that it allows the user to see, which will be sent over the wire, as data not html, and reside on the client for the duration of the session. That way, when the user attempts to query specialized versions of this data, the query can happen on the client side. When data is updated on the server side, either a oplog or “polling” service will notice this change and send the new information to each client that is allowed to see this data. When the client makes a change, the client side will assume this change is allowed and add this data to the client-side database. Now that a change has been made on the “client-side” database, it will send this data to the server. The server will check the authorization and any other security/verification measures included, and if allowed, add it to the mongo database. This data is then sent to the other users. If the data is not authorized, and denied by the server, the original client will their once correct data revert to an old state. This is called “latency compensation” and creates a more real time effect in the meteor framework. This entire process takes effect over what is a called a “DDP” connection, or Distributed Data Protocol. Let’s attempt to see this in effect. 

Everyone can hop on the website we made at winnipegjschat.meteor.com. Feel free to make some accounts, and try the chat. When one persons types their data in, they can see the data a split second before everyone else. Thats the latency compensation. That data is then passed through the DDP, and everyones site is updated. DDP takes place over websockets, and doesn’t need to be a meteor client. In fact, I’ve built both a laser tag game using Aurdinos and UDOO’s, and built a automated gardening sytems over DDP. It allowed all data to be transmited in real time to each device.

In fact, the database doesn’t even have to exist on the server end. Perhaps your making a game, and want to keep a lot of data about each user, but it isn’t needed on the server end of things. You can add a “client” end database as well. Of course, there are down sides to this, but it’s always there as an option!
I want to show is the ability to create data fixtures, and migrate that data. Lets make a quick fixture for our data. Perhaps when the site is first built, if their is no comments, one will be placed in. Lets do that:
	
	server/fixtures.js
	if(Comments.find({}).count() === 0){
	var timeNow = new Date();
	Comments.insert({content: 'Someone should say something!', createdAt: timeNow, updatedAt: timeNow});
}

	Perfect, we now have data fixtures that any of our development team members can share to each project with no problem. What about migrations? Lets add that quick, first lets add a package:

	meteor add percolatestudio:percolatestudio-migrations
	Then, we can add this:

	server/migrations.js

	


Migrations.add({
  version: 1,
  name: 'All comments get a title.',
  up: function() {
		Comments.update({}, {$set:{title: 'New Title'}}, {multi: true});
	},
  down: function() {
		Comments.update({}, {$unset:{title: 1}}, {multi:true});
	}
});

Migrations.migrateTo('latest');

Now we will be updated to version 1 of the migrations list, since we are looking at the latest. Perhaps we deicide we don’t want this newest version, lets revert back.

server/migrations.js
Migrations.migrateTo('0');

The last great thing. Meteor simple handles connecting to cordova. Lets do that now!
meteor add-platform ios
meteor run ios
And were done! Not only does this all work in real-time already, we can actually update our code in the emulator while its running. Awesome stuff! Lastly, your changes can automatically be pushed through the app store.

Meteor's hot code push to support live updates of installed apps without going through the app store! It's pretty cool stuff: whenever you deploy a new version, each installed copy of the app downloads the new code and stores it internally on the device. That means when you open them, apps always run the latest code without having to check with the server first.

I hope this has outlines some of the awesome and simple ways Meteor does things. I didn’t go over iron-router, the standard routing system. This is the best routing system I’ve used, it has amazing documentation, and is super thorough. I’ve rather enjoyed ui-router these days, but it still hasn’t reached the same point that I’ve felt iron-router has. The awesome template code is already incredibly reusable, but iron-router pushes that to a much greater degree.

There are other awesome plugin’s out there for Meteor. These include plugins for Famous, Angular, Ionic, and more. Theres also a built in analytical system that you can freely add to your project. In fact, meteor has the ability to make a full REST api, along with it’s DDP connections! 

Maybe the most important thing to show though, is how easy you and your team can share your projects. Lets upload this to github. Now I’m going to clone it, and try starting it up. Infact, any of you can if you install meteor. Let see how simple it is to install all the dependencies needed by the app. Do It. What’s an easier way to install packages other then just starting your app! Awesome!

I hope you enjoyed the walk through!



