# Listener Module
When interacting with bukkit, you may need to create listeners for bukkit events. The process is the same for doing so, except you will implement `SelfRegisteringListener` instead of `Listener`. Then you will not need to register the listener in the main class. Example:

```java
import dev.crafty.core.bukkit.SelfRegisteringListener;
import org.bukkit.event.EventHandler;

public class MyListener implements SelfRegisteringListener {

    @EventHandler
    public void onEvent()...
}
```