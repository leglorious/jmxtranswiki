## Time Based Rolling Key Out Writer

This provides an extension over the KeyOutWriter by supporting rolling the logs into a new file by size and time properties.  This writer also utilises Logback as opposed to Log4j.  Rolling over to a new file does not involve renaming an existing file, which is useful for environments where the monitoring cannot handle file renaming.
