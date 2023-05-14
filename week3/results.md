# Observations from Week 3 Project

**Q1: Which node was elected cluster manager?**

> opensearch-node2 was elected the cluster manager.

> GET /_cat/nodes?v

> ip         heap.percent ram.percent cpu load_1m load_5m load_15m node.role node.roles                                        cluster_manager name

> 172.18.0.3           42          96   3    1.08    2.66     2.59 dimr      cluster_manager,data,ingest,remote_cluster_client *               opensearch-node2

> 172.18.0.4           57          96   3    1.08    2.66     2.59 dimr      cluster_manager,data,ingest,remote_cluster_client -               opensearch-node3

> 172.18.0.5           47          96   3    1.08    2.66     2.59 dimr      cluster_manager,data,ingest,remote_cluster_client -               opensearch-node1

---

> docker ps

> CONTAINER ID   IMAGE                                           COMMAND                  CREATED          STATUS                   PORTS                                                                                                      NAMES

> 6cadeeef3fd0   grafana/grafana:9.4.7                           "/run.sh"                4 minutes ago    Up 4 minutes             0.0.0.0:3000->3000/tcp, :::3000->3000/tcp                                                                  grafana

> 32521ebfa343   prom/prometheus:v2.43.0                         "/bin/prometheus --c…"   4 minutes ago    Up 4 minutes             0.0.0.0:9090->9090/tcp, :::9090->9090/tcp                                                                  prometheus

> b680f8462956   gcr.io/cadvisor/cadvisor:v0.47.1                "/usr/bin/cadvisor -…"   4 minutes ago    Up 4 minutes (healthy)   0.0.0.0:8080->8080/tcp, :::8080->8080/tcp                                                                  cadvisor

> f2f179adfd52   opensearchproject/opensearch:2.6.0              "./opensearch-docker…"   14 minutes ago   Up 6 minutes             0.0.0.0:9200->9200/tcp, :::9200->9200/tcp, 9300/tcp, 0.0.0.0:9600->9600/tcp, :::9600->9600/tcp, 9650/tcp   opensearch-node1

> 8db771df7d57   opensearchproject/opensearch:2.6.0              "./opensearch-docker…"   14 minutes ago   Up 6 minutes             9200/tcp, 9300/tcp, 9600/tcp, 9650/tcp                                                                     opensearch-node3

> 1b38885531bd   opensearchproject/opensearch:2.6.0              "./opensearch-docker…"   14 minutes ago   Up 6 minutes             9200/tcp, 9300/tcp, 9600/tcp, 9650/tcp                                                                     opensearch-node2

> 1fcbabf1197b   opensearchproject/opensearch-dashboards:2.6.0   "./opensearch-dashbo…"   14 minutes ago   Up 14 minutes            0.0.0.0:5601->5601/tcp, :::5601->5601/tcp                                                                  opensearch-dashboards

---

**Q2: After stopping the previous cluster manager, which node was elected the new cluster manager?**

> opensearch-node1 was elected the cluster manager after the previous one was stopped.

---

**Q3: Did the cluster manager node change again? (was a different node elected as cluster manager when you started the node back up?)**

> No. opensearch-node1 remained the cluster manager. OpenSearch doesn’t trigger leader election unnecessarily, unless the current leader goes down.

---

**Q4: How much faster was it to index the dataset with 0 replicas versus the previous time with 2 replica shards?**

> It was almost twice as fast to index with 0 replicas versus indexing with 2 replicas. Indexing rate increased from 1075 docs/sec to 2142 docs/sec on average.

---

> Indexing with 2 replicas:

> INFO:Indexing /workspace/datasets/product_data/products to bbuy_products with 8 workers, refresh_interval of -1 to host localhost with a maximum number of docs sent per file per worker of 200000 and 500 per batch.

> INFO:Done. 1275077 were indexed in 19.76561301746697 minutes.  Total accumulated time spent in `bulk` indexing: 56.057236529990526 minutes

> **Avg. 1075 docs/sec**

> index         shard prirep state     docs   store ip         node

> bbuy_products 0     p      STARTED 423938 428.4mb 172.18.0.5 opensearch-node1

> bbuy_products 0     r      STARTED 423938 425.2mb 172.18.0.4 opensearch-node3

> bbuy_products 0     r      STARTED 423938 419.7mb 172.18.0.3 opensearch-node2

> bbuy_products 1     p      STARTED 425833 434.3mb 172.18.0.4 opensearch-node3

> bbuy_products 1     r      STARTED 425833 420.7mb 172.18.0.5 opensearch-node1

> bbuy_products 1     r      STARTED 425833 422.8mb 172.18.0.3 opensearch-node2

> bbuy_products 2     p      STARTED 425306 427.4mb 172.18.0.3 opensearch-node2

> bbuy_products 2     r      STARTED 425306   420mb 172.18.0.5 opensearch-node1

> bbuy_products 2     r      STARTED 425306 422.8mb 172.18.0.4 opensearch-node3

---

> Indexing with 0 replicas

> INFO:Indexing /workspace/datasets/product_data/products to bbuy_products with 8 workers, refresh_interval of -1 to host localhost with a maximum number of docs sent per file per worker of 200000 and 500 per batch.

> INFO:Done. 1275077 were indexed in 9.921137419333293 minutes.  Total accumulated time spent in `bulk` indexing: 17.383537483836697 minutes

> **Avg. 2142 docs/sec**

> index         shard prirep state     docs   store ip         node

> bbuy_products 0     p      STARTED 423938 421.6mb 172.18.0.5 opensearch-node1

> bbuy_products 0     r      STARTED 423938 421.6mb 172.18.0.4 opensearch-node3

> bbuy_products 0     r      STARTED 423938 421.6mb 172.18.0.3 opensearch-node2

> bbuy_products 1     p      STARTED 425833 442.1mb 172.18.0.4 opensearch-node3

> bbuy_products 1     r      STARTED 425833 442.1mb 172.18.0.5 opensearch-node1

> bbuy_products 1     r      STARTED 425833 442.1mb 172.18.0.3 opensearch-node2

> bbuy_products 2     p      STARTED 425306 443.7mb 172.18.0.3 opensearch-node2

> bbuy_products 2     r      STARTED 425306 443.7mb 172.18.0.5 opensearch-node1

> bbuy_products 2     r      STARTED 425306 443.7mb 172.18.0.4 opensearch-node3

---

**Q5: Why was it faster?**

> Apart from the additional CPU, disk and memory usage to write to the additional replica shards, the approach towards replication by OpenSearch is the reason behind the slowness in the former attempt. OpenSearch indexes a document first on the primary shard and then forwards it to the replicas of that primary shard. Replica indexing is then carried out concurrently. The replicas then reply to the primary, after which the indexing request is acknowledged back to the client. So, indexing is sequential among the primary and its replicas, thus roughly doubling the index time ion the first approach.

---

**Q6: How long did it take to create the new replica shards?  This will be the difference in time between those two log messages.**

> 46 seconds.

> docker logs opensearch-node1

> [2023-05-13T13:47:46,022][INFO ][o.o.c.m.MetadataUpdateSettingsService] [opensearch-node1] updating number_of_replicas to [2] for indices [bbuy_products]

> …

> [2023-05-13T13:48:30,699][INFO ][o.o.c.r.a.AllocationService] [opensearch-node1] Cluster health status changed from [YELLOW] to [GREEN] (reason: [shards started [[bbuy_products][2], [bbuy_products][1]]]).

---

**Q7: Those two messages were both logged by the cluster_manager.  Why do you think the cluster manager is the node that logs these actions (versus non-manager nodes)?**

> As the cluster manager is the source of truth, it takes the responsibility of co-ordinating the allocation and population of the replica shards.

---

**Q8: Looking at the metrics dashboard, what queries/sec rate are you getting?**

> Each of the 3 nodes was able to sustain around 100 queries/sec with some peaks of even 150 queries/sec once in a while. So, we could easily get an average of 300 queries/sec.

---

**Q9: How does that compare to the max queries/sec rate you saw in week 2?**

> The maximum rate of week 2 was 120 queries/sec, obtained with 4 CPUs and 8 GB memory on a single node. We could easily beat that with an average of 300 queries/sec using the 3 node cluster.

---
---


# Useful queries from Week 3 Project

GET /_cat/indices

GET /_cat/nodes?v

GET /_cat/plugins

GET /_cat/shards/bbuy_products?v&s=shard,prirep

GET /bbuy_products/_count

PUT /bbuy_products/_settings
{
    "index": {
      "number_of_replicas": 2
    }
}

PUT bbuy_products/_settings
{
  "index.blocks.write": true
}

POST /bbuy_products/_shrink/bbuy_products_1shard
{
   "settings": {
     "index.number_of_replicas": 2,
     "index.number_of_shards": 1
   }
}
