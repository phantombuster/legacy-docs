Agent Module
============

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

When using PhantomJS, call ``create()`` with no arguments:

::

    buster = require('phantombuster').create();

Important note about asynchronous methods
-----------------------------------------

Following the philosophy of Node.js, all methods of the agent module are asynchronous. You have to use the callback function to know when and if a call finished successfully.

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

::

    Navigation step 1
    Text saved!
    This is executed a few ticks after saveText()
    And this is executed last

buster.argument
---------------

::

    buster.argument

Contains the agent's argument as a plain object. On Phantombuster, each agent receives a JSON object as argument, which can be set each time they are launched.

buster.apiKey
-------------

::

    buster.apiKey

Contains your Phantombuster API key as a string. This is useful for making requests to the Phantombuster API from within the agent.

buster.save()
-------------

::

    buster.save(urlOrPath)

    buster.save(urlOrPath, callback)

    buster.save(urlOrPath, saveAs, callback)

    buster.save(urlOrPath, saveAs, headers, callback)

Saves a distant or local file to your persistent storage.

This method is asynchronous and returns nothing. Use the callback to know when it has finished.

``urlOrPath`` (``String``)
    URL or path of the file to be saved.

    - ``https://www.google.com/images/srpr/logo11w.png`` (from the web)
    - ``foo/my_screenshot.jpg`` (from your agent's disk)
    - ``http://soundcloud.com/`` (you'll get the HTML content of their homepage)

    When saving a distant file, the `MIME type <https://en.wikipedia.org/wiki/Internet_media_type>`_ is taken from the ``Content-Type`` HTTP header (if present). When saving a local file, the MIME type is guessed from the file extension (if this fails, no MIME type is set).

``saveAs`` (``String``)
    Where to put the file on your persistent storage (optional). By default, the name will be taken from ``urlOrPath`` and the file will be saved at the root of your agent's folder in your persistent storage. If a file with the same name already exists, it is overwritten.

    - ``foo/`` (saves ``http://example.com/baz/bar.png`` as ``foo/bar.png``)
    - *null* (saves ``http://example.com/foo/bar.png`` as ``bar.png``)
    - ``foo/`` (fails on ``http://example.com/`` with ``could not determine filename``)
    - ``foo/a`` (saves ``http://example.com/bar.png`` as ``foo/a``)

    You do not need to create any intermediate directory (``a/b/c/d/e.jpg`` will work).

``headers`` (``PlainObject``)
    HTTP headers to use when requesting the file (optional). No need to worry about cookies, they are set by default.

``callback`` (``Function(String err, String url)``)
    Function to call when finished. When there is no error, ``err`` is *null* and ``url`` contains the full URL to the file on your persistent storage.

buster.download()
-----------------

::

    buster.download(url)

    buster.download(url, callback)

    buster.download(url, saveAs, callback)

    buster.download(url, saveAs, headers, callback)

Downloads a distant file to your agent's disk (not to your persistent storage).

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
    HTTP headers to use when requesting the file (optional). No need to worry about cookies, they are set by default.

``callback`` (``Function(String err, String path)``)
    Function to call when finished (optional). When there is no error, ``err`` is *null* and ``path`` contains the path to the file on your agent's disk.

buster.saveFolder()
-------------------

::

    buster.saveFolder()

    buster.saveFolder(path)

    buster.saveFolder(path, callback)

    buster.saveFolder(path, saveAs, callback)

Saves a folder from your agent's disk to your persistent storage.

This method is asynchronous and returns nothing. Use the callback to know when it has finished.

``path`` (``String``)
    Path of the folder to save (optional, defaults to ``.``).

    - ``.`` (everything from your current working directory)
    - ``any/sub/../sub/directory``

    Each file has its `MIME type <https://en.wikipedia.org/wiki/Internet_media_type>`_ guessed from its extension (if this fails, no MIME type is set).

``saveAs`` (``String``)
    Where to put the folder on your persistent storage (optional). By default, the folder will be saved at the root of your agent's folder in your persistent storage. If files with the same name already exist, they are overwritten.

    - ``/`` or empty string (root of your agent's folder in your persistent storage)
    - ``any/sub/directory``
    - ``dir/foo.txt`` (this will create a directory named ``foo.txt``, obviously not recommended)

    You do not need to create any intermediate directory (``a/b/c/d`` will work).

``callback`` (``Function(String err, String url)``)
    Function to call when finished (optional). When there is no error, ``err`` is *null* and ``url`` contains the full URL to the folder in your persistent storage.

buster.saveText()
-----------------

::

    buster.saveText(text, saveAs)

    buster.saveText(text, saveAs, callback)

    buster.saveText(text, saveAs, mime, callback)

Saves a string to a file in your persistent storage.

This method is asynchronous and returns nothing. Use the callback to know when it has finished.

``text`` (``String``)
    Contents of the file to save. Can be anything, really.

``saveAs`` (``String``)
    Where to put the file on your persistent storage. If a file with the same name already exists, it is overwritten.

    - ``file.txt``
    - ``any/sub/directory/file.json``
    - ``dir/`` (fails because no file name was given)

    You do not need to create any intermediate directory (``a/b/c/d`` will work).

``mime`` (``String``)
    `MIME type <https://en.wikipedia.org/wiki/Internet_media_type>`_ of the file being saved (optional). By default it is guessed from the file extension of the ``saveAs`` parameter (if this fails, no MIME type is set).

    - ``application/json``
    - ``text/csv``
    - ``text/html``

``callback`` (``Function(String err, String url)``)
    Function to call when finished (optional). When there is no error, ``err`` is *null* and ``url`` contains the full URL to the file in your persistent storage.

buster.saveBase64()
-------------------

::

    buster.saveText(base64String, saveAs)

    buster.saveText(base64String, saveAs, callback)

    buster.saveText(base64String, saveAs, mime, callback)

Saves a `Base64 <https://en.wikipedia.org/wiki/Base64>`_ encoded file to your persistent storage.

This method is asynchronous and returns nothing. Use the callback to know when it has finished.

``base64String`` (``String``)
    Contents of the file to save. Can be pure Base64 or a `Data URI Scheme <https://en.wikipedia.org/wiki/Data_URI_scheme>`_ string starting with ``data:``.

``saveAs`` (``String``)
    Where to put the file on your persistent storage. If a file with the same name already exists, it is overwritten.

    - ``file.jpg``
    - ``any/sub/directory/file.png``
    - ``dir/`` (fails because no file name was given)

    You do not need to create any intermediate directory (``a/b/c/d`` will work).

``mime`` (``String``)
    `MIME type <https://en.wikipedia.org/wiki/Internet_media_type>`_ of the file being saved (optional). By default it is guessed either from the Data URI Scheme string or from the file extension of the ``saveAs`` parameter (if this fails, no MIME type is set).

    - ``image/jpeg``
    - ``image/png``
    - ``image/svg+xml``

``callback`` (``Function(String err, String url)``)
    Function to call when finished (optional). When there is no error, ``err`` is *null* and ``url`` contains the full URL to the file in your persistent storage.

buster.mail()
-------------

::

    buster.mail(subject, text)

    buster.mail(subject, text, callback)

Sends an email to the address associated with your Phantombuster account and substracts 1 to your daily email counter.

This method is asynchronous and returns nothing. Use the callback to know when it has finished.

``subject`` (``String``)
    Subject of the email.

``text`` (``String``)
    Plain text contents of the email.

``callback`` (``Function(String err)``)
    Function to call when finished (optional). When there is no error, ``err`` is *null*.

buster.progressHint()
---------------------

::

    buster.progressHint(progress)

    buster.progressHint(progress, label)

Reports the progress state of the agent. This affects the width and content of the progress bar displayed in the agent console on Phantombuster.

This is useful for debugging purposes and is not required for the agent to function properly. It is also very nice to see the progress of your agent in real-time.

``progress`` (``Number``)
    Progress float value between ``0`` and ``1``. ``1`` means 100% of the work was completed, and ``0`` means 0%. If *null*, defaults to ``1``.

``label`` (``String``)
    Optional textual description of the state of your agent (clipped to 50 characters). This shows up as a text inside the progress bar displayed in the agent console.
