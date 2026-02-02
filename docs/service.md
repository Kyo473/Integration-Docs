---
sidebar_position: 3
title: Работа с service
hide_table_of_contents: true
---

# Использование объекта service

Объект `service` — Это набор методов для взаимодействия c API, вывода ошибок, работы с хуками и итераторами


| Название | Использование | Описание | Параметры | 
|-----------|----------------|-----------|------------|
| **base64Encode** | `service.base64Encode()` | Производит Base64-кодирование входной строки. | **Вход:** строка, которую нужно закодировать. |
| **base64Decode** | `service.base64Decode()` | Производит Base64-декодирование входной строки. | **Вход:** строка в формате `base64`, которую нужно декодировать. |
| **request** | `service.request({ url, method, headers, jsonBody })` | Выполняет HTTP-запрос. ⚠️ **Важно:** результатом является объект с полем `response`, содержащим байтовый массив данных от сервиса — для дальнейшей работы их нужно декодировать. | **url (обязательно)** — адрес ресурса. **method (обязательно)** — HTTP-метод (`GET`, `POST`, `PATCH`, `PUT`, `DELETE`). **headers (опционально)** — HTTP-заголовки. **jsonBody / multipartBody** — тело запроса (обязательно для `POST`, `PATCH`, `PUT`). |
| **hook** | Пример: ```{ key: "authorize", type: "button", label: "Авторизоваться", executeWithRedirect: (service, bundle) => {   return `https://gitlab.com/oauth/authorize?...`; }, executeWithSaveFields: (service, bundle) => { /* обмен code → token */ }, executeWithMessage: (service, bundle) => { /* проверка авторизации */ }}``` | Обрабатывает процесс авторизации и ответ от OAuth-ресурса (например, GitLab). Используется для выполнения редиректа, обмена `authorization code` на `access_token` и проверки успешной авторизации. | — |
| **stringError** | `service.stringError("Текст ошибки")` | Выводит сообщение об ошибке в блоке интеграции. | **Вход:** строка — текст ошибки, который будет отображён пользователю. |

:::tip 
Интеграции поддерживают ввод и передачу файлов с использованием multipartBody в запросах.
Это позволяет отправлять файлы в формате multipart/form-data.
:::

<details>
<summary>Пример передачи файла</summary>

`text` Однострочное текстовое поле для ввода строковых значений

```js
var resp = service.request({
    url: 'https://api.example.com/upload',
    method: 'POST',
    headers: {
        Authorization: 'Bearer ' + bundle.authData.api_key,
        'Content-Type': 'multipart/form-data'
    },
    multipartBody: [
        {
            key: 'file',
            fileValue: bundle.inputData.file_content,
            fileName: bundle.inputData.file_name,
            contentType: 'application/octet-stream'
        }
    ]
});
if (resp.status !== 200) {
    service.error.stringError('Ошибка запроса: ' + resp.status);
}

```

</details>