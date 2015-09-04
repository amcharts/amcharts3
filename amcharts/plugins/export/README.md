# amCharts Export

Version: 1.1.3


## Description

This plugin adds export capabilities to all amCharts products - charts and maps.

It allows annotating and exporting chart or related data to various bitmap, 
vector, document or data formats, such as PNG, JPG, PDF, SVG, JSON, XLSX and 
many more.


## Important notice

Please note that due to security measures implemented in modern browsers, some 
or all export options might not work if the web page is loaded locally (via 
file:///) or contain images loaded from different host than the web page itself.


## Usage

### 1) Include the minified version of file of this plugin as well as the 
bundled CSS file. I.e.:

```
<script src="amcharts/plugins/export/export.min.js"></script>
<link  type="text/css" href="amcharts/plugins/export/export.css" rel="stylesheet">
```

Or if you'd rather use amCharts CDN:

```
<script src="//cdn.amcharts.com/lib/3/plugins/export/export.min.js"></script>
<link  type="text/css" href="//cdn.amcharts.com/lib/3/plugins/export/export.css" rel="stylesheet">
```

(this needs to go after all the other amCharts includes)

### 2) Enable `export` with default options:

```
AmCharts.makeChart( "chartdiv", {
  ...,
  "export": {
    "enabled": true
  }
} );
```

### ... OR set your own custom options:

```
AmCharts.makeChart( "chartdiv", {
  ...,
  "export": {
    "enabled": true,
    "menu": [ {
      "class": "export-main",
      "menu": [ {
        "label": "Download",
        "menu": [ "PNG", "JPG", "CSV" ]
      }, {
        "label": "Annotate",
        "action": "draw",
        "menu": [ {
          "class": "export-drawing",
          "menu": [ "PNG", "JPG" ]
        } ]
      } ]
    } ]
  }
} );
```


## Loading external libraries needed for operation of this plugin

The plugin relies on a number of different libraries, to export images, draw 
annotations or generate download files.

Those libraries need to be loaded for the plugin to work properly.

There are two ways to load them. Choose the one that is right:

### 1) Automatic (preferred)

All libraries required for plugin operation are included withing plugins */libs* 
subdirectory.

If you want the plugin to load them on-demand (when it's needed for a certain 
operation), make sure you've set the [`path`](http://docs.amcharts.com/3/javascriptcharts/AmSerialChart#path) property in your chart setup.

If you are using relative url, note that it is relative to the web page you are 
displaying your chart on, not the export.js library.

In case you've moved the libs folder you need to tell the plugin where it is
`"libs": { "path": "../libs/" }`

### 2) Manual

You can also load all those JavaScript libraries by `<script>` tags. Since 
loading of libraries is on by default you will need to turn it off by setting 
`"libs": { "autoLoad": false }`

Here is a full list of the files that need to be loaded for each operation:

File | Located in | Required for
---- | ---------- | ------------
fabric.min.js | libs/fabric.js/ | Any export operation
FileSaver.js | libs/FileSaver.js/ | Used to offer download files
pdfmake.min.js | libs/pdfmake/ | Export to PDF format
vfs_fonts.js | libs/pdfmake/ | Export to PDF format
jszip.js | libs/jszip/ | Export to XLSX format
xlsx.js | libs/xlsx/ | Export to XLSX format


## Complete list of available export settings

Property | Default | Description
-------- | ------- | -----------
backgroundColor | #FFFFFF | RGB code of the color for the background of the exported image
enabled | true | Enables or disables export functionality
divId | | ID or a reference to div object in case you want the menu in a separate container.
fabric | {} | Overwrites the default drawing settings (fabricJS library)
fallback | {} | Holds the messages to guide the user to copy the generated output; `false` will disable the fallback feature
fileName | amCharts | A file name to use for generated export files (an extension will be appended to it based on the export format)
legend | {} | Places the legend in case it is within an external container
libs | | 3rd party required library settings (see the above section)
menu | [] | A list of menu or submenu items (see the next chapter for details)
pdfMake | {} | Overwrites the default settings for PDF export (pdfMake library)
position | top-right | A position of export icon. Possible values: "top-left", "top-right" (default), "bottom-left", "bottom-right"
removeImages | true | If true export checks for and removes "tainted" images that area lodead from different domains


## Configuring export menu

Plugin includes a way to completely control what is displayed on export menu. 
You can set up menus, sub-menus, down to any level. You can even add custom 
items there that execute your arbitrary code on click. It's so configurable 
it makes us sick with power ;)

The top-level menu is configured via `menu` property under `export`. It should 
always be an array, even if you have a single item in it.

The array items could be either objects or format codes. Objects will allow you 
to specify labels, action, icon, child items and even custom code to be executed
on click.

Simple format codes will assume you need an export to that format.

### Simple menu setup

Here's a sample of the simple menu setup that allows export to PNG, JPG and CSV:

```
"export": {
  "enabled": true,
  "menu": [ {
    "class": "export-main",
    "menu": [ "PNG", "JPG", "CSV" ]
  } ]
}
```

The above will display a menu out of three options when you hover on export 
icon:

* PNG
* JPG
* CSV

When clicked the plugin will trigger export to a respective format.

If that is all you need, you're all set.

Please note that we have wrapped out menu into another menu item, so that only 
the icon is displayed until we roll over the icon. This means that technically
we have a two-level hierarchical menu.

If we opmitted that first step, the chart would simply display a format list 
right there on the chart.

### Advanced menu setup

However, you can do so much more with the menu.

Let's add more formats and organize image and data formats into separate 
submenus.

To add a submenu to a menu item, simply add a `menu` array as its own property:

```
"export": {
  "enabled": true,
  "menu": [ {
    "class": "export-main",
    "menu": [ {
      "label": "Download as image",
      "menu": [ "PNG", "JPG", "SVG" ]
    }, {
      "label": "Download data",
      "menu": [ "CSV", "XLSX" ]
    } ]
  } ]
}
```

Now we have a hierarchical menu with the following topology:

* Download as image
  * PNG
  * JPG
  * SVG
* Download data
  * CSV
  * XLSX

We can mix "string" and "object" formats the way we see fit, i.e.:

```
"menu": [
  "PNG", 
  { "label": "JPEG",
    "format": "JPG" },
  "SVG"
]
```

The magic does not end here, though.

### Adding custom click events to menu items

Just like we set `label` and `format` properties for menu item, we can set 
`click` as well.

This needs to be a function reference. I.e.:

```
"menu": [
  "PNG", 
  { "label": "JPEG",
    "click": function () {
      alert( "Clicked JPEG. Wow cool!" );
    } },
  "SVG"
]
```

### Menu item reviver

By passing the `menuReviver` callback you are to adapt or completely replace the
generated menu item before it gets appended to the list (`ul`).
It retrieves two arguments and it needs to return a valid DOM element.

```
"menuReviver": function(item,li) {
  li.setAttribute("class","something special");
  return li;
}
```

### Menu walker

In case you don't like our structure, go ahead and write your own recursive function
to create the menu by the given list configured through `menu`.

```
"menuWalker": function(list,container) {
  // some magic to generate the nested lists using the given list
}
```

### Printing the chart

Adding menu item to print the chart or map is as easy as adding export ones. You 
just use "PRINT" as `format`. I.e.:

```
"menu": [
  "PNG", 
  "SVG",
  "PRINT"
]
```

Or if you want to change the label:

```
"menu": [
  "PNG", 
  "SVG",
  {
    "format": "PRINT",
    "label": "Print Chart"
  }
]
```

### Annotating the chart before export

OK, this one is so cool, you'll need a class 700 insulation jacket.

By default each menu item triggers some kind of export. You can trigger an 
"annotation" mode instead by including `"action": "draw"` instead.

```
"menu": [ {
  "class": "export-main",
  "menu": [ {
    "label": "Download",
    "menu": [ "PNG", "JPG", "CSV", "XLSX" ]
  }, {
    "label": "Annotate",
    "action": "draw"
  } ]
} ]
```

Now, when you click on the "Annotate" item in the menu, the chart will turn into 
an image editor which you can actual draw on.

As cool as it may sound, there's little we can do if the annotated chart if we 
can't save the result image.

That's where sub-menus come for the rescue again:

```
"menu": [ {
  "class": "export-main",
  "menu": [ {
    "label": "Download",
    "menu": [ "PNG", "JPG", "CSV", "XLSX" ]
  }, {
    "label": "Annotate",
    "action": "draw",
    "menu": [ {
      "class": "export-drawing",
      "menu": [ "JPG", "PNG", "SVG", PDF" ]
    } ]
  } ]
} ]
```

Now, when you turn on the annotation mode, a submenu will display, allowing to 
export the image into either PNG,JPG,SVG or PDF.

And that's not even the end of it. You can add menu items to cancel, undo, redo.

```
"menu": [ {
  "class": "export-main",
  "menu": [ {
    "label": "Download",
    "menu": [ "PNG", "JPG", "CSV", "XLSX" ]
  }, {
    "label": "Annotate",
    "action": "draw",
    "menu": [ {
      "class": "export-drawing",
      "menu": [ {
        "label": "Edit",
        "menu": [ "UNDO", "REDO", "CANCEL" ]
      }, {
        "label": "Save",
        "format": "PNG"
      } ]
    } ]
  } ]
} ]
```

If you need to filter the drawn elements you can pass the `reviver` method in your global configuration or pass it to the `capture` method if you export manually. To hide e.G. all free labels you can simply do so like following:
```
"menu": ["PNG"],
"reviver": function(obj) {
  if ( obj.className == "amcharts-label" ) {
    obj.opacity = 0;
  }
}
```

### A list of menu item properties

Property | Description
-------- | -----------
action | Set to "draw" if you want the item to trigger annotation mode
class | Class name applied to the tag
click | Function handler invoked upon click on menu item
format | A format to export chart/map to upon click (see below for a list of available formats)
icon | Icon file (will use chart's [path](http://docs.amcharts.com/3/javascriptcharts/AmSerialChart#path) if the URL is not full)
label | Text label to be displayed
menu | An array of submenu items
title | A title attribute of the link
backgroundColor | The background color of the canvas
fileName | A file name to use for generated export files (an extension will be appended to it based on the export format)
extension | File extension for the generated export file (uses format default if not defined)
mimeType | Internet media type to generate the export file (usses format default if not defined)
pageSize | A string or { width: number, height: number } ([details](#exporting-to-pdf))
pageOrientation | By default we use portrait, you can change it to landscape if you wish ([details](#exporting-to-pdf))
pageMargins | [left, top, right, bottom] or [horizontal, vertical] or just a number for equal margins ([details](#exporting-to-pdf))
content | Array of elements which represents the content ([details](#exporting-to-pdf))
freeDrawingBrush | Object which hold the settings of the brush e.G.: { color: "#FF00FF" }
multiplier | Scale factor for the generated image
lossless | Flag to print the actual vector graphic instead of buffered bitmap (print option only, experimental)

Available `format` values:

* JPG
* PNG
* SVG
* CSV
* JSON
* PDF
* XLSX
* PRINT

### Exporting to PDF

When exporting to PDF, you can set and modify the content of the resulting 
document. I.e. add additional text and/or modify image size, etc.

To do that, you can use menu item's `content` property.

Each item in `content` represents either a text line (string) or an exported 
image.

To add a text line, simply use a string. It can even be a JavaScript variable or 
a function that returns a string.

To include exported image, use `image: "reference"`.

Additionally, you can add `fit` property which is an array of pixel dimensions, 
you want the image to be scaled to fit into.

Here's an example of such export menu item:

```
{
  "format": "PDF",
  "content": [ "Saved from:", window.location.href, {
    "image": "reference",
    "fit": [ 523.28, 769.89 ] // fit image to A4
  } ]
}
```

Property | Description
-------- | -----------
pageSize | a string or { width: number, height: number }
pageOrientation | by default we use portrait, you can change it to landscape if you wish
pageMargins | [left, top, right, bottom] or [horizontal, vertical] or just a number for equal margins
content | array of elements which represents the content ([full description](https://github.com/bpampuch/pdfmake/))

Pagesize | Dimensions in pixel
-------- | -----------
4A0 | [4767.87, 6740.79]
2A0 | [3370.39, 4767.87]
A0 | [2383.94, 3370.39]
A1 | [1683.78, 2383.94]
A2 | [1190.55, 1683.78]
A3 | [841.89, 1190.55]
A4 | [595.28, 841.89]
A5 | [419.53, 595.28]
A6 | [297.64, 419.53]
A7 | [209.76, 297.64]
A8 | [147.40, 209.76]
A9 | [104.88, 147.40]
A10 | [73.70, 104.88]
B0 | [2834.65, 4008.19]
B1 | [2004.09, 2834.65]
B2 | [1417.32, 2004.09]
B3 | [1000.63, 1417.32]
B4 | [708.66, 1000.63]
B5 | [498.90, 708.66]
B6 | [354.33, 498.90]
B7 | [249.45, 354.33]
B8 | [175.75, 249.45]
B9 | [124.72, 175.75]
B10 | [87.87, 124.72]
C0 | [2599.37, 3676.54]
C1 | [1836.85, 2599.37]
C2 | [1298.27, 1836.85]
C3 | [918.43, 1298.27]
C4 | [649.13, 918.43]
C5 | [459.21, 649.13]
C6 | [323.15, 459.21]
C7 | [229.61, 323.15]
C8 | [161.57, 229.61]
C9 | [113.39, 161.57]
C10 | [79.37, 113.39]
RA0 | [2437.80, 3458.27]
RA1 | [1729.13, 2437.80]
RA2 | [1218.90, 1729.13]
RA3 | [864.57, 1218.90]
RA4 | [609.45, 864.57]
SRA0 | [2551.18, 3628.35]
SRA1 | [1814.17, 2551.18]
SRA2 | [1275.59, 1814.17]
SRA3 | [907.09, 1275.59]
SRA4 | [637.80, 907.09]
EXECUTIVE | [521.86, 756.00]
FOLIO | [612.00, 936.00]
LEGAL | [612.00, 1008.00]
LETTER | [612.00, 792.00]
TABLOID | [792.00, 1224.00]

## Styling the export menu

The plugin comes with a default CSS file `export.css`. You just need to include 
it on your page.

Feel free to override any styles defined in it, create your own version and 
modify as you see fit.

If you choose to modify it, we suggest creating a copy so it does not get 
overwritten when you update amCharts or plugin.


## Plugin API

We explained how you can define custom functions to be executed on click on 
export menu items.

Those functions can tap into plugin's methods to augment it with some custom 
functionality.

Here's an example:

```
menu: [ {
  label: "JPG",
  click: function() {
    this.capture({},function() {
      this.toJPG( {}, function( data ) {
        this.download( data, "image/jpg", "amCharts.jpg" );
      });
    });
  }
} ]
```

The above will use plugin's internal `capture` method to capture it's current state and `toJPG()`
method to export the chart to JPEG format.

Yes, you're right, it's the exact equivalent of just including "JPG" string. The 
code is here for the explanatory purposes.

Here's a full list of API functions available for your consumption:

Function | Parameters | Description
-------- | ---------- | -----------
toJPG | (object) options, (function) callback | Prepares a JPEG representation of the chart and passes the binary data to the callback function
toPNG | (object) options, (function) callback | Prepares a PNG representation of the chart and passes the binary data to the callback function
toSVG | (object) options, (function) callback | Prepares a SVG representation of the chart and passes the binary data to the callback function
toPDF | (object) options, (function) callback | Prepares a PDF representation of the chart and passes the binary data to the callback function
toJSON | (object) options, (function) callback | Prepares a JSON and passes the plain data to the callback function
toCSV | (object) options, (function) callback | Prepares a CSV and passes the plain data to the callback function
toXLSX | (object) options, (function) callback | Prepares a XLSX representation of the chart and passes the binary data to the callback function
toBlob | (object) options, (function) callback | Prepares a BLOB and passes the instance to the callback function
toCanvas | (object) options, (function) callback | Prepares a Canvas and passes the element to the callback function
toArray | (object) options, (function) callback | Prepares an Array and passes the data to the callback function
toImage | (object) options, (function) callback | Generates an image element which holds the output in an embedded base64 data url


## Fallback for IE9

Unfortunately our lovely Internet Explorer 9 does not allow us to offer downloads which has been locally generated.
For those cases the plugin will place an overlay on top of the chart to place an `img` or `textarea` to let the user manually save the generated output with some instructions above.
To avoid having a bigger payload by including senseless polyfills to your site, you may need to add following metatag in your `<head>` of your HTML document.

```
<meta http-equiv="X-UA-Compatible" content="IE=edge" />
```

This feature will kick in by default if you want to disable it simply pass `false` to the `fallback` parameter.

```
{
  fallback: false
}
```

In case you want to change our default messages you can modify it like following.

```
{
  fallback: {
    text: "CTRL + C to copy the data into the clipboard.",
    image: "Rightclick -> Save picture as... to save the image."
  }
}
```

## Requirements

This plugin requires at least 3.13 version of JavaScript Charts, JavaScript
Stock Chart or JavaScript Maps.

The export will also need relatively recent browsers.

IE10 and up are supported.

Partial support for IE9; Fallback mechanism.

IE8 and older are not supported I'm afraid. Hey, it's time to upgrade!


## Demos

They're all in subdirectory /examples.


## Extending this plugin

You're encouraged to modify, extend and make derivative plugins out of this
plugin.

You can modify files, included in this archive or, better yet, fork this project
on GitHub:

https://github.com/amcharts/export

We're curious types. Please let us know (contact@amcharts.com) if you do create
something new out of this plugin.


## License

This plugin is licensed under Apache License 2.0.

This basically means you're free to use or modify this plugin, even make your
own versions or completely different products out of it.

Please see attached file "license.txt" for the complete license or online here:

http://www.apache.org/licenses/LICENSE-2.0


## Contact us

* Email:contact@amcharts.com
* Web: http://www.amcharts.com/
* Facebook: https://www.facebook.com/amcharts
* Twitter: https://twitter.com/amcharts


## Changelog

### 1.1.3
* Added: Added reviver in capturing method to filter the drawn chart elements

### 1.1.2
* Added: Generalized fallback; does a lookup on the Blob constructor
* Fix: Wait for lazy images, triggers capture callback only when all images have been fully loaded
* Discovered: [Safari 5 issue](https://github.com/kangax/fabric.js/issues/2241) please adapt fabric.js manually to solve it

### 1.1.1
* Fix: CSV export issue on date based charts
* Fix: Enhanced migration script to obtain more settings

### 1.1.0
* Fix: Print issue on safari which captured the actual page instead of the export
* Added: IE9 download fallback for `text/plain` and `image/*` mime types (CSS has been modified)
* Added: `toImage` method; returns `img` element with embedded base64 imagery
* Added: `getBase64` option in `toSVG`
* Added: `toImage` usage in `toPRINT` to be able to choose the image type + settings.
* Added: `lossless` option in `toPRINT` (experimental)

### 1.0.9
* Added: IE9 base64 export
* Added: Third party updates + minified versions

### 1.0.8
* Fix: IE8 issue which prevents the chart from initiating

### 1.0.7
* Fix: issue on toCSV handling the header (first row)

### 1.0.6
* Fix: issue on revalidation the chart/map
* Added: [path](http://docs.amcharts.com/3/javascriptcharts/AmSerialChart#path) to load the libaries by default

### 1.0.5
* Added: divId to be able to place the menu within an external container
* Added: menuWalker to replace the whole menu generation
* Added: menuReviver to adapt menu items before being appended to the list
* Added: libs.async to load dependencies asynchronous (default true)

### 1.0.4
* Considering classNamePrefix (dont't forget to adapt export.css)
* Added: safety delay on print restore to ensure capturing the canvas

### 1.0.3
* Fix: flagged relative image paths as tainted

### 1.0.2
* Fix: compabitily on array method extension such as PrototypeJS

### 1.0.1
* Added: libs.reload: false, script tag crawling to avoid multiple insertions
* Fix: IE10 bug on print
* Fix: migration bug, replaces menu instead of appending

### 1.0
* Initial release