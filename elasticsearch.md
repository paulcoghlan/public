# Elasticsearch

## Health

- Do this first: `GET _cluster/health?v`
- Manually fix shards: `GET /_cat/shards?v`
- Super handy, shows shards per cluster: `GET /_cat/allocation?v`
- Find out why cluster is red: `GET _cluster/allocation/explain`
... then restore broken indexes from most recent snapshot (may need to delete existing index first!)
- Index health: `GET _cluster/health?level=indices`
- Shard health: `GET _cluster/health?level=shards`

- Get snapshot progress:

```sh
GET /_snapshot/_status
GET /_snapshot/found-snapshots/_status
```

- Look for snapshots when corrupted: `GET /_snapshot/found-snapshots/_all?ignore_unavailable=true`

## Stats

- Cluster Stats API: `GET _cluster/stats`
- Node Stats API: `GET _nodes/stats`
- Indices Stats API: `GET my_index/_stats`
- See Thread pool stats: `GET _cat/thread_pool?v&s=queue:desc,rejected:desc`
- Node stats: `GET _nodes/instance-0000000017/stats`

## _cat

- `_cat` API is a wrapper around many of the Elasticsearch JSON APIs: `GET _cat/nodes`
- use “*” to retrieve all columns, add the “v” parameter to display the column names in the response
`GET _cat/nodes?v&h=name,disk.avail,search.query_total,heap.percent`

## Indicies and Aliases

- Get settings, mappings and aliases of an index `GET <index>/`
- Explore existing aliases `GET /_cat/aliases/<alias_pattern>?v`

## Shards

- Locating unassigned ahards: `GET _cluster/allocation/explain`
- Unassigned shards for a specific index: `GET _cluster/allocation/<index>/explain`
- Try to reassign shards: `POST /_cluster/reroute?retry_failed=true`

- Check migration progress:

```sh
GET /_cat/recovery?active_only&v
GET _cat/recovery?active_only&v&h=i,s,t,ty,st,source_node,target_node,fp,b,bp
```

## Snapshots

- Check snapshot index restore: `GET /_cat/recovery?v&h=index,shard,type,stage,repository,files_percent,bytes_percent`
- Restore from specific snapshot

```sh
"transient": {
 ... 
 "restore_snapshot": {
      "repository_name": "found-snapshots",
      "snapshot_name": "scheduled-1527846004-instance-0000000001",
      "strategy": "full"
    }
```

## Tasks

- Task Management API shows tasks currently executing on the nodes: `GET _tasks`
- Pending Tasks API shows cluster-level changes that have not been executed yet: `GET _cluster/pending_tasks`
- Wait for status: `GET _cluster/health?wait_for_status=green`

## Debugging

- Generate OOM Exception: `PUT /oom {"settings":{"number_of_shards":1024, "number_of_replicas":1024}}`
- Slowlogs:

```sh
PUT /<index>/_settings
{
        "index.search.slowlog.threshold.query.warn": "10s",
        "index.search.slowlog.threshold.query.info": "5s",
        "index.search.slowlog.threshold.query.debug": "2s",
        "index.search.slowlog.threshold.query.trace": "500ms",
        
        "index.search.slowlog.threshold.fetch.warn": "1s",
        "index.search.slowlog.threshold.fetch.info": "800ms",
        "index.search.slowlog.threshold.fetch.debug": "500ms",
        "index.search.slowlog.threshold.fetch.trace": "200ms",
        "index.search.slowlog.level": "info"
}
```

- Heap Dump: `setuser <user> kill -3 <Java PID>`
- Thread Dump: `setuser <user> jcmd <pid> Thread.print`
- JVM Memory check: `sudo -u <user> jstat -gcutil 43`

## Restore from stale primary

- Cluster Reroute Stale Shard: `GET _cat/indices/<index_name>?v`
Find <index_uuid>
- `GET _cluster/allocation/explain`.  Look for candidate nodes to restore from
If more than one node, look on disk for largest index dir:

```sh
cd /app/data/nodes/0/indices/
dh -sh <index_uuid>
```

- Re-assign shard using stale primary

```sh
POST /_cluster/reroute
{
     "commands" : [
          {
              "allocate_stale_primary" : {
                "accept_data_loss":true,
                "index" : "insider-threat-public-2019.10.05", "shard" : 3,
                "node" : "instance-0000000109"
                }
            }
    ]
}
```

- Delete all indices

```sh
PUT /_cluster/settings
{
    "transient" : {
        "action.destructive_requires_name" : "false"
    }
}
```

- Speed up bulk reindex:

```sh
PUT /<index-name>/_settings
{
    "index" : {
        "number_of_replicas" : 0,
        "refresh_interval" : -1
    }
}
```

## Search query example

```sh
GET index-*/_search
{
  "size": 0,
  "query": {
    "bool": {
      "must": [
        {
          "match_all": {}
        },
        {
          "match_phrase": {
             "logger": {
               "query": "org.elasticsearch.xpack.slm.SnapshotRetentionTask"
              }
          }
        },
        {
          "query_string": {
            "default_field": "message.keyword",
            "query": "starting snapshot retention deletion for"
          }
        },
        {
          "range": {
            "@timestamp": {
              "gte": "now-1h"
            }
          }
        }
      ]
    }
  },
  "aggs": {
    "request_path": {
      "terms": {
        "field": "message"
      }
    }
  }
}
```

## Reindex

Reindex (copy) Local Index

```sh
POST _reindex
{
  "source": {
    "index": "nginx_json_elastic_stack_example"
  },
  "dest": {
    "index": "test_reindex"
  }
}
```

Reindex Remote Index

```sh
POST _reindex
{
  "source": {
    "remote": {
      "host": "http://elasticsearch:9200",
      "username": "elastic",
      "password": "<password>"
    },
    "index": "nginx_json_elastic_stack_example"
  },
  "dest": {
    "index": "test_remote_reindex"
  }
}
```

## Sample Logstash config

```sh
cat nginx_json_logs | ~/tools/logstash-5.2.1/bin/logstash -f nginx_json_logstash.conf
$ vi nginx_json_logstash.conf 

input {
  stdin {
    codec => json
    }
}

filter {

  date {
    match => ["time", "dd/MMM/YYYY:HH:mm:ss Z" ]
    locale => en
  }

  geoip {
    source => "remote_ip"
    target => "geoip"
  }

  useragent {
    source => "agent"
    target => "user_agent"
  }

  grok {
    match => [ "request" , "%{WORD:request_action} %{DATA:request1} HTTP/%{NUMBER:http_version}" ]
  }
}

output {
  stdout  {
    codec => dots {}
  }

  elasticsearch {
    hosts => ["https://elasticsearch:9243"]
    cacert => "/Users/paul/mycert.pem"
    index => "nginx_json_elastic_stack_example"
    document_type => "logs"
    template => "./nginx_json_template.json"
    template_name => "nginx_json_elastic_stack_example"
    template_overwrite => true
  }
}
```
