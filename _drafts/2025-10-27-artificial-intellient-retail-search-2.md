---
title: 'Artificial Intelligent Retail Search is here! (2) - Data science '
date: 2025-10-27
permalink: /posts/2025/10/27/artificial-intelligent-search-2
tags:
  - search
  - ai
  - retail search
excerpt: In this article, I explore the evolution of retail search and attempt to predict the future of retail search
  
---


![Elastic/Solr](/images/elastic.png)


In the previous article, we introduced the retail search problem and discussed some of the early, legacy solutions. A general consensus about those systems is that they primarily relied on lexical similarity—matching search terms directly with document text. While lexical similarity plays an important role in product discovery, especially for returning or experienced users, it has clear limitations in a diverse marketplace. Customers from different regions and backgrounds search for products described and maintained by thousands of suppliers, each using varying terminologies, formats, and standards. Naturally, this diversity calls for a more semantic approach to improve both recall and precision.

During this phase, search engineers began exploring ways to combine lexical and semantic search capabilities, aiming to build hybrid systems that could better understand user intent while preserving the efficiency of traditional keyword search.

## Cloud-Based Search (Solr, Elastic, Google Cloud Search, Microsoft Cognitive Search, etc.)
Some of these cloud-based search solutions were still black boxes, but for the first time, many online retailers began building their own search stacks. With the availability of cloud-managed services, developing and maintaining search infrastructure was no longer an impossible task for mid-to-large retailers. Even those using black-box solutions—such as Google or Microsoft’s search platforms—often built custom wrappers around the APIs to enable routing, personalization, and domain-specific tuning.


During this period, there were several modernization efforts — integrating APIs, improving indexing, and adding analytics — but the focus of this article is specifically on search relevancy.

To understand this better, let’s look at the key projects roughly in the order of how a search unfolds — from the moment a query is fired to when the results are displayed to the user.

**Autocomplete, Spell Correction, and Suggest**: These often exist as independent but interconnected projects, sometimes owned by separate teams. They range from simple implementations based on customer engagement data to advanced models using learning-to-rank (LTR) or deep learning approaches. While these features are not part of the core search relevancy layer, they influence relevancy indirectly — for instance, by rewriting queries through spell correction, or by guiding users toward queries (via autocomplete suggestions) that are more likely to yield meaningful results rather than dead ends.

**Query normalization, rewrite, or synonym expansion**: Query normalization projects focus on cleaning and standardizing user queries — correcting spelling mistakes, handling singular and plural forms, and normalizing token order. These variations are often referred to as surface forms in literature. Normalization typically leverages a combination of Natural Language Processing (NLP) techniques and customer engagement signals to identify the most effective normalized representation of a query.

Query rewriting, on the other hand, involves replacing the original search term with an alternative expression known to produce more relevant results while preserving the user’s intent.

In synonym expansion projects, the system enriches the current query by adding related or equivalent terms to increase recall and uncover more relevant products. Identifying, validating, and integrating synonyms into the search system can itself be a significant effort, often requiring dedicated resources and domain expertise.

**Entity recognition (query understanding)**: This is one of the classical project for search because it interprets the user intent of a query to details that the underlying search system can understand. If search system is a catalog with product classified as product types and attributes, then the query understanding module convert the queries to product types and the attributes for that product type. Query normalization can really help here because user intent sometimes needs to be normalized and the normalized form can be easily translated to search system relevant data point.

**Customer engagement**: While retrieving the documents from the underlying search system, it is a good practise to pass the documents with customer behavior signals in the order such the organic search results will have those documents in the recall. This allows to boost the documents with customer enagement and also show features like facets and filters without any additional call. There are several projects under the umbrella of the customer behavior e.g., removing position bias, identifying the trending documents, boosting the seasonal documents, personalization based on region or peronnel purchase history. 

### Why are we migrating again?
Explain emerging limitations — static relevance, siloed data, limited understanding of user intent, and difficulty adapting to multimodal (text, voice, image) search.

