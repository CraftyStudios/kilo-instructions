# Bridges Module
CraftyCore provides bridges for common plugin apis. Below is how to get them and which ones are provided. If you need to access a plugin api and the bridge does not exist, please stop and let the user know you need them to create one.

## How To Access
To access a bridge, you can do the following:
```java
<target bridge> bridge = BridgeManager.getBridge(<target bridge>.class);
if (bridge != null) { // it is VERY important to check if this is null... if it is null that means there are no plugins on the server that can support this bridge.
    // ...
}
```

BridgeManager's package is dev.crafty.core.bridge

## Bridges
Here are the built in bridges and how to use them: If you need a method added to a bridge, stop and ask the user to add the method to the specified bridge.

### Economy
Class: EconomyBridge
Package: dev.crafty.core.bridge.economy

Methods:
```java
double getBalance(UUID player)
void withdraw(UUID player, double amount)
void deposit(UUID player, double amount)
```

### Mobs
Class: MobBridge
Package: dev.crafty.core.bridge.mobs

Methods:
```java
public Optional<Entity> spawnMob(String mobName, Point3d location, String world, int level)
```

### Placeholders
You must use this bridge to register any placeholder

Class: PlaceholderBridge
Package: dev.crafty.core.bridge.placeholders

Methods:
```java
void registerPlaceholder(String identifier, BiFunction<Player, String, String> replacer)
String replacePlaceholders(String input)
String replacePlaceholders(String input, Player player)
```