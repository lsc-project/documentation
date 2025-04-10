************
Requirements
************

General requirements
====================

LSC requires a Java virtual machine (JRE).

LSC 2.2 requires Java >= 21

The recommended implementation is OpenJDK 21, but other distributions should work (Oracle for example).

Production servers running regularly scheduled synchronizations will also need cron, or another task scheduler.

RHEL-like
---------

On RHEL-like systems, install java with:

.. code-block:: console

    dnf install java-21-openjdk

debian-like
-----------

For debian-like systems, install java with:

.. code-block:: console

    apt install openjdk-21-jre

If unavailable (for debian 11 or 12 for example), you can install it with `Adoptium <https://adoptium.net/fr/temurin/releases/?os=linux&arch=x64>`__



Setting up your environment
===========================

Java
----

Check that the ``JAVA_HOME`` environment variable is set up. If not, set it up with a command like this (adapt the path to your system):

.. code-block:: console

    export JAVA_HOME=/usr/lib/jvm/java-21-openjdk-amd64

This may not be required if Java is directly installed on your system, and you can run java from anywhere:

.. code-block:: console

    java -version

We advice to use the ``JAVA_HOME`` environment variable if you want to be sure the LSC is always launched with the precise version you set.

Operating System
================

LSC is written in Java, and as such should run on any system with a JVM.

LSC has been successfully tested and used on various recent versions of GNU/Linux.

Shell environment
=================

LSC runtime does not require any particular shell (it is built upon /bin/bash).

On Windows, the sample script requires Cygwin.
