# Авито.Работа RecSys

## Проект был выполнен в рамках хакатона на школе математического моделирования при МАИ 2023

## Роль в проекте
* Backend - написание методов для бэкенда, работа с ChatGPT API и Yandex Cloud API
* ML - предобработка сырых данных

## Поставленные проблемы:

* Краткая версия вакансии не даёт достаточно информации для принятия решения
* Пользователи совершают ошибки при вводе запроса
* Запросы могут совпадать с категорией товаров (например “банки”)
* Текущая механика не персонализирована (нет учета истории контактов)

## Отделения проекта

### ML 

#### Улучшение читаемости текста вакансии (проблема 1)
  
[Код решения](./backend/api/summorize/fortest.py). Использование [ChatGPT API](  
https://platform.openai.com/docs/api-reference/models/list) для составления краткой карточки вакансии. Используем [Yandex Cloud API](https://cloud.yandex.ru/docs/translate/) для перевода текста с русского на английский для создание запроса к ChatGpt и для перевода запроса на русский. 

#### Учет истории контактов для персонализированных рекомендаций (проблема 4)
 
1. Мы используем прошлые контакты пользователя, а конкретно:  

+ данные о пользователе:
	+ Пол
	+ **Город**
	+ Дата регистрации на сервисе
	+ **Сфера деятельности**
	+ Количество контактов в этой сфере деятельности
+ и вакансиях, с которыми он взаимодействовал:
	+ **Город**
	+ **Сфера деятельности**
	+ График работы
	+  Профессия

	Изначально происхожит матчинг по сферам городу и сферам деятельности.

2. Также используем тексты этих вакансий, преобразованные с помощью нейросети Bert 
`transformers.BertTokenizer` в списки чисел ([эмбеддинги](https://en.wikipedia.org/wiki/Word_embedding)).
  
3. Находим в датасете список из топ n релевантных вакансий (по [косинусному расстоянию](https://scikit-learn.org/stable/modules/generated/sklearn.metrics.pairwise.cosine_distances.html)). И выдаём ему этот список, разделенный по сферам деятельности. [Код решения](./core_ml/modules/engine.py).

### backend

Описание backend-отделения и используемых технологий:

У нас есть два компонента:
- FastAPI для создания API
  - Метод get /graph, который возвращает граф вакансий
  - Метод post /summorize, который обрабатывает текст вакансии 
- Search. Для пользователя находятся релеватные вакансии
- Используется RPYC для общения между узлами.

### frontend

Описание frontend-отделения и используемых технологий:

- Vue.js для разработки пользовательского интерфейса
- С помощью axios фронтенд общается с бэкендом
- Вывод графа происходит с помощью [v-network-graph](https://dash14.github.io/v-network-graph/)

## Установка и запуск

1. Добавить токены в api_summorize_config.py
2. Добавить данные с пользователями и вакансиями в корневую папку data
3. Запустить docker контейнеры: ```docker compose up```
4. Фронтенд будет запущен локально и доступен по адресу http://10.5.0.2:5173/search
   
## Как могло бы выглядеть
![](./img/avito.png)
## Реализация
![](./img/realization.png)
## Лицензия


### [Licence](https://r.mtdv.me/articles/docker_run)
