*******************
Develop your plugin
*******************

A LSC plugin is defined as a third party component that can be:

* a source service
* a destination service
* a scripting language support
* a sync options implementation

There are multiple plugins, available under **LSC-project** project on `github <https://github.com/lsc-project?q=plugin&type=all&language=&sort=>`__

Service plugin
==============

A service plugin includes :

* a XSD extension to define the settings of the plugin service
* the plugin service which should implement either `IService <https://lsc-project.org/javadoc/latest/org/lsc/service/class-use/IService.html>`__ for a source service or `IWritableService <https://lsc-project.org/javadoc/latest/index.html?org/lsc/service/IWritableService.html>`__ for a destination service
* and maybe a connection definition if it isn't already defined in LSC

Sample service plugin
=====================

Consider looking at the *FooBarLscService* sample as described below. This fake service has been designed as an extension of a SimpleJdbcDestinationService to describe an embeddable LSC:

* Ordered List ItemFirst define the configuration extension format
* Generate the corresponding source:

.. code-block:: console

    $JAVA_HOME/bin/xjc -p com.foo.bar.generated \
        -b lsc.episode src/main/resources/etc/foobar-lsc-1.0.xsd

You will find the ``lsc.episode`` file inside the misc subdirectory of LSC source package.

* Load the configuration through the service constructor
* Configure it through a pluginDestinationService node in ``lsc.xml``
* Copy the XSD to your etc subdirectory (required to check the XML structure)
* Run it with flag ``-DLSC.PLUGINS.PACKAGEPATH=com.foo.bar.generated`` (so that JAXB parser could find the extended classes)
