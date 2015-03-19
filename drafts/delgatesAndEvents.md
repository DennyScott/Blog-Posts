<<<<<<< HEAD
##Getting Started
Before I start, I would be both a liar and a jerk if I said I learned about this topic on my own, and I had to read a lot of great articles on this topic. The best info out there for this topic was [Delegates, Events, and Singletons](http://www.indiedb.com/groups/unity-devs/tutorials/delegates-events-and-singletons-with-unity3d-c) posted by vicestudios.  The other great area is of course the actual [Unity Learn](http://unity3d.com/learn) section of the Unity site.  Watch these videos, they're really great and can teach you some really cool coding techniques.  For me, primarily coming from a JavaScript background, then transitioning to C#, an extra resource to just show you some common and well used patterns was really helpful!

####Basic Script Interaction
So first lets show how you may normally interact between two scripts in Unity.

``` c#
public class Bucket() {
    Player obj;
    GameObject playerObject;
    
    void Start() {
        playerObject = GameObject.FindWithTag("Player");
        obj = someGameObject.GetComponent<Player>();
    }
    
    public void PickUpBucket(GameObject objectToFollow) {
        if(!obj.HoldingBucket) {
            obj.HoldingBucket = true;
            gameObject.transform.parent = objectToFollow.transform;
        }
    }
}

public class Player() {
    bool holdingObject;
    
    public bool HoldingObject {get; set;}
}
```
*Note: We are only demonstrating a public api to pick up the object, an external manager or button press would call this api."*

####Separating Scripts
Now, in a real program I would likely recommend using enums to handle a state change like this, but this is just a simple example to show interacting between two scripts.  The Bucket script would be attached to a bucket object, and the player class attached to a player object. Now the goal of this exercise is to create re-usable code, and both of these classes are pretty specific to our game, so lets break down the Bucket class into a new class that may be a little more usable.

I should warn you, as you go through this your code will indeed become larger, so if you're all about the shortest code, please don't show up at my house with pitch forks and torches.

``` c#
public class PickUpObject() {
    Player obj;
    GameObject playerObject;
    
    void Start() {
        playerObject = GameObject.FindWithTag("Player");
        obj = someGameObject.GetComponent<Player>();
    }
    
    public void PickUpObject(GameObject objectToFollow) {
        if(!obj.HoldingBucket) {
            obj.HoldingBucket = true;
            gameObject.transform.parent = objectToFollow.transform;
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

``` C#
public class PickUpObject() {
    public delegate void TriggerEvent(GameObject g);
    public event TriggerEvent OnPickUp;
    
    public void PickUpObject(GameObject objectToFollow) {
        gameObject.transform.parent = objectToFollow.transform;
        if(OnPickUp != null)
            OnPickUp(gameObject);
    }
}

public class Player() {
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

Well one class shrunk, and the other one increased greatly!  Now this might seem like a bad thing, but really now the movement script only handles what it was created to do, and the player class now handles what it is supposed to.  Something like the Player class can't really be moved from game to game, but our PickUpObject script works independently now.  The delegate function at the top of our PickUpObject script is to designate what must be in the signature of the method that is using it.  I pass the game object for simplicity sake because generally thats what you'll need to access on the handling side.  This is because the handler many times has no idea what object just triggered its event, for example we had numerous buckets in this scene, passing the game object is a great way for it to know what has actually called it.  

The other line we add at the top is the event OnPickUp that has a type of TriggerEvent, meaning that it will need to contain a game object as an argument, and void as a return type.  Events can have many outside methods add (and remove) themselves from this event through the use of the += operator.  More on this will come later.

Now inside the PickUpObject method the OnPickUp event is actually called with the attached game object sent as a parameter.  We also surround this call in an if statement to check if the event is null since if nothing has subscribed to that event, its type will be null.  Once our Player class subscribes to the event though, it no longer is null and can be called like a method. 

Next in our Player class, we go through all buckets that may be in the scene, get the PickUpObject component, and += a handler method in this class onto that event.  This is a delegate behavior that allows us to add numerous methods to this object.  So other classes could also have their own handler for when this event is triggered, and when the event is called, it will call event attached handler method.

####Boolean Delegates and Events
Oh, but there is a small problem though.  We no longer check to make sure that the player is not already holding a bucket!  Well we can fix that through events again, but this time a little different.  Were going to use a little boolean trickery in our event!

``` c#
public class PickUpObject() {
    public delegate void TriggerEvent(GameObject g);
    public event TriggerEvent OnPickUp;
    
    public delegate bool IsAcceptable(Gameobject g);
    public event IsAcceptable CanPickUp;
    
    public void PickUpObject(Transform objectToFollow) {
        if(CanPickUp == null || CanPickUp()) {
            gameObject.transform.parent = objectToFollow.transform;
            if(OnPickUp != null)
                OnPickUp(gameObject);
        }
    }
}

public class Player() {
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

Now we have a delegate that returns a boolean value, and an event built from it.   Based on the flow we have, an item can only be picked up if CanPick has not been subscribed to by any handlers, or if the subscription method returns true.  Now this probably seems like a very strange mix as true and null usually don't mix, but its to protect script independence.  Basically this script will now always work when dropped on an object, unless another class adds some rules to it.  So in essence, a bucket can always be picked up, but if we want the functionality that it can only be picked up when another bucket is not already in the players possession, we add that functionality through an event call, maintaining script independence.

####Using the Invocation list
This event, when called, will call all attached scripts and return the value of the last one.  Then in our Player class we subscribe a handle function that return true if the bucket can be picked up. This works perfectly for a one attached handler script, but anymore and it will fail.  So we're going to do a neat little trick with it using the Invocation list.  Now you may think of other cool ways to use this, but I find this type of algorithm only really works with boolean value methods.  So lets code it quickly now.

``` c#
public class PickUpObject() {
    public delegate void TriggerEvent(GameObject g);
    public event TriggerEvent OnPickUp;
    
    public delegate bool IsAcceptable(Gameobject g);
    public event IsAcceptable CanPickUp;
    
    public void PickUpObject(Transform objectToFollow) {
        if(IsAbleToPickUp()) {
            gameObject.transform.parent = objectToFollow.tranform;
            if(OnPickUp != null)
                OnPickUp(gameObject);
        }
    }
    
    bool IsAbleToPickUp() {
        if(CanPickUp == null)
            return true;
        
        foreach(IsAcceptable check in CanPickUp.GetInvocationList()) {
            if (check() == false)
                return false;
        }
        
        return true;
    }
}

public class Player() {
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

So we moved our CanPickUp into a separate method and now use the GetInvocationList to cycle through each event handler subscribed.  If any of them are false, we return false, if not we return true.  So in essence we have made a gigantic extend-able OR statement with methods, but cleaned it up to be super simple.  Now we have our functionality complete, our PickUpObject script is independent but has the hooks needed to control some of the logic if needed by an outside source thus maintaining code independence.
___

##Drawbacks
There is one large draw back with this style of coding though, which is debugging.  Now, don't run for the hills in terror, its not really tough, it just requires a little more work to manage your code.  The reason this is tough for debugging is that you have to now work backwards through your code to debug.  Before, you could just go to bucket, see that its calling Player, and go straight to the Player class.  Well now, your going to go the the PickUpObject class, and then wonder what class is subscribing to this method that is not allowing it to be picked up. 

Of course, you could simply select the event, right click, and find its usages, but this may not be optimal for you which I totally understand.  I love using delegates and events like this as it allows me to jut drop scripts on and start enforcing rules, but if it feels a little to tough to manage, don't throw them out, just keep them in your pocket as a tool to use if the problem needed them arises.  

Hey thanks for reading guys!  Any questions you can reach me at travisscott301@gmail.com or @gizmmo_cti, and more importantly send me feedback so I can improve this article!
