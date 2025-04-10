*********
Releasing
*********

Quick notes for developers wanting to release a version of LSC.

Prerequisites
=============

Make sure a branch is ready for release. This must involve testing by automated tests and a call for testing by developers and willing users.

Update version
==============

Version needs to be updated in:

* ``pom.xml``
* ``src/install/lsc-core.spec``
* ``src/install/debian/changelog``

Tagging
=======

Then tag the version:

.. code-block:: console

    git tag -a vVERSION -m "Tag for release VERSION"

Set back SNAPSHOT version in pom.xml.

Building
========

Build with:

.. code-block:: console

    mvn clean package

Distributing
============

We currently distribute the following files:

  * ``lsc-core-VERSION-src.tar.gz``: source archive
  * ``lsc-core-VERSION-dist.tar.gz``: distribution archive, containing all required dependencies
  * ``lsc-core-VERSION-installer.jar``: automated graphical installer to install the dist archive above

See also :doc:`packaging <packaging>`.

