==========================================
Creating the Data Model and Initialization
==========================================

:Author:
    Timothy Paustian
    
Right now all our module does is say, "Hello!." That's great, but we really want it to store some data. To be able to work with data, we need to program our data model into the code. With the use of Doctrine 2, the new standard in Zikula, this is very easy to do. Doctrine is an object relational mapper (ORM). An ORM will map objects that define our data model to tables in whatever database we choose to store our data in. In most cases, Zikula site used MySQL to store their data, but theorhetically other databases or even non-databases could be used for persistence of your data. By setting up these *Entity* classes, the module programmer never needs to thing about SQL code and leave all of that to Doctrine.


Data entry is only going to be allowed in the Admin interface if you have Edit, Delete or Admin premissions. But b