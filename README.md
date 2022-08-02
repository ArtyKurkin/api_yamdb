# YaMDb (групповой проект)
### Описание
Проект __YaMDb__ собирает __отзывы (Review)__ пользователей на __произведения (Titles)__. Произведения делятся на категории: «Книги», «Фильмы», «Музыка». Список __категорий (Category)__ может быть расширен (например, можно добавить категорию «Изобразительное искусство» или «Ювелирка»).
### Команда разработки:
* ⚡️[Антон Яицкий(Тимлид, разработчик №1)](https://github.com/tonik350)
* ⚡️[Артем Куркин(разработчик №2)](https://github.com/ArtyKurkin)
* ⚡️[Экрем Унгорен(разработчик №3)](https://github.com/engoren)

Сами произведения в __YaMDb__ не хранятся, здесь нельзя посмотреть фильм или послушать музыку.
В каждой категории есть произведения: книги, фильмы или музыка. Например, в категории «Книги» могут быть произведения «Винни-Пух и все-все-все» и «Марсианские хроники», а в категории «Музыка» — песня «Давеча» группы «Насекомые» и вторая сюита Баха.  

Произведению может быть присвоен __жанр (Genre)__ из списка предустановленных (например, «Сказка», «Рок» или «Артхаус»). Новые жанры может создавать только `администратор`.  
Произведению может быть присвоен __жанр (Genre)__ из списка предустановленных (например, «Сказка», «Рок» или «Артхаус»). Новые жанры может создавать только `администратор`.  

Благодарные или возмущённые пользователи оставляют к произведениям текстовые __отзывы (Review)__ и ставят произведению оценку в диапазоне от одного до десяти; из пользовательских оценок формируется усреднённая оценка произведения — рейтинг. На одно произведение пользователь может оставить только один отзыв.  

##### В проекте использованы технологии:
* __Python 3.7__
* __Django 2.2.16__
* __DRF__
* __PyJWT__

### Как запустить проект:
__Клонировать репозиторий и перейти в него в командной строке__
```
cd api_yamdb
```
__Cоздать и активировать виртуальное окружение:__
```
python3 -m venv env
source env/bin/activate
python3 -m pip install --upgrade pip
```
__Затем нужно установить все зависимости из файла requirements.txt__
```
pip install -r requirements.txt
```
__Выполнить миграции:__
```
python3 manage.py migrate
```
### Примеры запросов для неаутентифицированных пользователей:
##### Подробная документация доступна по адресу  `http://127.0.0.1:8000/redoc/`
* `api/v1/categories/` _(GET)_ - Получение списка всех категорий
* `api/v1/genres/` _(GET)_ - Получение списка всех жанров
* `api/v1/titles/` _(GET)_ - Получение списка всех произведений
* `api/v1/titles/{title_id}/reviews/` _(GET)_ - Получение списка всех отзывов
* `api/v1/titles/{title_id}/reviews/{review_id}/comments/` _(GET)_ - Получение списка всех комментариев к отзыву  

### Пользовательские роли

__Аноним__ — может просматривать описания произведений, читать отзывы и комментарии.  
__Аутентифицированный пользователь (`user`)__ — может читать всё, как и __Аноним__, может публиковать отзывы и ставить оценки произведениям (фильмам/книгам/песенкам), может комментировать отзывы; может редактировать и удалять свои отзывы и комментарии, редактировать свои оценки произведений. Эта роль присваивается по умолчанию каждому новому пользователю.  
__Модератор (`moderator`)__ — те же права, что и у __Аутентифицированного пользователя__, плюс право удалять и редактировать любые отзывы и комментарии.  
__Администратор (`admin`)__ — полные права на управление всем контентом проекта. Может создавать и удалять произведения, категории и жанры. Может назначать роли пользователям.  
__Суперюзер Django__ должен всегда обладать правами __администратора__, пользователя с правами `admin`. Даже если изменить пользовательскую роль __суперюзера__ — это не лишит его прав __администратора__. 

### Самостоятельная регистрация новых пользователей

Пользователь отправляет POST-запрос с параметрами email и username на эндпоинт: 
```
POST api/v1/auth/signup/
```
```
{
  "email": "string",
  "username": "string"
}
```

Сервис YaMDB отправляет письмо с кодом подтверждения (confirmation_code) на указанный адрес email.
Пользователь отправляет POST-запрос с параметрами username и confirmation_code на эндпоинт /api/v1/auth/token/, в ответе на запрос ему приходит token (JWT-токен):
```
POST api/v1/auth/token/
```
```
{
  "username": "string",
  "confirmation_code": "string"
}
```
В результате пользователь получает токен и может работать с API проекта, отправляя этот токен с каждым запросом.
После регистрации и получения токена пользователь может отправить PATCH-запрос на эндпоинт /api/v1/users/me/ и заполнить поля в своём профайле (описание полей — в документации).

#### Примеры работы с API для авторизованных пользователей
Добавление категории:
```
Права доступа: Администратор.
POST api/v1/categories/
```
```
{
  "name": "string",
  "slug": "string"
}
```
Удаление категории:

```
Права доступа: Администратор.
DELETE api/v1/categories/{slug}/
```
Добавление жанра:

```
Права доступа: Администратор.
POST api/v1/genres/
```
```
 {
  "name": "string",
  "slug": "string"
}
```
Обновление публикации:

```
PUT api/v1/posts/{id}/
```
```
{
"text": "string",
"image": "string",
"group": 0
}
```
Добавление произведения:

```
Права доступа: Администратор. 
Нельзя добавлять произведения, которые еще не вышли (год выпуска не может быть больше текущего).
POST api/v1/titles/
```
```
 {
  "name": "string",
  "year": 0,
  "description": "string",
  "genre": [
    "string"
  ],
  "category": "string"
}
```
Обновление информации о произведении:

```
Права доступа: Администратор
PATCH api/v1/titles/{titles_id}/
```
```
 {
  "name": "string",
  "year": 0,
  "description": "string",
  "genre": [
    "string"
  ],
  "category": "string"
}
```
#### Работа с пользователями:
Получение списка всех пользователей:
```
Права доступа: Администратор
GET api/v1/users/ - Получение списка всех пользователей
```
Добавление пользователя:

```
Права доступа: Администратор
Поля email и username должны быть уникальными.
POST api/v1/users/ - Добавление пользователя
```
```
{
"username": "string",
"email": "user@example.com",
"first_name": "string",
"last_name": "string",
"bio": "string",
"role": "user"
}
```
Получение пользователя по username:

```
Права доступа: Администратор
GET api/v1/users/{username}/ - Получение пользователя по username
```
Изменение данных пользователя по username:

```Права доступа: Администратор
PATCH api/v1/users/{username}/ - Изменение данных пользователя по username
```
```
{
  "username": "string",
  "email": "user@example.com",
  "first_name": "string",
  "last_name": "string",
  "bio": "string",
  "role": "user"
}
```
Удаление пользователя по username:

```
Права доступа: Администратор
DELETE api/v1/users/{username}/ - Удаление пользователя по username
```
Подробная документация доступна по адресу `http://127.0.0.1:8000/redoc/`
