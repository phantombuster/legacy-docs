Modules
=======

This page lists all the installed modules that you can ``require()`` within your scripts. All modules are installed directly from NPM.

If you need us to install a specific module, contact us at contact@phantombuster.com and we'll do what we can.

To know the exact installed version of each module and the different versions of PhantomJS available, please see :ref:`Packages <packages>`.

For CasperJS, PhantomJS or Node
-------------------------------

These modules are compatible with all the Phantombuster commands (CasperJS, PhantomJS and Node). Require them at will.

- `phantombuster <agent_module.html>`_ — Access Phantombuster's services (download and save files, send emails, ...)
- `async <https://www.npmjs.com/package/async>`_ — Higher-order functions and common patterns for asynchronous code
- `bluebird <https://www.npmjs.com/package/bluebird>`_ — Full featured Promises/A+ implementation
- `deep-diff <https://www.npmjs.com/package/deep-diff>`_ — Calculate object differences
- `jstoxml <https://www.npmjs.com/package/jstoxml>`_, `jsontoxml <https://www.npmjs.com/package/jsontoxml>`_, `easyxml <https://www.npmjs.com/package/easyxml>`_, `data2xml <https://www.npmjs.com/package/data2xml>`_ — Convert objects or JSON to XML
- `linq <https://www.npmjs.com/package/linq>`_ — Microsoft's Language Integrated Query (LINQ) for JavaScript
- `mime <https://www.npmjs.com/package/mime>`_ — Mime-type mapping
- `pretty-data2 <https://www.npmjs.com/package/pretty-data2>`_, `pretty-data <https://www.npmjs.com/package/pretty-data>`_ — Pretty-print or minify XML, JSON, CSS and SQL files
- `resemblejs <https://www.npmjs.com/package/resemblejs>`_ — Image analysis and comparison with HTML5
- `underscore <https://www.npmjs.com/package/underscore>`_ — Functional programming helper library
- `xmltojson <https://www.npmjs.com/package/xmltojson>`_ — Convert XML to objects or JSON

For CasperJS and PhantomJS only
-------------------------------

These modules are only compatible with CasperJS or PhantomJS.

- `papaparse <https://www.npmjs.com/package/papaparse>`_ — Fast, in-browser CSV parser
- `whatwg-fetch <https://www.npmjs.com/package/whatwg-fetch>`_ — HTTP client (``window.fetch`` polyfill)

For Node only
-------------

These modules are only compatible with Node.

- `aws-sdk <https://www.npmjs.com/package/aws-sdk>`_ — The official AWS SDK for JavaScript
- `fast-csv <https://www.npmjs.com/package/fast-csv>`_ — CSV parser and writer
- `mkdirp <https://www.npmjs.com/package/mkdirp>`_, `fs-extra <https://www.npmjs.com/package/fs-extra>`_ — File system utilities
- `mongodb <https://www.npmjs.com/package/mongodb>`_, `mongoose <https://www.npmjs.com/package/mongoose>`_ — MongoDB
- `needle <https://www.npmjs.com/package/needle>`_, `request <https://www.npmjs.com/package/request>`_ — HTTP client
- `node-fetch <https://www.npmjs.com/package/node-fetch>`_ — ``window.fetch`` ported to node.js
- `through <https://www.npmjs.com/package/through>`_, `through2 <https://www.npmjs.com/package/through2>`_ — Simplified stream construction
- `xml2js <https://www.npmjs.com/package/xml2js>`_, `xml2json <https://www.npmjs.com/package/xml2json>`_ — XML to JavaScript object converter

Other modules
-------------

These modules are also available and compatible with Node. However, we have not yet tested them for CasperJS or PhantomJS compatibility.

- `axios <https://www.npmjs.com/package/axios>`_ — Promise based HTTP client
- `cheerio <https://www.npmjs.com/package/cheerio>`_ — Implementation of core jQuery for the server
- `is-my-json-valid <https://www.npmjs.com/package/is-my-json-valid>`_, `jsen <https://www.npmjs.com/package/jsen>`_ — JSON Schema validation
- `jsdom <https://www.npmjs.com/package/jsdom>`_ — Implementation of the DOM and HTML standards
- `lodash <https://www.npmjs.com/package/lodash>`_ — JavaScript utility library
- `moment <https://www.npmjs.com/package/moment>`_ — Parse, validate, manipulate, and display dates
- `once <https://www.npmjs.com/package/once>`_ — Only call a function once
- `pluralize <https://www.npmjs.com/package/pluralize>`_ — Pluralize and singularize any word
- `q <https://www.npmjs.com/package/q>`_ — Library for promises (CommonJS/Promises/A,B,D)
- `qs <https://www.npmjs.com/package/qs>`_ — Querystring parser
- `when <https://www.npmjs.com/package/when>`_ — Lightweight Promises/A+ and when() implementation

Injectable modules
------------------

These modules come preloaded on all agent disks. They can be injected in visited pages for easier DOM manipulation (mainly for scraping and automation purposes).

Use :ref:`nick.inject() <nick-inject>` or `page.injectJs() from PhantomJS <http://phantomjs.org/api/webpage/method/inject-js.html>`_ to add them in web pages.

- ``../injectables/jquery-2.2.3.min.js`` - `jQuery 2 <https://jquery.com/>`_ (84kB)
- ``../injectables/jquery-3.0.0.min.js`` - `jQuery 3 <https://jquery.com/>`_ (85kB)
- ``../injectables/underscore-1.8.3.min.js`` - `Underscore <http://underscorejs.org/>`_ (17kB)
- ``../injectables/lodash-core-4.13.1.min.js`` - `Lodash <https://lodash.com/>`_ (core build, 12kB)
- ``../injectables/lodash-full-4.13.1.min.js`` - `Lodash <https://lodash.com/>`_ (full build, 67kB)

For example, to use jQuery in a page that doesn't include it already, you could do this:

::

    nick.inject('../injectables/jquery-3.0.0.min.js', function(err) {
        if (!err)
            console.log('jQuery injected in current page')
    });
