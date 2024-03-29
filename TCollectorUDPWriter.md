#TCollectorUDPWriter

## TCollectorUDPWriter

Metrics are written to OpenTSDB via tcollector, over UDP, with this writer.
Advantages over directly connecting to OpenTSDB include buffering of metrics in case OpenTSDB is down and deduplication of redundant data points.
Please refer to the OpenTSDB documentation for more details.


### Limitations

* Attributes collected *must* be numeric; non-numeric values are now quietly discarded.
* Metric names, tag names, and tag values have a limited character set (as supported by OpenTSDB); see Sanitization below for more information.


### Configuration

* ```@class``` - ```com.googlecode.jmxtrans.model.output.TCollectorUDPWriter```
* host
* mergeTypeNamesTags
* metricNamingExpression


#### host

Hostname or IP address of the tcollector server.
Note that it is common to run tcollector on the same server as the application which reduces network traffic
and improves reliability of the collection process; in that scenario, localhost is used for this setting.

* **Default:** N/A.
* **Example:** localhost.
* **Required**


#### mergeTypeNamesTags

True => merge all typeNames into a single tag.  False => create one tag per typeNames element.
* **Default:** true.
* **Example:** false.


#### metricNamingExpression

JEXL expression used to calculate metric names, if defined; see below for more details.
* **Default:** null.
* **Example:** ```class + '.' + attribute```.


#### port

Port number on the tcollector server to which to connect.

* **Default:** N/A.
* **Example:** 8923.
* **Required**


#### tagName

Name of a tag assigned when a query returns multiple values.

* **Default:** type.
* **Example:** subtype.


#### tags

Object of constant tag names and values to include with the metrics generated by the query, if defined.
* **Default:** null.
* **Example:** ```{ "tag1" : "constant1", "tag2" : "constant2" }```


#### typeNames

List of keys from the MBean's object name used to create tags, if defined.  **Default:** null.

* **Example:** ```[ "Type", "Destination" ]```


### Metric Names

#### Default Naming

The attribute name is appended to the JMX Object class name to form the metric name sent to tcollector.
Here's an example for ActiveMQ :

```
org.apache.activemq.broker.jmx.QueueView.AverageEnqueueTime
```

Note that the ```resultAlias``` setting on the Query can be used to change the class name with a constant value.


#### JEXL-Based Naming

A JEXL expression can be used to formulate the metric names sent to tcollector for individual results
using the ```metricNamingExpression``` setting.

Below are the variables available for use with JEXL expressions:

|  VARIABLE  |  DESCRIPTION  |
|  ---  |  ---  |
|  alias  |  Query setting ```resultAlias```, if set.  |
|  attribute  |  Name of the attribute being queried.  |
|  class  |  Effective classname of the MBean - either ```alias```, if defined, or ```realclass``` otherwise.  |
|  realclass  |  Class name of the MBean.  |
|  result  |  Full ```Result``` object.  |
|  typename  |  Map of ObjectName keys to their values for the queried MBean.  |


Here are some examples:

|  EXAMPLE USE  |  EXAMPLE VALUE  |
|  ---  |  ---  |
|  ```alias```  |  ```my.result.alias```  |
|  ```attribute```  |  ```QueueSize```  |
|  ```class```  |  ```org.apache.activemq.broker.jmx.QueueView```  |
|  ```realclass```  |  ```org.apache.activemq.broker.jmx.QueueView```  |
|  ```result.query.attr[0]```  |  ```MemoryLimit```  |
|  ```typename['Type']```  |  ```Broker```  |


### typeNames Tag

When the ```typeNames``` setting is used (not to be confused with the JEXL variable of the same name), tags are added to the metric in one of two ways
based on the ```mergeTypeNamesTags``` setting.


#### ```mergeTypeNamesTags=true``` (default)

A single tag is added to the metric with the name of all the typeNames
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


#### ```mergeTypeNamesTags=false```

One tag is added to the metric for each of the names in the typeNames array.
The tag name for each is the name in the typeName list,
and the tag value is the matching ObjectName value.

For example:

```
typeNames: ["Type", "Destination"]
```

Results in two tags, one named ```Type``` and another named ```Destination```.
For the same ActiveMQ Queue, as mentioned above, named "testQueue",
the values are as shown below:


| Tag Name | Tag Value |
| --- | --- |
| ```Type``` | ```Queue``` |
| ```Destination``` | ```testQueue``` |



#### Source of Keys and Values

Note that the keys and values used for the ```typeNames``` setting are pulled from the JMX ObjectName
and not from attributes.  For example, ```Type``` and ```Destination``` are valid in the following example while ```Name``` is not:

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


### Multiple Value Queries, Attribute Name Mismatch, and the tagName Tag

When a query results in multiple values for a single attribute, such as an attribute containing an array,
each value is added as a separate metric.
This same logic is also applied when the atribute named in the Query does not match the attribute name in the result.
Additionaly, a tag is added to each metric to uniquely identify each metric data point in the query.
he name of the tag defaults to ```type``` but can be configured in the settings with the ```tagName``` setting.

Values for tagName depend on the type of complex data in the query result:

* ```CompositeData``` - tagName is the name of the bottom-level data element.
* ```TabularDataSupport``` - tagName is the name of the bottom-level data element.
* ```ObjectName[]``` - tagName is the canonical name of the object (using ObjectName.getCanonicalName()).
* ```Object[]``` - tagName is the name of the attribute with "." and the position in the array appended (e.g. Counts.0).

Note that information in ```CompositeData``` and ```TabularDataSupport``` may be split into multiple attributes
in the result depending on the hierarchy of data;
the additional tag is only included when there are multiple values for an individual attribute.
Please try it out on a target to get a better understanding of the results.



### Sanitization of Metrics Names, Tag Names, and Tag Values

OpenTSDB only supports the following character set for metric names, tag names, and tag values.
Here is the list (space-separated; note spaces are excluded from this set):

``` - _  . / a-z A-Z 0-9```

Any characters not in this set are sanitized as follows:

* Single and double quotes (```"``` and ```'```) are removed
* All other characters are replaced with underscore, ```_```


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
                            "@class": "com.googlecode.jmxtrans.model.output.TCollectorUDPWriter",
                            "host": "localhost",
                            "port": 8923,
                            "typeNames": ["Destination"],
                            "tags": { "test-tag1" : "test-tag1-value", "test-tag2" : "test-tag2-value" }
                        },
                        {
                            "@class": "com.googlecode.jmxtrans.model.output.TCollectorUDPWriter",
                            "host": "localhost",
                            "port": 8923,
                            "typeNames": ["Destination"],
                            "tags": {}
                        },
                        {
                            "@class": "com.googlecode.jmxtrans.model.output.TCollectorUDPWriter",
                            "host": "localhost",
                            "port": 8923,
                            "typeNames": ["Destination"],
                            "metricNamingExpression": "realclass + '.' + attribute",
                            "tags": {}
                        }
                    ],
                    "resultAlias": "custom.queue.metric.base.name",
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

NOTES:
* The first example writer configuration depicts use of resultAlias and custom tags.
* The second example writer configuration depicts use of resultAlias without custom tags.
* The third example writer configuration depicts use of metricNamingExpression to override the resultAlias for one writer.
