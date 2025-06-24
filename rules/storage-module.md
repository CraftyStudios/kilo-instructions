# Storage Module
CraftyCore provides a stoarge module that handles persistance for any data object

## When To Use
Anything that is being stored but does not need to be configured must use this module to persist

## How To Use
First, create your data class. This example will be for a game match:

```java
import dev.crafty.core.storage.StorageProvider;
import lombok.Data;

@Data
public class GameMatch {
    public static StorageProvider<GameMatch, String> storage;

    private final String id;
    private final String name;
    private final String map;
}
```

Next, register it in the main class:

```java
@Override
protected void onCraftyEnable() {
    GameMatch.storage = ProviderManager.getInstance().getProvider(GameMatch.class, "game_match"); // "game_match" is optional, this is if you want to specify a custom identifier
}
```

You can interact with this storage provider like so:
```java
GameMatch.storage.get("id")
        .thenAccept(match -> {
            if (match.isPresent()) {
                // Do something with the match
            } else {
                // Handle the case where the match is not found
            }
        })
        .exceptionally(ex -> {
            // Handle any exceptions that occurred during retrieval
            return null;
        });

GameMatch.storage.delete("id")

GameMatch.storage.exists("id").thenAccept(exists -> {});

GameMatch.storage.save("id", new GameMatch(...));
```

## Player Data
Storing player data like levels is done seperately from generally persisted objects. You can access this like so:

```java
PlayerDataProvider<Integer> playerDataProvider = ProviderManager.getInstance().forPlayer(player, Integer.class);
```

For example, if you want to store the player's balance, you can access it like so:
```java
CompletableFuture<Optional<Integer>> balanceFuture = playerDataProvider.get("balance");
balanceFuture.thenAccept(optionalBalance -> {
    optionalBalance.ifPresent(balance -> {
        // Do something with the balance
    });
});

playerDataProvider.save("balance", 1000);

playerDataProvider.exists("balance").thenAccept(exists -> {});

playerDataProvider.delete("balance");
```