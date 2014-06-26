Client Library
==============

.save()
-------

::

    .save(filePath, saveAs, headers, callback)

    .save(filePath, saveAs, callback)

    .save(filePath, callback)

    .save(filePath)

Saves a distant or local file to your agent's persistent storage.

If a file with the same name already exists on your agent's storage, it is overwritten.

``filePath`` (``String``)
    Path of the file to be saved.

    - ``https://www.google.com/images/srpr/logo11w.png`` (from the web)
    - ``foo/my_screenshot.jpg`` (from your agent's disk)
    - ``http://soundcloud.com/`` (you'll get the HTML content of their homepage)

``saveAs`` (``String``)
    Where to put the file on your storage (optional).

    - ``foo/`` will save ``http://example.com/bar.png`` as ``foo/bar.png``
    - *null* will save ``http://example.com/foo/bar.png`` as ``bar.png``
    - ``foo/`` will fail on ``http://example.com/`` with ``could not determine filename``
    - ``foo/a`` will save ``http://example.com/bar.png`` as ``foo/a``

    You do not need to create any intermediate directory (``a/b/c/d/e.jpg`` will work).

``headers`` (``PlainObject``)
    HTTP headers to use (optional).

``callback`` (``Function(String err)``)
