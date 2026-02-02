---
sidebar_position: 2
title: Входные поля
hide_table_of_contents: true
---
   
# Поля ввода и их использование

InputFields – это поля ввода, которые позволяют пользователю задавать параметры для блоков интеграции. Они необходимы для настройки параметров, которые будут использоваться в процессе выполнения блока.

## Поля для блоков интеграции

Эти параметры есть у каждого поля, перечисленного ниже

| Атрибут     | Тип      | Описание                                                                 |
|--------------|----------|--------------------------------------------------------------------------|
| `key`        | string   | Уникальный идентификатор поля. Используется для доступа к значению через `bundle.inputData[key]`. |
| `type`       | string   | Тип поля.                                                               |
| `label`      | string   | Текст метки поля.                                                       |
| `required`   | boolean  | Обязательно ли поле для заполнения. По умолчанию `false`.               |
| `hint`       | string   | Подсказка под полем. Поддерживает Markdown.                             |
| `default`    | any      | Значение по умолчанию, если пользователь не ввел данные.                |
| `placeholder`| string   | Текст-заполнитель внутри поля.                                          |
| `readOnly`   | boolean  | Поле нельзя редактировать. По умолчанию `false`.                        |
| `mappable`   | boolean  | Разрешает использовать переменные из других блоков. По умолчанию `true`.|


<details>
<summary>Параметры поля `text`</summary>

`text` Однострочное текстовое поле для ввода строковых значений

```ts
/**
 * Текстовое поле ввода
 */
export type TextBlockInputField = CommonBlockInputField & {
  type: "text";
  typeOptions?: {
    /** Включить полноэкранный режим */
    enableFullscreen?: boolean;
    /** Минимальная длина текста */
    minLength?: number;
    /** Максимальная длина текста */
    maxLength?: number;
    /** Регулярное выражение для валидации */
    pattern?: string;
    /** Сообщение об ошибке валидации */
    errorMessage?: string;
  };
};
```

</details>

<details>
<summary>Параметры поля `number`</summary>

`number` Предназначено для ввода чисел любого размера

```ts
/**
 * Числовое поле ввода (с плавающей точкой)
 */
export type NumberBlockInputField = CommonBlockInputField & {
  type: "number";
  typeOptions?: {
    /** Минимальное значение */
    min?: number;
    /** Максимальное значение */
    max?: number;
  };
};
```

</details>

<details>
<summary>Параметры поля `integer`</summary>

`integer` Предназначено для ввода целых чисел (от -9007199254740991 до 9007199254740991)

```ts
/**
 * Целочисленное поле ввода
 * @template Key - Тип ключа поля
 */
export type IntegerBlockInputField<Key extends string = string> =
  CommonBlockInputField<Key> & {
    type: "integer";
    typeOptions?: {
      /** Минимальное значение */
      min?: number;
      /** Максимальное значение */
      max?: number;
    };
  };
```

</details>

<details>
<summary>Параметры поля `date`</summary>

`date` Поле для выбора даты

```ts
/**
 * Поле ввода даты
 * @template Key - Тип ключа поля
 */
export type DateBlockInputField<Key extends string = string> =
  CommonBlockInputField<Key> & {
    type: "date";
    typeOptions?: {
      /** Минимальная дата (timestamp) */
      min?: number;
      /** Максимальная дата (timestamp) */
      max?: number;
      /** Отключенные даты (timestamp) */
      disabledDate: number[];
    };
  };
```

</details>

<details>
<summary>Параметры поля `datetime`</summary>

`datetime` Поле для выбора даты и времени

```ts
/**
 * Поле ввода даты и времени
 * @template Key - Тип ключа поля
 */
export type DateTimeBlockInputField<Key extends string = string> =
  CommonBlockInputField<Key> & {
    type: "datetime";
    typeOptions?: {
      /** Минимальная дата и время (timestamp) */
      min?: number;
      /** Максимальная дата и время (timestamp) */
      max?: number;
      /** Отключенные даты (timestamp) */
      disabledDate: number[];
    };
  };
```

</details>

<details>
<summary>Параметры поля `select`</summary>

`select` Поле для выбора одного значения из списка

```ts
/**
 * Выпадающий список (одиночный выбор)
 * @template Key - Тип ключа поля
 */
export type SelectBlockInputField<Key extends string = string> =
  CommonBlockInputField<Key> & {
    type: "select";
    /** Опции для выбора */
    options: SelectOptions;
  };
```

</details>

<details>
<summary>Параметры поля `multiSelect`</summary>

`multiSelect` Поле для выбора нескольких значений из списка

```ts
/**
 * Выпадающий список (множественный выбор)
 * @template Key - Тип ключа поля
 */
export type MultiSelectBlockInputField<Key extends string = string> =
  CommonBlockInputField<Key> & {
    type: "multiselect";
    /** Опции для выбора */
    options: SelectOptions;
    typeOptions?: {
      /** Минимальное количество выбранных элементов */
      minItems?: number;
      /** Максимальное количество выбранных элементов */
      maxItems?: number;
      /** Разделитель для строкового представления */
      delimiter?: string;
    };
  };
```

</details>

<details>
<summary>Параметры поля `keyValue`</summary>

`keyValue` Поле для ввода пар "ключ-значение", полезно для создания параметров запроса

```ts
/**
 * Поле ввода пар ключ-значение
 * @template Key - Тип ключа поля
 */
export type KeyValueBlockInputField<Key extends string = string> =
  CommonBlockInputField<Key> & {
    type: "keyValue";
    typeOptions?: {
      /** Можно ли сортировать элементы */
      sortable?: boolean;
    };
  };
```

</details>

<details>
<summary>Параметры поля `boolean`</summary>

`boolean` Булево поле,предстваляет из себя свитчер вкл/выкл

```ts
/**
 * Логическое поле (чекбокс)
 * @template Key - Тип ключа поля
 */
export type BooleanBlockInputField<Key extends string = string> =
  CommonBlockInputField<Key> & {
    type: "boolean";
  };
```

</details>

<details>
<summary>Параметры поля `stream`</summary>

`stream` Поле принимающее ArrayBuffer для файлов

```ts
type StreamBlockInputField<Key extends string = string> =
  CommonBlockInputField<Key> & {
    type: "stream";
  };
```

</details>

<details>
<summary>Параметры поля `group`</summary>

`group` Поле,позволяющее группировать другие поля.

```ts
type GroupBlockInputField<Key extends string = string> =
  CommonBlockInputField<Key> & {
    type: "group";
    properties: (
      | TextBlockInputField
      | NumberBlockInputField
      | SelectBlockInputField
      | KeyValueBlockInputField
      | BooleanBlockInputField
      | CodeBlockInputField
      | DateBlockInputField
      | DateTimeBlockInputField
      | StreamBlockInputField
      | IntegerBlockInputField
    )[];
  };
```

</details>

<details>
<summary>Параметры поля `array`</summary>

`array` Поле со списком эленментов

```ts
type ArrayBlockInputField<Key extends string = string> =
  CommonBlockInputField<Key> & {
    type: "array";
    properties: (
      | TextBlockInputField
      | NumberBlockInputField
      | SelectBlockInputField
      | KeyValueBlockInputField
      | BooleanBlockInputField
      | CodeBlockInputField
      | DateBlockInputField
      | DateTimeBlockInputField
      | StreamBlockInputField
      | IntegerBlockInputField
    )[];
  };
```

</details>

<details>
<summary>Параметры поля `code`</summary>

`code` Текстовое поле для ввода строковых значений с подсветкой диалекта

```ts
type SqlDialect =
  | "cassandra"
  | "clickhouse"
  | "greenplum"
  | "hive"
  | "mariadb"
  | "mssql"
  | "mysql"
  | "plsql"
  | "postgresql"
  | "sqlite"
  | "standard";

type CodeBlockEditorType =
  | "python"
  | "sql"
  | "html"
  | "yaml"
  | "javascript"
  | "json";

type CodeBlockInputField<Key extends string = string> =
  CommonBlockInputField<Key> & {
    type: "code";
  } & (
      | {
          editor: "sql";
          sqlDialect?: SqlDialect;
          default?: string;
          typeOptions?: {
            minLength?: number;
            maxLength?: number;
            pattern?: string;
            errorMessage?: string;
          };
        }
      | {
          editor: Exclude<CodeBlockEditorType, "sql">;
          default?: string;
          typeOptions?: {
            minLength?: number;
            maxLength?: number;
            pattern?: string;
            errorMessage?: string;
          };
        }
    );
```

</details>

## Поля для подключений

| Атрибут     | Тип      | Описание                                                                 |
|--------------|----------|--------------------------------------------------------------------------|
| `text`        | string   | Однострочное текстовое поле для ввода строковых значений |
| `password`       | string   | Текстовое поле,которое скрывается после сохранения                                                               |
| `number`      | number  | Числовое поле                                                     |
| `button`   | any  | Поле для запросов во внешнюю систему               |

<details>
<summary>Параметры поля `text`</summary>

`text` Однострочное текстовое поле для ввода строковых значений

```ts
type CommonConnectionInputField<Key = string> = {
    key: Key;
    type: "text" | "password" | "number" | "button";
    label: string;
};
```

</details>

<details>
<summary>Параметры поля `password`</summary>

`password` Позволяет вводить секретную информацию (например, пароль), которая будет скрыта при вводе

```ts
{
  type: "password",
  key: "apikey",
  label: "Ключ API",
  required: true,
  hint: "Ищите в настройках платформы",
}
```

</details>


<details>
<summary>Параметры поля `button`</summary>

`button` кнопка для выполнения запросов в API

```ts
type ButtonInputFieldConnection<AuthData extends AnyRecord, AdditionalAuthData extends AnyRecord = {}> = CommonConnectionInputField<string> & {
    type: "button";
    typeOptions: {
        redirect?: (service: ExecuteService, bundle: ConnectionExecuteBundle<AuthData>) => void;
        saveFields?: (service: ExecuteService, bundle: ConnectionExecuteBundle<AuthData>) => Partial<AdditionalAuthData>;
        message?: (service: ExecuteService, bundle: ConnectionExecuteBundle<AuthData & AdditionalAuthData>) => void | string;
    };
};
```

</details>
