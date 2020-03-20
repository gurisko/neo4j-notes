Cypher
======

## Patterns

### Nodes
A node is described using a pair of parentheses, typically with a name:
```
(n)
```

### Labels
Labels are sets of nodes.
A node can have any number of labels:
```
(p:Person:Programmer)
```

### Relationships
Relationships always have a direction.
A relationship must have exactly one relationship type.
The following are valid relationship patterns:
```
(a)-->(b)
(a)-[r]->(b)
(a)-[r:REL_TYPE]->(b)
(a)-[r:`TYPE WITH SPACES`]->(b)
(a)--(b) // This only works on existing data because it doesn't have a direction
(a)-[r:TYPE1|TYPE2]->(b) // This only works on existing data because it has more than one type
```

Many relationships can be expressed by using `*` followed by a number or a range such as:
```
(a)-[:REL_TYPE*2]->(b)
(a)-[*3..5]->(b)
(a)-[*3..]->(b)
(a)-[:REL_TYPE*..5]->(b)
(a)-[*]->(b)
```

### Properties
```
(a {name: 'Andy', sport: 'Brazilian Ju-Jitsu'})
(a)-[{blocked: false}]->(b)
```

### Paths
A series of connected nodes and relationships is called a "path".
Paths can be named using an identifier:
```
p = (a)-[*3..5]->(b)
```

## Clauses

### CREATE
* Creating multiple nodes is done by separating them with a comma: `CREATE (n), (m)`.
* All parts of a pattern that are not already in scope at the time will be created.

Example:
```
CREATE (:User {name: 'Amy'})-[:FRIEND]->(:User {name: 'Joe'})-[:FRIEND]->(:User {name: 'Tom'})
```

### RETURN
* To return a property, use the dot separator `RETURN a.name` or squared brackets `RETURN a['name']` (a dynamic property expression). `null` value is returned if a property doesn't exist.
* To return all properties, use `RETURN *`.
* To rename a column, use `AS <new name>`: `RETURN a.name AS FirstName`.
* To return only unique results, use `RETURN DISTINCT a`.

`RETURN` has three sub-clauses that come with it:
1. [`LIMIT`](#limit)
2. [`SKIP`](#skip)
3. [`ORDER BY`](#order-by)

### MATCH
The clause is used to search for [the pattern](#patterns) described in it.

Example:
```
MATCH (m:Movie {title: 'The Matrix Reloaded'})-[]-(p:Person)
RETURN p AS `People Involved`
```

### OPTIONAL MATCH
It uses `null` for missing parts of a pattern.

### WHERE
The clause contains a predicate that acts as a constraint or a filter:
* it adds constraints to `MATCH` and `OPTIONAL MATCH` clauses,
* it filters results of a `WITH` clause.

Example:
```
MATCH (n)
WHERE n:Person
RETURN n.name, n.age
```

### WITH
The clause separates query parts explicitly, piping the results from one query to be used at the start of the next query.

`WITH` has three sub-clauses that come with it:
1. [`LIMIT`](#limit)
2. [`SKIP`](#skip)
3. [`ORDER BY`](#order-by)

Example:
```
MATCH (user)-[:FRIEND]-(friend)
WHERE user.name = $name
WITH user, count(friend) AS friends
WHERE friends > 10
RETURN user
```

### UNWIND
The clause expands a list into a sequence of records.

Example:
```
UNWIND [1, 2, 3] AS x
RETURN x
```

### ORDER BY
The clause sorts the result.

Example:
```
MATCH (n:Movie)
RETURN n.title, n.released
ORDER BY n.released DESC, n.title ASC
```

*Note: The value `null` is considered the largest value.*

### LIMIT
The clause limits the number of records in the output.
It accepts any expression that results in a positive integer if it doesn't use any variable referring to nodes or relationships.

### SKIP
The clause skips the number of records in the output.
It accepts any expression that results in a positive integer if it doesn't use any variable referring to nodes or relationships.
The clause always precedes `LIMIT` clause.

Example:
```
MATCH (n)
RETURN n.name
ORDER BY n.name
SKIP toInteger(3*rand())+ 1
LIMIT 1
```

### SET
The clause can be used for adding labels to a node and adding (removing) properties.
If the property value is `null`, the property is removed.

Examples:
```
SET n:Label1:Label2
SET n.property1 = value, n.property2 = value
SET n += {prop: value}
SET n = {prop: value} // This removes any existing properties
SET n.property = null // This removes a property
```

### REMOVE
It removes labels and properties:
```
REMOVE n:Person
REMOVE n.property
```

### DELETE
The clause removes a graph element:
```
MATCH (n:Person {name: 'Tom'})
DELETE n
```

Use `DETACH DELETE` to delete a node, the relationships to and from the node:
```
MATCH (n)
DETACH DELETE n
```
*(The query above deletes all data in the database. It is not for deleting large amounts of data though)*

### MERGE
The clause ensures that [a pattern](#patterns) exists in the graph.

Example:
```
MATCH (oliver:Person {name: 'Oliver'}), (rob:Person {name: 'Rob'})
MERGE (oliver)-[:DIRECTED]->(movie:Movie)<-[:ACTED_IN]-(rob)
RETURN movie
```

> :warning:
`MERGE` can also be used with undirected relationship!
When it needs to create a new one, it will pick a direction.

The clause allows to specify behaviour when merging with:
* `ON CREATE` to assign values to an element being created,
* `ON MATCH` to update an element's values when it is found:
```
MERGE (a:Person {name: 'Sir Michael Caine'})
ON CREATE SET a.born = 1934,
              a.birthPlace = 'UK'
ON MATCH SET a.birthPlace = 'UK'
RETURN a
```
