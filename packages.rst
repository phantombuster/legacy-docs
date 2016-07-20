.. _packages:

Packages
========

Phantombuster allows you to select between multiple versions of PhantomJS and CasperJS. Available modules are bundled together in what we call "packages". Each package is assigned a number that you can specify within your scripts.

Very often a script will start like this:

::

    'use strict';
    'phantombuster command: casperjs';
    'phantombuster package: 2'; // use the 2nd module package (PhantomJS 2.1.1)
    'phantombuster dependencies: lib-Nick-beta.coffee';

    buster = require('phantombuster').create();
    Nick = require('lib-Nick-beta')
    nick = new Nick({
        printNavigation: true,
        userAgent: 'Mozilla/5.0 (Windows NT 10.0; Win64; x64) AppleWebKit/537.36 (KHTML, like Gecko) Chrome/42.0.2311.135 Safari/537.36 Edge/12.246'
    });

The important line here is the third one. It sets up the script's environment with a specific package.

Package 2: PhantomJS 2.1.1
--------------------------

**This is the recommended package. Use it!** To do so, add

::

    'phantombuster package: 2';

at the beginning of your script.

This package was added July 18th 2016. It includes:

- **PhantomJS 2.1.1** (installed from `phantomjs-prebuilt@2.1.7 <https://www.npmjs.com/package/phantomjs-prebuilt>`_)
- **CasperJS 1.1.2** (installed from `casperjs@1.1.2 <https://www.npmjs.com/package/casperjs>`_)
- **Node 6.x** (installed from `NodeSource setup_6.x <https://github.com/nodesource/distributions>`_)

Exact versions of all bundled modules:

::

	async@2.0.0-rc.6
	aws-sdk@2.4.6
	axios@0.13.1
	bluebird@3.4.1
	babel-polyfill@6.9.1
	cheerio@0.20.0
	coffee-script@1.10.0
	data2xml@1.2.5
	deep-diff@0.3.4
	easyxml@2.0.1
	es6-promise@3.2.1
	fast-csv@2.0.1
	fs-extra@0.30.0
	is-my-json-valid@2.13.1
	jsdom@9.4.1
	jsen@0.6.1
	jsontoxml@0.0.11
	jstoxml@0.2.3
	linq@3.0.5
	lodash@4.13.1
	mime@1.3.4
	mkdirp@0.5.1
	moment@2.14.1
	mongodb-extended-json@1.6.3
	mongodb@2.2.1
	mongoose@4.5.3
	needle@1.0.0
	node-fetch@1.5.3
	once@1.3.3
	papaparse@4.1.2
	pluralize@3.0.0
	pretty-data2@0.40.1
	pretty-data@0.40.0
	q@1.4.1
	qs@6.2.0
	request@2.73.0
	resemblejs@2.2.1
	through2@2.0.1
	through@2.3.8
	underscore@1.8.3
	whatwg-fetch@1.0.0
	when@3.7.7
	xml2js@0.4.17
	xml2json@0.9.1
	xmltojson@1.1.0

Package 1: PhantomJS 1.9.8
--------------------------

**This is the default package.** Remove any ``phantombuster package`` directive from your script to use this package.

Alternatively, your could force the package to ``1`` by putting this at the beginning of your script:

::

    'phantombuster package: 1';

but it's not necessary.

**It's not recommended to use this package** because it comes with old versions of PhantomJS and CasperJS.

This is the original Phantombuster package. It includes:

- **PhantomJS 1.9.8** (installed from the **deprecated** `phantomjs@1.9.20 <https://www.npmjs.com/package/phantomjs>`_)
- **CasperJS 1.1.0-beta3** (installed from the **deprecated** `casperjs@1.1.0-beta3 <https://www.npmjs.com/package/casperjs>`_)
- **Node 6.x** (installed from `NodeSource setup_6.x <https://github.com/nodesource/distributions>`_)

Exact versions of all bundled modules:

::

	async@2.0.0-rc.4
	aws-sdk@2.3.9
	axios@0.13.1
	bluebird@3.4.1
	babel-polyfill@6.9.1
	cheerio@0.20.0
	coffee-script@1.10.0
	data2xml@1.2.5
	deep-diff@0.3.4
	easyxml@2.0.1
	es6-promise@3.2.1
	fast-csv@2.0.0
	fs-extra@0.30.0
	is-my-json-valid@2.13.1
	jsdom@9.3.0
	jsen@0.6.1
	jsontoxml@0.0.11
	jstoxml@0.2.3
	linq@3.0.5
	lodash@4.13.1
	mime@1.3.4
	mkdirp@0.5.1
	moment@2.14.1
	mongodb-extended-json@1.6.3
	mongodb@2.1.18
	mongoose@4.5.3
	needle@1.0.0
	node-fetch@1.5.3
	once@1.3.3
	papaparse@4.1.2
	pluralize@3.0.0
	pretty-data2@0.40.1
	pretty-data@0.40.0
	q@1.4.1
	qs@6.2.0
	request@2.72.0
	resemblejs@2.2.0
	through2@2.0.1
	through@2.3.8
	underscore@1.8.3
	whatwg-fetch@1.0.0
	when@3.7.7
	xml2js@0.4.16
	xml2json@0.9.1
	xmltojson@1.1.0
