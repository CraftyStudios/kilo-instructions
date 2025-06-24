# i18n Crafty Core Module
Crafty Core contains an i18n module. The following will detail how and when to use this.

## When To Use
You will use the i18n module for **all** publicly facing messages to users in chat.

## How To Use
If the project does not contain a lang enum, you will create a new enum called `Lang`. This enum should look like the following:

```java
package dev.crafty.demo;

import dev.crafty.core.i18n.i18nEnum;

public enum Lang implements i18nEnum {
    ;

    private final String key;

    Lang(String key) {
        this.key = key;
    }

    @Override
    public String getTranslationKey() {
        return this.key;
    }
}
```

### Creating A Message
To create a message, first add an entry to the enum like so:
```java
DEMO_MESSAGE("demo.demo-message");
```

Next, add the corresponding entry to the lang.yml file:
```yml
demo:
  demo-message: "<gold>Hello, World!"
```

These messages are styled using the minimessage formatting.

### Sending To User
To send the message to the user, use the i18n query builder. Example:
```java
Main.INSTANCE.i18n.query(Lang.DEMO_MESSAGE) // use the instance that you created
                .player(player) // player object
                .send();
```

### Advanced Usage
You may need to add a placeholder to the message. These can be replaced when sending the message. Here is an example:
```java
PLACEHOLDER_MESESAGE("demo.placeholder-message")
```

```yml
demo:
  placeholder-message: "Your name is {name}"
```

```java
Main.INSTANCE.i18n.query(Lang.DEMO_MESSAGE) // use the instance that you created
                .player(player) // player object
                .placeholder("name", player.getName()) // replacing the placeholder
                .send();
```

You can add unlimited placeholders by chaining the \#placeholder method.