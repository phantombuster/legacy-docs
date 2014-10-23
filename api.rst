API
===

Introduction
------------

The Phantombuster API is composed of HTTPS endpoints returning JSON data. Right now the API is not totally finished and is subject to major (breaking) changes. However it is usable and allows you to do interesting stuff with Phantombuster!

We deliberately made the API extremely simple to use. Any developer should be able to get responses in a matter of minutes.

Response format
---------------

All endpoints return JSON following the `JSend <http://labs.omniti.com/labs/jsend>`_ specification. It basically means all successfull responses have HTTP code ``200`` and look like this:

::

    {
        "status": "success",
        "data": {
            ...
        }
    }

Authentication and request format
---------------------------------

Authentication is dead simple: put your API key in the ``X-Phantombuster-Key-1`` HTTP header (or in the ``key`` parameter) of every request you make.

To get your API key, simply go to your `settings page <https://phantombuster.com/settings>`_ and click ``Reveal``.

Request parameters should be put in the HTTP ``GET`` query string, like a normal HTTP ``GET`` request. Here is how a typical request looks like:

::

    GET /api/v1/agent/785/launch.json?command=casperjs&saveLaunchOptions=1 HTTP/1.1
    Host: phantombuster.com
    X-Phantombuster-Key-1: YOUR_API_KEY

You can also put your API key as a ``GET`` parameter. This is not recommended because your key might show up in log files:

::

    GET /api/v1/agent/785/launch.json?command=casperjs&saveLaunchOptions=1&key=YOUR_API_KEY HTTP/1.1
    Host: phantombuster.com

Please be aware that your key is precious as anyone who knows it can launch your agents. Do not hesitate to generate a new one if you think it has been compromised.

Errors
------

If something bad happened, the HTTP code will be ``4XX`` or ``5XX`` and the response will look like this:

::

    {
        "status": "error",
        "message": "Description of what happened"
    }

The error response might also contain a ``code`` field and a ``data`` field describing the error in more details.

Here are some error HTTP codes you might encounter:

- ``400``: missing parameter or something else wrong with the given parameters
- ``401``: missing API key or wrong API key
- ``404``: the requested object was not found (bad ID?)
- ``500``: for some reason our servers could not handle your request

agent/{id}.json
---------------

::

    /api/v1/agent/{id}.json

Get an agent record.

``{id}`` (``Number``)
    ID of the agent to retrieve.

``withScript`` (``String``)
    If present and not empty, and if the agent has an associated script, also return the script record.

Sample response:

::

    {
        "status": "success",
        "data": {
            "id": 1763,
            "name": "Nice Agent",
            "scriptId": 1902,
            "proxy": "none",
            "proxyAddress": null,
            "proxyUsername": null,
            "proxyPassword": null,
            "disableWebSecurity": false,
            "ignoreSslErrors": false,
            "loadImages": true,
            "launch": "manually",
            "nbLaunches": 94,
            "showDebug": true,
            "awsFolder": "nVFRid8kvsuPeuCL80DnBg",
            "executionTimeLimit": 5,
            "fileMgmt": "folders",
            "fileMgmtMaxFolders": 10,
            "lastEndMessage": "Execution time limit reached",
            "lastEndStatus": "error",
            "userAwsFolder": "QwYH17CB0Xj",
            "script": {
                "id": 1902,
                "name": "nice_agent.coffee",
                "source": "phantombuster",
                "url": null,
                "text": " ... script contents ... ",
                "httpHeaders": null,
            }
        }
    }

agent/{id}/launch.json
----------------------

::

    /api/v1/agent/{id}/launch.json

Add an agent to the launch queue. This call always succeeds — to know if the agent was successfully added to the launch queue, call either ``/api/v1/agent/{id}/output.json`` or ``/api/v1/user.json``.

``{id}`` (``Number``)
    ID of the agent to launch.

``command`` (``String``)
    Command to use when launching the agent (optional). Can be either ``casperjs`` or ``phantomjs``.

``argument`` (``String``)
    JSON argument as a string (optional). The argument can be retrieved with ``buster.argument`` in the agent's script.

``saveLaunchOptions`` (``String``)
    If present and not empty, ``command`` and ``argument`` will be saved as the default launch options for the agent.

Sample response:

::

    {
        "status": "success",
        "data": null
    }

agent/{id}/abort.json
---------------------

::

    /api/v1/agent/{id}/abort.json

Abort a running agent.

``{id}`` (``Number``)
    ID of the agent to stop.

Sample response:

::

    {
        "status": "success",
        "data": null
    }

agent/{id}/output.json
----------------------

::

    /api/v1/agent/{id}/output.json

Get data from an agent: status, messages, console output, and launch number. You can call this endpoint multiple times and get only fresh output data by specifying ``fromMessageId``, ``fromOutputPos`` and ``launchNumber`` based on the last response you received.

``{id}`` (``Number``)
    ID of the agent from which to retrieve the output.

``fromMessageId`` (``Number``)
    Return the agent's messages starting from this ID (optional). If not present, returns a few last messages.

``fromOutputPos`` (``Number``)
    Return the agent's console output starting from this position (optional). If not present, assumes ``0``.

``launchNumber`` (``Number``)
    Get console output from a specific launch (optional). If not present, all the console output from the agent's output cache is returned. If specified and equal to the launch number of the currently running agent, the current console output is returned. If specified and not equal to the launch number of the running agent, the console output cache is returned (if available).

Sample response:

::

    {
        "status": "success",
        "data": {
            "status": "running",
            "launchNumber": 94,
            "messages": [
                {
                    "id": 65444,
                    "date": 1414080820,
                    "dateUtc": 1414080820,
                    "text": "Agent started",
                    "type": "normal"
                }
            ],
            "output": "* Container a255b8220379 started in directory /home/phantom/agent",
            "outputPos": 8
        }
    }

script/{id}.{ext}
-----------------

::

    /api/v1/script/{id}.{ext}

Get a script record.

``{id}`` (``Number``)
    ID of the script to retrieve.

``{ext}`` (``String``)
    

Sample response:

::

    {
        "status": "success",
        "data": null
    }
