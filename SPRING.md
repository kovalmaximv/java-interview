# Spring
### Как работает @Transactional spring

### Bean creation lifecycle
1) Инициализируется контекст
2) BeanDefinitionReader читает и запоминает определение бинов
3) BeanFactory читает из BeanDefinitionReader все BeanPostProcessor и создает их
4) BeanFactory создает все остальные бины из BeanDefinitionReader
5) К каждому бину применяются все BeanPostProcessor beforeInitialization методы
6) У каждого бина вызывается init() метод
7) К каждому бину применяются все BeanPostProcessor afterInitialization методы
8) Готовый бин кладется в контейнер.

Важно, что синглтоны создаются сразу, а прототайпы по требованию (прототайпы в контейнер не складываются).

### Трехфазовый конструктор
Идея в том, чтоб настроить Bean 

1) Java construct.
2) @PostConstruct / init method (BeanPostProcessor). Для обращения к механизмам Spring.
3) ApplicationContextListener. Может работать на этапах, когда контекст уже создан.

**Зачем нужен init метод**  
Если мы обратимся к спринговым компонентам в контроллере, то получим NPE. BeanInjection происходит после создания 
работы контроллера. А в init method все зависимости спринга будут на месте.

### ApplicationContextListener  
Данный интерфейс позволяет слушать ApplicationContext и как-то реагировать на его lifecycle events.
Его events в натуральном порядке:

1) ContextStartedEvent
2) ContextRefreshedEvent
3) ContextStoppedEvent
4) ContextClosedEvent

### BeanFactoryPostProcessor
Позволяет повлиять на BeanFactory после его инициализации.
Например можно поправить определенный BeanDefinition.

Схема работы такая:

1) BeanDefinitionReader читает XML -> генерит BeanDefinition
2) BeanFactoryPostProcessor читает BeanDefinition -> как-то их изменяет МЫ ТУТ
3) BeanFactory читает BeanDefinition -> кладет бины в IoC container

### Зачем BeanPostProcessor нужно before и after initialization
before передаст измененный объект в init метод, иногда это нежелательно

### Обновление Prototype в Singletone
```java
class abstract Singleton {
    public abstract Prototype getPrototype();
}

@Config
class JavaConfig {
    @Bean
    @Scope("prototype")
    public Prototype prototype() {
        return prototype;
    }
    
    @Bean
    public Singleton singleton() {
        return new Singleton() {
            @Override
            public Prototype getPrototype() {
                return prototype();
            }
        };
    }
}
```

### ApplicationContextInitilizer
Отрабатывает, когда контекст только начинает инициализацию, еще ничего не создано