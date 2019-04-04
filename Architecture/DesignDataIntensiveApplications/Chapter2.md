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
SQL is a **declarative** query language.

Many commonly used programming languages are **imperative** code. Like:
```js
function getSharks() {
  var sharks = [];
  for (var i = 0; i < animals.length; i++) {
    if (animals[i].family == "Sharks") {
      sharks.push(animals[i]);
    }
  }
  return sharks;
}
```
while SQL, it followed the structure of the relational algebra fairly closely:
```sql
select * from animals where family = 'Sharks'
```
An imperative language tells the computer to perform certain operations in a certain order. You can imagine stepping through the code line by line, evaluating conditions, updating variables, and deciding whether to go around the loop one more time.

In a declarative query language, like SQL or relational algebra, you just specify the pattern of the data you want: What conditions the results must meet, and how you want the data to be transformed(e.g., sorted, grouped, and aggregated) but not how to achieve the goal. It's up to the database system's query optimizer.

A declarative query language is attractive because it is typically more concise and easier to work with than an imperative API. And it also hides implementation details of the database engine, which makes it possible for the database system to introduce performance improvements without requiring any changes to queries.

Imperative code is very hard to parallelize across multiple cores and multiple machines, because it specifieds instructions that must be performed in a particular order. Declarative languages have a better change of getting faster in parallel execution because they specify only the pattern of the results, not the algorithm that is used to determine the results.

### Declarative Queries on the Web
In a web browser, using declarative CSS styling is much better than manipulating styles imperatively in JavaScript. Similarly, in databases, declarative query languages like SQL turned out to be much better than imperative query APIs.

### MapReduce Querying
Nothing to record, just introduce the map-reduce based query languages, like MongoDB

## Graph-Like Data Models
What if many-to-many relationships are very common in your data? The relational model can handle simple cases of many-to-many relationships, but as the connections within your data become more complex, it becomes more natural to start modeling your data as a graph.

A graph consists of two kinds of objects: vertices and edges.

### Property Graphs

In the property graph model, each vertex consists of:
* A unique identifier
* A set of outgoing edges
* A set of incoming edges
* A collection of properties(key-value pairs)

Each edge consists of:
* A unique identifier
* The vertex at which the edge starts(tail vertex)
* The vertex at which the edge ends(head vertex)
* A label to describe the kind of relationship between the two vertices
* A collection of porperties

### The Cypher Query Language
It's a declarative language for a Property Graph model. And this section provides an example.

### Graph Queries in SQL
It's very complicated when using SQL to query in a graph model. It means SQL is not suitable for graph model cases.

### Triple-Stores and SPARQL
I a triple-store, all information is stored in the form of very simple three-part statements:(subject, predicate, object). For example, in the triple(Jim, likes, bananas)

The subject of a triple is equivalent to a vertex in a graph. The object is one of the two things:
1. A value in a primitive datatype, such as a string or a number. In that case, the predicate and object of the triple are equivalent to the key and value of a property on the subject vertex. Like (lucy, age, 33) -> {"age": 33}
2. Another vertex in the graph. In this case, the predicate is the edge in the graph.

> I'm not interest in the following content in this section. Will come back later if need.

### The Foundation: Datalog
Datalog is a much older language. It provides the foundation that later query languages build upon.

Datalog's data model is similar to the triple-store model, generalized a bit. Instead of writing a triple as (subject, predicate, object), we write it as predicate(subject, object).
```datalog
name(namerica, 'North America')
type(namerica, continent)

within(usa, namerica)
```

This is another language processing data. An interesting model and query language.

## Summary
Historically, data started out being represented as one big tree (the hierarchical model), but that wasn't good for representing many-to-many relationships, so the relational model was invented to solve that problem. More rencently, developers found that some applications don't fit well in the relational model either. New nonrelational "NoSQL" datastores have diverged in two main directions:
1. Document databases target use cases where data comes in self-contained documents and relationships between one document and another are rare.
2. Graph databases go in the opposite direction, targeting use cases where anything is potentially related to everything.

One thing that document and graph databases have in common is that they typically don't enforce a schema for the data they store, which can make it easier to adapt applications to changing requirements. However your application most likely still assumes that data has a certain structure: it's just a question of whether the schema is explicit(enforced on write) or implicit(handled on read).
