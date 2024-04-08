# Hibernate && Spring JPA

- [N+1 проблема](#n1-проблема)

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