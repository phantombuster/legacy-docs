Agent Module: The basics
========================

The Agent Module (also called ``buster``) is a special module made by the Phantombuster team. It provides some cool features and is linked to your Phantombuster account.

Here's a short list of what you can do with our module:

- Save files to your persistent storage
- Download files to your agent's disk
- Indicate the progress of your agent
- Set or reset the time limit of your agent
- Send emails
- ...

This module is compatible with all the Phantombuster commands (CapserJS, PhantomJS and Node).

Initialization
--------------

The agent module is named ``phantombuster``. Use ``require('phantombuster')`` and call ``create()`` to instantiate it.

::

    buster = require('phantombuster').create(casperInstance);

You can require a specific version of the agent module by using ``phantombuster-vX`` where ``X`` is the version number (for now there is only version ``1``). This can be useful if you want your script to continue working even after we make breaking changes to newer versions of the agent module.

::

    buster = require('phantombuster-v1').create(casperInstance);

When using the agent module in conjunction with CasperJS, you must pass the CasperJS instance to ``create()``. A typical CasperJS script starts like this:

::

    casper = require('casper').create();
    mouse = require('mouse').create(casper);
    buster = require('phantombuster').create(casper);

When using PhantomJS or Node, call ``create()`` with no arguments:

::

    buster = require('phantombuster').create();

Asynchronous methods
--------------------

Following the philosophy of Node.js, most methods of the agent module are asynchronous. You have to use the callback function to know when (and if) a call finished successfully.

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

CasperJS steps blocking
-----------------------

If the agent module is instantiated with a CasperJS instance passed in ``create()``, its methods will block the current navigation step for your convenience. For example:

::

    casper = require('casper').create();
    buster = require('phantombuster').create(casper); // pass the CasperJS instance

    casper.start('https://example.com', function() {

        // method calls signal to CasperJS to not go to the next step until they are finished
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

Internally, the agent module makes calls to `buster.blockSteps()`_ and `buster.unblockSteps()`_. You can also use these methods for stopping the execution of steps when calling other asynchronous functions.

buster.argument
---------------

::

    buster.argument

Contains the agent's argument as a ``PlainObject``. On Phantombuster, each agent receives a JSON object as argument, which can be set each time they are launched.

buster.apiKey
-------------

::

    buster.apiKey

Contains your Phantombuster API key as a ``String``. This is useful for making requests to the Phantombuster API from within the agent.

buster.agentId
--------------

::

    buster.agentId

Contains the ID of the currently running agent as a ``Number``. This is useful for making requests to the Phantombuster API from within the agent.

buster.containerId
------------------

::

    buster.containerId

Contains the ID of the currently running container as a ``Number``. This is useful for making requests to the Phantombuster API from within the agent.

buster.proxyAddress
-------------------

::

    buster.proxyAddress

Contains the proxy address currently being used by your agent as a ``String`` (PhantomJS/CasperJs only), or an empty string if there is no proxy in use. This is useful to know which proxy was selected from a pool.

buster.download()
-----------------

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
    HTTP headers to use when requesting the file (optional). Cookies are automatically set when using CasperJS or PhantomJs.

``callback`` (``Function(String err, String path)``)
    Function to call when finished (optional). When there is no error, ``err`` is *null* and ``path`` contains the path to the file on your agent's disk.

buster.mail()
-------------

::

    buster.mail(subject [, text, to, callback])

Sends an email from Phantombuster and substracts 1 to your daily email counter.

This method is asynchronous and returns nothing. Use the callback to know when it has finished.

``subject`` (``String``)
    Subject of the email.

``text`` (``String``)
    Plain text contents of the email.

``to`` (``String``)
    Where to send the email (optional). When omitted, the email will be sent to the address associated with your Phantombuster account.

``callback`` (``Function(String err)``)
    Function to call when finished (optional). When there is no error, ``err`` is *null*.

buster.notify()
---------------

::

    buster.notify(message [, options , callback])

Sends a push notification to your device(s) using Pushover. For this call to work, you must have set a Pushover user key in your `settings <https://phantombuster.com/settings>`_ and have installed a `Pushover client <https://pushover.net/clients>`_ on at least one of your devices.

This method is asynchronous and returns nothing. Use the callback to know when it has finished.

``message`` (``String``)
    Text contents of the notification.

``options`` (``PlainObject``)
    Additionnal parameters to send to Pushover. Get the full details at the `Pushover API documentation <https://pushover.net/api>`_.

    - ``device`` - your device name to send the message directly to that device, rather than all of your devices (multiple devices may be separated by a comma)
    - ``title`` - your message's title, otherwise *Phantombuster* is used
    - ``url`` - a supplementary URL to show with your message
    - ``url_title`` - a title for your supplementary URL, otherwise just the URL is shown
    - ``priority`` - send as ``-2`` to generate no notification/alert, ``-1`` to always send as a quiet notification or ``1`` to display as high-priority and bypass your quiet hours
    - ``timestamp`` - a Unix timestamp of your message's date and time to display, rather than the time the message is received by Pushover
    - ``sound`` - the name of one of the sounds supported by device clients to override your default sound choice

    Note: at the moment this method does not support the receipt system of Pushover (``priority`` set to ``2``).

``callback`` (``Function(String err)``)
    Function to call when finished (optional). When there is no error, ``err`` is *null*.

buster.solveCaptcha()
---------------------

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

buster.progressHint()
---------------------

::

    buster.progressHint(progress [, label])

Reports the progress state of the agent. This affects the width and content of the progress bar displayed in the agent console on Phantombuster.

This is useful for debugging purposes and is not required for the agent to function properly. Sometimes it's just nice to see the progress of your agent in real-time.

This method returns nothing and has no callback.

``progress`` (``Number``)
    Progress float value between ``0`` and ``1``. ``1`` means 100% of the work was completed, and ``0`` means 0%. If *null*, defaults to ``1``.

``label`` (``String``)
    Optional textual description of the state of your agent (clipped to 50 characters). This shows up as a text inside the progress bar displayed in the agent console.

buster.overrideTimeLimit()
--------------------------

::

    buster.overrideTimeLimit(seconds [, callback])

Overrides the execution time limit of the agent. When the execution time reaches the specified number of seconds, the agent is stopped.

This method is asynchronous and returns nothing. Use the callback to know when it has finished.

``seconds`` (``Number``)
    New time limit of the agent in seconds (integer), or ``0`` to disable the time limit. If the specified number of seconds is already lower than the current execution time, the agent is stopped right away.

``callback`` (``Function(String err)``)
    Function to call when finished (optional). When there is no error, ``err`` is *null*.

buster.blockSteps()
-------------------

::

    buster.blockSteps()

**CasperJS only.**

Stops the execution of CasperJS steps until `buster.unblockSteps()`_ is called (behind the scenes, it uses the same system as ``casper.wait()``).

This is very useful when calling asynchronous functions if you want to wait for the callback before continuing your CasperJS navigation. Simply call ``blockSteps()`` before the asynchronous call, and ``unblockSteps()`` in the callback.

**After, unblockSteps() must be called the same number of times, otherwise navigation will be blocked.**

buster.unblockSteps()
---------------------

::

    buster.unblockSteps()

**CasperJS only.**

Signals CasperJS to continue the execution of its steps. Goes in pair with `buster.blockSteps()`_.

**This method must be called the same number of times blockSteps() was called, otherwise navigation will be blocked.**

buster.setAgentObject()
-----------------------

::

    buster.setAgentObject([agentId,] object [, callback])

Sets (in fact, **replaces**) the object of an agent. It's recommended to first fetch the object with `buster.getAgentObject()`_ (to update it) because **this method overwrites the whole object**.

This method is asynchronous and returns nothing. Use the callback to know when it has finished.

``agentId`` (``Number``)
    ID of the agent which will get its object set (optional). By default, this is the ID of the currently running agent.

``object`` (``PlainObject``)
    Object to save.

``callback`` (``Function(String err)``)
    Function to call when finished (optional). When there is no error, ``err`` is *null*.

buster.getAgentObject()
-----------------------

::

    buster.getAgentObject([agentId,] callback)

Gets the object of an agent.

This method is asynchronous and returns nothing. Use the callback to know when it has finished.

``agentId`` (``String``)
    ID of the agent from which to get the object (optional). By default, this is the ID of the currently running agent.

``callback`` (``Function(String err, PlainObject object)``)
    Function to call when finished. When there is no error, ``err`` is *null* and ``object`` is a valid object (which may be empty but never *null*).

buster.setGlobalObject()
------------------------

::

    buster.setAgentObject(object [, callback])

Sets (in fact, **replaces**) the global object of your account. It's recommended to first fetch the global object with `buster.getGlobalObject()`_ (to update it) because **this method overwrites the whole object**.

This method is asynchronous and returns nothing. Use the callback to know when it has finished.

``object`` (``PlainObject``)
    Object to save.

``callback`` (``Function(String err)``)
    Function to call when finished (optional). When there is no error, ``err`` is *null*.

buster.getGlobalObject()
------------------------

::

    buster.getGlobalObject(callback)

Gets the global object of your account.

This method is asynchronous and returns nothing. Use the callback to know when it has finished.

``callback`` (``Function(String err, PlainObject object)``)
    Function to call when finished. When there is no error, ``err`` is *null* and ``object`` is a valid object (which may be empty but never *null*).

buster.setResultObject()
------------------------

::

    buster.setResultObject(object [, callback])

Sets (in fact, **replaces**) the result object of your agent. Think of the result object as the output value of your agent. This is useful for returning a small set of JSON fields containing whatever your agent might want to return when it exits.

Use this method in conjunction with the :ref:`launch-an-agent` API endpoint (with ``output`` set to ``result-object``) to launch **and** get the result of your agent in one single HTTP request.

This method is asynchronous and returns nothing. Use the callback to know when it has finished.

``object`` (``PlainObject``)
    Object to set as result object.

``callback`` (``Function(String err)``)
    Function to call when finished (optional). When there is no error, ``err`` is *null*.
