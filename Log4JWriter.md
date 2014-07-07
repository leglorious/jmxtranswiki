#Log4JWriter

## Log4JWriter

This writer logs jmxtrans metrics using Log4j configuration.

It provides Log4j mapped diagnostic contexts (MDC) for the following variables:
* server
* metric
* value
* resultAlias
* attributeName
* key
* Epoch

This writer has been initially created to send jmxtrans metrics to logstash.

## Configuration

* Add Log4j socket appender by adding _log4j_socket_appender.xml_ in jmxtrans directory
```
<?xml version="1.0" encoding="UTF-8" ?>
<!DOCTYPE log4j:configuration SYSTEM "log4j.dtd">
<log4j:configuration xmlns:log4j="http://jakarta.apache.org/log4j/">
    <appender name="STDOUT" class="org.apache.log4j.ConsoleAppender">
        <layout class="org.apache.log4j.PatternLayout">
            <param name="ConversionPattern" value="[%d{dd MMM yyyy HH:mm:ss}] %-6r %-5p (%C:%L) - %m\n"/>
        </layout>
    </appender>
    <appender name="asyncAppender" class="org.apache.log4j.AsyncAppender">
        <param name="BufferSize" value="1024" />
        <param name="Blocking" value="true" />
        <!-- needed to get the class:linenumber output
             @see http://marc.info/?l=log4j-user&m=105591790712092&w=2 -->
        <param name="LocationInfo" value="true"/>
        <appender-ref ref="fileAppender" />
    </appender>
    <appender name="fileAppender" class="org.apache.log4j.DailyRollingFileAppender">
        <param name="File" value="${jmxtrans.log.dir}/jmxtrans.log"/>
        <param name="Append" value="true"/>
        <param name="DatePattern" value="'.'yyyyMMdd"/>
        <layout class="org.apache.log4j.PatternLayout">
            <param name="ConversionPattern" value="[%d{dd MMM yyyy HH:mm:ss}] [%t] %-6r %-5p (%C:%L) - %m\n"/>
        </layout>
    </appender>
     
    <appender name="Log4JWriterAppender" class="org.apache.log4j.DailyRollingFileAppender">
        <param name="File" value="${jmxtrans.log.dir}/log4jwriter.log"/>
        <param name="Append" value="true"/>
        <param name="DatePattern" value="'.'yyyyMMdd"/>
        <layout class="org.apache.log4j.PatternLayout">
            <param name="ConversionPattern" value="[%d{dd MMM yyyy HH:mm:ss}] server:%X{server} metric:%X{metric} value:%X{value} %m\n"/>
        </layout>
    </appender>    
     
    <appender name="Log4JWriterSocketAppender" class="org.apache.log4j.net.SocketAppender">
        <param name="RemoteHost" value="${jmxtrans.log4j.socket.host}"/>
        <param name="Port" value="${jmxtrans.log4j.socket.port}"/>
        <param name="ReconnectionDelay" value="${jmxtrans.log4j.socket.reconnectionDelay}"/>
    </appender>
 
    <logger name="com.googlecode.jmxtrans">
        <level value="${jmxtrans.log.level}"/>
    </logger>
    <logger name="org.quartz">
        <level value="warn"/>
    </logger>
    <logger name="Log4JWriter">
        <level value="INFO"/>
        <appender-ref ref="Log4JWriterAppender"/>
        <appender-ref ref="Log4JWriterSocketAppender"/>
    </logger>
     
    <root>
        <level value="error"/>
        <appender-ref ref="asyncAppender"/>
    </root>
</log4j:configuration>
```
* Configure Log4j socket appender to connect to logstash by modifying _jmxtrans.sh_
```
LOG4J_CONFIGURATION_FILE=${LOG4J_CONFIGURATION_FILE:-"file:log4j_socket_appender.xml"}
LOG4J_SOCKET_HOST=${LOG4J_SOCKET_HOST:-"localhost"}
LOG4J_SOCKET_PORT=${LOG4J_SOCKET_PORT:-"4562"}
LOG4J_SOCKET_RECONNECTION_DELAY=${LOG4J_SOCKET_RECONNECTION_DELAY:-"10000"}
JMXTRANS_OPTS="$JMXTRANS_OPTS -Djmxtrans.log.level=${LOG_LEVEL} -Dlog4j.configuration=${LOG4J_CONFIGURATION_FILE} -Djmxtrans.log4j.socket.host=${LOG4J_SOCKET_HOST} -Djmxtrans.log4j.socket.port=${LOG4J_SOCKET_PORT} -Djmxtrans.log4j.socket.reconnectionDelay=${LOG4J_SOCKET_RECONNECTION_DELAY}"
```