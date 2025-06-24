# Logging Module
CraftyCore provides a logging module for the plugin

## When To Use
When logging any info, warning, error, debugging, or tracing

## How to Access
You can access the logger through the static reference to the main class:

```java
Main.INSTANCE.logger.info("test", args);
Main.INSTANCE.logger.warn("test", args);
Main.INSTANCE.logger.error("test", args);
Main.INSTANCE.logger.debug("test", args);
Main.INSTANCE.logger.trace("test", args);
```

You can use %s to replace the args. The args are of type Object... so you can add unlimited.