# Anti Spam Module
CraftyCore has an anti spam module. Use it if the message could spam the user to keep chat clean

## How To Use
```java
import dev.crafty.core.player.AntiSpam;

AntiSpam.send()
        .to(player)
        .message(Main.INSTANCE.i18n....)
        .key("spam_key") // set this to be the key for the message
        .cooldown(Duration.ofSeconds(5))
        .send();
```