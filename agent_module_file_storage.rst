.. _agent-module-file-storage:

Agent Module: File storage
==========================

These four methods allow you to store files in your persitent storage.

Files that are on your agent's disk but not saved to your persistent storage **will be lost** when your agent exits.

Before these methods can be used, you need to :ref:`require() and create() the agent module <agent-module>`.

save()
------

::

    buster.save(urlOrPath [, saveAs, headers, callback])

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
    HTTP headers to use when requesting the file (optional). Cookies are automatically set when using CasperJS or PhantomJS.

``callback`` (``Function(String err, String url)``)
    Function to call when finished. When there is no error, ``err`` is *null* and ``url`` contains the full URL to the file on your persistent storage.

saveBase64()
------------

::

    buster.saveBase64(base64String, saveAs [, mime, callback])

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

saveFolder()
------------

::

    buster.saveFolder([path, saveAs, callback])

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

saveText()
----------

::

    buster.saveText(text, saveAs [, mime, callback])

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
