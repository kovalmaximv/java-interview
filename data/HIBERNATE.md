# Hibernate && Spring JPA

- [Lazy/Eager loading](#lazyeager-loading)
- [N+1 проблема](#n1-проблема)
- [LazyInitializationException](#lazyinitializationexception)
- [Состояния сущностей](#состояния-сущностей)

## Lazy/Eager loading
Для погружения связей сущностей в Hibernate есть два типа подгрузки:
1) Lazy - связанные сущности подгружаются по требованию
2) Eager - связанные сущности подгружаются сразу

ManyToOne и OneToOne по умолчанию lazy. OneToMany - eager.

Загружать все вложенные сущности жадно может быть не всегда хорошей идеей. Зачем, если они нам, например, не нужны.

## N+1 проблема
Проблема N+1 встречается в Hibernate и других ORM фреймворках. Появляется она, если у нас есть объект со связью
`@OneToMany` или `@ManyToOne`. В таком случае Hibernate сначала загрузит изначальный объект (**1 запрос**), а потом 
сделает **N запросов**, чтобы достать все связанные объекты. Отсюда название проблемы.

Пример:
```java
@Entity
public class Post {
    @Id
    private Long id;
    private String title;
    @OneToMany
    private List<PostComment> commentList;
}
 
@Entity
public class PostComment {
    @Id
    private Long id;
    @ManyToOne
    private Post post;
    private String review;
}
```
Теперь если попытаться запросить Post, то вместе с получением поста Hibernate отправит N запросов за комментариями. 
Получим N+1 проблему. 

Пути решения проблемы:
1) `join fetch` - в `@Query` запросе JPA или запросе `EntityManager` нужно указать `join fetch` с указанием, для какого
поля необходимо прописать join. Данные будут подтягиваться в рамках одного запроса с join
2) Entity graph - специальный инструмент, который указывает на уровне сущности/репозитория, какие поля должны быть 
подтянуты с помощью join. Принцип действия похож на предыдущий инструмент. На уровне entity используется аннотация 
`@NamedEntityGraph`, на уровне метода репозитория `@EntityGraph`

## LazyInitializationException
Hibernate требует HibernateSession для выполнения любых запросов к БД. 

В случае чистого Hibernate сессии управляются вручную. В случае Spring JPA сессии открываются и закрываются 
автоматически под капотом. Например:
1) Во время выполнения запроса уровня Repository
2) Во время выполнения метода помеченного как Transactional

Если попытаться получить lazy вложенные сущности вне сессии, то Hibernate просто не сможет обратиться к БД для 
подгрузки этой сущности и свалится с ошибкой LazyInitializationException. Классический пример: получили данные в 
Repository, далее в Service пытаетесь получить вложенные объекты и получаете LazyInitializationException.

Способы решения проблемы:
1) Использовать EntityGraph/Join fetch в запросе, чтобы hibernate сразу подгрузил необходимые вложенные сущности 
(использование Eager может привести к N+1 проблеме)
2) Навесить `@Transactional(readOnly=true)` на метод уровня Service

Если коллекция в сервисе используется не сразу, а где-то дальше по стеку, где сессии уже не будет, то можно использовать
конструкцию `Hibernate.initialize(entity.getLazyLoadedProperty())`.

## Состояния сущностей
Сущность может быть в одном из следующих состояний: transient, persistent, detached.

**Transient** - объект создан при помощи конструктора, но еще не сохранен в БД. Если id заполнено, то это уже detached.  
**Persistent** - объект сохранен в БД и прикреплен к hibernate session.  
**Detached** - объект сохранен в БД и не прикреплен к hibernate session.  
**Deleted** - объект помечен к удалению и будет удален из БД.  

Объект можно переводить по статусам при помощи методов:
1) persist - transient -> persistent - сохраняет в БД и присоединяет к сессии. Выдает исключение, если у объекта заполнен id.
2) merge - detached -> persistent - сохраняет изменения в БД, если объект изменялся, пока он был detached. Необходимо 
использовать, если этот объект уже есть в сессии и нам необходимо обновить его при помощи detached версии  
3) replicate - detached -> persistent - сохраняет объект в БД когда у него установлен id. Как правило, необходим при 
миграции данных из одной БД в другую. Если в БД уже есть сущность с таким id, устанавливается поведение на выбор: 
[IGNORE, OVERWRITE, LATEST_VERSION, EXCEPTION]
4) delete - persistent -> deleted - удаляет объект из БД
5) save - transient/detached -> persistent - сохраняет объект в БД, всегда генерирует новый id, даже если у объекта он 
уже был установлен
6) update - detached -> persistent - обновляет объект в БД. Если данный объект уже есть в сессии, то update упадет с 
ошибкой, нужно использовать merge
7) refresh - detached -> persistent - обновляет detached объект данными из БД