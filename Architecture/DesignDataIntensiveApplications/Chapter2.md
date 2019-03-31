# Chapter2 Data Models and Query Launguages
In this chapter, we will look at a range of general-purpose data models for data storage and querying. We will compare the relational model, the document model and a few graph-based models.

## Relational Model Versus Document Model

### NoSQL
Drivings behind the adoption of NoSQL:
* A need for greater scalability than relational databases can easily achieve, including very large datasets or very high write throughput
* A widespread preference for free and open source software over commercial database products
* Specialized query operations that are not well supported by the relational model
* Frustration with the restrictiveness of relational schemas, and a desire for a more dynamic and expressive data model.

### The Object-Relational Mismatch
Most application development today is done in object-oriented programming languages, which leads to a common criticism of the SQL data model: if data is stored in relational tables, an awkward translation layer is required between the objects in application code and the database model of tables, rows and columns.

Object-relational mapping(ORM) frameworks like ActiveRecord and Hibernate reduce the amount of work for the translation layer but they can't completely hide the differences.

For a data structure like a resume, which is mostly a self-contained document, a JSON representation can be quite appropriate. JSOn has the appeal of being much simpler than XML. Document-oriented databases like MongoDB support this data model.

JSON reduces the impedance mismatch between the application code and the storage layer. There are also problems with JSON as a data encoding format. The lack of schema...

### Many-to-One and Many-to-Many Relationships
Many-to-One and Many-to-Many relationships is hard to model even for relation model and document model.

### Are Document Databased Repeating History?
While many-to-many relationships and joins are routinely used in relational databases, document databases and NoSQL reopened the debate on how best to represent such relationships in a database. This debate is much older than NoSQL.

Hierarchical model has some remarkable similarities to the JSON model used by document databases. It represented all data as a tree of records nested within records, much like the JSON structure.  Like document databases, IMS worked well for one-to-many relationships, but it made many-to-many relationships difficult, and it didn't support joins. Developers had to decide whether to duplicate(denormalize) data or to manually resolve references from one record to another.

Various solutions were proposed to solve the limitations of the hierarchical model. The two most prominent were the relational model(which became SQL and took over the world) and the network model(which initially had a large following but eventually faded into obscurity)

**The network model**

The network model(also known as CODASYL model) was a generalization of the hierarchical model. In the tree structure of the hierarchical model, every record has exact one  parent; in the network model, a record could have multiple parents.

The links between records in the network model were not foreign keys, but more like pointers in a programming language. The only way of accessing a record was to follow a path from a root record along these chains of links. This was called an access path.

The problem was that they made the code for querying and updating the database complicated and inflexible. With both hierarchical and network model, if you didn't have a path to the data you wanted, you were in a difficult situation. You could change the access paths, but then you had to go though a lot of handwritten database query code and rewrite it to handle the new access paths. It was difficult to make changes to an appliction's data model.

**The relational model**

In a relational database, the query optimizer automatically deicdes which parts of the query to execute in which order, and which indexes to use. Those choices are effectively the "access path", but the big difference is that they are made automatically by the query optimizer, not by the application developer, so we rarely need to think about it.

### Relational Versus Document Databases Today
The main arguments in favor of the document data model are schema flexibility, better performance due to locality, and that for some applications it is closer to the data structures used by application. The relational model counters by providing better support for joins and many-to-one and many-to-many relationships.

Schema-on-read: Know the schema when the data is read. Schema-on-write: Know the schema when the data is written.

A hybrid of the relational and document models is a good route for databases to take in the future. 

## Query Languages for Data


## Graph-Like Data Models


## Summary
