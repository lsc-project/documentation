*************
Using plugins
*************

A plugin is a component that extends the capabilities of LSC in term of :

* new source / ldap services
* scripting language
* sync options implementation

There are multiple plugins, available under **LSC-project** project on `github <https://github.com/lsc-project?q=plugin&type=all&language=&sort=>`__


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
