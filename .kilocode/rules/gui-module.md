# Gui Module
This module allows you to create publically facing guis for the users.

## How to Use
First, create a new class in the `gui` package. Name it (gui name)Gui. For example a gui that is a demo gui would be named DemoGui.

Create the class like so:
```java
package dev.crafty.demo;

import dev.crafty.core.gui.Menu;
import org.bukkit.entity.Player;

public class DemoGui extends Menu {
    public DemoGui(Player player) {
        super("demo-menu", player, Main.INSTANCE);
    }

    @Override
    protected void setup() {

    }
}
```

Now we can add actions and other things to the items in the setup method. In this example, we will add a home gui

```java
record Home(String name, int x) {} // this is for the sake of demonstration... don't create a record in the gui class for the mapped items

@Override
protected void setup() {
    // registering an action with the id send_message
    registerAction("send_message", (e) -> e.getWhoClicked().sendMessage("hi"));

    // supplying an item
    registerItemSupplier("custom_item", () -> ItemStack.of(Material.CHAIN));

    List<Home> homes = List.of(new Home("home1", 1), new Home("home2", 2)); // normally you would fetch it from the storage system

    // registering a mapper
    registerMappedItemConfig(
            "get_homes", // mapping id
            (home) -> Map.of(), // metadata (more later)
            (home) -> Map.of( // placeholders for the item
                    "{home-name}", home.name,
                    "{home-x}", String.valueOf(home.x)
            ),
            homes // list of the items (defines T)
    );
}
```

Whenever you create a gui, you must create the respective yml file in the `menus` folder in `resources`. For this gui, the id is demo-menu, so you would create a yml file `demo-menu.yml`.

```yml
title: "<dark_purple>Inventory Browser" # Uses minimessage
fill: # configuring the fill item
  enabled: true # should '#' be fill or air
  item: # item config
    material: BLACK_STAINED_GLASS_PANE
    name: " "
    lore: []

layout: # defines the layout of the inventory. This inventory has 5 rows. Each row **must** have 9 items in it. the characters correspond to the ids defined in the items section. The '#' is reserved for fill items, so you cannot register an item with that id. The layout has a max of 6 rows and a minimum of 1
  - "#########"
  - "#a#b#c###"
  - "#########"
  - "###jjj###"
  - "#########"

items: # where the items are defined
  a: # the key corresponding to the one in the gui
    type: STATIC # STATIC means that the item is fully defined in config
    item: # the item
      material: CHEST
      name: "<gold>Storage"
      lore: ["<gray>Open your storage"]

  b:
    type: DYNAMIC # DYNAMIC means the item is fully provided by the provider
    provider: "custom_item" # the provider id

  c:
    type: CONDITIONAL # CONDITIONAL means that there will be a condition to see the item
    conditions:
      - "string.length(%player_name%) > 20" # condition (more on this later )
    true-item: #item to display if true
      item:
        material: DIAMOND
        name: "<aqua>VIP Access"
        lore: ["<gray>You are a VIP"]
    false-item: # item to display if false
      item:
        material: COAL
        name: "<gray>No Access"
        lore: ["<red>You need VIP"]
      actions: # action to run  on click
        - "send_message"

  j:
    type: MAPPED # MAPPED means that the slots with this key will be filled by the mapper
    mapper:
      source: "get_homes" # id of the mapper
      icon-template: #icon definition
        material: PLAYER_HEAD
        name: "<green>{item_name}" # used placeholders
        lore: ["<gray>Owned item"]
      actions:
        - "tp_to_house"
```

### Item Config
An item can be configured like so:
```yml
item:
  a:
    item:
      material: <minecraft material>
      name: <name with placehodlers styled by minimessage>
      lore: [<lore with placeholders and minimessage>]
      glow: true/false (whether to glow or not)
    actions: #actions defined in code to run on click
      - "my_action_id"
      - "another_action"
```

In the example, you will notice that the action "tp_to_hose" is not defined. This requires metadata which is detailed below:

### Mapping Metadata
Attaching metadata to an item is required when you want to know which of the mapped items a player clicked on. We can edit the house example to allow players to tp to a clicked house like so:

```java
private static final String HOME_NAME_KEY = "home-name"; // metadata key

record Home(String name, int x, Location loc) {}

@Override
protected void setup() {
    List<Home> homes = ...

    // registering a mapper
    registerMappedItemConfig(
            "get_homes", // mapping id
            (home) -> Map.of( // added metadata
                HOME_NAME_KEY, home.name
            ),
            (home) -> Map.of( // placeholders for the item
                    "{home-name}", home.name,
                    "{home-x}", String.valueOf(home.x)
            ),
            homes // list of the items (defines T)
    );

    registerAction(
            "tp_to_home",
            (e) -> {
                Player player = (Player) e.getWhoClicked();
                String homeName = getMetadataFromItem(HOME_NAME_KEY, e.getCurrentItem()); // accessing the metadata
                if (homeName != null) {
                    Home home = findHomeByName(homeName); // example to find by name
                    player.teleport(home.loc);
                }
            }
    );
}
```