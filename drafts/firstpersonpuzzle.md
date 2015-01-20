So you want to make a first person puzzle game, but your not sure where to start.  Well, this article can hopefully give you a start on how to start and create a game.  First and foremost though, we just want to say your awesome for wanting to start and remember, the most important step in learning anything is just getting going.  Many times, eaither reading an article or buying a simple tutorial book and following along is a great way to start, but then it becomes time for you to learn on your own. 

So first, let's create a project in Unity and name it something simple, like FirstPersonPuzzle.  Include the Character Controller package, as this package contains the FirstPersonController that we are going to use in this project!

<<Image 1>>

Next we are going to make a simple plane to walk on, and give it the following information inside the transform component.  Click the plane in the heirarchy view, and rename it to Ground.

<<Image 2>> <<Image 3>>

Alright now, it's a little dark in here, so lets quickly throw in a directional light, just to spice the place up a little bit, and put it in a nice place above us to keep it out of way while building the scene.

<<Image 4>> <<Image 5>>

Well, now that we can see the ground, we can see that it looks no good.  Well I'm going to tell you, I'm not an artist.  Unless you count the beauty strokes of the keyboard while programming an art, which I do, ut according to my grade 8 art teacher, it is not.  So what we're going to do is give it a grey look so we don't burn our eyes.  First, lets make some Project folders to give this project a little more structure. A Scripts, Prefabs, and Materials folder structure will really do for us. Then we will make a material, inside the Materials folder, and then drag and drop that material on to the plan in the heirarchy.

<<Image 7>> <<Image 8>>

Delete the Main Camera found in the heirarchy.

<<Image 10>>

Now drag the First Person Controller from the Standard Assets folder into the heirarchy.  Put the controller at the following transform position and you should be ready to go walking around the scene by hitting the play button!  Just remember to switch the tag of the Controller to the Player tag, as seen in the screenshot.  

<<Image 9>> <<Image 12>>

Next, we're going to create  a little elevator script.  I know I know, why an elevator in a puzzle game.  Well, I want to show a little bit of how moving objects look, as well as triggering an action, and I wanted the signle most ridiculous, jaw dropping, out of this world way to do so.  Unforutnatly it didn't work... so I put in an elevator.

Now lets create a cube quickly straight in the heirarchy and give it the following transform information.

<<Image 13>> <<Image 14>>

Now let's make another material, make it red, and place it onto the cube we just made.  Rename that cube to "Elevator".

<<Image 15>> <<Image 16>> <<Image 17>>

Now create another cube in the heirarchy and call it Platform, and give it the following transform attributes.

<<Image 18>> <<Image 19>>

Okay, lastly for objects, create another cube, and name it ElevatorTrigger. 
For ease, in the heirarchy, drag the ElevatorTrigger cube we created into the Elevator object, making Elevator now the parent of ElevatorTrigger as shown.
 
<<Image 21>>

Now go to the ispector, and right click the Mesh Renderer and remove the component.  This will essentially leave our object invisible.  Also check the box in the Box Colliser called Is Trigger so that this collider will watch for trigger enters.  Were going to be using this for our coding.  Make sure all transform attributes are as given.

<<Image 20>>

You scene should looks something like what is shown here.

<<Image 22>>

Now right click the Scripts folder and sleect create, C# Script, and name it Elevator.

<<Image 23>>

Now, explaining code is always very hard, so I'm going to try and do my best without being boring.  

<<Image 24>>

First, the lerpToPosition and StartLrp function are taken almost word for word from the unity documentation for Vector3.Lerp.  I did this so as to not have to explain Lerping heavily as its actually a fairly complex function, but all you have to know is that we are going to take a startPosition (where the elevator is currently), and endPosition (where the elevator is going to go) and then have it travel there over a certain amount of time (which will be caluclated using the speed we want to go).

The magic really happens in the OnTriggerEnter method though.  When a collider enters our trigger, this method is instantly called once.  In it we check to see if what collided with us is the player, if so, we allow the lerp to begin.

Lastly, in CheckLErpComplete, we do a little clean up that once the position of the elevator is at the endPositon, we stop the Lerp.  This clean up a little overhead for unity.

Drag this Script on to the ElevatorTrigger button, and give the attributes the following values, and your scene should be ready to go!

<<Image 25>>

Just remember, learning is all about failing, over and over, so don't become discouraged when things fail, or code you have written just doesn't work.  That is part of the building process, and it would be a lie to tell you that when I wrote this code for article that it worked the first time.  It didn't, but you iterate, change the isea