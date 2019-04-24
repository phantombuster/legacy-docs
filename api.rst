API
===

The Phantombuster API gives you control over your account. It is composed of HTTPS endpoints returning JSON data.

Here's a short list of what the API allows:

- Launch and abort agents
- Get console output, status, progress and messages from an agent
- Get real-time console output from an agent
- Get user, agent and script records
- Write scripts
- ...

We deliberately made the API extremely simple to use. Any developer should be able to get responses in a matter of minutes.

Versioning
----------

All API endpoints URLs start with ``https://phantombuster.com/api/v1/``.

Only version ``1`` exists for now.

Response format
---------------

All endpoints return JSON following the `JSend <http://labs.omniti.com/labs/jsend>`_ specification. It basically means all successfull responses have HTTP code ``2XX`` and look like this:

::

    {
        "status": "success",
        "data": {
            ...
        }
    }

Date/time fields are Unix/POSIX timestamps (in seconds).

Authentication and request format
---------------------------------

Authentication is dead simple: put your API key in the ``X-Phantombuster-Key-1`` HTTP header (or in the ``key`` parameter) of every request you make.

To get your API key, simply go to your `settings page <https://phantombuster.com/settings>`_ and click *Reveal*.

Parameters can be put in the query string or in the request body for ``POST`` requests. Here is how a typical request looks like:

.. code-block:: http

    GET /api/v1/agent/785/launch?command=casperjs&saveLaunchOptions=1 HTTP/1.1
    Host: phantombuster.com
    X-Phantombuster-Key-1: YOUR_API_KEY

You can also put your API key as a parameter. This is not recommended because your key might show up in log files:

.. code-block:: http

    GET /api/v1/agent/785/launch?command=casperjs&saveLaunchOptions=1&key=YOUR_API_KEY HTTP/1.1
    Host: phantombuster.com

Please be aware that your key is precious as anyone who knows it can launch your agents (and do other mean things). Do not hesitate to generate a new one if you think it has been compromised.

Errors
------

If something bad happens, the HTTP code will be ``4XX`` or ``5XX`` and the response will look like this:

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

Get an agent record
-------------------

::

    GET /api/v1/agent/{id}.json

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
            "maxParallelExecs": 1,
            "userAwsFolder": "QwYH17CB0Xj",
            "nonce": 123,
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

.. _launch-an-agent:

Launch an agent
---------------

::

    POST /api/v1/agent/{id}/launch

Add an agent to the launch queue.

This endpoint supports three types of outputs:

    - Standard JSON output (by setting ``output`` to ``json``, which is the default) to get back a ``containerId`` in JSON.

        This ID can later be used to track this launch and get console output by calling ``/api/v1/agent/{agentId}/output.json?containerId={containerId}``.

    **~ or ~**

    - Result object output (by setting ``output`` to ``result-object``) to get a blocking JSON response which will close when your agent finishes.

        The response will contain your agent's exit code (``Number``) and its result object (``PlainObject``) if it was set (using :ref:`buster-set-result-object`). This endpoint is very useful for getting a response from your agents "synchronously" — just make a single HTTP request and wait for your result object/exit code.

        Use ``first-result-object`` instead to have the request terminate immediately after the first call to :ref:`buster-set-result-object`. This is the fastest way to get a response from an agent using the API. However you will only get the result object and nothing else (no exit code or console output for example).

        Use ``result-object-with-output`` instead to get the console output of your agent in addition to all the other fields.

        Obviously this endpoint can be very slow to terminate (if your agent takes a long time or is queued). To prevent any risk of timeout, a space character is sent every 10 seconds to keep the HTTP socket alive (spaces do not prevent JSON parsing).

        Note: The HTTP headers are sent before your agent finishes, so you'll get a ``200 OK`` even if your agent fails during execution (but not if it fails to queue).

    **~ or ~**

    - `Event stream <https://developer.mozilla.org/en-US/docs/Server-sent_events/Using_server-sent_events>`_ output (by setting ``output`` to ``event-stream``) to get a ``text/event-stream`` HTTP response.

        Each line of console output is sent as an event stream message starting with ``data:``. When you receive the first message, you know the agent has started. When the agent has finished, the connection is closed. At regular intervals, event stream comments (starting with ``:``) are sent to keep the connection alive.

        `See a demo of this endpoint in action. <https://jsfiddle.net/papsss/0u1k9tob/>`_

    **~ or ~**

    - Raw output (by setting ``output`` to ``raw``) to get an HTTP ``text/plain``, chunked, streaming response of the raw console output of the agent.

        **This is not recommended** as almost all HTTP clients will timeout at one point or another, especially if your agent stays in queue for a few minutes (in which case the endpoint will send *zero* bytes for a few minutes, waiting for the agent to start — even cURL and Wget struggle to handle non-transmitting HTTP responses).

``{id}`` (``Number``)
    ID of the agent to launch.

``output`` (``String``)
    One of ``json``, ``result-object``, ``first-result-object``, ``result-object-with-output``, ``event-stream`` or ``raw`` (optional, default to ``json``). This allows you to choose what type of response to receive.

``command`` (``String``)
    Command to use when launching the agent (optional). Can be either ``casperjs``, ``phantomjs`` or ``node``.

``argument`` (``String``)
    JSON argument as a ``String`` (optional). The argument can be retrieved with ``buster.argument`` in the agent's script.

``saveLaunchOptions`` (``String``)
    If present and not empty, ``command`` and ``argument`` will be saved as the default launch options for the agent.

Note: ``command`` and ``argument`` work together. When setting one, always set the other. When one or both are set, the saved launch options of the agent are ignored.

Note: The ``GET`` HTTP method is also allowed for this endpoint.

Sample response of ``json`` output:

::

    {
        "status": "success",
        "data": {
            "containerId": 76426
        }
    }

Sample response of ``result-object`` output:

::

    {
        "status": "success",
        "message": "Agent finished (success)",
        "data": {
            "containerId": 76426,
            "executionTime": 17,
            "exitCode": 0,
            "resultObject": {
                "your": "data",
                "is": {
                    "here": [123]
                }
            }
        }
    }

Sample response of ``first-result-object`` output:

::

    {
        "status": "success",
        "data": {
            "containerId": 76426,
            "resultObject": {
                "your": "data",
                "is": {
                    "here": [123]
                }
            }
        }
    }

Sample response of ``result-object-with-output`` output:

::

    {
        "status": "success",
        "message": "Agent finished (success)",
        "data": {
            "containerId": 76426,
            "executionTime": 17,
            "exitCode": 0,
            "resultObject": {
                "your": "data",
                "is": {
                    "here": [123]
                }
            }
            "output": "This is a console output line!\r\nAnd this is another one :)\r\n"
        }
    }

Sample response of ``event-stream`` output:

.. code-block:: text

    : container 76426 in queue

    : container 76426 in queue

    data: This a console output line!
    data: 

    : container 76426 still running

    data: And this is

    data: another one :)
    data: 

    : container 76426 ended

Sample response of ``raw`` output:

.. code-block:: text

    This is a console output line!
    And this is another one :)

Abort an agent
--------------

::

    POST /api/v1/agent/{id}/abort.json

Abort all running instances of the agent.

``{id}`` (``Number``)
    ID of the agent to stop.

Note: The ``GET`` HTTP method is also allowed for this endpoint.

Sample response:

::

    {
        "status": "success",
        "data": null
    }

Get data from a running agent
-----------------------------

::

    GET /api/v1/agent/{id}/output.json

Get data from an agent: console output, status, progress and messages. This API endpoint is specifically designed so that it's easy to get incremental data from an agent.

This endpoint has two modes:

    - "Track" mode (by setting ``mode`` to ``track``, which is the default when a ``containerId`` is specified) to get console output from a particular instance of the agent.

        In this mode, requests must have the ``containerId`` parameter set to the instance's ID from which you wish to get console output.

    ~ or ~

    - "Most Recent" mode (by setting ``mode`` to ``most-recent``, which is the default when ``containerId`` is left at ``0``) to get console output from the most recent instance of the agent.

        In this mode, your first call should have parameter ``containerId`` left at ``0``. From then on, all subsequent calls must have parameter ``containerId`` set to the previously returned container ID (when a new instance of the agent is started, a different ``containerId`` will be returned).

``{id}`` (``Number``)
    ID of the agent from which to retrieve the output, status and messages.

``mode`` (``String``)
    Either ``track`` or ``most-recent`` (optional, defaults to ``most-recent`` if ``containerId`` is left at ``0``, otherwise defaults to ``track``). This controls from which instance of the agent the console output is returned. In "Most Recent" mode, the most recent instance is selected each time a request is made. In "Track" mode, the console output from a particular instance is returned, as specified by the ``containerId`` parameter.

``containerId`` (``Number``)
    ID of the instance from which to get console output (optional, ``0`` by default). In "Most Recent" mode, always use the last ``containerId`` you received on a previous call or ``0`` for the first call. In "Track" mode, always set this parameter to the instance's ID from which you wish to get console ouput.

``fromMessageId`` (``Number``)
    Return the agent's messages starting from this ID (optional, ``-1`` by default). If not present or ``-1``, no messages are returned. Use the biggest message ID you received on a previous call to only get fresh messages.

``fromOutputPos`` (``Number``)
    Return the agent's console output starting from this position (optional, ``0`` by default). This number corresponds to the number of bytes emitted by the agent. Use the last ``outputPos`` you received on a previous call to only get new output data.

Note: The ``agentStatus`` and ``containerStatus`` fields have 3 possible values: ``running``, ``queued`` or ``not running``.

Note: The ``containerStatus`` field is only present in "Track" mode and represents the status of the tracked agent instance.

Note: The ``resultObject`` field is only present when a result object was set using :ref:`buster.setResultObject() <buster-set-result-object>`.

Sample response:

::

    {
        "status": "success",
        "data": {
            "agentStatus": "running",
            "containerStatus": "running",
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
                    "text": "Agent started",
                    "type": "normal",
                    "context": [
                        "Launch type: manual",
                        "Execution time limit: 60s"
                    ]
                }
            ],
            "output": "* Container a255b8220379 started in directory /home/phantom/agent",
            "outputPos": 245,
            "resultObject": {
                "your": "data",
                "is": {
                    "here": [123]
                }
            }
        }
    }

Get container records
---------------------

::

    GET /api/v1/agent/{id}/containers.json

Get a list of ended containers for an agent, ordered by date. Useful for listing the last available output logs from an agent.

Container history is saved for up to 7 days.

``{id}`` (``Number``)
    ID of the agent from which to retrieve the containers.

Sample response:

::

    {
        "status": "success",
        "data": [
            {
                "id": 195119,
                "queueDate": 1427810471,
                "launchDate": 1427810471,
                "launchType": "automatic",
                "launchNumber": 476,
                "endDate": 1427812088,
                "lastEndMessage": "Agent finished (error)",
                "lastEndStatus": "error",
                "exitCode": 1
            },
            {
                "id": 195050,
                "queueDate": 1427806874,
                "launchDate": 1427806874,
                "launchType": "automatic",
                "launchNumber": 475,
                "endDate": 1427810029,
                "lastEndMessage": "Agent finished (success)",
                "lastEndStatus": "success",
                "exitCode": 0
            }
        ]
    }

.. _get-script-by-id:

Get a script by its ID
----------------------

::

    GET /api/v1/script/by-id/{mode}/{id}

Get a script record by its ID.

``{id}`` (``Number``)
    ID of the script to retrieve.

``{mode}`` (``String``)
    Either ``json`` or ``raw``. If ``raw`` is used, the script is returned as raw text data, without any JSON.

``withoutText`` (``String``)
    If present and not empty, do not send the script's contents but only its metadata (only in JSON mode).

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
            "lastSaveDate": 1427806874,
            "nonce": 123
        }
    }

.. _get-script-by-name:

Get a script by its name
------------------------

::

    GET /api/v1/script/by-name/{mode}/{name}

Get a script record by its name.

``{name}`` (``String``)
    Name of the script to retrieve, with its extension (``.js`` or ``.coffee``).

``{mode}`` (``String``)
    Either ``json`` or ``raw``. If ``raw`` is used, the script is returned as raw text data, without any JSON.

``withoutText`` (``String``)
    If present and not empty, do not send the script's contents but only its metadata (only in JSON mode).

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
            "lastSaveDate": 1427806874,
            "nonce": 123
        }
    }

List scripts
------------

::

    GET /api/v1/scripts.json

Get the list of all your scripts without text. To get a script contents, fetch it individually by its :ref:`ID <get-script-by-id>` or :ref:`name <get-script-by-name>`.

Sample response:

::

    {
        "status": "success",
        "data": [
            {
                "id": 450,
                "name": "script1.coffee",
                "source": "phantombuster",
                "url": "",
                "httpHeaders": null,
                "lastSaveDate": 1446562593,
                "nonce": 12
            },
            {
                "id": 452,
                "name": "script2.js",
                "source": "sdk",
                "url": "",
                "httpHeaders": null,
                "lastSaveDate": 1446562789,
                "nonce": 4
            }
        ]
    }

Delete a script
---------------

::

    DELETE /api/v1/script/{id}.json

Delete one of your script.

``{id}`` (``Number``)
    ID of the script to delete.

Sample response:

::

    {
        "status": "success",
        "data": null
    }

Update or create a script
-------------------------

::

    POST /api/v1/script/{name}

Update an existing script or create a new one if it does not exist (in this case, the new script ID is returned in the ``data`` field).

``{name}`` (``String``)
    Name of the script to update or create, with its extension (``.js`` or ``.coffee``).

``text`` (``String``)
    Full text contents of the script. This parameter must be in the request body in ``x-www-form-urlencoded`` format.

``insertOnly`` (``String``)
    If present and not empty, make sure that we don't update an existing script (optional). An error will be returned if a script with the same name already exists.

``source`` (``String``)
    Optional ``String`` describing from where the script comes from. Reserved sources keywords are ``phantombuster``, ``web``, ``sdk`` and ``bot builder``. Only 20 alpha-numeric characters (and space) are allowed.

Sample response:

::

    {
        "status": "success",
        "data": 345
    }

List running agents and account info
------------------------------------

::

    GET /api/v1/user.json

Get information about your Phantombuster account and your agents.

Sample response:

::

    {
        "status": "success",
        "data": {
            "email": "excellent.customer@gmail.com",
            "timeLeft": 14087,
            "emailsLeft": 100,
            "captchasLeft": 10,
            "storageLeft": 9991347906,
            "databaseLeft": 239222784,
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
                    "runningContainers": 1,
                    "progress": {
                        "progress": 0.544,
                        "label": "A progress label",
                        "runtime": 477
                    }
                }
            ]
        }
    }
