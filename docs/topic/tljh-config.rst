.. _topic/tljh-config:

=====================================
Configuring TLJH with ``tljh-config``
=====================================

``tljh-config`` is the commandline program used to make configuration
changes to TLJH.

Running ``tljh-config``
======================`

You can run ``tljh-config`` in two ways:

#. From inside a terminal in JupyterHub while logged in as an admin user.
   This method is **recommended**.

#. By directly calling ``/opt/tljh/hub/bin/tljh-config`` as root when
   logged in to the server via other means (such as SSH). This is an
   advanced use case, and not covered much in this guide.

Set a configuration property
============================

TLJH's configuration is organized in a nested tree structure. You can
set a particular property with the following command:

.. code-block:: bash

   sudo tljh-config set <property-path> <value>


where:

#. ``<property-path>`` is a dot-separated path to the property you want
   to set.
#. ``<value>`` is the value you want to set the property to.

For example, to set the password for the DummyAuthenticator, you
need to set the ``auth.DummyAuthenticator.password`` property. You would
do so with the following:

.. code-block:: bash

   sudo tljh-config set auth.DummyAuthenticator.password mypassword


This can only set string and numerical properties, not lists.

Some of the existing ``<property-path>`` are listed below by categories:

**Authentication**

    Use ``auth.type`` to determine authenticator to use. All parameters
    in the config under ``auth.{auth.type}`` will be passed straight to the
    authenticators themselves.

**User Lists**

* ``users.allowed`` takes in usernames to whitelist

* ``users.banned`` takes in usernames to blacklist

* ``users.admin`` takes in usernames to designate as admins

**User Server Limits**

* ``limits.memory`` Specifies the maximum memory that can be used by each
  individual user. It can be specified as an absolute byte value. You can use
  the suffixes K, M, G or T to mean Kilobyte, Megabyte, Gigabyte or Terabyte
  respectively. Setting it to ``None`` disables memory limits.

  .. code-block:: bash

     sudo tljh-config set limits.memory 4G

  Even if you want individual users to use as much memory as possible,
  it is still good practice to set a memory limit of 80-90% of total
  physical memory. This prevents one user from being able to single
  handedly take down the machine accidentally by OOMing it.

* ``limits.cpu`` A float representing the total CPU-cores each user can use.
  1 represents one full CPU, 4 represents 4 full CPUs, 0.5 represents
  half of one CPU, etc. This value is ultimately converted to a percentage and
  rounded down to the nearest integer percentage,
  i.e. 1.5 is converted to 150%, 0.125 is converted to 12%, etc.
  Setting it to ``None`` disables CPU limits.

  .. code-block:: bash

     sudo tljh-config set limits.cpu 2

**User Environment**

    ``user_environment.default_app`` Set default application users are
    launched into. Currently can be set to the following values
    ``jupyterlab`` or ``nteract``

    .. code-block:: bash

       sudo tljh-config set user_environment.default_app jupyterlab

View current configuration
==========================

To see the current configuration, you can run the following command:

.. code-block:: bash

   sudo tljh-config show

This will print the current configuration of your TLJH. This is very
useful when asking for support!

Reloading JupyterHub to apply configuration
===========================================

After modifying the configuration, you need to reload JupyterHub for
it to take effect. You can do so with:

.. code-block:: bash

   sudo tljh-config reload

This should not affect any running users. The JupyterHub will be
restarted and loaded with the new configuration.

Advanced: ``config.yaml``
=========================

``tljh-config`` is a simple program that modifies the contents of the
``config.yaml`` file located at ``/opt/tljh/config.yaml``. ``tljh-config``
is the recommended method of editing / viewing configuration since editing
YAML by hand in a terminal text editor is a large source of errors.
