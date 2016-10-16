# Описание Web API сервера Batya IM

## 1. Введение

#### 1.1. Входящий формат сообщения в JSON

Многие из запросов протокола возвращают помимо прочего сообщения, отправленные кому-то там или там хуй пойми короче. Во всех случаях JSON-объект сообщения имеет один и тот же формат:

```
{
    guid: [guid],
    sender: [sender],
    timestamp: [timestamp],
    type: [type],
    content: [content]
}
```

* *guid*: глобальный идентификатор сообщения. Применяется для избежания дублей в чате при повторном получении сообщения;
* *sender*: `username` отправителя;
* *timestamp*: UNIX timestamp, обозначающий момент, когда сообщение пришло на сервер;
* *type*: тип сообщения (поддерживаемые типы см. ниже);
* *content*: содержание сообщение (кодировка в зависимости от типа).

|type|content
|---|---|
|text|Текст сообщения в UTF-8
|image|*зарезервировано*
|audio|*зарезервировано*
|video|*зарезервировано*
|sticker|*зарезервировано*

Прим.: типы сообщений кроме `text` будут имплементированы в будущих версиях протокола если вы хотя бы это сделаете.

## 2. Список запросов

#### 2.1. Авторизация пользователя

* **Method/URL:** `POST /login`

*  **URL Params:** *none*

* **Data Params**

  ```
  {
    username: [username],
    password: [password]
  }
  ```
  *password️*: plain text password

* **Success Response:**
  
  * **Code:** `200 Ok`
  * **Content:** `{ token: [token] }`

  *token*: аутентификационный токен пользователя
   
* **Error Response:**

  * **Code:** `403 Forbidden`
  * **Content:** `{ error : "Invalid username or password" }`

* **Sample Call:**

  *TODO: добавить пример на js*
  


#### 2.2. Регистрация пользователя

* **Method/URL:** `POST /register`
  
*  **URL Params:** *none*

* **Data Params**

  ```
  {
    username: [username],
    password: [password]
  }
  ```

* **Success Response:**
  
  * **Code:** `200 Ok`
  * **Content:** `{ token: [token] }`
  
  *token*: аутентификационный токен пользователя
 
* **Error Response:**

  * **Code:** `403 Forbidden`
  * **Content:** `{ error : "Username already taken" }`

* **Sample Call:**

  *TODO: добавить пример на js*



#### 2.3. Получение списка контактов

Список контактов — список объектов, состоящих из имени пользователя и последнего сообщения в диалоге с этим пользователем.

Список сортируется по имени пользователя в лексикографическом порядке, чтобы уменьшить вероятность пропусков или повторов в списке при загрузке его частями.

В одном ответе приходит не более 25 контактов.

* **Method/URL:** `GET /:auth_token/contacts[/offset/:offset]`

* **URL Params:**

  * *auth_token*: аутентификационный токен пользователя (32 символа base64)
  * *offset*: количество контактов, которые неободимо пропустить с начала

* **Success Response:**
  
  * **Code:** `200 Ok`
  * **Content:**
    ```
    [
      {
        name: [username],
        last_message: {
          <формат объекта сообщения см. п. 1.1>
        }
      },
      ...<up to 24 more times>...
    ]
    ```
    
    * *username*: имя пользователя из списка контактов

