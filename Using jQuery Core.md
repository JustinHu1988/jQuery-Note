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
    <script src="prototype.js"></script>
    <script src="jquery.js"></script>
    <script>
    var $j = jQuery.noConflict();
    // $j is now an alias to the jQuery function; creating the new alias is optional.
    $j(document).ready(function(){
        $j("div").hide();
        });
    
    // The $ variable now has the prototype meaning, which is a shortcut for
    // document.getElementById(). mainDiv below is a DOM element, not a jQuery object.
    window.onload = function(){
        var mainDiv = $("main");
    }
    </script>

In the code above, the `$` will revert back to its meaning in original library. You'll still be able to use the full function name `jQuery` aswell as the new alias `$j` in the rest of your application. The new alias can be named anything you'd like:`jq`, `$J`, `awesomeQuery`, etc.

Finally, if you don't want to define another alternative to the full `jQuery` function name (you really like to use `$` and don't care about using the other library's `$` method), then there's still another approach you might try: simply add the `$` as an argument passed to your `jQuery(document).ready()` function. This is most frequently used in the case where you still want the benefits of really concise jQuery code, but don't want to cause conflicts with other libraries.

    <!-- Another way to put jQuery into no-conflict mode. -->
    <script src="prototype.js"></script>
    <script src="jquery.js"></script>
    <script>
    jQuery.noConflict();
    jQuery(document).ready(function($){
        //You can use the locally-scoped $ in here as an alias to jQuery.
        $("div").hide();
        });

    //The $ variable in the global scope has the prototype.js meaning.
    window.onload = function(){
        var mainDiv = $("main");
    }
    </script>

This is probably the ideal solution for most of your code, considering that there'll be less code that you'll have to change in order to achieve conplete compatibility.

###Including jQuery Before Other Libraries
The code snippets above rely on jQuery being loaded after prorotype.js is loaded. If you include jQuery before other libraries, you may use `jQuery` when you do some work with jQuery, but the `$` will have the meaning defined in the other library. There is no need to relinquish the `$` alias by calling `jQuery.noConflict()`.

    <!-- loading jQuery before other libraries. -->
    <script src="jquery.js"></script>
    <script src="prototype.js"></script>
    <script>
    //Use full jQuery function name to reference jQuery.
    jQuery(document).ready(function(){
        jQuery("div").hide();
        });
    //Use the $ variable as defined in prototype.js
    window.onload = function(){
        var mainDiv = $("main");
    };
    </script>

### Summary of Ways to Reference the jQuery Function
Here's a recap of ways you can reference the jQuery function when the presence of another library creates a conflict over the use of the `$` variable.

#### Create a New Alias
The `jQuery.noConflict()` method returns a reference to the jQuery function, so you can capture it in whatever variable you'd like:

    <script src="prototype.js"></script>
    <script src="jquery.js"></script>
    <script>
    //Give $ back to prototype.js; create new alias to jQuery.
    var $jq = jQuery.noConflict();
    </script>

####Use an Immediately Invoked Function Expression
You can continue to use the standard `$` by wrapping your code in an immediately invoked function expression; this is also a standard pattern for jQuery plugin authoring, where the author cannot know whether another library will have taken over the `$`. See the Plugins section for more information about writing plugins.

    <!-- Using the $ inside an immediately-invoked function expression. -->
    <script src="prototype.js"></script>
    <script src="jquery.js"></script>
    <script>
    jQuery.noConflict();
    (function($){
        //Your jQuery code here, using the $
        })(jQuery);
    </script>

Note that if you use this technique, you will not be able to use prototype.js methods inside the immediately invoked function. `$` will be a reference to jQuery, not prototype.js.

####Use the Argument That's Passed to the `jQuery(document).ready()` Function

    <script src="jquery.js"></script>
    <script src="prototype.js"></script>
    <script>
    jQuery(document).ready(function($){
        //Your jQuery code here, using $ to refer to jQuery.
        });
    </script>

Or using the more concise syntax for the DOM ready function:
    
    <script src="jquery.js"></script>
    <script src="prototype.js"></script>
    <script>
    jQuery(function($){
        //Your jQuery code here, using the $
        });
    </script>


##Attributes

An element's attributes can contain useful information for your application, so it's important to be able to get and set them.

###The `.attr()` method
The `.attr()` method acts as both a getter and a setter. As a setter, `.attr()` can accept either a key and a value, or an object containing one or more key/value, or an object containing one or more key/value pairs.

`.attr()` as a setter:

    $("a").attr("href","allMyHrefsAreTheSameNow.html");
    $("a").attr({
        title:"all titles are the same too!",
        href:"somethingNew.html"
        });

`.attr()` as a getter:

    $("a").attr("href"); //Returns the href for the first a element in the document


##Selecting Elements
The most basic concept of jQuery is to "select some elements and do something with them". jQuery supports most CSS3 selectors, as well as some non-standard selectors. For a complete selector reference, visit the "Selectors documentation on api.jquery.com".

###Selecting Elements by ID

    $("#myId");  //Note IDs must be unique per page.

###Selecting Elements by Class Name

    $(".myClass");

###Selecting Elements by Attribute

    $("input[name='first_name']");

###Selecting Elements by Compound CSS Selector

    $("#contents ul.people li");

###Selecting Elements with a Comma-separated List of Selectors

    $("div.myClass, ul.people");

###Pseudo-Selectors

    $("a.external:first");
    $("tr:odd");

    //Selector all input-like elements in a form (more on this below).
    $("#myForm :input");
    $("div:visible");
    //All except the first three divs.
    $("div:gt(2)");
    //All currently animated divs.
    $("div:animated");

**Note:** When using the `:visible` and `:hidden` pseudo-selectors, jQuery tests the actual visibility of the element, not its CSS `visibility` or `display` properties. jQuery looks to see if the element's physical height and width on the page are both greater than zero.

However, this test doesn't work with `<tr>` elements. In the case of `<tr>` jQuery does check the CSS `display` property, and considers an element hidden if its `display` property is set to `none`.

Elements that have not been added to the DOM will always be considered hidden, even if the CSS that would affect them would render them visible. See the **Manipulating Elements** section to learn how to create and add elements to the DOM.


###Choosing Selectors
Choosing good selectors is one way to improve JavaScript's performance. Too much specificity can be a bad thing. A selector such as `#myTable thead tr th.special` is overkill if a selector such as `#myTable th.special` will get the job done.

####Does My Selection Contain Any Elements?
Once you're made a selection, you'll often want to know whether you have anything to work with. A common mistake is to use:

    //Doesn't work!
    if ($("div.foo")){
        ...
    }

This won't work. When a selection is made using `$()`, an object is always returned, and objects always evaluate to `true`. Even if the selection doesn't contain any elements, the code inside the `if` statement will still run.

The best way to determine if there are any elements is to test the selection't `.length` property, which tells you how many elements were selected. If the answer is 0, the `.length` property will evaluate to `false` when used as a boolean value：

    //Testing whether a selection contains elements.
    if ($("div.foo").length){
        ...
    }

####Saving Selections
jQuery doesn't cache elements for you. If you've made a selection that you might need to make again, you should save the selection in a variable rather than making the selection repeatedly.

    var divs = $("div");

Once the selection is stored in a variable, you can call jQuery methods on the variable just like you would have called them on the original selection.

A selection only fetches the elements that are on the page at the time the selection is made. If elements are added to the page later, you'll have to repeat the selection or otherwise add them to the selection stored in the variable. Stored selections don't magically update when the DOM changes.

####Refining & Filtering Selections
Sometimes the selection contains more than what you're after. jQuery offers several methods for refining and filtering selections.

    //Refining selections.
    $("div.foo").has("p");          //div.foo elements that contain <p> tags
    $("h1").not(".bar");            //h1 elements that don't have a class of bar
    $("ul li").filter(".current");  //unordered list items with class of current
    $("ul li").first();                  //just the first unordered list items
    $("ul li").eq(5);                  // the sixth

####Selecting Form Elements
jQuery offers several pseudo-selectors that help find elements in forms. These are especially helpful because it can be difficult to distinguish between form elements based on their state or type using standard CSS selectors.

**:checked**

Not to be confused with *:checkbox*, `:checked` targets *checked* checkboxes, but keep in mind that whis selector works also for *checked* radio buttons, and `<select>` elements (for `<select>` elements only, use the `:selected` selector):

    $("form :checked");

The `:checked` pseudo-selector works when used with *checkboxes*, *radio buttons* and *selects*.

**:disabled**

Using the `:disabled` pseudo-selector targets any `<input>` elements with the `disabled` attribute:
    
    $("form :disabled");

In order to get the best performance using `:disabled`, first select elements with a standard jQuery selector, then use `.filter(":disabled")`, or precede the pseudo-selector with a tag name or some other selector.

**:enabled**

Basically the inverse of the `:disabled` pseudo-selector, the `:enabled` pseudo-selector targets any elements that do not have a `disabled` attribute:

    $("form :enabled");

In order to get the best performance using `:enabled`, first select elements with a standrad jQuery selector, then use `.filter(":enabled")`, or precede the pseudo-selector with a tag name or some other selector.

**:input**
Using the `:input` selector selects all `<input>`, `<textarea>`, `<select>`, and `<button>` elements:
    
    $("form :input");

**:selected**
Using the `:selected` pseudo-selector targets any selected items in `<option>` elements:

    $("form :selected");

In order to get the best performance using `:selected`, first select elements with a standard jQuery selector, then use `.filter(":selected")`, or precede the pseudo-selector with a tag name or some other selector.

**Selecting by type**
jQuery provides pseudo selectors to select form-specific elements according to their type:
    :password
    :reset
    :radio
    :text
    :submit
    :checkbox
    :botton
    :image
    :file


##Working with Selections

####Getters & Setters
Some jQuery methods can be used to either assign or read some value on a selection. When the method is called with a value as an argument, it's referred to as a setter because it sets (or assigns) that value. When the method is called with no argument, it gets (or reads) the value of the element. Setters affects all elements in a selection, with the exception of `.text()`, which retrieves the values of all the elements.

    //The .html() method sets all the h1 elements' html to be "hello world":
    $("h1").html("hello world");

    //The .html() method returns the html of the first h1 element:
    $("h1").html();
    // >"hello world"

Setter return a jQuery object, allowing you to continue calling jQuery methods on your selection. Getters return whatever they were asked to get, so you can't continue to call jQuery methods on the value returned bu the getter.

    //Attempting to call a jQuery method after calling a getter.
    //This will NOT work:
    $("h1").html().addClass("test");

####Chaining

If you call a method on a selection and that method returns a jQuery object, you can continue to call jQuery methods on the object without pausing for a semicolon. This practice is referred to as "chaining":

    $("#content").find("h3").eq(2).html("new text for the third h3!");

It may help code readability to break the chain over several lines:

    $("#content")
        .find("h3")
        .eq(2)
        .html("new text for the third h3!");

jQuery also provides the `.end()` method to get back to the original selection should you change the selection in the middle of a chain:

    $("#content")
        .find("h3")
        .eq(2)
            .html("new text for the third h3!")
            .end() //Restores the selection to all h3s in #content
        .eq(0)
            .html("new text for the first h3!");

Chaining is extraordinarily powerful, and it's a feature that many libraries have adapted since it was made popular by jQuery. However, it must be used with care - extensive chaining can make code extremely difficult to modify or debug. There is no hard-and-fast rule to how long a chain should be - just know that it's easy to get carried away.


##Manipulating Elements

For complete documentation of jQuery manipulation methods, visit the **Manipulation documentation on api.jquery.com**.

###Getting and Setting Information About Elements
There are many ways to change an existing element. Among the most common tasks is changing the inner HTML or attribute of an element. jQuery offers simple, cross-browser methods for these sorts of manipulations. You can also get information about elements using many of the same methods in their getter incarnations. For more information on getters and setters, see the **Working with Selections** section. Here are a few methods you can use to get and set information about elements:

1. `.html()` -- Get or set the HTML contents.
2. `.text()` -- Get or set the text contents; HTML will be stripped.
3. `.attr()` -- Get or set the value of the provided attribute.
4. `.width()` -- Get or set the width in pixels of the first elements in the selection as an integer.
5. `.height()` -- Get or set the height in pixels fo the first element in the selection as an integer.
6. `.position()` -- Get an object with position information for the first element in the selection, relative to its first positioned ancestor.(This is a getter only)
7. `.val()` -- Get or set the value of form elements.

Changing things about elements is trivial, but remember that the change will affect all elements in the selection. If you just want to change one element, be sure to specify that in the selection before calling a setter method.

    // Changing the HTML of an element.
    $("#myDiv p:first").html("New <strong>first</strong> paragraph!");


###Moving, Copying, and Removing Elements
While there are a variety of ways to move elements around the DOM, there are generally two approaches:
    
1. Place the selected element(s) relative to another element.
2. Place an element relative to the selected element(s).

For example, jQuery provides `.insertAfter()` and `.after()`. The `.insertAfter()` method places the selected element(s) after the element provided as an argument. The `.after()` method places the element provided as an argument after the selected element. Several other methods follow this pattern: `.insertBefore()` and `.before()`, `.appendTo()` and `.append()`, and `.prependTo()` and `.prepend()`.

The method that makes the most sense will depend on what elements are selected, and whether you need to store a reference to the elements you're adding to the page. If you need to store a reference, you will always want to take the first approach -- placing the selected elements relative to another element -- as it returns the element(s) you're placing. In this case, `.insertAfter()`, `.insertBefore()`, `appendTo()`, and `.prependTo()` should be the tools of choice.

    //Moving elements using different approaches.

    //Make the first list item the last list item:
    var li = $("#myList li:first").appendTo("#myList");

    //Another approach to the same problem:
    $("#myList").append($("#myList li:first"));
    //Note that there's no way to access the list item that we moved, as this returns the list itself.


###Cloning Elements
Methods such as `.appendTo()` move the element, but sometimes a copy of the element is needed instead. In this case, use `.clone()` first:

    //Making a copy of an element.
    //Copy the first list item to the end of the list:
    $("#myList li:first").clone().appendTo("#myList");

If you need to copy related data and events, be sure to pass `true` as an argument to `.clone()`.


###Removing Elements
There are two ways to remove elements from the page: `.remove()` and `.detach()`. Use `.remove()` when you want to permanently remove the selection from the page. While `.remove()` does return the removed element(s), those elements will not have their associated data and events attached to them if you return them to the page.

Use `.detach()` if you need the data and events to persist. Like `.remove()`, it returns the selection, but it also maintains the data and events associated with the selection, so you can restore the selection to the page at a later time.

The `.detach()` method is extremely valuable if you are doing heavy manipulation on an element. In that case, it's beneficial to `.detach()` the element from the page, work on it in your code, then restore it to the page when you're done. This limits expensive "DOM touches" while maintaing the element's data and events.

If you want to leave the element on the page but remove its contents, you can use `.empty()` to dispose of the element's inner HTML.


###Creating New Elements
jQuery offers a trivial and elegant way to create new elements using the same `$()` method used to make selections:

    //Creating new elements from an HTML string.
    $("<p>This is a new paragraph</p>");
    $("<li class=\"new\">new list item</li>");

    //Creating a new element with an attribute object.
    $("<a/>",{
        html:"This is a <strong>new</strong> link",
        "class":"new",
        href:"foo.html"
        });

Note that the attributes object in the second argument above, the property name class is quoted, although the property names `html` and `href` are not. Property names generally do not need to be quoted unless they are **reserved words**(as `class` is in this case).

When you create a new element, it is not immdiately added to the page. There are several ways to add an element to the page once it's been created.

    //Getting a new element on to the page.
    var myNewElement = $("<p>New element</p>");
    myNewElement.appendTo("#content");
    myNewElement.insertAfter("ul:last"); //This will remove the p from #content!
    $("ul").last().after(myNewElement.clone()); //Clone the p so now we have two.

The created element doesn't need to be stored in a variable - you can call the method to add the element to the page directly after the `$()`. However, most of the time you'll want a reference to the element you added so you won't have to select it later.

You can also create an element as you're adding it to the page, but note that in this case you don't get a reference to the newly created element:

    //Creating and adding an element to the page at the same time.
    $("ul").append("<li>list item</li>");

The syntax for adding new elements to the page is easy, so it's tempting to forget that there's a huge performance cost for adding to the DOM repeatedly. If you're adding many elements to the same container, you'll want to concatenate all the HTML into a single string, and then append that string to the container instead of appending the element's one at a time. Use an array to gather all the pieces together, then join them into a single for appending:

    var myItems = [];
    var myList = $("#myList");
    for(var i=0; i<100; i++){
        myItems.push("<li>item " + i + "</li>");
    }
    myList.append(myItems.join(""));

###Manipulating Attributes
jQuery's attribute manipulation capabilities are extensive. Basic changes are simple, but the `.attr()` method also allows for more complex manipulations. It can either set an explicit value, or set a value using the return value of a function. When the function syntax is used, the function receives two arguments: the zero-based index of the element whose attribute is being changed, and the current value of the attribute being changed.

    //Manipulating a single attribute.
    $("#myDiv a:first").attr("href","newDestination.html");

    //Manipulating mulitple attributes.
    $("#myDiv a:first").attr({
        href:"nerDestination.html",
        rel:"nofollow"
        });

    //Using a function to determine an attribute's new value.
    $("#myDiv a:first").attr({
        rel:"nofollow",
        href: function(idx,href){
            return "/new/" + href;
        }
    });
    $("#myDiv a:first").attr("href", function(idx,href){
        return "/new/" + href;
    });



##The jQuery Object
When creating new elements (or selecting existing ones), jQuery returns the elements in a collection. Many developers new to jQuery assume that this collection is an array. It has a zero-indexed sequence of DOM elements, some familiar array functions, and a `.length` property, after all. Actually, the jQuery object is more complicated than that.

DOM and DOM Elements
The Document Object Model(DOM for short) is a representation of an HTML document. It may contain any number of DOM elements. At a high level, a DOM element can be thought of as "piece" of a web page. It may contain text and/or other DOM elements. DOM elements are described by a type, such as `<div>`, `<a>`, or `<p>`, and any number of attributes such as `src`, `href`, `class` and so on. For a more thorough description, refer to **the official DOM specification from the W3C**.

Elements have properties like any JavaScript object. Among these properties are attributes like `.tagName` and methods like `.appendChild()`. These properties are the only way to interact with the web page via JavaScript.

###The jQuery Object
It turns out that working directly with DOM elements can be awkward. The jQuery object defines many methods to smooth out the experience for developers. Some benefits of the jQuery Object include:

**Compatibility** -- The implementation of element methods varies across browser vendors and versions. The following snippet attempts to set the inner HTML of a `<tr>` element stored in `target`:

    var target = document.getElementById("target");
    target.innerHTML = "<td>Hello <b>World</b>!</td>";

This works in many cases, but it will fail in most versions of Internet Explorer. In that case, the recommended approach(http://quirksmode.org/dom/html/) is to use pure DOM methods instead. By wrapping the `target` element in a jQuery object, these edge cases are taken care of, and the expected result is achieved in all supported browsers:

    //Setting the inner HTML with jQuery.
    var target = document.getElementById("target");
    $(target).html("<td>Hello <b>World</b>!</td>")

**Convenience** -- There are also a lot of common DOM manipulation use cases that are awkward to accomplish with pure DOM methods. For instance, inserting an element stored in `newElement` after the target element requires a rather verbose DOM method:

    // Inserting a new element after another with the native DOM API.
    var target = document.getElementById("target");
    var newElement = document.createElement("div");
    target.parentNode.insertBefore(newElement, target.nextSibling);

By wrapping the `target` element in a jQuery object, the same task becomes much simpler:

    //Inserting a new element after another with jQuery.
    var target = document.getElementById("target");
    var newElement = document.createElement("div");
    $(target).after(newElement);

For the most part, these details are simply "gotchas" standing between you and your goals.


####Getting Elements Into the jQuery Object
When the jQuery function is invoked with a CSS selector, it will return a jQuery object wrapping any element(s) that match this selector. For instance, writing:

    //Selecting all <h1> tags.
    var headings = $("h1");

`headings` is now a jQuery element containing all the `<h1>` tags already on the page. This can be verified by inspecting the `.length` property of `headings`:

    //Viewing the number of <h1> tags on the page.
    var headings = $("h1");
    alert(headings.length);

If the page had more than one `<h1>` tag, this number will be greater than one. If the page has no `<h1>` tags, the `.length` property will be zero. Checking the `.length` property is a common way to ensure that the selector successfully matched on or more elements.

If the goal is to select only the first heading element, another step is required. There are a number of ways to accomplish this, but the most straight-forward is the `.eq()` function.

    //Selecting only the first <h1> element on the page(in a jQuery object)
    var headings = $("h1");
    var firstHeading = headings.eq(0);

Now firstHeading is a jQuery object containing only the first `<h1>` element on the page. And because `firstHeading` is a jQuery object, it has useful methods like `.html()` and `.after()`. jQuery also has a method named `.get()` which provides a related function. Instead of returning a jQuery-wrapped DOM element, it returns the DOM element itself.

    //Selecting only the first <h1> element on the page.
    var firstHeadingElem = $("h1").get(0);

Alternatively, because the jQuery object is "array-like", it supports array subscripting via brackets:

    //Selecting only the first <h1> element on the page (alternate approach).
    var firstHeadingElem = $("h1")[0];

In either case(`get()` or `[]`), `firstHeadingElem` contains the native DOM element. This means it has DOM properties like `.innerHTML` and methods like `.appendChild()`, but not jQuery methods like `.html()` or `.after()`. The `firstHeadingElem` element is more difficult to work with, but there are certain instances that require it. One such instance is making comparisons.

####Not All jQuery Objects are Created `===`
An important detail regarding this "wrapping" behavior is that each wrapped object is unique. This is true **even if the object was created with the same selector or contain references to the exact same DOM elements**.

    //Creating two jQuery objects for the same element.
    var logo1 = $("#logo");
    var logo2 = $("#logo");

Although `logo1` and `logo2` are created in the same way (and wrap the same DOM element), they are not the same object. For example:

    //Comparing jQuery objects.
    alert($("#logo") === $("#logo")); //alert "false"

However, both objects contain the same DOM element. The `.get()` method is useful for testing if two jQuery objects have the same DOM element.

    // Comparing DOM elements.
    var logo1 = $("#logo");
    var logo1Elem = logo1.get(0);

    var logo2 = $("#logo");
    var logo2Elem = logo2.get(0);
    alert(logo1Elem === logo2Elem); //alerts "true"

Many developers prefix a `$` to the name of variables that contain jQuery objects in order to help differentiate. There is nothing magic about this pracitice -- it just helps some people keep track of what different variables contain. The previous example could be re-written to follow this convention:

    //Comparing DOM elements (with more readable variable names).
    var $logo1 = $("#logo");
    var logo1 = $logo1.get(0);

    var $logo2 = $("#logo");
    var logo2 = $logo2.get(0);

    alert(logo1 === logo2);  //alerts "true"

This code functions identically to the example above, but it is a little more clear to read.

Regardless of the naming convertion used, it is very important to make the distinction between jQuery object and native DOM elements. Native DOM methods and properties are not present on the jQuery object, and vice versa.
Error messages like "event.target.closest is not a function" and "TypeError: Object [object Object]" has no method "setAttribute" indicate the presence of this common mistake. 

####jQuery Objects Are Not "Live"
Given a jQuery object with all the paragraph elements on the page:
 