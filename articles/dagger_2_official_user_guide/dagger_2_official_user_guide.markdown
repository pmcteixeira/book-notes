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

## 2\. Declaring Dependencies

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

## 6\. Reusable scope

Sometimes you want to limit the number of times an `@Inject`-constructed class is instantiated or a `@Provides` method is called, but you don't need to guarantee that the exact same instance is used during the lifetime of any particular component or subcomponent. This can be useful in environments such as Android, where allocations can be expensive.

For these bindings, you can apply `@Reusable` scope. `@Reusable`-scoped bindings, unlike other scopes, are not associated with any single component; instead, each component that actually uses the binding will cache the returned or instantiated object.

That means that if you install a module with a `@Reusable` binding in a component, but only a subcomponent actually uses the binding, then only that subcomponent will cache the binding's object. If two subcomponents that do not share an ancestor each use the binding, each of them will cache its own object. If a component's ancestor has already cached the object, the subcomponent will reuse it.

**There is no guarantee that the component will call the binding only once**, so applying `@Reusable` to bindings that return mutable objects, or objects where it's important to refer to the same instance, is dangerous. It's safe to use @Reusable for immutable objects that you would leave unscoped if you didn't care how many times they were allocated.

```java
@Reusable // It doesn't matter how many scoopers we use, but don't waste them.
class CoffeeScooper {
  @Inject CoffeeScooper() {}
}

@Module
class CashRegisterModule {
  @Provides
  @Reusable // DON'T DO THIS! You do care which register you put your cash in.
            // Use a specific scope instead.
  static CashRegister badIdeaCashRegister() {
    return new CashRegister();
  }
}

@Reusable // DON'T DO THIS! You really do want a new filter each time, so this
          // should be unscoped.
class CoffeeFilter {
  @Inject CoffeeFilter() {}
}
```

## 7\. Lazy injections

Sometimes you need an object to be instantiated lazily. For any binding `T`, you can create a `Lazy<T>` which defers instantiation until the first call to `Lazy<T>`'s `get()` method. If `T` is a singleton, then `Lazy<T>` will be the same instance for all injections within the `ObjectGraph`. Otherwise, each injection site will get its own `Lazy<T>` instance. Regardless, subsequent calls to any given instance of `Lazy<T>` will return the same underlying instance of T.

```java
class GridingCoffeeMaker {
  @Inject Lazy<Grinder> lazyGrinder;

  public void brew() {
    while (needsGrinding()) {
      // Grinder created once on first call to .get() and cached.
      lazyGrinder.get().grind();
    }
  }
}
```

## 8\. Provider injections

Sometimes you need multiple instances to be returned instead of just injecting a single value. While you have several options (Factories, Builders, etc.), one option is to inject a `Provider<T>` instead of just `T`. A `Provider<T>` invokes the binding logic for `T` each time `.get()` is called. If that binding logic is an `@Inject` constructor, a new instance will be created, but a `@Provides` method has no such guarantee.

```java
class BigCoffeeMaker {
  @Inject Provider<Filter> filterProvider;

  public void brew(int numberOfPots) {
  ...
    for (int p = 0; p < numberOfPots; p++) {
      maker.addFilter(filterProvider.get()); //new filter every time.
      maker.addCoffee(...);
      maker.percolate();
      ...
    }
  }
}
```

## 9 . Qualifiers

Sometimes the type alone is insufficient to identify a dependency. For example, a sophisticated coffee maker app may want separate heaters for the water and the hot plate.

In this case, we add a qualifier annotation. This is any annotation that itself has a `@Qualifier` annotation. Here's the declaration of `@Named`, a qualifier annotation included in javax.inject:

```java
@Qualifier
@Documented
@Retention(RUNTIME)
public @interface Named {
  String value() default "";
}
```

You can create your own qualifier annotations, or just use `@Named`. Apply qualifiers by annotating the field or parameter of interest. The type and qualifier annotation will both be used to identify the dependency.

```java
class ExpensiveCoffeeMaker {
  @Inject @Named("water") Heater waterHeater;
  @Inject @Named("hot plate") Heater hotPlateHeater;
  ...
}
```

Supply qualified values by annotating the corresponding @Provides method.

```java
@Provides @Named("hot plate") static Heater provideHotPlateHeater() {
  return new ElectricHeater(70);
}
```

```java
@Provides @Named("water") static Heater provideWaterHeater() {
  return new ElectricHeater(93);
}
```

Dependencies may not have multiple qualifier annotations.
