****************************************
Database connection (databaseConnection)
****************************************

This section describe the `JDBC <http://en.wikipedia.org/wiki/JDBC_driver>`__ database connection settings. 

Let's look at a sample:

.. code-block:: XML

    <lsc>
      <connections>
        <databaseConnection>
          <name>jdbc-dst-conn</name>
          <url>jdbc:postgresql://127.0.0.1:5432/lsc</url>
          <username>lsc</username>
          <password>lsc</password>
          <driver>org.postgresql.Driver</driver>
        </databaseConnection>
      </connections>
    </lsc>

First, to be able to reference the connection in the service, it's mandatory to give it a unique name element so that it can be referenced later in services (here ``jdbc-dst-conn``).

* **url**: mandatory, this field shall contain a valid JDBC connection that fits to your database
* **username**: mandatory, this element shall contain the string that is used to identify against the database
* **password**: mandatory, this is the clear text value that is used to connect to the database
* **driver**: mandatory, this is the JDBC class name that is the entry point to your database driver
