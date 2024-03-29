# How to write queries

## Generating Queries

Figuring out how to write queries is probably the most difficult part of using jmxtrans, but once you get the hang of it, it really isn't that difficult. Below is an image of JConsole looking at the metrics provided by Ehcache which is a commonly used object caching library for Java.

![pic](https://raw.githubusercontent.com/jmxtrans/jmxtrans/master/src/site/images/ehcache-example.png)

As you can see, I've marked in red a few areas of note. That is because we are going to create a query for 3 attributes ```"CacheHits", "CacheMisses", "ObjectCount"``` from all of the available caches (there is only one shown ```ClientCache```).

The "obj" field can be discovered by clicking on the MBean itself ( ```ClientCache``` in this case) and selecting the ```ObjectName``` field. As in the example below, you may want to add ```,*``` or ```,name=*```  in order to catch child MBeans.

Here is some example json that queries one server, w2, on port 1099:

```
{
  "servers" : [ {
    "host" : "w2",
    "port" : "1099",
    "queries" : [ {
      "obj" : "net.sf.ehcache:type=CacheStatistics,*",
      "attr" : [ "CacheHits", "CacheMisses", "ObjectCount" ],
      "outputWriters" : [ {
        "@class" : "com.googlecode.jmxtrans.model.output.KeyOutWriter",
        "settings" : {
          "outputFile" : "/tmp/keyout2.txt",
          "maxLogFileSize" : "10MB",
          "maxLogBackupFiles" : 200,
          "debug" : true,
          "typeNames" : ["name"]
        }
      } ]
    } ]
  } ]
}
```

You can see the use of a wildcard ```*``` as part of the ```obj``` configuration. The ```attr``` configuration shows the three attributes that we care about. Finally, there is a rather cryptic *but very important* piece of configuration called ```"typeNames": ["name"]```. That is necessary because we are using a wildcard and the results of the query have ```name=ClientCache``` in them and we want to filter that as part of the outputWriter so that we can uniquely identify the results. If there was another cache called ```OtherCache```, we would see ```name=OtherCache```.

Thus, the final output writting to the keyout2.txt file will look like this:

```
w2_1099.net_sf_ehcache_management_CacheStatistics.ClientCache.CacheHits	487239	1308855201
w2_1099.net_sf_ehcache_management_CacheStatistics.ClientCache.CacheMisses	30164	1308855201
w2_1099.net_sf_ehcache_management_CacheStatistics.ClientCache.ObjectCount	4270	1308855201
```

If you don't like the classname garbage, you can alias that to something else by adding the ```resultAlias``` configuration to the query like this: 

```
"obj" : "net.sf.ehcache:type=CacheStatistics,*",
"resultAlias": "ehcache"
```

Thus:

```
w2_1099.ehcache.ClientCache.CacheHits	487239	1308855201
w2_1099.ehcache.ClientCache.CacheMisses	30164	1308855201
w2_1099.ehcache.ClientCache.ObjectCount	4270	1308855201
```

If you want a different hostname to show up, use the ```alias``` configuration like this:

```
  "servers" : [ {
    "host" : "w2",
    "alias" : "w2.foo.com",
    "port" : "1099",
```

## Generating Queries using VisualVM

Some people may find that VisualVM makes it easier to find the required MBean information.
 
In VisualVM:

1. Connect to the Java instance using VisualVM. If it's a remote JVM, you'll need to configure a a "remote host" first, then a JMX Connection.
2. Open the JMX connection by double-clicking the node in the left-hand pane
3. Choose the "MBeans" tab in the right tab.
4. Find the MBEAN you want to monitor (in the center-pane tree)
5. Click the "Metadata" tab in the right-most pane.
6. You should see the "ObjectName" under "MBeanInfo". You should be able to copy/paste this into JMXTrans, and choose "attributes" from the "Attributes" tab.

## Alternative query definition using YAML
Note that for setups with lots of reused queries, you might profit from using the YAML configuration format described in [[YAMLConfig]]. It allows to specify a query once and apply it to many hosts without repetition.