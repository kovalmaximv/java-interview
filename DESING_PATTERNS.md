# Паттерны проектирования
### Фабричный метод
Фабричный метод - порождающий паттерн проектирования, который определяет общий интерфейс для создания объектов,
позволяя подклассам изменять тип создаваемых объектов.

```java
class Animal {}
class Cat extends Animal {}
class Dog extends Animal {}

abstract class ClassWithFactoryMethod {
    abstract Animal createAnimal();
}

class CatClassWithFactoryMethod extends ClassWithFactoryMethod {
    Cat createAnimal() {
        return new Cat();
    }
}

class DogClassWithFactoryMethod extends ClassWithFactoryMethod {
    Dog createAnimal() {
        return new Dog();
    }
}
```

Применимость:
1) Фабричный метод разделяет код создания объектов и код работы с объектами. Благодаря этому можно добавлять новые
типы объектов, не меняя код работы с ними.
2) Когда вы хотите переиспользовать уже созданные объекты вместо создания новых.

### Абстрактная фабрика
Абстрактная фабрика - порождающий паттерн проектирования, который позволяет создавать семейства связанных объектов. 

Класс с единственным методом, который является фабричным - вырожденный пример абстрактной фабрики.

```java
public interface FurnitureFactory {
    Chair createChair();
    CoffeeTable createCoffeeTable();
    Sofa createSofa();
}

public class VictorianFurnitureFactory implements FurnitureFactory {
    VictorianChair createChair() { return new VictorianChair(); }
    VictorianCoffeeTable createCoffeeTable() { return new VictorianCoffeeTable(); }
    VictorianSofa createSofa() { return new VictorianSofa(); }
}

public class ModernFurnitureFactory implements FurnitureFactory {
    ModernChair createChair() { return new ModernChair(); }
    ModernCoffeeTable createCoffeeTable() { return new ModernCoffeeTable(); }
    ModernSofa createSofa() { return new ModernSofa(); }
}
```

Применимость:
1) Когда бизнес логика работает с разными видами связанных друг с другом объектов.
2) Когда в программе уже используется фабричный метод, но очередные имзенения предполагают введение новых типов 
продуктов.

### Строитель
Строитель - порождающий паттерн, позволюящий создавать сложные объекты пошагово. Как правило, строитель создает
неизменяемые объекты (иначе можно использовать сеттеры).

Если у нас есть объект с большим количеством параметров, не все из которых обязательно должны быть заданы, то в данном 
классе может быть монструозный конструктор. Второй подход - использовать отдельный класс строитель, который будет 
создавать необходимый класс со всеми необходимыми параметрами (пряча монструозный конструктор)

Для управления строителями можно сделать отдельный класс директор.

```java
public class Car {
    private final CarType carType;
    private final int seats;
    private final Engine engine;
}

public class CarPrototype {
    private final CarType carType;
    private final int seats;
    private final Engine engine;
}

public interface Builder {
    void setCarType(CarType carType);
    void setSeats(int seats);
    void setEngine(Engine engine);
}

public class CarBuilder implements Builder {
    private CarType carType;
    private int seats;
    private Engine engine;
    
    // setters 
    
    public Car getResult() {
        return new Car(carType, seats, engine);
    }
}

public class PrototypeCarBuilder implements Builder {
    private CarType carType;
    private int seats;
    private Engine engine;

    // setters 

    public CarPrototype getResult() {
        return new CarPrototype(carType, seats, engine);
    }
}

public class Director {
    public void constructSportCar(Builder builder) {
        builder.setCarType(CarType.SPORT_CAR);
        builder.setEngine(new Engine(3.0, 0));
        builder.setSeats(2);
    }
}
```

Применимость:
1) У вас 100500 вариаций конструктора и вы хотите от всех избавиться и оставить 1
2) Когда код должен создавать разные вариации какого-то сложного неизменяемого объекта.

