# Dagger 2 User's Guide

[Article](https://google.github.io/dagger/users-guide)

## 1\. Dependency Injection in code

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
            Coffee coffee =
                new CoffeeMaker(heater, pump).makeCoffee();
        }
    }
```
