#Using jQuery Core

## $ vs $()
Until now, we've been dealing entirely with methods that are called on a jQuery object. For example:

    $("h1").remove();

Most jQuery methods are called on jQuery objects as shown above; these methods are said to be part of the `$.fn` namespace, or the "jQuery prototype", and are best thought of as jQuery object methods.
>$.fn = jQuery.fn = jQuery.prototype

However, there are several methods that do not act on a selection; these methods are said to be part of the jQuery namespace, and are best thought of as core jQuery methods.

This distinction can be incredibly confusing to new jQuery users. Here's what you need to remember:
1. Methods called on jQuery selections are in the `$.fn` namespace, and automatically receive and return the selection as `this`.
2. Methods in the `$` namespace are generally utility-type methods, and not work with selections; they are not automatically passed any arguments, and their return value will vary.
>Why methods in the `$` namespace do not work with selections?

There are a few cases where object methods and core methods have the same names, such as `$.each()` and `.each()`. In these cases, be extremely careful when reading the documentation that you are exploring the correct method.

##$(document).ready()
A page can't be manipulated safely until the document is "ready". jQuery detects this state of readiness for you. Code included inside `$(document).ready()` will only run once the page Document Object Model(DOM) is ready for JavaScript code to execute. Code included inside `$(window).load(function(){...})` will run once the entire page (images or iframes), not just the DOM, is ready.

    // A $(document).ready() block.
    $(document).ready(function(){
        console.log("DOM is ready!");
        });

Experienced developers sometimes use the shorthand `$()` for `$(document).ready()`.

    //Shorthand for $(document).ready()
    $(function(){
        console.log("DOM is ready!");
        });

You can also pass a named function to `$(document).ready()` instead of passing an anonymous function.

    //Passing a named funciton instead of an anonymous function.
    function readyFn(jQuery){
        //Code to run when the document is ready.
    }
    $(document).ready(readyFn);
    // or;
    $(window).load(readyFn);

The example below shows `$(document).ready()` and `$(window).load()` in action.

    <html>
    <head>
        <script src="https://code.jquery.com/jquery-1.9.1.min.js"></script>
        <script>
        $(document).ready(function(){
            console.log("document loaded");
            });
        $(window).load(function(){
            console.log("window loaded");
            });
        </script>
    </head>
    <body>
        <iframe src="http://techcrunch.com"></iframe>
    </body>
    </html>

##Avoiding Conflicts with Other Libraries
The jQuery library and virtually all of its plugins are contained within the `jQuery` namespace. As a general rule, global objects are stored inside the jQuery namespace as well, so you shouldn't get a clash between jQuery and any other lebrary (like prototype.js, MooTools, or YUI).

That said, there is one caveat: *by deafult, jQuery uses `$` as a shortcut for jQuery*. Thus, if you are using another JavaScript library that uses the `$` variable, you can run into conflicts with jQuery. In order to avoid these conflicts, you need to put jQuery in no-conflict mode immediately after it is loaded onto the page and before you attempt to use jQuery in your page.

###Putting jQuery Into No-Conflict Mode
When you put jQuery into no-conflict mode, you have the option of assigning a new variable name to replace the `$` alias.

    <!-- Putting jQuery into no-conflict mode. -->