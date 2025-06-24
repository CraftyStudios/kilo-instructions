# Config Module
Crafty Core contains a module for defining configs.

## When To Use
Use this module when creating and interacting with any config. You are to create new config values for items that are deemed important for the user to be able to configure. Do not create unneccesary and unused config values.

### SectionWrapper
The SectionWrapper class is a wrapper for the ConfigurationSection bukkit object. This wraps all of those methods with Optionals. Please keep that in mind when interacting with it.

### Creating A Config
To create a config, create a new file named (config name)Config. If you are referring to the main config, create a file called Config. You do not need to create a class for Lang config, that is already handled. The class should look like the following:

```java
package dev.crafty.demo;

import dev.crafty.core.config.ConfigurationUtils;
import dev.crafty.core.config.annotation.ConfigValue;
import dev.crafty.core.config.annotation.ConfigurationFile;
import lombok.Getter;

@ConfigurationFile(file = "config.yml")
@Getter
public class Config {
    @ConfigValue(key = "test.test")
    private String test;

    // you must include a no-args constructor
    public TestConfig() {}

    public TestConfig(Main plugin) {
        ConfigurationUtils.registerAndLoad(this, plugin);
    }
}
```

Note: This example has the value test.test. In your config, you should **always** create a default value corresponding to the key. Make sure the file that you referenced in the file = "" exists so you can create the value. If it doesn't exist, create it in the resources folder.

And in your main class, include this in the onCraftyEnable():
```java
// ...
public Config config; // create a public field for the new config

@Override
protected void onCraftyEnable() {
    // ...
    this.config = new Config(this); // you must pass the plugin instance to the config
}
```

Now to access the config from any file, you can do this:
```java
public void doSomething() {
    String test = Main.INSTANCE.config.test;
}
```

## Cached Configuration Objects
Cached Configuration Objects are objects that are dynamically created by the user through a file. These should be used in places where the user can create more items of the same type, like kits, ranks, etc.. These are cached by CraftyCore automatically. Below is an example of how to create one.

First, create your class. This example will be for kits.
```java
package dev.crafty.demo;

import dev.crafty.core.config.CachedConfigObject;
import lombok.Data;

@Data
public class Kit {
    public static CachedConfigObject<String, Kit> storage;

    private final String name;
    private final String description;
    private final int requiredLevel;
}
```

Next create a serializer for the object. The next section will detail more into how to create a serializer, so this example is minimal.

```java
package dev.crafty.demo.serializer;

import dev.crafty.core.config.SectionWrapper;
import dev.crafty.core.config.serializer.ConfigSerializer;
import dev.crafty.core.util.Optionals;
import dev.crafty.demo.Kit;

import java.util.Optional;
import java.util.concurrent.atomic.AtomicReference;

public class KitSerializer implements ConfigSerializer<Kit> {
    @Override
    public void serialize(SerializationArgs<Kit> args) {
        // ...
        // see next section for more
    }

    @Override
    public Optional<Kit> deserialize(SectionWrapper section, String path) {
        // ...
        return Optional.of(kit); // details ommitted for brevity; see next section for more
    }

    @Override
    public Class<Kit> getType() {
        return Kit.class;
    }
}
```

Next edit the main class to reflect the following
```java
@Override
protected void onCraftyEnable() {
    ConfigurationUtils.registerSerializer(new KitSerializer()); // register the new serializer we created

    Kit.storage = new CachedConfigObject.Builder<String, Kit>() // set the static storage object in the Kit class
            .configFile(new File(this.getDataFolder(), "kits.yml")) // this does not have to be created yet
            .configSection("kits") // nor does this
            .keyToStringFunction((key) -> key)
            .keyFromStringFunction((key) -> key)
            .serializer(new KitSerializer()) // reference the new serializer
            .build();

@Override
protected void onConfigReloaded() {
    Kit.storage.loadAll(); // fetches new values
}
```

### Accessing Created Objects
Now we allow users to create kits through the kits.yml "kits" section. You should always create an example item when creating cached configuration objects. You will know how to create the config version after reading the serializer section. To access the created objects, you have a few methods. You can also interact with the data as detailed below:

```java
public void useStorage() {
    Optional<Kit> kit = Kit.storage.get("key"); (the parameter is of the key type)
    List<Kit> kits = Kit.storage.getAll();

    Kit.storage.remove("key"); // doesn't have to exist

    Kit newKit = new Kit(...);
    Kit.storage.set(newKit.getName(), kit); // keep the property of the key the same, doesn't always need to be the name.
}
```

## Config Serializers
This section details how to create and use config serializers, as well as the built-in ones

### When To Create
Create a config serializer when creating a cached config object or using it as a config property. For example:
```java
@ConfigValue(key = "test.kit")
private Kit kit; // you need to create a serializer for this
```

### Creating
To create a serializer, create a new package called `serializer` and add the serializer. The class name should be `object name`Serializer, like this: KitSerializer for kits.

The following will detail how to create a Kits serializer.

Create the class:

```
package dev.crafty.demo.serializer;

import dev.crafty.core.config.SectionWrapper;
import dev.crafty.core.config.serializer.ConfigSerializer;
import dev.crafty.core.util.Optionals;
import dev.crafty.demo.Kit;

import java.util.Optional;
import java.util.concurrent.atomic.AtomicReference;

public class KitSerializer implements ConfigSerializer<Kit> {
    @Override
    public void serialize(SerializationArgs<Kit> args) {

    }

    @Override
    public Optional<Kit> deserialize(SectionWrapper section, String path) {

    }

    @Override
    public Class<Kit> getType() {
        return Kit.class; // reference the class
    }
}
```

First we will populate the serialize method. The `SerializationArgs` has the following properties:

`T value, SectionWrapper section, YamlConfigurationWrapper parent, String path, File configFile, boolean save`. You will only need to access a few to serialize.

Here is an example serialize method:
```java
private static final String NAME_KEY = "name";
private static final String DESCRIPTION_KEY = "description";
private static final String REQUIRED_LEVEL_KEY = "required-level";

@Override
public void serialize(SerializationArgs<Kit> args) {
    // access the properties you need for clarity
    var section = args.section();
    var path = args.path();
    var kit = args.value();
    var id = path + kit.getName(); // define the id

    // setting the properties
    section.set(id + "." + NAME_KEY, kit.getName());
    section.set(id + "." + DESCRIPTION_KEY, kit.getDescription());
    section.set(id + "." + REQUIRED_LEVEL_KEY, kit.getRequiredLevel());

    save(args); // you must include this
}
```

To deserialize, you will just access the values and create a new object. Here is an example:

```java
@Override
public Optional<Kit> deserialize(SectionWrapper section, String path) {
    var id = path + section.getString(path + ".name");

    if (!section.contains(id + ".name")) {
        return Optional.empty();
    }

    // you are working with the SectionWrapper; thus accessing things returns an optional
        Optional<String> optionalName = section.getString(id + "." + NAME_KEY);
        Optional<String> optionalDescription = section.getString(id + "." + DESCRIPTION_KEY);
        Optional<Integer> optionalRequiredLevel = section.getInt(id + "." + REQUIRED_LEVEL_KEY);

    // utility provided by CraftyCore
    if (!Optionals.areAllPresent(optionalName, optionalDescription, optionalRequiredLevel)) {
        return Optional.empty();
    }

    AtomicReference<Kit> kit = new AtomicReference<>();

    Optionals.ifAllPresent(optionalName, optionalDescription, optionalRequiredLevel, (name, description, requiredLevel) -> {
        kit.set(new Kit(name, description, requiredLevel));
    });

    return Optional.of(kit.get());
}
```

Notice the key fields. Create those to maintain structure and clarity in the code.

Note: If you have more than 3 properties, you will not be able to use the \#ifAllPresent() method. Please instead check if they are all present and unwrap them to access the properties.

Next you need to register the serializer in the main class:

```java
@Override
protected void onCraftyEnable() {
    ConfigurationUtils.registerSerializer(new KitSerializer());
}
```

Below is an example yml entry that is valid for the serializer. Here is the kit we are serializing:
Kit
name = "test"
description = "test desc"
requiredLevel = 2

```yml
test:
  name: "test"
  description: "test desc"
  required-level: 2
```

### Accessing Serializers
To access a serializer, you can just create a new instance and serialize/deserialize.

```java
Kit kit = new KitSerializer().deserialize(...);
new KitSerializer().serialize(...);
```

### Built-ins
CraftyCore contains built in serializers. Below are the list of them and their packages:

ItemStackSerializer | dev.crafty.core.config.serializer.builtin
LocationSerializer | dev.crafty.core.config.serializer.builtin
ItemActionSerializer | dev.crafty.core.items.serializers
Point2dSerializer | dev.crafty.core.geometry.serializers
Polygon3dSerializer | dev.crafty.core.geometry.serializers
ItemCraftingRecipeSerializer | dev.crafty.core.items.serializers

Please use these if needed instead of creating a new serializer