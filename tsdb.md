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
- High cardinality (many values for an attribute)

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
  