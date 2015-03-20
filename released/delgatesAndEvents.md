Before I start, I would be both a liar and a jerk if I said I learned about this topic on my own, and I had to read a lot of great articles on this topic. The best info out there for this topic was [Delegates, Events, and Singletons](http://www.indiedb.com/groups/unity-devs/tutorials/delegates-events-and-singletons-with-unity3d-c) posted by vicestudios.  The other great area is of course the actual [Unity Learn](http://unity3d.com/learn) section of the Unity site.  Watch these videos, they're really great and can teach you some really cool coding techniques.  For me, primarily coming from a JavaScript background, then transitioning to C#, an extra resource to just show you some common and well used patterns was really helpful!

#Getting Started
As a warning to those reading this article, I will be using some less then ideal calls in my code.  As stated later in the article, I am only using them for simplicity of reading to the viewer to know what objects I'm using.  I understand they are not the greatest method for actually referencing objects in the hierarchy, but as this article does not focus on efficiency of hierarchy calls, I left them in for simplicity.

##Basic Script Interaction
So first lets show how you may normally interact between two scripts in Unity.

####Bucket.cs
``` c#
using UnityEngine;
using System.Collections;

public class Bucket() : MonoBehaviour {
    Player playerObj;
    Other otherObj
    GameObject playerObject;
    GameObject otherObject
    
    void Start() {
        playerObject = GameObject.FindWithTag("Player");
        playerObj = someGameObject.GetComponent<Player>();

        otherObject = GameObject.FindWithTag("Other");
        otherObj = someGameObject.GetComponent<Other>();
    }
    
    public void PickUp(GameObject objectToFollow) {
        if(!playerObj.HoldingBucket) {
            playerObj.HoldingBucket = true;
            gameObject.transform.parent = objectToFollow.transform;
            otherObj.DoSomethingWithBucket();
        }
    }
}
```

####Player.cs
``` C#
using UnityEngine;
using System.Collections;

public class Player() : MonoBehaviour {
    bool holdingObject;
    
    public bool HoldingObject {get; set;}
}
```

####Other.cs
``` C#
using UnityEngine;
using System.Collections;
public class Other : MonoBehaviour {

    public void DoSomethingWithBucket() {
         Debug.Log("Doing something else with the bucket!");
    }

}


```
*Note: We are only demonstrating a public api to pick up the object, an external manager or button press would call this api."*

##Separating Scripts
Now, in a real program I would likely recommend using enums to handle a state change like this, but this is just a simple example to show interacting between two scripts.  The Bucket script would be attached to a bucket object, and the player class attached to a player object. Now the goal of this exercise is to create re-usable code, and both of these classes are pretty specific to our game, so lets break down the Bucket class into a new class that may be a little more usable.

I should warn you, as you go through this your code will indeed become larger, so if you're all about the shortest code, please don't show up at my house with pitch forks and torches.

####PickUpObject.cs
``` c#
using UnityEngine;
using System.Collections;

public class PickUpObject() : MonoBehaviour {
    Player playerObj;
    Other otherObj
    GameObject playerObject;
    GameObject otherObject
    
    void Start() {
        playerObject = GameObject.FindWithTag("Player");
        playerObj = someGameObject.GetComponent<Player>();

        otherObject = GameObject.FindWithTag("Other");
        otherObj = someGameObject.GetComponent<Other>();
    }
    
    public void PickUp(GameObject objectToFollow) {
        if(!obj.HoldingBucket) {
            obj.HoldingBucket = true;
            gameObject.transform.parent = objectToFollow.transform;
            otherObj.DoSomethingWithBucket();
        }
    }
}



```
___

##Creating Delegates and Events
Okay well thats a start, but our PickUpObject script is still very coupled with the Player class, and contextually it doesn't make sense.  If this script is simply to just to pick up objects, why does it adjust an attribute in the Player class?  Now one class will generally always be coupled in some manner, unless you use something more complicated like a Pub-Sub adapter to manage messages, and it seems that our player class is acting as more of a manager here, so it will be the one that we will load with the coupling logic.  

When constructing these scripts, I tend to want scripts such as PickUpObject to be "Plug and Play" on any object I drop it on, meaning I don't have to code whole aspects again.  This is going to be a building process, so let's re-factor this code and add some simple void delegates and events.

####Void Delegates and Events
The first type of delegate I'm going to be showing the the void type delegate.  This means I'm going to make a method signature that returns a type of void, and then I can create other methods that are of that delegate type, ensuring that I return void.  I also must pass the same parameters as the delegate, so in the case coming up, this means whenever the event is called it must also pass a GameObject as an argument.

####PickUpObject.cs
``` C#
using UnityEngine;
using System.Collections;

public class PickUpObject() : MonoBehaviour {
    public delegate void TriggerEvent(GameObject g);
    public event TriggerEvent OnPickUp;
    
    public void PickUp(GameObject objectToFollow) {
        gameObject.transform.parent = objectToFollow.transform;
        if(OnPickUp != null)
            OnPickUp(gameObject);
    }
}
```

####Player.cs
``` C#
using UnityEngine;
using System.Collections;

public class Player() : MonoBehaviour {
    bool holdingObject;
    
    void Start() {
        GameObject[] buckets = GameObject.FindGameObjectsWithTag("Bucket");
        foreach(GameObject bucket in buckets) {
            bucket.GetComponent<PickUpObject>().OnPickUp += HandleOnPickUp;
        }
    }
    
    void HandleOnPickUp(GameObject g) {
        HoldingObject = true;
    }
    
    
    public bool HoldingObject {get; set;}
}
```

####Other.cs
``` C#
using UnityEngine;
using System.Collections;

public class Other : MonoBehaviour {

    void Start () {
        RegisterBuckets();
    }

    void RegisterBuckets() {
        GameObject[] buckets = GameObject.FindGameObjectsWithTag("Bucket");
        foreach(GameObject bucket in buckets) {
            PickUpObject objScript = bucket.GetComponent<PickUpObject>();
            objScript.OnPickUp += HandleOnPickUp;
        }
    }

    void HandleOnPickUp(GameObject g) {
        Debug.Log("Doing something else with the bucket!");
    }
}

```

Well one class shrunk, and the other one increased greatly!  Now this might seem like a bad thing, but really now the movement script only handles what it was created to do, and the player class now handles what it is supposed to.  Something like the Player class can't really be moved from game to game, but our PickUpObject script works independently now.  The delegate function at the top of our PickUpObject script is to designate what must be in the signature of the method that is using it.  I pass the game object for simplicity sake because generally thats what you'll need to access on the handling side.  This is because the handler many times has no idea what object just triggered its event, for example we had numerous buckets in this scene, passing the game object is a great way for it to know what has actually called it.  

The other line we add at the top is the event OnPickUp that has a type of TriggerEvent, meaning that it will need to contain a game object as an argument, and void as a return type.  Events can have many outside methods add (and remove) themselves from this event through the use of the += operator.  More on this will come later.

Now inside the PickUpObject method the OnPickUp event is actually called with the attached game object sent as a parameter.  We also surround this call in an if statement to check if the event is null since if nothing has subscribed to that event, its type will be null.  Once our Player class subscribes to the event though, it no longer is null and can be called like a method. 

Next in our Player class, we go through all buckets that may be in the scene, get the PickUpObject component, and += a handler method in this class onto that event.  This is a delegate behavior that allows us to add numerous methods to this object.  So other classes could also have their own handler for when this event is triggered, and when the event is called, it will call event attached handler method.

####Boolean Delegates and Events
Oh, but there is a small problem though.  We no longer check to make sure that the player is not already holding a bucket!  Well we can fix that through events again, but this time a little different.  Were going to use a little boolean trickery in our event!

####PickUpObject.cs
``` c#
using UnityEngine;
using System.Collections;

public class PickUpObject() : MonoBehaviour {
    public delegate void TriggerEvent(GameObject g);
    public event TriggerEvent OnPickUp;
    
    public delegate bool IsAcceptable(Gameobject g);
    public event IsAcceptable CanPickUp;
    
    public void PickUp(GameObject objectToFollow) {
        if(CanPickUp == null || CanPickUp()) {
            gameObject.transform.parent = objectToFollow.transform;
            if(OnPickUp != null)
                OnPickUp(gameObject);
        }
    }
}
```

####Player.cs
``` C#
using UnityEngine;
using System.Collections;

public class Player() : MonoBehaviour {
    bool holdingObject;
    
    void Start() { GameObject[] buckets =
    GameObject.FindGameObjectsWithTag("Bucket"); foreach(GameObject bucket in
    buckets) { PickUpObject objScript = bucket.GetComponent<PickUpObject>();
    objScript.OnPickUp += HandleOnPickUp; objScript.CanPickUp +=
    HandleCanPickUp;
            
        }
    }
    
    void HandleOnPickUp(GameObject g) {
        HoldingObject = true;
    }
    
    bool HandleCanPickUp(GameObject g) {
    	return !HoldingObject;
    }
    
    
    public bool HoldingObject {get; set;}
}
```

####Other.cs
``` C#
using UnityEngine;
using System.Collections;

public class Other : MonoBehaviour {
    int waterLevel = 0;
    
    void Start () {
        RegisterBuckets();
    }

    void RegisterBuckets() {
        GameObject[] buckets = GameObject.FindGameObjectsWithTag("Bucket");
        foreach(GameObject bucket in buckets) {
            PickUpObject objScript = bucket.GetComponent<PickUpObject>();
            objScript.OnPickUp += HandleOnPickUp;
            objScript.CanPickUp += HandleCanPickUp;
            
        }
    }

    bool HandleCanPickUp(GameObject g) {
        return waterLevel < 3;
    }

    void HandleOnPickUp(GameObject g) {
        waterLevel++;
    }

}
```

Now we have a delegate that returns a boolean value, and an event built from it.   Based on the flow we have, an item can only be picked up if CanPick has not been subscribed to by any handlers, or if the subscription method returns true.  Now this probably seems like a very strange mix as true and null usually don't mix, but its to protect script independence.  Basically this script will now always work when dropped on an object, unless another class adds some rules to it.  So in essence, a bucket can always be picked up, but if we want the functionality that it can only be picked up when another bucket is not already in the players possession, we add that functionality through an event call, maintaining script independence.

####Using the Invocation list
This event, when called, will call all attached scripts and return the value of the last one.  Then in our Player class we subscribe a handle function that return true if the bucket can be picked up. This works perfectly for a one attached handler script, but anymore and it will fail.  So we're going to do a neat little trick with it using the Invocation list.  Now you may think of other cool ways to use this, but I find this type of algorithm only really works with boolean value methods.  So lets code it quickly now.

####PickUpObject.cs
``` c#
using UnityEngine;
using System.Collections;

public class PickUpObject() : MonoBehaviour {
    public delegate void TriggerEvent(GameObject g);
    public event TriggerEvent OnPickUp;
    
    public delegate bool IsAcceptable(Gameobject g);
    public event IsAcceptable CanPickUp;
    
     public void PickUp(GameObject objectToFollow) {
        if(IsAbleToPickUp()) {
            gameObject.transform.parent = objectToFollow.transform;
            if(OnPickUp != null)
                OnPickUp(gameObject);
        }
    }
    
    bool IsAbleToPickUp() {
        if(CanPickUp == null)
            return true;
        
        foreach(IsAcceptable check in CanPickUp.GetInvocationList()) {
            if (check(gameObject) == false)
                return false;
        }
        
        return true;
    }
}
```

####Player.cs
``` C#
using UnityEngine;
using System.Collections;

public class Player() : MonoBehaviour {
    bool holdingObject;
    
    void Start() {
        GameObject[] buckets = GameObject.FindGameObjectsWithTag("Bucket");
        foreach(GameObject bucket in buckets) {
            PickUpObject objScript = bucket.GetComponent<PickUpObject>();
            objScript.OnPickUp += HandleOnPickUp;
            objScript.CanPickUp += HandleCanPickUp;
            
        }
    }
    
    void HandleOnPickUp(GameObject g) {
        HoldingObject = true;
    }
    
    bool HandleCanPickUp(GameObject g) {
        return HoldingObject == false;
    }
    
    
    public bool HoldingObject {get; set;}
}
```

####Other.cs
``` C#
using UnityEngine;
using System.Collections;

public class Other : MonoBehaviour {
    int waterLevel = 0;
    
    void Start () {
        RegisterBuckets();
    }

    void RegisterBuckets() {
        GameObject[] buckets = GameObject.FindGameObjectsWithTag("Bucket");
        foreach(GameObject bucket in buckets) {
            PickUpObject objScript = bucket.GetComponent<PickUpObject>();
            objScript.OnPickUp += HandleOnPickUp;
            objScript.CanPickUp += HandleCanPickUp;
            
        }
    }

    bool HandleCanPickUp(GameObject g) {
        return waterLevel < 3;
    }

    void HandleOnPickUp(GameObject g) {
        waterLevel++;
    }

}
```

So we moved our CanPickUp into a separate method and now use the GetInvocationList to cycle through each event handler subscribed.  If any of them are false, we return false, if not we return true.  So in essence we have made a gigantic extend-able OR statement with methods, but cleaned it up to be super simple.  Now we have our functionality complete, our PickUpObject script is independent but has the hooks needed to control some of the logic if needed by an outside source thus maintaining code independence.
___

##Spicing it up with some Syntactical Sugar
After receiving some feedback for this article, the common feeling was that instead of using custom defined delegates, we should be using Action delegate and the Func delegate.  These are 2 delegates that are already included in the system that can do the same job as the custom delegates we have been using.  These simply remove the line needed for creating the custom delegate, and nothing more, so don't feel nervous if you don't quite understand what they are.  These are the exact same thing we were already declaring, but now remove the line needed for creating the delegate, as C# already has these delegates created in the System.  Now be warned that while this lowers your lines of code needed by one, it may also reduce the readability of some sections of code, especially if there are a large amount of parameters in your delegate.  It can also make type checking a little more complicated if needed, but that being said we don't really have either of this problem in our code, so lets throw it in!

####Action
When we declare the Action delegate, it is the same thing as our TriggerEvent delegate but allows us to not have to create a custom delegate.  Actions can have numerous parameters just like when we create a delegate, but automatically have a void return value.  This will slightly clean up our code, with the exact same results, so lets go ahead and change that up!

####PickUpObject.cs
``` C#
using UnityEngine;
using System.Collections;
using System;

public class PickUpObject : MonoBehaviour {
    public event Action<GameObject> OnPickUp;
    
    public delegate bool IsAcceptable(GameObject g);
    public event IsAcceptable CanPickUp;
    
    public void PickUp(GameObject objectToFollow) {
        if(IsAbleToPickUp()) {
            gameObject.transform.parent = objectToFollow.transform;
            if(OnPickUp != null)
                OnPickUp(gameObject);
        }
    }
    
    bool IsAbleToPickUp() {
        if(CanPickUp == null)
            return true;
        
        foreach(IsAcceptable check in CanPickUp.GetInvocationList()) {
            if (check(gameObject) == false)
                return false;
        }
        
        return true;
    }
}
```

This code switch will change nothing with our other classes so we can just leave them as is, but allows us to remove the custom delegate we created at the top of this script.  Note that I also added the using System.Actions at the top.  If you didn't want to add this, you could just say System.Action in place of just Action, as I know it is a fairly common word to use.

####Func
Alright, the second piece to the puzzle, the Func we will declare will act like our IsAcceptable delegate, but this delegate is more expendable then what we use it for.  Basically, if you want to create a delegate that returns a value you can use Func to declare that delegate rather then make a custom delegate, you can just use the Func delegate.  Just like I stated before, remember if your looking for clarity on code a custom delegate may be more reasonable for you, as well if you use type checking on your delegates.  In our case, the only thing it kind of messes up a bit is the readability of the Invocation list, but only a bit, so we should be fine.

####PickUpObject.cs
``` C#
using UnityEngine;
using System.Collections;
using System;

public class PickUpObject : MonoBehaviour {
    public event Action<GameObject> OnPickUp;
    
    public event Func<GameObject, bool> CanPickUp;
    
    public void PickUp(GameObject objectToFollow) {
        if(IsAbleToPickUp()) {
            gameObject.transform.parent = objectToFollow.transform;
            if(OnPickUp != null)
                OnPickUp(gameObject);
        }
    }
    
    bool IsAbleToPickUp() {
        if(CanPickUp == null)
            return true;
        
        foreach(Func<GameObject, bool> check in CanPickUp.GetInvocationList()) {
            if (check(gameObject) == false)
                return false;
        }
        
        return true;
    }
}
```

So really not much has changed here, we removed the custom delegate, and added in our Func call.  The way these work is that the first generic parameter is argument, and you can add a bunch of these, followed by a final argument which will be the return value of the delegate.  Remember, it will always be the final argument in this that will be the return value of the delegate.  In our case, the bool is the return value of the CanPickUp method, so we list it at the end.  Now our InvocationList line has changed a bit, with the IsAcceptable delegate being removed, we need to cast the method into the correct type, which in this case is the Func delegate with a game object argument first, and a return value of bool.  Now any more parameters and this could get kind of messy looking, and tough to sometimes figure out whats calling what.  Its all a matter of preference, so just know going in which you want to choose.

##Unregistering your Events
So one of the largest drawbacks that can happen with events is that if they are not properly managed, they can cause memory leaks.  I think we all know that memory leaks are very bad, so lets fix that up now.  Really all you need to fix this up is to unregister these events when were done with them.  In fact, whenever you register an event, you should also include a section to unregister it immediately to make sure you don't fall into that trap.  

##Unregister on Subscribers
I'm actually going to move our event subscriptions to an OnEnable method in case our objects can be enabled and disabled (or simply destroyed), as well as add in an unregister (i.e. -=) first just in case our Buckets have already been registered once.  If they haven't, this will fail silently and keep going, which is perfect for us.  Also on the OnDisable, we simply unregister the handlers we used.  This works as simply as it looks, and will remove itself from the invocation list for when the event is called.

####Player.cs
``` C#
using UnityEngine;
using System.Collections;

public class Player : MonoBehaviour {
    bool holdingObject;
    
    void OnEnable() {
        RegisterBuckets();
    }

    void RegisterBuckets() {
        GameObject[] buckets = GameObject.FindGameObjectsWithTag("Bucket");
        foreach(GameObject bucket in buckets) {
            PickUpObject objScript = bucket.GetComponent<PickUpObject>();
            objScript.OnPickUp -= HandleOnPickUp;
            objScript.CanPickUp -= HandleCanPickUp;
            objScript.OnPickUp += HandleOnPickUp;
            objScript.CanPickUp += HandleCanPickUp;
            
        }
    }

    void Update() {
        if (Input.GetKeyDown("space")) {
            GameObject[] buckets = GameObject.FindGameObjectsWithTag("Bucket");
            GameObject bucket = buckets[0];
            PickUpObject objScript = bucket.GetComponent<PickUpObject>();
            objScript.PickUp(gameObject);
        }
    }
    
    void HandleOnPickUp(GameObject g) {
        HoldingObject = true;
        Debug.Log("Player Picked Up!");
    }
    
    bool HandleCanPickUp(GameObject g) {
        return HoldingObject == false;
    }

    void OnDisable() {
        GameObject[] buckets = GameObject.FindGameObjectsWithTag("Bucket");
        foreach(GameObject bucket in buckets) {
            PickUpObject objScript = bucket.GetComponent<PickUpObject>();
            objScript.OnPickUp -= HandleOnPickUp;
            objScript.CanPickUp -= HandleCanPickUp;
            
        }
    }
    
    
    public bool HoldingObject {get; set;}
}
```

####Other.cs
``` C#
public class Other : MonoBehaviour {
    int waterLevel = 0;
    
    void OnEnable () {
        RegisterBuckets();
    }

    void RegisterBuckets() {
        GameObject[] buckets = GameObject.FindGameObjectsWithTag("Bucket");
        foreach(GameObject bucket in buckets) {
            PickUpObject objScript = bucket.GetComponent<PickUpObject>();
            objScript.OnPickUp -= HandleOnPickUp;
            objScript.CanPickUp -= HandleCanPickUp;
            objScript.OnPickUp += HandleOnPickUp;
            objScript.CanPickUp += HandleCanPickUp;
            
        }
    }

    bool HandleCanPickUp(GameObject g) {
        return waterLevel < 3;
    }

    void HandleOnPickUp(GameObject g) {
        waterLevel++;
    }

    void OnDisable() {
        GameObject[] buckets = GameObject.FindGameObjectsWithTag("Bucket");
        foreach(GameObject bucket in buckets) {
            PickUpObject objScript = bucket.GetComponent<PickUpObject>();
            objScript.OnPickUp -= HandleOnPickUp;
            objScript.CanPickUp -= HandleCanPickUp;
        }
    }

}
```

####Unregister the Subscribed
This one is a little different, and technically not needed, but safety first so lets remove the event listener on the subscribed class on disable.  This means that when the Bucket is disabled, we want to reset the event and loose our invocation list, which is done by simply giving it the value of null.

####PickUpObject.cs
``` C#
using UnityEngine;
using System.Collections;
using System;

public class PickUpObject : MonoBehaviour {
    public event Action<GameObject> OnPickUp;
    
    public event Func<GameObject, bool> CanPickUp;
    
    public void PickUp(GameObject objectToFollow) {
        if(IsAbleToPickUp()) {
            gameObject.transform.parent = objectToFollow.transform;
            if(OnPickUp != null)
                OnPickUp(gameObject);
        }
    }
    
    bool IsAbleToPickUp() {
        if(CanPickUp == null)
            return true;
        
        foreach(Func<GameObject, bool> check in CanPickUp.GetInvocationList()) {
            if (check(gameObject) == false)
                return false;
        }
        
        return true;
    }

    void OnDisable() {
        OnPickUp = null;
    }
}
```
___

##Drawbacks
There is one large draw back with this style of coding though, which is debugging.  Now, don't run for the hills in terror, its not really tough, it just requires a little more work to manage your code.  The reason this is tough for debugging is that you have to now work backwards through your code to debug.  Before, you could just go to bucket, see that its calling Player, and go straight to the Player class.  Well now, your going to go the the PickUpObject class, and then wonder what class is subscribing to this method that is not allowing it to be picked up. 

Of course, you could simply select the event, right click, and find its usages, but this may not be optimal for you which I totally understand.  I love using delegates and events like this as it allows me to jut drop scripts on and start enforcing rules, but if it feels a little to tough to manage, don't throw them out, just keep them in your pocket as a tool to use if the problem needed them arises.  

Hey thanks for reading guys!  Any questions you can reach me at travisscott301@gmail.com or @gizmmo_cti, and more importantly send me feedback so I can improve this article!
