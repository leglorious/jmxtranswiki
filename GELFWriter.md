# GELFWriter

## Introduction

[Graylog](https://www.graylog.org/) is an advanced log collector and analytics platform. While there are several possible data collectors (so-called "Inputs") available for the platform, Graylog comes with its own, optimized log format called ["GELF"](http://docs.graylog.org/en/latest/pages/gelf.html). GELF is basically optimized JSON with a set of default attributes that is directly delivered to the Graylog input using UDP or TCP.

![Graylog architecture](http://docs.graylog.org/en/latest/_images/architec_bigger_setup.png)

The jmxtrans GELFWriter converts the queried results to GELF format, using the keys and values of each result as additional fields. The message is combined from the different results (<key>=<value>), separated by whitespace.

You can (and should) add additional fields in the GELFWriter-configuration, so that you can identify the log messages and put them in the right stream.

## Example Configuration

```json
{
  "servers" : [ {
    "host" : "w2",
    "alias" : "10.0.3.16:w2.fullname.com",
    "port" : "1099",
    "queries" : [ {
      "obj" : "java.lang:type=GarbageCollector,name=ConcurrentMarkSweep",
      "outputWriters" : [ {
        "@class" : "com.googlecode.jmxtrans.model.output.gelf.GelfWriter",
        "host" : "10.0.3.16",
        "port" : 12201,
        "additionalFields": {
          "tags": [ "jmx", "w2" ]
        }
      } ]
    } ]
  } ]
}
```

You can probably leave most settings to default. These are the most important settings:

* *host*: Hostname of the GELF-Input-Server
* *port (optional)*: Port (defaults to 12201)
* *transport (optional)*: Use TCP-transport ("tcp") or UDP-transport ("udp") (defaults to tcp)
* *additional_fields (optional)*: Add these fields to the GELF-Message before sending it to the GELF-Input

There are advanced configuration parameters for controlling the connection to the GELF-Input. These match the properties of the [GelfConfiguration-class](https://github.com/Graylog2/gelfclient/blob/master/src/main/java/org/graylog2/gelfclient/GelfConfiguration.java) from the official GELF-Client by Graylog. See there for their defaults.
