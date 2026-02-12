products
=====

# 1. Brainstorm: Grapevine

Each of these instances is intended to calculate of baseline GrapeRank scores for a large number of customers. Its neo4j database will keep track of a large database of NostrUser nodes with FOLLOWS, MUTES, and REPORTS connections. Its memory requirements are anticipated to be expansive, making it prohibitive for the average nosrt user, which is why it is designed to serve multiple paying customers. Also, it will deal in publicly available, non-sensitive information.

## Progress
- prototype exists: straycat.brainstorm.social
- currently being rebuilt by Nostr Dev team

## tech stack
- neo4j
- auth: nostr

## NosFabrica monetization

Customers will pay for their scores to be calculated. Different tiers of service will exist:
- more frequent automatic updates (weekly, daily, hourly, etc)
- greater number of updates-on-demand
- more options for fine-tuning parameters (3 presets available for the free tier; paid tiers can make their own presets)

# 2. Brainstorm: Knowledge Graph

This differs from Brainstorm: Grapevine product in several important ways:
- personalized; one instance, one user
- the expectation is that it will contain private data and preferences
- the graph database is expected to be much smaller, require less memory, therefore more affordable for average users. Although optionally, they can get as large as the user wants/needs.

## Tech stack
- neo4j
- unclear whether this should be paired with a non-graph, high performance key-value database like LMDB
- Current plan is for the front end to be minimal, like the strfry front end. Day to day user access will be via a separate app or client -- see Product #3 (below).

## Progress
- several partial prototypes exist but none were ever complete; the closest to completion was when I was still working on IPFS, prior to my switch to nostr in Dec 2022

## NosFabrica monetization
hosting, similar to the Relay Tools monetization strategy. Self-hostring is possible, as is the case for nostr relays, but as is the case for relays, many users may opt for hosting services.

# 3. Brainstorm nostr client

Purpose: front end for Product #2. 

Not so much a product per se. This will be a true nostr client, pure front end, no back end, except that it will 
