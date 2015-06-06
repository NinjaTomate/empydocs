.. _Configuration:

Configuration
=============

Quick rundown of the config options used by various components and how
they're processed by ``configparser``, listed in alphabetical order.
Configs are stored in ``config.yaml`` in the working directory of your
Empyrean instance.

On running Empyrean for the first time, it will generate a default
config containing defaults for the mandatory options listed below. This
may not be suitable for all use cases, so please read on.


Mandatory options
-----------------

Omission of any of the following options will cause Empyrean to fail to
start.

``DEFAULT_FILE_LIMIT``
^^^^^^^^^^^^^^^^^^^^^^

    | Type: ``int``
    | Default: ``200``

Specifies the default number of files an account is allowed to have
associated with it. This can be manually adjusted on an
account-by-account basis from [[Yahweh]]. If an account breaches their
file limit, Empyrean will automatically delete the oldest file before
committing the new file to disk.

``DEFAULT_FILE_SIZE_LIMIT``
^^^^^^^^^^^^^^^^^^^^^^^^^^^

    | Type: ``int``
    | Default: ``50``

Specifies the default maximum file size accounts can upload. This can be
manually adjusted on an account-by-account basis from [[Yahweh]].

``SECRET``
^^^^^^^^^^

    | Type: ``str``
    | Default: A new hmac hash of a random value

| This option is used primarily by [[Yahweh]] for acessing its user
  database, but may be used by other components in the future.
| Empy.org uses this secret to validate Github webhooks.

``STORAGE_PATH``
^^^^^^^^^^^^^^^^

    | Type: ``str``
    | Default: Directory ``_files`` in the current working directory

This option specifies the directory where uploaded files will be stored.
The path must exist and the user running Empyrean must have write
access, failing that Empyrean will fail to start.

Optional options
----------------

``ALLOWED_ORIGINS``
^^^^^^^^^^^^^^^^^^^

    | Type: ``str``
    | Default: ``"https://empy.org"``

This option sets the origins that browsers will allow access to the API
from as per the CORS standard. For more information, see
https://developer.mozilla.org/en-US/docs/Web/HTTP/Access\_control\_CORS.

``DISABLE_THUMBS``
^^^^^^^^^^^^^^^^^^

    | Type: ``bool``
    | Default: ``False``

As generating thumbnails from arbitrary user-supplied data can
constitute a quite serious security risk, there's a config option to
disable it. Thumbnails are enabled by default since this is likely the
typical use case. Thumbnails are stored in ``STORAGE_PATH`` + ``/thumbs/``.

.. NOTE::
    It may be useful to know that thumbnail generation is primarily
    reliant on system thumbnailers in ``/usr/share/thumbnailers/``,
    depending on ImageMagick's ``convert`` utility as a backup. If
    thumbnail generation is important to you, you may want to investigate
    what thumbnailing packages are available for your system and ensure
    ImageMagick is installed and in your ``PATH``.


``EMAIL``
^^^^^^^^^

    | Type: ``str``
    | Default: ``"N/A"``

This specifies the administrator contact email for your Empyrean
instance.

.. _EMPASTE_ENABLE:

``EMPASTE_ENABLE``
^^^^^^^^^^^^^^^^^^

    | Type: ``bool``
    | Default: ``False``

The option, when set to true, will enable the [[EmPaste]] module.

.. _EMPASTE_URL:

``EMPASTE_URL``
^^^^^^^^^^^^^^^

    Type: ``str``

Set this value if you intend to host EmPaste on a different domain or at
a different path. Empyrean uses this option to determine what the
complete URL is for pastes.

``MONGO``
^^^^^^^^^

    | Type: ``dict``
    | Default: ``{}``

This option is a dictionary that is passed straight to the
``MONGODB_SETTINGS`` parameter of the Flask app. See
https://flask-mongoengine.readthedocs.org/en/latest/ for more
information.

.. NOTE::
   If this option is omitted, the default will be to connect to MongoDB
   on ``localhost`` using the database named ``empyrean``.

``PROVIDER``
^^^^^^^^^^^^

    Type: ``dict``

This is where you can set information about your service to be displayed
to users. Should you decide to set this option, clients accessing this
will expect two lower-case keys: ``name`` and ``url``. ``name`` should
be a human-readable name for your service (eg ``Empy``) and ``url``
should be your service's home page (eg ``https://empy.org``). Omission
of this option will cause the request hostname to be returned instead.

``SERVE_URL``
^^^^^^^^^^^^^

    Type: ``str``

Set this value if you intend to host the API and the file server on
different domains. Empyrean uses this option to determine what the
complete URL is for files, and in this option's absence it will assume
its the same as the hostname the request was made on.

``WEB_REDIRECT``
^^^^^^^^^^^^^^^^

    | Type: ``str``
    | Default: ``"https://empy.org"``

This is the redirect that is returned by Empyrean when the user attempts
to access a path that they shouldn't, eg ``/`` or ``/api/``.

``YAHWEH_DISABLE``
^^^^^^^^^^^^^^^^^^

    | Type: ``bool``
    | Default: ``False``

You can use this value to disable the user management console. This may
be preferable if you have no need for a dedicated user management
console and wish to reduce attack surface or if you prefer an alternate
user management scheme.

Example configs
---------------

A plain config generated by Empyrean on first run:

::

    DEFAULT_FILE_LIMIT: 200
    DEFAULT_FILE_SIZE_LIMIT: 50
    SECRET: <some HMAC value>
    STORAGE_PATH: /home/gbarrett/dev/github/empyrean/_files

``config.yaml`` for Empy (with some slight edits):

::

    SECRET: <some secret>
    STORAGE_PATH: /var/www/empyrean/_files
    EMAIL: bob@bob131.so
    PROVIDER:
        name: Empy
        url: https://empy.org/
    DEFAULT_FILE_LIMIT: 200
    DEFAULT_FILE_SIZE_LIMIT: 50
    SERVE_URL: https://files.empy.org/
    EMPASTE_ENABLE: true
    EMPASTE_URL: https://paste.empy.org
