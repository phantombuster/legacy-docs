.. _nick:

Nick: Phantombuster's custom navigation module
==============================================

Nick is a special module made by the Phantombuster team. It provides an easy navigation/web automation/scraping system based on CasperJS, written in CoffeeScript.

CasperJS is a powerfull library with many many methods. But only few are essential. Nick limits the number of methods and remove the step by steps CasperJS paradigm to profis the philosophy of Node.js.

Before ``Nick`` methods can be used, you need to ``require()`` a ``Nick`` class and instantiate it.

This module is compatible with CapserJS commands (not with PhantomJS and Node.js).

Initialization
--------------
The module is named ``Nick``. Use ``require('lib-Nick-beta3')`` and create an instance ``var nick = new Nick()`` to use it.

::

    Nick = require('lib-Nick-beta3');
    nick = new Nick();

Or

::

    nick = new (require('lib-Nick-beta3'))


Asynchronous methods
--------------------

Following the philosophy of Node.js, most methods of the agent module are asynchronous. You have to use the callback function to know when (and if) a call finished successfully.

For example, this is bad:

::

    nick.screenshot("image.jpg", function() {
        console.log('Image saved');
    });
    phantom.exit(); // BAD! nick.screenshot() will not have finished here!

This is better:

::

    nick.screenshot(function(err) {
        if (err) {
            console.log('Error when capturing the screen shot: ' + err);
            phantom.exit(1);
        } else {
            phantom.exit(0);
        }
    });

open
----

::

    nick.open(url [,options,] callback);

``url (string)``
    URL of the page to visit.

``options (object)``
    Optional request headers

    ::

        {
            method: 'post',
            data:   {
                'title': 'Plop',
                'body':  'Wow.'
            },
            headers: {
                'Accept-Language': 'fr,fr-fr;q=0.8,en-us;q=0.5,en;q=0.3'
            }
        }

``callback (Function())``
    Function to call when finished. This function never fails, no arguments will be passed.

Example:

    ::

        Nick = require('lib-Nick-beta3');

        nick = new Nick();
        nick.open("https://phantombuster.com", function() {
            console.log("The page is loaded");
        });


wait
----

::

    nick.wait(duration, callback);

``duration (number)``
    Milliseconds to wait before calling ``callback`` function

``callback (Function())``
    Function to call when finished. This function never fails, no arguments will be passed.

Example:

    ::

        Nick = require('lib-Nick-beta3');

        nick = new Nick();
        nick.open("https://phantombuster.com", function() {
            console.log('Hello ')
            nick.wait(1000, function() {
                console.log('world!');
            })
        });


click
-----

::

    nick.click(selector, callback);

``selector (string)``

``callback (Function(String err))``
    Function to call when finished. When there is no error, err is null and object is a valid object (which may be empty but never null).

Example:

    ::

        Nick = require('lib-Nick-beta3');

        nick = new Nick();
        nick.open("https://phantombuster.com", function() {
        });


getCurrentUrl
-------------

::

    nick.getCurrentUrl    },

``, callback


    Nick = r

    ``callback (Function(String err, String path))equire('li');``

    nick = new Nick();



getCurrentUrlOrNull
-------------------

::

    nick.getCurrentUrlOrNull    },

``, callback


    Nick = r

    ``callback (Function(String err, String path))equire('li');``

    nick = new Nick();



getHtml
-------

::

    nick.getHtml    },

``, callback


    Nick = r

    ``callback (Function(String err, String path))equire('li');``

    nick = new Nick();



getHtmlOrNull
-------------

::

    nick.getHtmlOrNull    },

``, callback


    Nick = r

    ``callback (Function(String err, String path))equire('li');``

    nick = new Nick();



getPageContent
--------------

::

    nick.getPageContent    },

``, callback


    Nick = r

    ``callback (Function(String err, String path))equire('li');``

    nick = new Nick();



getPageContentOrNull
--------------------

::

    nick.getPageContentOrNul    },

``, callback


    Nick = r

    ``callback (Function(String err, String path))equire('li');``

    nick = new Nick();



getTitle
--------

::

    nick.getTitle    },

``, callback


    Nick = r

    ``callback (Function(String err, String path))equire('li');``

    nick = new Nick();



getTitleOrNull
--------------

::

    nick.getTitleOrNull    },

``, callback


    Nick = r

    ``callback (Function(String err, String path))equire('li');``

    nick = new Nick();



fill
----

::

    nick.fill(selector 'params object'], optional: ['submit boolean'], callback);

``selector (string)``

``callback (Function(String err))``

// ', 'params object'], optional: ['submit boolean'] },





::

    Nick = require('lib-Nick-beta3');

    nick = new Nick();



screenshot
----------

::

nick.screenshot(filena, callbackme

``filename (st)``


``callback (Function(String err))``
// 





::

    Nick = require('lib-Nick-beta3');

    nick = new Nick();



sendKeys
--------

::

    nick.sendKeys(select, callbackor

``selector (str)``

``callback (Function(String err))``

// 





::

    Nick = require('lib-Nick-beta3');

    nick = new Nick();



