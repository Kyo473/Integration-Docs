---
sidebar_position: 3
title: Архитектура пользовательских интеграций
hide_table_of_contents: true
---

# Введение

Взаимодействие с API позволяет системам обмениваться данными и выполнять различные действия. Для корректной работы интеграции необходимо правильно настроить запросы и аутентификацию, чтобы обеспечить безопасный и эффективный доступ к внешним сервисам.


## Базовая структура интеграций

```js
app = {
    schema: 2,
    version: 'Номер версии',
    label: 'Название интеграции',
    description: 'Описание интеграции',
    blocks: {},
    connections: {}
}
```
## Blocks

Blocks – это основные строительные элементы интеграции, которые выполняют определенные задачи.
Например, блок может получать данные из API или отправлять данные в определенное приложение.
Блоки нужны для структурирования и выполнения конкретных шагов в процессе интеграции.

:::tip context и bundle передается как часть аргументов в функцию executePagination.
В зависимости от реализации, контекст может быть использован для хранения данных между вызовами и передаваться обратно в функцию для обработки в следующем шаге.

```ts
executePagination: (z, bundle, context) => {
  // Логика работы с контекстом и данных API
}
```
:::
<details>
<summary>Пример функционального блока</summary>
```js
export const getAllScripts: IntegrationBlock<
  { onlyActive: boolean },
  ICommonAuthData
> = {
  label: "Получить все скрипты в системе",
  description: "Блок получает все скрипты в системе",
  inputFields: [
    {
      key: "onlyActive",
      type: "boolean",
      label: "Только активные скрипты",
      default: true,
    },
  ],
  executePagination: (service, bundle, _context) => {
    if (!bundle.authData || !bundle.authData.url) {
      service.stringError("Отсутствует подключение.");
    }
    const explorer = new GraphQLClient(service);
    const response = explorer.query<IApiResponse>(
      bundle.authData.url,
      `query {
        automation {
          script {
            script_general_list {
              items {
                element {
                  id
                  name
                  enabled
                }
              }
            }
          }
        }
      }`
    );

    const items = response.data?.automation.script.script_general_list.items ?? [];

    const output = items.reduce<TNormalizedItem[]>((acc, { element }) => {
      if (element.name.includes("_DISABLE_")) {
        return acc;
      }

      if (bundle.inputData.onlyActive && !element.enabled) {
        return acc;
      }

      acc.push([element.id, element.name, element.enabled]);
      return acc;
    }, []);

    const outputVariables: OutputBlockVariables[] = [
      { type: "Long", name: "id" },
      { type: "String", name: "name" },
      { type: "Boolean", name: "enabled" },
    ];

    return {
      output,
      output_variables: outputVariables,
      state: undefined,
      hasNext: false,
    };
  }
}
```
</details>

## Connections

Это параметры подключения к внешним системам и сервисам.
Они используются для установления связи между интеграцией и внешними API или базами данных.

<details>
<summary>Пример блока подключений</summary>
```js
import { IntegrationConnection } from "@infomaximum/integration-sdk";
import { ICommonAuthData } from "../types/connections";

export const basicConnect: IntegrationConnection<ICommonAuthData> = {
  label: "Подключение по ApiKey",
  description: "Подключение по ApiKey",
  inputFields: [
    {
      key: "connection_apiKey",
      type: "text",
      label: "API-ключ",
    },
    {
      key: "connection_host",
      type: "text",
      label: "Хост",
    },
    {
      key: "connection_port",
      type: "text",
      label: "Порт ",
    },
    {
      key: "host_button",
      type: "button",
      label: "Автоопределение хоста",
      typeOptions: {
        saveFields: (service, bundle) => {
          const preparedHost = bundle.authData.BASE_URL.replace(
            /^https?:\/\//,
            ""
          ).replace("/webhook/" + bundle.authData.GUID, "");

          const [host, port = ""] = preparedHost.split(":");

          return {
            connection_host: host,
            connection_port: port,
          };
        },
      },
    },
  ],
  execute: (service, bundle) => {
    function createUrl(host: string, port: string, apiKey: string): string {
      const hasProtocol = /^https?:\/\//.test(host);
      const base = hasProtocol ? host : `http://${host}`;

      if (!port || port === "0") {
        return `${base}/graphql?api_key=${apiKey}`;
      }
      return `${base}:${port}/graphql?api_key=${apiKey}`;
    }

    return {
      apiKey: bundle.authData.connection_apiKey,
      host: bundle.authData.connection_host,
      port: bundle.authData.connection_port,
      url: createUrl(
        bundle.authData.connection_host,
        bundle.authData.connection_port,
        bundle.authData.connection_apiKey
      ),
    };
  },
  refresh: () => {
    /*Empty*/
  },
};

```
</details>

<details>
<summary>Пример использования redirect</summary>

```js
redirect: (_service, bundle) =>
  "https://login.microsoftonline.com/" +
  bundle.authData.tenant_id +
  "/oauth2/v2.0/authorize?" +
  "response_type=code" +
  "&client_id=" +
  bundle.authData.client_id +
  "&redirect_uri=" +
  bundle.authData.redirect_url +
  "&scope=User.Read Calendars.Read OnlineMeetingTranscript.Read.All OnlineMeetings.Read offline_access",
```

</details>

<details>
<summary>Пример использования saveFields</summary>

:::danger
`access_token`,`refresh_token`,`expiration_time` должны иметь именно такие названия при сохранении,иначе механизм рефреш не сработает.
:::


```js
saveFields: (service, bundle) => {
const body =
  "code=" +
  authCode +
  "&client_id=" +
  clientId +
  "&redirect_uri=" +
  bundle.authData.redirect_url +
  "&client_secret=" +
  clientSecret +
  "&grant_type=authorization_code";

const response = service.request<ArrayBuffer>({
  url: `https://login.microsoftonline.com/${catalogIdentifier}/oauth2/v2.0/token`,
  method: "POST",
  headers: {
    "Content-Type": "application/x-www-form-urlencoded",
  },
  jsonBody: body,
});
const exchangeCode = JSON.parse(new TextDecoder().decode(response.response));

const accTok = exchangeCode.access_token;
if (!accTok) {
  service.stringError(
    "Не удалось выполнить обмен Authorization Code на access_token.\n" +
      JSON.stringify(exchangeCode.response)
  );
}
  return {
    access_token: accTok,
    refresh_token: exchangeCode.refresh_token,
    expiration_time: exchangeCode.expires_in,
  };
},
```

</details>



## Поля ввода и их использование

inputFields – это поля ввода, которые позволяют пользователю задавать параметры для блоков интеграции.
Они необходимы для настройки параметров, которые будут использоваться в процессе выполнения блока.

:::tip
Вы можете узнать больше из этих разделов [Входные поля](/docs/InputFields) и [Выходные поля](/docs/OutputVariables) 
:::

## Доступный функционал GraalJS

| Категория      | Функционал             | Статус      | Комментарий специалиста                                                                                           |
|----------------|------------------------|-------------|--------------------------------------------------------------------------------------------------------------------|
| **Core JS**     | Стандарты ES2023+      | ✅ Доступно  | `Object`, `Array`, `Math`, `JSON`, `Map`, `Set` работают в полном объёме.                                          |
| **Interop**     | Java Access            | ⛔ Блокировано | Объекты `Java`, `Packages` присутствуют, но вызовы методов запрещены *(Unsupported operation)*.                     |
| **Interop**     | Polyglot               | ⛔ Блокировано | `Polyglot.eval` и `evalFile` недоступны для запуска других языков (Python/R).                                      |
| **Concurrency** | SharedArrayBuffer      | ✅ Доступно  | Выделение разделяемой памяти разрешено для высокопроизводительных задач.                                          |
| **Concurrency** | Atomics (Data)         | ✅ Доступно  | `add`, `load`, `exchange`, `compareExchange` работают корректно.                                                  |
| **Concurrency** | Atomics (Wait)         | ⛔ Блокировано | `Atomics.wait` выдаёт `TypeError`, защищая поток от зависания.                                                    |                                  |
| **Encoding**    | TextEncoder/Decoder    | ✅ Доступно  | Полноценная поддержка преобразования строк в байты и обратно.                                                     |
