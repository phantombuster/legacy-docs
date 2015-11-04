Agent Module: Return data
=========================

These four methods allow you to return data from your agents in different ways.

Before these methods can be used, you need to :ref:`require() and create() the agent module <agent-module>`.

mail()
------

::

    buster.mail(subject, text [, to, callback])

Sends an email from your agent and substracts 1 to your daily email counter.

This method is asynchronous and returns nothing. Use the callback to know when it has finished.

``subject`` (``String``)
    Subject of the email.

``text`` (``String``)
    Plain text contents of the email.

``to`` (``String``)
    Where to send the email (optional). When omitted, the email will be sent to the address associated with your Phantombuster account.

``callback`` (``Function(String err)``)
    Function to call when finished (optional). When there is no error, ``err`` is *null*.

notify()
--------

::

    buster.notify(message [, options , callback])

Sends a push notification to your device(s) using Pushover. For this call to work, you must have set a Pushover user key in your `settings <https://phantombuster.com/settings>`_ and have installed a `Pushover client <https://pushover.net/clients>`_ on at least one of your devices.

This method is asynchronous and returns nothing. Use the callback to know when it has finished.

``message`` (``String``)
    Text contents of the notification.

``options`` (``PlainObject``)
    Additionnal parameters to send to Pushover. Get the full details at the `Pushover API documentation <https://pushover.net/api>`_.

    - ``device`` - your device name to send the message directly to that device, rather than all of your devices (multiple devices may be separated by a comma)
    - ``title`` - your message's title, otherwise *Phantombuster* is used
    - ``url`` - a supplementary URL to show with your message
    - ``url_title`` - a title for your supplementary URL, otherwise just the URL is shown
    - ``priority`` - send as ``-2`` to generate no notification/alert, ``-1`` to always send as a quiet notification or ``1`` to display as high-priority and bypass your quiet hours
    - ``timestamp`` - a Unix timestamp of your message's date and time to display, rather than the time the message is received by Pushover
    - ``sound`` - the name of one of the sounds supported by device clients to override your default sound choice

    Note: at the moment this method does not support the receipt system of Pushover (``priority`` set to ``2``).

``callback`` (``Function(String err)``)
    Function to call when finished (optional). When there is no error, ``err`` is *null*.

progressHint()
--------------

::

    buster.progressHint(progress [, label])

Reports the progress state of the agent. This affects the width and content of the progress bar displayed in the agent console on Phantombuster.

This is useful for debugging purposes and is not required for the agent to function properly. Sometimes it's just nice to see the progress of your agent in real-time.

This method returns nothing and has no callback.

``progress`` (``Number``)
    Progress float value between ``0`` and ``1``. ``1`` means 100% of the work was completed, and ``0`` means 0%.

``label`` (``String``)
    Optional textual description of the state of your agent (clipped to 50 characters). This shows up as a text inside the progress bar displayed in the agent console.

.. _buster-set-result-object:

setResultObject()
-----------------

::

    buster.setResultObject(object [, callback])

Sets (in fact, **replaces**) the result object of your agent. Think of the result object as the output value of your agent. This is useful for returning a small set of JSON fields containing whatever your agent might want to return when it exits.

Use this method in conjunction with the :ref:`launch <launch-an-agent>` API endpoint (with ``output`` set to ``result-object``) to launch **and** get the result of your agent in one single HTTP request.

This method is asynchronous and returns nothing. Use the callback to know when it has finished.

``object`` (``PlainObject``)
    Object to set as result object.

``callback`` (``Function(String err)``)
    Function to call when finished (optional). When there is no error, ``err`` is *null*.
