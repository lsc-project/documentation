****************
Upgrade from 2.1
****************

Install the new version, through packages or tarball. Here are some tips to adapt your current configuration to this new version.


Installation and requirements
=============================

As described in :doc:`installation <installation>` and :doc:`requirements <requirements>` sections, there are somes changes:

* LSC 2.2 is bundled only for RHEL-like systems 8 and 9, and debian-like systems 11 and 12
* LSC 2.2 packages now require **Java 21** for running. However, it is still compatible with java 11 and 17 if you build the code yourself.
* the GPG key signing the packages has changed to ``lsc-project-securoty.gpg``. You must import this new key.
* Nagios plugins are now in the nagios-plugins-lsc package, you must install it if you need them.
* HOME directory for lsc user has been moved from ``/etc/lsc`` to ``/var/lib/lsc``. The package should do the upgrade automatically, but if you have customized the HOME directory, you should do the change by yourself. In this case, take care to define a HOME directory writable by lsc user, else graalvm would fail to start.

Scripting
=========

The supported java versions aren't handling Javascript code the same way. If you have some javascript code in your tasks, be sure to read the [Scripting part](https://www.lsc-project.org/documentation/latest/scripting.html) of the documentation.

XML namespace
=============

Update all configuration files to use the 2.2 XML namespace:

.. code-block:: XML

    <lsc xmlns="http://lsc-project.org/XSD/lsc-core-2.2.xsd" revision="0">

Logging configuration
=====================

The ``logback.xml`` file has been improved.

You are encouraged to adapt your specific logback.xml configurations if you have modified them.

There are two evolutions:

1. Remove the logback initialization blathering at startup. Add these lines in the ``<configuration>`` section:

.. code-block:: XML

        <!-- Stop logback initialization blathering at startup -->
        <statusListener class="ch.qos.logback.core.status.NopStatusListener" />

2. The layout sections must now be surrounded by an encoder tag. You should add this <encoder> tag. For example:

.. code-block:: XML

        <!-- Standard output to console -->
        <appender name="CONSOLE" class="ch.qos.logback.core.ConsoleAppender">
                <encoder class="ch.qos.logback.core.encoder.LayoutWrappingEncoder">
                        <layout class="org.lsc.utils.output.LdifLayout">
                                <Pattern>%date{MMM dd HH:mm:ss} - %-5level - %message%n</Pattern>
                        </layout>
                </encoder>
        </appender>


Google Apps Destination service
===============================

This service is obsolete and has been removed in 2.2 version.

Audits
======

Audit configuration has been removed in 2.2.

If you have some configuration sections containing:

.. code-block:: XML

    <audits>
      <audit>
        ...
      </audit>
    </audits>

You must remove them and use :doc:`logback configuration <logging>` instead.

Behavior change for multi-valued attribute replacement
======================================================

Before version 2.2, during an entry update, LSC behavior was to systematically replace all values for attributes in FORCE policy.

This could lead to slow modifications, especially for entries with many values,
like groups holding many members. However, sometimes it's cheaper
to only add the missing values plus remove the extra values.

LSC 2.2 takes this problematic into consideration.

If (number of missing values + number of extra values) < number of expected values after modification, then LSC applies 2 operations:

* 1 add of missing values
* 1 delete of extra values

else, it performs a full replace with all values.
