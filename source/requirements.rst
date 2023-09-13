************
Requirements
************

General requirements
====================

All you need to use LSC is a Java 1.8 virtual machine (JRE).

Download `latest JRE from Oracle <https://www.oracle.com/java/technologies/downloads/>`__ or install corresponding openjdk, packaged with your distribution (known to work with OpenJDK but not GCJ or Gnu Classpath Java environments).

If you use the LSC RPM, the Oracle Java RPM is needed as a dependency.

Production servers running regularly scheduled synchronizations will also need cron, or another task scheduler.

Setting up your environment
===========================

Java
----

Check that the ``JAVA_HOME`` environment variable is set up. If not, set it up with a command like this (adapt the path to your system):

.. code-block:: bash

    export JAVA_HOME=/usr/lib/jvm/java-8-openjdk-amd64

This may not be required if Java is directly installed on your system, and you can run java from anywhere:

.. code-block:: bash

    java -version

We advice to use the ``JAVA_HOME`` environment variable to be sure that LSC will work even when launched by a cron job.

Operating System
================

LSC is written in Java, and as such should run on any system with a JVM.

LSC has been successfully tested and used on various recent versions of GNU/Linux, MacOS X and Windows.

Shell environment
=================

LSC runtime does not require any particular shell (it is build upon /bin/bash).

On Windows, the sample script requires Cygwin.
