.. _Gabriel_:

Gabriel
=======

Gabriel is the pure Javascript client for Empyrean hosted at
https://empy.org/gabriel.

Gabriel is currently in a state of near non-maintenance. Contributions
are more than welcome!

Project details
===============

| License: GPLv3
| Maintainer: `Bob131 <https://github.com/Bob131>`__
| Github: https://github.com/empyreanfs/gabriel

Deployment
==========

Gabriel (on the ``master`` branch) should be pretty easy to set up;
Clone the repo, amend the ``api_url`` line in ``/static/main.js`` to
point to your API and serve. Bon Appétit!

If you wish to mirror the setup on
`empy.org <https://empy.org/gabriel>`__, the ``gh-pages`` branch
includes some additional commits for user-customisable providers. If you
do redeploy this code, be warned that the CORS configuation on
``api.empy.org`` will refuse requests from origins other than
`empy.org <https://empy.org>`__.
