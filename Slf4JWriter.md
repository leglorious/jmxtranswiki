# Slf4J Writer

Prints the query results using the [SLF4J](https://www.slf4j.org/) logging library.
```
      "outputWriters" : [ {
        "@class" : "com.googlecode.jmxtrans.model.output.Slf4JOutputWriter",
        "logger": "com.googlecode.jmxtrans.result"
      } ],
```
This writer doesn't print anything in the logs by default, it's required to either use the MDC variables or the `ResultSerializer` to configure the log format.

## MDC

It provides SLF4J mapped diagnostic contexts (MDC) for the following variables:
* server
* metric
* value
* resultAlias
* attributeName
* key
* epoch

These variables can then be used in the Logback configuration to tune the log format:
```
<appender name="CONSOLE" class="ch.qos.logback.core.ConsoleAppender"> 
  <layout>
    <Pattern>%date %X{server} %X{metric}=%X{value} - %msg%n</Pattern>
  </layout> 
</appender>
```

## Result Serializer

Like [StdoutWriter](StdoutWriter#result-serializers), you can configure how the result get formatted.
The `Slf4JOutputWriter` has a no-op `ResultSerializer` implementation by default.
