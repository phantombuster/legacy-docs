API
===

The Phantombuster API is composed of HTTPS endpoints returning JSON data.

We deliberately made the API extremely simple to use. Any developer should be able to get responses in a matter of minutes.

Versioning
----------

All API endpoints URLs start with ``https://phantombuster.com/api/vX/`` where ``X`` is the version number of the API. For now, only version ``1`` exists.

When breaking changes will be made to the API, this version number will increase.

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

Please be aware that your key is precious as anyone who knows it can launch your agents (and do other mean things). Do not hesitate to generate a new one if you think it has been compromised.

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

Add an agent to the launch queue.

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
        "data": {
            "containerId": 76426
        }
    }

agent/{id}/abort.json
---------------------

::

    /api/v1/agent/{id}/abort.json

Abort all running instances of the agent.

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

Get data from an agent: console output, status, progress and messages. This API endpoint is specifically designed so that it's easy to get incremental data from an agent.

Two types of requests can be made to this endpoint :

    - "Most Relevant" mode (by setting ``mode`` to ``most-relevant``, which is the default) to get console output from the most recent instance of the agent. In this mode, your first call should have parameter ``containerId`` set to ``0``. From then on, all subsequent calls must have parameter ``containerId`` set to the previously returned container ID (when a new instance of the agent is started, a different ``containerId`` will be returned).

    ~ or ~

    - "Specific Container" mode (by setting ``mode`` to ``specific-container``) to get console output from a particular instance of the agent. In this mode, requests must have the ``containerId`` parameter set to the instance's ID from which you wish to get console output.


``{id}`` (``Number``)
    ID of the agent from which to retrieve the output, status and messages.

``mode`` (``String``)
    Either ``most-relevant`` or ``specific-container`` (optional, ``most-relevant`` by default). This controls from which instance of the agent the output data is returned. In "Most Relevant" mode, the most recent instance is selected each time a request is made. In "Specific Container" mode, only the console output from a particular instance is returned, as specified by the ``containerId`` parameter.

``containerId`` (``Number``)
    ID of the instance from which to get console output (optional, ``0`` by default). In "Most Relevant" mode, always use the last ``containerId`` you received on a previous call or ``0`` for the first call. In "Specific Container" mode, always set this parameter to the instance's ID from which you wish to get console ouput.

``fromMessageId`` (``Number``)
    Return the agent's messages starting from this ID (optional, ``-1`` by default). If not present or ``-1``, no messages are returned. Use the biggest message ID you received on a previous call to only get fresh messages.

``fromOutputPos`` (``Number``)
    Return the agent's console output starting from this position (optional, ``0`` by default). This number roughly corresponds to the number of bytes emitted by the agent. Use the last ``outputPos`` you received on a previous call to only get new output data.

Sample response:

::

    {
        "status": "success",
        "data": {
            "agentStatus": "running",
            "runningContainers": 1,
            "queuedContainers": 0,
            "containerId": 76427,
            "progress": {
                "progress": 0.1,
                "label": "Initializing...",
                "runtime": 3
            },
            "messages": [
                {
                    "id": 65444,
                    "date": 1414080820,
                    "dateUtc": 1414080820,
                    "text": "Agent started",
                    "type": "normal",
                    "context": [
                        "Launch type: manual",
                        "Execution time limit: 60s"
                    ]
                }
            ],
            "output": "* Container a255b8220379 started in directory /home/phantom/agent",
            "outputPos": 245
        }
    }

script/by-id/{mode}/{id}
------------------------

::

    /api/v1/script/by-id/{mode}/{id}

Get a script record by ID.

``{id}`` (``Number``)
    ID of the script to retrieve.

``{mode}`` (``String``)
    Either ``json`` or ``raw``. If ``raw`` is used, the script is returned as raw text data, without any JSON.

Sample response:

::

    {
        "status": "success",
        "data": {
            "id": 1902,
            "name": "nice_agent.coffee",
            "source": "phantombuster",
            "url": null,
            "text": " ... script contents ... ",
            "httpHeaders": null,
        }
    }

script/by-name/{mode}/{name}
----------------------------

::

    /api/v1/script/by-name/{mode}/{name}

Get a script record by name.

``{name}`` (``String``)
    Name of the script to retrieve.

``{mode}`` (``String``)
    Either ``json`` or ``raw``. If ``raw`` is used, the script is returned as raw text data, without any JSON.

Sample response:

::

    {
        "status": "success",
        "data": {
            "id": 1902,
            "name": "nice_agent.coffee",
            "source": "phantombuster",
            "url": null,
            "text": " ... script contents ... ",
            "httpHeaders": null,
        }
    }

user.json
---------

::

    /api/v1/user.json

Get information about your Phantombuster account and your agents.

Sample response:

::

    {
        "status": "success",
        "data": {
            "email": "excellent.customer@gmail.com",
            "plan": {
                "key": "startup",
                "name": "Start-Up",
                "executionTime": 14400,
                "emails": 100,
                "size": 10000000000
            },
            "timeLeft": 14087,
            "emailsLeft": 100,
            "storageLeft": 9991347906,
            "agents": [
                {
                    "id": 1388,
                    "name": "My first agent",
                    "scriptId": 0,
                    "lastEndMessage": "Agent has no associated script",
                    "lastEndStatus": "launch failed",
                    "queuedContainers": 2,
                    "runningContainers": 0
                },
                {
                    "id": 1713,
                    "name": "My second agent",
                    "scriptId": 2003,
                    "lastEndMessage": "Agent finished with exit code 0",
                    "lastEndStatus": "success",
                    "queuedContainers": 0,
                    "runningContainers": 1
                }
            ]
        }
    }

mail.json
---------

::

    /api/v1/mail.json

Send an email to the address associated with your Phantombuster account and substract 1 to your daily email counter.

``subject`` (``String``)
    Subject of the email.

``text`` (``String``)
    Plain text contents of the email.

Sample response:

::

    {
        "status": "success",
        "data": null
    }
