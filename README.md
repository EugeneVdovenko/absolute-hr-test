# Тестовое задание для Абсолют-страхование

## Реализовать приложение (backend, api) для контроля курса валют.
1. Получение курса валют (1 к 1 или 1 ко многим) на любой день.
2. Возможность сохранить подборку (наборы валют из пункта 1).
3. Получение курса для сохраненной подборки на указанный день (Не нужно авторизации делать, просто по уникальному идентификатору).
4. Возмжоность добавлять примечание к подборке.

## Проблема:
1. Минимизация запросов к внешнему сервису.
2. Оптимизация/ускорение ответов для пользователя.

## Требования
- Реализовать на laravel 7.x версии, php 7.4, база данных на выбор.
- Для получения курса валют использовать любой удобный сервис, например https://www.cbr.ru
- Результат прислать ссылкой на git (github, gitlab, bibucket, etc., не имеет значение).
- При выполнение будет хорошо, если засечете время выполнения задания.

## Описание
- Приложение реализовано на Laravel 7 и PostgreSQL 12.
- После установки обновление курсов происходит по cron раз в сутки.
- Можно запустить консольную команду для обновления курсов.
- API в качестве тела запроса ожидает JSON-сроку.
- Ответ от API - JSON-строка.
- Запрос курсов идет в веб-сервис ЦБ РФ.

## Установка
- `git clone git@github.com:EugeneVdovenko/absolute-hr-test.git ./project`
- `cd project`
- `composer install`
- Внести данные в файл`.env` для подключени к БД:
  ```
  DB_CONNECTION=pgsql
  DB_HOST=127.0.0.1
  DB_PORT=5432
  DB_DATABASE=homestead
  DB_SCHEMA=currencies
  DB_USERNAME=homestead
  DB_PASSWORD=secret
  ```
- Создание базы
  ```
  php artisan app:init
  ```
- Подтягиваем зависимости
  ```
  php artisan migrate
  ```
- Добавить в cron
  ```
  * * * * * cd /path-to-project && php artisan schedule:run >> /dev/null 2>&1
  ```
- Можно на встроенном вебсервере
  ```
  php artisan serve --host 0.0.0.0
  ``` 

## Консольные команды
- Обновление курсов
  ```
  php artisan exchange-rate:update
  ```

## Маршруты

- Добавить отлеживаемую валюту
  ```
  POST http://localhost:8000/api/currencies/add
  
  {
      "code": "EUR",
      "title": "Euro"
  }
  ```
- Создать коллекцию
  ```
  POST http://localhost:8000/api/collections/add
  
  {
      "title": "Коллекция 2",
      "currencies": {
          "USD": "RUB",
          "EUR": "RUB",
          "RUB": "AUD"
      }
  }
  ```
- Запрос курсов по коллекции
  ```
  GET http://localhost:8000/api/collections/{id}/rates?[date={dd-mm-YYYY}]
  
  {
      "data": [
          {
              "source": "USD",
              "target": "RUB",
              "rate": "76.012"
          },
          {
              "source": "EUR",
              "target": "RUB",
              "rate": "90.2643"
          }
      ],
      "meta": {
          "success": {
              "status": "OK"
          }
      }
  }
  ```
- Добавить комментарий к коллекции
  ```
  POST http://localhost:8000/api/collections/{id}/comment
  
  {
      "comment": "Комментарий к коллекции"
  }
  ```

## Roadmap
- нужны валидвации запросов на корректность получаемых данных
- добавить проверку на наличие курса в базе, чтобы избежать дублей
- оптимизиовать преобразование кодов валют в ID
