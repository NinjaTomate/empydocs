API Reference
=============

-  **Successful requests will always respond with 200 OK.** Any other
   response code (including 3XX redirects) should be considered a
   failure mode.
-  **The API will always return a JSON encoded response.**
-  **In the case of a failed request, the JSON response must have a
   ``message`` key containing a human-readable description of the
   failure.**

Whilst the last two rules are technically true from the perspective of
the empyrean-core project, there are some notable exceptions which may
occur in practice. Much like other WSGI programs, Empyrean is usually
deployed behind a production webserver like Nginx which may issue 3XX or
5XX responses with HTML bodies. Please be mindful of this when consuming
this API.

Another thing to be weary of when writing an application to consume the
Empyrean API is that some resources mightn't be where you might expect.
The default path for API endpoints for example is at ``/api``, but this
can be set up elsewhere (eg the official Empy instance has it at
https://api.empy.org/). For this reason, the providers list at
https://empy.org/providers.json specifies the full path to the APIs with
trailing ``/`` instead of just domain names. Its recommend that you
don't rely on either a separate subdomain or a particular path when
writing your applications.


Authentication
--------------

Authentication is by `HTTP Basic
Auth <https://en.wikipedia.org/wiki/Basic_access_authentication>`__,
either by providing username+password or by supplying the API key in the
username field and omitting a password.

The following are valid ways of authenticating via curl:

    | ``curl https://serve.empy.org/api/do/something --user <apikey>:``
    | ``curl https://serve.empy.org/api/do/something --user <email>:<password>``

Both forms are valid for most API endpoints, however some endpoints (ie
``/api/account/key`` and ``/api/account/pass``) will not accept an API
key as authentication for security reasons. While you *can* use a
username+password combo to authenticate using the other endpoints,
please note that this is **heavily discouraged**.

Checksums
---------

The checksums computed by Empyrean use the ``adler32`` function from
zlib as described on
`Wikipedia <https://en.wikipedia.org/wiki/Adler-32#Example>`_ (ie
starting with ``1`` as an initial value with the final result being
displayed as a hex string, excluding the leading ``0x``). This value is
included with file info, can be queried (see below) and is used as the
file's ETag in the format ``adler32-<hash>``. Situations may arise in
which a file doesn't have a checksum attribute, so avoid relying on its
presence in your code.

API Endpoints
-------------

``/api/account``
^^^^^^^^^^^^^^^^

    | Requires authentication
    | Allowed methods: ``GET``

Returns all available information about the authenticated account.

Examples
~~~~~~~~

-  Auth with key (auth with username+password is identical except for
   the ``auth`` key which would have the value ``"password"``)

   ::

       {
            "file_limit": 200,
            "file_size_limit": 50,  // in megabytes
            "key": "0123456789abcdef0123456789abcdef0123456789abcdef0123456789abcdef"
       }

``/api/account/key``
^^^^^^^^^^^^^^^^^^^^

    | Requires username+password authentication
    | Allowed methods: ``GET``, ``PUT``

``GET`` will return the API key for the authenticated user, ``PUT``
will generate a new key for that user.

.. CAUTION::
   Generating a new API key is irreversible and will
   nullify the old key; all devices using that key will have to
   reauthenticate.

Examples
~~~~~~~~

-  ``GET`` with username+password

   ::

       {
            "key": "0123456789abcdef0123456789abcdef0123456789abcdef0123456789abcdef"
       }

-  ``PUT`` with username+password

   ::

       {
            "old": "fedcba9876543210fedcba9876543210fedcba9876543210fedcba9876543210",
            "new": "0123456789abcdef0123456789abcdef0123456789abcdef0123456789abcdef"
       }

``/api/account/pass``
^^^^^^^^^^^^^^^^^^^^^

    | Requires username+password authentication
    | Allowed methods: ``POST``

Sets a new password for authenticated account. The new password is
provided in a form field with the name ``np``.

Examples
~~~~~~~~

-  Auth with username+password

   ::

       {
            "message": "Password set"
       }

-  Auth with username+password and empty/non-existant ``np`` form field
   (HTTP status ``400``)

   ::

       {
            "message": "Password cannot be empty"
       }

``/api/files``
^^^^^^^^^^^^^^

    | Requires authentication
    | Allowed methods: ``GET``, ``POST``

``GET`` retrieves a list of uploaded files for the authenticated
account, ``POST`` is used to upload files (form field ``f``).

Examples
~~~~~~~~

-  Auth'd ``GET``

   ::

       {
            "count": 2,
            "files": [
                {
                    "checksum": "aaaaaaaa",
                    "id": "aBcDeFg012",
                    "date": "2015-03-18T14:51:57+00:00",
                    "filename": "important_document_2.pdf",
                    "mimetype": "application/pdf",
                    "preview": "https://serve.empy.org/preview/aBcDeFg012.pdf",
                    "size": 356289,  //size in bytes
                    "url": "https://serve.empy.org/aBcDeFg012.pdf"
                },
                {
                    "checksum": "ffffffff",
                    "id": "0123456xYZ",
                    "date": "2015-02-11T11:03:47+00:00",
                    "filename": "cool_pic.jpg",
                    "mimetype": "image/jpeg",
                    "preview": "https://serve.empy.org/preview/0123456xYZ.png",
                    "size": 56739,
                    "url": "https://serve.empy.org/0123456xYZ.jpg"
                }
            ]
       }

-  Auth'd ``POST``

   ::

       {
            "message": "File uploaded",
            "id": "354xy451gg",
            "url": "https://serve.empy.org/354xy451gg.txt"
       }

``/api/files/<file_id>``
^^^^^^^^^^^^^^^^^^^^^^^^

    | Requires authentication on ``DELETE``
    | Allowed methods: ``GET``, ``DELETE``

-  ``GET``: Retrieve information about ``file_id``. Without correct
   authentication and authorization (ie does this user own this file?)
   only very limited information will be displayed.
-  ``DELETE``: Delete file

Examples
~~~~~~~~

-  ``GET`` without auth

   ::

       {
            "message": "Failed to authenticate, displaying limited information",
            <file_id>: {
                "checksum": "ffffffff",
                "mimetype": "example/mimetype",
                "size": 1234
            }
       }

-  ``GET`` with auth

   ::

       {
            "checksum": "ffffffff",
            "id": <file_id>,
            "date": "2015-02-11T11:03:47+00:00",
            "filename": "cool_pic.jpg",
            "mimetype": "image/jpeg",
            "preview": "https://serve.empy.org/preview/<file_id>.png",
            "size": 56739,
            "url": "https://serve.empy.org/<file_id>.jpg"
       }

-  ``DELETE`` with auth

   ::

       {
            "message": "File deleted"
       }

-  Any method on non-existant ``file_id`` (HTTP status ``404``)

   ::

       {
            "message": "File not found"
       }

``/api/files/checksums``
^^^^^^^^^^^^^^^^^^^^^^^^

    | Requires authentication
    | Allowed methods: ``GET``

This returns a dictionary of checksums and file IDs in the hope that a
smaller return message will lower the latency for checks pre-upload.

Examples
~~~~~~~~

-  ``GET``

   ::

       {
            "aaaaaaaa": "aBcDeFg012",
            "ffffffff": "0123456xYZ"
       }

``/api/files/checksums/<search>``
^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^

    | Requires authentication
    | Allowed methods: ``GET``

This will return a list of files matching the checksum sent in the url
(eg ``/api/checksums/ffffffff``).

Examples
~~~~~~~~

-  ``GET``

   ::

       {
            "results": [
                {
                    "checksum": "ffffffff",
                    "id": "0123456xYZ",
                    "date": "2015-02-11T11:03:47+00:00",
                    "filename": "cool_pic.jpg",
                    "mimetype": "image/jpeg",
                    "preview": "https://serve.empy.org/preview/0123456xYZ.png",
                    "size": 56739,
                    "url": "https://serve.empy.org/0123456xYZ.jpg"
                }
            ]
       }

``/api/info``
^^^^^^^^^^^^^

    Allowed methods: ``GET``

Returns information about the service provider

Examples
~~~~~~~~

-  ``GET``

   ::

       {
            "contact": "bob@bob131.so",
            "provider": {
                "NAME": "Empy",
                "URL": "https://empy.org"
            },
            "stats": {
                "files": 368,
                "users": 8
            },
            "version": <short commit hash>
       }

Common failure modes
--------------------

There are several responses that are reused among the API endpoints for
situations where an action is invalid or unauthorized which are defined
in ``empyrean_api/error.py``. Any application that consumes Empyrean's
API should be prepared to catch any of these errors on any request.

File too large (HTTP status ``413``)
^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^

::

    {
        "message": "File too large"
    }

Server-side error (HTTP status ``5XX``)
^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^

This is an indication that your service provider has fucked up, and you
should send them many emails. Regardless, clients should try and handle
500 series errors as gracefully as possible.

Account disabled (HTTP status ``403``)
^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^

::

    {
        "message": "This account has been disabled"
    }

Login incorrect (HTTP status ``401``)
^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^

This might be caused by: - Trying to use an API key on an endpoint that
requires the username+password combo - A stale API key (using an API key
after a new one has been generated) - Incorrect formatting of the HTTP
Basic Auth header

::

    {
        "message": "Authentication credentials incorrect"
    }

Restricted mimetype (HTTP status ``403``)
^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^

This is triggered if the server detects an attempt to upload a file that
isn't in the user's allowed mimetype list.

::

    {
        "message": "This user is forbidden from uploading this file type"
    }

Does not own (HTTP status ``403``)
^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^

This response is sent if a state-changing operation is attempted on a
file belonging to another user.

::

    {
        "message": "This user does not own this file"
    }
