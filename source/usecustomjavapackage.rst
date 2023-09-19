***********************
Use custom java package
***********************

Presentation
============

When scripting in LSC (see :doc:`Scripting <scripting>`), you can import a java package and use its java classes within LSC JS engine. All you need is to add your jar file and its dependencies into LSC lib folder and import your package at the beginning of your script.

This tutorial shows how to set up a "hello world" type call.

Create jar file
===============

* In a folder, create folder path ``com/myjar``
* Add a file ``Utils.java`` in myjar folder, containing :

.. code-block::

    package com.myjar;
    public class Utils {
        public static String helloWorld() {
            return "hello world";
        }
    }

* Compile : ``javac com/myjar/Utils.java``
* Package : ``jar cfv myjar.jar com.myjar.Utils com/myjar/Utils.class``

Adapt LSC to see and call your package class
============================================

* Place ``myjar.jar`` file into your ``%lsc_home%/lib`` folder
* Add this in your lsc.xml :

.. code-block:: XML

    <dataset>
        <name>sn</name>
        <forceValues>
        <string>js:importPackage(com.myjar);Utils.helloWorld();</string>
        </forceValues>
    </dataset>

* **Result**: The sn attributes of users are all populated with string "hello world".

