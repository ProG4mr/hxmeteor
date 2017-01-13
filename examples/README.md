# Meteor Haxe Extern example

This folder contains a couple (default) Meteor examples and the tutorial you find [here](https://www.meteor.com/tutorials/blaze/creating-an-app)

- [helloworld](helloworld)
- [clock](clock)
- [leaderboard](leaderboard)
- [template](template)
- [router](router)


These examples follow [this](https://www.meteor.com/tutorials/blaze/creating-an-app) tutorial.

- [tutorial01](tutorial01) read instructions: [1. Creating an app](https://www.meteor.com/tutorials/blaze/creating-an-app)
- [tutorial02](tutorial02) read instructions: [2. Templates](https://www.meteor.com/tutorials/blaze/templates)
- [tutorial03](tutorial03) read instructions: [3. Collections](https://www.meteor.com/tutorials/blaze/collections)
- [tutorial04](tutorial04) read instructions: [4. Forms and events](https://www.meteor.com/tutorials/blaze/forms-and-events)
- [tutorial05](tutorial05) read instructions: [5. Update and remove](https://www.meteor.com/tutorials/blaze/update-and-remove)
- [tutorial07](tutorial07) read instructions: [7. Temporary UI state (hack)](https://www.meteor.com/tutorials/blaze/temporary-ui-state)
- [tutorial07b](tutorial07b) read instructions: [7. Temporary UI state (using `Session`)](https://www.meteor.com/tutorials/blaze/temporary-ui-state)
- [tutorial08](tutorial08) read instructions: [8. Adding user accounts](https://www.meteor.com/tutorials/blaze/adding-user-accounts)
- [tutorial09](tutorial09) read instructions: [9. Security with methods](https://www.meteor.com/tutorials/blaze/security-with-methods)
- [tutorial10](tutorial10) read instructions: [10. Publish and subscribe](https://www.meteor.com/tutorials/blaze/publish-and-subscribe)


# Cheat sheet

In Meteor, the `this` keyword has a different context and properties in callbacks like: `Meteor.publish(), Meteor.method(), template.helpers(), Router.route()`:

```javascript
Template.myTemplate.helpers = {
	firstId:function () {
		return this.firstNode().id;
	}
}

Router.route('/', function () {
  this.render('MyTemplate');
});
```

To mimic these namespaces in a typed manner, context objects like `TemplateCtx`, `PublishCtx`, `MethodCtx` and `RouterCtx` are provided. The example above becomes:

```haxe
Templace.get('myTemplate').helpers = {
	firstId:function () {
		return TemplateCtx.firstNode().id;
	}
}

Route.route('/', function () {
	RouterCtx.render('MyTemplate');
});
```

Create and use global variables are difficult in Haxe

```javascript
Template.body.events({
  'submit .new-task'(event) {
    ...
  },
});
```

```haxe
Template.get('body').events({
  'submit .new-task': function (event) {
    ...
  }
});

```

Generate variables on the flight is a big no-no in Haxe

```javascript
Template.body.onCreated(function bodyOnCreated() {
  this.state = new ReactiveDict();
});
```

```haxe
Template.get('body').onCreated(function bodyOnCreated() {
  /*
  http://api.haxe.org/Reflect.html
  "The Reflect API is a way to manipulate values dynamically through an abstract interface in an untyped manner. Use with care."
  */
  Reflect.setField(TemplateCtx, 'state', new ReactiveDict() );// generates: this["state"] = new ReactiveDict();

  /*
  just ignore everything, and use untyped JavaScript
  */
  // untyped __js__('this.state = {0}', new ReactiveDict()); // generates: this.state = new ReactiveDict();
});
```


# Difference

There is a difference between the "original" Meteor way and the Haxe way.

Meteor prefers ES6 files but you can use plain vanilla JS as well (Meteor is build with vanilla js!).
Because there is no ES6 export in Haxe (yet), we use vanilla JS (because Meteor is backwards compatible).

In the Meteor tutorial they use different `.js` files to create structure.
Eventually the framework will collect all `.js` files and combine them in 1 (js) file.

Because we use packages to structure our app in Haxe, we use 1 file for the client and 1 for the server.

The end result is the same!.



# Basic folder structure

```
+ projectname
	+ src
		+ client
			+ templates
				- Home.hx
			- Client.hx
		+ server
			- Server.hx
		+ shared
			+ model
				- Model.hx
			- Shared.hx
			- AppRouter.hx
	+ www
		+ node_modules
		+ .meteor
		+ client
			+ lib
				+ css
				+ js
			+ style
				- main.css
			+ templates
				- home.html
				- nav.html
			- client.js (Haxe generated file)
		+ server
			- server.js (Haxe generated file)
		+ public
			+ img
			+ fonts


```



# Build and Run:

All examples follow simular build pattern.

Open the example folder you want to try in your terminal

`cd path/to/folder`

Build with Haxe and start meteor

```
haxe build.hxml
cd www
meteor npm install
meteor
```