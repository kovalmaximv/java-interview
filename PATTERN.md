# Шаблоны проектирования

### SOLID
1) Single Responsibility - Модуль имеет только 1 причину для изменения
2) Open-Closed Principle - Модуль открыт для расширения и закрыт для изменений
3) Liskov Principle - Взаимозаменяеыме части должны иметь общий контракт. По сути про интерфейсы
4) Interface Segregation Principle - Необходимо избегать от зависимостей, которые не используются
5) Dependency Inversion Principle - высокоуровневый код не должен зависеть от низкоуровневого кода. 
Зависимость направлена на абстракции, а не реализации

Стройте зависимости от абстракций, они стабильный.

### Что такое Dependency Injection?
**Dependency Injection** (внедрение зависимости) - паттерны и принципы разработки, которые позволяют писать 
слабосвязный код. В полном соответствии с принципом единой обязанности объект отдаёт заботу о построении требуемых 
ему зависимостей внешнему, специально предназначенному для этого общему механизму.