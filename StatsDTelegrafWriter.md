#StatsDTelegrafWriter

## StatsD Telegraf Writer

[StatsD](https://github.com/etsy/statsd) is a network daemon that runs on the Node.js platform and listens for statistics, like counters and timers, sent over UDP and sends aggregates to one or more pluggable backend services.  

**This writer is intended to work with [Telegraf](https://www.influxdata.com/time-series-platform/telegraf/)**, as it takes advantage of extended support for tags, something other StatsD servers do not support.

This is a basic writer contributed by [Nate Good](mailto:nate@codecounselors.com).

Note: This is currently only available in the [SNAPSHOT builds (>260-SNAPSHOT)](https://oss.sonatype.org/content/repositories/snapshots/org/jmxtrans/jmxtrans/)

### Configuration

Example json query that outputs JVMMemory values to Telegraf in StatsD form with tags:

```
"queries" : [
{
  "obj" : "java.lang:type=Memory",
  "attr" :  ["HeapMemoryUsage","NonHeapMemoryUsage","ObjectPendingFinalizationCount"],
  "resultAlias": "JVMMemory",
  "outputWriters" : [ {
    "@class" : "com.googlecode.jmxtrans.model.output.StatsDTelegrafWriterFactory",
    "port" : 8125,
    "host" : "localhost",
    "tags" : { "instance" : "tomcat01" },
    "bucketType" : "c,c,c"
  }]
}
```

* `resultAlias` is required.  It is a borrowed concept from the InfluxDBWriter, and will become the InfluxDB measurement name.
* `tags` can be used to add an additional tags to every event for a given query
* `bucketType` can be specified as a single value which will be applied to every event, or you may specify a different bucket type for each query attribute.  
  - Note: Currently you can not mix int64 (counter) and float metrics for the same measurement.  This means if you use a counter, then all attributes must be counters.  You can however mix timings and sets, for example.
  - The bucketType is appended before the newline as part of the StatsD protocol, so you can do complex operations there, such as ```c|@0.1```

### Schema Design

JMX Query attributes will become tags, along with the key/value pairs they contain.  For example, HeapMemoryUsage would emit distinct StatsD event for every attribute/key pair, but combined into the same writer output:
```
JVMMemory,jmxport=10012,attribute=HeapMemoryUsage,resultKey=committed:12345|c\n
JVMMemory,jmxport=10012,attribute=HeapMemoryUsage,resultKey=init:23456|c\n
JVMMemory,jmxport=10012,attribute=HeapMemoryUsage,resultKey=used:45678|c
```

These will each correspond to an InfluxDB Series, as shows by a `show series` InfluxDB Query
```
JVMMemory,attribute=HeapMemoryUsage,host=qa,instance=qa1,jmxport=10012,metric_type=counter,resultKey=committed
JVMMemory,attribute=HeapMemoryUsage,host=qa,instance=qa1,jmxport=10012,metric_type=counter,resultKey=init
JVMMemory,attribute=HeapMemoryUsage,host=qa,instance=qa1,jmxport=10012,metric_type=counter,resultKey=used
```

- `jmxport` is a tag added by the writer

This writer allows you to retrieve InfluxDB events in a flexible way.  For example: `select * from JVMMemory group by attribute,resultKey limit 2`  

If you're using a tool like Grafana this simplifies charting as well.