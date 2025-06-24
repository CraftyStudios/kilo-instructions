# Command Module
CraftyCore provides a command module to use

## When To Use
When creating any command

## How to use
To create a command, create a new class in the `commands` package called (command)Command. If you were to create a command /test, it would be TestCommand.

The class should look like the following:

```java
package dev.crafty.demo.commands;

import dev.crafty.core.acf.annotation.*;
import dev.crafty.core.bukkit.EnhancedBaseCommand;
import org.bukkit.entity.Player;

@CommandAlias("test")
public class TestCommand extends EnhancedBaseCommand {
    @Default
    public void execute(Player player) {

    }
}
```

The code in the execute method is run when the player executes /test.

You can add more aliases for the command by adding them in the @CommandAlias:

```java
@CommandAlias("test|t")
```

Now the user can run /test and /t.

You have access to the following annotations for the commands as well:

```java
@Description // applied to the command method, @Description("text") defined description

@Subcommand("hi") // add to a method to define a sub command

@CommandPermission("test.admin") // required permission to execute command / subcommand
```

### Command Completions
You may need to add tab completions for a command. You can add them like so:

In the main class:
```
@Override
protected void onCraftyEnable() {
    commandManager.getCommandCompletions().registerCompletion("kits", (c) -> Kit.storage.getAll().stream().map(Kit::getName).toList()); // must return string list
}
```

Then you can use it like so:

```java
@Default
@CommandCompletion("@kits")
public void onExecute(Player player, String kitName) {

}
```

For more completions, you can do it like this:

```java
@Default
@CommandCompletion("@kits @somethingElse")
public void onExecute(Player player, String kitName, String someOtherArg) {

}
```

The system comes with default command completions:

@nothing - returns nothing

@range - takes a completion, pass range like so @range:0-20 or @range:20 (assumed 0 start)

@mobs - list of EntityType enum values (Bukkit)

@chatcolors - lists of all the chat colors supported by your platform (Bukkit, Bungee)

@worlds - lists all world names (Bukkit)

@players - lists all online players that you can see

### Detailed Arguments
To access arguments normally, you can do String[] args as a parameter and gain access to the arguments. However, you can also specify OfflinePlayer and it will automatically be resolved. To mark an argument as optional, just put the @Optional annotation in front of the parameter.

It is best practice to create a command context to resolve the input to a java object if applicable. In the kits example, we said String kitName, but now we need to parse it into a kit. Instead, we can register a command context to translate it into a Kit automatically like so:

In our main class:
```java
@Override
protected void onCraftyEnable() {
    commandManager.getCommandContexts().registerContext(Kit.class, (c) -> Kit.storage.get(c.popFirstArg()).get());
}
```

Now we can update our example to be the following:

```java
@Default
@CommandCompletion("@kits")
public void onExecute(Player player, Kit kit) {

}
```

And the kit is automatically resolved.