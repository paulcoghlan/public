# Grafana

- Front/backend application intefacing to TSDB
- Graphite, InflubDB

## TSDB

See [https://grafana.com/docs/grafana/latest/basics/timeseries/]

- e.g. Graphite, InfluxDB, Prometheus
- Modern TSDB take advantage of the fact that measurements are only ever appended, and rarely updated or removed
- Only need to record deltas - e.g. for timestamps:
`572524345, +30, -1, +1, +0`
- Measurements filtered using tags or labels
- Labels are key/value pairs for identifying Dimensions

## Prometheus

- Heritage: Soundcloud engineers (ex-Google)
- Pull-based HTTP request to collectors
- Built-in service discovery
- Alerts
- Stores float64 metric_name{ Key, Value } pairs, easier to extend when adding new datacentre etc.
- Automatically generated labels and time series: `job`, `instance`

### PromQL

Good reference is: [https://valyala.medium.com/promql-tutorial-for-beginners-9ab455142085]

- Counters
- Gauges
- Timers/Complex Types
  - Histograms
  - Summaries
  - Scalars
  - Instant Vector: maps to single datapoint
    - Can be charted
    - Can do arthmetric
    - Convert to Range Vector by appending a duration
  - Range Vector: maps to sliding window e.g. `rate(http_requests_total[5m]`
    - Mostly used for graphs
    - Cannot be charted
    - Can do arthmetric
- Filter multiple time series using `metric_name{<label selector>}`, e.g.`node_network_receive_bytes_total{device="eth1"}`
  - Selectors:
    - `=`
    - `!=`
    - `=~` (regexp, handy for *OR*s `{device=~"eth1|lo"}`)
    - `!~` (regexp, e.g. `{device!~"eth.+"}`)
    - ',' for *AND* e.g. `node_network_receive_bytes_total{instance="node42:9100", device=~"eth.+"}`
- Rates (e.g. `rate(pings_time_seconds_total[5m])/rate(pings_count_total[5m])`)
  - Smaller delta increases noise, bigger delta smooths graph
  - `rate()` works for gauges, not counters!
- Operators: [https://prometheus.io/docs/prometheus/latest/querying/operators/]  
  - Aggregations:
    - `sum`, `min`, `max`, `avg`, `stddev`, `stdvar`, `count`, `quartile`
  - Grouping
    - `BY`, e.g. `sum(rate(node_network_receive_bytes_total[5m])) by (instance)`
    - `WITHOUT`
- Vector Matching
  - One to One: `<vector expr> <bin-op> ignoring(<label list>) <vector expr>`
  - Many to One: complex!

### Alerting

- Use alertmanager to manage duplicates when you have HA Prometheus instances.
- Active alerts haven't lasted `for xm` - they then become firing 

## Cortex

Cortex is a time-series store built on Prometheus

- Horizontally scalable
- Highly Available
- Durable, long-term storage
- Multi-tenant

- Essentially a de-dupe, compression engine - uses 3rd party long-term storage (S3 etc).
- Handles clustering automagically?
- Ingester
- NoSQL Index
- Blob store

PromQL engine does sums

- Cortex writes 3 replicas of each chunk
- Chunks need merging/deduping

## Loki

- Promtail uses same labels as Prometheus for consistency
- Use BoltDB locally

## Jsonnet/Tanka

- Remember: everything is compiled to JSON!
- Jsonnet needed as YAML doesn't work for dynamic domain names, environments, etc in k8s
- Introduces functions, deep merging with `+`
- Visible objects have `:`
- Hidden objects have `::`
- `self` refers to the current object.
- `$` refers to the outer-most object.
- Debug using :````
```
error 'Printing' + std.toString(obj)
```

- Jsonnet stopped being developed, so Grafana took best bits and rebuilt it into [Tanka](https://github.com/grafana/tanka)
- Good reference: https://jsonnet.org/ref/language.html

- Root object in `main.jsonnet`:

```
{
    "some_deployment": { /* ... */ },
    "some_service": { /* ... */ }
}
```

- Add key/value pairs like this:
```
{
  _config:: {
    key1: {
      key2: value
    }
  }
}
```
* Add constructor function like this:

```
{
  // hidden k namespace for this library
  k:: {
    deployment: {
      new(name, containers): {
        apiVersion: "apps/v1",
        kind: "Deployment",
        metadata: {
          name: name,
```

```
(import "kubernetes.libsonnet") +
...
{
  grafana: {
    deployment: $.k.deployment.new("grafana", [{
      image: 'grafana/grafana',
      name: 'grafana',
      ports: [{
          containerPort: 3000,
          name: 'ui',
      }],
    }]),
```

- 
- Workflow: `tk show`, `tk diff`, `tk apply`
- Simplified abstractions: just Jsonnet and environments
- Jsonnet --tanka--> kubectl
- Adds:
  - Context discovery: confirm cluster
  - Enhanced diff: server side

- Starts with `main.jsonnet` (same as main.go)
- Select cluster with `spec.json`

## Collectors

- collectd [https://collectd.org/]
- statsd [https://github.com/statsd/statsd]
- Prometheus exporters
- Push:
  - Easy to push to more TSDBs
  - TSBD might get flooded
- Pull:
  - More authentic data, TSDB has more control
  - Firewalls/VPNs may make it hard to access collector from TSDB

## InfluxDB Format

[https://docs.influxdata.com/influxdb/v1.7/write_protocols/line_protocol_tutorial/#syntax]

```txt
  weather,location=us-midwest temperature=82 1465839830100400200
  |    -------------------- --------------  |
  |             |             |             |
  |             |             |             |
+-----------+--------+-+---------+-+---------+
|measurement|,tag_set| |field_set| |timestamp|
+-----------+--------+-+---------+-+---------+

measurement:   weather
tags:          location=us-midwest
field_set:     temperature=82
timestamp:     1465839830100400200
```

## WAL - write ahead log

- Once a server agrees to perform an action, it should do so even if it fails and restarts losing all of its in-memory state
- WAL stores data uncompressed
- Changes written to disk log file before applied to database in compressed form

## Requirements

- Append-only
- Ordered by time
- Bulk reads, single writes
- Most recent first
- High cardinality (many unique combinations of all labels)
  - Active series is the one receiveing data
  - Churn rate is how often time series become active/inactive (e.g. changing `pod_name` in k8s). Use `rate(prometheus_tsdb_head_series_created_total[5m])` to check
  - 

Delta encoding
Varint to reduce space

[https://github.com/nakabonne/tstorage/]

## Compression Techniques

- Integer compression
  - Delta encoding
  - Delta-of-delta encoding
  - Simple-8b
  - Run-length encoding

- Floating point compression:
  - XOR-based compression
-Data-agnostic compression:
  - Dictionary compression
  