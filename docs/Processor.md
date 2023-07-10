# Processor

Why don't data processors make good chefs?

Because they always try to byte everything before it's bit-fully ready!

## Overview

Serving as the beating heart of our search functionality, the Processor meticulously digests raw HTML data harvested by the Scraper. The journey of data transformation commences with **content extraction** and proceeds with **link map construction**, **PageRank calculation**, **content tokenization**, **IDF scoring**, and concludes with **BM25 computation**.

The final output is a dynamic multi-to-multi inverted index, crafted for efficient retrieval by our search functions. This comprehensive data processing pipeline is finely tuned using parallel programming techniques to ensure lightning-fast speed. Our Processor not only drives the search process but also ensures an optimal balance between accuracy and efficiency. 

The Processor's role extends beyond simple data manipulation; it is the bridge between raw, unstructured information and meaningful, searchable data, all achieved at a remarkably rapid pace.

## 1. Content Extraction

Content extraction is a central yet challenging facet of any project dealing with raw HTML. Its primary aim is to isolate and extract the human-readable content nestled within raw HTML, simultaneously eliminating any boilerplate elements that could introduce unwanted noise. This task can prove daunting as each webpage sports a unique structure, reflecting the diverse design styles of its creators. Consequently, developing a universal formula that can effectively sift through all webpage noise remains a formidable challenge.

However, ingesting raw HTML without extracting content or simply extract all the text from the HTML can significantly contaminate our database and confound our tokenizer. Hence, content extraction is not just important, it's absolutely vital. This intricate process is critical in ensuring that our search results are clean, relevant, and devoid of unnecessary distractions.

### Our approach

For effective content extraction, we employ a grading strategy. Each node in the HTML tree (considering the HTML as a tree) is assigned a grade, and the node with the highest grade is selected as containing the "main content" of the document.

Node grading is based on several key features:

- **tag name:** The tag name is a crucial determinant of a node's potential to contain the "main content". Nodes tagged with `<content>`, `<article>`, or `<main>` are more likely to house the main content than those tagged with `<menu>`, `<nav>`, or `<aside>`.

- **children tags:** The distribution of a node's child tags can also suggest its likelihood of being the main content. Nodes with a higher percentage of `<p>` tags among child tags are scored favorably.

- **text - children ratio:** Nodes with an excessively high or low number of children, or those with too much or too little text, are less likely to contain the main content — they're located too high or too low in the HTML tree. We thus use the text-to-children ratio to assess the suitability of a node, aiming for nodes that contain only a few sizable blocks of text.

  We use the following equation to calculate this ratio:
  $$
  \frac{t\times(1+c/n)}{c/n}
  $$
  Here, $t$ is the text length, $c$ is the total number of child nodes, and $n$ is a variable used to gauge what counts as "too few children". The $n$ variable is also instrumental in adjusting whether we want the chosen node to be closer to the root or leaf.

We recursively grade each node in the HTML tree and ultimately select one node per document as the main content.

You can find the implementation and the benchmark result **[here](https://github.com/midstreeeam/peduncle)**.



In addition to text extraction, the content extractor also conducts HTML analysis, extracting critical elements such as tags, links, metadata, scripts, and others essential for further processing.

## 2. Link Map

The Link Map in our system acts as a multi-to-multi relationship map, representing all the internal links within our document corpus. Essentially, the Link Map operates like a graph, and as such, it can be expressed as a matrix.

In our approach, we focus solely on internal links, ignoring any links pointing to external websites that fall outside our corpus. For instance, links directing to [4chan](https://www.4chan.org/) are disregarded, as it doesn't pertain to our DKU-focused database. (If there are DKU-relate contents in 4chan, please let us know.)

Despite our narrow focus, the size of the Link Map can be considerable, growing exponentially (the growing will generally decrease to linear in super large database, which is not our case) with the size of the corpus. For instance, a collection of 10,000 DKU-related web pages can result in over 500,000 internal links. The vast scale of the Link Map poses challenges in managing and quickly computing the data. Nevertheless, it serves as a crucial resource in achieving efficient, interconnected searches across our database.

### Webpage Deduplication

Duplicates within the web page corpus can adversely affect the quality of the link map, so we strive to eliminate them.

The existence of duplicate webpages in a corpus is a frequent occurrence due to several factors:

1. **Canonical URLs:** This is the most common reason for duplicate webpages, where multiple different URLs point to the same content.
2. **URL Parameters:** A single webpage can have an infinite number of URLs if it allows for parameter variations.
3. **Mirrored Websites:** Although less common, some websites are completely mirrored, leading to duplication.

Addressing webpage duplication is a complex task. Our scraper is designed to track all redirections and can prevent many duplicate webpages from entering our database. However, we cannot guarantee the complete absence of duplicate webpages in our database. As we continuously strive for perfection, refining our deduplication processes remains an ongoing commitment.

### Sparse Matrix Operation

As we previously mentioned, the sheer quantity of links in our database is substantial. However, this volume is dwarfed when compared to the potential size of the link map matrix. To put it into perspective, 10,000 DKU-related web pages might comprise over 500,000 links, but the potential size of the matrix is a staggering 100,000,000! If we allocated 1 Byte to store each link, we would require 100MB to accommodate the entire matrix, which is obviously unaffordable.

To overcome this challenge, we have to handle the sparse matrix. There are numerous strategies to deal with sparse matrices, and our choice is to utilize the **Compressed Sparse Row (CSR)** format. CSR is renowned for its calculation efficiency, which is critical as our system performs numerous operations on the matrix. By leveraging CSR, we can ensure optimal performance while reducing the storage requirements for our expansive link map.

## 3. PageRank

PageRank is a way of measuring the importance of a webpage. It was developed by Google co-founder Larry Page, and is one of the algorithms that Google uses to determine the order in which search results are displayed. It is important to every search engine since people always want important information at a higher place.

Generally it can be calculated like this:
$$
PR(p) = (1-d) + d \left( \frac{{PR(t_1)}}{{C(t_1)}} + \frac{{PR(t_2)}}{{C(t_2)}} + \ldots + \frac{{PR(t_n)}}{{C(t_n)}} \right)
$$
In this formula, $PR(p)$ represents the PageRank of a given page $p$. $PR(t_i)$ refers to the PageRank of the pages $t_i$ that link to page $p$, while $C(t_i)$ represents the number of outbound links from page $t_i$. The parameter $d$ is the damping factor, typically set to $0.85$, which represents the probability of a user randomly clicking on a link rather than following the links on the page.

This algorithm reflects a **basic Markov Chain model**, which enables us to execute ultra-fast matrix operations, and swiftly calculate the PageRank. By harnessing the power of the PageRank algorithm, we can offer more meaningful search results.

## 4. Tokenization

Tokenization is a fascinating, yet intricate aspect of our work, and a **tooooo deeeeeep reservoir** of knowledge to dive into. At the current stage, we've opted to utilize existing solutions. Specifically, we use **[jieba](https://github.com/fxsjy/jieba)** and **[nltk](https://www.nltk.org/)** for Chinese and English tokenization, respectively. These libraries offer efficient and precise tokenization, and we are grateful for the work of researchers in this area.

But implementing a library doesn't mean we are free of challenges. A number of complex issues still need to be addressed.

### Stemming and Lemmatization

To make our search engine user-friendly, we must normalize and consolidate different forms of the same word. For instance, searching "run", "running", "runs", or "ran" should yield similar results.

Traditional stemming rules, like the **[Porter Stemmer](https://tartarus.org/martin/PorterStemmer/def.txt)**, provide us a framework to map these word forms into a common base form. It is easy to implement and can produce usable results. Today, more advanced machine-learning techniques, such as the **WordNet Lemmatizer**, offer improved performance. While we haven't yet implemented the latter, we're actively considering incorporating AI-based solutions in the future.

### Case Sensitivity

The capitalization of a word can also present challenges. In most cases, the meaning of a word remains unchanged regardless of its capitalization, so it seems straightforward to treat them as equivalent (like "Run" and "run").

However, there are instances where capitalization significantly changes the meaning of a word. For example, "IT" in "DKU IT" stands for Information Technology, whereas "it" is generally a pronoun.

Addressing this issue is tricky. Ideally, a search for "run" should also return results containing "Run", but a search for "IT" shouldn't necessarily include results for "it". Balancing these considerations is part of our ongoing work to improve our search capabilities.

## 5. TF-IDF and BM25

While PageRank measures the importance of webpages, the TF-IDF and BM25 are algorithms measuring the relativeness between input query and documents been searched. Higher TF-IDF or BM25 value means higher relativeness, then the document should pop up in the result.

### TF-IDF

- **Term Frequency**: TF means term frequency, which measures the frequency of a term (word) within a document. It quantifies the importance of a term in a document relative to other terms within the same document. The higher the term frequency, the more likely the term is to be important in the document.
- **Inverse Document Frequency**: Inverse Document Frequency (IDF) determines the significance of a term in the entire corpus by considering the proportion of documents that contain the term. Terms that occur in many documents are considered less informative, while terms that appear in a smaller number of documents are deemed more important. It is pretty intuitive: common tokens like "and", "is" must be less important than uncommon tokens like "library", "schedule".

Indeed, the crux of the TF-IDF algorithm is to multiply the term frequency (TF) with the inverse document frequency (IDF). This process results in a quantifiable metric that reflects the relationship between a token and a document. The formula to calculate TF-IDF is as follows:
$$
\text{{TF-IDF}}(t, d, D) = \frac{{\text{{TF}}(t, d)}}{{\sum_{{t' \in d}} \text{{TF}}(t', d)}} \times \log \left( \frac{{\left| D \right|}}{{\left| \{d' \in D : t \in d'\} \right|}} \right)
$$
In this formula:

- $t$ symbolizes a specific term or word
- $d$ indicates a particular document
- $D$ represents the entire collection of documents or the corpus
- $|{d' \in D : t \in d'}|$ denotes the number of documents within the corpus that contain the term $t$

### BM25

While TF-IDF has proven to be a reliable and effective weighting scheme for information retrieval, BM25 (Best Match 25) is often favored in the realm of search engines and information retrieval systems, **including SSO** (yes, we are using BM25 instead of TF-IDF).

Similar to TF-IDF, BM25 calculates relevance based on Term Frequency (TF) and Inverse Document Frequency (IDF), but it also incorporates an added layer of standardization and normalization. This added sophistication makes BM25 a robust model for measuring the importance of words in documents.

Comparing to TF-IDF, it provides additional **Term Frequency Saturation**, **Document Length Normalization**, and **Tunable Parameters**. 
$$
\text{{BM25}}(q, D, d) = \sum_{i=1}^{n} \text{{IDF}}(q_i) \cdot \frac{{\text{{TF}}(q_i, d) \cdot (k_1 + 1)}}{{\text{{TF}}(q_i, d) + k_1 \cdot (1 - b + b \cdot \frac{{\text{{length}}(d)}}{{\text{{avg\_length}}}})}}
$$
In this formula:

- $q$ represents a query.
- $D$ represents the entire document collection (corpus).
- $d$ represents a specific document.

The BM25 score for document $d$ given query $q$ is calculated using the following components:

- $n$ represents the number of terms in the query $q$.
- $q_i$ denotes each term in the query $q$.
- $IDF(q_i)$ is the Inverse Document Frequency, which quantifies the rarity or importance of term $q_i$ in the document collection $D$.
- $TF(q_i, d)$ is the Term Frequency, which measures the frequency of term $q_i$ in document $d$.
- $k_1$ and $b$ are tunable parameters that control the scaling factors for term frequency and document length normalization.
- $\text{{length}}(d)$ represents the length (number of words) of document $d$.
- $\text{{avg\_length}}$ is the average document length in the corpus.



Indeed, the algorithm employed in SSO is **not strictly BM25**, but more closely resembles **BM25+**, with the addition of weighting for specific elements, such as words appearing in the title.
$$
\text{{BM25+}}(q, D, d) = \sum_{i=1}^{n} \left( \text{{IDF}}(q_i) \cdot \frac{{\text{{TF}}(q_i, d) \cdot (k_1 + 1)}}{{\text{{TF}}(q_i, d) + k_1 \cdot (1 - b + b \cdot \frac{{\text{{length}}(d)}}{{\text{{avg\_length}}}})}} \right) + \sum_{i=1}^{n_t} \text{{TitleWeight}}(q_i, d) \cdot \text{{IDF}}(q_i)
$$
In our approach, we assign distinct weights to text found within different HTML tags such as `<title>`, `<h1>`, `<h2>`, `<em>` and so forth. This nuanced weighting allows us to prioritize and emphasize certain areas of the content, making the search results more accurate and relevant. (Maybe we should call it BM25++, or BM25#)

Notably, we also factor in words appearing in the URL into our model. This feature enhances the depth of the search process, often capturing relevant information that is encoded in the URL structure of a webpage.

By integrating these advanced features, SSO ensures a **comprehensive and precise search experience**.
