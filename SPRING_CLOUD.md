# Spring Cloud
### Что это
Spring Cloud позволяет не реализовывать некоторые распределенные паттерны и часто используемые вспомогательные сервисы.
По аналогии со Spring Boot он позволяет из коробки развернуть и донастроить спринговый сервис, который будет решать
поставленные задачи. 

Предлагаемые сервисы:
- Distributed/versioned configuration
- Service registration and discovery
- Routing
- Service-to-service calls
- Load balancing
- Circuit Breakers
- Global locks
- Leadership election and cluster state
- Distributed messaging
- etc

### Spring Cloud Config
Данный инструмент позволяет реализовать Config Server, который будет подтягивать конфиги из git репы. Сервисы будут 
подтягивать конфигурацию с данного Config Server.

Config Server позволяешь шифровать и дешифровать значения. Таким образом в нем можно хранить зашифрованные sensitive
данные, которые сервис дешифрует при получении.

### Spring Cloud Service discovery
