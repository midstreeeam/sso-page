# Retrieval Engine

## Overview

The Retrieval Engine embodies the essence of **Search**.

Having run the [Processor](https://chat.openai.com/c/Processor.html), we're now equipped with an array of resources - BM25 scores, PageRank scores, inverse index, and more. Yet, the key question is, how do we execute a search when a user inputs a query and hits 'Enter'? How can we convert complex data relationships into digestible search results? This is precisely where the Retrieval Engine steps in.

For versatility and optimization, we have incorporated two distinct retrieval models within the engine - the **Sparse Matrix Retrieval Model** and the **Vector Retrieval Model**. These models can independently and seamlessly conduct their operations, ensuring a smooth user experience.

## Single Token Search

Both our retrieval models - Boolean and Vector - operate **identically** when processing single query tokens.

Thanks to the Inverted Index which can be indexed by tokens, we can promptly locate all the documents that contain the specific keyword, along with their respective BM25 and PageRank scores. Subsequently, the sorted documents based on the scores can be returned to provide highly relevant results.

We use a simple linear combination strategy to merge the BM25 and PageRank scores: $w1*score_{BM25} + w2*score_{PageRank}$. This approach is feasible because the Processor has already normalized these scores.

Consequently, single token searches are typically rapid and accurate. It is the **most recommended usage** of the SSO search engine.

## Boolean Retrieval

Boolean Retrieval is a very primitive way of retrieval. It basically answers one single question does this word (token) exists in this web page (document). We could think about retrieval as a big spreadsheet, the column being the distinct words, and the row being distinct document. If one word exists in one document, the cross section will be set to one, otherwise zero. Because such spreadsheet is generally consist of al most all zeros, it is also referred to as a sparse matrix retrieval technic.

During the retrieval, the document retrieval based on the document ID. In the settings of spreadsheet, it means that it looks for matches from the first row to the last. The importance of a word in a given document is not considered here.

This kind of strict match works well in the scenario that accuracy matters, and the indexing scope is not too large, meaning that the returned retrieval result is human digestible amount. Such retrieval method, as one could image, does not work well in the context of Internet or even on relatively large sites.

## Ranked Boolean Retrieval, TF-IDF

One might wonder, what if we could tell what site is important to our query words, would that be great? Yes, of course. However, back when the demand for better retrieval method appears, there is nothing like ChatGPT or anything close. Back then, most people probably still think owning a Ti-81 makes them the coolest kids on the street. 

Jokes aside. The ranked boolean retrieval general term referring to different technics. The most adopted one is TF-IDF. It is a genius invention. The principle of the TF-IDF is as follow, if a word appears more in one document, the document is more related to the word, called Term Frequency (TF), and if the word appears a lot across document, the word is less important, called Inverse Document Frequency (IDF). It is simple but works well. 

In real world implementation, both the TF and IDF has different implementation details. THe major difference is in how they normalize the score. The rationale behind the normalization is that if one term appears in document $a$ 10 times, and appears in document $b$ 100 times. It does not make the word 10 times more related to document $b$ than document $a$. Typically, a logarithmic normalization is applied to the score. However, in the case of search-sou, a polynomial one is found to perform better.

## Vector Retrieval

In contrast to Boolean Retrieval, Vector Retrieval contemplates a broader range of search candidates. If we visualize our Inverted Index as a matrix, each document can be conveniently represented by a vector. The vector's length is the total number of unique tokens we have, and each vector element's value is the corresponding BM25 score between the document and the token.

We calculate the **cosine similarity** between document embedding vectors to easily determine the relationship between any two documents. So, in vector retrieval, **we interpret the input query as a short document**, and compare it with all other documents to find the most similar ones.

However, a limitation of vector retrieval is that the input order of words in the query doesn't impact the results, since they will generate the same embedding vector. (This drawback can be prevented from using dense retrieval model since transformer is very sensitive to order of tokens)

Furthermore, vector retrieval is notably slower than Boolean retrieval, as it encompasses a larger search scope (but not the entire corpus, as two vectors will be perpendicular if they don't share common tokens).

**For optimizing similarity search in high-dimensional vector spaces, Kaiyuan Lou and Yizhou Bi conducted some research and proposed an optimization using Hierarchical Clustering.** 

Although it hasn't been integrated into SSO, you can still see their implementation **[here](https://github.com/loeeeee/DensePassageRetrieval)** and report/benchmark **[here](./EDRFHC.html)**.

## Why I Can't Search an Entire Sentence

The major distinction between **Sparse Retrieval** (like SSO) and **Dense Retrieval** (like Google) underlies this issue. Sparse retrieval models can't comprehend the natural language inputs from users, whereas dense retrieval models, containing **LLMs** (Language Learning Models), can.

Dense retrieval models employ vector retrieval, much like sparse retrieval models we have. However, instead of using a highly sparse embedding matrix, dense retrieval employs LLMs (primarily **BERT**) to create compact, dense embeddings for each document. With appropriate fine-tuning, the BERT encoder can understand human languages, enabling natural language searches on platforms like Google.

Integrating a dense retrieval model into SSO is challenging as it necessitates additional training of LLMs. Furthermore, we would need to transition our database from a traditional format to a vector database for efficient vector similarity searches. (the search space truly expand to the whole corpus in dense retrieval) Therefore, at this stage, we are not considering the addition of dense retrieval.