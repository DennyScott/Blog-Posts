Well its been a bit of time since I made a post, and I really want to make this a regular thing.  I honestly have so many half completed blogs saved in draft that I simply haven't quite finished, but from now on I'm going to try and make at least some regular posts.  So today I'm going to make a post on Delegates and Events.  First, I would be both a liar and a jerk if I said I learned about this topic on my own, and read a lot of great articles.  The best one out there for this topic I found was at [Delegates, Events, and Singletons](http://www.indiedb.com/groups/unity-devs/tutorials/delegates-events-and-singletons-with-unity3d-c) posted by vicestudios.  The other great area is of course the actual [Unity Learn](http://unity3d.com/learn) section of the Unity site.  Watch these videos, there really great and can teach you some really cool coding techniques.  For me coming from mostly javascript to C#, an extra resource to just show you some common and well used patterns was really helpful!

I was going do define a structure for how I set up my projects and show how refactoring code for more modularity works in a a practical snece, but to be honest that lead me down a rabbit hole that I realized should be seperate blog posts on their own.  I'm going to try and keep my scope narrow on this post to not have it just drone on.  And thus another blog as entered the development hell that is the drafts section.  So first lets show how you may normally interact between two scripts in Unity.

``` c#
public class Bucket() {
	Player obj;
    GameObject playerObject;
    
    void Start() {
    	playerObject = GameObject.FindWithTag("Player");
    	obj = someGameObject.GetComponent<Player>();
    }
    
    public void PickUpBucket(Transform objectToFollow) {
    	if(!obj.HoldingBucket) {
    		obj.HoldingBucket = true;
            gameObject.transform.parent = objectToFollow;
        }
    }
}

public class Player() {
	bool holdingObject;
    
    public bool HoldingObject {
    	get {
        	return holdingObject;
        }
        set {
        	holdingObject = value;
        }
    }
}
```

Now, in a real program I would likely recommend things like states using enums to handle a state change like this, but this is just a simple example to show interacting between two scripts.  The one thing you really gotta give unity for scripting is it is extremly simple to get it working.  This is quite short, and does a lot of cool stuff for us.  Now the Bucket script would likely be attached to some sort of bucket object, and the player class attached to a player object.  Now the goal of this is to create some re-usable code, and both of these are pretty specific to our game, so lets break down the Bucket class into a new class that may be a little more usable.  I should warn you, as you go through this your code will indeed become bigger, so if your all about the shortest code, please don't show up at my house with pitch forks and torches.

``` c#
public class PickUpObject() {
	Player obj;
    GameObject playerObject;
    
    void Start() {
    	playerObject = GameObject.FindWithTag("Player");
    	obj = someGameObject.GetComponent<Player>();
    }
    
    public void PickUpObject(Transform objectToFollow) {
    	if(!obj.HoldingBucket) {
	    	obj.HoldingBucket = true;
        	gameObject.transform.parent = objectToFollow;
        }
    }
}



```

Okay well thats a start, but this PickUpObject script is still very coupled with the Player class, and contextually doesn't make sense.  If it is simply just to pick up objects, why does it adjust the Player class? (Adjusts a bool value to true)  In that manner, the player class is also coupled to this class since it has a holding bucket variable that its also waiting for.  Now one class will generally always be coupled (or not through the use of a pub-sub operator, which myself or Denny will write about one day), and it seems that our player class is acting as more of a manager here, so it will be the one that we will load with the coupling logic.  When contructing these scripts, I tend to want scripts such as PickUpObject to be "Plug and Play" on any object I drop it on, meaning I don't have to code whole aspects again.  So this is going to be a building process, so let's refactor this code and add some simple void delegates and events.

``` C#
public class PickUpObject() {
	public delegate void TriggerEvent(GameObject g);
    public event TriggerEvent OnPickUp;
    
    public void PickUpObject(Transform objectToFollow) {
    	gameObject.transform.parent = objectToFollow;
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
    	g.transform.parent = gameObject;
        HoldingObject = true;
    }
    
    
    public bool HoldingObject {
    	get {
        	return holdingObject;
        }
        set {
        	holdingObject = value;
        }
    }
}
```

Well one class shrunk, and the other one increased greatly!  Now this might seem like a bad thing, but really now the movement script only handles what it was created to do, and the player object now handles what it is supposed to.  Something like the Player class can't really be moved from game to game, but our PickUpObject class works independently now.  How you ask, well through delegates and events.  We create our delegate function at the top of our PickUpObject script, and designate what must be in the signature of the object.  I pass the game object for simplictiy sake because generally thats what you'll need to access on the handling side since many times, the handler has no idea what object just triggered its event, like say if we had numourous buckets in this scene.  

From here we call the event when the item is pickedUp with the gameObject attached as an argument.  We also surround this call in an if statement to check if the event is null first.  We do this because if nothing has subscribed to that event, its type is null.  Once our Player class subscribes to the event though, it no longer is null and can be called like a method. 

Next in our Player class, we go through all buckets that may be in the scene, get the PickUpObject component, and += a handler method in this class onto that event.  This is a delegate behavior that allows us to add numerous methods to this object.  So other classes could also have their own behaviours when this event is triggered, and when the event is called, it will call event attached handler method.

Oh, but there is a small problem though.  We no longer check to make sure that the player is not already holding a bucket!  Well we can fix that through events again, but this time a little different.  Where going to use a little boolean trickery in our event!

``` c#
public class PickUpObject() {
	public delegate void TriggerEvent(GameObject g);
    public event TriggerEvent OnPickUp;
    
    public delegate bool IsAcceptable(Gameobject g);
    public event IsAcceptable CanPickUp;
    
    public void PickUpObject(Transform objectToFollow) {
    	if(CanPickUp == null || CanPickUp()) {
    		gameObject.transform.parent = objectToFollow;
	    	if(OnPickUp != null)
    	    	OnPickUp(gameObject);
        }
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
    	g.transform.parent = gameObject;
        HoldingObject = true;
    }
    
    bool HandleCanPickUp(GameObject g) {
    	return HoldingObject == false;
    }
    
    
    public bool HoldingObject {
    	get {
        	return holdingObject;
        }
        set {
        	holdingObject = value;
        }
    }
}
```

Okay so now we have a delegate that returns a boolean value, and an event built from it.   Based on the flow we have, an item can only be picked up if CanPick has not been subscribed to, or if the subscription method returns true.  Now this probably seems like a very strange mix, but its to protect independence.  Basically this script will now always work, unless another class adds some rules to it.  So in essance, a bucket can always be picked up, but if we want the functionality that it can only be picked up when another bucket is not already in the players possesion, we add that functionality through an event call, maintaining script independece.

This event when called will call all attached scripts and return the value of the last one.  Then in our Player class we subscribe a handle function that return true if the bucket can be picked up. This works perfectly for a one attached handler script, but anymore and it will fail.  So where going to do a neat little trick with it using the Invocation list.  Now you may think of other cool ways to use this, but I find this type of algroithm only really works with boolean value methods.  So lets code it quickly now.

``` c#
public class PickUpObject() {
	public delegate void TriggerEvent(GameObject g);
    public event TriggerEvent OnPickUp;
    
    public delegate bool IsAcceptable(Gameobject g);
    public event IsAcceptable CanPickUp;
    
    public void PickUpObject(Transform objectToFollow) {
    	if(IsAbleToPickUp()) {
    		gameObject.transform.parent = objectToFollow;
	    	if(OnPickUp != null)
    	    	OnPickUp(gameObject);
        }
    }
    
    void IsAbleToPickUp() {
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
    	g.transform.parent = gameObject;
        HoldingObject = true;
    }
    
    bool HandleCanPickUp(GameObject g) {
    	return HoldingObject == false;
    }
    
    
    public bool HoldingObject {get; set;}
}
```

So we moved our CanPickUp into a seperate method for cleaness, and now use the GetInvocationList to cycle through each event handler subscribed.  If any of them are false, we return false, if not we return true.  So in essance we have made a gigantic OR statment with a ton of methods, but cleaned it up to be super simple.  Now we have our functionality complete, our PickUpObject script is independent but has the hooks needed to control some of the logic if needed.