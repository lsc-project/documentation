*************
Using plugins
*************

A plugin is a component that extends the capabilities of LSC in term of:

* new source / ldap services
* scripting language
* sync options implementation

There are multiple plugins, available under **LSC project** organization on `github <https://github.com/lsc-project?q=plugin&type=all&language=&sort=>`__

Official plugins
================

Executable
----------

This plugin enables any sysadmin to use LSC on a custom source or destination without writing Java code, only by wrapping each method through a script command.

See `documentation <https://github.com/lsc-project/lsc-executable-plugin/blob/master/README.md>`__

Fusion Directory
----------------

This plugin uses REST API of Fusion Directory to read and write data.

See `documentation <https://github.com/lsc-project/lsc-fusiondirectory-plugin/blob/master/README.md>`__

James
-----

The object of this plugin is to synchronize addresses aliases and users from one referential to a James server.

See `documentation <https://github.com/lsc-project/lsc-james-plugin/blob/master/README.md>`__

Microsoft Graph API
-------------------

The object of this plugin is to synchronize users from a Microsoft Azure Active Directory using the Graph API.

See `documentation <https://github.com/lsc-project/lsc-microsoft-graph-api-plugin/blob/master/README.md>`__

Usage
=====

Generally, for using a plugin, you need to:

* get the plugin (the .jar file) and put it inside LSC lib directory. (for example ``/usr/lib/lsc``)
* read the plugin documentation to understand how to configure the new parameters/options
* load the plugin, either by exporting ``JAVA_OPTS`` in your shell:

.. code-block:: console

    export JAVA_OPTS="-DLSC.PLUGINS.PACKAGEPATH=org.lsc.plugins.connectors.PLUGIN-NAME.generated"

or by adding the equivalent line in lsc launcher (usually in ``/usr/bin/lsc``)

* :doc:`run LSC <run>` the usual way

Develop your plugin
===================

See :doc:`Develop your plugin <plugin-dev>`
