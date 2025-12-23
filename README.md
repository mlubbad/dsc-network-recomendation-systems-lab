# Amazon Recommendation System - Lab

## Introduction

This lab walks through building an **item-based** collaborative filtering recommendation system using an Amazon books dataset.[file:11] You will represent books as nodes in a graph, connect them with weighted edges based on co-purchase information, and then generate recommendations from graph-based similarity.[file:11]

## Objectives

In this lab you will:

- Load an edge list of co-purchased books into a NetworkX graph and inspect the structure.
- Load and explore book metadata, including title, categories, ratings, and centrality measures.
- Select a subset of books of interest to test the recommender system.
- Use graph-based similarity (edge weights and neighborhood relationships) to produce item-item recommendations.
- Generate and display the top 10 recommended books for selected titles using the precomputed edge weights.

## Load the Dataset

You start by importing the required libraries, creating an undirected graph, and loading the edge list file `books_data.edgelist`, which encodes book-to-book connections and their weights (strength of co-purchase similarity). The first few rows show `source`, `target`, and `weight` columns for pairs of related books.

import pandas as pd
import networkx as nx
G = nx.Graph()

df = pd.read_csv('books_data.edgelist', names=['source', 'target', 'weight'], delimiter=' ')
df.head()

## Load the Metadata 

You then import the book metadata from `books_meta.txt`, which is tab-separated and contains fields such as `ASIN`, `Title`, `Categories`, `Group`, `SalesRank`, `TotalReviews`, `AvgRating`, `DegreeCentrality`, and `ClusteringCoeff`.[file:11] This metadata will be used later to display human-readable book information for recommendations.

meta = pd.read_csv('books_meta.txt', sep='\t')
meta.head()

text

## Build the Graph

Using the edge list, you add weighted edges to the NetworkX graph so each node represents a book (identified by its ASIN) and each edge weight reflects similarity between two books. This graph structure is the backbone of the item-based collaborative filtering system.

for _, row in df.iterrows():
G.add_edge(row['source'], row['target'], weight=row['weight'])

text

## Select Books to Test Your Recommender On

Next, you select a small subset of books that you want to generate recommendations for, often by choosing a few ASINs or titles from the metadata. This subset serves as the query set on which you will run the recommender and inspect the results.

Example: select by ASIN or title
test_books = meta.sample(3)['ASIN'].tolist()
test_books

text

## Helper Functions for Lookup

To make the output readable, you typically write helper functions that map between ASINs and titles using the metadata DataFrame. This lets you print recommendations using book names instead of just IDs.

asin_to_title = dict(zip(meta['ASIN'], meta['Title']))

def get_title(asin):
return asin_to_title.get(asin, asin)


## Generate Recommendations for a Few Books of Choice

The file `books_data.edgelist` already contains similarity information as weights, so you can use the graph to find the most strongly connected neighbors for each selected book. For each book in your subset, you retrieve its neighbors, sort them by edge weight in descending order, and print the top 10 recommended books by title.

def get_recommendations(asin, top_n=10):
# Get neighbors and weights
neighbors = G[asin]
scored = [(nbr, neighbors[nbr]['weight']) for nbr in neighbors]
scored = sorted(scored, key=lambda x: x, reverse=True)[:top_n]
​
return scored

for asin in test_books:
print(f"\nRecommendations for: {get_title(asin)} ({asin})")
recs = get_recommendations(asin, top_n=10)
for rec_asin, w in recs:
print(f" -> {get_title(rec_asin)} ({rec_asin}) | similarity: {w}")


## Summary

In this lab, you use graph-based collaborative filtering to recommend similar Amazon books based on co-purchase data.[file:11] By combining a weighted item-item graph with rich metadata, you generate and interpret top-N recommendations for selected titles.
