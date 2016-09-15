# Dagger 2 User's Guide

[Article](https://google.github.io/dagger/users-guide)

## 1\. Dependency Injection

```java
/* Without DI */
class CoffeeMaker {
    private final Heater heater;
    private final Pump pump;

    CoffeeMaker() {
        this.heater = new ElectricHeater();
        this.pump = new Thermosiphon(heater);
    }

    Coffee makeCoffee() { /* ... */}
}

class CoffeeMain {
    public static void main(String[] args) {
        Coffee coffee = new CoffeeMaker().makeCoffee();
    }
}
```

```java
/* With Manual DI */
class CoffeeMaker {
    private final Heater heater;
    private final Pump pump;

    CoffeeMaker(Heater heater, Pump pump) {
        this.heater = checkNotNull(heater);
        this.pump = checkNotNull(pump);
    }

    Coffee makeCoffee() {/* ... */}

    class CoffeeMain {
        public static void main(String[] args) {
            Heater heater = new ElectricHeater();
            Pump pump = new Thermosiphon(heater);
            Coffee coffee = new  CoffeeMaker(heater, pump).makeCoffee();
        }
    }
```

![DI in pictures](assets/di_in_pictures.png)

## 1\. Declaring Dependencies

Use `@Inject` to annotate the constructor that Dagger should use to create instances of a class. When a new instance is requested, Dagger will obtain the required parameters values and invoke this constructor.

```java
class Thermosiphon implements Pump {
    private final Heater heater;

    @Inject
    Thermosiphon(Heater heater) {
        this.heater = heater;
    }

    ...
}
```

Dagger can inject fields directly. In this example it obtains a `Heater` instance for the heater field and a `Pump` instance for the pump field.

```java
class CoffeeMaker {
    @Inject Heater heater;
    @Inject Pump pump;

    ...
}
```

Note: If your class has `@Inject`-annotated fields but no `@Inject`-annotated constructor, Dagger will inject those fields if requested, but will not create new instances. Add a no-argument constructor with the `@Inject` annotation to indicate that Dagger may create instances as well. Classes that lack `@Inject` annotations cannot be constructed by Dagger.

## 3\. Satisfying Dependencies

`@Inject` doesn't work everywhere:

- Interfaces can't be constructed.
- Third-party classes can't be annotated.
- Configurable objects must be configured!

For these cases, use a `@Provides`-annotated method to satisfy a dependency. The method's return type defines which dependency it satisfies. For example, `provideHeater()` is invoked whenever a Heater is required:

```java
@Provides
static Heater provideHeater() {
    return new ElectricHeater();
}
```

It is possible for`@Provides`methods to have dependencies of their own. This one returns a`Thermosiphon` whenever a Pump is required:

```java
@Provides static Pump providePump(Thermosiphon pump) {
    return pump;
}
```

All `@Provides` methods must belong to a module. These are just classes that have an `@Module` annotation.

```java
@Module
class DripCoffeeModule {
  @Provides static Heater provideHeater() {
    return new ElectricHeater();
  }

  @Provides static Pump providePump(Thermosiphon pump) {
    return pump;
  }
}
```

**By convention, `@Provides` methods are named with a `provide` prefix and module classes are named with a `Module` suffix.**

## 4\. Building the Graph

The `@Inject` and `@Provides`-annotated classes form a graph of objects, linked by their dependencies. Calling code like an application's `main` method or an Android `Application` accesses that graph via a well-defined set of roots. In Dagger 2, that set is defined by an interface with methods that have no arguments and return the desired type. By applying the `@Component` annotation to such an interface and passing the `module` types to the modules parameter, Dagger 2 then fully generates an implementation of that contract.

```java
@Component(modules = DripCoffeeModule.class)
interface CoffeeShop {
  CoffeeMaker maker();
}
```

The implementation has the same name as the interface prefixed with `Dagger`. Obtain an instance by invoking the `builder()` method on that implementation and use the returned builder to set dependencies and `build()` a new instance.

```java
CoffeeShop coffeeShop = DaggerCoffeeShop.builder()
    .dripCoffeeModule(new DripCoffeeModule())
    .build();
```

**NOTE:** If your `@Component` is not a top-level type, the generated component's name will be include its enclosing types' names, joined with an underscore. For example, this code:

```java
class Foo {
  static class Bar {
    @Component
    interface BazComponent {}
  }
}
```

would generate a component named DaggerFoo_Bar_BazComponent.

Any module with an accessible default constructor can be elided as the builder will construct an instance automatically if none is set. And for any module whose `@Provides` methods are all static, the implementation doesn't need an instance at all. If all dependencies can be constructed without the user creating a dependency instance, then the generated implementation will also have a `create()` method that can be used to get a new instance without having to deal with the builder.

Now, our CoffeeApp can simply use the Dagger-generated implementation of CoffeeShop to get a fully-injected CoffeeMaker.

```java
public class CoffeeApp {
    public static void main(String[] args) {
      CoffeeShop coffeeShop = DaggerCoffeeShop.create();
      coffeeShop.maker().brew();
    }
}
```

## 5\. Singletons and Scoped Bindings

Annotate an @Provides method or injectable class with `@Singleton`. The graph will use a single instance of the value for all of its clients.

```java
@Provides @Singleton static Heater provideHeater() {
  return new ElectricHeater();
}
```

The `@Singleton` annotation on an injectable class also serves as documentation. It reminds potential maintainers that this class may be shared by multiple threads.

```java
@Singleton
class CoffeeMaker {
  ...
}
```

Since Dagger 2 associates scoped instances in the graph with instances of component implementations, the components themselves need to declare which scope they intend to represent. For example, it wouldn't make any sense to have a `@Singleton` binding and a `@RequestScoped` binding in the same component because those scopes have different lifecycles and thus must live in components with different lifecycles. To declare that a component is associated with a given scope, simply apply the scope annotation to the component interface.

```java
@Component(modules = DripCoffeeModule.class)
@Singleton
interface CoffeeShop {
  CoffeeMaker maker();
}
```

Components may have multiple scope annotations applied. This declares that they are all aliases to the same scope, and so that component may include scoped bindings with any of the scopes it declares.
