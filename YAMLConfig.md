If you're not used to JSON or you'd like a more concise way to manage your jmxtrans queries using the simple [YAML][YAML] format.

There are 2 solutions:

* Use a YAML configuration having the same structure as JSON configuration files.
* Use the _yaml2jmxtrans_ tool (installed on your PATH) to generate your JSON queries from a lightweight YAML file.

# Using standard JSON/YAML configuration

Starting with JMXTrans 266, you can use YAML configuration files exactly as you use JSON configuration files.

## YAML Format

This YAML configuration file

```yaml
servers: 
  - port: 8004
    host: mysys.mydomain
    queries:
      - outputWriters:
          - "@class": com.googlecode.jmxtrans.model.output.GraphiteWriterFactory
            port: 2003
            host: mygraphite.mydomain
        obj: java.lang:type=OperatingSystem
        attr: 
          - SystemLoadAverage
          - FreePhysicalMemorySize
          - FreeSwapSpaceSize
          - OpenFileDescriptorCount
    numQueryThreads" : 2
```

is the same as this JSON confifuration file

```json
{
  "servers" : [ {
    "port" : "8004",
    "host" : "mysys.mydomain",
    "queries" : [ {
      "outputWriters" : [ {
        "@class" : "com.googlecode.jmxtrans.model.output.GraphiteWriterFactory",
          "port" : 2003,
          "host" : "mygraphite.mydomain"
      } ],
      "obj" : "java.lang:type=OperatingSystem",
      "attr" : [ "SystemLoadAverage", "FreePhysicalMemorySize", "FreeSwapSpaceSize", "OpenFileDescriptorCount" ]
    } ],
    "numQueryThreads: 2
  } ]
}
```

Note the `@class` key must be wrapped in double quotes because it starts with a special @ character.

## Usage

```
./jmxtrans.sh start config/config.yaml
```

or

```
./jmxtrans.sh start config
```

# Using _yaml2jmxtrans_ configuration converter

## YAML format

This example should mostly be self-explanatory.

```yaml
# Host/Port Graphite listens on
graphite_host: "graphite.yourdomain.com"
graphite_port: 2003

# Global port to query JMX on
# query_port and global_host_alias are mandatory
# Will accept a blank space if alias and host is provided in host sets
query_port: 5400
global_host_alias: 

# Query definitions, every query needs obj, resultAlias, attr
#   from jmxtrans format, "name" must be given for referencing
#   the query in host sets
queries:
    - name: mempool
      obj: "java.lang:type=MemoryPool,name=*"
      resultAlias: "memorypool"
      attr:
        - "Usage"
    - name: gc
      obj: "java.lang:type=GarbageCollector,name=*"
      resultAlias: "gc"
      attr:
        - "CollectionCount"
        - "CollectionTime"
    - name: hibernate
      obj: "Hibernate:type=statistics,name=*"
      resultAlias: "hibernate"
      attr:
        - "QueryExecutionMaxTime"
        - "Queries"
        - "TransactionCount"
    - name: sys
      obj: "java.lang:type=OperatingSystem"
      resultAlias: "sys"
      attr:
        - "SystemLoadAverage"
        - "AvailableProcessors"
        - "TotalPhysicalMemorySize"
        - "FreePhysicalMemorySize"
        - "TotalSwapSpaceSize"
        - "FreeSwapSpaceSize"
        - "OpenFileDescriptorCount"
        - "MaxFileDescriptorCount"
    - name: threads
      obj : "java.lang:type=Threading"
      resultAlias: "threads"
      attr:
        - "DaemonThreadCount"
        - "PeakThreadCount"
        - "ThreadCount"
        - "TotalStartedThreadCount"

# Define named sets of hosts that get the same queries
# query_names and hosts is a list
# Mention like machine01.yourdomain.com:5400;mac1
# if query_port and global_host_alias are not specified
sets:
  - setname: set1
    query_names:
            - mempool
            - gc
            - hibernate
            - sys
            - threads
    hosts:
            - machine01.yourdomain.com
            - machine02.yourdomain.com
            - machine03.yourdomain.com
            - machine04.yourdomain.com
  - setname: set2
    query_names:
            - mempool
            - gc
            - hibernate
            - sys
            - threads
    hosts:
            - machine11.yourdomain.com
            - machine12.yourdomain.com
            - machine13.yourdomain.com
            - machine14.yourdomain.com
```

## Usage

```
yaml2jmxtrans.py INFILE.yaml
```
    
Generates JSON jmxtrans configuration files, one for each host set
defined in _INFILE.yaml_.

## Dependencies

* python 2.6
* [PyYAML][PyYAML]

## Limitations

* _yaml2jmxtrans.py_ only supports [Graphite][Graphite] as an output
  writer as that's what I use. Please file issues if you require
  other output writers.
* The graphite host configuration is global, you cannot use
  yaml2jmxtrans.py_ if you need multiple output writers.

[YAML]: http://yaml.org/
[PyYAML]: http://pyyaml.org/
[DRY]: http://de.wikipedia.org/wiki/Don%E2%80%99t_repeat_yourself
[Graphite]: http://graphite.wikidot.com/