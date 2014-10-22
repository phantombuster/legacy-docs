Agent Module Reference
======================

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

``saveAs`` (``String``)
    Where to put the file on your persistent storage (optional). If a file with the same name already exists, it is overwritten.

    - ``foo/`` (saves ``http://example.com/baz/bar.png`` as ``foo/bar.png``)
    - *null* (saves ``http://example.com/foo/bar.png`` as ``bar.png``)
    - ``foo/`` (fails on ``http://example.com/`` with ``could not determine filename``)
    - ``foo/a`` (saves ``http://example.com/bar.png`` as ``foo/a``)

    You do not need to create any intermediate directory (``a/b/c/d/e.jpg`` will work).

``headers`` (``PlainObject``)
    HTTP headers to use when requesting for the file (optional). No need to worries about cookies, they are set by default.

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
    Where to put the file on your agent's disk (optional). If a file with the same name already exists, it is overwritten.

    - ``foo/`` (saves ``http://example.com/baz/bar.png`` as ``foo/bar.png``)
    - *null* (saves ``http://example.com/foo/bar.png`` as ``bar.png``)
    - ``foo/`` (fails on ``http://example.com/`` with ``could not determine filename``)
    - ``foo/a`` (saves ``http://example.com/bar.png`` as ``foo/a``)

    Intermediate directories are not created automatically.

``headers`` (``PlainObject``)
    HTTP headers to use when requesting for the file (optional).

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

``saveAs`` (``String``)
    Where to put the folder on your persistent storage (optional). If files with the same name already exist, they are overwritten.

    - ``/`` or empty string (root of your agent's folder in your persistent storage)
    - ``any/sub/directory``
    - ``dir/foo.txt`` (this will create a directory named ``foo.txt``, obviously not recommended)

    You do not need to create any intermediate directory (``a/b/c/d`` will work).

``callback`` (``Function(String err, String url)``)
    Function to call when finished (optional). When there is no error, ``err`` is *null* and ``url`` contains the full URL to the folder in your persistent storage.

buster.saveText()
-------------------

::

    buster.saveText(text, saveAs)

    buster.saveText(text, saveAs, callback)

    buster.saveText(text, saveAs, mime, callback)


Saves a string to a file on your persistent storage.

This method is asynchronous and returns nothing. Use the callback to know when it has finished.

``text`` (``String``)
    Contents of the file to save. Can be anything, really.

``saveAs`` (``String``)
    Where to put the file on your persistent storage. If a file with the same name already exists, it is overwritten.

    - ``file.txt``
    - ``any/sub/directory/file.txt``
    - ``folder/`` (fails because no file name was given)

    You do not need to create any intermediate directory (``a/b/c/d`` will work).

``mime`` (``String``)
    `Mime type <https://en.wikipedia.org/wiki/Internet_media_type>`_ of the file being saved. By default it is guessed based on the file extension from the ``saveAs`` parameter.

    - ``application/json``
    - ``text/csv``
    - ``text/html``

``callback`` (``Function(String err, String url)``)
    Function to call when finished (optional). When there is no error, ``err`` is *null* and ``url`` contains the full URL to the file in your persistent storage.
