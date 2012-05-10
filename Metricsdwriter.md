#Metricsdwriter

## Metricsd Writer

[Metricsd](https://github.com/mojodna/metricsd) is a metrics aggregator for Graphite that supports gauges, counters, histograms and meters.
Here is an example .json file that outputs HeapMemoryUsage and NonHeapMemoryUsage directly to Metricsd:

```
{
	"servers": [{
		"host": "a01",
		"alias": "a01",
		"port": "1003",					
		"queries": [{
			"outputWriters": [{
				"@class": "com.googlecode.jmxtrans.model.output.MetricsdWriter",
				"settings": {
					"host": "x02",
					"port": "1234",
					"rootPrefix": "jvm"
				}
			}],
		"obj" : "java.lang:type=Memory",
		"attr" : [ "HeapMemoryUsage", "NonHeapMemoryUsage" ],
		"resultAlias": "heapUsage",
		"metricsType": "gauge"
	   }]
}
```
