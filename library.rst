Client Library
==============

.save() method
--------------

::

    .save(filePath, saveAs, headers, callback)

    .save(filePath, saveAs, callback)

    .save(filePath, callback)

    .save(filePath)

Saves a distant or local file to your storage.

- ``filePath`` (``String``)
    Path to the file to be saved. If it starts with ``http://`` or ``https://``, a download attempt will be made first. If this download attempt fails, 

- ``saveAs`` (``String``)
- ``headers`` (``PlainObject``)
- ``callback`` (``Function(String err)``)
