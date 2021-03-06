# Некоторые предложения для обсуждения развития проекта brief-api

## Naming

### Функции

1. Название функции всегда должно начинаться с глагола;
2. Название функции которая создает должно начинаться с глагола **Create**;
3. Название функции которая выгребает(не важно откуда) должно начинаться с глагола **Get**;
4. Название функции которая удаляет должно начинаться с глагола **Delete** в случае если она физически удаляет сущность;
5. Название функции которая архивирует запись (is_deleted = true) должно начинаться с глагола **Archive**;
6. Название функции которая обновляет сущность, должно начинаться с глагола **Update**;
7. В названии функции должны отсутствовать артикли **(the, a, an)**
8. В названии функции должны отсутствовать слова **(all, one, single)** и т.д. Указание на то что функция работает с множеством сущностей должно быть отражено форме существительного.
9. Естественно название функции должно соответствовать общим правилам. https://www.golangprograms.com/naming-conventions-for-golang-functions.html

| Good | Bad |
| ------ | ------ |
| CreateActivity | AddActivity |
| GetActivity | Activity, SelectActivity, ReadActivity |
| UpdateBriefTeam | UpdateTheBriefTeam |
| GetBriefTeams | GetAllBriefTeams |

### Ручки

1.  Название ручки должно начинаться с названия сущности во множественном числе (даже если мы знаем что у нас всегда будет одна сущность);
2.  Название ручки из нескольких слов должно быть разделено символом **-** ;
3.  В названии не допускаются **глаголы**;
4.  В названии не допускаются слова: **all, one** и т. д.

| Good | Bad |
| ------ | ------ |
| GET: /brief-teams | GET: /brief-team |
| GET: /brief-teams/{id} | GET: /brief-team?id= |
| POST: /brief-teams | POST: /brief-teams/add |
| PUT: /brief-teams | PUT: /brief-teams/update |
| PUT: /brief-teams | PUT: /brief-teams/update/all |
| DELETE: /brief-teams | DELETE: /brief-teams/{id} |

# Запросы
 - Получение одной сущности **GET: / brief-teams/ {id-записи} ? getParams...**
 - Получение множества сущностей **GET: / brief-teams ? getParams...**
 - Создание сущности|сущностей **POST: / brief-teams** . Параметры в теле запроса.
 - Обновление сущности|сущностей **PUT: / brief-teams** . Параметры в теле запроса.
 - Удаление сущности|сущностей **DELETE: / brief-teams** . Параметры в теле запроса.  ~~DELETE: /brief-teams/{id}~~

# База данных

Работа с бд реализуется двумя разными способами:
1. github.com/jmoiron/sqlx
2. github.com/jinzhu/gorm

## sqlx
Почему Sqlx это заебись:
1. Соответствует духу Go -> простота и очевидность кода, нет магии;
2. Очень популярна;
3. Не надо много капаться в доке, просто пишешь sql и всё ок;
4. Не паришься со scan rows, всё распердоливается с struct и т.д, короче радуешься жизни;
5. Подробнее см на github. 

Почему Sqlx это хуйня:
1. Трата времени на написание шаблонного кода, однотипных SELECT, INSER INTO и т.д;
2. С увеличением числа и сложности запросов, сильно увеличивается сложность поддержки;
3. Например для добавления одного поля к сущности, может потребоваться залезть в несколько sql запросов и хорошо если только в одном файле;
4. Добавление функций поиска, пагинации для сущности, приводит к изобретению специального конструктора запросов, который подходит только этой конкретной сущности -> смотри 2 пункт.

## gorm
Почему Gorm это хуйня:
1. Это обосанная ORM, НЕ cоответствует духу Go, слишком много магии;
2. Нужно изучать доку, иначе хуй поймешь почему ничего не работает или работает не так;
3. Куча issues на github;
4. Много не очевидного поведения;
5. Использует interface{} и рефлексию что не в лучшую сторону влияет на скорость;
6. Из 5п. -> что если ты решил создавать HeighLoad project уровня facebook, google, yandex или чё-та попроще, то ORM это не лучший вариант.

Почему Gorm это заебись:
1. Не тратишь время на написание шаблонного sql-кода;
2. С увеличением числа и сложности запросов, сильно НЕ увеличивается сложность поддержки;
3. Абстракция которая позволяет создавать обобщенные, универсальные для всех сущьностей функции;

# Для тех кто дочитал
Для того чтобы код нашего проекта хоть как то был единообразен была сделана приблуда которая на основе таблицы генерирует шаблон CRUD.

Что она дает (приблуда https://github.com/nir007/brief-api-gen):
1. файл для пакета api с созданными ручками CRUD и валидными комментами для swagger;
2. файл для пакета dto с готовыми типами для CRUD;
3. файл для пакета orm с готовыми ф. для CRUD.

Для тех кто использует **sqlx** она (приблуда) тоже подходит за исключением **файла для пакета orm**.

Пака не может **ВСЁ**, но она задает стандартную структуру от которой можно плясать.
Если зайдет, то можно добавить JOINS по foreign keys и т д.
