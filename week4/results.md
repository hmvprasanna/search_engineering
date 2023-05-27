# Observations from Week 4 Project

## After both leader cluster and follower cluster were created:

```
curl -XGET -u 'admin:admin' -k 'https://localhost:9200'
{
  "name" : "opensearch-cl1-node1",
  "cluster_name" : "opensearch-cluster-leader",
  "cluster_uuid" : "LAJ0KrEYRu63lnVx3ZH4jg",
  "version" : {
    "distribution" : "opensearch",
    "number" : "2.6.0",
    "build_type" : "tar",
    "build_hash" : "7203a5af21a8a009aece1474446b437a3c674db6",
    "build_date" : "2023-02-24T18:57:04.388618985Z",
    "build_snapshot" : false,
    "lucene_version" : "9.5.0",
    "minimum_wire_compatibility_version" : "7.10.0",
    "minimum_index_compatibility_version" : "7.0.0"
  },
  "tagline" : "The OpenSearch Project: https://opensearch.org/"
}
```

```
curl -XGET -u 'admin:admin' -k 'https://localhost:9201'
{
  "name" : "opensearch-cl2-node1",
  "cluster_name" : "opensearch-cluster-follower",
  "cluster_uuid" : "C44zNT4_Rry2U-K38gUCOA",
  "version" : {
    "distribution" : "opensearch",
    "number" : "2.6.0",
    "build_type" : "tar",
    "build_hash" : "7203a5af21a8a009aece1474446b437a3c674db6",
    "build_date" : "2023-02-24T18:57:04.388618985Z",
    "build_snapshot" : false,
    "lucene_version" : "9.5.0",
    "minimum_wire_compatibility_version" : "7.10.0",
    "minimum_index_compatibility_version" : "7.0.0"
  },
  "tagline" : "The OpenSearch Project: https://opensearch.org/"
}
```

## Running docker ps command to check the same:

```
CONTAINER ID   IMAGE                                           COMMAND                  CREATED      STATUS         PORTS                                                                                                      NAMES
f111e83a1c48   opensearchproject/opensearch:2.6.0              "./opensearch-docker…"   5 days ago   Up 9 minutes   9200/tcp, 9300/tcp, 9600/tcp, 9650/tcp                                                                     opensearch-cl1-node3
947c7b9b183c   opensearchproject/opensearch:2.6.0              "./opensearch-docker…"   5 days ago   Up 9 minutes   9200/tcp, 9300/tcp, 9600/tcp, 9650/tcp                                                                     opensearch-cl2-node3
d932c3ec3327   opensearchproject/opensearch:2.6.0              "./opensearch-docker…"   5 days ago   Up 9 minutes   9300/tcp, 9650/tcp, 0.0.0.0:9201->9200/tcp, :::9201->9200/tcp, 0.0.0.0:9601->9600/tcp, :::9601->9600/tcp   opensearch-cl2-node1
b9fa223d8cf4   opensearchproject/opensearch:2.6.0              "./opensearch-docker…"   5 days ago   Up 9 minutes   9200/tcp, 9300/tcp, 9600/tcp, 9650/tcp                                                                     opensearch-cl2-node2
198e645f9dcc   opensearchproject/opensearch-dashboards:2.6.0   "./opensearch-dashbo…"   5 days ago   Up 9 minutes   0.0.0.0:5601->5601/tcp, :::5601->5601/tcp                                                                  opensearch-dashboards
9c2f653abbdf   opensearchproject/opensearch:2.6.0              "./opensearch-docker…"   5 days ago   Up 9 minutes   0.0.0.0:9200->9200/tcp, :::9200->9200/tcp, 9300/tcp, 0.0.0.0:9600->9600/tcp, :::9600->9600/tcp, 9650/tcp   opensearch-cl1-node1
8fc865128fdd   opensearchproject/opensearch:2.6.0              "./opensearch-docker…"   5 days ago   Up 9 minutes   9200/tcp, 9300/tcp, 9600/tcp, 9650/tcp                                                                     opensearch-cl1-node2
```

## Checking node health and statuses:

```
curl -XGET -u 'admin:admin' -k 'https://localhost:9201/_cat/nodes?v'
ip         heap.percent ram.percent cpu load_1m load_5m load_15m node.role node.roles                                        cluster_manager name
172.20.0.5           18          71   7    0.74    1.11     1.29 dimr      cluster_manager,data,ingest,remote_cluster_client *               opensearch-cl2-node1
172.20.0.6           41          71   7    0.74    1.11     1.29 dimr      cluster_manager,data,ingest,remote_cluster_client -               opensearch-cl2-node2
172.20.0.2           32          70   7    0.74    1.11     1.29 dimr      cluster_manager,data,ingest,remote_cluster_client -               opensearch-cl2-node3
```

```
curl -XGET -u 'admin:admin' -k 'https://localhost:9200/_cat/nodes?v'
ip         heap.percent ram.percent cpu load_1m load_5m load_15m node.role node.roles                                        cluster_manager name
172.20.0.8           31          70   2    0.77    1.11     1.29 dimr      cluster_manager,data,ingest,remote_cluster_client -               opensearch-cl1-node2
172.20.0.7           25          70   3    0.77    1.11     1.29 dimr      cluster_manager,data,ingest,remote_cluster_client -               opensearch-cl1-node3
172.20.0.4           42          71   3    0.77    1.11     1.29 dimr      cluster_manager,data,ingest,remote_cluster_client *               opensearch-cl1-node1
```

## Setting up cross-cluster replication:

```
curl -XPUT -k -H 'Content-Type: application/json' -u 'admin:admin' 'https://localhost:9201/_cluster/settings?pretty' -d '
{
  "persistent": {
    "cluster": {
      "remote": {
        "my-connection-alias": {
          "seeds": ["172.20.0.4:9300","172.20.0.8:9300","172.20.0.7:9300"]
        }
      }
    }
  }
}'

{
  "acknowledged" : true,
  "persistent" : {
    "cluster" : {
      "remote" : {
        "my-connection-alias" : {
          "seeds" : [
            "172.20.0.4:9300",
            "172.20.0.8:9300",
            "172.20.0.7:9300"
          ]
        }
      }
    }
  },
  "transient" : { }
}
```

## Index creation on the leader cluster - bbuy_products:

```
curl -k -X PUT -u admin:admin https://localhost:9200/bbuy_products -H 'Content-Type: application/json' -d @/workspace/search_engineering/week4/bbuy_products.json
```

## Intitating replication for the newly created index on the follower cluster:

```
curl -XPUT -k -H 'Content-Type: application/json' -u 'admin:admin' 'https://localhost:9201/_plugins/_replication/bbuy_products/_start?pretty' -d '
{
   "leader_alias": "my-connection-alias",
   "leader_index": "bbuy_products",
   "use_roles":{
      "leader_cluster_role": "all_access",
      "follower_cluster_role": "all_access"
   }
}'

{
  "acknowledged" : true
}
```

## Verifying the status of replication on the follower cluster:

```
curl -XGET -k -u 'admin:admin' 'https://localhost:9201/_plugins/_replication/bbuy_products/_status?pretty'

{
  "status" : "SYNCING",
  "reason" : "User initiated",
  "leader_alias" : "my-connection-alias",
  "leader_index" : "bbuy_products",
  "follower_index" : "bbuy_products",
  "syncing_details" : {
    "leader_checkpoint" : -3,
    "follower_checkpoint" : -3,
    "seq_no" : -2
  }
}
```

## Initial state of indexes on both leader and follower:

```
curl -XGET -u 'admin:admin' -k 'https://localhost:9200/_cat/indices?v'
health status index                        uuid                   pri rep docs.count docs.deleted store.size pri.store.size
green  open   security-auditlog-2023.05.27 uCKjelJgTpC22y1TVdWQHw   1   1         20            0    484.2kb        200.8kb
green  open   .opensearch-observability    R_8asWjsTpuvilShiR1lVw   1   2          0            0       624b           208b
green  open   security-auditlog-2023.05.21 h0VTk-N5Q62P7V07l43OPA   1   1          5            0     70.3kb         35.1kb
green  open   .kibana_92668751_admin_1     Oi08Z1FmRduryXUhLRVODw   1   1          1            0     10.3kb          5.1kb
green  open   bbuy_products                CxdtW1lLTa6sgHGlH1HVJQ   3   2          0            0      1.8kb           624b
green  open   .opendistro_security         _OJZQ8hcTQOfM9nZavgJRg   1   2         10            0    141.4kb         49.5kb
green  open   .kibana_1                    0xD7uHXeRaqTUK0BUTFhhA   1   1          1            0     10.3kb          5.1kb
```

```
curl -XGET -u 'admin:admin' -k 'https://localhost:9201/_cat/indices?v'
health status index                        uuid                   pri rep docs.count docs.deleted store.size pri.store.size
green  open   security-auditlog-2023.05.27 UQdFSuTkThijevk_Thstxg   1   1         51            0    227.1kb         95.6kb
green  open   .opensearch-observability    HUAXz3lIQRuKElkUwo7a6w   1   2          0            0       624b           208b
green  open   .kibana_92668751_admin_1     TB7srDt2SICiixYPAyDE6g   1   1          0            0       416b           208b
green  open   bbuy_products                u12u9O9eRUOtjjE9UTe_mQ   3   2          0            0      1.8kb           624b
green  open   .kibana_1                    Mz9lwQQmSxypUC6bV5MB7g   1   1          0            0       416b           208b
green  open   .opendistro_security         SiECET0STC-8L-Rjbk2juw   1   2         10            0    126.7kb         42.2kb
```

## Indexing step:

```
python index.py -s $BBUY_DATA -w 8 -b 500
INFO:Indexing /workspace/datasets/product_data/products to bbuy_products with 8 workers, refresh_interval of -1 to host localhost with a maximum number of docs sent per file per worker of 200000 and 500 per batch.
INFO:Done. 1275077 were indexed in 12.868222453416577 minutes.  Total accumulated time spent in `bulk` indexing: 69.35172572018395 minutes
```

## Final state of indexes on both leader and follower after indexing is complete:

```
curl -XGET -u 'admin:admin' -k 'https://localhost:9200/_cat/indices?v'
health status index                        uuid                   pri rep docs.count docs.deleted store.size pri.store.size
green  open   security-auditlog-2023.05.27 uCKjelJgTpC22y1TVdWQHw   1   1        213            0    944.5kb        492.4kb
green  open   .opensearch-observability    R_8asWjsTpuvilShiR1lVw   1   2          0            0       624b           208b
green  open   security-auditlog-2023.05.21 h0VTk-N5Q62P7V07l43OPA   1   1          5            0     70.3kb         35.1kb
green  open   .kibana_92668751_admin_1     Oi08Z1FmRduryXUhLRVODw   1   1          1            0     10.3kb          5.1kb
green  open   bbuy_products                CxdtW1lLTa6sgHGlH1HVJQ   3   2    1275077            0      3.5gb          1.1gb
green  open   .kibana_1                    0xD7uHXeRaqTUK0BUTFhhA   1   1          1            0     10.3kb          5.1kb
green  open   .opendistro_security         _OJZQ8hcTQOfM9nZavgJRg   1   2         10            0    141.4kb         49.5kb
```

```
curl -XGET -u 'admin:admin' -k 'https://localhost:9201/_cat/indices?v'
health status index                        uuid                   pri rep docs.count docs.deleted store.size pri.store.size
green  open   security-auditlog-2023.05.27 UQdFSuTkThijevk_Thstxg   1   1        229            0    522.6kb        242.9kb
green  open   .opensearch-observability    HUAXz3lIQRuKElkUwo7a6w   1   2          0            0       624b           208b
green  open   .kibana_92668751_admin_1     TB7srDt2SICiixYPAyDE6g   1   1          0            0       416b           208b
green  open   bbuy_products                u12u9O9eRUOtjjE9UTe_mQ   3   2    1275077            0      3.6gb          1.1gb
green  open   .kibana_1                    Mz9lwQQmSxypUC6bV5MB7g   1   1          0            0       416b           208b
green  open   .opendistro_security         SiECET0STC-8L-Rjbk2juw   1   2         10            0    126.7kb         42.2kb
```

## Verifying the status of replication on the follower cluster after indexing:

```
curl -XGET -k -u 'admin:admin' 'https://localhost:9201/_plugins/_replication/bbuy_products/_status?pretty'
{
  "status" : "SYNCING",
  "reason" : "User initiated",
  "leader_alias" : "my-connection-alias",
  "leader_index" : "bbuy_products",
  "follower_index" : "bbuy_products",
  "syncing_details" : {
    "leader_checkpoint" : 1275074,
    "follower_checkpoint" : 1275074,
    "seq_no" : 1275075
  }
}
```

