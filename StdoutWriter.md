# Standard Out Writer

This is a basic writer that just prints the list of Results from a Query to System.out (sysout). This makes debugging your Queries a lot easier because you can see what the results look like.

This is an example of the configuration for 3 different queries, writing to StdOut. At the very bottom you can see that ```numQueryThreads=2``` which means that 2 of the queries will be executed concurrently and as soon as one of them is done the final one will execute.

```
{
  "servers" : [ {
    "port" : "1099",
    "host" : "w2",
    "queries" : [ {
      "outputWriters" : [ {
        "@class" : "com.googlecode.jmxtrans.model.output.StdOutWriter"
      } ],
      "obj" : "java.lang:type=Memory",
      "attr" : [ "HeapMemoryUsage", "NonHeapMemoryUsage" ]
    }, {
      "outputWriters" : [ {
        "@class" : "com.googlecode.jmxtrans.model.output.StdOutWriter"
      } ],
      "obj" : "java.lang:name=CMS Old Gen,type=MemoryPool",
      "attr" : [ "Usage" ]
    }, {
      "outputWriters" : [ {
        "@class" : "com.googlecode.jmxtrans.model.output.StdOutWriter"
      } ],
      "obj" : "java.lang:name=ConcurrentMarkSweep,type=GarbageCollector",
      "attr" : [ "LastGcInfo" ]
    } ],
    "numQueryThreads" : 2
  } ]
}
```

## Result Serializers

You can plug a `ResultSerializer` in the `StdOutWriter` to customize how the query result is formatted:
```
{
  "@class" : "com.googlecode.jmxtrans.model.output.StdOutWriter"
  "resultSerializer": {
    "@class": "com.googlecode.jmxtrans.model.output.ToStringResultSerializer",
    "verbose": false
  }
}
```

You can choose among basic `ResultSerializer` implementations:
* `ToStringResultSerializer` is the default one and is mostly used for debugging purpose. It has a `verbose` flag to print more information.
* `KeyValueResultSerializer` print result in the form key=value where the key is built using MBean Id, attribute name...
* Your own.