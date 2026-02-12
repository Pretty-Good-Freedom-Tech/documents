Add Relationship
=====

For a user to add a relationship to the knowledge graph, step 1 is to add an item to the list of relationships. As per the DLists Custom NIP, this requires publication of a kind 9999 or 39999 event. This event is subsequently processed by the relevant cypher query (found in the [setup page](./setup.md)) which recognizes it by its z-tag as a relationship that should be added to the neo4j database.
