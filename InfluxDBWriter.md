#InfluxDBWriter

## InfluxDB Writer

[InfluxDB](http://graphite.wikidot.com/) is an open-source distributed time series database. All you have to do to configure jmxtrans to use InfluxDB is to setup the InfluxDBWriter with basic connection details and the writer will send what ever you want to measure to it.
All query result values are written to InxfluxDB as fields (of type: number, float, boolean or string). The Server hostname is written as a tag as are typeName,objDomain,className and attributeName (see resultTags attribute below to reduce the tag list)

Here is an example .json file that outputs HeapMemoryUsage and NonHeapMemoryUsage directly to InfluxDB:

```
{
  "servers" : [ {
    "port" : "1099",
    "host" : "w2",
    "queries" : [ {
      "obj" : "java.lang:type=Memory",
      "attr" : [ "HeapMemoryUsage", "NonHeapMemoryUsage" ],
      "resultAlias":"jvmMemory",
      "outputWriters" : [ {
        "@class" : "com.googlecode.jmxtrans.model.output.InfluxDbWriterFactory",
        "url" : "http://127.0.0.1:8086/",
        "username" : "admin",
        "password" : "admin",
        "database" : "jmxDB"
      } ]
    } ]
  } ]
}
```

InfluxDBWriter attributes:

Query attributes
* *resultAlias* - This is used as the [measurement](https://influxdb.com/docs/v0.9/concepts/key_concepts.html#measurement) container in the database

Output Writer attributes
* *url* - The URL for the InfluxDB server
* *username* - The username for InfluxDB
* *password* - The password for InfluxDB
* *database* - The name of the InfluxDB database to write the measurements to (The database is created if it    does not exist)

* *resultTags* (Optional) - An array of JMX attributes to write as tags. All attributes listed below are written as tags by default so this property should only be used in the case where less attributes are required:

 * typeName
 * objDomain
 * className
 * attributeName

* *retentionPolicy* (Optional) - The [retention policy](https://influxdb.com/docs/v0.9/concepts/key_concepts.html#retention-policy") to use for the data written to the measurement. The "default" retention policy is automatically applied by this writer which ensures infinite duration and a replication factor set to the number of nodes in the cluster.

* *writeConsistency* (Optional) The consistency setting to use when writing to the database. The default setting of "ALL" is automatically applied by this writer: Allowed values are:

 * ALL = Write succeeds only if write reached all cluster members.
 * ANY = Write succeeds if write reached any cluster members.
 * ONE = Write succeeds if write reached at least one cluster members.
 * QUORUM = Write succeeds only if write reached a quorum of cluster

Note: Clustering is still under development for InfluxDB but these are write settings that are provided by the Influx DB [Java Client](https://github.com/influxdb/influxdb-java) the source can be found [here](https://github.com/influxdb/influxdb-java/blob/ee202ea5a55c1073d84e5eaf00c672076f204f65/src/main/java/org/influxdb/InfluxDB.java)
