# Scheduler Module
CraftyCore allows you to schedule actions to be run at set intervals

## How To Use
First create a new class in `actions` package named (action name)Action, such as AnnounceAction

Create the class like so:

```java
package dev.crafty.demo.actions;

import dev.crafty.core.scheduler.ScheduledAction;
import org.bukkit.Bukkit;

import java.time.Duration;

public class AnnounceAction implements ScheduledAction {
    @Override
    public String id() {
        return "announce_action"; // set the id to something that describes the action
    }

    @Override
    public Duration duration() { // the interval of when to execute; this is every 10 minutes
        return Duration.ofMinutes(10);
    }

    @Override
    public void run() {
        // what to run when called
        Bukkit.getOnlinePlayers().forEach(player -> player.sendMessage("Hello World!"));
    }
}

```

Next register the action in the main class:

```java
@Override
protected List<ScheduledAction> getScheduledActions() {
    return List.of(
        new AnnounceAction()
    );
}
```