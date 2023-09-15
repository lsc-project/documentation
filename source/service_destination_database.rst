****************************
Database destination service
****************************

This destination service uses standard SQL commands to update the database.

The Database source service is using a :doc:`database connection <connection_database>` to get the various connection settings. The requests are served by :doc:`iBatis <database>` ORM engine.

Let's find below a sample and the parameters description:

.. code-block:: XML

    <lsc>
      <tasks>
        <task>
          <databaseDestinationService>
            <name>postgresql-src-service</name>
            <connection reference="jdbc-dst-conn" />
            <requestNameForList>getInetOrgPersonList</requestNameForList>
            <requestNameForObject>getInetOrgPerson</requestNameForObject>
            <requestsNameForInsert>
              <string>insertInetOrgPerson</string>
            </requestsNameForInsert>
            <requestsNameForUpdate>
              <string>updateInetOrgPerson</string>
            </requestsNameForUpdate>
           <requestsNameForDelete>
              <string>deleteInetOrgPerson</string>
            </requestsNameForDelete>
          </databaseDestinationService>
        </task>
      </tasks>
    </lsc>

* **requestNameForList**: mandatory, this is the SQL request name to list available objects in the destination database
* **requestNameForObject**: mandatory, this is the SQL request name to get the full object according to the datasets identifying it uniquely
* **requestsNameForInsert**: mandatory, this is a list of the requests' name that should be launched each time a new object is discovered
* **requestsNameForUpdate**: mandatory, this is a list of the requests' name that should be launched each time an updated object has to be synchronized
* **requestsNameForDelete**: mandatory, this is a list of the requests' name that should be launched each time an old object has to be removed

.. important::

    Please notice that:

        * if a transactional engine is available, all modification operations (INSERT, UPDATE, DELETE) are done in a transaction: thus if one of the request fails, the others are rolled back to keep a fully stable data model
        * there's no ``requestNameForNextId`` or ``requestNameForClean`` in this service (regarding the corresponding :doc:`database source service <service_source_database>`)


See then how to configure :doc:`XML persistence map <persistence>` with iBatis.

