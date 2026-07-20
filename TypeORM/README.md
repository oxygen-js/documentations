# Документация TypeORM на русском

Перевод официальной документации [TypeORM](https://typeorm.io/) — ORM для TypeScript и JavaScript, работающей с Node.js, браузером и мобильными платформами.

Страницы пронумерованы в порядке чтения, как в официальном оглавлении.


- [Начало работы](<01. Getting Started.md>)

## Data Source

- [DataSource](<02. Data Source/01. DataSource.md>)
- [Опции источника данных](<02. Data Source/02. Data Source Options.md>)
- [Несколько источников данных, баз данных, схем и настройка репликации](<02. Data Source/03. Multiple data sources, databases, schemas and replication setup.md>)
- [DataSource API](<02. Data Source/04. DataSource API.md>)
- [Обработка значений null и undefined в условиях where](<02. Data Source/05. Handling null and undefined values in where conditions.md>)

## Entity

- [Сущности](<03. Entity/01. Entities.md>)
- [Встраиваемые сущности](<03. Entity/02. Embedded Entities.md>)
- [Наследование сущностей](<03. Entity/03. Entity Inheritance.md>)
- [Древовидные сущности](<03. Entity/04. Tree Entities.md>)
- [Сущности-представления](<03. Entity/05. View Entities.md>)
- [Вынесение определения сущности](<03. Entity/06. Separating Entity Definition.md>)

## Relations

- [Связи](<04. Relations/01. Relations.md>)
- [Связи «один к одному»](<04. Relations/02. One-to-one relations.md>)
- [Связи «многие к одному» / «один ко многим»](<04. Relations/03. Many-to-one - one-to-many relations.md>)
- [Связи «многие ко многим»](<04. Relations/04. Many-to-many relations.md>)
- [Жадные и ленивые связи](<04. Relations/05. Eager and Lazy Relations.md>)
- [Частые вопросы о связях](<04. Relations/06. Relations FAQ.md>)

- [Индексы](<05. Indexes.md>)

## Migrations

- [Как работают миграции?](<06. Migrations/01. How migrations work.md>)
- [Настройка](<06. Migrations/02. Setup.md>)
- [Создание вручную](<06. Migrations/03. Creating manually.md>)
- [Генерация](<06. Migrations/04. Generating.md>)
- [Выполнение и откат](<06. Migrations/05. Executing and reverting.md>)
- [Откат миграций](<06. Migrations/06. Reverting.md>)
- [Статус](<06. Migrations/07. Status.md>)
- [Имитация миграций и откатов](<06. Migrations/08. Faking Migrations and Rollbacks.md>)
- [API query runner](<06. Migrations/09. Query Runner API.md>)
- [Дополнительные опции](<06. Migrations/10. Extra options.md>)
- [Vite](<06. Migrations/11. Vite.md>)

## Working with Entity Manager

- [EntityManager](<07. Working with Entity Manager/01. EntityManager.md>)
- [Репозиторий](<07. Working with Entity Manager/02. Repository.md>)
- [Опции find](<07. Working with Entity Manager/03. Find Options.md>)
- [Пользовательские репозитории](<07. Working with Entity Manager/04. Custom repositories.md>)
- [`EntityManager` API](<07. Working with Entity Manager/05. EntityManager API.md>)
- [API репозиториев](<07. Working with Entity Manager/06. Repository APIs.md>)

## Query Builder

- [Выборка с помощью Query Builder](<08. Query Builder/01. Select using Query Builder.md>)
- [Вставка данных через Query Builder](<08. Query Builder/02. Insert using Query Builder.md>)
- [Обновление через Query Builder](<08. Query Builder/03. Update using Query Builder.md>)
- [Удаление с помощью Query Builder](<08. Query Builder/04. Delete using Query Builder.md>)
- [Работа со связями](<08. Query Builder/05. Working with Relations.md>)
- [Кэширование запросов](<08. Query Builder/06. Caching queries.md>)

- [Query Runner](<09. Query Runner.md>)

- [Транзакции](<10. Transactions.md>)

- [Слушатели и подписчики](<11. Listeners and Subscribers.md>)

## Performance Optimization

- [Введение](<12. Performance Optimization/01. Introduction.md>)
- [Эффективное использование Query Builder](<12. Performance Optimization/02. Query Builder.md>)
- [Использование индексов](<12. Performance Optimization/03. Indexes.md>)
- [Ленивая и жадная загрузка](<12. Performance Optimization/04. Lazy and Eager Loading.md>)
- [Подсказки для запросов](<12. Performance Optimization/05. Query Hints.md>)
- [Постраничная выборка](<12. Performance Optimization/06. Pagination.md>)
- [Кэширование](<12. Performance Optimization/07. Caching.md>)

## Drivers

- [Google Spanner](<13. Drivers/01. Google Spanner.md>)
- [Microsoft SQLServer](<13. Drivers/02. Microsoft SQLServer.md>)
- [MongoDB](<13. Drivers/03. MongoDB.md>)
- [MySQL / MariaDB](<13. Drivers/04. MySQL - MariaDB.md>)
- [Oracle](<13. Drivers/05. Oracle.md>)
- [Postgres / CockroachDB](<13. Drivers/06. Postgres - CockroachDB.md>)
- [SAP HANA](<13. Drivers/07. SAP HANA.md>)
- [SQLite](<13. Drivers/08. SQLite.md>)

- [Логирование](<14. Logging.md>)

- [Использование CLI](<15. Using CLI.md>)

## Guides

- [Active Record против Data Mapper](<16. Guides/01. Active Record vs Data Mapper.md>)
- [Использование валидации](<16. Guides/02. Using Validation.md>)
- [Пример использования TypeORM с Express](<16. Guides/03. Example using TypeORM with Express.md>)
- [Использование с JavaScript](<16. Guides/04. Using with JavaScript.md>)
- [Переход с Sequelize на TypeORM](<16. Guides/05. Migration from Sequelize to TypeORM.md>)
- [SQL-тег](<16. Guides/06. SQL Tag.md>)

## Help

- [FAQ](<17. Help/01. FAQ.md>)
- [Поддерживаемые платформы](<17. Help/02. Supported platforms.md>)
- [Справочник по декораторам](<17. Help/03. Decorator reference.md>)
- [Поддержка](<17. Help/04. Support.md>)

## Releases

- [Примечания к выпуску 1.0](<18. Releases/01. Release Notes.md>)
- [Обновление с 0.3 до 1.0](<18. Releases/02. Upgrading from 0.3.md>)
- [Дорожная карта](<18. Releases/03. Roadmap.md>)


---

> _Перевод официальной документации TypeORM (https://typeorm.io/). Оригинал распространяется по лицензии MIT (© TypeORM)._
