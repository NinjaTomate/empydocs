EmPaste
=======

EmPaste acts as a sort of pastebin, both to facilitate the serving of
anonymously uploaded pastes and serving of user-uploaded text files in
an interface that allows convenient versioning of existing files and
provides automatic syntax-highlighting powered by
`highlight.js <https://highlightjs.org/>`_. To enable EmPaste for your
instance, you will need to set the :ref:`EMPASTE_ENABLE` config option
to ``True`` and, optionally, create a user with the email address
``empaste@empy.org`` to enable anonymous pastes.

Its recommend you've read the :ref:`Yahweh` article and are comfortable
using it.

Setup
-----

1. Add ``EMPASTE_ENABLE: true`` to the end of your ``config.yaml`` file.
   This will start giving users links to EmPaste for text files but any
   attempt to upload a paste anonymously will fail.

2. [Optional] Set the :ref:`EMPASTE_URL` option to reflect where you
   intend to host EmPaste.

3. [Optional] Create a new user with the email address
   ``empaste@empy.org``. Its recommended that you restrict the allowed
   mimetypes of this account to ``text/plain``, set the maximum file
   size to a small value, clear the file limit field to allow
   unlimited text files to be added and disable logins. See below for
   an example:

   |New account|

.. NOTE::
   ``empaste@empy.org``'s mailbox is ``/dev/null``. Any mail sent to it
   will never be read.

   The password above is not important; That field is only filled out
   because a password is required to initialise a new account.



Thats it! You're done. Go check out your new pastebin!

.. |New account| image:: https://files.empy.org/DE0lJfFvup.png
