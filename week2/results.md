# Observations from Week 2 Project

**Level 1: How long did it take to index the 1.2M product data set? What docs/sec indexing rate did you see?**

> INFO:Indexing /workspace/datasets/product_data/products to bbuy_products with 16 workers, refresh_interval of -1 to host localhost with a maximum number of docs sent per file per worker of 200000 and 500 per batch.

> INFO:Done. 1275077 were indexed in 18.43055604945 minutes.  Total accumulated time spent in `bulk` indexing: 100.78439870291692 minutes

> 1150 docs/sec - 1800 docs/sec for a brief while but 1000 docs/sec most of the time, leading to the average of 1150 docs/sec.

---

**L1: Notice that the Index size rose (roughly doubled) while the content was being indexed, peaked, then ~ 5 minutes after indexing stopped, the index size dropped down substantially. Why did it drop back down? (What did OpenSearch do here?)**

> Opensearch (and Lucene under the hood) does not update the documents in-place if they are already present in the index. They are created anew and are merged later in the form of Lucene segments, periodically. Since the id here is the product id, indexing process created duplicate documents that got merged later and hence we could see the index size drop, once the segments were merged.

---

**L1: Looking at the metrics dashboard, what queries/sec rate are you getting?**

> 60 queries/sec on an average.

---

**L1: What resource(s) appear to be the constraining factor?**

> CPU was clearly the constraining factor as it constantly hovered around 100% usage.

---

**L2: As you increased CPU and memory in your L2 tests, what seemed to be the constraining factor limiting indexing rate?**

> CPU continued to be hitting peak loads and memory also went pretty high - and there wasn’t much of an improvement in the indexing docs/sec rate with attempts of (2 CPU, 2 GB memory; 2 CPU, 4 GB memory as well as 4 CPU, 8 GB memory). This remained hovering around 900 docs/sec - 1100 docs/sec.

---

**L2: As you increased CPU and memory in your L2 tests, what seemed to be the constraining factor limiting indexing rate? What was the constraining factor for querying rate?**

> CPU continued to be hitting peak loads and memory also went pretty high - and there wasn’t much of an improvement in the indexing docs/sec rate with attempts of (2 CPU, 2 GB memory; 2 CPU, 4 GB memory as well as 4 CPU, 8 GB memory). This remained hovering around 900 docs/sec - 1100 docs/sec.
 
> With increase of CPU and memory, I could notice linear improvement in the querying rate as below:

> (1 CPU, 2 GB memory) - 60 queries/sec 

> (2 CPU, 4 GB memory) - 91 queries/sec

> (4 CPU, 8 GB memory) - 120 queries/sec

---
