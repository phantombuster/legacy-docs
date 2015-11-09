.. _agent-module-database:

Agent Module: Database
======================

These methods allow you to read and write collections of JSON documents to a MongoDB database.

Before these methods can be used, you need to :ref:`require() and create() the agent module <agent-module>`.

This module works with PhantomJS, CasperJS and Node. However, when using Node, we recommend using the ``mongodb`` module instead, which is faster and way more complete.

If your Phantombuster account has a database, you can call these methods without any configuration. If that's not the case (or if you want to use your own database), you must specifiy your MongoDB connection string with `db.setConnectionUrl()`_ before any other call to database methods.

Extended JSON mode
------------------

Requests can be made in either normal JSON mode (which is the default) or extended JSON mode by setting the ``extendedJson`` field to ``true`` in query options.

Internally, MongoDB supports many different types such as dates, regular expressions and IDs. These cannot be represented in plain JSON and this is where extended JSON becomes useful.

- **In normal JSON mode**, data is transferred to and from MongoDB in plain JSON. JavaScript native types ``string``, ``number`` and ``boolean`` are automatically converted to their equivalent for MongoDB, but no other processing is done.

  For example, a call to `db.find()`_ could return this:

  ::

      [
        {
          "_id": "56211c5e7ab57e010aa49280",
          "date": "2015-10-16T15:48:46.134Z",
          "aNumberField": 42
        }
      ]

- **In extended JSON mode**, JSON data is processed before being written and after being read from the MongoDB database.

  For example, a call to `db.find()`_ could return this:

  ::

      [
        {
          "_id": {
            "$oid": "56211d1bf90c1e0100371c63"
          },
          "date": {
            "$date": "2015-10-16T15:51:55.304Z"
          },
          "aNumberField": 42
        }
      ]

  Please see the official `MongoDB documentation on extended JSON <https://docs.mongodb.org/manual/reference/mongodb-extended-json/>`_.

Extended JSON mode can be enabled globally by instantiating the agent module like this:

::

    // casperInstance is optional (applies only when using CasperJS)
    buster = require('phantombuster').create(casperInstance, { extendedJson: true });

When extended JSON mode is enabled globally, you can still disable it for individual requests by explicitly setting the ``extendedJson`` field to ``false`` in query options.

Note: when enabled, extended JSON mode always applies for incoming **and** outgoing data (such as inserted documents and update queries).

db.count()
----------

::

    buster.db.count(collection [, options], callback)

Returns the number of documents in a collection.

This method is asynchronous and returns nothing. Use the callback to get the result.

``collection`` (``String``)
    Name of the collection on which to execute the count operation.

``options`` (``PlainObject``)
    Additionnal parameters for the request (optional, by default all documents are counted).

    - ``limit`` (``Number``) - limit of documents to count
    - ``skip`` (``Boolean``) - number of documents to skip for the count

``callback`` (``Function(String err, Number count)``)
    Function to call when finished. When there is no error, ``err`` is *null*.

    ``count`` contains the result.

db.delete()
-----------

::

    buster.db.delete(collection [, options, callback])

Deletes documents from a collection.

This method is asynchronous and returns nothing. Use the callback to know when it has finished.

``collection`` (``String``)
    Name of the collection from which documents will be deleted.

``options`` (``PlainObject``)
    Additionnal parameters for the request (optional, by default all documents are deleted).

    - ``query`` (``PlainObject``) - MongoDB query for choosing which documents to delete
    - ``extendedJson`` (``Boolean``) - enable `Extended JSON mode`_ for this query

``callback`` (``Function(String err, PlainObject res)``)
    Function to call when finished (optional). When there is no error, ``err`` is *null*.

    ``res.deletedCount`` contains the number of documents deleted by the query.

db.find()
---------

::

    buster.db.find(collection [, options], callback)

Returns documents from a collection.

This method is asynchronous and returns nothing. Use the callback to get the result.

``collection`` (``String``)
    Name of the collection in which documents will be searched.

``options`` (``PlainObject``)
    Additionnal parameters for the request (optional, by default all documents are returned).

    - ``query`` (``PlainObject``) - MongoDB query for selecting documents
    - ``limit`` (``Number``) - limits the number of documents returned
    - ``sort`` (``PlainObject``) - how to sort the documents (field names associated with ``1`` or ``-1``, for example ``{ "name": -1 }``)
    - ``fields`` (``PlainObject``) - fields to include (``1``) or exclude (``0``), for example: ``{ "name": 1 }``
    - ``skip`` (``Number``) - number of documents to skip (useful for pagination)
    - ``extendedJson`` (``Boolean``) - enable `Extended JSON mode`_ for this query (applies for the returned array **and** for the ``query`` option field)

``callback`` (``Function(String err, Array documents)``)
    Function to call when finished. When there is no error, ``err`` is *null*.

    ``documents`` is an array containing all the documents found.

db.insert()
-----------

::

    buster.db.insert(collection, documents [, options, callback])

Inserts documents into a collection.

This method is asynchronous and returns nothing. Use the callback to know when it has finished.

``collection`` (``String``)
    Name of the collection in which documents will be inserted.

``documents`` (``PlainObject`` or ``Array``)
    Document or array of documents to insert into the collection.

``options`` (``PlainObject``)
    Additionnal parameters for the request (optional).

    - ``extendedJson`` (``Boolean``) - enable `Extended JSON mode`_ for this query (applies for the returned object **and** for the ``documents`` array)

``callback`` (``Function(String err, PlainObject res)``)
    Function to call when finished (optional). When there is no error, ``err`` is *null*.

    - ``res.insertedCount`` contains the number of documents inserted by the query
    - ``res.insertedIds`` is an array containing all the newly created MongoDB IDs

db.setConnectionUrl()
---------------------

::

    buster.db.setConnectionUrl(url [, callback])

Sets the connection string of the MongoDB server you wish to use. If your Phantombuster account has a database, there is no need to call this method.

Note: this method must be called prior to any other database methods.

This method is asynchronous and returns nothing. Use the callback to know when it has finished.

``url`` (``String``)
    Full, canonical MongoDB connection string, for example ``mongodb://user:password@host.com:27017/database``.

``callback`` (``Function(String err)``)
    Function to call when finished (optional). When there is no error, ``err`` is *null*.

db.update()
-----------

::

    buster.db.update(collection, update [, options, callback])

Updates documents stored in a collection.

This method is asynchronous and returns nothing. Use the callback to know when it has finished.

``collection`` (``String``)
    Name of the collection in which documents will be updated.

``update`` (``PlainObject``)
    MongoDB update parameter describing what modifications to apply.

``options`` (``PlainObject``)
    Additionnal parameters for the request (optional, by default all documents are updated).

    - ``query`` (``PlainObject``) - MongoDB filter query for selecting which documents to update
    - ``upsert`` (``Boolean``) - Do an upsert instead of an update (creates a new document when no document matches the query criteria)
    - ``extendedJson`` (``Boolean``) - enable `Extended JSON mode`_ for this query (applies for the returned object **and** for the ``update`` object **and** for the ``query`` option field)

``callback`` (``Function(String err, PlainObject res)``)
    Function to call when finished (optional). When there is no error, ``err`` is *null*.

    - ``res.matchedCount`` contains the number of documents that matched the filter query
    - ``res.modifiedCount`` contains the number of documents that were updated by the query
    - ``res.upsertedCount`` contains the number of inserted documents in case of an upsert
    - ``res.upsertedId`` contains the newly created MongoDB ID in case of an upsert
