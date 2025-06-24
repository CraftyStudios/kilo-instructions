# Plugin Setup
Should the user ask you to create a new plugin, you will follow these instructions completely.

## Technologies
You will create a new maven java project. The default package name should be dev.crafty.(plugin name). You will ask the user what the plugin name should be. You will always add the lombok and craftycore dependency to the pom.

You will **always** ask the user for the most updated version of the crafty core dependency. Below are the dependencies that you must include.

```xml
<repository>
    <id>lightmc-repository-releases</id>
    <name>LightMC Repository</name>
    <url>https://repo.lightmc.uk/releases</url>
</repository>

<dependency>
    <groupId>io.papermc.paper</groupId>
    <artifactId>paper-api</artifactId>
    <version>1.21.5-R0.1-SNAPSHOT</version>
    <scope>provided</scope>
</dependency>

<dependency>
    <groupId>org.projectlombok</groupId>
    <artifactId>lombok</artifactId>
    <version>1.18.38</version>
    <scope>provided</scope>
</dependency>

<dependency>
  <groupId>dev.crafty</groupId>
  <artifactId>craftycore</artifactId>
  <version>**</version>
  <scope>provided</scope>
</dependency>
```

You will create the plugin.yml in the resources folder. Here is what it should look like:

```yml
name: (plugin name)
version: '1.0-SNAPSHOT'
main: dev.crafty.(plugin name).Main
api-version: '1.21'
depend:
  - "CraftyCore"
```

Your main class will extend CraftyPlugin. Here is the baseline class:


```java
package dev.crafty.demo;

import dev.crafty.core.plugin.CraftyPlugin;

public final class Main extends CraftyPlugin {
    public static TestCraftyCore INSTANCE; // you will use this to access the instance of the plugin when needed

    @Override
    protected void onCraftyEnable() {
        INSTANCE = this;
    }

    @Override
    protected void onCraftyDisable() {

    }

    @Override
    protected void onConfigReloaded() {

    }

    @Override
    protected String minimumCoreVersion() {
        return "**"; // set this to the crafty core version in pom.xml
    }

    @Override
    protected boolean hologramSupport() {
        return false; // if the user's plugin requires holograms, enable this
    }

    @Override
    protected String getPackage() {
        return "dev.crafty.demo"; // the same package as defined at the top of the file
    }
}
```

You will also always create a blank config.yml and lang.yml file in the resources folder.