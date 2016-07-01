Modules
=======

This page lists all the installed modules that you can ``require()`` within your scripts. All modules come from NPM and are Node-compatible.

If you need us to install a specific module, contact us at contact@phantombuster.com and we'll do what we can.

For CasperJS, PhantomJS or Node
-------------------------------

These modules are compatible with all the Phantombuster commands (CasperJS, PhantomJS and Node). Require them at will.

- `phantombuster <agent_module.html>`_ — Access Phantombuster's services (download and save files, send emails, ...)
- `async <https://www.npmjs.com/package/async>`_ — Higher-order functions and common patterns for asynchronous code
- `mime <https://www.npmjs.com/package/mime>`_ — Mime-type mapping
- `jstoxml <https://www.npmjs.com/package/jstoxml>`_, `jsontoxml <https://www.npmjs.com/package/jsontoxml>`_, `easyxml <https://www.npmjs.com/package/easyxml>`_, `data2xml <https://www.npmjs.com/package/data2xml>`_ — Convert objects or JSON to XML
- `xmltojson <https://www.npmjs.com/package/xmltojson>`_ — Convert XML to objects or JSON
- `pretty-data <https://www.npmjs.com/package/pretty-data>`_ — Pretty-print or minify XML, JSON, CSS and SQL files
- `linq <https://www.npmjs.com/package/linq>`_ — Microsoft's Language Integrated Query (LINQ) for JavaScript
- `underscore <https://www.npmjs.com/package/underscore>`_ — Functional programming helper library
- `resemblejs <https://www.npmjs.com/package/resemblejs>`_ — Image analysis and comparison with HTML5
- `deep-diff <https://www.npmjs.com/package/deep-diff>`_ — Calculate object differences

For CasperJS and PhantomJS only
-------------------------------

These modules are only compatible with CasperJS or PhantomJS.

- `papaparse <https://www.npmjs.com/package/papaparse>`_ — Fast, in-browser CSV parser

For Node only
-------------

These modules are only compatible with Node.

- `needle <https://www.npmjs.com/package/needle>`_, `request <https://www.npmjs.com/package/request>`_ — HTTP client
- `mkdirp <https://www.npmjs.com/package/mkdirp>`_, `fs-extra <https://www.npmjs.com/package/fs-extra>`_ — File system utilities
- `wrench <https://www.npmjs.com/package/wrench>`_ — Recursive file system (and other) operations
- `through <https://www.npmjs.com/package/through>`_, `through2 <https://www.npmjs.com/package/through2>`_ — Simplified stream construction
- `aws-sdk <https://www.npmjs.com/package/aws-sdk>`_ — The official AWS SDK for JavaScript
- `tinderjs <https://www.npmjs.com/package/tinderjs>`_ — Programmatically access the Tinder dating service API
- `fast-csv <https://www.npmjs.com/package/fast-csv>`_ — CSV parser and writer
- `xml2js <https://www.npmjs.com/package/xml2js>`_, `xml2json <https://www.npmjs.com/package/xml2json>`_ — XML to JavaScript object converter
- `mongodb <https://www.npmjs.com/package/mongodb>`_, `mongoose <https://www.npmjs.com/package/mongoose>`_ — MongoDB

Other modules
-------------

These modules are also available and compatible with Node. However, we have not yet tested them for CasperJS or PhantomJS compatibility.

- `when <https://www.npmjs.com/package/when>`_ — Lightweight promises/A+ and when() implementation
- `q <https://www.npmjs.com/package/q>`_ — Library for promises (CommonJS/Promises/A,B,D)
- `jsdom <https://www.npmjs.com/package/jsdom>`_ — Implementation of the DOM and HTML standards
- `cheerio <https://www.npmjs.com/package/cheerio>`_ — Implementation of core jQuery for the server
- `lodash <https://www.npmjs.com/package/lodash>`_ — JavaScript utility library
- `qs <https://www.npmjs.com/package/qs>`_ — Querystring parser
- `is-my-json-valid <https://www.npmjs.com/package/is-my-json-valid>`_, `jsen <https://www.npmjs.com/package/jsen>`_ — JSON Schema validation

Injectable modules
------------------

These modules come preloaded on all agent disks. They can be injected in visited pages for easier DOM manipulation (mainly for scraping and automation purposes).

Use :ref:`nick.inject() <nick-inject>` or `page.injectJs() from PhantomJS <http://phantomjs.org/api/webpage/method/inject-js.html>`_ to add them in web pages.

- ``../injectables/jquery-2.2.3.min.js`` - `jQuery 2 <https://jquery.com/>`_ (84kB)
- ``../injectables/jquery-3.0.0.min.js`` - `jQuery 3 <https://jquery.com/>`_ (85kB)
- ``../injectables/underscore-1.8.3.min.js`` - `Underscore <http://underscorejs.org/>`_ (17kB)
- ``../injectables/lodash-core-4.13.1.min.js`` - `Lodash <https://lodash.com/>`_ (core build, 12kB)
- ``../injectables/lodash-full-4.13.1.min.js`` - `Lodash <https://lodash.com/>`_ (full build, 67kB)
