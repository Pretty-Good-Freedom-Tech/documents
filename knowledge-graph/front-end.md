Brainstorm Knowledge Graph: Front end
=====

# Background

See the [products page](../products.md) for a description of the overall purpose of Brainstorm products and how they fit together to serve a unified purpose.

# Features

Users will be able to 
- create, edit, share, and explore simple lists
- flesh out simple lists into structured lists, object-oriented lists, and concepts
- integrate concepts vertically and horizontally

One of the most anticipated milestones will be the demonstration of loose consensus, whereby a community develops conventions / arrives at consensus over a variety of things in a quick, efficient, and fully self-sovereign manner.

# Architecture

Under the hood, user actions will be effected via:
- create, edit, and delete nostr events (major focus on kinds 9998, 9999, 39998, 39999 as per DLists Custom NIP, but all other event kinds will be subject to usage) that will be stored in a variety of nostr relays, community and personalized
- make changes to the user's neo4j graph database. During the development phase, the associated cypher queries will be written on the front end and transmitted via bolt+s, but over time the direct management of cypher queries may be shifted to the back end for a variety of reasons, including better security.

# NIPs

The starting point and basic building block is the [Decentralized Lists](https://nostrhub.io/naddr1qvzqqqrcvypzpef89h53f0fsza2ugwdc3e54nfpun5nxfqclpy79r6w8nxsk5yp0qyt8wumn8ghj7un9d3shjtnwdaehgu3wvfskueqqzdjx2cm9de68yctvd9ax2epdd35hxarn4nteah) (DLists) Custom NIP.

For a list of Custom NIPs that build upon DLists, including several still in draft form, see [this list of references](https://github.com/Pretty-Good-Freedom-Tech/brainstorm-references/blob/main/references.md).

# Development

The design of the Knowledge Graph will be driven by user stories, each of which is a problem with a WoT-based solution:
- There are too many impersonators, spammers, and other bad actors on nostr
- I can't distinguish a real nostr user from a bot
- I want an always-up-to-date list of Nostr Apps
- My repo is flooded with a deluge of PRs and issues, and I need to know which ones are worth my time and attention.
- I want a list of songs to play on my podcast, Plebchain Radio
- Which bitcoin hardware wallets are the best?

... This list could go on and on. 

Plan: Get a high end mac Studio, set up OpenClaw, create a team of agents, flesh out each User Story, each of which will include detailed steps taken by users on the front end and what exactly will be happening on the back end. Each User Story will contribute to a wireframe, the tech stack, the design of architecture. Once all of these are sufficiently fleshed out, the team of agents will proceed with building the product.

# Preliminary implementations

### Vinney's [WoT Sandbox](https://dlist-ui.netlify.app/)

Relatively full implementation of DLists, allowing the user to create and edit list headers (kind 9998, 39998) and list items (kinds 9999, 39999).

However the Sandbox this does NOT integrate with neo4j in any fashion.

### straycat's [Brainstorm: Knnowledge Graph front end app](https://brainstorm-knowledge-graph.vercel.app/#/dashboard)

Incomplete implementation of DLists; it currently supports mutable list headers and items (kinds 39998, 39999) but not immutable (kinds 9998, 9999). 

However this DOES execute neo4j cypher queries via bolt.

# Cypher queries

- [setup](./setup.md)
- [retrieval](./retrieval.md)

