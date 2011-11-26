# best practice #

## Purpose ##

The following is a list of best practices for developers writing plugins for Popcorn.js. 

The goals are to provide the best user experience, to empower authors who may or may not have advanced programming skills and to make plugins general and reusable.

## Style ##

* Wherever possible, let the author set the styles, especially for things like fonts and colors. If you want to provide default styles, set classes and create a style sheet in the <head> that can be overridden. When setting style.display, use 'none' to hide and leave it blank to show. Do not assume the page needs your element to display as 'block', as there are other options (inline, inline-block, table, etc.). Allow absolute positioning (top, left, bottom, right) and size (width, height) in options where relevant. Accept all CSS length formats (em, px, %, etc), but percentages are preferred. If position options are provided, set container element to 'position: absolute'.
* Assume video or any other page elements can change size. If setting size or position of elements, use percentages.

## Events ##

If your plugin requires resources to load from the network, don't assume it will be loaded by the time the event displays. In start, make sure all resources are loaded before displaying.

* Implement frame method where appropriate for animations, such as movement or opacity.
* Always implement _teardown. Be careful about memory leaks, and do not assume that either start or end has run.
* Allow options to specify onStart, onFrame and onEnd callbacks that get called at the end of your start, frame and end events, with the options object and any other relevant information or elements. Accept a function or a string as the name of a global function. Callback functions should run after your code and should be wrapped in "try/catch" to prevent bad author code from taking down all of Popcorn.
* If your plugin loads complex elements, such as large images, canvases or iframes, check for duplicates.  Keep a list of created events, and if two events have the same data but don't overlap in time, re-use the first set of elements you created.  Allow for an option to force no duplicates, in case the author wants to change something in a callback.
* Optimize _setup and _teardown. When events are edited in Popcorn Maker, they are destroyed and re-created for every single change. So assume events will be created and destroyed hundreds of times, often with the same or similar values.

## Interaction ##

* If a user can click on your content, either to link to an external site or to engage in more detailed interaction, pause the main media.
* If you have rich content, pause the media when a user hovers their mouse over your content for more than 0.5 seconds. When the mouse is moved off, resume playing, but only if the media was not paused in the first place. (Make sure to handle all types of events that may move the mouse, not just mouseout.)
* Popcorn now provides a language setting. Use this to localize or translate your content wherever possible. For example, if querying Wikipedia, modify the subdomain to match the current language.) Try to allow authors to provide multiple versions of text.

## Data ##

* Make every effort to provide defaults or guess at values for properties that are not provided. "Guesses" should be predictable.
* If a required property is missing, fail gracefully with start and end methods that do nothing. (New events created by Popcorn Maker will only have start and end values and must not crash.)
* For any properties that handle multiple values, accept an array, a delimited string, a JSON encoded string or a single value. Arrays are the most flexible, but strings may be necessary, as when Popcorn event data are provided from Butter/Popcorn Maker.
* Names of properties applied to each event's options object should be standardized where possible so they may be handled generically by callbacks and effect plugins. The following table lists common properties.

<table cellpadding="0" cellspacing="0" class="c15"><tbody><tr><td class="c5"><p class="c0"><span class="c13">Property</span></p></td><td class="c11"><p class="c0"><span class="c13">Description</span></p></td><td class="c1"><p class="c0"><span class="c13">type(s)</span></p></td></tr><tr><td class="c5"><p class="c0"><span class="c6">target</span></p></td><td class="c11"><p class="c0"><span>id of element in which to place created content. Also accept a reference to the actual HTMLElement</span></p></td><td class="c1"><p class="c0"><span>string, HTMLElement</span></p></td></tr><tr><td class="c5"><p class="c0"><span class="c6">container</span></p></td><td class="c11"><p class="c0"><span>Most plugins create a new element containing the content for each event, typically a </span><span class="c6">&lt;div&gt;</span><span>. This is not typically provided by the author, but it should be attached to the options object for callbacks and effects.</span></p></td><td class="c1"><p class="c0"><span>HTMLElement</span></p></td></tr><tr><td class="c5"><p class="c0"><span class="c6">onStart,</span></p><p class="c0"><span class="c6">onFrame,</span></p><p class="c0"><span class="c6">onEnd</span></p></td><td class="c11"><p class="c0"><span>An optional callback function provided by the author that runs at the end of the start, frame and end methods, respectively. If provided as a string, plugin setup should look for the function in the global namespace, and change the property value to a reference to the function.</span></p></td><td class="c1"><p class="c0"><span>function, string</span></p></td></tr><tr><td class="c5"><p class="c0"><span class="c6">title</span></p></td><td class="c11"><p class="c0"><span>A string of text that serves as a title to accompany more detailed media. Typically plain text with no line breaks.</span></p><p class="c0 c10"><span></span></p><p class="c0"><span>Add to DOM with </span><span class="c6">document.createTextNode(options.title)</span></p></td><td class="c1"><p class="c0"><span>string</span></p></td></tr><tr><td class="c5"><p class="c0"><span class="c6">text</span></p></td><td class="c11"><p class="c0"><span>A string of text that may serve as the main content or possibly a caption. Typically plain text with possible line breaks.</span></p><p class="c0 c10"><span></span></p><p class="c0"><span>Add to DOM by splitting text by line breaks and alternating text nodes with </span><span class="c6">&lt;br&gt;</span><span> elements.</span></p></td><td class="c1"><p class="c0"><span>string</span></p></td></tr><tr><td class="c5"><p class="c0"><span class="c6">html</span></p></td><td class="c11"><p class="c0"><span>Arbitrary html content. Accept either a string or a DOM Node.</span></p></td><td class="c1"><p class="c0"><span>string, Node</span></p></td></tr><tr><td class="c5"><p class="c0"><span class="c6">url</span></p></td><td class="c11"><p class="c0"><span>Location of an external resource on the network, such as an image or an iframe. URL may be used directly or parsed for an identifier</span></p></td><td class="c1"><p class="c0"><span>string</span></p></td></tr><tr><td class="c5"><p class="c0"><span class="c6">link</span></p></td><td class="c11"><p class="c0"><span>A URL that will serve as an active, clickable link.</span></p></td><td class="c1"><p class="c0"><span>string</span></p></td></tr><tr><td class="c5"><p class="c0"><span class="c6">id</span></p></td><td class="c11"><p class="c0"><span>An identifier for a specific, external resource (e.g. a twitter user id, a single flickr photo, etc.)</span></p></td><td class="c1"><p class="c0"><span>string, number</span></p></td></tr><tr><td class="c5"><p class="c0"><span class="c6">classes</span></p></td><td class="c11"><p class="c0"><span>Any classes that may be applied to the container. Accept an array of strings or a string delimited by commas or spaces. (Use 'classes' here because 'class' is a reserved word in Javascript.)</span></p></td><td class="c1"><p class="c0"><span>array, string</span></p></td></tr><tr><td class="c5"><p class="c0"><span class="c6">style</span></p></td><td class="c11"><p class="c0"><span>CSS that may be added to the container (</span><span class="c6">.style.cssText = options.style</span><span>). If you implement this, run it early so can override specific style attributes as necessary.</span></p></td><td class="c1"><p class="c0"><span>string</span></p></td></tr></tbody></table>