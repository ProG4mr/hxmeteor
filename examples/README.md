# Meteor Haxe Extern example

This folder contains a couple (default) Meteor examples and the tutorial you find [here](https://www.meteor.com/tutorials/blaze/creating-an-app)

- [helloworld](helloworld) `meteor create simple-todo` example
- [clock](clock)
- [leaderboard](leaderboard)
- [template](template) IronRouter, Bootstrap, models, the whole shebang
- [router](router) IronRouter example with Bootstrap for styling and Haxe autocompile via NPM [onchange](https://www.npmjs.com/package/onchange)


The following examples are from [this](https://www.meteor.com/tutorials/blaze/creating-an-app) Meteor tutorial.

- [tutorial01](tutorial01)  read instructions: [1. Creating an app](https://www.meteor.com/tutorials/blaze/creating-an-app)
- [tutorial02](tutorial02)  read instructions: [2. Templates](https://www.meteor.com/tutorials/blaze/templates)
- [tutorial03](tutorial03)  read instructions: [3. Collections](https://www.meteor.com/tutorials/blaze/collections)
- [tutorial04](tutorial04)  read instructions: [4. Forms and events](https://www.meteor.com/tutorials/blaze/forms-and-events)
- [tutorial05](tutorial05)  read instructions: [5. Update and remove](https://www.meteor.com/tutorials/blaze/update-and-remove)
- [tutorial07](tutorial07)  read instructions: [7. Temporary UI state (hack)](https://www.meteor.com/tutorials/blaze/temporary-ui-state)
- [tutorial07b](tutorial07b) read instructions: [7. Temporary UI state (using `Session`)](https://www.meteor.com/tutorials/blaze/temporary-ui-state)
- [tutorial08](tutorial08)  read instructions: [8. Adding user accounts](https://www.meteor.com/tutorials/blaze/adding-user-accounts)
- [tutorial09](tutorial09)  read instructions: [9. Security with methods](https://www.meteor.com/tutorials/blaze/security-with-methods)
- [tutorial10](tutorial10)  read instructions: [10. Publish and subscribe](https://www.meteor.com/tutorials/blaze/publish-and-subscribe)


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
  Ignore everything and use untyped JavaScript
  */
  // untyped __js__('this.state = {0}', new ReactiveDict()); // generates: this.state = new ReactiveDict();
});
```

I ran into something unexpected with the `server` part:

You can use `console.log('foo');` for debugging also on the `server` side.
But when using the Haxe way on the server it will generate code that will not work.

```javascript
import js.Browser.console;
console.log("foo");

// or

js.Browser.console.log("foo");

// Both will generate:
// window.console.log("foo"); // but on the server there is no `window`
```

Why is that? ... well the server is `node.js` so the browser version on console.log will not work.
Probably the best way would be to add the nodejs externs and use that import.

```
haxelib install hxnodejs
// add to build.hxml
-lib hxnodejs
// import
import js.node.console.Console;
```


But you can always use the default Haxe log methode

```haxe
trace('foo');
// Will generate:
//console.log('foo');
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
	- build.hxml (build file for Haxe)
	- package.json (automatic build for Haxe)
	+ src
		+ client
			+ templates
				- Home.hx (Code specific for 'home' template)
			- Client.hx (Starting point for client code, will be generate to client.js)
		+ server
			- Server.hx  (Starting point for Server code, will be generate to server.js)
		+ shared (folder for code shared between client and server)
			+ model
				- Model.hx
			- Shared.hx
			- AppRouter.hx
	+ www or bin (export folder for Haxe / root folder for Meteor)
		+ node_modules
		+ .meteor (meteor specific folder)
		+ client
			+ lib
				+ css
				+ js
			+ style
				- main.css
			+ templates (template folder, not Haxe specific)
				- home.html
				- nav.html
			- client.js (Haxe generated file)
		+ server
			- server.js (Haxe generated file)
		+ public (folder accessable for the client html)
			+ img
			+ fonts
		- .gitignore (ignore the node_modules)
		- package.json (npm install)
```

The `www` folder is in most tutorials the project folder.
For Haxe it's the export/deploy folder.



# Build and Run:

All examples follow similar build pattern.

Open the example folder you want to try in your terminal

`cd path/to/folder`

Build with Haxe and start Meteor

```
haxe build.hxml
cd www
meteor npm install
meteor
```