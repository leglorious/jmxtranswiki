#OpenTSDBWriter

## OpenTSDBWriter

Metrics are written to OpenTSDB with this writer.

### Limitations

* Attributes collected *must* be numeric; otherwise, metrics will fail when sent to OpenTSDB.


### Configuration

*```outputWriters```*
* ```@class``` - ```com.googlecode.jmxtrans.model.output.OpenTSDBWriter```
* settings

*```settings```*
* host - hostname or IP address of the OpenTSDB server
* port - port of the OpenTSDB server
* typeNames - list of attribute names from the MBean used to format a dynamic tag for the metric
* tags - object of constant tag names and values to include with the query


### Metric Names

The attribute name is appended to the JMX Object class name to form the metric name sent to OpenTSDB.
Here's an example for ActiveMQ :

```
org.apache.activemq.broker.jmx.QueueView.AverageEnqueueTime
```


### typeNames Tag

When the ```typeNames``` setting is used, a single tag is added to the metric with the name of all the typeNames
concatenated, and a value of the ObjectName keys with those names concatenated with an underscore between each.
For example:

```
typeNames: ["Type", "Destination"]
```

Results in a tag named *```TypeDestination```*.  For an ActiveMQ Queue named "testQueue", the value of this tag is
then *```Queue_testQueue```*:


```
TypeDestination=Queue_testQueue
```

Note that these values are pulled from the JMX ObjectName and not from attributes.  For example, the ```Type``` and
```Destination``` are valid here while ```Name``` is not:

*ObjectName*
```
org.apache.activemq:BrokerName=localhost,Type=Queue,Destination=testQueue
```

*Attributes*
```
...
Name = testQueue
...
```



### Example Configuration

Here is an example configuration for use with ActiveMQ.

```
{
    "servers": [
        {
            "port": "1099",
            "host": "localhost",
            "username": "jmxReadonlyUser",
            "password": "secret",
            "queries": [
                {
                    "outputWriters": [
                        {
                            "@class": "com.googlecode.jmxtrans.model.output.OpenTSDBWriter",
                            "settings": {
                                "host": "tsd1.example.com",
                                "port": 4242,
                                "typeNames": ["Destination"],
                                "tags": { "test-tag1" : "test-tag1-value", "test-tag2" : "test-tag2-value" }
                            }
                        },
                        {
                            "@class": "com.googlecode.jmxtrans.model.output.OpenTSDBWriter",
                            "settings": {
                                "host": "tsd2.example.com",
                                "port": 4242,
                                "typeNames": ["Destination"],
                                "tags": {}
                            }
                        }
                    ],
                    "obj": "org.apache.activemq:BrokerName=*,Type=Queue,*",
                    "attr": [
                        "QueueSize",
                        "MemoryPercentUsage",
                        "AverageEnqueueTime"
                    ]
                }
            ]
        }
    ]
}
```
