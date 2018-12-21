# NagiosWriter

## Nagios Writer

This writer is for Nagios:

##  Conf example:

```
{ "outputWriters" : [ {
"@class" : "com.googlecode.jmxtrans.model.output.NagiosWriter",
"settings" : {
"typeNames" : [ "name" ],
"outputFile": "/tmp/denis_jmx",
"nagiosHost": "HOSTNAME",
"prefix": "Pre ",
"posfix": " Pos",
"filters": ["HeapMemoryUsage_used", "NonHeapMemoryUsage_max"],
"thresholds": ["@1000000000:3000000000,", ""]
}
} ],
"obj" : "java.lang:type=Memory",
"resultAlias": "memory",
"attr" : [ "HeapMemoryUsage", "NonHeapMemoryUsage", "" ]
}
```

outputFile - Should be the Nagios command file.
- Cannot be empty.
nagiosHost - The host name defined at Nagios.
- Cannot be empty.
prefix - Used in the service name to prefix the service description.
- Can be empty.
posfix - Used in the service name to posfix the service description.
- Can be empty.
filters - Attr that should be used as a service name.
-Cannot be empty.
- Attr HeapMemoryUsage can create 3 outputs:
- HeapMemoryUsage_used
- HeapMemoryUsage_min
- HeapMemoryUsage_max
For each one that you want to send to Nagios you must include it on filters.
thresholds - It defines the threshold for each filter using the Nagios range definition.
- Cannot be empty.
- Syntax:
- Each threshold has two parts 'warning,critical'
- Each part can contain a range definition.
- http://nagiosplug.sourceforge.net/developer-guidelines.html#THRESHOLDFORMAT
- An empty ("") threshold means always OK.
- If there is only one field or it starts with comma that service will never be in warning state.
- "@1000000000"
- ",@1000000000"
- If the fields ends with comma that service will never be in critical state:
- "@1000000000,"

Filters and threholds are bound, so threholds must be in the same position as the filters which it must apply.
Also they must have the same size.
