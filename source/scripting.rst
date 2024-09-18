*********
Scripting
*********

You can use script in several locations of ``lsc.xml``:

* <mainIdentifier>
* <conditions>
* <dataset>

Supported scripting language are:

* Javascript (Nashorn interpretor) (``js:``)
* Javascript (Rhino interpretor) (``rjs:``), also with debugger (``rdjs:``)
* Javascript (Graal interpretor) (``gj:`` or ``js`` [JDK11+])
* Groovy (``gr:``)

.. note::
   The following JDKs are compatible with Nashorn:
   * ``openjdk:v1.8-11``
   * ``azul:v11-13``
   The following JDKs are compatible with GraalJS:
   * ``openjdk:v11``
   * ``azul:v13``
   * ``corretto:v15-v20``
   * ``graalvm-ce:v21``
   * ``graalvm-jdk:v21``

LSC objects
===========

You have access to LSC objects in script:

* srcBean: source entry
* dstBean: destination entry

Variables
=========

Some variables are provided by LSC and can be used in script:

* nocreate: dry run flag for create
* noupdate: dry run flag for update
* nodelete: dry run flag for delete
* nomodrdn: dry run flag for modrdn


External files
==============

You can load an external file and use functions inside your LSC script, thanks to ``<scriptInclude>`` markup.

For example, at the end of a task definition, insert:

.. code-block:: XML

    <task>
    
    ...
    
    <scriptInclude>
      <string>test.js</string>
    </scriptInclude>
    
    </task>

.. tip::

    You can add several files, by adding other ``<string>`` values


The file ``test.js`` must be in the configuration directory, else you should set the relative path (for example ``../test.js``) if the file is in another location.

Dummy example:

.. code-block:: js

    function test() {
            return "test-->";
    }

Then this method can be used in a dataset, for example:

.. code-block:: XML

    <dataset>
      <name>carLicense</name>
      <policy>FORCE</policy>
      <forceValues>
        <string>
          js:test()+srcBean.getDatasetFirstValueById(carLicense)
        </string>
     </forceValues>
    </dataset>


.. note::

    If the extension is .js, it is assumed to be a javascript file.
    It the extension is .groovy, .gvy .gy or .gsh, it is assumed to be a groovy file.

Debugger
========

You can launch Rhino debugger by using ``rdjs:`` instead of ``rjs:`` or ``js:``. This will open this window:

.. image:: images/lsc_js_debugger.png
   :alt: lsc_js_debugger
   :align: center

|

.. tip::

    Launch LSC in a single thread (option ``-t 1``) to avoid multiple debug windows.

