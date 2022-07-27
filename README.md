![workflow](https://github.com/ValentinDevPy/yamdb_final/actions/workflows/yamdb_workflow.yml/badge.svg)

# yamdb_final

yamdb_final

# YaMDb api

## Самая актуальная версия API всемирно известного YaMDb.

YaTube API это RESTful API, позволяющий создавать и редактировать отзывы на различные произведения, оценивать их и
оставлять комментарии к отзывам. В основе проекта лежат Django и Django REST Framework.

## Особенности

- Пишите свои ревью и ставьте оценки произведениям.
- Авторизация происходит по токену.
- Токен можно получить введя код подтверждения из электронного письма.
- При просмотре информации о каком-либо произведении будет выведена его средняя оценка.
- Для авторизованных пользователей присутствует возможность комментировать отзывы.

## Технологии

- [Django](https://github.com/django/django) - фреймворк, который включает в себя все необходимое для быстрой разработки
  различных веб-сервисовю
- [Django REST Framework](https://www.django-rest-framework.org/) - фреймворк, расширяющий возможности Django и
  позволяющий быстро писать RESTful API для Django-проектов.

Конечно же YaTube API это ПО с открытым исходным кодом и [публичным репозиторием](https://github.com/ValentinDevPy/yamdb_final)
на GitHub.

## Пользовательские роли

```Аноним — может просматривать описания произведений, читать отзывы и комментарии.```

```Аутентифицированный пользователь (user) — может, как и Аноним, читать всё, дополнительно он может публиковать отзывы и ставить оценку произведениям (фильмам/книгам/песенкам), может комментировать чужие отзывы;может редактировать и удалять свои отзывы и комментарии. Эта роль присваивается по умолчанию каждому новому пользователю.```

```Модератор (moderator) — те же права, что и у Аутентифицированного пользователя плюс право удалять любые отзывы и комментарии.```

```Администратор (admin) — полные права на управление всем контентом проекта. Может создавать и удалять произведения, категории и жанры. Может назначать роли пользователям.```

```Суперюзер Django — обладет правами администратора (admin)```


## Начало работы

### Как запустить проект:

Сделайте форк репозитория https://github.com/ValentinDevPy/yamdb_final и склонируйте этот форк на свою рабочую машину.


Сделайте push образа контейнера из api_yamdb/Dockerfile в ваш DockerHub.


Скопируйте файлы docker-compose.yaml и nginx/default.conf из  директории 
проекта на сервер в home/<ваш_username>/docker-compose.yaml и home/<ваш_username>/nginx/default.conf соответственно.

Добавьте следующие SECRETS для вашего репозитория:

```
DB_ENGINE -engine вашей базы данных
DB_HOST - адрес вашей базы данных
DB_NAME - имя вашей базы данных
DB_PORT - порт по которому происходит подключение к БД
POSTGRES_USER - пользователь вашей базы данных
POSTGRES_PASSWORD - пароль от вашей БД

DOCKER_USERNAME - ваше имя пользователя DockerHub
DOCKER_PASSWORD - ваш пароль DockerHub

HOST - ip адрес вашего сервера
USER - пользователь удаленного сервера
PASSPHRASE - секретная фраза от вашего SSH ключа (если она есть)
SSH_KEY - ключ SSH с вашего локального компьютера

TELEGRAM_TO - ваш Telegram id
TELEGRAM_TOKEN - токен бота, который используется для уведомлений
```

После запуска контейнера необходимо собрать статику на удаленном сервере:

```
 docker-compose exec web python manage.py collectstatic
```

После сборки статики выполнить миграции:

```
docker-compose exec web python manage.py migrate
```

API будет доступен по адресу:

```
http://<HOST>/api/v1/
```

## Примеры работы с API:

Получение списка всех категорий и создание новой(доступно только для роли 'Administrator') происходит на эндпоинте:

```
http://<HOST>/api/v1/categories/
```

Пример ответа при GET запросе с параметрами offset и count:

```
[
  {
    "count": 0,
    "next": "string",
    "previous": "string",
    "results": [
      {
        "name": "string",
        "slug": "string"
      }
    ]
  }
]

```

Редактирование категорий запрещено. Удаление категории происходит на эндпоинте:

```
http://<HOST>/api/v1/categories/{slug}/
```

Получение списка жанров и создание нового(доступно только для роли 'Administrator') происходит на эндпоинте:

```
http://<HOST>/api/v1/genres/
```

Пример ответа:

```
[
  {
    "count": 0,
    "next": "string",
    "previous": "string",
    "results": [
      {
        "name": "string",
        "slug": "string"
      }
    ]
  }
]
```

Получение конкретного жанра, а так же его редактирование запрещено. А удаление происходит на эндпоинте:

```
http://<HOST>/api/v1/genres/{slug}/
```

Получение списка произведений и создание нового происходит на эндпоинте:

```
http://<HOST>/api/v1/titles/
```

Пример ответа:

```
[
  {
    "id": 0,
    "title": "string",
    "slug": "string",
    "description": "string"
  }
]
```

Для конкретной группы доступен только метод GET(id - первичный ключ таблицы Groups) на эндпоинте:

```
http://<HOST>/api/v1/groups/{id}/
```

Пример ответа:

```
{
  "id": 0,
  "title": "string",
  "slug": "string",
  "description": "string"
}
```

Следующий эндпоинт возвращает все подписки пользователя, сделавшего запрос. Анонимные запросы запрещены (метод GET):

```
http://<HOST>/api/v1/follow/
```

Пример ответа:

```
[
  {
    "user": "string",
    "following": "string"
  }
]
```

Подписка пользователя от имени которого сделан запрос на пользователя переданного в теле запроса. Анонимные запросы
запрещены (метод POST):

```
http://<HOST>/api/v1/follow/
```

Создание, обновление и верификация токена происходит на следущих эндпоинтах:

```
http://<HOST>/api/v1/titles/{title_id}/reviews/{review_id}/comments/
```

Пример ответа:

```
[
  {
    "count": 0,
    "next": "string",
    "previous": "string",
    "results": [
      {
        "id": 0,
        "text": "string",
        "author": "string",
        "pub_date": "2019-08-24T14:15:22Z"
      }
    ]
  }
]
```

Получение конкретного комментария, а так же его редактирование и удаление
(доступно только для автора комментария, модератора или администратора) происходит на эндпоинте:

```
http://<HOST>/api/v1/titles/{title_id}/reviews/{review_id}/comments/{comment_id}/
```

Пример ответа:

```
{
  "id": 0,
  "text": "string",
  "author": "string",
  "pub_date": "2019-08-24T14:15:22Z"
}
```

Регистрация пользователя и получение jwt токена происходит на следующих эндпоинтах:

```
http://<HOST>/api/v1/auth/signup/
http://<HOST>/api/v1/auth/token/
```

## Лицензия

**MIT**

**Free Software, Hell Yeah!**


