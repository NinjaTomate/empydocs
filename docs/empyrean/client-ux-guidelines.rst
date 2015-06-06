Client UX Guidelines
====================

This document is a continual work in progress, none of the following
rules are hard and fast. If your client doesn't comply, we can promise
that no sword-wielding ninja lawyers will hunt you down.

----

Clients must/should
-------------------

List of UX suggestions that clients really ought to follow

Leverage relevant sharing APIs for the client's target platform
~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~

Enabling simpler ways to interface with your client can shave seconds
off what would otherwise be a very repetitive and tedious workflow.
Using Android's share functionality or adding a context menu option to
Windows explorer.exe can vastly improve a user's experience

Allow the user to manually specify an Empyrean server to use
~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~

Users should be able to enter arbitrary URLs upon which the client's API
requests will be made. This allows users to use the same Empyrean apps
on their own private servers as others can do with Empy.org

Support both HTTP and HTTPS
~~~~~~~~~~~~~~~~~~~~~~~~~~~

Users should be able to select either HTTP or HTTPS to facilitate
private server deployment

Optional HTTPS cert verification
~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~

Verification of HTTPS certificates should be configurable, again to
facilitate private server deployment for those who do not wish to forgo
encryption

Display file upload progress
~~~~~~~~~~~~~~~~~~~~~~~~~~~~

Despite library support for this being patchy at best, its probably safe
to say that if you intend your tool to be used by the general public
then displaying file upload progress is a must

----

Clients may
-----------

List of UX suggestions that clients may opt to follow for a better
experience

Facilitate the creation of content to upload
~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~

There are plenty of temporary file storage sites around, but an
opportunity that is opened up by the design of Empyrean and similar
services is the streamlining of creating content and sharing it
immediately. This is what Empyrean was really designed for. Clients can
do this by eg implementing a screenshot feature or accepting text to
upload as a new file.

Check providers.json for available public Empyrean servers
~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~

Clients may query https://empy.org/providers.json for a list of known
public Empyrean servers and present this list to the user on setup

Test whether a file may already exist on the user's account
~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~

You can use the information provided by the Empyrean API about a user's
files to do some simple tests regarding the potential for a file to have
been already uploaded. [STRIKEOUT:This doesn't have to be a particularly
complicated or rigorous test, a simple filename match will do.]
[[Querying for checksums matching the file to be
uploaded\|API#api-endpoints\_api-checksums-search]] is the way this
should be implemented. If you think the file might already exist on the
server, notifying the user via a warning or a prompt (eg
``Continue/replace existing file/abort``) may just spare them having to
manage file duplication

On text file upload, return EmPaste link
~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~

If EmPaste is enabled on the Empyrean instance your client is
connecting to, when text files are uploaded the response will have an
additional attribute: ``paste_url``. Since the EmPaste web interface
does syntax highlighting, you may want to present this link to the
user rather than the link to the plain text file.

.. NOTE::
   If you do implement this, you shouldn't rely on EmPaste being enabled;
   It is disabled by default.
