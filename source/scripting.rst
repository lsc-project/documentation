*********
Scripting
*********

You can use script in several locations of ``lsc.xml``:

* <mainIdentifier>
* <conditions>
* <dataset>

The available scripting languages in Java are:

+--------------+----------------------------------------------------+
| Java version | Available JS engine                                |
+==============+====================================================+
| Java 8       | Jscript evaluator (js), RhinoJS (rjs), Groovy (gr) |
+--------------+----------------------------------------------------+
| Java 11      | Jscript evaluator (js), RhinoJS (rjs)              |
+--------------+----------------------------------------------------+
| Java 17      | RhinoJS                                            |
+--------------+----------------------------------------------------+
| Java 21      | RhinoJS                                            |
+--------------+----------------------------------------------------+

In LSC 2.1, the default JS engine was Jscript evaluator (js).

Starting with **LSC 2.2**, the default JS engine is:

* **GraalJS** (bundled with LSC) (gjs), or if unavailable
* **Jscript evaluator** (js), or if unavailable
* **RhinoJS** (rjs)


You can force the JS engine used in a specific location by prepending the appropriate prefix, like in the following example (here, using **RhinoJS**):


.. code-block:: XML

 	<string>
		<![CDATA[rjs:
			var carl = srcBean.getDatasetValuesById("carLicense");
			var result = new java.util.ArrayList();
			for (var i=0;i<carl.size(); i++) {
				result.add(carl[i]);
			}	
			result.toString();
		]]>
	</string>

If no prefix is provided, the default JS engine will be used.

Scripting does not have to start with **CDATA**. Here is a single liner example, 
where we concatanate two strings, one of them being pulled from an attribute value:

.. code-block:: XML

	<dataset>
		<name>userPassword</name>
		<policy>FORCE</policy>
		<forceValues>
			<string>"secret" + srcBean.getDatasetFirstValueById("cn")</string>
		</forceValues>
	</dataset>

This snippet of code will use the default JS engine: **GraalJS**.

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


