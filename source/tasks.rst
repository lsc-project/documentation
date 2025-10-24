********************
General task options
********************

The node **lsc>tasks** is the root node of all available LSC tasks. You may want to define as many ``<task>`` children as required, with a name subnode defining their name :

.. code-block:: XML

    <lsc>
      <tasks>
        <task>
          <name>taskName1</name>
          <.../>
        </task>
        <task>
          <name>taskName2</name>
          <.../>
        </task>
          <.../>
        <task>
          <name>taskNameN</name>
          <.../>
        </task>
      </tasks>
    </lsc>

A task contain the following parameters:

* **name**: Name of the task
* **bean**: Bean name:

    * ``org.lsc.beans.SimpleBean``: default bean, use this value if you don't want specific behavior
    * ``org.lsc.beans.OrderedValuesBean``: use this bean to force LSC to modify attributes if values order has changed

* **cleanHook**: fully qualified name of a static method to invoke after the clean task. This method can have a single TaskType parameter or no parameter.
* **syncHook**: fully qualified name of a static method to invoke after the synchronization task. This method can have a single TaskType parameter or no parameter.
* A source service (see :doc:`services <services>`)
* A destination service (see :doc:`services <services>`)
* **errorIfEmptySource**: boolean, display a log error if source contains no entry for sync task (-s) (default: true)
* **errorIfEmptyDestination**: boolean, display a log error if destination contains no entry for clean task (-c) (default: true)
* :doc:`Synchronization rules <syncrules>`
* **customLibrary**: load a Java library
* **scriptInclude**: include external script file, see :doc:`Scripting <scripting>`

