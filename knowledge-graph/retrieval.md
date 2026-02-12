Retrieval of Data from the Knowledge Graph
=====

This document provides an overview, with examples, of cypher queries used to retrieve data from the knowledge graph.

Note: the three canonical relationship types may be referenced either by their slug or by their alias:
- CLASS_THREAD_INITIATION a.k.a. IS_THE_CONCEPT_FOR
- CLASS_THREAD_PROPAGATION a.k.a. IS_A_SUPERSET_OF
- CLASS_THREAD_TERMINATION a.k.a. HAS_ELEMENT

# Return all class threads

```
MATCH classPath = (listHeader)-[:IS_THE_CONCEPT_FOR]->(superset:Superset)-[:IS_A_SUPERSET_OF *0..5]->()-[:HAS_ELEMENT]->()
RETURN classPath
```

# Return all class threads originating from the list header for dog

```
MATCH classPath = (listHeader)-[:IS_THE_CONCEPT_FOR]->(superset:Superset)-[:IS_A_SUPERSET_OF *0..5]->()-[:HAS_ELEMENT]->()
WHERE listHeader.uuid = "39998:e5272de914bd301755c439b88e6959a43c9d2664831f093c51e9c799a16a102f:b08502ed-9adf-42b4-9e10-ef3090179346"
RETURN classPath
```

# Return all Irish Setters

```
MATCH classPath = (listHeader)-[:IS_THE_CONCEPT_FOR]->(superset:Superset)-[:IS_A_SUPERSET_OF *0..5]->(subset:Set)-[:HAS_ELEMENT]->(dog)
WHERE listHeader.uuid = "39998:e5272de914bd301755c439b88e6959a43c9d2664831f093c51e9c799a16a102f:b08502ed-9adf-42b4-9e10-ef3090179346"
AND subset.uuid = '<uuid for the set of Irish Setters>'
RETURN dog
```

(this query may return duplicate results for the same dog; probably needs to be edited)

# Return all Musicians

```
MATCH classPath = (listHeader)-[:IS_THE_CONCEPT_FOR]->(superset:Superset)-[:IS_A_SUPERSET_OF *0..5]->()-[:HAS_ELEMENT]->(musician)
WHERE listHeader.uuid = '<uuid for musicians list header>'
RETURN musician
```

# Return all jazz Musicians

```
MATCH classPath = (listHeader)-[:IS_THE_CONCEPT_FOR]->(superset:Superset)-[:IS_A_SUPERSET_OF *0..5]->(subset)-[:HAS_ELEMENT]->(musician)
WHERE listHeader.uuid = '<uuid for musicians list header>'
AND subset.uuid = '<uuid for the set of all jazz musicians>'
RETURN musician
```

# Return all jazz Musicians authored by trusted pubkey

```
MATCH classPath = (listHeader)-[:IS_THE_CONCEPT_FOR]->(superset:Superset)-[:IS_A_SUPERSET_OF *0..5]->(subset)-[:HAS_ELEMENT]->(musician)
WHERE listHeader.uuid = '<uuid for musicians list header>'
AND subset.uuid = '<uuid for the set of all jazz musicians>'
OPTIONAL MATCH (author:NostrUser)-[:AUTHORED_BY]->(musician)
WHERE author.influence_user_pubkey > 0.1
RETURN musician
```

(not sure if that syntax is correct)

# Return all jazz Musicians authored by trusted pubkey AND upvoted by at least one trusted pubkey

```
MATCH classPath = (listHeader)-[:IS_THE_CONCEPT_FOR]->(superset:Superset)-[:IS_A_SUPERSET_OF *0..5]->(subset)-[:HAS_ELEMENT]->(musician)
WHERE listHeader.uuid = '<uuid for musicians list header>'
AND subset.uuid = '<uuid for the set of all jazz musicians>'
OPTIONAL MATCH (author:NostrUser)-[:AUTHORED_BY]->(musician)
WHERE author.influence_user_pubkey > 0.1
OPTIONAL MATCH (rater:NostrUser)-[:AUTHORED_BY]->(reaction:Event {kind: 7})-[:TAGGED_WITH]->(Tag {type: 'p'})-[REFERENCES]->(musician)
WHERE reaction.content = "+" AND rater.influence_user_pubkey > 0.15
RETURN musician
```

(This query is definitely not yet correct -- still needs work)

