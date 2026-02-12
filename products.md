Products
=====

Current plan for a slate of products based on the Tapestry model.

# Brainstorm

The Brainstorm suite of products provides the user with:

`
a personalized Nostr Knowledge Graph curated with the assistance of one's trusted, extended community. To do this, Brainstorm keeps track of your Grapevine. Your Grapevine identifies who is the most trustworthy, in any given context, to help you curate content, facts, and information.
`

# 1. Brainstorm: Grapevine

Each of Brainstorm instance is intended to calculate of baseline GrapeRank scores for a large number of customers. Its neo4j database will keep track of a large database of NostrUser nodes with FOLLOWS, MUTES, and REPORTS connections. Its memory requirements are anticipated to be expansive, making it prohibitive for the average nostr user, which is why it is designed to serve multiple paying customers. Also, it will deal in publicly available, non-sensitive information.

## Progress
- prototype exists: straycat.brainstorm.social
- currently being rebuilt by Nostr Dev team

## tech stack
- neo4j
- auth: nostr

## NosFabrica monetization

Customers will pay for their scores to be calculated. Paid tiers will provide:
- more frequent automatic updates (weekly, daily, hourly, etc)
- greater number of updates-on-demand
- more options for fine-tuning parameters (3 presets available for the free tier; paid tiers can make their own presets)

# 2. [Brainstorm: Knowledge Graph (back end graph db)](./knowledge-graph/back-end.md)

This differs from Brainstorm: Grapevine product in several important ways:
- personalized; one instance, one user
- the expectation is that it will contain private data and preferences
- the graph database is expected to be much smaller, require less memory, therefore more affordable for average users. Although optionally, they can get as large as the user wants/needs.

## Tech stack / architecture
- lives on an always-connected device
- neo4j, with data model as per the tapestry protocol
- unclear whether this should be paired with a non-graph, high performance key-value database like LMDB
- Current plan is for the front end to be minimal, like the strfry front end, just lets you know it's up and running. Day to day user access will be via a separate app or client -- see Product #3 (below). (This architecture is subject to change)

## Future development
- could make versions that live on smartphones or other devices
- may pair with local LLM that can have access to curated data, help user search through it, etc.

## Progress
- several partial prototypes exist but none were ever complete; the closest to completion was when I was still working on IPFS, prior to my switch to nostr in Dec 2022

## NosFabrica monetization
hosting, similar to the Relay Tools monetization strategy. Self-hostring is possible, as is the case for nostr relays, but as is the case for relays, many users may opt for hosting services.

# 3. [Brainstorm nostr client (front end access to the knowledge graph)](./knowledge-graph/front-end.md)

Purpose: front end for Product #2 (and maybe also for Product #1?). True nostr clients; web app, smartphone apps. 

Why separate the front end (Product 3) from the back end (Product 2)? For the same reasons that relays and clients are separate entities in nostr.

