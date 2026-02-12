Brainstorm Knowledge Graph Back-end
=====

The core of the Brainstorm Knowledge Graph is the back end of the Knowledge Graph: a graph database.

Tech stack: Orly

## Modifications: 

### 1. enable bolt+s on Orly (now)

For now, during the early development phase, cypher queries will be delivered via the front end (see: [Brainstorm Knowledge Graph front end starter prototype](https://brainstorm-knowledge-graph.vercel.app)) to the back end via bolt+s. This will be the easiest and quickest way to proceed. The front end will require nostr log in via a whitelisted account.

Bolt access: `bolt://testorly.nosfabrica.com` or `bolt+s://testorly.nosfabrica.com` (we will probably want to spin up a new Orly instance at a url like `https://testorly-knowledge-graph.nosfabrica.com`)

Once enabled, I will test the connection from my Brainstorm Knowledge Graph front end starter prototype, [test page 7](https://brainstorm-knowledge-graph.vercel.app/#/helloWorld/testPage7). When I run this app locally, bolt (not bolt+s) works. But the deployed app requires SSL/TLS.

I managed to enable SSL/TLS for connections to `bolt+s://straycat.brainstorm.social:7687` on my Brainstorm 1.0 prototype (see [straycat.brainstorm.social](https://straycat.brainstorm.social) by making the following edits to `/etc/neo4j/neo4j.conf`:

```
# Enable Bolt with TLS
dbms.connector.bolt.enabled=true
dbms.connector.bolt.tls_level=REQUIRED
dbms.connector.bolt.listen_address=0.0.0.0:7687

# Point to existing SSL certificates
dbms.ssl.policy.bolt.enabled=true
dbms.ssl.policy.bolt.base_directory=/etc/letsencrypt/live/straycat.brainstorm.social
dbms.ssl.policy.bolt.private_key=privkey.pem
dbms.ssl.policy.bolt.public_certificate=fullchain.pem
dbms.ssl.policy.bolt.client_auth=NONE
```

I had to give permission for neo4j to read the certificates, not sure if Orly will need the same:

```
sudo chmod -R 755 /etc/letsencrypt/live/
sudo chmod -R 755 /etc/letsencrypt/archive/
```

Then restart neo4j.

However, I reverted the changes after noting that it broke some features of Brainstorm 1.0, so check to make sure this doesn't break Orly.

### 2. Pass cypher queries through API (soon but not needed immediately)

For better security, we will want to protect the neo4j database behind an API that will check nostr AUTH. Not a priority for now.

### 3. Automation (later)

We will eventually want to automate certain cypher queries from the back end to do things like check the knowledge graph for inconsistencies of the class thread rule.

### 4. Background processing (later)

To improve the performance of retrieval of frequently-requested data, we may wish to set up automated tasks that run cypher queries and cache the results in a paired database -- maybe Badger. (Example: fetch all elements of the set: Jazz Musicians.) But this is not yet a priority. We may want to wait until optimization is actually needed, lest we spend time building optimizations that turn out to be unnecessary.

