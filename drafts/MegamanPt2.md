#Megaman Clone Part 2

##Creating a Weapon

Alright, so first off in this one lets create a weapon.  Now, I'm not going into minute details like creating an actual weapon for our hero, but lets for sure create a bullet, or else its going to be hard to shoot enemies!  So first, create a sphere called "Bullet" change all its scale values to 0.2 and attach a new material to it that will be yellow.  Make sure afterwards to make the Bullet a Prefab by dragging it into the Project Assets folder.  Lastly, remove the sphere collider, and add a circle collider 2D to it. 

Now that we have our bullet, lets create a new script called "Weapon", and attach it to our Player Object. We'll also create another new script called "Bullet" and attach it to our Bullet Prefab. In fact we didn't do it in the last article, but lets actually make the Player object a prefab.  Now first, open the PlayerMovement script and lets make a quick edit.

``` c#
public enum Direction {LEFT, RIGHT};
public class PlayerMovement : MonoBehaviour {

	public float speed = 5.0f;
	public bool isOnGround = false;
	public float jumpPower = 7.0f;
	
	private Transform _transform;
	private Rigidbody2D _rigidbody;
	private Direction playerDirection = Direction.RIGHT;

	public Direction PlayerDirection {
		get {
			return playerDirection;
		}
	}

	// Use this for initialization
	void Start () {
		_transform = GetComponent(typeof (Transform)) as Transform;
		_rigidbody = GetComponent(typeof (Rigidbody2D)) as Rigidbody2D;
	}
	
	// Update is called once per frame
	void Update () {
		MovePlayer();
		Jump();
	}

	void MovePlayer() {
		float translate = Input.GetAxis("Horizontal") * speed * Time.deltaTime;
		_transform.Translate(translate, 0, 0);

		if(translate > 0) {
			playerDirection = Direction.RIGHT;
		} else if (translate < 0) {
			playerDirection = Direction.LEFT;
		}
	}

		void Jump() {
		if(Input.GetKeyDown(KeyCode.Space) && isOnGround) {
			_rigidbody.AddForce(new Vector2(0, jumpPower), ForceMode2D.Impulse);
		}
	}

	void OnCollisionEnter2D() {
		isOnGround = true;
	}

	void OnCollisionExit2D() {
		isOnGround = false;
	}
}
```
Okay, so we have created a new enum state called Direction, and an associated property called playerDirection, that is going to keep track of what way our player is currently facing.  we also created a property, because nothing else but our PlayerMovement script should change our players direction.  Also this stops it from appearing in the inspector, which if it was there, could eventually start cluttering things if our designers are not really supposed to be touching that.  Lastly, in our MovePlayer method called every update, we add a simple if statement to keep track of what way our player moved last.  Note that it is not affected at 0, this is because we want to know the last direction moving, so if our player is at a standstill, we still want to shoot the previous way clicked.

Alright, lets open our Bullet.cs script and quickly make some edits to it!

``` c#
using UnityEngine;

public class Bullet : MonoBehaviour {

	public Direction bulletDirection = Direction.RIGHT;
	public float speed = 5.0f;

	private Transform _transform;
	
	void Start() {
		_transform = transform;
	}
	// Update is called once per frame
	void Update () {
		MoveBullet();
	}

	void MoveBullet () {
		int moveDirection = bulletDirection == Direction.LEFT ? -1 : 1;

		float translate = moveDirection * speed * Time.deltaTime;
		_transform.Translate(translate, 0, 0);
	}
}
```
So we now have our bullet that will move in a direction based on its own direction state.  Now all we need is a part to manage all of these interactions.  This will be our weapon script, so lets open that now!

``` c#
using UnityEngine;

public class Weapon : MonoBehaviour {

	public GameObject bullet;
	
	private PlayerMovement playerMovement;

	// Use this for initialization
	void Start () {
		playerMovement = GetComponent<PlayerMovement>();
	}
	
	// Update is called once per frame
	void Update () {
		if(Input.GetButtonDown("Fire1")) {
			var tBullet = Instantiate(bullet, gameObject.transform.position, bullet.transform.rotation) as GameObject;
			tBullet.GetComponent<Bullet>().bulletDirection = playerMovement.PlayerDirection;
		}
	}
}
```

So now we have what is essentially a manager of these two together.  This one will wait for a users input, create the bullet, and then set its direction depending on the players current direction.  We use the Fire1 button so that this can be changed later in the Input manager and work on other controllers easily.  Now, I do want to point out something with our connection between the plaerMovement class and the bulletDirection class.  First and foremost is we have a very tight coupling on these classes, which isn't great, but for the continuation of this post, I'm going to skip it.  But if you wish know more about this I suggest researching delegates and events, as well as decoupling in unity.  For now though, this will do.

##Creating an Enemy

Next let’s create an enemy for this bullet to interact with.  So lets create a cube, make him red with a material, and then give him the tag "Enemy" as well as the name "Enemy".  Take off the box collider, and attach a box collider 2D, as well as a rigidbody2D.  Lastly, make this enemy a prefab.  It should look like the following in the inspector.

Now to make sure our player and bullet don't bump each other anymore, let's quickly take that out of the physicsManager.  First, create three layers, "Bullet", "Player", and "Enemy".  Each opf these three game objects should be put on their respective layers.  Now in the PhysicsManager under Edit _> Project Settings -> Physics 2D, make sure that the player and bullet classes are NOT checked, so they no longer respond to each other.

Okay, now let's create an "Enemy" script and attach it to the Enemy game object.

``` c#
using UnityEngine;

public class Enemy : MonoBehaviour {

	public int health = 10;


	public void Damage(int dmg) {
		health -= dmg;
		if(health <= 0)
			Destroy(gameObject);
	}
}
```

In here, we have a very simple script that just contains a health int, and a method to adjust the health of our enemy.  Realistically our player class should have a very similar set up, but for the sake of scope, we can just do this for our enemy. Also, when our enemy class takes enough damage, we destroy that game object. Now were going to have to change our Bullet script as well to know what to do with this class.

``` C#
using UnityEngine;

public class Bullet : MonoBehaviour {

	public Direction bulletDirection = Direction.RIGHT;
	public float speed = 5.0f;
	public int damage = 5;

	private Transform _transform;
	
	void Start() {
		_transform = transform;
	}
	// Update is called once per frame
	void Update () {
		MoveBullet();
	}

	void MoveBullet () {
		int moveDirection = bulletDirection == Direction.LEFT ? -1 : 1;

		float translate = moveDirection * speed * Time.deltaTime;
		_transform.Translate(translate, 0, 0);
	}

	void OnCollisionEnter2D(Collision2D collision){
		if(collision.collider.tag == "Enemy") {
			collision.collider.gameObject.GetComponent<Enemy>().Damage(damage);
			Destroy(gameObject);
		}
		
	}
}
```

We've added a couple of things.  First, we now have a damage int at the top of our class that is used to measure the damage this bullet will do to our enemy.  We could for example, hold down the shoot button which increases the damage of our bullet.  For this, we'll just keep it at a base amount.  Next, we add the OnCollisionEnter2D method, which is going to handle what to do if our bullet interacts with an enemy.  If the collided with object is an enemy, our bullet will call the Damage method in the enemy class, and then destroy itself afterwards.  In honesty, we could actually put that destroy outside the if statement so that no matter what the bullet hit it would destroy itself.

So now if we try our game we have an enemy in the game world who after two hits will actually die.  Yes I know hes not really of any danger right now, but this is a great start for finding hittable targets!  If this project continued, the next thing added should be a simple enemy movement script, some weapons perhaps for our enemies, and then some simple level design!