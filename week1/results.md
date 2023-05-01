# Results of Level 1 of Week 1 Project

**With all fields, given bbuy_products.json mapping**

> INFO:Indexing /workspace/datasets/product_data/products to bbuy_products with 8 workers, refresh_interval of -1 to host localhost with a maximum number of docs sent per file per worker of 200000 and 200 per batch.

> INFO:Done. 1275077 were indexed in 15.909543470216644 minutes.  Total accumulated time spent in `bulk` indexing: 49.13183816900612 minutes
---

**With all fields, given bbuy_products_no_map.json mapping**

> INFO:Indexing /workspace/datasets/product_data/products to bbuy_products with 8 workers, refresh_interval of -1 to host localhost with a maximum number of docs sent per file per worker of 200000 and 200 per batch.

> INFO:Done. 1275077 were indexed in 15.256489445983608 minutes.  Total accumulated time spent in `bulk` indexing: 41.81053390992359 minutes
---

**With only 5 fields, given bbuy_products.json mapping**

> INFO:Indexing /workspace/datasets/product_data/products to bbuy_products with 8 workers, refresh_interval of -1 to host localhost with a maximum number of docs sent per file per worker of 200000 and 200 per batch.

> INFO:Done. 1275077 were indexed in 3.397887693500282 minutes.  Total accumulated time spent in `bulk` indexing: 15.203965782978534 minutes
---

**With only 5 fields, given bbuy_products_no_map.json mapping**

> INFO:Indexing /workspace/datasets/product_data/products to bbuy_products with 8 workers, refresh_interval of -1 to host localhost with a maximum number of docs sent per file per worker of 200000 and 200 per batch.

> INFO:Done. 1275077 were indexed in 2.0856356838334857 minutes.  Total accumulated time spent in `bulk` indexing: 8.184123563644729 minutes
---

**With all fields, given bbuy_products_no_map.json mapping, refresh interval = -1**

> INFO:Indexing /workspace/datasets/product_data/products to bbuy_products with 8 workers, refresh_interval of -1 to host localhost with a maximum number of docs sent per file per worker of 200000 and 200 per batch.

> INFO:Done. 1275077 were indexed in 10.848911227166536 minutes.  Total accumulated time spent in `bulk` indexing: 30.634544198831524 minutes
---

**With all fields, given bbuy_products_no_map.json mapping, refresh interval = 1s**

> INFO:Indexing /workspace/datasets/product_data/products to bbuy_products with 8 workers, refresh_interval of 1s to host localhost with a maximum number of docs sent per file per worker of 200000 and 200 per batch.

> INFO:Done. 1275077 were indexed in 13.759470314983627 minutes.  Total accumulated time spent in `bulk` indexing: 36.61595789493246 minutes
---

**With all fields, given bbuy_products_no_map.json mapping, refresh interval = 60s**

> INFO:Indexing /workspace/datasets/product_data/products to bbuy_products with 8 workers, refresh_interval of 60s to host localhost with a maximum number of docs sent per file per worker of 200000 and 200 per batch.

> INFO:Done. 1275077 were indexed in 11.240539134866655 minutes.  Total accumulated time spent in `bulk` indexing: 31.163110372007456 minutes
---

**With all fields, given bbuy_products_no_map.json mapping, refresh interval = -1, batch size = 400**

> INFO:Indexing /workspace/datasets/product_data/products to bbuy_products with 8 workers, refresh_interval of -1 to host localhost with a maximum number of docs sent per file per worker of 200000 and 400 per batch.

> INFO:Done. 1275077 were indexed in 12.008201187466815 minutes.  Total accumulated time spent in `bulk` indexing: 31.77152998434176 minutes
---

**With all fields, given bbuy_products_no_map.json mapping, refresh interval = -1, batch size = 800**

> INFO:Indexing /workspace/datasets/product_data/products to bbuy_products with 8 workers, refresh_interval of -1 to host localhost with a maximum number of docs sent per file per worker of 200000 and 800 per batch.

> INFO:Done. 1275077 were indexed in 11.783977393916576 minutes.  Total accumulated time spent in `bulk` indexing: 30.45727875126225 minutes
---

**With all fields, given bbuy_products_no_map.json mapping, refresh interval = -1, batch size = 1600**

> INFO:Indexing /workspace/datasets/product_data/products to bbuy_products with 8 workers, refresh_interval of -1 to host localhost with a maximum number of docs sent per file per worker of 200000 and 1600 per batch.

> INFO:Done. 1275077 were indexed in 12.30442288254999 minutes.  Total accumulated time spent in `bulk` indexing: 31.568546532812856 minutes
---

**With all fields, given bbuy_products_no_map.json mapping, refresh interval = -1, batch size = 3200**

> INFO:Indexing /workspace/datasets/product_data/products to bbuy_products with 8 workers, refresh_interval of -1 to host localhost with a maximum number of docs sent per file per worker of 200000 and 3200 per batch.

> INFO:Done. 1275077 were indexed in 10.240428240433296 minutes.  Total accumulated time spent in `bulk` indexing: 26.041152617206293 minutes
---

**With all fields, given bbuy_products_no_map.json mapping, refresh interval = -1, batch size = 5000**

> INFO:Indexing /workspace/datasets/product_data/products to bbuy_products with 8 workers, refresh_interval of -1 to host localhost with a maximum number of docs sent per file per worker of 200000 and 5000 per batch.

> INFO:Done. 1275077 were indexed in 11.624839501349681 minutes.  Total accumulated time spent in `bulk` indexing: 28.6062431680999 minutes
---

# Results of Level 2 of Week 1 Project

**No changes to the query**

python query.py --query_file $QUERY_FILE --max_queries 10000
> INFO:Finished running 10000 queries in 2.4937081979995126 minutes
---
**After removing fuzziness**

python query.py --query_file $QUERY_FILE --max_queries 10000
> INFO:Finished running 10000 queries in 2.238240849482827 minutes
---
**After dropping function scores**

python query.py --query_file $QUERY_FILE --max_queries 10000
> INFO:Finished running 10000 queries in 1.4103426826836463 minutes
---
**After dropping all matches except multi_match**

python query.py --query_file $QUERY_FILE --max_queries 10000
> INFO:Finished running 10000 queries in 1.1570566962502198 minutes
---
**After retaining only name and shortDescription in multi_match**

python query.py --query_file $QUERY_FILE --max_queries 10000
> INFO:Finished running 10000 queries in 0.7203098560334183 minutes
---
