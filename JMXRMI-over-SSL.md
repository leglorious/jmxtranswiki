Starting with release 267, JMXTrans can communicate with application over SSL.
This is particularly useful when using passwords to authenticate JMXRMI connections.

# On monitored application side

The monitored application must have the following JVM flags:

```
-Dcom.sun.management.jmxremote=true
-Dcom.sun.management.jmxremote.port=12345
-Dcom.sun.management.jmxremote.rmi.port=12345
-Dcom.sun.management.jmxremote.ssl=true
-Dcom.sun.management.jmxremote.registry.ssl=true
-Djavax.net.ssl.keyStore=path/to/keystore.jks
-Djavax.net.ssl.keyStorePassword=KeystorePassword
-Djavax.net.ssl.trustStore=path/to/truststore.jks
-Djavax.net.ssl.trustStorePassword=TruststorePassword
```

The `keystore.jks` contains the SSL certificate used to encrypt the communication. This certificate DN (or its SubjectAltNames) must match the host name as later used by JMXTrans.
The `truststore.jks` contains the autority chain used to build the above key.
Both JKS files should be built with Java's [keytool](http://docs.oracle.com/javase/8/docs/technotes/tools/unix/keytool.html).
It is possible to use self-signed certificates.

# On JMXTrans monitoring application side

JMXTrans must be running with truststore contained the same autority chain as above:

```
-Djavax.net.ssl.trustStore=path/to/truststore.jks
-Djavax.net.ssl.trustStorePassword=TruststorePassword
```
These flags can be set with environment variables:

```shell
export SSL_TRUSTSTORE="path/to/truststore.jks"
export SSL_TRUSTSTORE_PASSWORD="TruststorePassword"
```

Then in JSON configuration file, the SSL must be enabled

```json
{
  "servers":[
    {
      "port":"12345",
      "host":"monitoredhost",
      "ssl":true,
      "queries": [ ... ]
    }
  ]
}
```
