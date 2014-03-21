**This Wiki page describes the operation and configuration of code which has not yet been committed to the JMXTrans project.** For reference, please see [#41](https://github.com/jmxtrans/jmxtrans/pull/41)

## Introduction

JMX Operations, while being a core component of the Java Management Extensions (JMX) is seldom actually implemented in JMX monitoring applications. Operations allow external (monitoring) applications to invoke methods exported by MBeans, possibly passing parameters to the method. For example, an application may contain a JMX operation which verifies the proper operation of an entire software stack and returns the overall latency of an end-to-end test. JMXTrans may invoke this method and pass the returned latency value to Ganglia, Graphite, etc. which, in turn, could alert an administrator of poor performance issues.

## Configuration

The following example JSON configuration monitors one JMX Operation "getLoggerLevel" which exists in MBean "java.util.logging:type=Logging". This method takes one String parameter of value "global". 

```
{
  "servers" : [ {
    "port" : "1099",
    "host" : "w2",
    "queries" : [ {
      "outputWriters" : [ {
        "@class" : "com.googlecode.jmxtrans.model.output.StdOutWriter",
        "settings" : {
        }
      } ],
      "obj" : "java.util.logging:type=Logging",
      "attr" : [  ],
      "oper" : [ {
 	      	"method" : "getLoggerLevel",
   		"parameters" : [ "global" ]
      	} ]
    } ],
    "numQueryThreads" : 2
  } ]
}
```
