So you want to make a first person puzzle game, but you're not sure where to start. Well, this post can hopefully give you a heads-up on how to start and create a game. When creating this post we wanted to make a complex system of colored keys and locked doors, but we decided on a simple trigger system moving an elevator. That may seem really simplistic, but it encompasses really everything you need to make the harder key color key scene described, while keeping this lesson short.

Let's begin. First create a project in Unity and name it something simple, like FirstPersonPuzzle. Include the Character Controller package, as this package contains the FirstPersonController that we are going to use in this project! If this is your first time using Unity, there are some great scripts packaged with Unity. Two examples are SmoothFollow.js and SmoothLookAt.js. The first of which has the camera follow a specific game object you designate and follow it without giving a choppy look that can come from just having the camera follow the object. SmoothLookAt will have the camera look at a designated game object, but it does not have a quick cut feeling when the script is run.  There are also C# versions you can find of almost all of these scripts online through the Unity community. We don't have enough time to get into them, but we encourage you to try them for yourself!

[]

Next we are going to make a simple plane to walk on, and give it the following information inside the transform component. Click the plane in the hierarchy view, and rename it to Ground.

Game development

puzzles

Hmm, it's a little dark in here, so lets quickly throw in a directional light, just to spice the place up a little bit, and put it in a nice place above us to keep it out of way while building the scene.

 puzzles

Game development, puzzles

First we will make a material, and then drag and drop that material on to the plane in the hierarchy.

Game development

Game development

Delete the Main Camera found in the hierarchy. You hierarchy should now look like this.

Game development, puzzles

Now drag the First Person Controller from the Standard Assets folder into the hierarchy. Put the controller at the following transform position and you should be ready to go walking around the scene by hitting the play button! Just remember to switch the tag of the Controller to the Player tag, as seen in the screenshot.

Game development, puzzles

Game development, puzzles

Next, we're going to create a little elevator script. We know, why an elevator in a puzzle game. Well, I want to show a little bit of how moving objects look, as well as triggering an action, and I wanted the single most ridiculous, jaw dropping, out of this world way to do so. Unfortunately it didn't work... so we put in an elevator.

Create a cube quickly straight in the hierarchy and give it the following transform information.

Game development, puzzles

Game development, puzzles

Now let's make another material, make it red, and place it onto the cube we just made. Rename that cube to "Elevator". Your scene should look like this:

Game development, puzzles

Create another cube in the hierarchy and call it Platform, and give it the following transform attributes.

Game development, puzzles

Okay, lastly for objects, create another cube, and name it ElevatorTrigger.

For ease, in the hierarchy, drag the ElevatorTrigger cube we created into the Elevator object, making Elevator now the parent of ElevatorTrigger as shown.

Game development, puzzles

Now go to the inspector, and right click the Mesh Renderer and remove the component. This will essentially leave our object invisible. Also check the box in the Box Collider called Is Trigger so that this collider will watch for trigger enters. We're going to be using this for our coding. Make sure all transform attributes are as given.

Game development, puzzles

Now click create and select create --> C# Script, and name it Elevator. This script is going to be our first and only script. Explaining code is always very hard, so we’re going to try and do our best without being boring.





First, the lerpToPosition and StartLerp function are taken almost word for word from the Unity documentation for Vector3.Lerp. We did this so as to not have to explain Lerping heavily as its actually a fairly complex function, but all you have to know is that we are going to take a startPosition (where the elevator is currently), and endPosition (where the elevator is going to go) and then have it travel there over a certain amount of time (which will be calculated using the speed we want to go).

The magic really happens in the OnTriggerEnter method though. When a collider enters our trigger, this method is instantly called once. In it we check to see if what collided with us is the player. If so, we allow the lerp to begin.

Lastly, in CheckLerpComplete, we do a little clean up that once the position of the elevator is at the endPositon, we stop the Lerp. This will clean up a little overhead for Unity.

Drag this Script on to the ElevatorTrigger button, and give the attributes the following values, and your scene should be ready to go!

Game development, puzzles

Just remember, learning is all about failing, over and over, so don't become discouraged when things fail or code you have written just doesn't seem to work. That is part of the building process, and it would be a lie to tell you that when we wrote this code for article and that it worked the first time. It didn't, but you iterate, change the idea to something better, and come out with a working product at the end.