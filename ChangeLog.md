#ChangeLog

This seems kind of silly to maintain. Please just look at the issues and commits lists.

## v?

  * Issue #15. KeyOutWriter was using a static log instance so it would combine logs together. Duh.
  * Issue #14. Fixed file descriptor leakage in RRDToolWriter.
  * r261. Added better support for weblogic.

## v250

  * log level is now configurable on /etc/default/jmxtrans or /etc/sysconfig/jmxtrans in LOG_LEVEL variable. Default value stay debug. If you're using JMXTrans zip, you should now provide it with -Djmxtrans.log.level=debug on Java command line. 

## v239

  * jmxtrans multi-instances support by providing PIDFILE vars to jmxtrans.sh and JMXTRANS_OPTS passed to Java command line
  * Ant like variables into json files, so you could avoid hardcoding some values, like graphite servers.
  * RPM package (built on Fedora)

## v233

  * Support for Ganglia added.

## v168

  * Support for caching the JMX side of the connection. 

## v155

  * Debian installer
  * Documentation on the website for starting jmxtrans-all.jar has also been updated
  * .sh script for starting things up