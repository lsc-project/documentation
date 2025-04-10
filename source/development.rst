*************************
Development Documentation
*************************

The following documentation is about developing LSC itself - not a LSC synchronization task. Make sure you've read all the :doc:`general documentation <index>` before continuing here!

Working environment
===================

As well as the general :doc:`Requirements <requirements>`, you need some extra tools to hack LSC - make sure you match these requirements:

* Java Virtual Machine (JDK) >= 1.8
* `Maven <http://maven.apache.org/download.html>`__ >=2 (Maven 3 recommended)
* Add the mvn command line wrapper to Maven in your path (through ``~/.bashrc`` or something equivalent)
* You can use Eclipse, NetBeans,... to edit the Java code. Integrating LSC and its Maven dependencies is described :doc:`here <ideintegration>`.

You should follow :doc:`our defined code style <codestyle>`.

Java API for LSC-core
=====================

The JavaDoc is generated from comments in LSC code. It needs a lot of improvement, all comments in code are very welcome!

* `https://lsc-project.org/javadoc/latest/ <https://lsc-project.org/javadoc/latest/>`__

This can be very useful to get a function name to call from SyncOptions in the configuration file (see `LscBean <http://lsc-project.org/javadoc/latest/org/lsc/beans/LscBean.html>`__, `AD <http://lsc-project.org/javadoc/latest/org/lsc/utils/directory/AD.html>`__, `ScriptableJndiServices <http://lsc-project.org/javadoc/latest/org/lsc/jndi/ScriptableJndiServices.html>`__, `DateUtils <http://lsc-project.org/javadoc/latest/org/lsc/utils/DateUtils.html>`__...

Development tools
=================

You can find LSC on GitHub: `https://github.com/lsc-project <https://github.com/lsc-project>`__. There you can fill bugs, see the history of commits,...

Running tests
-------------

.. note::

    Tests are run through Maven.

Run all tests:

.. code-block:: console

    mvn test

Run one test, named MyTest (note: don't put in the classpath here, write ``MyTest`` not ``org.lsc.MyTest``!):

.. code-block:: console

    mvn -Dtest=MyTest test

Don't run tests:

.. code-block:: console

    mvn -Dmaven.test.skip=true someOtherTarget


Building
========

Various builds can be done : 

* the standard build:

.. code-block:: console

    mvn clean package

* a build including PostgreSQL jar:

.. code-block:: console

    mvn -Ppostgresql clean package

Releasing
=========

See these notes on :doc:`doing a release <release>`.


Run LSC under Eclipse
=====================

This section covers:

* Integration of LSC in an Eclipse (netbeans) environment
* Installation of maven integrated plugin in ide environnement
* Running / debuging LSC

Please follow: :doc:`Integrated Development Environment Integration <ideintegration>`

