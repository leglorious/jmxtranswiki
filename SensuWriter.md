Send metrics to [Sensu](http://sensuapp.org/) monitoring  service.

* Source: SensuWriter.java
* The SensuWriter sends metrics in Sensu Event format to Sensu Client's event data port (tcp 3030).  The output block is formatted in Graphite Message Format.

## Settings
 * `host`: The destination Sensu Client. Defaults to localhost.
 * `handler`: The Sensu handler name to pass in the event data. Defaults to graphite.

## Sample configuration

```json
{
  "servers" : [ {
    "port" : "8888",
    "host" : "localhost",
    "queries" : [ {
      "obj" : "java.lang:type=Memory",
      "resultAlias": "jvm.memory",
      "attr" : [ "HeapMemoryUsage", "NonHeapMemoryUsage" ],
      "outputWriters" : [ {
        "@class" : "com.googlecode.jmxtrans.model.output.SensuWriter",
        "host" : "localhost",
        "handler" : "graphite"
      } ]
    } ]
  } ]
}
```
