+++
title = 'Introduction to Approximate Nearest Neighbor (ANN) Search: How Vector Search Works, Explained for Beginners'
description = 'A beginner-friendly guide to Approximate Nearest Neighbor (ANN) search, the technology behind RAG and recommendation systems. Covers indexes like HNSW and IVF, quantization, distance metrics, search parameters, and reranking.'
date = 2026-06-09T00:00:00+09:00
draft = false
categories = ["Engineering"]
tags = ["ANN", "Vector Search", "Machine Learning", "AI"]
+++

## Overview

RAG (Retrieval-Augmented Generation) systems that combine LLMs like ChatGPT with internal documents, "recommended for you" sections on e-commerce sites, similar-image search — behind all of these runs a technology called **vector search**. And what makes vector search fast enough for real-world use is the topic of this article: **ANN (Approximate Nearest Neighbor) search**.

In this article, I will explain what ANN is, why "approximate" is good enough, and walk through the six building blocks of an ANN system — indexes, quantization, distance metrics, search parameters, storage strategy, and pre/post-processing — in a way that beginners can follow.

## What Is ANN?

### Starting with Nearest Neighbor Search

Nearest neighbor search is the task of finding the data point closest to a given query point among a large collection of data.

In the context of vector search, documents and images are converted in advance into **embeddings** — arrays of numbers with hundreds to thousands of dimensions. Texts with similar meanings end up close to each other in this vector space. In other words, "finding semantically similar documents" can be reformulated as "finding nearby vectors in space."

The most naive approach is to compare the query vector against every single vector in the dataset, one by one. This is brute-force **kNN (k-Nearest Neighbors)** search. It always returns the exact answer, but with 100 million records you need 100 million distance computations — far too slow for real-time search.

### Trading Exactness for Speed

This is where ANN comes in. ANN takes the approach of "**giving up the guarantee of a 100% exact answer in exchange for returning a nearly correct answer orders of magnitude faster**."

Imagine looking for a book in a library. If you scan every shelf from end to end, you will definitely find the book — after several hours. In practice, you rely on the classification system: "cookbooks should be around here," and only check the shelves that look relevant. Occasionally you might miss a book that was shelved somewhere unexpected, but most of the time you find what you want in minutes. ANN turns this "narrow down first, then look" strategy into a data structure.

Search quality is measured by **recall**. For example, if you return 9 of the true top 10 results, your recall is 90%. In practice, the sweet spot is typically maintaining 95–99% recall while running hundreds to thousands of times faster than brute force.

## The Six Building Blocks of an ANN System

ANN is not a single algorithm but a combination of several techniques. Let's look at the six main components one by one.

### 1. Indexes (Search Data Structures)

At the heart of ANN is the index — a data structure built for fast search. Indexes fall into three major families.

**Graph-based** methods build a graph connecting nearby vectors to each other, then traverse the edges to home in on the neighbors. The most prominent example is **HNSW (Hierarchical Navigable Small World)**, currently the most widely used index. Like a highway network, it maintains both coarse long-distance links and fine-grained local links in a hierarchical structure. Other examples include NSG and Vamana (used in DiskANN).

**Tree-based** methods recursively partition the space using tree structures. Classic examples are **KD-tree** and **Annoy**, developed by Spotify.

**Hash-based** methods use special hash functions designed so that nearby vectors are likely to receive the same hash value. The representative technique is **LSH (Locality Sensitive Hashing)**.

In addition, **IVF (Inverted File Index)** is another popular approach: it clusters the data ahead of time and searches only the clusters closest to the query.

### 2. Quantization

Quantization compresses vectors to reduce memory usage and speed up distance computations. It is used in combination with indexes.

- **SQ (Scalar Quantization)**: converts each dimension from float32 to int8 or similar, compressing the data to roughly 1/4 the size
- **PQ (Product Quantization)**: splits each vector into multiple subspaces and encodes each part as the ID of a representative point (a code). It achieves high compression ratios and is a staple for large-scale data
- **BQ (Binary Quantization)**: compresses each dimension down to a single bit, enabling ultra-fast distance computation with bitwise operations

The more you compress, the more memory you save — but accuracy drops because information is lost. Here too, you are tuning a trade-off.

### 3. Distance Metrics

How you define "near" is another important choice. The main distance (similarity) metrics are:

- **Cosine similarity**: measures similarity by the angle between vectors. Commonly used for text embeddings
- **Euclidean distance (L2)**: the straight-line distance in space
- **Inner product (dot product)**: reflects both the direction and the magnitude of vectors

Which one to choose depends on the characteristics of the model that produced the embeddings. Most embedding models document their recommended metric, and following that recommendation is the standard practice.

### 4. Search Parameters (Tuning)

ANN indexes expose "knobs" for adjusting the trade-off between accuracy and speed.

For HNSW:

- `M`: the number of connections (edges) per node. Larger values improve accuracy but increase memory usage
- `ef_construction`: the number of candidates examined during index construction. Larger values produce a better graph but take longer to build
- `ef_search`: the number of candidates kept during search. Larger values improve recall but slow down queries

For IVF:

- `nprobe`: the number of clusters searched per query. Larger values improve accuracy but reduce speed

In real deployments, the standard approach is to measure recall and latency on your own data and find the smallest parameter values that meet your requirements.

### 5. Storage Strategy

Where you keep the data is another major design decision.

- **In-memory**: keep everything in RAM. Fastest, but memory costs balloon at the scale of hundreds of millions of records
- **Disk-based**: approaches like **DiskANN** keep most of the index on SSD. Somewhat slower, but they make datasets too large for memory affordable to handle

### 6. Pre-processing and Post-processing

There are also techniques outside the index itself that improve accuracy and cost.

- **Dimensionality reduction (pre-processing)**: techniques like PCA reduce the number of dimensions, lowering compute and memory requirements
- **Reranking (post-processing)**: after ANN coarsely narrows down the candidates (say, to the top 100), only those candidates are re-sorted using the uncompressed vectors or exact distance computation. This is a key technique for recovering the accuracy lost to quantization at a small additional cost

## Designing Practical Systems

Combining all the elements above to balance **speed, accuracy, memory, and cost** is the essence of building a practical ANN system.

A particularly common pattern is **quantization + index + reranking**:

1. Load PQ-compressed vectors into an HNSW or IVF index to narrow down candidates quickly with a small memory footprint
2. Re-sort only the small set of remaining candidates using the original full-precision vectors

This two-stage design gives you the best of both worlds: memory is saved through compression, while final accuracy is guaranteed by reranking. It appears constantly in large-scale vector search systems, so it is well worth remembering.

## Summary

In this article, I covered the fundamentals of ANN (Approximate Nearest Neighbor) search.

- ANN trades a small amount of exactness for orders-of-magnitude speedups, and it underpins vector search applications such as RAG and recommendations
- An ANN system is a combination of multiple components: indexes (HNSW, IVF, etc.), quantization (PQ, SQ, BQ), distance metrics, search parameters, storage strategy, and pre/post-processing
- The essence of the design is tuning the speed–accuracy–memory–cost trade-off, with quantization + index + reranking as the go-to pattern

Libraries and databases such as Faiss, Milvus, and pgvector let you use these mechanisms without implementing them yourself. However, understanding what the parameters mean and how the trade-offs are structured makes a big difference in how well you can tune a system in production. I hope this article serves as that foundation.
