A [DocPad](https://github.com/bevry/docpad) plugin that manages CSS and JS assets. Combined with Grunt.js’ Frontend task, creates cache-reset links to static files.

The main reasons to use this plugin are:

* Get compiled by Grunt.js resources and output links to resources with last modified date prefix to effectively reset cache every time resource is re-compiled.
* Organize resources into sets that can be selectively redefined in templates and documents.

## Installation ##

Run `npm install --save docpad-plugin-frontend` command in your DocPad project root.
    
## How it works ##

This plugin extends template data object with `assets(type)` method. When called inside layout template, returns sorted list of files for specified `type`. The `type` is simply a string identifier of the resource type, for example, `css` or `js`.

Whenever you call `assets(type)` method from template, it will collect resource list of specified type from current document and layout with respect of layout templates inheritance.

## Simple example ##

In your `layout` template, let’s say `default.html.eco`, define list of CSS and JS resources in document’s meta data. You can separate file entries with with comma:

```html
---
css: "/css/style.css"
js:  "/js/fileA.js,/js/fileB.js"
---
<!doctype html>
<html>
<body>
    
</body>
</html>
```

In the same template, use `assets()` method to get list of resources (I’m using [Eco](https://github.com/sstephenson/eco) templates, but you can use any other):

```html
---
css: "/css/style.css"
js:  "/js/fileA.js,/js/fileB.js"
---
<!doctype html>
<html>
<head>
    <% for url in @assets('css'): %><link rel="stylesheet" href="<%= url %>" /><% end %>
    <% for url in @assets('js'): %><script src="<%= url %>"></script><% end %>
</head>
<body>
    
</body>
</html>
```

If you’re using Grunt.js’ Frontend template task, generated file urls may look like this:

```html
<!doctype html>
<html>
<head>
    <link rel="stylesheet" href="/20121101001423/css/style.css" />
    <script src="/20121101001423/js/fileA.js"></script>
    <script src="/20121001001129/js/fileB.js"></script>
</head>
<body>
    
</body>
</html>
```

As you can see, generated urls contains numeric prefix which is _last compilation date_, taken from build catalog generated by Grunt.js Frontend task. Such prefixes can effectively reset cache from static resources every time this resource is recompiled.

## Organizing resources ##

You can organize resources into sets that can be overridden by descendant templates or document. All you need is to add numeric suffix to resource type, like this: `css2`, `js10` and so on.

For example, in `default.html.eco` template you can define the following resource sets in meta data:

    ---
    js: "fileA.js"
    js2: "fileB.js,fileC.js"
    js3: "fileD.js"
    ---

In `page.html.eco` template, which inherits `default.html.eco`, you can redefine resource set:

    ---
    layout: default
    js2: "foo.js"
    ---
    
You can also redefine resource sets in document that uses one of these templates:

    ---
    layout: page
    js3: "page.js"
    ---
    
The final resource list retrieved by  `assets('js')` call for your document will look like this:

    fileA.js
    foo.js
    page.js
    
All sets are sorted by numeric suffix.

****************

A real-world example is available in [Emmet Documentation](https://github.com/emmetio/emmet-docs) web-site source.