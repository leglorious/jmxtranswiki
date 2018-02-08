#ZabbixWriterFactory

## Zabbix Writer Factory

**UNRELEASED** - This is a new jmxtrans feature that has not been released yet. You will need to build jmxtrans from source in order to use this new writer until then.

Send metrics to [Zabbix](https://www.zabbix.com/) monitoring server/proxy.

Uses the Zabbix Sender protocol - https://www.zabbix.org/wiki/Docs/protocols#Zabbix_protocols

Here is an example .json file that outputs HeapMemoryUsage and NonHeapMemoryUsage directly to Zabbix:

```
{
  "servers" : [ {
    "host" : "localhost",
    "alias" : "tomcat.dev",
    "port" : "1099",
    "queries" : [ {
      "obj" : "java.lang:type=Memory",
      "attr" : [ "HeapMemoryUsage", "NonHeapMemoryUsage" ],
      "outputWriters" : [ {
        "@class" : "com.googlecode.jmxtrans.model.output.ZabbixWriterFactory",
        "port" : 10051,
        "host" : "zabbix.dev"
      } ]
    } ]
  } ]
}
```

On the Zabbix Server side you will need to create trapper items matching the keys from jmxtrans.

**IMPORTANT** - ZabbixWriterFactory prefixes everything with 'jmxtrans.' to avoid collisions in Zabbix

Here's an example:
![zabbix-jmxtrans-trapperitem](https://user-images.githubusercontent.com/971900/35945930-865b4a70-0c27-11e8-8291-8d43f0d3dda3.png)

