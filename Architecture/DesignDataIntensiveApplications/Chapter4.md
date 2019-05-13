# Chapter 4 Encoding and Evolution

## Formats for Encoding Data
The translation from the in-memory representation to a byte sequence is called encoding(also know as serialization or marshalling), and the reverse is called decoding(parsing, deserialization, unmarshalling)

### Language-Specific Formats
The disadvantage is that the encoding-decoding method is tied with language.

### JSON, XML, and Binary Variants
* JSON and XML is human readable.
* There are a lot of ambiguity around the encoding of numbers.
* JSON and XML can support Unicode character but don't support binary strings
* CSV doesn't have any schema, so it is up to the application to define the meaning of each row and column.

There are some encoding method based on JSON or XML, to encoding to binary data. The data volume can be reduced a little.

### Thrift and Protocol Buffers
There are two different binary encoding formats, called BinaryProtocol and CompactProtocol.
The Thrift encoding language differs from JSON Binary encoding method, is that there is no field names in encoding data. Instead, the encoded data contains `field` tags, which are numbers. Field tags are like aliases for fields.

Protocol Buffers is similar to Thrift.

One detail is that: in the schema, each field was marked as required or optional, but not in the encoded data. The difference simply that required enables a runtime check that fails if the field is not set, which can be useful for catching bugs.

**Field tags and schema evolution **
Field tag is the key of schema evolution. Field tags are critical to the meaning of the encoded data. You can change the name of field, but you can't change field tag, since that would make all existing encoded data invalid.

You can add new field to schema, as long as you give each field a new tag umber. If old code read data written by new code, including a new field with a tag number, it can simply ignore that field. Therefore, the old code can read data that were written by new code.

What about backward compatibility? As long as each field has a unique tag number, new code can always read old data. As long as you mark new field as **optional**.

Removing a field is just like adding a field. But you can only remove a field that is optional.

**Datatypes and schema evolution **
Changing the datatype of a field maybe possible, but not all. Like 32-bit integer to 64-bit integer.

And Protocol Buffer has `repeated` option, this is to represent a `List`. Thrift has dedicated `List` type. The disadvantage is that for Thrift it's not possible to change `List` type to non-lit, the advantage is that it can support nested list.

### Avro
Design for Hadoop. No field tag specified. When decoding, the reader compare the writer schema and reader schema to make it compatible. So, the key is how the reader knows the writer's schema. There are several ways, but nothing interesting.  

### The Merits of Schemas

## Models of Dataflow

### Dataflow Through Databases

### Dataflow Through Servides: REST and RPC
** The problems with remote procedure calls (RPCs) **
The RPC model tries to make a request to a remote network service look the same as calling a function or method in your programming language, within the same process.

### Message-Passing Dataflow

## Summary
