Let's talk about Object Oriented Javascript. As with everything in Javascript, there is many ways to architect Object Orienteation. The following is my best practice. It's the collective sum of opinons between different developers at my workplace, and the general approach to keep things simple, and allow testing.

##What is Object Oriented Javascript
Javascript doesn't allow Object Orientation in the traditional sense. Rather Javascript accomplishes Object Orientation using what is known as Prototype-based programming. For a better description of prototype-based programming, we will use Mozillas definition: 

Prototype-based programming is a style of object-oriented programming in which classes are not present, and behavior reuse (known as inheritance in class-based languages) is accomplished through a process of decorating existing objects which serve as prototypes. This model is also known as class-less, prototype-oriented, or instance-based programming.

In general, we will be building a variable in Javascript, then adding Prototype functionalities to this variable. This will allow other Objects to "inherit" these functionalities. As with traditional Object-Oriented programming Languages, new objects of this variables can be made using the **new** keyword. To get a better grasp, we'll have some demos in a section further ahead. Until then, just understand that Javascript is attempting to mimic tradtional Object Orientation, but using a different prototype-based system. This syntax will also be greatly cleaned up in ECMA 6, which we will discuss further on.

##Why would I want to Use Object Orientation
Faor those developers who may not understand why we want to use Object Orientation, Object Orientation allows us to make our code more modular. These days, web apps code and complexity has increased significantly. What was once passed off to a server, or not possible, has now been placed to the front-end of web applications. That means there is more code then ever running on a users browser while viewing our app. Furthermore, things like Node-Webkit, Cordova, or just games mean that whole applications take place on the Client End, with little to know connection to a back-end environment. 

As our code complexity and density increases, we have to be smarter about the ways we architect our apps. Gone are the days we simply store this data in a couple files, place it in the window namespace, and call what we need. We have to be careful about overriding other namespaces. We have larger teams, who need ot be able to work on our code, familiar architecture and standards helps integrate new team members. Now I'm not saying Object Orientation will answer all these problems, but it certainly is a step in the right direction. In fact, lets look at why my workplace moved to this style of Object Oriented javascript.

We had been developing a small "game", allowing simple actions like moving items, and actioning them. Before this decision, our app had been a standard angular app. Once we decided to add some gameplay, we quickly prototyped our game with CreateJS and moved forward using the Canvas. I had used OO like programming with Angular, but had rarely dwelved deep into construction full OO systems with Javascript. After our first sprint, our code was a mess. We had gigantic main.js file full of Code Smells, duplication, and dependencies. It was so bad, that only one developer only knew what was truly happening in that file. When new team members were added to the project, they would avoid working on that area of the app.

It was a bit to late, but we decided we needed to better architect our app, and we moved to a stronger OO system. Of course, myself and the other developers come from a heavy Java and C# background, so the move was the simplest one. We searched up what some of the best approaches to Object Orientation in javascript were, and I gave the book "The Principles of Object-Oriented Javascript" by Nicholas Zakas a read. I won't go into detail about each progresive step we took, but after working on a number of apps, we came across a style of OO Javascript that we all agreed on. 

Don't worry if you don't understand Object Orientation yet, we'll explain it step-by-step in the next section!
a
##How Do I Use Object Orientation
a
Lets start at the basics. In Object Orientation, our first step is to split logical pieces of the Code into Objects. If you don't understand, lets pretend you were programming me as a person into your code. Well we could create an Object called Denny that has all my properties, as such.

 var Denny = {
    class: "Mammal",
    name: "Denny",
    eyeColor: "blue",
    height: "5ft10",
    weight: "Too Much",
    faviouriteBand: "The Beatles",
    programsIn: ["Javascript", "C#", "Java"],
    sayHello: function() {
        console.log("hello");
    },
    payTaxes: function() {
        friendDoesTax();
        console.log("You are now broke");
    },
    breath: function() {
        takeBreath();
    }
 };

 Great, we have some properties and functions of an Object. But what if we wanted to make another person? Well they would require us to create a whole new object manually. We'd have to make sure we place the same properties in, and that they output correct. Alright, so lets do that.

var Jim = {
    class: "Mammal",
    name: "Jim",
    eyeColor: "green",
    height: "6ft1",
    weight: "Probably better shape then Denny",
    faviouriteBand: "The Band",
    programsIn: [],
    sayHello: function() {
        console.log("hello");
    },
    payTaxes: function() {
        payToOverseasBank();
        console.log("Still has more then Denny");
    },
    breath: function() {
        takeBreath();
    }
 };

Great, but now we run into a number of issues. Lets say that we suddenly have a standard way we need to pay taxes. Now we must go into both objects and change their function for payTaxes.

var newTaxFunction = function() {
    payToTheMan();
}

Jim.payTaxes = newTaxFunction;
Denny.payTaxes = newTaxFunction;

Furthermore, there is a lot of shared code between these types. We want a way to encapsulate this data, but also also us to share code. So lets break this down logically. We can see that we allow decleration for a class of Animal. Now, we should always attempt not to over-architect our application. At this point, lets assume that the base object we need to make for the type Mammal. I realize there could be another superclass before mammal, but lets try and keep the demo simple. So we'll create a Mammal class.


var Mammal = function(name) {
    this.name = "Default";
    this.constructor(name);
}

Mammal.prototype = {
    constructor: function(name){
        this.name = name;
        this.eat('meat');
    },
    eat: function(food) {
        console.log("You ate lots of " + food);
    }

}

Great, now for an explanation of what we wrote. The top mammal function is our object itself. It will be what someone calls when using your code. Inside, by habit, we place the default instance values and the call to the constructor. Our team has come to an agreement keeping the methods in the Prototype, while placing instance variables into the Object declaration. In our example, we can identifiy two types of variable exposure. Public and Privllaged. The functions in the prototype are public functions. That is, they can be called, but they do not have access to the Mammals private functions or variables. We do not have any currently, so this isn't a problem. We will look at private variables shortly.

We also can see an example of a privallaged variable, which is name. It is attached to the object itself (using this.name), so we can call the variable from outside on the Mammal function. Someone with a Java or C# background might believe this to be like a protected variable, but it is not. Privlaged functions are similar to public functions in these languages, in that they can call their own objects private methods, and be called externally. Public function/variables in javascript are a little different, again, they can only call privallaged methods.

So lets see how inheritance works with this new object. Now that we have Mammal, lets build a Person class, and extend it from Mammal.



The style we use is one that we as a team decided upon. Our early iterations of Object Oriented Javascript worked just fine, but were often confusing to new team members. They didn't have a patterend look, were messy, using public functions, private functions, and privlaged functions. Some team members were upset when they could not test private functions. Of course, this is a matter of debate itself, but we felt moving to this new system would allow team members to choose if they wanted to test private functions.

##So there are no Private Functions

##Why do you place everything into a public JSON for methods

##Testing Object Oriented Javascript

##ECMA 6
