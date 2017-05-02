# Perry

Perry is an ultra-light-weight Web Content Management System - if you can call it that.
Perry addresses a small set of problems, but addresses them well - it does not do much more.

Simply speaking, Perry provides a means for injecting dynamically loaded content into static html pages. It uses moustache templates to promote seperation-of-concern between data and its presentation.

There is more behind the philosophy of Perry for those that are interested, for others, lets just get started!

# Getting Started

## Download the Javascript files and its dependencies.

Download the latest stable js file for Perry from [here]. 

## Dependencies
Perry has a dependency on jquery and handlebars.js 3.0.3

Handlebars can be downloaded from http://builds.handlebarsjs.com.s3.amazonaws.com/handlebars.runtime-v3.0.3.js

You can link in jquery from the google cdn

## Include Perry and the dependencies
Ensure that you include Perry and all its dependencies through a set of <script> tags on your html page. 

```html
<html>
	<head>
		...
	</head>
	<body>
		...
		
		<script src="https://ajax.googleapis.com/ajax/libs/jquery/1.11.1/jquery.min.js"></script>
		<script src="./js/handlebars-v3.0.3.js"></script>
		<script src="./js/iyana-perry.js"></script>
	</body>
</html>
```

### TODO
1. Better packaging with require.js, bower and npm
2. Explore server side rendering of templates on node.js
3. Uplift code to ES6/7
4. Test cases
5. Find a cdn to host this
6. Better examples

# Hello World
In order to render a fragment, Perry requires three things:
1. A template to rendered
2. Data to merge into the template (optional)
3. A placeholder to render the template into


## Template
Perry uses Handlebars as its template engine. A template in Perry is a self-contained Handlerbars template.

Create a template and store it in a folder that can be accessed by your web-server

```html
<div>
	Hello, {{salutation}} {{firstName}} {{lastName}} <br/>
</div>
```

## Data
Perry uses json for providing data to the template. Perry isn't fussed where the data comes from - it could be from a static json file or a dynamically generated response from a REST API.
For now, lets just create a json file that we can serve off our web server.

```javascript
{
    "salutation": "Prof.", 
    "firstName": "Dufen",
    "lastName": "Schmurtz"
}
```

## Placeholder
And finally, we need a place to render that into

```html
<html>
	...
	<div data-perry-data="../data/duff.json" data-perry-template="../template/hello.html">
		Loading ...
	</div>
	...
</html>
```

## How it works
Once the page finishes loading, Perry looks for any divs that are marked up with a data-perry-data attribute.

It then downloads the data and template from the specified URL. When both the pieces finish loading, Perry merges the data into the template and displays it in the placeholder.

> Note:
> The Placeholder div and its contents are replaced by the merged template - the inner and outer html both.

# Digging in deeper
## Russian dolls
Perry allows for templates to be referenced within templates. If a merged template has a div with a data-perry-data attribute, Perry will try to resolve that template as well!

## Templates without data
If you create a div that has a data-perry-template attribute but no data-perry-data attribute, Perry will simply load and display the template without a merge operation.
This could be useful for displaying boiler-plate information that changes regularly.

## Inline templates
Perry does not recognise or support inline templates. Don't waste your time there!

## Repeaters
The JSON data that is loaded by Perry can either be a JSON Object or a JSON array.

If Perry receives a JSON object, it merges the template with the JSON object and replaces the placeholder.

If Perry receives a JSON Array, it loops through the array and merges each object in the array with the template, concatenates them and replaces the placeholder.

In our Hello World example, switch to a different JSON

```javascript
[
    {"salutation": "Prof.", "firstName": "Dufen","lastName": "Schmurtz"},
    {"salutation": "Dr.", "firstName": "F.","lastName": "Xavier"}
]
```

Perry will render
```html
<div>
	Hello, Prof. Dufen Schmurtz <br/>
</div>
<div>
	Hello, Dr. F. Xavier <br/>
</div>
```

## Outer template
In some situations we may want to provide a single piece of markup inside which repeated elements are rendered.

This can be achieved by specifying a data-perry-outer-template attribute in the placeholder div.
```html
<html>
	...
	<div 
    data-perry-data="../data/duff.json" 
    data-perry-template="../template/hello.html" 
    data-perry-outer-template="../template/outer-hello.html">
		Loading ...
	</div>
	...
</html>
```

The outer template can be used to provide arbitrary markup that wraps the individual elements. The location where the individual elements should appear is indicated by {{merged}} as a Handlebars expression

``` html
<div>
	A big Hello to:
	{{merged}}
</div>
```

And we will get:
``` html
<div>
	A big Hello to:
	<div>
		Hello, Prof. Dufen Schmurtz <br/>
	</div>
	<div>
		Hello, Dr. F. Xavier <br/>
	</div>
</div>
```

## Error handling

Perry's default behaviour is to be lazy. When something goes wrong, Perry quietly logs the error to the console.

You could get Perry to display error messages by providing alternatives.
```html
<div>
	Loading
	<div class="perry-error">
		Something went badly wrong!
	</div>
</div>
```

You can use either class="perry-error" or data-perry-error-div="true"

If you have multiple Perry placeholders on a page, this could look quite ridiculous. To avoid that predicament, Perry allows for a page level error div that you specify with perry-global-error

``` html
<div class="perry-global-error">
```

When the page finishes loading, Perry hides any error divs that it finds.
If Perry encounters an error while rendering a placeholder on a page, it will remove that placeholder from the page's DOM and make the error div visible.

You may find it preferable to use css to mark the div as initially hidden as well. This will improve the experience on slow client devices.

> Note:
> For this functionality to work correctly, you must have a css class called `hidden` defined and in context.

## Using Page URL parameters in placeholder attributes

There is some functionality to do this, but I haven't yet got around to documenting it! Check out the examples.

# License
Perry is licensed under the Apache 2.0 license. It is free for commercial and non-commercial use. 
The copyright for Perry vests with Iyana Limited, a company incorporated under UK law.
