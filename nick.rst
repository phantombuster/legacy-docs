.. _nick:

Nick
====

Nick is a special module made by the Phantombuster team. It provides an easy navigation/web automation/scraping system. It's based on CasperJS and is written in CoffeeScript.

CasperJS is a powerful library with many methods. But only a few are essential. Nick limits the number of methods and replaces the step-by-step CasperJS paradigm with the async/callback paradigm of Node.

Before ``Nick`` methods can be used, you need to ``require()`` a ``Nick`` class and instantiate it.

Your agents must be launched with the CasperJS command when using this module. Nick is not compatible with Node or PhantomJS.

Initialization
--------------

The module is named ``Nick``. Use ``require('lib-Nick-beta')`` and create an instance to use it. A typical Nick-based script starts like this:

::

    'use strict';
    'phantombuster command: casperjs';
    'phantombuster package: 2';
    'phantombuster dependencies: lib-Nick-beta.coffee'

    var Nick = require('lib-Nick-beta');
    var nick = new Nick({
        printNavigation: true
    });

An instance of Nick is similar to a browser tab. If you want to open multiple pages/tabs simultaneously, instantiate multiple Nicks.

Asynchronous methods
--------------------

Following the philosophy of Node, most of Nick's methods are asynchronous. You have to use the callback function to know when (and if) a call finished successfully.

For example, this is bad:

::

    nick.screenshot('image.jpg', function() {
        console.log('Screenshot taken');
    });
    nick.exit(); // BAD! nick.screenshot() will not have finished here!

This is better:

::

    nick.screenshot('image.jpg', function(err) {
        if (err) {
            console.log('Error when capturing the screenshot: ' + err);
            nick.exit(1);
        } else {
            nick.exit(0);
        }
    });

Warning: no parallelism
-----------------------

Options
-------

open()
------

::

    nick.open(url [, options], callback);

Opens the webpage at ``url``. You can forge GET, POST, PUT, DELETE and HEAD requests.

This method is asynchronous and returns nothing. Use the ``callback`` to know when it has finished.

More info: http://docs.casperjs.org/en/latest/modules/casper.html#open

``url`` (``String``)
    URL of the page to visit.

``options`` (``PlainObject``)
    Optional request headers.

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

``callback`` (``Function()``)
    Function called when finished. No arguments are returned. To know if the page opened successfully, use `waitUntilVisible()`_ or similar.

Example:

    ::

        nick.open("https://phantombuster.com/cloud-services", function() {
            console.log("The page is loaded (or not!) — use waitUntilVisible() or similar to be sure");
            nick.exit();
        });

wait()
------

::

    nick.wait(duration, callback);

Waits for a ``duration`` time before calling ``callback``.

This method is asynchronous and returns nothing. Use the ``callback`` to know when it has finished.

More info: http://docs.casperjs.org/en/latest/modules/casper.html#wait

``duration`` (``Number``)
    Milliseconds to wait before calling ``callback`` function.

``callback`` (``Function()``)
    Function called when finished. This function never fails, no arguments will be passed.

Example:

    ::

        nick.open("https://phantombuster.com/cloud-services", function() {
            console.log('Hello')
            nick.wait(1000, function() {
                console.log('world!');
                nick.exit();
            })
        });

waitUntilPresent()
------------------

::

    nick.waitUntilPresent(selectors, timeout [, condition = "and"], callback);

Waits until a DOM element, matching the provided selector, is present. If the method has to wait more than ``timeout`` milliseconds, ``callback`` is called with a ``"timeout"`` error. By default, ``condition`` is set to ``"and"``.

It is considered good practice to always use a ``wait*()`` method after a page load and before any action on selectors.

This method is asynchronous and returns nothing. Use the ``callback`` to know when it has finished.

More info: http://docs.casperjs.org/en/latest/modules/casper.html#waitforselector

``selectors`` (``Array or String``)
    An array of CSS3 selectors describing the path to DOM elements.

``timeout`` (``Number``)
    Milliseconds to wait before calling ``callback`` function with an error.

``condition`` (``String``)
    When ``selectors`` is an array, this argument lets you choose how to wait for the elements. If ``condition`` is ``"and"``, the method will wait for the presence of all ``selectors`` in the DOM. Otherwise if ``condition`` is ``"or"``, the method will wait until any ``selector`` of the array is present in the DOM.

``callback`` (``Function(String err, String sel)``)
    Function called when finished. When there is no error, ``err`` is null.

    - In case of success (``err`` is *null*):
        - if ``condition`` is ``"and"`` then ``sel`` is *null* because all selectors are present
        - if ``condition`` is ``"or"`` then ``sel`` is one of the present selectors of the given array

    - In case of failure (``err`` is ``"timeout"``)
        - if ``condition`` is ``"and"`` then ``sel`` is one of the absent selectors of the given array
        - if ``condition`` is ``"or"`` then ``sel`` is *null* because no selectors were found

Example with selector argument as a string:

    ::

        nick.open("https://phantombuster.com/cloud-services", function() {
            nick.waitUntilPresent('html', 2000, function(err) {
                if (err) {
                    console.log(err);
                    nick.exit(1);
                }
                console.log("'html' selector is present");
                nick.exit(0);
            });
        });

This example succeeds if all selectors are present in the DOM:

    ::

        nick.open("https://phantombuster.com/cloud-services", function() {
            nick.waitUntilPresent(['p', 'span', 'h2.title'], 2000, 'and', function(err, selector) {
                if (err) {
                    console.log(err);
                    console.log("One of the missing selectors is:", selector);
                    nick.exit(1);
                }
                console.log("'html', 'foo', 'bar' selectors are present");
                nick.exit(0);
            });
        });

This example succeeds if one or more selector is present in the DOM:

    ::

        nick.open("https://phantombuster.com/cloud-services", function() {
            nick.waitUntilPresent(['p', 'span', 'h2.title'], 2000, 'or', function(err, selector) {
                if (err) {
                    console.log(err);
                    console.log("'p', 'span', 'h2.title' selectors are missing");
                    nick.exit(1);
                }
                console.log("First matching selector:", selector);
                nick.exit(0);
            });
        });

waitWhilePresent()
------------------

::

    nick.waitWhilePresent(selectors, timeout [, condition = "and"], callback);

Waits while a DOM element, matching the provided selector, is present. If the method has to wait more than ``timeout`` milliseconds, ``callback`` is called with a ``"timeout"`` error. By default, ``condition`` is set to ``"and"``.

It is considered good practice to always use a ``wait*()`` method after a page load and before any action on selectors.

This method is asynchronous and returns nothing. Use the ``callback`` to know when it has finished.

More info: http://docs.casperjs.org/en/latest/modules/casper.html#waitwhileselector

``selectors`` (``Array or String``)
    An array of CSS3 selectors describing the path to DOM elements.

``timeout`` (``Number``)
    Milliseconds to wait before calling ``callback`` function with an error.

``condition`` (``String``)
    When ``selectors`` is an array, this argument lets you choose how to wait for the elements. If ``condition`` is ``"and"``, the method will wait for the presence of all ``selectors`` in the DOM. Otherwise if ``condition`` is ``"or"``, the method will wait until any ``selector`` of the array is present in the DOM.

``callback`` (``Function(String err, String sel)``)
    Function called when finished. When there is no error, ``err`` is null.

    - In case of success (``err`` is *null*):
        - if ``condition`` is ``"and"`` then ``sel`` is *null* because all selectors are present
        - if ``condition`` is ``"or"`` then ``sel`` is one of the present selectors of the given array

    - In case of failure (``err`` is ``"timeout"``)
        - if ``condition`` is ``"and"`` then ``sel`` is one of the absent selectors of the given array
        - if ``condition`` is ``"or"`` then ``sel`` is *null* because no selectors were found

Example with selector argument as a string:

    ::

        nick.open("https://phantombuster.com/cloud-services", function() {
            nick.waitWhilePresent('html', 2000, function(err) {
                if (err) {
                    console.log(err);
                    nick.exit(1);
                }
                console.log("'html' selector is not present anymore");
                nick.exit(0);
            });
        });

This example succeeds if all selectors is not present in the DOM:

    ::

        nick.open("https://phantombuster.com/cloud-services", function() {
            nick.waitWhilePresent(['p', 'span', 'h2.title'], 2000, 'and', function(err, selector) {
                if (err) {
                    console.log(err);
                    console.log("One of the missing selectors is:", selector);
                    nick.exit(1);
                }
                console.log("'html', 'foo', 'bar' selectors are present");
                nick.exit(0);
            });
        });

This example succeeds if one or more selector is not present in the DOM:

    ::

        nick.open("https://phantombuster.com/cloud-services", function() {
            nick.waitWhilePresent(['p', 'span', 'h2.title'], 2000, 'or', function(err, selector) {
                if (err) {
                    console.log(err);
                    console.log("'p', 'span', 'h2.title' selectors are missing");
                    nick.exit(1);
                }
                console.log("First matching selector:", selector);
                nick.exit(0);
            });
        });


waitUntilVisible()
------------------

::

    nick.waitUntilVisible(selectors, timeout [, condition = "and"], callback);

Waits until a DOM element, matching the provided selector, is visible. If the method has to wait more than ``timeout`` milliseconds, ``callback`` is called with a ``"timeout"`` error. By default, ``condition`` is set to ``"and"``.

It is considered good practice to always use a ``wait*()`` method after a page load and before any action on selectors.

This method is asynchronous and returns nothing. Use the ``callback`` to know when it has finished.

More info: http://docs.casperjs.org/en/latest/modules/casper.html#waituntilvisible

``selectors`` (``Array or String``)
    An array of CSS3 selectors describing the path to DOM elements.

``timeout`` (``Number``)
    Milliseconds to wait before calling ``callback`` function with an error.

``condition`` (``String``)
    When ``selectors`` is an array, this argument lets you choose how to wait for the elements. If ``condition`` is ``"and"``, the method will wait for the presence of all ``selectors`` in the DOM. Otherwise if ``condition`` is ``"or"``, the method will wait until any ``selector`` of the array is present in the DOM.

``callback`` (``Function(String err, String sel)``)
    Function called when finished. When there is no error, ``err`` is null.

    - In case of success (``err`` is *null*):
        - if ``condition`` is ``"and"`` then ``sel`` is *null* because all selectors are present
        - if ``condition`` is ``"or"`` then ``sel`` is one of the present selectors of the given array

    - In case of failure (``err`` is ``"timeout"``)
        - if ``condition`` is ``"and"`` then ``sel`` is one of the absent selectors of the given array
        - if ``condition`` is ``"or"`` then ``sel`` is *null* because no selectors were found

Example with selector argument as a string:

    ::

        nick.open("https://phantombuster.com/cloud-services", function() {
            nick.waitUntilVisible('html', 2000, function(err) {
                if (err) {
                    console.log(err);
                    nick.exit(1);
                }
                console.log("'html' selector is not present anymore");
                nick.exit(0);
            });
        });

This example succeeds if all selectors is visible in the DOM:

    ::

        nick.open("https://phantombuster.com/cloud-services", function() {
            nick.waitUntilVisible(['p', 'span', 'h2.title'], 2000, 'and', function(err, selector) {
                if (err) {
                    console.log(err);
                    console.log("One of the missing selectors is:", selector);
                    nick.exit(1);
                }
                console.log("'html', 'foo', 'bar' selectors are present");
                nick.exit(0);
            });
        });

This example succeeds if one or more selector is visible in the DOM:

    ::

        nick.open("https://phantombuster.com/cloud-services", function() {
            nick.waitUntilVisible(['p', 'span', 'h2.title'], 2000, 'or', function(err, selector) {
                if (err) {
                    console.log(err);
                    console.log("'p', 'span', 'h2.title' selectors are missing");
                    nick.exit(1);
                }
                console.log("First matching selector:", selector);
                nick.exit(0);
            });
        });

waitWhileVisible()
------------------

::

    nick.waitWhileVisible(selectors, timeout [, condition = "and"], callback);

Waits while a DOM element, matching the provided selector, is visible. If the method has to wait more than ``timeout`` milliseconds, ``callback`` is called with a ``"timeout"`` error. By default, ``condition`` is set to ``"and"``.

It is considered good practice to always use a ``wait*()`` method after a page load and before any action on selectors.

This method is asynchronous and returns nothing. Use the ``callback`` to know when it has finished.

More info: http://docs.casperjs.org/en/latest/modules/casper.html#waitwhilevisible

``selectors`` (``Array or String``)
    An array of CSS3 selectors describing the path to DOM elements.

``timeout`` (``Number``)
    Milliseconds to wait before calling ``callback`` function with an error.

``condition`` (``String``)
    When ``selectors`` is an array, this argument lets you choose how to wait for the elements. If ``condition`` is ``"and"``, the method will wait for the presence of all ``selectors`` in the DOM. Otherwise if ``condition`` is ``"or"``, the method will wait until any ``selector`` of the array is present in the DOM.

``callback`` (``Function(String err, String sel)``)
    Function called when finished. When there is no error, ``err`` is null.

    - In case of success (``err`` is *null*):
        - if ``condition`` is ``"and"`` then ``sel`` is *null* because all selectors are present
        - if ``condition`` is ``"or"`` then ``sel`` is one of the present selectors of the given array

    - In case of failure (``err`` is ``"timeout"``)
        - if ``condition`` is ``"and"`` then ``sel`` is one of the absent selectors of the given array
        - if ``condition`` is ``"or"`` then ``sel`` is *null* because no selectors were found

Example with selector argument as a string:

    ::

        nick.open("https://phantombuster.com/cloud-services", function() {
            nick.waitWhileVisible('html', 2000, function(err) {
                if (err) {
                    console.log(err);
                    nick.exit(1);
                }
                console.log("'html' selector is not present anymore");
                nick.exit(0);
            });
        });

This example succeeds if all selectors are not visible:

    ::

        nick.open("https://phantombuster.com/cloud-services", function() {
            nick.waitWhileVisible(['p', 'span', 'h2.title'], 2000, 'and', function(err, selector) {
                if (err) {
                    console.log(err);
                    console.log("One of the missing selectors is:", selector);
                    nick.exit(1);
                }
                console.log("'html', 'foo', 'bar' selectors are present");
                nick.exit(0);
            });
        });

This example succeeds if one or more selector is not visible:

    ::

        nick.open("https://phantombuster.com/cloud-services", function() {
            nick.waitWhileVisible(['p', 'span', 'h2.title'], 2000, 'or', function(err, selector) {
                if (err) {
                    console.log(err);
                    console.log("'p', 'span', 'h2.title' selectors are missing");
                    nick.exit(1);
                }
                console.log("First matching selector:", selector);
                nick.exit(0);
            });
        });

end()
-----

::

        Exit the process.

exit()
------

::

    Exit the process.

evaluate()
----------

    ::

        nick.evaluate(sandboxedFunction [, argumentObject], callback);

Evaluates the function in the current page DOM context. The execution is sandboxed, the web page has no access to the Nick context. Data can be given through ``argumentObject``.

This method is asynchronous and returns nothing. Use the ``callback`` to know when it has finished.

More info: http://docs.casperjs.org/en/latest/modules/casper.html#evaluate

``sandboxedFunction`` (``Function([Object argumentObject])``)
    The function evaluated in the DOM context. ``argumentObject`` is a copy of the object given in the second optional argument.

``argumentObject`` (``PlainObject``)
    Object to copy to the DOM context and given to the ``sandboxedFunction`` optional argument.

``callback`` (``Function(String err[, Object ret])``)
    Function called when finished. When there is no error, ``err`` is null and ``ret`` is a copy of the object returned by sandboxedFunction call in DOM context.

Example:

    ::

        var num = 21;

        nick.evaluate(function(arg) {
            return arg.n * 2;
        }, {
            'n': num
        }, function(err, ret) {
            if (err) {
                console.log(err);
                nick.exit(1);
            }
            console.log("Evaluation succeeded. Return value is", ret); // "Evaluation succeeded. Return value is 42"
            nick.exit(0);
        });

evaluateAsync()
---------------

    ::

        nick.evaluateAsync(sandboxedFunction [, argumentObject], callback);

Evaluates the function in the current page DOM context. The execution is sandboxed and asynchronous, the web page has no access to the Nick context. Data can be given through ``argumentObject``. Because ``sandboxedFunction`` is asynchronous the function ``done`` must be called.

This method is asynchronous and returns nothing. Use the ``callback`` to know when it has finished.

More info: http://docs.casperjs.org/en/latest/modules/casper.html#evaluate

``sandboxedFunction`` (``Function([Object argumentObject], done)``)
    The function evaluated in the DOM context. ``argumentObject`` is a copy of the object given in the second optional argument. ``done`` must be called before the function ends with the same arguments as ``callback``.

``argumentObject`` (``PlainObject``)
    Object to copy to the DOM context and given to the ``sandboxedFunction`` optional argument.

``callback`` (``Function(String err[, Object ret])``)
    Function called when finished. When there is no error, ``err`` is null and ``ret`` is a copy of the object returned by sandboxedFunction call in DOM context.

Example:

    ::

        var num = 21;

        nick.evaluateAsync(function(arg, done) {
            return done(null, arg.n * 2;)
        }, {
            'n': num
        }, function(err, ret) {
            if (err) {
                console.log(err);
                nick.exit(1);
            }
            console.log("Evaluation succeeded. Return value is", ret); // "Evaluation succeeded. Return value is 42"
            nick.exit(0);
        });

.. _nick-inject:

inject()
--------

    ::

        nick.inject(url, callback);

Inject a script in the current DOM page context. The script can be hosted locally on the agent's disk or on a remote server.

This method is asynchronous and returns nothing. Use the ``callback`` to know when it has finished.

``url`` (``object``)
    Path to a script hosted locally or remotely.

``callback`` (``Function(String err)``)
    Function called when finished. When there is no error, ``err`` is null.

Example:

    ::

        nick.inject("https://code.jquery.com/jquery-2.1.4.min.js", function(err) {
            if (err) {
                console.log(err);
                nick.exit(1);
            }
            console.log("Jquery script inserted!");
            nick.exit(0);
        });

click()
-------

::

    nick.click(selector, callback);

Performs a click on the element matching the provided ``selector`` expression.

This method is asynchronous and returns nothing. Use the ``callback`` to know when it has finished.

More info: http://docs.casperjs.org/en/latest/modules/casper.html#click

``selector`` (``string``)
    A CSS3 or XPath expression that describe the path to DOM elements.

``callback`` (``Function(String err)``)
    Function called when finished. When there is no error, ``err`` is *null* and object is a valid object (which may be empty but never null).

Example:

    ::

        var selector = "a.btn-warning";

        nick.open("https://phantombuster.com/cloud-services", function() {
            nick.waitUntilVisible(selector, 2000, function(err) {
                if (err) {
                    console.log(err)
                    nick.exit(1);
                }
                nick.click(selector, function(err) {
                    if (err) {
                        console.log(err)
                        nick.exit(1);
                    }
                    console.log("Click on 'TRY FREE' button done.");
                    nick.exit(0);
                });
            });
        });

getCurrentUrl()
---------------

    ::

        nick.getCurrentUrl(callback)

Retrieves current page URL and calls the ``callback`` function with the URL in second argument. Note that the url will be url-decoded.

This method is asynchronous and returns nothing. Use the ``callback`` to know when it has finished.

More info: http://docs.casperjs.org/en/latest/modules/casper.html#getcurrenturl

``callback`` (``Function(String err, String decodedUrl)``)
    Function called when finished. When there is no error, ``err`` is *null* and ``decodedUrl`` is a url-decoded string.

Example:

    ::

        nick.open("https://phantombuster.com/cloud-services", function() {
            nick.getCurrentUrl(function(err, url) {
                if (err) {
                    console.log(err);
                    nick.exit(1);
                }
                console.log("Current Url: ", url);
                nick.exit(0);
            });
        });

getCurrentUrlOrNull()
---------------------

::

    nick.getCurrentUrlOrNull()

This method is synchronous and returns *null* if it fails otherwise it returns a the current URL as a string. Note that the url will be url-decoded.

More info: http://docs.casperjs.org/en/latest/modules/casper.html#getcurrenturl

This function takes no arguments.

Example:

    ::

        nick.open("https://phantombuster.com/cloud-services", function() {
            console.log(nick.getCurrentUrlOrNull());
            nick.exit();
        });

getHtml()
---------

::

    nick.getHtml(callback)

Retrieves current page HTML and calls the ``callback`` function with the HTML as a string in second argument.

This method is asynchronous and returns nothing. Use the ``callback`` to know when it has finished.

More info: http://docs.casperjs.org/en/latest/modules/casper.html#gethtml

``callback`` (``Function(String err, String html)``)
    Function called when finished. When there is no error, ``err`` is *null* and ``html`` is the HTML string.

Example:

    ::

        nick.open("https://phantombuster.com/cloud-services", function() {
            nick.getHtml(function(err, html) {
                if (err) {
                    console.log(err);
                    nick.exit(1);
                }
                console.log("HTML: ", html);
                nick.exit(0);
            });
        });

getHtmlOrNull()
---------------

::

    nick.getHtmlOrNull()

This method is synchronous and returns *null* if it fails otherwise it returns the page HTML as a string.

More info: http://docs.casperjs.org/en/latest/modules/casper.html#gethtml

This function takes no arguments.

Example:

    ::

        nick.open("https://phantombuster.com/cloud-services", function() {
            console.log(nick.getHtmlOrNull());
            nick.exit();
        });

getContent()
------------

::

    nick.getContent(callback)

Retrieves current page content and call the ``callback`` function with the page content as a string in the second argument.

This method is asynchronous and returns nothing. Use the ``callback`` to know when it has finished.

More info: http://docs.casperjs.org/en/latest/modules/casper.html#getpagecontent

``callback`` (``Function(String err, String html)``)
    Function called when finished. When there is no error, ``err`` is *null* and ``html`` is the HTML string.

Example:

    ::

        nick.open("https://phantombuster.com/cloud-services", function() {
            nick.getPageContent(function(err, content) {
                if (err) {
                    console.log(err);
                    nick.exit(1);
                }
                console.log("Page content: ", content);
                nick.exit(0);
            });
        });

getContentOrNull()
------------------

::

    nick.getContentOrNull()

This method is synchronous and returns *null* if it fails otherwise it returns the page content (string).

More info: http://docs.casperjs.org/en/latest/modules/casper.html#getpagecontent

This function takes no arguments.

Example:

    ::

        nick.open("https://phantombuster.com/cloud-services", function() {
            var content = nick.getPageContentOrNull();

            if (content == null) {
                console.log("content is null");
                nick.exit(1);
            }
            console.log("Content: ", content);
            nick.exit(0);
        });

getTitle()
----------

::

    nick.getTitle(callback)

Retrieves current page title and call the ``callback`` function with the title in second argument.

This method is asynchronous and returns nothing. Use the ``callback`` to know when it has finished.

More info: http://docs.casperjs.org/en/latest/modules/casper.html#gettitle

``callback`` (``Function(String err, String title)``)
    Function called when finished. When there is no error, ``err`` is *null* and ``title`` is the current page title string.

Example:

    ::

        nick.open("https://phantombuster.com/cloud-services", function() {
            nick.getTitle(function(err, title) {
                if (err) {
                    console.log(err);
                    nick.exit(1);
                }
                console.log("Page title: ", title);
                nick.exit(0);
            });
        });


getTitleOrNull()
----------------

::

    nick.getTitleOrNull()

This method is synchronous and returns *null* if it fails otherwise it returns a the current page title string.

More info: http://docs.casperjs.org/en/latest/modules/casper.html#gettitle

This function takes no arguments.

Example:

    ::

        nick.open("https://phantombuster.com/cloud-services", function() {
            var title = nick.getTitleOrNull();

            if (title == null) {
                console.log("title is null");
                nick.exit(1);
            }
            console.log("Title: ", title);
            nick.exit(0);
        });

fill()
------

::

    nick.fill(selector, inputs [, submit], callback);

Fills form inputs with the given values and optionally submits it. Inputs are referenced by their name attribute.

This method is asynchronous and returns nothing. Use the ``callback`` to know when it has finished.

More info: http://docs.casperjs.org/en/latest/modules/casper.html#gettitle

``selector`` (``String``)
    A CSS3 or XPath expression that describe the path to DOM elements.

``inputs`` (``PlainObject``)
    An object composed by name:value, with name, the input name and value, the value to set.

``submit`` (``Boolean``)
    If ``true`` the form will be automatically sent.

``callback`` (``Function(String err)``)
    Function called when finished. When there is no error, ``err`` is *null*.


Example with simple HTML form:

    ::

        <form action="/contact" id="contact-form" enctype="multipart/form-data">
            <input type="text" name="subject"/>
            <textearea name="content"></textearea>
            <input type="radio" name="civility" value="Mr"/> Mr
            <input type="radio" name="civility" value="Mrs"/> Mrs
            <input type="text" name="name"/>
            <input type="email" name="email"/>
            <input type="file" name="attachment"/>
            <input type="checkbox" name="cc"/> Receive a copy
            <input type="submit"/>
        </form>

A Nick script filling the form and sending it:

    ::

        nick.open("https://example.com", function() {
            nick.fill('form#contact-form', {
                'subject': 'I am watching you',
                'content': 'So be careful.',
                'civility': 'Mr',
                'name': 'Chuck Norris',
                'email': 'chuck@norris.com',
                'cc': true,
                'attachment': '/Users/chuck/roundhousekick.doc'
            }, true, function(err) {
                if (err) {
                    console.log(err);
                    nick.exit(1);
                }
                console.log("Form sent!");
                nick.exit(0);
            });
        });


screenshot()
------------

    ::

        nick.screenshot(filename [, clipRect, imgOptions], callback)

Take a screenshot of the current page. Without optional arguments, this method take a screenshot of the entire page.

This method is asynchronous and returns nothing. Use the ``callback`` to know when it has finished.

More info: http://docs.casperjs.org/en/latest/modules/casper.html#capture

``path`` (``String``)
    The path of the screenshot. The format is defined by the file extention. 'image.jpg' will create a JPEG image in the current folder.

``clipRect`` (``PlainObject``)
    This optional argument set the position and the size of the screenshot square.

    Example:

    ::

        clipRect = {
            top: 100,
            left: 100,
            width: 500,
            height: 400
        }

``imgOptions`` (``PlainObject``)
    This optional argument set the two avalaible image options. Such as the format and the quality of the screenshot image.

    Example:

    ::

        imgOptions = {
            format: 'jpg',
            quality: 50
        }

``callback`` (``Function(String err)``)
    Function called when finished. When there is no error, ``err`` is *null*.

Example:

    ::

        nick.open("https://phantombuster.com/cloud-services", function() {
            nick.screenshot('./image.jpg', function(err) {
                if (err) {
                    console.log(err);
                    nick.exit(1);
                }
                console.log("Screenshot saved!")
                nick.exit(0);
            });
        });


Example with options:

    ::

        var buster = require('phantombuster').create()

        nick.open("https://phantombuster.com/cloud-services", function() {
            nick.screenshot('./image.jpg'
            , {
                top: 90,
                left: 190,
                width: 900,
                height: 360
            }
            , {
                format: 'png',
                quality: 100
            }
            , function(err) {
                if (err) {
                    console.log(err);
                    nick.exit(1);
                }
                console.log("Screenshot saved!")
                buster.saveFolder(function(err) {
                    if (err) {
                        console.log(err);
                        nick.exit(1);
                    }
                    nick.exit(0);
                });
            });
        });

selectorScreenshot()
--------------------

sendKeys()
----------

::

    nick.sendKeys(selector, keys [, options], callback)

Write keys in an ``<input>``, ``<textarea>`` or any DOM element with ``contenteditable="true"`` in the current page.

This method is asynchronous and returns nothing. Use the ``callback`` to know when it has finished.

More info: http://docs.casperjs.org/en/latest/modules/casper.html#sendkeys

``selector`` (``String``)
    A CSS3 or XPath expression that describes the path to DOM elements.

``keys`` (``String``)
    Keys to send to the editable DOM element.

``options`` (``PlainObject``)
    The three options avalable are:
        - ``reset`` (``Boolean``): remove the content of the targeted element before sending key presses.
        - ``keepFocus`` (``Boolean``): keep the focus in the editable DOM element after keys have been sent.
        - ``modifiers`` (``PlainObject``): modifier string concatenated with a ``+`` (available modifiers are ``ctrl``, ``alt``, ``shift``, ``meta`` and ``keypad``).

``callback`` (``Function(String err)``)
    Function called when finished. When there is no error, ``err`` is *null*.

Example:

    ::

        nick.open("https://phantombuster.com/cloud-services", function() {
            nick.sendKeys('#message', "Boo!", function(err) {
                if (err) {
                    console.log(err);
                    nick.exit(1);
                }
                console.log("Keys sent!")
                nick.exit(0);
            });
        });


Example with optional argument:

    ::

        nick.open("https://phantombuster.com/cloud-services", function() {
            nick.sendKeys('#message', "s", {
                reset: false,
                keepFocus: true,
                modifiers: "ctrl+alt+shift"
            }, function(err) {
                if (err) {
                    console.log(err);
                    nick.exit(1);
                }
                console.log("Keys sent!")
                nick.exit(0);
            });
        });
