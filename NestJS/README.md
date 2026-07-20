# Документация NestJS на русском

Перевод официальной документации [NestJS](https://docs.nestjs.com/) — прогрессивного Node.js-фреймворка для построения серверных приложений.

Страницы пронумерованы в порядке чтения, как на официальном сайте.


- [Введение](<01. Introduction.md>)

## Overview

- [Первые шаги](<02. Overview/01. First steps.md>)
- [Контроллеры](<02. Overview/02. Controllers.md>)
- [Провайдеры](<02. Overview/03. Providers.md>)
- [Модули](<02. Overview/04. Modules.md>)
- [Middleware](<02. Overview/05. Middleware.md>)
- [Фильтры исключений](<02. Overview/06. Exception filters.md>)
- [Pipes](<02. Overview/07. Pipes.md>)
- [Guards](<02. Overview/08. Guards.md>)
- [Interceptors](<02. Overview/09. Interceptors.md>)
- [Пользовательские декораторы маршрутов](<02. Overview/10. Custom decorators.md>)

## Fundamentals

- [Пользовательские провайдеры](<03. Fundamentals/01. Custom providers.md>)
- [Асинхронные провайдеры](<03. Fundamentals/02. Asynchronous providers.md>)
- [Динамические модули](<03. Fundamentals/03. Dynamic modules.md>)
- [Области внедрения](<03. Fundamentals/04. Injection scopes.md>)
- [Циклическая зависимость](<03. Fundamentals/05. Circular dependency.md>)
- [Ссылка на модуль](<03. Fundamentals/06. Module reference.md>)
- [Ленивая загрузка модулей](<03. Fundamentals/07. Lazy-loading modules.md>)
- [Контекст выполнения](<03. Fundamentals/08. Execution context.md>)
- [События жизненного цикла](<03. Fundamentals/09. Lifecycle events.md>)
- [Discovery service](<03. Fundamentals/10. Discovery service.md>)
- [Независимость от платформы](<03. Fundamentals/11. Platform agnosticism.md>)
- [Тестирование](<03. Fundamentals/12. Testing.md>)

## Techniques

- [Конфигурация](<04. Techniques/01. Configuration.md>)
- [База данных](<04. Techniques/02. Database.md>)
- [Mongo](<04. Techniques/03. Mongo.md>)
- [Валидация](<04. Techniques/04. Validation.md>)
- [Кэширование](<04. Techniques/05. Caching.md>)
- [Сериализация](<04. Techniques/06. Serialization.md>)
- [Версионирование](<04. Techniques/07. Versioning.md>)
- [Планирование задач](<04. Techniques/08. Task scheduling.md>)
- [Очереди](<04. Techniques/09. Queues.md>)
- [Logger](<04. Techniques/10. Logging.md>)
- [Cookies](<04. Techniques/11. Cookies.md>)
- [Events](<04. Techniques/12. Events.md>)
- [Сжатие](<04. Techniques/13. Compression.md>)
- [Загрузка файлов](<04. Techniques/14. File upload.md>)
- [Потоковая передача файлов](<04. Techniques/15. Streaming files.md>)
- [HTTP-модуль](<04. Techniques/16. HTTP module.md>)
- [Сессии](<04. Techniques/17. Session.md>)
- [Model-View-Controller](<04. Techniques/18. Model-View-Controller.md>)
- [Производительность (Fastify)](<04. Techniques/19. Performance (Fastify).md>)
- [Server-Sent Events](<04. Techniques/20. Server-Sent Events.md>)

## Security

- [Аутентификация](<05. Security/01. Authentication.md>)
- [Авторизация](<05. Security/02. Authorization.md>)
- [Шифрование и хэширование](<05. Security/03. Encryption and Hashing.md>)
- [Helmet](<05. Security/04. Helmet.md>)
- [CORS](<05. Security/05. CORS.md>)
- [Защита от CSRF](<05. Security/06. CSRF Protection.md>)
- [Ограничение частоты запросов](<05. Security/07. Rate limiting.md>)

## GraphQL

- [Используем силу TypeScript и GraphQL](<06. GraphQL/01. Quick start.md>)
- [Резолверы](<06. GraphQL/02. Resolvers.md>)
- [Мутации](<06. GraphQL/03. Mutations.md>)
- [Подписки](<06. GraphQL/04. Subscriptions.md>)
- [Скалярные типы](<06. GraphQL/05. Scalars.md>)
- [Директивы](<06. GraphQL/06. Directives.md>)
- [Интерфейсы](<06. GraphQL/07. Interfaces.md>)
- [Union-типы](<06. GraphQL/08. Unions and Enums.md>)
- [Field middleware](<06. GraphQL/09. Field middleware.md>)
- [Mapped-типы](<06. GraphQL/10. Mapped types.md>)
- [Плагины в Apollo](<06. GraphQL/11. Plugins.md>)
- [Сложность запросов](<06. GraphQL/12. Complexity.md>)
- [Расширения](<06. GraphQL/13. Extensions.md>)
- [Плагин CLI](<06. GraphQL/14. CLI Plugin.md>)
- [Генерация SDL](<06. GraphQL/15. Generating SDL.md>)
- [Совместное использование моделей](<06. GraphQL/16. Sharing models.md>)
- [Прочие возможности](<06. GraphQL/17. Other features.md>)
- [Федерация](<06. GraphQL/18. Federation.md>)

## WebSockets

- [Шлюзы](<07. WebSockets/01. Gateways.md>)
- [Фильтры исключений](<07. WebSockets/02. Exception filters.md>)
- [Pipes](<07. WebSockets/03. Pipes.md>)
- [Guards](<07. WebSockets/04. Guards.md>)
- [Interceptors](<07. WebSockets/05. Interceptors.md>)
- [Адаптеры](<07. WebSockets/06. Adapters.md>)

## Microservices

- [Обзор](<08. Microservices/01. Overview.md>)
- [Redis](<08. Microservices/02. Redis.md>)
- [MQTT](<08. Microservices/03. MQTT.md>)
- [NATS](<08. Microservices/04. NATS.md>)
- [RabbitMQ](<08. Microservices/05. RabbitMQ.md>)
- [Kafka](<08. Microservices/06. Kafka.md>)
- [gRPC](<08. Microservices/07. gRPC.md>)
- [Пользовательские транспорты](<08. Microservices/08. Custom transporters.md>)
- [Фильтры исключений](<08. Microservices/09. Exception filters.md>)
- [Pipes](<08. Microservices/10. Pipes.md>)
- [Guards](<08. Microservices/11. Guards.md>)
- [Interceptors](<08. Microservices/12. Interceptors.md>)

- [Развёртывание](<09. Deployment.md>)

- [Автономные приложения](<10. Standalone apps.md>)

## CLI

- [Обзор](<11. CLI/01. Overview.md>)
- [Рабочие пространства](<11. CLI/02. Workspaces.md>)
- [Библиотеки](<11. CLI/03. Libraries.md>)
- [Справочник команд CLI](<11. CLI/04. Usage.md>)
- [Nest CLI и скрипты](<11. CLI/05. Scripts.md>)

## OpenAPI

- [Введение](<12. OpenAPI/01. Introduction.md>)
- [Типы и параметры](<12. OpenAPI/02. Types and Parameters.md>)
- [Операции](<12. OpenAPI/03. Operations.md>)
- [Безопасность](<12. OpenAPI/04. Security.md>)
- [Mapped types](<12. OpenAPI/05. Mapped Types.md>)
- [Декораторы](<12. OpenAPI/06. Decorators.md>)
- [Плагин CLI](<12. OpenAPI/07. CLI Plugin.md>)
- [Прочие возможности](<12. OpenAPI/08. Other features.md>)

## Recipes

- [Read-Eval-Print-Loop (REPL)](<13. Recipes/01. REPL.md>)
- [Генератор CRUD (только TypeScript)](<13. Recipes/02. CRUD generator.md>)
- [SWC](<13. Recipes/03. SWC (fast compiler).md>)
- [Passport (аутентификация)](<13. Recipes/04. Passport (auth).md>)
- [Горячая перезагрузка](<13. Recipes/05. Hot reload.md>)
- [MikroORM](<13. Recipes/06. MikroORM.md>)
- [SQL (TypeORM)](<13. Recipes/07. TypeORM.md>)
- [MongoDB (Mongoose)](<13. Recipes/08. Mongoose.md>)
- [SQL (Sequelize)](<13. Recipes/09. Sequelize.md>)
- [Модуль маршрутизации](<13. Recipes/10. Router module.md>)
- [Проверки работоспособности (Terminus)](<13. Recipes/11. Health checks.md>)
- [CQRS](<13. Recipes/12. CQRS.md>)
- [Документация](<13. Recipes/13. Compodoc.md>)
- [Prisma](<13. Recipes/14. Prisma.md>)
- [Sentry](<13. Recipes/15. Sentry.md>)
- [Раздача статики](<13. Recipes/16. Serve static.md>)
- [Nest Commander](<13. Recipes/17. Commander.md>)
- [Async Local Storage](<13. Recipes/18. Async local storage.md>)
- [Necord](<13. Recipes/19. Necord.md>)
- [Suites](<13. Recipes/20. Suites (Automock).md>)

## FAQ

- [Serverless](<14. FAQ/01. Serverless.md>)
- [HTTP-адаптер](<14. FAQ/02. HTTP adapter.md>)
- [Keep-alive соединения](<14. FAQ/03. Keep-Alive connections.md>)
- [Глобальный префикс](<14. FAQ/04. Global path prefix.md>)
- [Сырое тело запроса](<14. FAQ/05. Raw body.md>)
- [Гибридное приложение](<14. FAQ/06. Hybrid application.md>)
- [HTTPS](<14. FAQ/07. HTTPS & multiple servers.md>)
- [Жизненный цикл запроса](<14. FAQ/08. Request lifecycle.md>)
- [Типичные ошибки](<14. FAQ/09. Common errors.md>)

## Devtools

- [Обзор](<15. Devtools/01. Overview.md>)
- [Интеграция с CI/CD](<15. Devtools/02. CI-CD integration.md>)

- [Руководство по миграции](<16. Migration guide.md>)

## Discover

- [Кто использует Nest?](<17. Discover/01. Who is using Nest.md>)

- [Поддержка](<18. Support us.md>)


---

> _Перевод официальной документации NestJS (https://docs.nestjs.com/). Оригинал распространяется по лицензии MIT (© Kamil Myśliwiec)._
