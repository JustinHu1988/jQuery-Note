jQuery Note


#How jQuery Works
##Basics
To ensure that their code runs after the browser finishes loading the document, many JavaScript programmers wrap their code in a onload function:

    window.onload = function(){
        alert("welcome");
    };

Unfortunately, the code doesn't run until all images are finished downloading, including banner ads. To run code as soon as the document is ready to be manipulated, jQuery has a statement known as the ready event:

    $(document).ready(function(){
        alert("welcome");
        });

###Prevent the default behavior
For `click` and most other events, you can prevent the default behavior by calling `event.preventDefault()` in the event handler:

    $(document).ready(function(){
        $("a").click(function(event){
            alert("As you can see, the link no longer took you to jquery.com");
            event.preventDefault();
            });
        });

###Adding and Removing an HTML Class
>You must place the remaining jQuery examples inside the `ready` event so that your code executes when the document is ready to be worked on.

First, add some style information into the `<head>` of the document, like this:
    
    <style>
    a.test{
        font-weight:bold;
    }
    </style>

Next, add the `.addClass()` call to the script:

    $("a").addClass("test");

All `<a>` elements are now bold.
To remove an existing class, use `.removeClass()`:

    $("a").removeClass("test");

###Special Effects
jQuery also provides some handy effects, for example:

    $("a").click(function(event){
        event.preventDefault();
        $(this).hide("slow");
        });

##Callbacks and Functions 
A *callback* is a function that is padded as an argument to another function and is executed after its parent function has completed. Callbacks are special because they patiently wait to execute until their parent finishes. Meanwhile, the browser can be executing other functions or doing all sorts of other work.

###Callback without Arguments
If a callback has no arguments, you can pass it in like this:

    $.get("myhtmlpage.html", myCallBack);

When `$.get()` finishes getting the page `myhtmlpage.html`, it executes the `myCallBack()` function.
>The second parameter here is simply the function name(but not as a string, and without parentheses).

###Callback with Arguments
Executing callbacks with arguments can be tricky.

**Wrong**
This code example will not work:

    $.get("myhtmlpage.html", myCallBack(param1, param2));

The reason this fails is that the code executes `myCallBack(param1,param2)` immediately and then passes `myCallBack()`'s *return value* as the second parameter to `$.get()`. We actually want to pass the function `myCallBack()`, not `myCallBack(param1,param2)`'s return value (which might or might not be a function). So, how to pass in `myCallBack()` and include its arguments?

**Right**
To defer executing `myCallBack()` with its parameters, you can use an anonymous function as a wrapper. Note the use of `function(){`. The anonymous function does exactly one thing: calls `myCallBack()`, with the values of `param1` and `param2`.

    $.get("myhtmlpage.html", function(){
        myCallBack(param1, param2);
        });
When `$.get()` finishes getting the page `myhtmlpage.html`, it executes the anonymous function, which executes `myCallBack(param1, param2)`.


#Additional jQuery Support
