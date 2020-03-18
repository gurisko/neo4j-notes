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
(a)--(b)
(a)-[r]->(b)
(a)-[r:REL_TYPE]->(b)
(a)-[r:TYPE1|TYPE2]->(b) // This only works on existing data
```

Many relationships can be expressed by using `*` followed by a number of a range such as:
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
