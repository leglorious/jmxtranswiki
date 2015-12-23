Send metrics to [Librato](https://metrics.librato.com/) monitoring metrics service.

* Source: LibratoWriter.java
* The Librato Writer uses the [POST /v1/metrics](http://dev.librato.com/v1/post/metrics) HTTP API.

## Counter and Gauge metrics configuration

Librato distinguishes 2 types of metrics: `counter` (ever increasing values like 'request count') and `gauge` (fluctuating values like 'pool size').
But there is no type for metrics in jmxtrans. As gauge seems to be the most used type, LibratoWriter will send metrics as gauge to Librato.

## Settings
 * `url`: Librato server URL. Optional, default value: `https://metrics-api.librato.com/v1/metrics`.
 * `user`: Librato user. Mandatory.
 * `token`: Librato token. Mandatory.
 * `libratoApiTimeoutInMillis`: read timeout of the calls to Librato HTTP API. Optional, default value: 1000.
 * `source`: Librato . Optional, default value: `#hostname#` (the hostname of the server).
 * `proxyHost`: Proxy Host. Optional.
 * `proxyPort`: Proxy Port. Optional.

## Sample configuration

```json
{
  "servers" : [ {
    "port" : "8888",
    "host" : "localhost",
    "queries" : [ {
      "obj" : "java.lang:type=Memory",
      "resultAlias": "jvm.memory",
      "attr" : [ "HeapMemoryUsage", "NonHeapMemoryUsage" ],
      "outputWriters" : [ {
        "@class" : "com.googlecode.jmxtrans.model.output.LibratoWriter",
        "username" : "user@example.com",
        "token" : "af8f7a8500a9d385adf788aeaf24d91449d12cdfeb"
      } ]
    } ]
  } ]
}
```
