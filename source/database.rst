*******************************
Database definition with Ibatis
*******************************


Source of this article: `http://www.mybatis.org/ <http://www.mybatis.org/>`__ ;

Introduction
------------

The iBATIS Data Mapper framework will help you to significantly reduce the amount of Java code that you normally need to access a relational database. iBATIS simply maps JavaBeans to SQL statements using a very simple XML descriptor.

Simplicity is the key advantage of iBATIS over other frameworks and object relational mapping tools. To use the iBATIS Data Mapper you only need to be familiar with JavaBeans, XML and SQL. There is very little else to learn. There is no complex scheme required to join tables or execute complex queries. Using Data Mapper, you have the full power of real SQL at your fingertips.

Data Mapper (com.ibatis.sqlmap.*)
=================================

Concept
-------

The iBATIS Data Mapper API allows programmers to easily map JavaBeans objects to ``PreparedStatement`` parameters and ``ResultSets``. The philosophy behind Data Mapper is simple: provide a simple framework to provide 80% of JDBC functionality using only 20% of the code.

.. image:: images/flow.jpg
   :alt: ibatis_flow
   :align: center


How does it work?
-----------------

Data Mapper provides a very simple framework for using XML descriptors to map JavaBeans, Map implementations, primitive wrapper types (String, Integer...) and even XML documents to a SQL statement. The following is a high level description of the lifecycle:

* Provide an object as a parameter (either a JavaBean, Map or primitive wrapper). The parameter object will be used to set input values in an update statement, or where clause values in a query,...
* Execute the mapped statement. This step is where the magic happens. The Data Mapper framework will create a ``PreparedStatement`` instance, set any parameters using the provided parameter object, execute the statement and build a result object from the ``ResultSet``.
* In the case of an update, the number of rows effected is returned. In the case of a query, a single object, or a collection of objects is returned. Like parameters, result objects can be a JavaBean, a Map, a primitive type wrapper or XML.

Resources
=========

* Define :doc:`XML persistence map <persistence>` in LSC

