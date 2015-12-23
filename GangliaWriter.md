#GangliaWriter

## GangliaWriter

[Ganglia](http://ganglia.sourceforge.net) is another monitoring / graphing tool that is similar to Graphite in that it produces graphs of metrics. By default it includes the collection of a lot of server metrics such as cpu load and memory usage. Ganglia uses rrd as the backend storage format. It also uses a series of daemons to help spread the graphing load around.

jmxtrans has a writer that connects to a gmond (not gmetad!) process and writes data directly to it using UDP. One nice feature of Ganglia is that you do not need to configure it when you add new hosts.

![ganglia](http://jmxtrans.googlecode.com/svn/wiki/ganglia.png)

As described in the (borrowed) image above, you can see that jmxtrans is writing data to a gmond instance. Since I support host spoofing, whatever is set as ```host``` or optionally ```alias``` is what is sent to Ganglia as the host. Thus, jmxtrans is effectively acting like a gmond instance itself. This also means that jmxtrans doesn't have to run on the same machine as the gmond instance.  Note that for proper host spoofing Ganglia compares both the name of the host and the IP address associated with it.  The GangliaWriter will attempt to resolve the address of the host (or alias) for you.  If this does not work you can correctly spoof Ganglia by providing an ```alias``` value in the form that it expects, specifically IP:hostname (e.g. "10.10.10.3:myhost").

If there is demand, future versions of this plugin can support writing to multicast just like the other gmond instances do. That said, looking at Hadoop's support of Ganglia, it is just writing straight UDP, so I suspect this isn't going to be a high demand feature.

## Example Configuration

```
{
  "servers" : [ {
    "host" : "w2",
    "alias" : "10.0.3.16:w2.fullname.com",
    "port" : "1099",
    "queries" : [ {
      "obj" : "java.lang:type=GarbageCollector,name=ConcurrentMarkSweep",
      "outputWriters" : [ {
        "@class" : "com.googlecode.jmxtrans.model.output.GangliaWriter",
        "groupName" : "memory",
        "host" : "10.0.3.16",
        "port" : 8649,
        "slope" : "both",
        "units" : "bytes",
        "tmax" : 60,
        "dmax" : 900,
        "sendMetadata": 5
      } ]
    } ]
  } ]
}
```

GangliaWriter settings attributes:

  * *host* - The hostname for the machine running gmond.
  * *alias* (*optional*) - The spoofed hostname (see documentation above).
  * *port* - The port that gmond is accepting UDP requests on.
  * *groupName* - How you want your graphs grouped in the Ganglia interface.
  * *slope* (*optional*) - The slope associated with the value(s) for this MBean: ZERO, POSITIVE, NEGATIVE, BOTH.  Defaults to BOTH if not specified.  This will apply to all values collected by this query.
  * *units* (*optional*) - A String describing the expected units for this metric for display purposes.  Defaults to none.
  * *tmax* (*optional*) - The maximum expected time (in seconds) between readings of this metric.  Defaults to 60.
  * *dmax* (*optional*) - The maximum time (in seconds) that the value should be retained by the gmond instance in the absence of updates.  Defaults to 0, which means forever.
  * *sendMetadata* (*optional*) - Controls the frequency with which the metadata packets are sent. 1 would be send always, 2 send half the time, etc. Care should be taken to not let the metric get stale. Defaults to 30.