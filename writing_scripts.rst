Writing scripts
===============

Phantombuster allows you to write scripts in two languages: JavaScript and `CoffeeScript <http://coffeescript.org/>`_.

To create a new script, go to your `scripts page <https://phantombuster.com/scripts?createNew>`_, enter a filename ending in ``.js`` or ``.coffee`` and click `Create`.

Each script can be launched on the Phantombuster platform by one of the following commands:

- `CasperJS <http://casperjs.org/>`_ — Framework built on top of PhantomJS to easily write complex navigation scenarios
- `PhantomJS <http://phantomjs.org/>`_ — Headless, scriptable WebKit browser (where Phantombuster got its name from)
- `Node <https://nodejs.org/>`_ — Execute your scripts on Chrome's JavaScript runtime

Context of scripts
------------------

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

Lock a script's launch command
------------------------------

If you want to make sure a script is always launched with the same command, add a ``phantombuster command`` directive (similar to ``"use strict";``).

::

    // Possible values are: casperjs, phantomjs and node
    "phantombuster command: node";
    "use strict";

    // The rest of your script...
    needle = require("needle");
