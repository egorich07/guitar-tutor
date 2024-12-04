---
title: Выбор урока
sidebar_position: 1
---
# Сценарий выбора урока 


```
@startuml
left to right direction

actor НеавторизованныйПользователь
actor АвторизованныйПользователь


usecase "Регистрация" as Register
usecase "Вход" as Login


НеавторизованныйПользователь -- Register
Register <.right.  Login : extend
НеавторизованныйПользователь -left-|> АвторизованныйПользователь



usecase "Выбор инструмента" as ChooseInstrument
usecase "Выбор уровня игры" as ChooseLevel


АвторизованныйПользователь -- ChooseInstrument


usecase "Выбор урока" as ChooseCourse
usecase "Просмотр доступных уроков" as WatchCourse


АвторизованныйПользователь -- WatchCourse
WatchCourse <.left. ChooseCourse : extend
ChooseInstrument <.left. WatchCourse : include
ChooseInstrument <.left. ChooseLevel : include


usecase "Добавить в избранное" as AddToFavorite
usecase "Перейти к выполнению урока" as DoExercise
usecase "Прослушивание примеров" as ListenExercise
usecase "Просмотр информации об уроке" as WatchExercise
 

АвторизованныйПользователь -- WatchExercise
WatchExercise <.. ListenExercise : extend
WatchExercise <.. DoExercise: extend
WatchExercise <.. AddToFavorite: extend
ChooseCourse <.up. WatchExercise : extend

@enduml

```
---
## Описание алгоритма
---
### **1. Авторизация пользователя**

1. **Клиент** отправляет запрос: `POST /auth/login` с телом:
    
    `{   "login": "user@example.com",   "password": "password123" }`
    
2. **System** проверяет данные:
    - Если данные корректны:
        - **System** создает токен для авторизации.
        - **System** отправляет **Клиент**:

            `{   "token": "abc123",   "userId": "user123" }`
            
    - Если данные некорректны:
        - **System** отправляет **Клиент**: `401 unauthorized`.

---
### **2. Регистрация нового пользователя**

1. **Клиент** отправляет запрос: `POST /auth/register` с телом:
    
    `{   "email": "user@example.com",   "password": "password123",   "name": "John Doe" }`
    
2. **System** проверяет данные:
    - Если данные корректны:
        - **System** регистрирует нового пользователя.
        - **System** отправляет **Клиент**:
            
            `{   "userId": "user123",   "message": "Registration successful." }`
            
    - Если данные некорректны:
        - **System** отправляет **Клиент**: `400 bad request`.

---
### **3. Сохранение выбранного инструмента**

1. **Клиент** отправляет запрос: `POST /user/preferences/instrument` с телом:

    `{   "userId": "user123",   "instrumentId": "guitar" }`
    
2. **System** проверяет данные:
    - Если данные корректны:
        - **System** сохраняет предпочтение пользователя.
        - **System** отправляет **Клиент**:
            
            `{   "message": "Preferences saved successfully." }`
            
    - Если данные некорректны:
        - **System** отправляет **Клиент**: `400 bad request`.

---
### **4. Получение списка уроков**

1. **Клиент** отправляет запрос: `GET /lessons?instrumentId=guitar&difficulty=beginner&page=1&pageSize=10`.
2. **System** проверяет параметры:
    - Если параметры корректны:
        - **System** возвращает список уроков:
            
            `{   "lessons": [     {       "lessonId": "lesson1",       "title": "Guitar Basics",       "description": "Learn basic chords and strumming.",       "difficulty": "beginner",       "duration": "15m",       "imageUrl": "https://example.com/lesson1.jpg"     },     {       "lessonId": "lesson2",       "title": "Introduction to Scales",       "description": "Get started with scales.",       "difficulty": "beginner",       "duration": "20m",       "imageUrl": "https://example.com/lesson2.jpg"     }   ],   "pagination": {     "page": 1,     "pageSize": 10,     "totalPages": 2   } }`
            
    - Если параметры некорректны:
        - **System** отправляет **Клиент**: `400 bad request`.

---
### **5. Получение информации об уроке**

1. **Клиент** отправляет запрос: `GET /lessons/lesson1`.
2. **System** проверяет идентификатор урока:
    - Если урок найден:
        - **System** отправляет **Клиент**:
            
            json
            
            Copy code
            
            `{   "lessonId": "lesson1",   "title": "Guitar Basics",   "description": "Learn basic chords and strumming.",   "videoUrl": "https://example.com/lesson1.mp4",   "progress": {     "completed": 30,     "total": 100   },   "recommendations": [     "lesson2",     "lesson3"   ] }`
            
    - Если урок не найден:
        - **System** отправляет **Клиент**: `404 not found`.
