Deploying Empyrean
==================
Table of Contents
-----------------

-  `Dependencies <#dependencies>`__

   -  `Distribution-specific
      dependencies <#distribution-specific-dependencies>`__

-  `Installation <#installation>`__

   -  `Step 0: Preparing <#step-0-preparing>`__
   -  `Step 1: Cloning the Repo <#step-1-cloning-the-repo>`__
   -  `Step 2: Creating the default config
      file <#step-2-creating-the-default-config-file>`__
   -  `Step 3: Configuring your
      webserver <#step-3-configuring-your-webserver>`__
   -  `Step 4: Configuring uWSGI <#step-4-configuring-uwsgi>`__
   -  `Step 5: Adding accounts <#step-5-adding-accounts>`__
   -  `Congratulations! <#congratulations>`__

Dependencies
------------

-  Python 3
-  Git
-  Mongodb
-  python3-pip
-  python3-xdg
-  xdg-utils
-  Nginx/Apache (optional)
-  screen (optional)

   -  installing those deps pulls in around 500MB of deps, why?

Distribution-specific dependencies
~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~

-  this space intentionally left blank

Installation
------------

Step 0: Preparing
~~~~~~~~~~~~~~~~~

| Create a new user to run Empyrean under
| Launch mongoDB.

Step 1: Cloning the Repo
~~~~~~~~~~~~~~~~~~~~~~~~

``$ git clone https://github.com/empyreanfs/Empyrean``

Step 1.1: Downgrade PyMongo if using Debian Jessie:
^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^

``$ pip3 install pymongo==2.8``

Step 2: Creating the default config file
~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~

::

    $ cd Empyrean/
    $ python3 WSGI.py

Step 2.1: Figuring out why mongoDB isn't working
^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^

-  try running ``$ mongod --smallfiles`` if you have a system with
   limited space
-  try running ``$ mongod`` instead of mongodb as a service to get some
   useful logs

Step 2.2: Customizing your config file
^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^

-  note that STORAGE\_PATH *has* to end in \_files because of a bug in
   nginx

Step 3: Configuring your webserver
~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~

Nginx:
^^^^^^

Configure Nginx to forward requests to Empyrean. Example config for
this:

::

    server {
            listen       80;
            server_name  empyrean.local;

            # file size limit
            client_max_body_size 100M;

            error_page 413 @toobig;
            location @toobig {
                return 413 '{"message": "File too large"}';
            } 
        
            error_page 502 @json;
            location @json {
                internal;
                return 502 '{"message": "Empyrean instance is currently down"}';
            }
        
            # this assumes your STORAGE_PATH is /srv/empyrean/_files/
            # only edit the root var, retain the location entry
            location /_files {
                internal;
                root /srv/empyrean/;
            }
        
            location / {
                include     uwsgi_params;
                proxy_set_header    Host $host;
                proxy_set_header    X-Real-IP $remote_addr;
        
                # edit to the socket uWSGI listens on
                uwsgi_pass  unix:///srv/empyrean/empy.sock;
            }
        }

Step 4: Configuring uWSGI
~~~~~~~~~~~~~~~~~~~~~~~~~

| Now you should configure uWSGI so that your server can actually deliver some content.
| An example file for an uwsgi.ini would be this:

::

    [uwsgi]
    module = WSGI:app

    uid = empyrean
    socket = /srv/empyrean/empy.sock
    chown-socket = empyrean:www-data
    chmod-socket = 660
    vacuum = true

    die-on-term = true

| Now to launch your Empyrean instance, simply execute
| ``# uwsgi --ini uwsgi.ini``
| in a screen session.

You should now be able to access your.server/admin and see an admin
login panel. If that is not the case, you may have done something wrong.

Step 5: Adding accounts
~~~~~~~~~~~~~~~~~~~~~~~

| You can now log in to the Yahweh admin panel at your.server/admin. The default credentials are admin:password, so you should probably change them by logging in, clicking the Yahweh tab, and entering a new password. After that is done, click the Empyrean button, select Accounts, and click "Create" in the new form. Input your email address and the password you'd like to set into the box and click submit and save.

| Your account has now been created and you are ready to use Empyrean.

Congratulations!
