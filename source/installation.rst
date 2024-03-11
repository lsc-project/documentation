************
Installation
************

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

LSC is packaged for RedHat/CentOS distributions. You can either install RPM manually, or use our YUM repository.

Using yum repository is encouraged.

Manual download
---------------

You can download the RPM here: `download area <https://lsc-project.org/download.html>`__.

The public key can be downloaded here: `http://ltb-project.org/wiki/lib/RPM-GPG-KEY-LTB-project <http://ltb-project.org/wiki/lib/RPM-GPG-KEY-LTB-project>`__

To import this key:

.. code-block:: console

    rpm --import http://ltb-project.org/wiki/lib/RPM-GPG-KEY-LTB-project

Just run this command to install them (with dependency management):

.. code-block:: console

    yum localinstall lsc*

Yum repository
--------------

.. important::

    Yum repositories are available for this release versions (``$releasever``):

    * 6/6Server
    * 7/7Server
    * 8/8Server

Configure the yum repository :

.. code-block:: console

    vi /etc/yum.repos.d/lsc-project.repo

.. code-block::

    [lsc-project]
    name=LSC project packages
    baseurl=http://lsc-project.org/rpm/$releasever/noarch
    enabled=1
    gpgcheck=1
    gpgkey=file:///etc/pki/rpm-gpg/RPM-GPG-KEY-LTB-project

Then update:

.. code-block:: console

    yum update

The public key can be downloaded here: `http://ltb-project.org/wiki/lib/RPM-GPG-KEY-LTB-project <http://ltb-project.org/wiki/lib/RPM-GPG-KEY-LTB-project>`__

To import this key:

.. code-block:: console

    rpm --import http://ltb-project.org/wiki/lib/RPM-GPG-KEY-LTB-project

You are now ready to install:

.. code-block:: console

    yum install lsc

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

    vi /etc/apt/sources.list.d/lsc-project.list

.. code-block::

    deb [signed-by=/usr/share/keyrings/lsc-project.asc]     https://lsc-project.org/debian lsc main
    deb-src [signed-by=/usr/share/keyrings/lsc-project.asc] https://lsc-project.org/debian lsc main

Then update:

.. code-block:: console

    apt-get update

The public key can be downloaded here: `http://ltb-project.org/wiki/lib/RPM-GPG-KEY-LTB-project <http://ltb-project.org/wiki/lib/RPM-GPG-KEY-LTB-project>`__

To import this key:

.. code-block:: console

    wget https://ltb-project.org/documentation/_static/RPM-GPG-KEY-LTB-project -O /usr/share/keyrings/lsc-project.asc

You are now ready to install:

.. code-block:: console

    apt-get install lsc

Files location
==============

With RPM or Debian packages, LSC files are installed in the following locations:

* ``/usr/bin/lsc``: wrapper to launch LSC
* ``/etc/lsc/``: configuration
* ``/var/log/lsc/``: logs
* ``/etc/init.d/lsc``: init script (for asynchronous connectors)
* ``/etc/default/lsc``: init script configuration
* ``/etc/cron.d/lsc``: cron configuration
* ``/usr/lib/lsc/``: LSC libraries
* ``/var/lib/lsc/nagios/``: monitoring scripts
* ``/usr/share/doc/lsc``: sample scripts

