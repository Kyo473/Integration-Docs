---
sidebar_position: 4
title: Работа с bundle
hide_table_of_contents: true
---

# Использование контекста в интеграции

Данные, которые вводит пользователь в поля ввода, можно использовать в коде интеграции с помощью объектов bundle.
Объект bundle предоставляет доступ к этим данным и включает две ключевые части: authData и inputData.
Эти данные используются для настройки запросов и выполнения других действий внутри блока.

## bundle.authData

bundle.authData содержит данные для аутентификации, которые вы настраиваете в разделе "Подключения" (Connections).
Все поля ввода, указанные в подключении, становятся доступными через bundle.authData.
Эти данные используются для доступа к API или другим внешним сервисам.

<details>
<summary>Пример использования authData</summary>

Если вы настроили подключение с полями `api_key` и `api_url`, то эти данные будут доступны в bundle.authData и могут быть использованы в вашем блоке для выполнения запросов

```js
(service, bundle) => {
    var response = service.request({
        url: bundle.authData.api_url + '/data',
        method: 'GET',
        headers: {
            Authorization: 'Bearer ' + bundle.authData.api_key
        }
    });

    if (response.status !== 200) {
        service.stringError('Ошибка запроса: ' + response.status);
    }
    return {
        output: () => ({
            data: response.json
        })
    };
}
```

</details>

## bundle.inputData

bundle.inputData содержит данные, введенные пользователем в поля ввода (inputFields) блока.
Эти данные используются для настройки параметров запроса или выполнения других действий в блоке.
Все поля ввода, которые вы настраиваете в блоке, становятся доступными через bundle.inputData.

<details>
<summary>Пример использования inputData</summary>

Если у вас есть блок с полями ввода `api_endpoint` и `access_token`, эти данные будут доступны в `bundle.inputData` и могут быть использованы для настройки вашего запроса

```js
(service, bundle) => {
    var response = service.request({
        url: bundle.inputData.api_endpoint,
        method: 'GET',
        headers: {
            Authorization: 'Bearer ' + bundle.inputData.access_token
        }
    });
    if (response.status !== 200) {
        service.stringError('Ошибка запроса: ' + response.status);
    }
    return {
        output: () => ({
            data: response.json
        })
    };
}
```
</details>