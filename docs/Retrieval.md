# Retrieval Engine

## Overview

The Retrieval Engine embodies the essence of **Search**.

Having run the [Processor](https://chat.openai.com/c/Processor.html), we're now equipped with an array of resources - BM25 scores, PageRank scores, inverse index, and more. Yet, the key question is, how do we execute a search when a user inputs a query and hits 'Enter'? How can we convert complex data relationships into digestible search results? This is precisely where the Retrieval Engine steps in.

For versatility and optimization, we have incorporated two distinct retrieval models within the engine - the **Boolean Retrieval Model** and the **Vector Retrieval Model**. These models can independently and seamlessly conduct their operations, ensuring a smooth user experience.

## Single Token Search

Both our retrieval models - Boolean and Vector - operate identically when processing single query tokens.

Thanks to the Inverted Index which can be indexed by tokens, we can promptly locate all the documents that contain the specific keyword, along with their respective BM25 and PageRank scores. Subsequently, the sorted documents based on the scores can be returned to provide highly relevant results.

We use a simple linear combination strategy to merge the BM25 and PageRank scores: `w1*BM25_score + w2*PageRank_score`. This approach is feasible because the Processor has already normalized these scores.

Consequently, single token searches are typically rapid and accurate. It is the **most recommended usage** of the SSO search engine.

## Boolean Retrieval

## Vector Retrieval

## Why I can't search a whole sentence

