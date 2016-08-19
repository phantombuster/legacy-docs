.. _agent-module:

Agent Module
============

The Agent Module (also called ``buster``) is a special module made by the Phantombuster team. It provides some cool features and is linked to your Phantombuster account.

Here's a short list of what you can do with our module:

- Save files to your persistent storage
- Download files to your agent's disk
- Indicate the progress of your agent
- Set or reset the time limit of your agent
- Send emails
- Interact with MongoDB databases
- Solve CAPTCHAS
- ...

This module is compatible with all the Phantombuster commands (CapserJS, PhantomJS and Node).

Initialization
--------------

The agent module is named ``phantombuster``. Use ``require('phantombuster')`` and call ``create()`` to instantiate it.

- When using Nick, Node or PhantomJS:

    ::

        buster = require('phantombuster').create();

- When using the agent module in conjunction with CasperJS, you **must** pass the CasperJS instance to ``create()``. A typical CasperJS script starts like this:

    ::

        casper = require('casper').create();
        buster = require('phantombuster').create(casper);

Asynchronous methods
--------------------

Following the philosophy of Node, most methods of the agent module are asynchronous. You have to use the callback function to know when (and if) a call finished successfully.

For example, this is bad:

::

    buster.saveFolder(function() {
        console.log('Folder saved');
    });
    phantom.exit(); // BAD! saveFolder() will not have finished here, in fact it will not even have started!

This is better:

::

    buster.saveFolder(function(err) {
        if (err) {
            console.log('Error when saving folder: ' + err);
            phantom.exit(1);
        } else {
            phantom.exit(0);
        }
    });

CasperJS step control
---------------------

If the agent module is instantiated with a CasperJS instance passed in ``create()``, its methods will block the current navigation step for your convenience. For example:

::

    casper = require('casper').create();
    buster = require('phantombuster').create(casper); // pass the CasperJS instance

    casper.start('https://example.com', function() {

        // methods ask CasperJS to not go to the next step until they are finished
        buster.saveText('foo bar baz', 'foo.txt', function() {
            console.log('Text saved!');
        });

        console.log('Navigation step 1');
    });

    // steps will wait for the end of all the previous agent module calls
    casper.then(function() {
        console.log('This is executed a few ticks after saveText()');
    });

    casper.run(function() {
        console.log('And this is executed last');
        casper.exit()
    });

This script will output:

.. code-block:: text

    Navigation step 1
    Text saved!
    This is executed a few ticks after saveText()
    And this is executed last

Internally, the agent module makes calls to `blockSteps()`_ and `unblockSteps()`_. You can also use these methods for stopping the execution of steps when calling your own asynchronous functions or when using ``async`` for example.

agentId
-------

::

    buster.agentId

Contains the ID of the currently running agent as a ``Number``. This is useful for making requests to the Phantombuster API from within the agent.

apiKey
------

::

    buster.apiKey

Contains your Phantombuster API key as a ``String``. This is useful for making requests to the Phantombuster API from within the agent.

argument
--------

::

    buster.argument

Contains the agent's argument as a ``PlainObject``. On Phantombuster, each agent receives a JSON object as argument, which can be set each time they are launched.

blockSteps()
------------

::

    buster.blockSteps()

**CasperJS only.**

Stops the execution of CasperJS steps until `unblockSteps()`_ is called (behind the scenes, it uses the same system as ``casper.wait()``).

This is very useful when calling asynchronous functions if you want to wait for the callback before continuing your CasperJS navigation. Simply call ``blockSteps()`` before the asynchronous call, and ``unblockSteps()`` in the callback.

**After, unblockSteps() must be called the same number of times, otherwise navigation will be blocked.**

containerId
-----------

::

    buster.containerId

Contains the ID of the currently running container as a ``Number``. This is useful for making requests to the Phantombuster API from within the agent.

download()
----------

::

    buster.download(url [, saveAs, headers, callback])

Downloads a distant file to your agent's disk (not to your persistent storage). If you do not save the file to your persistent storage (see :ref:`agent-module-file-storage`), **it will be lost when your agent exits**.

This method is asynchronous and returns nothing. Use the callback to know when it has finished.

``url`` (``String``)
    URL of the file to be downloaded.

    - ``https://www.google.com/images/srpr/logo11w.png``
    - ``http://soundcloud.com/`` (you'll get the HTML content of their homepage)

``saveAs`` (``String``)
    Where to put the file on your agent's disk (optional). By default, the name will be taken from ``url`` and the file will be saved in the current working directory on your agent's disk. If a file with the same name already exists, it is overwritten.

    - ``foo/`` (saves ``http://example.com/baz/bar.png`` as ``foo/bar.png``)
    - *null* (saves ``http://example.com/foo/bar.png`` as ``bar.png``)
    - ``foo/`` (fails on ``http://example.com/`` with ``could not determine filename``)
    - ``foo/a`` (saves ``http://example.com/bar.png`` as ``foo/a``)

    Intermediate directories are not created automatically on your agent's disk.

``headers`` (``PlainObject``)
    HTTP headers to use when requesting the file (optional). Cookies are automatically set when using CasperJS or PhantomJS.

``callback`` (``Function(String err, String path)``)
    Function to call when finished (optional). When there is no error, ``err`` is *null* and ``path`` contains the path to the file on your agent's disk.

getAgentObject()
----------------

::

    buster.getAgentObject([agentId,] callback)

Gets the object of an agent.

This method is asynchronous and returns nothing. Use the callback to know when it has finished.

``agentId`` (``String``)
    ID of the agent from which to get the object (optional). By default, this is the ID of the currently running agent.

``callback`` (``Function(String err, PlainObject object)``)
    Function to call when finished. When there is no error, ``err`` is *null* and ``object`` is a valid object (which may be empty but never *null*).

getGlobalObject()
-----------------

::

    buster.getGlobalObject(callback)

Gets the global object of your account.

This method is asynchronous and returns nothing. Use the callback to know when it has finished.

``callback`` (``Function(String err, PlainObject object)``)
    Function to call when finished. When there is no error, ``err`` is *null* and ``object`` is a valid object (which may be empty but never *null*).

overrideTimeLimit()
-------------------

::

    buster.overrideTimeLimit(seconds [, callback])

Overrides the execution time limit of the agent. When the execution time reaches the specified number of seconds, the agent is stopped.

This method is asynchronous and returns nothing. Use the callback to know when it has finished.

``seconds`` (``Number``)
    New time limit of the agent in seconds (integer), or ``0`` to disable the time limit. If the specified number of seconds is already lower than the current execution time, the agent is stopped right away.

``callback`` (``Function(String err)``)
    Function to call when finished (optional). When there is no error, ``err`` is *null*.

proxyAddress
------------

::

    buster.proxyAddress

Contains the proxy address currently being used by your agent as a ``String`` (PhantomJS/CasperJs only), or an empty string if there is no proxy in use. This is useful to know which proxy was selected from a pool.

setAgentObject()
----------------

::

    buster.setAgentObject([agentId,] object [, callback])

Sets (in fact, **replaces**) the object of an agent. It's recommended to first fetch the object with `getAgentObject()`_ (to update it) because **this method overwrites the whole object**.

This method is asynchronous and returns nothing. Use the callback to know when it has finished.

``agentId`` (``Number``)
    ID of the agent which will get its object set (optional). By default, this is the ID of the currently running agent.

``object`` (``PlainObject``)
    Object to save.

``callback`` (``Function(String err)``)
    Function to call when finished (optional). When there is no error, ``err`` is *null*.

setGlobalObject()
-----------------

::

    buster.setAgentObject(object [, callback])

Sets (in fact, **replaces**) the global object of your account. It's recommended to first fetch the global object with `getGlobalObject()`_ (to update it) because **this method overwrites the whole object**.

This method is asynchronous and returns nothing. Use the callback to know when it has finished.

``object`` (``PlainObject``)
    Object to save.

``callback`` (``Function(String err)``)
    Function to call when finished (optional). When there is no error, ``err`` is *null*.

solveCaptcha()
--------------

::

    buster.solveCaptcha(selector [, casperInstance], callback)

**CasperJS only.**

Tries to solve a CAPTCHA image. This method takes a screenshot of the area indicated by ``selector`` and sends it to one of our partners for solving.

If your CAPTCHA image is trivial, an OCR algorithm will quickly return the text, otherwise a human will solve it. This process generally takes less than 30 seconds and accuracy is >90%.

When a result string is returned, 1 is substracted from your daily CAPTCHA counter. In approximately 10% of the cases the result will be incorrect — retry at will.

This method is asynchronous and returns nothing. Use the callback to know when it has finished.

``selector`` (``String``)
    CSS3 selector pointing to the CAPTCHA image.

``casperInstance`` (``CasperJS``)
    Optional CasperJS instance that will be used for capturing the image. Ignore this parameter if you called ``create()`` with a CasperJS instance already.

``callback`` (``Function(String err, String result)``)
    Function to call when finished. When there is no error, ``err`` is *null* and ``result`` contains the solved CAPTCHA text.

solveCaptchaBase64()
--------------------

::

    buster.solveCaptchaBase64(base64String, callback)

Tries to solve a CAPTCHA image. This method takes `Base64 <https://en.wikipedia.org/wiki/Base64>`_ encoded image and sends it to one of our partners for solving.

If your CAPTCHA image is trivial, an OCR algorithm will quickly return the text, otherwise a human will solve it. This process generally takes less than 30 seconds and accuracy is >90%.

When a result string is returned, 1 is substracted from your daily CAPTCHA counter. In approximately 10% of the cases the result will be incorrect — retry at will.

This method is asynchronous and returns nothing. Use the callback to know when it has finished.

``base64String`` (``String``)
    CAPTCHA image to solve. Can be pure Base64 or a `Data URI Scheme <https://en.wikipedia.org/wiki/Data_URI_scheme>`_ string starting with ``data:``.

``callback`` (``Function(String err, String result)``)
    Function to call when finished. When there is no error, ``err`` is *null* and ``result`` contains the solved CAPTCHA text.

solveCaptchaImage()
-------------------

::

    buster.solveCaptchaImage(urlOrPath [, headers], callback)

Tries to solve a CAPTCHA image. This method takes an URL or a path of an image and sends it to one of our partners for solving.

If your CAPTCHA image is trivial, an OCR algorithm will quickly return the text, otherwise a human will solve it. This process generally takes less than 30 seconds and accuracy is >90%.

When a result string is returned, 1 is substracted from your daily CAPTCHA counter. In approximately 10% of the cases the result will be incorrect — retry at will.

This method is asynchronous and returns nothing. Use the callback to know when it has finished.

``urlOrPath`` (``String``)
    URL or path of the CAPTCHA image to be solved.

``headers`` (``PlainObject``)
    HTTP headers to use when requesting the image (optional). Cookies are automatically set when using CasperJS or PhantomJS.

``callback`` (``Function(String err, String result)``)
    Function to call when finished. When there is no error, ``err`` is *null* and ``result`` contains the solved CAPTCHA text.

unblockSteps()
--------------

::

    buster.unblockSteps()

**CasperJS only.**

Signals CasperJS to continue the execution of its steps. Goes in pair with `blockSteps()`_.

**This method must be called the same number of times blockSteps() was called, otherwise navigation will be blocked.**
