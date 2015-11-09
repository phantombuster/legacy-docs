.. _sdk:

SDK
===

All your scripts can easily be written right on our website, in the provided CoffeeScript/JavaScript web editor.

However, you might prefer using your own editor, locally on your machine. We made `Phantombuster's SDK <https://www.npmjs.com/package/phantombuster-sdk>`_ specifically for this.

The SDK will **monitor a directory** on your disk for changes in your scripts. As soon as a change is detected, the script will be uploaded in your Phantombuster account.

Installation
------------

Because we made the SDK as a Node module, you need to have ``npm`` (`Node Package Manager <https://www.npmjs.com/>`_) installed on your machine. Then type:

::

    # npm install -g phantombuster-sdk

This will globally install the ``phantombuster`` command.

Project directory
-----------------

All your scripts will go in a project directory. You can arrange the contents of this directory however you like (by creating a sub-directory for each category of scripts for example).

You can choose any name for this directory. For example:

::

    $ mkdir phantom-project

Configuration
-------------

All the SDK's configuration goes into a single file named ``phantombuster.cson`` at the root of your project directory. It's written in `CoffeeScript Object Notation <https://www.npmjs.com/package/cson>`_ (``.cson``).

Open/create this file with the editor of your choice:

::

    $ vi phantom-project/phantombuster.cson

Configuration is very simple:

::

    [
        name: 'account name'
        apiKey: 'YOUR_API_KEY_HERE'
        scripts:
            'my-script.js': 'project/test/my-script.js'
            'another-script.coffee': 'folder/script.coffee'
    ]

- ``name`` is an arbitrary ``String`` of your choice. People generally put in the name of their Phantombuster account.

  This name is shown each time a script is successfully saved on Phantombuster's servers.

- ``apiKey`` must contain the API key of your Phantombuster account as a ``String``.

  You can find it in your `settings page <https://phantombuster.com/settings>`_ (click *Reveal*). It's important to keep this key secret because it allows anyone in its possession to manipulate your Phantombuster account (``phantombuster.cson`` is a private file and should not be shared). If you think your API key has been compromised, do not hesitate to generate a new one.

- ``scripts`` is a ``PlainObject`` containing a list of scripts that you wish to manage with the SDK.

  Each line (key/value pair) represents a script. The key (left side) is the script's name on Phantombuster (therefore it must not contain any special characters or any slashes). The value (right side) is the location of this script's file on your machine's disk, in your project directory (in fact, relative to the location of ``phantombuster.cson``).

Launch
------

To start the SDK, simply navigate to your project directory and execute the ``phantombuster`` command.

::

    $ cd phantom-project
    $ phantombuster

This will start a monitoring process. If any modification happens to your script's files, they will automatically be saved in your Phantombuster account.

It means you can now start the editor of your choice and start developping your bots in an environment you like.

Advanced usage
--------------

::

    phantombuster [-c config.cson] [script1.coffee [script2.coffee...]]

You can specify another configuration file with the ``-c`` option.

You can also provide one or more paths to scripts to upload them to your Phantombuster account (without starting the monitoring process).
