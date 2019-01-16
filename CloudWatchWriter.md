#CloudWatchWriter

## CloudWatchWriter

[Amazon CloudWatch](https://aws.amazon.com/cloudwatch/) is a monitoring service for AWS cloud resources and the applications you run on AWS. You can use Amazon CloudWatch to collect and track metrics, collect and monitor log files, and set alarms.

jmxtrans has a writer that connects to CloudWatch using the AWS Java SDK and writes data directly to it using HTTP. The CloudWatchWriter is only usable in AWS, because it uses EC2 Metadata for credentials and the region information.  

## Example Configuration

```
{
   "servers":[
      {
         "alias":"testhost",
         "host":"localhost",
         "port":"1105",
         "queries":[
            {
               "obj":"java.lang:type=OperatingSystem",
               "attr":[
                  "SystemLoadAverage"
               ],
               "resultAlias":"jvm.cpu",
               "outputWriters":[
                  {
                     "@class":"com.googlecode.jmxtrans.model.output.CloudWatchWriter",
                     "settings":{
                        "namespace":"jmx",
                        "dimensions":[
                           {
                              "name":"InstanceId",
                              "value":"$InstanceId"
                           },{
                              "name":"SomeKey",
                              "value":"SomeValue"
                           }]
                     }
                  }
               ]
            }
         ]
      }
   ]
}
```

CloudWatchWriter settings attributes:

  * *host* - The hostname for the machine running the process to monitor.
  * *alias* (*optional*) - The spoofed hostname (see documentation above).
  * *port* - The port of the process to monitor.
  * *namespace* - The CloudWatch namespace (CloudWatch namespaces are containers for metrics. Metrics in different namespaces are isolated from each other, so that metrics from different applications are not mistakenly aggregated into the same statistics.)