# Elasticsearch

## Get version of ES
```
curl -XGET 'localhost:9200'
```

```
{
  "status" : 200,
  "name" : "node_name",
  "cluster_name" : "cluster_name",
  "version" : {
    "number" : "1.4.2",
    "build_hash" : "927caff6f05403e936c20bf4529f144f0c89fd8c",
    "build_timestamp" : "2014-12-16T14:11:12Z",
    "build_snapshot" : false,
    "lucene_version" : "4.10.2"
  },
  "tagline" : "You Know, for Search"
}
```
