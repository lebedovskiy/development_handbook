# Разработка Golang приложений

## Язык программирования и структура проекта

В разрабатываемых проектах используется язык программирования golang. Для управления версиями компилятора используется утилита mise (<https://mise.jdx.dev>), также в конфигурационном файле mise.toml прописываются все необходимые команды и переменные окружения.

## Фреймворки

В зависимости от типа модуля используется соответствующий фреймворк:

| **Тип модуля** | **Название фреймворка** | **Ссылка на репозитарий и документацию** |
| -------------- | ----------------------- | ---------------------------------------- |
| Веб-приложение | Gin                     | <https://github.com/gin-gonic/gin>       |
|                |                         | <https://gin-gonic.com/docs/>            |

## Библиотеки

| **Решаемая задача** | **Название библиотеки** | **Ссылка на репозитарий и документацию** |
| ------------------- | ----------------------- | ---------------------------------------- |
| Интернационализация | Go-i18n                 | <https://github.com/nicksnyder/go-i18n>  |

## Основные положения

1. Код должен соответствовать Google styleguide (<https://google.github.io/styleguide/go/guide>) или Uber styleguide (<https://github.com/uber-go/guide>)
2. В проекте должна использоваться самая свежая стабильная версия языка
3. В проекте должны использоваться самые свежие стабильные версии библиотек
4. Необходимо стараться сокращать количество внешних зависимостей для снижения количества точек отказа
5. Необходимо стараться избегать использования нестандартных подходов к разработке
6. Еженедельно для всех приложений, находящихся на поддержке, производится обновление зависимостей
7. Весь код должен быть покрыт тестами с минимальным покрытием 80%
8. Код должен быть отформатирован и проходить проверку линтерами
9. Во любых конфигурационных файлах **[запрещается]** использование IP за редким исключением - обязательно должны быть прописаны доменные имена для всех серверов
10. Код должен работать в выделенном docker-контейнере. Для проекта подготавливается отдельный docker-compose. При росте сложности проекта или при запросе клиента должна производиться миграция на k8s
11. Все асинхронные процессы выносятся в отдельный модуль
12. Все чувствительные данные (email, номер телефона, ИНН, СНИЛС, SSN итд) должны шифроваться перед отправкой в БД, а также удаляться из логов
13. Код должен быть изначально написан с поддержкой i18n
14. Должен использоваться APM или Exceptions tracker
15. Должен использоваться мониторинг
16. Структура проекта должна следовать принципам чистой архитектуры:
    - `/cmd` - точки входа в приложение
    - `/internal` - приватный код приложения
    - `/pkg` - код, который может быть использован другими приложениями
    - `/api` - спецификации API, protocol buffers, и т.д.
    - `/configs` - конфигурационные файлы
    - `/test` - тестовые данные и инструменты
17. Использовать контекст (`context.Context`) для управления временем жизни операций
18. Обработка ошибок должна быть явной, с применением пакета `errors` для добавления контекста к ошибкам
19. Использовать интерфейсы для абстракции и тестирования
20. Применять dependency injection через конструкторы

## Наилучшие практики

1. Придерживаться принципа "Accept interfaces, return structs"
2. Использовать `go generate` для автоматизации генерации кода
3. Применять пакет `sync` для безопасной конкурентной работы, избегать использования глобальных переменных
4. Для работы с JSON использовать тэги и встроенные функции `encoding/json`
5. Использовать `context.Context` для передачи дедлайнов, отмены и метаданных через API границы
6. Именование переменных:
   - Короткие имена для локальных переменных
   - Более описательные имена для экспортируемых сущностей
   - Избегать сокращений кроме общепринятых (HTTP, URL и т.д.)
7. Организация кода:
   - Группировать связанные функции в одном файле
   - Разделять логику бизнес-слоя от инфраструктурного
8. Обработка ошибок:
   - Проверять все возвращаемые ошибки
   - Добавлять контекст к ошибке перед возвращением
   - Использовать sentinel errors для проверки известных ошибок
9. Логирование:
   - Использовать структурированное логирование
   - Различать уровни логирования (debug, info, warn, error)
   - Не логировать чувствительную информацию
10. Тестирование:
    - Писать небольшие, сфокусированные тесты
    - Использовать табличные тесты для тестирования множества кейсов
    - Применять моки для внешних зависимостей

## Вывод проекта в продакшн -- чеклист

1. Тесты проходят
2. Нет ошибок линтера
3. Зависимости обновлены
4. Все асинхронные задачи вынесены в отдельный воркер
5. Код реализован с учетом рекомендаций по безопасности <https://go.dev/doc/security/> и <https://snyk.io/blog/go-security-cheatsheet-for-go-developers/>
6. Убедиться, что все сервера и системы имеют имя в dns и обращения к серверам происходят по доменным именам
7. Проверить, что для всех серверов прописаны статические IP
8. Наличие мониторинга серверов
9. Наличие bug tracking - [AppSignal APM](https://appsignal.com), [Bugsnag](https://www.bugsnag.com), [Rollbar](http://rollbar.com/)
10. Наличие ротации логов - [logrotate](https://www.systutorials.com/docs/linux/man/5-logrotate.conf/)
11. Наличие бэкапа
12. Наличие документации
13. Наличие инструкций по восстановлению после падения
14. Контроль доступов и защита от вторжений
15. Выделены сервера, домены, ресурсы для production-prodlike-staging-development сред
16. Должен быть выстроен процесс обновления системы и зависимостей приложения
17. ВСЕ службы и сервисы зарегистрированы на компанию и оплачены
18. Исходный код отгружен в git-репозитарий
19. Настроено автоматическое развертывание (CI/CD)
20. Настроена отказоустойчивая инфраструктура
21. Проверена производительность под нагрузкой
