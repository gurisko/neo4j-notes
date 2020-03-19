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

### MATCH
The clause is used to search for [the pattern](#patterns) described in it.

### RETURN
* To return a property, use the dot separator `RETURN a.name` or squared brackets `RETURN a['name']` (a dynamic property expression). `null` value is returned if a property doesn't exist.
* To return all properties, use `RETURN *`.
* To rename a column, use `AS <new name>`: `RETURN a.name AS FirstName`.
* To return only unique results, use `RETURN DISTINCT a`.
