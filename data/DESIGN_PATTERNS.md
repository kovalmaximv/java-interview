# Design pattern 

- [Фабричный метод](#фабричный-метод)
- [Абстрактная фабрика](#абстрактная-фабрика)
- [Строитель](#строитель)
- [Адаптер](#адаптер)
- [Мост](#мост)
- [Компоновщик](#компоновщик)
- [Декоратор](#декоратор)
- [Цепочка команд](#цепочка-команд)
- [Состояние](#состояние)
- [Стратегия](#стратегия)

## Фабричный метод
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

## Абстрактная фабрика
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
2) Когда в программе уже используется фабричный метод, но очередные изменения предполагают введение новых типов 
продуктов.

## Строитель
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

## Адаптер
Адаптер - структурный паттерн проектирования, который позволяет объектам с несовместимыми интерфейсами работать вместе.

Принцип работы:
1) Адаптер имеет интерфейс, который совместим с одним из объектов
2) Этот объект может свободно вызывать методы адаптера
3) Адаптер получает эти вызовы и перенаправляет их второму объекту, но уже в том формате, который поймет второй объект

Применимость:
1) Когда вы хотите использовать сторонний класс, но его интерфейс не соответствует остальному коду приложения

## Мост
Мост - структурный паттерн проектирования, который разделяет один или несколько классов на две отдельные иерархии - 
абстракцию и реализацию, позволяя изменять их независимо друг от друга. 

Абстракция тут - нечто управляющее, например GUI.  
Реализация - нечто управляемое (непосредственно выполняющее работу), например API.

Де факто, это стандарт проектирования сейчас и мало кто пишет продакшен код, не используя этот паттерн.

Смысл паттерна в том, чтобы разделить эти две сущности и развивать их независимо. Допустим у нас есть софт, который
должен быть мультиплатформенный и поддерживать разные роли пользователя (админ, юзер, етс). Можно попробовать намешать
это в едином коде, но гораздо легче выделить модуль GUI под каждую платформу и единый API ко всему этому делу. Эти
два модуля можно развивать независимо.

## Компоновщик
Компоновщик - структурный паттерн, который позволяет сгруппировать множество объектов в древовидную структуру, а затем
работать с ней, как будто это единый объект. Таким образом вызов к дереву делегируется каждому объекту в этом дереве.

```java
public interface Saleable {
    double getPrice();
}

public class Good implements Saleable {
    // ...
    public double getPrice() {
        // return its own price
    }
}

public class Package implements Saleable {
    // ...
    List<Good> goods;
    
    public double getPrice() {
        // delegate to Good in List<Good> goods
    }
}

public class Order implements Saleable {
    // ...
    List<Package> packages;
    
    public double getPrice() {
        // delegate to Package in List<Package>
    }
}
```

Применимость:
1) Когда нужно работать с древовидной структурой объектов
2) Когда нужно единообразно трактовать простые и составные объекты

## Декоратор
Декоратор - структурный паттерн, позволяющий динамически добавлять новую функциональность. Оборачивая какой-то базовый 
класс в другие классы (путем агрегации). Базовый и оборачивающий класс должны иметь единый интерфейс, чтобы для 
пользователя не было разницы, к какому классу обращаться. Оборачивающий класс сначала вызывает внутренний класс, затем 
проделывает свою работу. Таким образом можно обернуть друг в друга много декораторов.

```java
public interface Notifier {
    void notify();
}

// base class
public class EmailNotifier implements Notifier {
    public void notify() {
        // send email notification
    }
}

public class SlackNotifier implements Notifier {
    private final Notifier notifier; 
    
    public SlackNotifier(Notifier notifier) {
        this.notifier = notifier;
    }

    public void notify() {
        notifier.notify();
        // send slack notification
    }
}

// and other Notifiers Realization

public class Main {
    public static void main(String[] args) {
        EmailNotifier notifier = new EmailNotifier();
        if (slackEnabled) {
            notifier = new SlackNotifier(notifier);
        }
        notifier.notify();
    }
}

```

Применимость:
1) Когда нужно добавлять функциональность на лету
2) Когда нельзя расширить функциональность при помощи наследования

## Цепочка команд
Цепочка команд - поведенческий паттерн, позволяющий передавать запрос последовательно по цепочке обработчиков. Каждый
обработчик содержит ссылку на следующий. Если работа обработчика завершилась штатно, то запрос передается следующему 
обработчику. Иначе выкидывается исключение и следующему обработчику запрос не передается.

```java
interface Handler {
    void setNext(Handler command);
    boolean handle(Request request);
}

public class AuthenticationHandler implements Handler {
    private Handler next;
    
    public void setNext(Handler handler) {
        this.next = handler;
    }
    
    public boolean handle(Request request) {
        if (canHandle(request)) {
            // business logic
        }
        if (next != null) {
            next.handle(request);
        }
    }
}
```

Применимость:
1) Когда нужно обрабатывать разные запросы несколькими способами, но заранее неизвестно какие запросы будут приходить
2) Когда важно, чтобы обработчики запроса выполнились один за другим в строгом порядке

## Состояние
Состояние - поведенческий паттерн, позволяющий объектам менять поведение в зависимости от состояния и менять состояние 
в зависимости от каких-либо факторов. Состояние в объекте выделяется в отдельный класс, которому делегируются
возможные действия с объектом. Класс-состояние знает, как обрабатывать все действия с объектом. Вводится класс контекст,
содержащий состояние объекта и все возможные действия с ним.

```java
interface State {
    void doThis();
    void doThat();
}

public class ConcreteState implements State {
    private Context context;
    
    public void doThis() {}
    public void doThat() {
        state = new OtherState();
        context.changeState(state);
    }
}

public class Context {
    private State state;
    
    public void changeState(State state) {
        this.state = state;
        state.setContext(this);
    }
    
    public void doThis() {
        state.doThis();
    }
    
    public void doThat() {
        state.doThat();
    }
}

public class Main {
    public static void main(String[] args) {
        State initialState = new ConcreteState();
        Context context = new Context();
        context.changeState(initialState);
        
        context.doThis();
    }
}
```

Применимость:
1) Когда есть объект, у которого много состояний. Состояние очень сильно влияет на поведение этого объекта. Код 
поведения часто меняется.

## Стратегия
Стратегия - поведенческий паттерн, позволяющий выделить семейство схожих алгоритмов в отдельные классы. Такие классы
можно взаимозаменять во время выполнения программы. Для схожих алгоритмов выделяется интерфейс, а все реализации 
алгоритма попадают в отдельные классы. Интерфейс алгоритма используется в коде и на его место можно поставить любой из
классов реализации.

```java
interface Strategy {
    void execute();
}

public class ConcreteStrategy implements Stratergy {
    public void execute() {}
}

public class Context {
    private Strategy strategy;
    
    public void setStrategy(Strategy strategy) {
        this.strategy = strategy;
    }
    
    public void doSomething() {
        strategy.execute();
    }
}

public class Main {
    public static void main(String[] args) {
        Context context = new Context();
        Strategy strategy = new ConcreteStrategy();
        context.setStrategy(strategy);
        context.doSomething();

        context.setStrategy(anotherStrategy);
        context.doSomething();
    }
}
```

Применимость:
1) Когда нужно использовать разные вариации алгоритма внутри одного объекта
2) Когда у вас множество похожих классов, немного отличающиеся поведением