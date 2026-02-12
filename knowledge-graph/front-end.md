Brainstorm Knowledge Graph: Front end
=====

# NIPs

The starting point and basic building block is the [Decentralized Lists](https://nostrhub.io/naddr1qvzqqqrcvypzpef89h53f0fsza2ugwdc3e54nfpun5nxfqclpy79r6w8nxsk5yp0qyt8wumn8ghj7un9d3shjtnwdaehgu3wvfskueqqzdjx2cm9de68yctvd9ax2epdd35hxarn4nteah) (DLists) Custom NIP.

# Preliminary implementations

### Vinney's [WoT Sandbox](https://dlist-ui.netlify.app/)

Relatively full implementation of DLists, allowing the user to create and edit list headers (kind 9998, 39998) and list items (kinds 9999, 39999).

However the Sandbox this does NOT integrate with neo4j in any fashion.

### straycat's [Brainstorm: Knnowledge Graph front end app](https://brainstorm-knowledge-graph.vercel.app/#/dashboard)

Incomplete implementation of DLists; it currently supports mutable list headers and items (kinds 39998, 39999) but not immutable (kinds 9998, 9999). 

However this DOES execute neo4j cypher queries via bolt.
