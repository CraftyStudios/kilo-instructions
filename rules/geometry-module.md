# Geometry Module
CraftyCore contains a geometry module that allows you to interact with 2d and 3d points.

## When To Use
When serializing any things like regions to config or interacting with them

## How To Use
```java
@Data
public class Region {
    private final Polygon region;

    public boolean isPlayerIn(Player player) {
        return region.contains(player.getLocation());
    }
}
```

The polygon interface has the following methods:

```java
    boolean contains(Location location); // bukkit location

    boolean contains(Point3d point);

    int getVertexCount();
```

There is one type of polygon called the polygon3d. You can create it like so:

```java
Polygon polygon3d = new Polygon3d(
    List.of(new Point2d(0,2), new Point2d(2, 2)), // list of point 2ds for vertices
    0, // min y
    10 // max y
)
```

These have serializers built in:
Point2d Serializer serializes like so:
```yml
(path):
    x: number
    z: number
```

Polygon3d:
```yml
(path):
    minY: number
    maxY: number
    vertices:
        1:
          x: number
          z: number
      ...
```