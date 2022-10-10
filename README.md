# Filter

## Learning Goals

- Use the `filter` method to select stream elements that match a predicate.
- Create a stream pipeline with intermediate `filter` operation followed by a terminal operation.
- Chain multiple filter methods.

## What is Filtering?

`Stream<T>	filter(Predicate<? super T> predicate)`

The `filter` method selects elements from a stream that match the
given predicate. The `filter` method is an intermediate stream operation
that returns a new stream containing the selected elements. Filtering
is often followed by a terminal operation such `count` or `forEach`.

The code below shows a stream-->filter-->forEach pipeline to select
the even integers in an array.  Each step in the pipeline is coded
as a separate statement.  Notice the `filter` method returns a new stream:

```java
import java.util.Arrays;
import java.util.stream.IntStream;

public class Example {
    public static void main(String[] args) {
        int[] nums = {1, 2, 3, 4, 5, 6, 7, 8, 9, 10};

        //stream-->filter-->forEach pipeline as separate statements
        
        //Step 1: convert data source to stream
        IntStream numStream = Arrays.stream(nums);

        //Step 2: filter to get a new stream with a subset of elements
        IntStream evenNumStream = numStream.filter(num -> num % 2 == 0);

        //Step 3: Use terminal operation to print the elements in the stream
        evenNumStream.forEach(System.out::println);
    }
}
```

The program prints:

```text
2
4
6
8
10
```

We can also code the pipeline in a single statement, producing the same output:

```java
public static void main(String[] args) {
        int[] nums = {1, 2, 3, 4, 5, 6, 7, 8, 9, 10};

        //stream-->filter-->forEach pipeline as single statement

        Arrays.stream(nums)
           .filter(num -> num%2 == 0)
           .forEach(System.out::println);
}
```


## Filtering Object Streams

Let’s see an example where we filter objects.
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

    public String getName() { return name; }

    public int getPrice() { return price; }

    public Type getType() { return type; }

    @Override
    public String toString() {
        return "Dessert{" +
                "name='" + name + '\'' +
                ", price=" + price +
                ", type=" + type +
                '}';
    }

    public enum Type { CAKE, DONUT, CUSTARD }
}
```

Let’s look at how we can filter a
list of `Dessert` objects to select those with type `DONUT`.

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

        desserts.stream()
                .filter(dessert -> dessert.getType().equals(Dessert.Type.DONUT))
                .forEach(System.out::println);
    }
}
```

The filtered stream contains two desserts:

```text
Dessert{name='caramel dust donut', price=3, type=DONUT}
Dessert{name='classic glazed donut', price=2, type=DONUT}
```

Let's  use the `min` method after filtering to find the lowest donut price.

Recall `min` returns an optional element in case the filtered stream is empty.

```java
import java.util.Arrays;
import java.util.Comparator;
import java.util.List;
import java.util.Optional;

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

        Optional<Dessert> cheapestDonut = desserts.stream()
                .filter(dessert -> dessert.getType().equals(Dessert.Type.DONUT))
                .min(Comparator.comparingInt(Dessert::getPrice));

        cheapestDonut.ifPresentOrElse(
                donut -> System.out.println("Lowest donut price: $" + donut.getPrice()),
                () -> System.out.println("No donuts in list"));
    }
}

```

The program prints:

```text
Lowest donut price: $2
```

## Chaining Filter Methods

The `filter` method can be chained to filter out multiple elements. 
For example, to select custard desserts under $5:

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

        desserts.stream()
                .filter(dessert -> dessert.getType().equals(Dessert.Type.CUSTARD))
                .filter(dessert -> dessert.getPrice() < 5)
                .forEach(System.out::println);

    }
}
```

The chained filtering returns one element:

```text
Dessert{name='simple custard', price=4, type=CUSTARD}
```

We can also use a single `filter` method call by combining the two
predicates with the logical operator `&&`. 

```java
desserts.stream()
        .filter(dessert -> dessert.getType().equals(Dessert.Type.CUSTARD) && dessert.getPrice() < 5)
        .forEach(System.out::println);
```


## Summary

The `filter` method is an intermediate stream operation that returns a new stream
containing elements that match a predicate.

## Resources

[Java 11 Stream filter()](https://docs.oracle.com/en/java/javase/11/docs/api/java.base/java/util/stream/Stream.html#filter(java.util.function.Predicate))
