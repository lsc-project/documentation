************
Installation
************


Requirements
============

Make sure you followed the :doc:`requirements <requirements>`.


Tarball
=======

You can get the LSC distribution from the `download area <https://lsc-project.org/download.html>`__. Get zip or tar.gz archive, and unarchive it:

.. code-block:: console

    tar zxvf lsc-core-VERSION-dist.tar.gz

LSC is now installed in the subdirectory lsc-VERSION. The main files are:

* ``bin/lsc``: wrapper to launch LSC
* ``etc/lsc.xml``: main configuration file
* ``sample/``: sample scripts

Red-hat-like distribution
=========================

LSC is packaged for RedHat/CentOS/Rockylinux/Almalinux distributions. You can either install RPM manually, or use our YUM repository.

Using yum repository is encouraged.

Manual download
---------------

You can download the RPM here: `download area <https://lsc-project.org/download.html>`__.

The public key can be downloaded here: `https://lsc-project.org/documentation/latest/security/lsc-project-security.gpg <https://lsc-project.org/documentation/latest/security/lsc-project-security.gpg>`__

To import this key:

.. code-block:: console

    rpm --import https://lsc-project.org/documentation/latest/security/lsc-project-security.gpg

Just run this command to install them (with dependency management):

.. code-block:: console

    dnf install ./lsc*

Yum repository
--------------

.. important::

    Yum repositories are available for this release versions (``$releasever``):

    * 8/8Server
    * 9/9Server

Configure the yum repository :

.. code-block:: console

    vi /etc/yum.repos.d/lsc-project.repo

.. code-block::

    [lsc-project]
    name=LSC project packages
    baseurl=http://lsc-project.org/rpm/$releasever/noarch
    enabled=1
    gpgcheck=1
    gpgkey=file:///etc/pki/rpm-gpg/lsc-project-security.gpg

Then update:

.. code-block:: console

    dnf update

The public key can be downloaded here: `https://lsc-project.org/documentation/latest/security/lsc-project-security.gpg <https://lsc-project.org/documentation/latest/security/lsc-project-security.gpg>`__

To import this key:

.. code-block:: console

    rpm --import https://lsc-project.org/documentation/latest/security/lsc-project-security.gpg

You are now ready to install:

.. code-block:: console

    dnf install lsc

Debian
======

LSC is packaged for Debian/Ubuntu distributions. You can either install it manually, or use our APT repository:

Using apt repository is encouraged.

Manual download
---------------

You can download the package `here <https://lsc-project.org/download.html>`__.

Just run this command to install:

.. code-block:: console

    dpkg -i lsc*


APT repository
--------------

Configure the apt repository :

.. code-block:: console

    vi /etc/apt/sources.list.d/lsc-project.sources

.. code-block::

    Types: deb
    URIs: https://lsc-project.org/debian
    Suites: lsc
    Components: main
    Signed-By: /usr/share/keyrings/lsc-project.gpg

.. note::

    You can also use the old-style source.list format. Edit ``lsc-project.list`` and add::

        deb [arch=amd64 signed-by=/usr/share/keyrings/lsc-project.gpg] https://lsc-project.org/debian lsc main


The public key can be downloaded here: `https://lsc-project.org/documentation/latest/security/lsc-project-security.gpg <https://lsc-project.org/documentation/latest/security/lsc-project-security.gpg>`__

To import this key:

.. code-block:: console

    curl https://lsc-project.org/documentation/latest/security/lsc-project-security.gpg | gpg --dearmor > /usr/share/keyrings/lsc-project.gpg

Then update:

.. code-block:: console

    apt update


You are now ready to install:

.. code-block:: console

    apt install lsc

Files location
==============

With RPM or Debian packages, LSC files are installed in the following locations:

* ``/usr/bin/lsc``: wrapper to launch LSC
* ``/etc/lsc/``: configuration
* ``/var/log/lsc/``: logs
* ``/lib/systemd/system/lsc-*`` services and timers for sync and async connectors
* ``/etc/default/lsc-*``: default configuration for sync and async services
* ``/etc/cron.d/lsc``: cron configuration
* ``/usr/lib/lsc/`` or ``/usr/lib64/lsc/``: LSC libraries
* ``/var/lib/lsc/nagios/``: monitoring scripts
* ``/usr/share/doc/lsc``: sample scripts

