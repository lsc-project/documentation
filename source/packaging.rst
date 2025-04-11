*********
Packaging
*********

LSC RPM
=======

LSC can be packaged as a RedHat package.

To achieve this, connect on a Fedora or a RedHat host and build a package through the classic Maven way in lsc source directory:

.. code-block::

    cd lsc ; mvn package ; cd ..

Create the rpm build structure and copy the required files:

.. code-block:: console

    cd 
    mkdir -p rpmbuild/{SOURCES,SPECS,RPMS,SRPMS,BUILD,BUILDROOT}
    cp target/lsc-core-X.Y-SNAPSHOT-dist.tar.gz rpmbuild/SOURCES
    cp src/install/lsc.spec rpmbuild/SPECS

Launch the build:

.. code-block:: console

    rpmbuild -ba rpmbuild/SPECS/lsc.spec

The corresponding packages will be available:

* ``rpmbuild/RPMS/i386/lsc-2.0.SNAPSHOT-1.fc15.i386.rpm``
* ``rpmbuild/SRPMS/lsc-2.0.SNAPSHOT-1.fc15.src.rpm``


LSC Debian packaging
====================

LSC can be packaged as a DEB package.

To achieve this, connect on a Debian or a Ubuntu host, build a package through the classic Maven way in lsc source directory:

.. code-block:: console

    cd lsc ; mvn clean package

Copy the debian build structure inside the dist directory:

.. code-block:: console

    cp -aR src/install/debian target/lsc-core-*-dist/lsc-*/

Launch the build:

.. code-block:: console

    cd target/lsc-core-*-dist/lsc-*/
    dpkg-buildpackage -b -kLSC

