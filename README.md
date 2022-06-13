# Filter

## Learning Goals

- Use the filter method on streams
- Chain multiple filter methods

## What is Filtering?

Filtering is an operation that allows us to only obtain specific elements from a
stream. The `filter` method on streams can be used to only include desired
elements in a stream.

```java
import java.util.Arrays;

public class Example {
    public static void main(String[] args) {
        int[] nums = {1, 2, 3, 4, 5, 6, 7, 8, 9, 10};
        int[] evenNums = Arrays.stream(nums)
                            .filter(num -> num % 2 == 0)
                            .toArray();
        System.out.println(Arrays.toString(evenNums)); // [2, 4, 6, 8, 10]
    }
}
```

## Chaining Filter Methods

The `filter` method can be chained to filter out multiple elements. Let’s see an
example where we filter objects.

We’ll be using the `Dessert` class.

```java
class Dessert {
    private final String name;
    private final int price;
    private final Type type;

    public Dessert(String name, int price, Type type) {
        this.name = name;
        this.price = price;
        this.type = type;
    }

    public String getName() {
        return name;
    }

    public int getPrice() {
        return price;
    }

    public Type getType() {
        return type;
    }

    @Override
    public String toString() {
        return name;
    }

    public enum Type { CAKE, DONUT, CUSTARD }
}
```

Now that we have defined the `Dessert` class, let’s look at how we can filter a
list of `Dessert` objects.

```java
import java.util.Arrays;
import java.util.List;

public class Example {
    public static void main(String[] args) {
        List<Dessert> desserts = Arrays.asList(
                new Dessert("tiramisu", 8, Dessert.Type.CAKE),
                new Dessert("caramel dust donut", 3, Dessert.Type.DONUT),
                new Dessert("strawberry shortcake", 10, Dessert.Type.CAKE),
                new Dessert("classic glazed donut", 2, Dessert.Type.DONUT),
                new Dessert("simple custard", 4, Dessert.Type.CUSTARD),
                new Dessert("baked custard", 5, Dessert.Type.CUSTARD),
                new Dessert("pumpkin cake", 5, Dessert.Type.CAKE),
                new Dessert("caramel apple coffee cake", 6, Dessert.Type.CAKE));

        List<Dessert> filteredDesserts = desserts.stream()
                .filter(dessert -> dessert.getType().equals(Dessert.Type.DONUT))
                .filter(dessert -> dessert.getPrice() < 5)
                .toList();

        System.out.println(filteredDesserts);
    }
}
```

```plaintext
[caramel dust donut, classic glazed donut]
```

Note that we could’ve used a single `filter` method by combining the two
predicates into a single one.

```java
import java.util.Arrays;
import java.util.List;

public class Example {
    public static void main(String[] args) {
        List<Dessert> desserts = Arrays.asList(
                new Dessert("tiramisu", 8, Dessert.Type.CAKE),
                new Dessert("caramel dust donut", 3, Dessert.Type.DONUT),
                new Dessert("strawberry shortcake", 10, Dessert.Type.CAKE),
                new Dessert("classic glazed donut", 2, Dessert.Type.DONUT),
                new Dessert("simple custard", 4, Dessert.Type.CUSTARD),
                new Dessert("baked custard", 5, Dessert.Type.CUSTARD),
                new Dessert("pumpkin cake", 5, Dessert.Type.CAKE),
                new Dessert("caramel apple coffee cake", 6, Dessert.Type.CAKE));

        List<Dessert> filteredDesserts = desserts.stream()
                .filter(dessert -> dessert.getType().equals(Dessert.Type.DONUT) && dessert.getPrice() < 5)
                .toList();

        System.out.println(filteredDesserts);
    }
}
```

```plaintext
[caramel dust donut, classic glazed donut]
```
