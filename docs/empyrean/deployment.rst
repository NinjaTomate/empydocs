Deploying Empyrean
==================

Dependencies
------------

-  Python 3
-  MongoDB
-  (Optional) pip

Python
~~~~~~

(Installing pip and running ``pip install -r requirements.txt`` will
install all Python dependencies)

-  Flask
-  PyYaml
-  python-magic
-  uwsgi
-  Pillow
-  Flask-Security
-  Flask-Admin
-  Flask-SQLAlchemy
-  Flask-MongoEngine
-  Flask-CORS
-  bcrypt

Instructions
------------

(While this page is being updated, see
http://blog.bob131.so/2015/02/24/deploying-empyrean-redux.html)

:ref:`Configuration`

Yahweh :ref:`Yahweh first time setup`

Distribution-specific instructions
----------------------------------

Debian 8 and older
~~~~~~~~~~~~~~~~~~

pymongo versions later than 2.8 are known to not work.

Additional dependencies:

-  ``python3-cffi``

If using pip:

-  Install package ``python3-pip``
-  Run ``pip3 install -r requirements.txt``
-  Downgrade pymongo to version 2.8 by executing
   ``pip3 install pymongo==2.8``
