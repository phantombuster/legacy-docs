Client Library
==============

.save()
-------

::

    .save(filePath, saveAs, headers, callback)

    .save(filePath, saveAs, callback)

    .save(filePath, callback)

    .save(filePath)

**Saves a distant or local file to your Phantombuster persistent storage.**

If a file with the same name already exists on your storage, it is overwritten.

``filePath`` (``String``)
    **Path of the file to be saved.**

    If ``filePath`` starts with ``http://`` or ``https://``, a download attempt will be made first. If the download attempt fails, the file will be looked for on disk.

    Example values:

    - ``https://www.google.com/images/srpr/logo11w.png``
    - ``my_screenshot.jpg``
    - ``https://soundcloud.com/``
    - ``foo/bar.zip``

``saveAs`` (``String``)
    **Where to put the file on your storage.**

    If omitted or *null*, the last portion of ``filePath`` will be used if possible (for example ``https://www.google.com/images/srpr/logo11w.png`` will be saved as ``logo11w.png``).

    If ``saveAs`` ends with ``/``, the last portion of ``filePath`` will be added at the end if possible (for example ``https://www.google.com/images/srpr/logo11w.png`` will be saved as ``foo/logo11w.png`` if ``saveAs`` is ``foo/``).

    There is no need to create intermediate directories: ``long/path/to/some/directory/`` is valid and will immediately work.

    Example values:

    - *null*
    - ``/``
    - ``foo.jpg``
    - ``foo/bar/``

``headers`` (``PlainObject``)

``callback`` (``Function(String err)``)
