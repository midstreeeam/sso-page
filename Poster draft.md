## 1. What is SSO

SSO (Search So) is a specialized search engine, meticulously crafted to cater to the unique requirements of **mid-sized organizations** such as DKU. Our strength lies in delivering rapid, top-notch search results. The entire multifaceted search engine can be deployed with a **single instruction** and requires **minimal maintenance**.

SSO is a fully automated, comprehensive search solution. Our mission is to convey information to the relevant audience with **minimal strain on the IT department**, while offering **superior customizability** compared to other search engines like Google.

SSO provides:

- **High-Quality Search**: Our engine delivers precise search results across the entire corpus, ensuring that you find exactly what you're looking for.
- **Fast Retrieval**: With SSO, waiting is a thing of the past. Our retrieval speed is swift, offering immediate access to search results.
- **Single-Instruction Deployment**: Deployment is as simple as it gets. All you need is a small server, and anyone capable of typing on a keyboard can set it up.
- **Customizability**: SSO provide the freedom to define the search range and make customizable SEO adjustments, all through a single configuration file.
- **Fully Automatic Information Updates**: SSO stays current. If documents being searched are modified, or a new document is created, SSO will automatically update its database to ensure that the information retrieved is the latest. Managing documents becomes effortless, as everything is searchable.
- **Full History**: SSO maintains a complete history of the entire document set. If an essential file is accidentally destroyed, you can still retrieve it through SSO. In fact, you can not only find it but also access all historical versions of it.

## 2. Why SSO

You may wonder why there's a need for another self-hosted search engine when large publicly accessible search engines like Google exist, and even LLM can be a suitable choice for information retrieval. However, our focus is on delivering search solutions for **mid-sized organizations**, such as companies, schools, and non-profit organizations. These entities often manage websites and documents that can be **challenging to handle without a search engine**, and the information that needs to be retrieved may be **internal, beyond Google's reach**. This is where SSO comes in.

Still questioning SSO's effectiveness? **Just try searching for anything you want in DKU's search bar**, and you'll grasp why an internal search engine is **indispensable**.

|DKU Search Bar|SSO|
|---|---|
|![[poster-0.png]]|![[poster-1.png]]|
|![[poster-2.png]]|![[poster-3.png]]|

## 3. Market Analysis

If you believe DKU is the only organization struggling with internal information retrieval, you're mistaken!

Although students rarely need sensitive data and universities' public info is on Google, company employees face challenges. Due to **privacy policies**, much of their needed information isn't on Google. Many use WeChat or Lark for data organization, but these aren't built for advanced searches. Central databases can't replace a true search engine. **Often, employees ask for documents because they can't find them.** The challenge of internal retrieval is widespread, with no standard solution, causing countless wasted hours in searches. The market is **Large**.

The internal search engine market for Small and Medium-sized Enterprises (SMEs) presents a substantial opportunity. According to our investigation, most of them using WeChat or Lark for data arrangement, and it is common for employees to ask for documents sharing simply because they can not find them. With approximately 125 million SMEs worldwide, the total potential market size for this specific segment can be enormous. 
## 4. Target Customers

Our target customers encompass a diverse range of organizations across various fields, as long as they face challenges in internal information retrieval. This includes not only **mid-sized companies, schools, and non-profit organizations** but also **individually hosted forums, websites**, and online communities. What's more, while some **larger corporations** may have their own internal search solutions (such as Microsoft), others do not. SSO can step in to fill this gap, offering a tailored search solution that meets their specific needs.

## 5. How SSO works

The SSO is powered by three primary components - the **Scraper**, **Data Processor**, and **Retrieval Engine**.

- **Scraper**: The Scraper is our data collection tool, meticulously extracting raw HTML from the web. This ensures no potential information is lost during the data collection process.
- **Data Processor**: The Data Processor takes on the vital role of processing the raw information gathered by the Scraper. It culminates this process by generating an Inverted Index, accompanied by BM25 scores and PageRank.
- **Retrieval Engine**: The Retrieval Engine comes into play when users engage with the search button. It seamlessly performs data retrieval from the database, ensuring accurate results are delivered swiftly.

For more technical understanding of sso's mechanism, please check **The SSO Doc** (QR Code provided).

## 6. Competitors

**Elasticsearch**: Elasticsearch is the most widely used self-hosted search engine solution, known for its high search quality and rapid speed, capable of handling super-large datasets. However, its complexity and high hardware demands mean that it requires **significant IT resources**, both in terms of hardware and engineering hours. While offering better scalability, Elasticsearch may be overkill for many organizations. Compared to Elasticsearch, SSO may provide fewer advanced features and not perform as well with enormous datasets, but it is far more user-friendly. What's more, the need for complex features and search capabilities over vast databases is **not aligned with the needs of our target customers**.

**MeiliSearch**: MeiliSearch is a more lightweight and user-friendly option compared to Elasticsearch, making it closer to SSO in its approach. It excels in search speed and is particularly focused on searching over local files with properly arranged indexing (for example, searching through movies with associated introductions and ratings). In contrast, SSO emphasizes **document and website search**, automatically generating all tags and quotations. This makes it more suitable for organizational use, where most documents are stored in pure text with various styles. SSO is specifically designed to search through such content, aligning perfectly with the needs of our target audience.

## 7. Try SSO

|   DKU-Wiki   |   SSO   |   SSO Doc   |
| ---- | ---- | ---- |
|   ![[qr-wiki.png]]   |   ![[qr-sso.png]]   |   ![[qr-doc.png]]   |

