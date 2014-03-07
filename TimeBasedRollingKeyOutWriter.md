# TimeBasedRollingKeyOutWriter

## Time Based Rolling Key Out Writer

This provides an extension over the KeyOutWriter by supporting rolling the logs into a new file by size and time properties.  This writer writes the logs to a file which is tab delimited by default.

The writer also utilises Logback as opposed to Log4j.  Rolling over to a new file does not involve renaming an existing file, which is useful for environments where the monitoring cannot handle file renaming.

The writer shares the same settings as the KeyOutWriter.

However, the outputFile name setting defines when the rollover based on date/time should take place.  For instance the setting below:

````
"outputFile": "threading.log%d{yyyy-MM-dd}.%i",
````

This setting indicates that the file should automatically be rolled over each day.  It also indicates that if the maxLogFileSize is reached a new file should be used with the %i value being incremented.  In this manner the files for 7th March 2014, if the file size had got too big once and had to roll to a new file, would be:

````
threading.log2014-03-07.0
threading.log2014-03-07.1
````

Please note that the final number is not related to the hour the log file was created and is simply a number incremented from zero.

For more information on the date and time setting consult the Logback documentation here: http://logback.qos.ch/manual/appenders.html under the section Size and time based archiving and the section TimeBasedRollingPolicy.

Here is an example configuration which is very similar to that used by KeyOutWriter but with the datetime rollover specified in the outputFile setting.

```
{
  "servers" : [ {
    "port" : "1099",
    "host" : "w2",
    "queries" : [ {
      "outputWriters" : [ {
        "@class" : "com.googlecode.jmxtrans.model.output.TimeBasedRollingKeyOutWriter",
        "settings" : {
          "outputFile" : "/tmp/keyout2.txt%d{yyyy-MM-dd}.%i",
          "maxLogFileSize" : "10MB",
          "maxLogBackupFiles" : 200,
          "delimiter" : "\t" 
          "debug" : true,
          "typeNames" : ["name"]
        }
      } ],
      "obj" : "net.sf.ehcache:type=CacheStatistics,*",
      "attr" : [ "CacheHits", "CacheMisses", "ObjectCount" ]
    }
     ]
  } ]
}
```