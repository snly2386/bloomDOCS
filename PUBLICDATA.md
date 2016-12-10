Introduction
=======

BloomAPI Public Data is a way to programatically access public datasets. It may be used from sites such as <www.bloomapi.com/search> or, as a software developer, it may be queried for use in application development. The documentation below explains how, as a software developer, to use or deploy your own copy of BloomAPI Public Data.

Note that anything marked as *Experimental* in this documentation may be removed without warning in a future release. If you’d like to depend on these features in production, sign up for an account or consider hosting your own copy of BloomAPI Public Data.

API Usage
=======

Calling
---------------

BloomAPI is a http-based API. It can be queried at <http://www.bloomapi.com/api/> or at the url of your own deployment.

Experimental JSONP is currently supported by providing a `callback` method through the parameter callback.

All responses are JSON objects with the following parameters

``` javascript
Example 200 Response

curl -XGET https://www.bloomapi.com/api/search/usgov.hhs.npi

{ "meta":
  {
    "count": 1923
  },
  "result": [
    {
      "npi": 1111111111,
      "type": "individual"
    }
  ]
}
```

| Name          | Description
| ------------- |:-------------:|
| meta          | Information related to the query such as number or results or warning messages |
| results       | Payload response such as the data being queried for                            |


Errors
---------------
Errors are communicated through http return codes. Codes that are returned by BloomAPI include

* 400: User error such as invalid parameters
* 404: API endpoint or entity not found
* 5xx: Server error. Likely caused by a bug in BloomAPI

``` javascript
Example 400 Response

{
  "error:": "Error message",
  "type": "parameter|fatal",
  "details": {
    "some-key": "An object that represents this error type"
  }
}

`400` errors also include a JSON response object explaining the cause of the error
```

API Key
---------------

Development access at <http://www.bloomapi.com/api> is available without an API key, however, for production access, for https access, or for access to private data sources — an API key is required. Some of the current private datasources include:

* Federal Sanctions List
* PECOS
* Hospital Compare
* ICD-9/10/ Crosswalks
* HCPCS
* FDA NDC
* AHRQ files
* CCLF (Claims and Claims Line Feed)
* If you are interested in datasources such as these or unlimited production access, you’ll need a  BloomAPI Account.

Sign Up to request an API Key.

Once you have an API key, it can be added to any request by adding the parameter secret to any request.

``` javascript
Example Usage of API Key

https://www.bloomapi.com/api/search/usgov.hhs.npi?secret=:api_key_here&key1=practice_address.zip&op1=eq&value1=98101
```

Programatic Clients
=======






How it works
------------

Flatdoc is a hosted `.js` file (along with a theme and its assets) that you can
add into any page hosted anywhere.

#### All client-side

There are no build scripts or 3rd-party services involved. Everything is done in
the browser. Worried about performance? Oh, It's pretty fast.

Flatdoc utilizes the [GitHub API] to fetch your project's Readme files. You may
also configure it to fetch any arbitrary URL via AJAX.

#### Lightning-fast parsing

Next, it uses [marked], an extremely fast Markdown parser that has support for
GitHub flavored Markdown.

Flatdoc then simply renders *menu* and *content* DOM elements to your HTML
document. Flatdoc also comes with a default theme to style your page for you, or
you may opt to create your own styles.

Markdown extras
---------------

Flatdoc offers a few harmless, unobtrusive extras that come in handy in building
documentation sites.

#### Code highlighting

You can use Markdown code fences to make syntax-highlighted text. Simply
surround your text with three backticks. This works in GitHub as well.
See [GitHub Syntax Highlighting][fences] for more info.

    ``` html
    <strong>Hola, mundo</strong>
    ```

#### Blockquotes

Blockquotes show up as side figures. This is useful for providing side
information or non-code examples.

> Blockquotes are blocks that begin with `>`.

#### Smart quotes

Single quotes, double quotes, and double-hyphens are automatically replaced to
their typographically-accurate equivalent. This, of course, does not apply to
`<code>` and `<pre>` blocks to leave code alone.

> "From a certain point onward there is no longer any turning back. That is the
> point that must be reached."
> --Franz Kafka

#### Buttons

If your link text has a `>` at the end (for instance: `Continue >`), they show
up as buttons.

> [View in GitHub >][project]

Customizing
===========

Basic
-----

### Theme options

For the default theme (*theme-white*), You can set theme options by adding
classes to the `<body>` element. The available options are:

#### big-h3
Makes 3rd-level headings bigger.

``` html
<body class='big-h3'>
```

#### no-literate
Disables "literate" mode, where code appears on the right and content text
appear on the left.

``` html
<body class='no-literate'>
```

#### large-brief
Makes the opening paragraph large.

``` html
<body class='large-brief'>
```

### Adding more markup

You have full control over the HTML file, just add markup wherever you see fit.
As long as you leave `role='flatdoc-content'` and `role='flatdoc-menu'` empty as
they are, you'll be fine.

Here are some ideas to get you started.

 * Add a CSS file to make your own CSS adjustments.
 * Add a 'Tweet' button on top.
 * Add Google Analytics.
 * Use CSS to style the IDs in menus (`#acknowledgements + p`).

### JavaScript hooks

Flatdoc emits the events `flatdoc:loading` and `flatdoc:ready` to help you make
custom behavior when the document loads.

``` js
$(document).on('flatdoc:ready', function() {
  // I don't like this section to appear
  $("#acknowledgements").remove();
});
```

Full customization
------------------

You don't have to be restricted to the given theme. Flatdoc is just really one
`.js` file that expects 2 HTML elements (for *menu* and *content*). Start with
the blank template and customize as you see fit.

[Get blank template >][template]

Misc
====

Inspirations
------------

The following projects have inspired Flatdoc.

 * [Backbone.js] - Jeremy's projects have always adopted this "one page
 documentation" approach which I really love.

 * [Docco] - Jeremy's Docco introduced me to the world of literate programming,
 and side-by-side documentation in general.

 * [Stripe] - Flatdoc took inspiration on the look of their API documentation.

 * [DocumentUp] - This service has the same idea but does a hosted readme
 parsing approach.

Attributions
------------

[Photo](http://www.flickr.com/photos/doug88888/2953428679/) taken from Flickr,
licensed under Creative Commons.

Acknowledgements
----------------

© 2013, 2014, Rico Sta. Cruz. Released under the [MIT
License](http://www.opensource.org/licenses/mit-license.php).

**Flatdoc** is authored and maintained by [Rico Sta. Cruz][rsc] with help from its
[contributors][c].

 * [My website](http://ricostacruz.com) (ricostacruz.com)
 * [Github](http://github.com/rstacruz) (@rstacruz)
 * [Twitter](http://twitter.com/rstacruz) (@rstacruz)

[rsc]: http://ricostacruz.com
[c]:   http://github.com/rstacruz/flatdoc/contributors

[GitHub API]: http://github.com/api
[marked]: https://github.com/chjj/marked
[Backbone.js]: http://backbonejs.org
[dox]: https://github.com/visionmedia/dox
[Stripe]: https://stripe.com/docs/api
[Docco]: http://jashkenas.github.com/docco
[GitHub pages]: https://pages.github.com
[fences]:https://help.github.com/articles/github-flavored-markdown#syntax-highlighting
[DocumentUp]: http://documentup.com

[project]: https://github.com/rstacruz/flatdoc
[template]: https://github.com/rstacruz/flatdoc/raw/gh-pages/templates/template.html
[blank]: https://github.com/rstacruz/flatdoc/raw/gh-pages/templates/blank.html
[dist]: https://github.com/rstacruz/flatdoc/tree/gh-pages/v/0.9.0