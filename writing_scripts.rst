Writing scripts
===============

At its core, Phantombuster allows you to script "web robots" in two languages: JavaScript and `CoffeeScript <http://coffeescript.org/>`_.

To create a new script, `log in <https://phantombuster.com/login>`_ and go to your `scripts page <https://phantombuster.com/scripts?createNew>`_, enter a filename ending in ``.js`` or ``.coffee`` and click `Create`.

Each script can be launched on our platform by one of the following commands (in fact, binaries):

- `Node <https://nodejs.org/>`_ — Execute your scripts in V8, Chrome's JavaScript runtime
- `PhantomJS <http://phantomjs.org/>`_ — Headless, scriptable WebKit browser (where Phantombuster got its name from)
- `CasperJS <http://casperjs.org/>`_ — Framework built on top of PhantomJS to easily write complex navigation scenarios

You can also write your own modules to better control and optimize how your robots will navigate the web. Or use the one made by the Phantombuster team: :ref:`Nick`.

**Quick start (read this!)**
----------------------------

**If you are in a hurry, please read at least the four following sections.**

The best way to understand and get started quickly with Phantombuster is to try some sample scripts.

Once logged in (`log in here <https://phantombuster.com/login>`_ if you haven't already), check out some of these scripts (choose your preferred language and framework or the first one if you don't know):

- **Nick** — Phantombuster's custom navigation module, easiest to understand and get started with:
    - CoffeeScript sample: `sample-Nick.coffee <https://phantombuster.com/script/308>`_
    - JavaScript sample: `sample-Nick.js <https://phantombuster.com/script/309>`_
    - :ref:`API documentation <Nick>`
- **CasperJS** — popular choice but not everyone agrees with its step-based navigation system:
    - CoffeeScript sample: `sample-CasperJS.coffee <https://phantombuster.com/script/310>`_
    - JavaScript sample: `sample-CasperJS.js <https://phantombuster.com/script/311>`_
    - API documentation: http://docs.casperjs.org/
- **PhantomJS** — provides the "low-level" functionality on which the two previous frameworks are based:
    - CoffeeScript sample: `sample-PhantomJS.coffee <https://phantombuster.com/script/312>`_
    - JavaScript sample: `sample-PhantomJS.js <https://phantombuster.com/script/314>`_
    - API documentation: http://phantomjs.org/api/
- **Node** — by far the fastest of all but uses a completely different scripting API (and it's not a headless web browser):
    - CoffeeScript sample: `sample-Node.coffee <https://phantombuster.com/script/315>`_
    - JavaScript sample: `sample-Node.js <https://phantombuster.com/script/316>`_
    - API documentation: https://nodejs.org/api/

When viewing a script, click *Quick Launch* in the top right corner to run it. You'll see your script execution in real-time. Just below the console output, your persistent storage is displayed (it's where your saved files will show up).

Do not hesitate to copy-paste these scripts to test more features.

**Agents and scripts**
----------------------

Now that you launched your first few scripts, you probably noticed that they run within an **agent** (if you used the *Quick Launch* feature, the agent you used was named *Quick Launch Agent*).

**Agents are configuration settings that describe how to run a certain script.** They allow you to control how and when a script is launched. The combination of a script and an agent gives you a full featured "web robot" that can scrape and automate stuff on the web.

To create an agent, go to your `agents page <https://phantombuster.com/agents?createNew>`_ and enter a name of your choice. The most important settings of an agent are which script to launch and when to launch it. But you'll see there are a lot of other options...

**In what environment do my scripts run?**
------------------------------------------

Your scripts are executed in Linux containers (they are similar to very light virtual machines).

Available to you are a few gigabytes of RAM, a few gigabytes of hard disk space and a fast internet connection. These are temporary resources that are freed right after your agent finishes its job.

What's important to know is that files written on your agent's disk **will be lost when it exits**. To keep files, save them to your persistent storage using our :ref:`agent module <agent-module-file-storage>`.

More technical details (for the nerds):
    - The container engine is Docker
    - Containers are running Debian
    - Agents always start in ``/home/phantom/agent`` which is empty
    - Agents run under the user ``phantom``

**Phantombuster's SDK**
-----------------------

All your scripts can easily be written right on our website, in our custom CoffeeScript/JavaScript editor.

However, you might prefer using your own editor, locally on your machine. We made Phantombuster's SDK specifically for this. First, you need to have ``npm`` installed. Then do this:

::

    npm install -g phantombuster-sdk

It will install the ``phantombuster`` command. :ref:`Discover how to use it → <SDK>`

Requiring other scripts
-----------------------

All your scripts (and samples/libraries) can be required. The requiring script must have a ``phantombuster dependencies`` directive (similar to ``"use strict";``) listing its dependencies.

::

    // Comma separated list of dependencies
    // Specify the full name (with extension)
    "phantombuster dependencies: my-script.js, lib-CasperPool.coffee";
    "use strict";

    // The rest of your script...
    require("my-script");
    CasperPool = require("lib-CasperPool");

Writing libraries/modules
-------------------------

When the name of a script starts with ``lib``, its launch will be disabled. This allows you to safely write libraries that can later be required using ``phantombuster dependencies`` then ``require()``.

::

    // In script "lib-Foo.js"

    module.exports = {
        foo = function() {
            console.log("bar");
        }
    }

::

    // In script "my-script.js"

    "phantombuster dependencies: lib-Foo.js";

    require("lib-Foo").foo(); // outputs "bar"

Locking a script's launch command
---------------------------------

If you want to make sure a script is always launched with the same command, add a ``phantombuster command`` directive (similar to ``"use strict";``).

::

    // Possible values are: casperjs, phantomjs and node
    "phantombuster command: node";
    "use strict";

    // The rest of your script...
    needle = require("needle");
