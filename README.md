
JavaScript Global Best Practices
================================


One of the major problems when working with JavaScript is Global Pollution. Global scope create many problems like Accidental override issue, possible side effects with other functionality, fear to change team member's code and Global are never garbage-collected but solution also along the way so simply you can do with the help of the following best practices.

One Global Concept:

One global concept is one global variable holds all properties and functions in global scope. It normally attached to Window object but not necessary to refer via window object.

One global concept is famous in almost all JavaScript frameworks. Like DOJO use dojo global variable. , JQuery use two global variable $ and jQuery. YUI library use YUI global variable. ExtJS use Ext global variable.

if (typeof MyNamespace === "undefined")
    MyNamespace = {};

MyNamespace.MyModule = function () {
    // Module created do something
}

Alternatively you can do like
var MyNamespace = MyNamespace || {}; //Short 

MyNamespace.MyModule = function () {
    // Module created do something
}

But what if want to create namespace look like,
MyNamespace.MyModule.NestedModule

if (typeof MyNamespace === "undefined")
    MyNamespace = {};
if (typeof MyNamespace.MyModule === "undefined")
    MyNamespace.MyModule = {};
if (typeof MyNamespace.MyModule.NestedModule === "undefined")
    MyNamespace.MyModule.NestedModule = {};
         
Alternatively you can do little better code like below snippets
var MyNamespace = MyNamespace || {};

MyNamespace.MyModule || {};
MyNamespace.MyModule.NestedModule || {};
However still it seems to be ugly if we have too many nested modules in multiple places. So better use small utility function for create namespace. The below code server better for create nested namespace.
var App = App || {};

App.namespace = function (ns) {
    var parts = ns.split("."),
        objects = this,
        len,
        partNS;

    for (var i = 0, len = parts.length; i < len ; i++) {
        var partNS = parts[i];
        objects[partNS] = objects[partNS] || {};
        objects = objects[partNS];
    }
}
That's it. Hereafter don't worry about nested module creation. But one thing before going to use any module ensures that namespace method called. To create nested module simply call namespace function like below.
App.namespace("MyProgramming.Heros");

App.MyProgramming.Heros.authors = ["Douglas Crockford", "Nicholas Zakas”];

App.MyProgramming.skills = ["Advanced JavaScript", "JQuery", "AngularJS", "BackboneJS", "HTML5", "CSS3"];

console.log(App.MyProgramming.Heros.authors);

console.log(App.MyProgramming.skills);  

Module Concept:

Another way of restricts the global scope by using modules. Module provide generic piece of functionality which never allow to creating global or namespace instead all the code takes place within a single function that responsible for executing or publishing an interface.
Asynchronous module definition (AMD) is a JavaScript API for defining modules such that the module and its dependencies can be asynchronously loaded. It is useful in improving the performance of websites by bypassing synchronous loading of modules along with the rest of the site content.
In addition to loading multiple JavaScript files at runtime, AMD can be used during development to keep JavaScript files encapsulated in many different files.

Module concepts are implemented in many JavaScript libraries like YUI, Dojo, AngularJS, RequireJS, Etc.
Here take require.js library. It allows loading module in asynchronous fashion manner along with dependencies. RequireJS API have two methods define and require in global scope. To make use of those functions we can achieve modular concepts.
define - To define a module. Method optionally has module name and their dependencies.
require - To load the defined module with their dependencies object then it execute callback function.
<script type="text/javascript" data-main="js/main.js" src="js/require.js"></script>

Inside module/person.js define simple module. Like below

define([], function () {
    var person = function (name) {
        this.name = name;
    }

    person.prototype.getName = function () {
        return this.name;
    }

    person.prototype.sayHello = function () {
        console.log("Hello " + this.name);
    }

    return person;
});
The main.js file gets executed once require.js fully loaded. Load the module when exactly necessary with require () API. Like below
require(["module/person"], function (Person) {
    var person = new Person("RequireJS");
    person.sayHello();
});
With the help of module approach we can maintain large JavaScript application without mess, able to create well encapsulate data and behavior, separate the responsibility via Module and easy to test module and fear to change other’s code.
Note: Next version of JavaScript (ES6 Harmony) may have built-in module support.
Explain still why namespace approach import for organize code with AngularJS example (Directive/Filter/Controllers). 

No (Zero) Global Concept:

In JavaScript you can do without creating even a single global variable. But In this model has some limitation which should not have any dependencies. If it has any dependencies then need to maintain order of code executing. Zero global approach cannot use in some case like want extend behavior and modified at runtime is not possible with this approach. Anyhow server better way when a script has no dependencies with any other scripts.

 (function(global){

    "use strict";

    var localVar1,
        localVar2,
        localVar3;

    function ModuleA() {
        // doSomething()
    }

    // Finally publishing module
    global.ModuleA = ModuleA;

})(this);

Here "use strict" statement absolutely need due to accidently we might have change to declare global variable. Let’s take following scenario.
var localVar1; //Note here semicolon
    localVar2; //So localVar2 is global variable
Sometimes few typo mistakes happened like below statement,
var localVar1, //Note here Comma
    localVar2; //Now localVar2 is local variable
If use strict mode it throw error because of variable declared without var statement. To avoid this kind annoying mistake at first place use JavaScript code quality tool like JSLint and JSHint

Inspired: Nicholas C. Zakas

