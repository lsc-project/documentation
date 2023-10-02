*************
Embedding LSC
*************

Context and goals
-----------------

Most applications include a user repository for most of the time identification, often authentication and sometimes authorization. This means that they should include interfaces and workflows for users management:

* creation
* updates
* archive or suppression
* password management, lockout and release


These applications are used in complex information systems where existing user directories and databases are managed for operating systems and other applications.
Writing a business application is not easy, but most of the time, software developers are not aware of the security constraints and users management workflow that is used by organizations and companies that will use their software

Authorization is most of the time embedded and manageed by the application because if the application is not trivial, it's complex or simply impossible to extract the authorization from the application data model.

But to simplify authentication, Single Sign On software and API are available and can be incorporated by business software to avoid dealing with every and all authentication factors available and used by their users. 

Quite interesting, but the application still needs to know the users and that's why LSC has been designed, initially outside the application, to retrieve the users information from an existing directory or database and to format and store into the application database. This is exactly what a ETL (Extract, Transform and Load) tool is doing.

But most of the time, writing directly to a database is annoying: database models are quite volatile: modern APIs are able to change it on the fly (Hibernate, JPA, Ruby, ...) and even without this behavior, each software update should be analyzed to see if a database schema update is included. And with this change, the synchronization format should be updated to fit to the new model! This fits well to stable schemas and old style conception and software design but not for new development lifecycle: RAD, SCRUM, Agile,...

That's why the LSC is now providing a new way to use it: embeddding it inside the application so that it can use the synchronization features to read users information and keep in sync any other referential according the application data model. Let's imagine that the application is a CRM: LSC can sync the company users to create, update and delete their profile inside the application database but also sync the contacts in the CRM with the company messaging system so that the messaging client can use the customer address book. For the application developer, it only requires binding the object to the LSC datamodel without writing a single line of code about external directories or databases synchronization !

In terms of code effort, lets imagine that you choose LSC to keep the application users in sync with the company's directory. The application connector to write is quite simple and should only be able to: 

* list the application users
* create, update or delete application users (including their account and personal information)


Of course, embedding LSC is most recommanded for Java application, but it could be done with every language: it *just* requires to keep the requests in sync, which is much more simplier for the application development team.


