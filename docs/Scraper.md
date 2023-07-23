# Scraper

## Overview

Developed by Sichang He, the Scraper is a recursive web crawler used to fetch raw HTML data based on a domain name. Crafted with the efficiency and speed of Rust, this tool forms an integral part of our data acquisition process. 

For a more comprehensive understanding, the documentation and source code can be found **[here](https://github.com/SichangHe/scraper)**. Although it's an independent rewrite of SSO's proprietary scraper and may not match exactly what , it embodies the same spirit and core functionality.

**You are welcomed to use!**

## Crawling Strategy

Our approach to website crawling is centered on **Domain Names**. Our main focus is on all websites within the `.dukekunshan.edu.cn` domain. Additionally, we also crawl websites within the `scholars.duke.edu/person` domain to gather information about our esteemed professors.

However, we don't want to limit our knowledge base solely to DKU's domain. To that end, we also crawl the **outer ring**. The outer ring refers to all webpages that are directly linked from DKU's internal pages. Currently, **our ring depth is set to 1**, which means we don't crawl further into pages that are linked by these outer ring pages.

Nonetheless, our scraper supports multiple starting points and domains. If there are other webpages or domains you believe would be valuable for us to crawl beyond just DKU's official pages, please **[reach out to us](./IndexGuide.md)**. We are open to expanding our crawling strategy to include these additional domains and their respective outer rings.
