Setup
=====

This document provides an overview of cypher queries that take as input kinds 9998, 9999, 39998, 39999 events and turns them into a Knowledge Graph, as per the tapestry protocol.

## Overview

The cypher commands below make use of several parameters that can be adjusted via [user settings](https://brainstorm-knowledge-graph.vercel.app/#/settings).

Each kind 9999 and 39999 events with z-tag: `uuid_for_relationships` (a hardcoded parameter) encodes a single relationship of a specific `relationshipType` between two Event nodes. For each relationship, we will use neo4j cypher to `MERGE` the relationship into the neo4j database. 

## Prerequisites:

Before running *bnkgi*, neo4j must first be populated with the relevant data. How to do that will be outlined in a separate document. (In a nutshell: import all kinds 7, 9998, 9999, 39998, and 39999 events from a relay such as `wss://dcosl.brainstorm.world` into Orly, and make sure that data gets transmitted into neo4j as Event nodes in the standard fashion.)

Note: we are deprecating `naddr` uuids in favor of `a-tag` uuids.

# Create Knowledge Graph:

Given an Orly-Neo4j relay:

1. Make sure all necessary parameters (relays, `uuid_for_relationships`, etc) are hardcoded. (These will be editable by the end user once we set up some kind of front end.)
2. Make sure the relay is populated with all available kinds 7, 9998, 9999, 39998, 39999 events (download from `aDlistRelays`)
3. Update Nodes (below)
4. Add Relationships (below)

## Update Nodes

For every Event node of kind: 9998 or 39998, add the node label: `ListHeader`.

```jsx
MATCH (listHeader:Event)
WHERE listHeader.kind = 9998 or listHeader.kind = 39998
SET listHeader:ListHeader
RETURN listHeader
```

For every Event node of kind: 9999 or 39999, add the node label: `ListItem`.

```jsx
MATCH (listItem:Event)
WHERE listItem.kind = 9999 or listItem.kind = 39999
SET listItem:ListItem
RETURN listItem
```

For each `ListHeader` and `ListItem`, add the property: `uuid` which is either the event `id` or the `naddr` of that event.

```jsx
MATCH (n:Event)
WHERE n.kind = 9998 or n.kind = 9999
SET n.uuid = n.id
RETURN n
```

```jsx
MATCH (n:Event)
WHERE n.kind = 39998 or n.kind = 39999
SET n.uuid = n.naddr
RETURN n
```

Or, if `naddr` is not already present: 

```jsx
MATCH (n:Event)-[:TAGGED_WITH]->(tag:Tag {type: "d"})
WHERE n.kind = 39998 OR n.kind = 39999
RETURN n.kind as kind, n.pubkey as pubkey, tag.value as dTag, n.id as eventId
```

Then iterate through each result, calculate `naddr` based on `pubkey`, `dTag`, and `kind`, and update:

```jsx
MATCH (n:Event {id: "<eventId>"})
SET n.naddr = "<naddr>", n.uuid = "<naddr>"
RETURN n
```

OR (if we decide for convenience to use the aTag in place of naddr):

```jsx
MATCH (n:Event)-[:TAGGED_WITH]->(tag:Tag {type: "d"})
WHERE n.kind = 39998 OR n.kind = 39999
WITH n, n.kind as kind, n.pubkey as pubkey, tag.value as dTag, n.id as eventId
SET n.aTag = kind + ":" + pubkey + ":" + dTag, n.uuid = kind + ":" + pubkey + ":" + dTag
RETURN n
```

For each of the following “canonical” Knowledge Graph node types, add node labels: `Set`, `Superset`, `JSONSchema`, `Property`, `Relationship` to every ListItem node that is connected to the relevant z-Tag:

```jsx
MATCH (listItem:ListItem)-[:TAGGED_WITH]->(:Tag {type: "z", value: "<uuid_for_sets"})
SET listItem:Set
RETURN listItem
```

Then do the same using for:

- Superset: `uuid_for_supersets`
- JSONSchema: `uuid_for_JSONSchemas`
- Property: `uuid_for_properties`
- Relationship: `uuid_for_relationships`

## Add Relationships

First, find every relationship using the cypher query:

```jsx
MATCH (relationship:ListItem)-[:TAGGED_WITH]->(tag:Tag {type: "z", value:"<uuid_for_relationships>"})
RETURN relationship
```

This should also work:

```jsx
MATCH (relationship:Relationship)
RETURN relationship
```

Next, iterate through each relationship:

1. Extract the values from each of these tags:
    - `nodeFrom`: should be the uuid of ListItem or ListHeader nodes.
    - `relationshipType`: should be the slug of one of the canonical relationship types, such as `CLASS_THREAD_INITIATION` or `ENUMERATION`
    - `nodeTo`: should be the uuid of ListItem or ListHeader nodes.
2. Add the relationship to neo4j using the neo4j cypher query:

```jsx
MERGE (nodeFrom:Event {uuid:"<nodeFrom>"})-[:<relationshipType>]->(nodeTo:Event {uuid:"<nodeTo>"})
```

ALTERNATIVELY, something based on this (but the MERGE statement needs to be fixed):

```jsx
MATCH (relationship:ListItem)-[:TAGGED_WITH]->(nodeFrom:Tag {type: "nodeFrom"})
OPTIONAL MATCH (relationship)-[:TAGGED_WITH]->(nodeTo:Tag {type: "nodeTo"})
OPTIONAL MATCH (relationship)-[:TAGGED_WITH]->(relationshipType:Tag {type: "relationshipType"})
WITH nodeFrom.value AS uuid_nodeFrom, nodeTo.value AS uuid_nodeTo, relationshipType.value AS label_relationshipType
MERGE (nodeFrom:Event {uuid:"<uuid_nodeFrom>"})-[rel:"<label_relationshipType>"]->(nodeTo:Event {uuid:"<uuid_nodeTo>"})
RETURN uuid_nodeFrom, label_relationshipType, uuid_nodeTo
```

FIX MERGE — OPTION 1: (needs APOC.merge; use `CALL apoc.help('merge')` to see if it is supported)

```jsx
MATCH (relationship:ListItem)-[:TAGGED_WITH]->(nodeFrom:Tag {type: "nodeFrom"})
OPTIONAL MATCH (relationship)-[:TAGGED_WITH]->(nodeTo:Tag {type: "nodeTo"})
OPTIONAL MATCH (relationship)-[:TAGGED_WITH]->(relationshipType:Tag {type: "relationshipType"})

WITH 
  nodeFrom.value AS uuid_nodeFrom,
  nodeTo.value   AS uuid_nodeTo,
  coalesce(relationshipType.value, "RELATED") AS relType   // fallback if tag is missing

MERGE (from:Event {uuid: uuid_nodeFrom})
MERGE (to:Event   {uuid: uuid_nodeTo})

CALL apoc.merge.relationship(from, relType, {}, {}, to, {}) 
YIELD rel

RETURN uuid_nodeFrom, relType, uuid_nodeTo, type(rel) AS createdRelType
```

FIX MERGE — OPTION 2: (needs very recent, late 2025 or early 2026 neo4j community edition with support for dynamic expressions like $relType)

```jsx
MATCH (relationship:ListItem)-[:TAGGED_WITH]->(nodeFrom:Tag {type: "nodeFrom"})
OPTIONAL MATCH (relationship)-[:TAGGED_WITH]->(nodeTo:Tag {type: "nodeTo"})
OPTIONAL MATCH (relationship)-[:TAGGED_WITH]->(relationshipType:Tag {type: "relationshipType"})

WITH 
  nodeFrom.value AS uuid_nodeFrom,
  nodeTo.value   AS uuid_nodeTo,
  coalesce(relationshipType.value, "RELATED") AS relType   // fallback if missing

MERGE (from:Event {uuid: uuid_nodeFrom})
MERGE (to:Event   {uuid: uuid_nodeTo})

MERGE (from)-[r:($relType)]->(to)

RETURN uuid_nodeFrom, relType, uuid_nodeTo, type(r) AS createdType
```

FIX MERGE — OPTION 3: Figure out each and every relType (using below minus the MERGE statements)

```jsx
MATCH (relationship:ListItem)-[:TAGGED_WITH]->(nodeFrom:Tag {type: "nodeFrom"})
OPTIONAL MATCH (relationship)-[:TAGGED_WITH]->(nodeTo:Tag {type: "nodeTo"})
OPTIONAL MATCH (relationship)-[:TAGGED_WITH]->(relationshipType:Tag {type: "relationshipType"})

WITH 
  nodeFrom.value AS uuid_nodeFrom,
  nodeTo.value   AS uuid_nodeTo,
  relationshipType.value AS relType

MERGE (from:Event {uuid: uuid_nodeFrom})
MERGE (to:Event   {uuid: uuid_nodeTo})

FOREACH (ignore IN CASE WHEN relType = 'IS_THE_CONCEPT_FOR'     THEN [1] ELSE [] END |
  MERGE (from)-[:IS_THE_CONCEPT_FOR]->(to)
)
FOREACH (ignore IN CASE WHEN relType = 'IS_A_SUPERSET_OF'   THEN [1] ELSE [] END |
  MERGE (from)-[:IS_A_SUPERSET_OF]->(to)
)
FOREACH (ignore IN CASE WHEN relType = 'HAS_ELEMENT'   THEN [1] ELSE [] END |
  MERGE (from)-[:HAS_ELEMENT]->(to)
)
FOREACH (ignore IN CASE WHEN relType = 'IS_A_PROPERTY_OF'      THEN [1] ELSE [] END |
  MERGE (from)-[:IS_A_PROPERTY_OF]->(to)
)
FOREACH (ignore IN CASE WHEN relType = 'IS_THE_JSON_SCHEMA_FOR'   THEN [1] ELSE [] END |
  MERGE (from)-[:IS_THE_JSON_SCHEMA_FOR]->(to)
)
FOREACH (ignore IN CASE WHEN relType = 'ENUMERATES'   THEN [1] ELSE [] END |
  MERGE (from)-[:ENUMERATES]->(to)
)
// Repeat for each possible relType value in your data
// ...
RETURN uuid_nodeFrom, relType, uuid_nodeTo
```

