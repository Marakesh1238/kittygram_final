О проекте Kittygram:
Kittygram — социальная сеть для обмена фотографиями любимых питомцев. Позволяет размещать профили котиков с указанием имени, года раждения, цветом окраса (на выбор из палитры), любыми достижениями, которые может придумать владелец. Профиль котика может быть с фото или без. Редактировать профиль питомца может только добавивший его хозяин. Проект разворачивается на сервере в контейнерах. Автоматическое тестирование обновлений в ветку main и перезапуск приложения на сервере осуществляется с помощью облачного сервиса github Actions.

Как запустить проект:
Сделайте форк репозитория с гитхаб.

Настройте Secrets and variables для Actions в настройках своего гитхаба:

DOCKER_PASSWORD
DOCKER_USERNAME
HOST - ip фашего сервера
SSH_KEY - приватный ключ для вашего сервера
SSH_PASSPHRASE - пароль от вашего сервера
USER - имя пользователя от вашего сервера
TELEGRAM_TO - ваш ID в Telegram
TELEGRAM_TOKEN - токен вашего бота в Telegram (для отправки сообщений об успешном запуске проекта на сервере)
Создайте на сервере в домашней дерриктории папку kittygram.

В корне этой папке создайте файл .env и заполните данные по примеру .env.example

Получите доменное имя для вашего сайта.

В настройках nginx на сервере сделайте прокси для всех запросов:

# Kittigram server configuration
server {
    server_tokens off;
    server_name имя_вашего_сайта;

        location /media/ {
            proxy_pass http://127.0.0.1:9000;
            client_max_body_size 20M;
        }

        location /api/ {

            proxy_pass http://127.0.0.1:9000;
            client_max_body_size 20M;
        }

        location /admin/ {
            proxy_pass http://127.0.0.1:9000;
            client_max_body_size 20M;
        }

        location / {
            proxy_pass http://127.0.0.1:9000;
        }

Настройте cerbot, запустите его и получите ssl сертификат для вашего сайта

Перезапустите nginx.

Теперь любой push на гитхаб в этот репозиторий в ветку main автоматически проведет тесты кода, загрузит образы контейнеров в ваш аккаунт dockerhub и развернет проект на сервере.

Примеры запросов API:
Операции с котиками

Get api/cats/
Вернет список из 10 котиков на странице

{
    "count": 55,
    "next": "http://api.example.org/cats/?offset=50&limit=10",
    "previous": "http://api.example.org/cats/?offset=30&limit=10",
    "results": [{}]
}
Post api/cats/
Опубликует профиль котика. Необязательные поля: achievements, image.

{
    "name": "string: max_length=16",
    "color": "string: webcolors.hex_to_name",
    "birth_year": "integer",
    "owner": 0,
    "achievements": 0,
    "image": "string(base64)"
}
Операции с кошачьими достижениями

Get api/achievements/
Предоставит список всех достижений заданных пользователями.

Post api/achievements/
{
    "id": 0,
    "name": "string: max_length=64"
}
Операции с пользователями

Post api/users/
Регистрация пользователя на сайте

{
    "username": "User.USERNAME_FIELD",
    "password": "string",
    "re-password": "string"
}
Ответ:

{
    "username": "User.USERNAME_FIELD",
    "pk": "User._meta.pk.name "
}
Post api/token/login/
Залогинить пользователя на сайте

{
    "username": "User.USERNAME_FIELD",
    "password": "string"
}
Ответ:

{
    "auth_token": "string"
}
Post api/token/logout/
Разлогинить пользователя на сайте

{
    "username": "User.USERNAME_FIELD",
    "password": "string"
}
Ответ - статус HTTP_204_NO_CONTENT

Технологии:
Docker

Backend

Django
djangorestframework
joiser
PostgreSQL
Frontend

JavaScript
React
WSGI сервер

Gunicorn
WEB сервер

NGINX
CD/CI

Github Actions
Об авторе:
Долгов Даниил - бэкенд разработчик на пайтон. https://github.com/marakesh1238
