## Working with DynamoDB

A guide to working with DynamoDB

## Selecting a Primary Key

- The primary key can be formulated with a single partition key or optionally as a composite key by including a sort key.
- The more unique the partition key the more uniformly data will be distributed across partitions.
- Primary keys comprising of a single partition key limits the data retrieval options and may require more scan operations to retrieve data.
- Including a sort key in the primary key can assist with not only sorting but also data retrieval by providing additional search capabilities.
- DynamoDB uses a consistent hashing scheme

## Querying

Use query operations over scan.

- Scan grabs everything in the table returning a collection of items and attributes.
- Queries use sort and partition keys to locate data directly.
- Scan has O(n) performance as it needs to iterate the entire data set.
- Scan is charged based on the amount of data scanned and not on what data is returned.
- Parallel scans are possible to increase the speed of retrieval however this can drain read capacity quickly.
- Local secondary indexes rely on the same partition key as the primary index but allow you to choose a different sort key.
- Global secondary indexes allow for the creation of a completely new composite key index.
- The smaller your requests are the less you are charged. Keep records small to consume minimal units needed. Choose small attribute values and names.
- Use [Document paths](https://docs.aws.amazon.com/amazondynamodb/latest/developerguide/Expressions.Attributes.html#Expressions.Attributes.NestedElements.DocumentPathExamples) in query expression when using map and/or list types instead of plain JSON strings.  


## Scaling

- DynamoDB has extreme scaling handling millions of queries per second.
- Supports tables of any size.
- Updates are instant

## PutItem

- By default PutItem returns no data.
- Use the ReturnValues parameter on input to instruct DynamoDB to return attributes. (options include ALL_OLD, ALL_NEW etc)
- Use conditional expressions to identify if a partition key already exists in the table thus preventing unexpected overwrites.
- There is no additional cost assoicated with requesting return value from PutItem operation. Aside from the processing overhead of receiving a larger response. No reach capacity units are consumed.
- PutItem only recognizes NONE or ALL_OLD values for ReturnValues attribute.
- 1 WCU per 1024bytes (1kb). Items can be up to 400kb creating a range between 1 and 400 WCU.
- Item size is sum of all its attributes sizes (name and value) and including hash and range keys.
- String and StringSet unicode with UTF-8 binary encoding. Consume between 1 and 4 bytes.
- 1 byte = English alphabet,numbers, punctuation , common symbols
- 2 bytes = pound sign, some languages like German
- 3 bytes = See docs , example Japenese language characters
- 4 bytes - emojis
- StringSet is a collection of strings - sum sizes of each string in the set

## DynamoDB Streams

- Streams are a mechanism for tracking changes to a table.
- Events can be streamed to DynamodDB streams or a Kineses steam
- Kinesis streams are more suitable for large volumes of data
- DynamodDB streams are suitable to track the granular changes to a table item
- DDB Streams are charged based on the amount of data streamed and not on what data is returned.
- DDB Streams are free to scale to millions of records per second.
- DDB Streams record all changees to all items in the exact order they happen
- When DDB streams is enabled different options exist to control the data being streamed
  - Keys only - only the key attributes of the modified item are streamed
  - New image - the entire item, as it became after it was modified, is streamed
  - Old image - the entire item, as it was just before it was modified, is streamed
  - New and old images - both the new and the old images of the item are streamed
- Streams data is retained for 24hours. After that if no consumer has read the data it will be lost.
- Lambda functions can be used to consume the stream data.
- Lambda polls the shards in the DynamodDB stream for records at a base rate of 4 times per second. 
- By default, Lambda invokes your function as soon as records are available. If the batch that Lambda reads from the event source has only a single record, Lambda will send only one record to the function. 
- To avoid invoking the function with a small number of records, you can configure Lambda to invoke the function only after a certain amount of time by configuring a batching window.
- Records are sent to Lambda either when the batching window expires or when the 6MB payload limit is reached.

## Choosing a UUID

- Use a UUID generator package based on [RFC4122](https://www.rfc-editor.org/rfc/rfc4122.html). No centralized authority is required to administer. The UUID's described in this specification are 128 bits long and can guarantee uniqueness across space and time. A Go implementation available from https://github.com/google/uuid.
- UUID generaters are less error prone then a solution using DynamoDB tables which need to coordinate the primary key counter using atomic ADD operations.

## Reeference Material

### Calculating Item size

| Partition key | Sort Key                                                                 | Attribute1 |
| ------------- | ------------------------------------------------------------------------ | ---------- |
| UUID          | COUNTRY-REG-CITY                                                         | Name       |
| 4 BYTES       | COUNTRY - 7 bytes, (-) 1 byte , REG - 3 bytes, (-) 1 byte CITY - 4 bytes | 4 bytes    |
|               | TOTAL 24 BYTES per header                                                |

| Sample Row                           | ---                      | ---        |
| ------------------------------------ | ------------------------ | ---------- |
| 14f5688d-0a7d-8b9d-122b-b6c63d90db23 | Ireland#Connaught#Galway | Joe Bloggs |
| 37 bytes                             | 24 bytes                 | 10 bytes   |
|                                      | Total 71 bytes           |

Total per item 95 bytes => 1WCU
