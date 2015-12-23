#StatsDWriter

## StatsD Writer

[StatsD](https://github.com/etsy/statsd) is a network daemon that runs on the Node.js platform and listens for statistics, like counters and timers, sent over UDP and sends aggregates to one or more pluggable backend services (e.g., Graphite).

This is a basic writer contributed by [Neil Houston](mailto:neil.houston@and.co.uk).

Example .json file that outputs HeapMemoryUsage directly to StatsD as a counter:

```
{
  "servers" : [ {
    "port" : "1099",
    "host" : "w2",
    "queries" : [ {
      "obj" : "java.lang:type=Memory",
      "attr" : [ "HeapMemoryUsage" ],
      "outputWriters" : [ {
        "@class" : "com.googlecode.jmxtrans.model.output.StatsDWriter",
        "port" : 8125,
        "host" : "192.168.192.133",
        "bucketType" : "c"
      } ]
    } ]
  } ]
}
```

The bucketType is appended before the newline as part of the StatsD protocol, so you can do complex operations there, such as ```c|@0.1```