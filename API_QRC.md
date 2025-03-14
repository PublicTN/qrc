# Подключение к PIM через API QRC.ai версия v1

# Общая информация

Откружение для подключения:

- `prod` окружение [https://qrc.ai](https://qrc.ai/)

⚠️ Доступ к окружению осуществляется по единому токену.

⚠️ Ограничение по пропускной способности: 2 запроса в сек.

# Получение токена

Для получения токена необходимо ответственному за ваш продукт менеджеру запросить токен по почте [pim@tn.ru](mailto:pim@tn.ru)

В запросе нужно указать:

- Название ресурса, который будет подключаться к API QRC
- Описание ресурса

# Получение информации в формате json из API QRC

## Swagger

```json
{
    "swagger": "2.0",
    "info": {
        "title": "Product API",
        "contact": {},
        "version": "0.0.1"
    },
    "host": "https://qrc.ai",
    "basePath": "/api",
    "paths": {
        "/v1/product/updated": {
            "post": {
                "security": [
                    {
                        "ApiKeyAuth": []
                    }
                ],
                "description": "ApiKeyAuth required (Authorization: Bearer \u003ctoken\u003e)",
                "produces": [
                    "application/json"
                ],
                "tags": [
                    "product info"
                ],
                "summary": "Updated product retrieve (time in RFC3339 or unix timestamp)",
                "parameters": [
                    {
                        "type": "string",
                        "description": "from",
                        "name": "from",
                        "in": "query",
                        "required": true
                    },
                    {
                        "type": "string",
                        "description": "to",
                        "name": "to",
                        "in": "query"
                    },
                    {
                        "description": "EKN List",
                        "name": "req",
                        "in": "body",
                        "required": true,
                        "schema": {
                            "type": "array",
                            "items": {
                                "type": "string"
                            }
                        }
                    }
                ],
                "responses": {
                    "200": {
                        "description": "OK",
                        "schema": {
                            "type": "array",
                            "items": {
                                "type": "string"
                            }
                        }
                    },
                    "404": {
                        "description": "Not Found",
                        "schema": {
                            "$ref": "#/definitions/helpers.ErrorMsg"
                        }
                    },
                    "500": {
                        "description": "Internal Server Error",
                        "schema": {
                            "$ref": "#/definitions/helpers.ErrorMsg"
                        }
                    }
                }
            }
        },
        "/v1/product/{ekn}": {
            "get": {
                "security": [
                    {
                        "ApiKeyAuth": []
                    }
                ],
                "description": "ApiKeyAuth required (Authorization: Bearer \u003ctoken\u003e)",
                "produces": [
                    "application/json"
                ],
                "tags": [
                    "product info"
                ],
                "summary": "Product retrieve",
                "parameters": [
                    {
                        "type": "string",
                        "description": "ekn",
                        "name": "ekn",
                        "in": "path",
                        "required": true
                    },
                    {
                        "type": "string",
                        "description": "локаль по умолчанию ru_RU",
                        "name": "locale",
                        "in": "query"
                    },
                    {
                        "type": "string",
                        "description": "список атрибутов которые необходимо вернуть разделенных запятой (по умолчанию все атрибуты)",
                        "name": "attributes",
                        "in": "query"
                    }
                ],
                "responses": {
                    "200": {
                        "description": "OK",
                        "schema": {
                            "$ref": "#/definitions/product.GetResponseV1"
                        }
                    },
                    "404": {
                        "description": "Not Found",
                        "schema": {
                            "$ref": "#/definitions/helpers.ErrorMsg"
                        }
                    },
                    "500": {
                        "description": "Internal Server Error",
                        "schema": {
                            "$ref": "#/definitions/helpers.ErrorMsg"
                        }
                    }
                }
            }
        }
    },
    "definitions": {
        "helpers.ErrorMsg": {
            "type": "object",
            "properties": {
                "message": {
                    "type": "string"
                }
            }
        },
        
        "product.Attribute": {
            "type": "object",
            "properties": {
                "attribute_type": {
                    "type": "string"
                },
                "data": {},
                "label": {
                    "type": "string"
                },
                "value": {}
            }
        },
        "product.Attributes": {
            "type": "object",
            "additionalProperties": {
                "$ref": "#/definitions/product.Attribute"
            }
        },
        
    },
    "securityDefinitions": {
        "ApiKeyAuth": {
            "type": "apiKey",
            "name": "Authorization.",
            "in": "header"
        }
    }
}
```

## Запрос для доступа к данным продукта по ЕКН

### Формат запроса

```jsx
curl -H "Authorization: Bearer `Ваш_токен`" https://qrcai.tndev.ru/api/v1/product/`ЕКН`
```

### Пример запроса

Пример запроса для ЕКН 19:

```jsx
curl -H "Authorization: Bearer Токен" https://qrcai.tndev.ru/api/v1/product/19
```

### Структура данных в JSON

```json
{
  "id": "string",
  "groups": {
    "groupName": {
      "attribute": {
        "attribute_type": "string",
        "data": "string",
        "label": "string",
        "value": {
					"string"
        },
  
  },
  "locale": "string",
  "locales": [
    "string"
  ],
  "categories": [
    "string"
  ]
}
```

где:

`id`- номер sku (ЕКН)

`groups`- ветка групп данных sku

`groupName`- код группы атрибутов sku

`attribute`- код атрибута sku с данными. Легенду см. [Атрибуты и группы атрибутов карточек ЕКН и систем](https://docs.google.com/spreadsheets/d/1KGepTSnfsn63dR7YDWIkwEm2Wg_zrquTOSo1jYOZKqA/edit?usp=sharing) 

`attribute_type`- тип атрибута (текст, число, ссылка на справочник и прочие)

`data`- указываются опции атрибута:
 - если атрибут ссылается на справочники или медиа, то здесь указывается перечень кодов записей справочников и карточек медиа
- если атрибут метрический, то указывается единица измерения и значение а данной единице измерения

`label`- название атрибута на языке локали (например, для локали **ru**_RU язык русский - обозначается прописными символами ru)

`value`- значение атрибута

`locale`- название локали, в которой предоставлены данные в формате `код языка_код страны Альфа2` , например ru_RU

`locales`- список локалей, в рамках которых доступны данные по sku

`categories`- перечень кодов категорий из ПИМ, в которых находятся sku

<aside>
💡 В случае, если атрибут ссылается на справочники или медиа, то в `value`указываются атрибуты справочников и карточке медиа, а также их значения.

</aside>

## Запрос для доступа к данным продукта по ЕКН с фильтром атрибутам

### Формат запроса

```jsx
curl -H "Authorization: Bearer `Ваш_токен`" https://qrcai.tndev.ru/api/v1/product/`[ЕКН](https://qrcai.tndev.ru/api/v1/product/ЕКН?locale=Локаль&attributes=Атрибут)`?attributes=`Атрибут_1`, `Атрибут_2`, …, `Атрибут_N`
```

### Пример запроса

Пример запроса для ЕКН 19 для атрибутов tds, atr_qual_manag_cert:

```jsx
curl -H "Authorization: Bearer Токен" https://qrcai.tndev.ru/api/v1/product/19?attributes=tds, atr_qual_manag_cert
```

## Запрос для доступа к данным продукта по ЕКН с фильтром по локали и атрибутам

### Формат запроса

```jsx
curl -H "Authorization: Bearer `Ваш_токен`" https://qrcai.tndev.ru/api/v1/product/`[ЕКН](https://qrcai.tndev.ru/api/v1/product/ЕКН?locale=Локаль&attributes=Атрибут)`?locale=`[Локаль](https://qrcai.tndev.ru/api/v1/product/ЕКН?locale=Локаль&attributes=Атрибут)`&attributes=`Атрибут_1`, `Атрибут_2`, …, `Атрибут_N`
```

### Пример запроса

Пример запроса для ЕКН 19 в локали ru_RU для атрибутов tds, atr_qual_manag_cert:

```jsx
curl -H "Authorization: Bearer Токен" https://qrcai.tndev.ru/api/v1/product/19?locale=ru_RU&attributes=tds, atr_qual_manag_cert
```

## Запрос для доступа к сертификатам о соответствии и документации продукта по ЕКН

<aside>
💡 Данный запрос возвращает такие документы как:

- Технические листы на системы (согласованные) astf_tds_sys_old
- Технические листы на марку (согласованные) tds
- Технические свидетельства (или отказные письма) req_tech_cert
- Технические одобрения tech_appr
- Технологические карты technological_map
- Страховые сертификаты insur_cert
- Стандарты организации (СТО) на проектирование и применение sto_design_usage
- Сертификаты о соответствии (или отказные письма) certificates  Сертификаты менеджмента качества astf_qual_manag_cert
- Свидетельства о государственной регистрации (СГР) (или отказные письма) stat_reg_cert
- Санитарно-гигиенические заключения (СЭЗ) (или отказные письма) san_hyg_concl
- Руководства по проектированию, монтажу и эксплуатации manual_design_installation_exploitation
Рекламные брошюры и каталоги promotional_material
- Протоколы испытаний сертификационные vol_cert_test_mond
- Пожарные сертификаты и декларации (или отказные письма) firecertificate
- Паспорта безопасности для химической продукции safety_ds_chem_prod
- Лист безопасности safety_data_sheet
- Информационные письма inform_letters
- Инструкции по монтажу installation_instructions
- Заключения на системы conclusions_on_systems
- Заключения на продукцию conclusions_on_products
- Другие документы other_docs
- Декларации о соответствии (или отказные письма) decl_conf_req
- Акустические сертификаты (или отказные письма) acoustic_cert
- Гарантийные сертификаты warranty
- Декларации характеристик dop
- Презентации маркетинговые presentations
</aside>

### Формат запроса

```jsx
curl -H "Authorization: Bearer `Ваш_токен`" https://qrcai.tndev.ru/api/v1/product/`[ЕКН](https://qrcai.tndev.ru/api/v1/product/ЕКН?locale=Локаль&attributes=Атрибут)`/certificates
```

### Пример запроса

Пример запроса для ЕКН 19:

```jsx
curl -H "Authorization: Bearer Токен" https://qrcai.tndev.ru/api/v1/product/19/certificates
```

## Определение списка обновленных ЕКН

### Формат запроса

```jsx
curl https://qrcai.tndev.ru/api/v1/product/updated?from=`YYYY`-`MM`-`DD`T`HH`:`mm`:`ss`.`ffK`&to=`YYYY`-`MM`-`DD`T`HH`:`mm`:`ss`.`ffK`\
-H "Authorization: Bearer `Ваш_токен`"
-H 'Content-Type: application/json' \
--data-raw ' [
"`ЕКН`"
]
```

### Пример запроса

Пример запроса для ЕКН 19, 602201, 606878 с 10:00 01.03.2023 по 20:00 12.04.2023:

```jsx
curl https://qrcai.tndev.ru/api/v1/product/updated?from=2023-03-01T10:00:00Z&to=2023-04-12T20:00:00Z \
-H "Authorization: Bearer Токен"
-H 'Content-Type: application/json' \
--data-raw ' [
        "19",
        "602201",
        "606878"
]
```

# Архитектура данных PIM

Схема архитектуры PIM:
https://i.imgur.com/zOppFSB.png

## Типы атрибутов

### Identifier

**Код типа атрибута в API:**  
`id`

**Описание:**  
Код для идентификации продукта. В PIM может быть только один атрибут идентификатора, этот код должен быть уникальным для каждого продукта. Этот атрибут обязателен для создания продуктов и не может быть удален.

**Пример данных:**  
```json
"id": "19"
```

---

### Text

**Код типа атрибута в API:**  
`pim_catalog_text`

**Описание:**  
Однострочное текстовое поле, которое может содержать до 255 символов, обычно используется для названия продукта.

**Пример данных:**  
```json
"atr_ship_point_unit_1c_cb": {
    "attribute_type": "pim_catalog_text",
    "label": "Единица измерения мест отгрузки из 1С ЦБ",
    "value": "поддон"
}
```

---

### Text area

**Код типа атрибута в API:**  
`pim_catalog_textarea`

**Описание:**  
Многострочное текстовое поле, которое можно использовать для описания товара.

**Пример данных:**
```json
"upakovka": {
    "attribute_type": "pim_catalog_textarea",
    "label": "Упаковка (описание)",
    "value": "Упаковка поддона с рулонами – термоусадочный белый пакет."
}
```

---

### Simple select

**Код типа атрибута в API:**  
`pim_catalog_simpleselect`

**Описание:**  
Список с одним вариантом выбора, содержащий пользовательские опции. Среди доступных вариантов можно выбрать только одно значение.

**Пример данных:**
```json
"atr_type_bitum_binder": {
    "attribute_type": "pim_catalog_simpleselect",
    "data": "bitum",
    "label": "Тип битумосодержащего вяжущего",
    "value": "Битумное"
}
```

---

### Multi select

**Код типа атрибута в API:**  
`pim_catalog_multiselect`

**Описание:**  
Список с несколькими вариантами выбора, содержащий пользовательские опции. Среди доступных вариантов можно выбрать более одного значения.

**Пример данных:**
```json
"atr_material_install_method": {
    "attribute_type": "pim_catalog_multiselect",
    "data": [
        "fusion"
    ],
    "label": "Способ монтажа материала",
    "value": {
        "fusion": "Наплавление"
    }
}
```

---

### Yes/No

**Код типа атрибута в API:**  
`pim_catalog_boolean`

**Описание:**  
Атрибут-переключатель для двух значений: Да, Нет.

**Пример данных:**
```json
"arch_prod": {
    "attribute_type": "pim_catalog_boolean",
    "label": "Архивный товар",
    "value": false
}
```

---

### Date *(Не используется)*

**Описание:**  
Поле даты. PIM отобразит календарь для выбора даты, который включает день, месяц и год.

---

### Number

**Код типа атрибута в API:**  
`pim_catalog_number`

**Описание:**  
Однострочное поле, которое может содержать только числа.

**Пример данных:**
```json
"atr_elongation_along_max": {
    "attribute_type": "pim_catalog_number",
    "label": "Максимальная сила растяжения вдоль, Н/50 мм",
    "value": "500"
}
```

---

### Measurement

**Код типа атрибута в API:**  
`pim_catalog_metric`

**Описание:**  
Однострочное поле, состоящее из поля с значением и поля с единицей измерения. Оно позволяет автоматически преобразовывать значения измерений в другие единицы, чтобы соответствовать вашим потребностям экспорта.

**Пример данных:**
```json
"atr_width_plus_minus_3_percent": {
    "attribute_type": "pim_catalog_metric",
    "label": "Ширина, ±3%",
    "value": {
        "amount": "1",
        "unit": "METER"
    }
}
```

---

### Price *(Не используется)*

**Описание:**  
Атрибут цены со значениями для каждой валюты. Отображаемые значения будут зависеть от валют, включенных в PIM.

---

### Image *(Не используется)*

**Описание:**  
Позволяет добавить изображение в атрибут (допустимые расширения: gif, jfif, jif, jpeg, jpg, pdf, png, psd, tif, tiff).

---

### File *(Не используется)*

**Описание:**  
Позволяет добавить файл в атрибут (допустимые расширения: csv, doc, docx, mp3, pdf).

---

### Asset collection

**Код типа атрибута в API:**  
`pim_catalog_asset_collection`

**Описание:**  
Расширенный тип атрибутов для управления несколькими медиа-ресурсами, такими как изображения, pdf-файлы, видео с Youtube и т. д.

**Пример данных:**
```json
"firecertificate": {
    "attribute_type": "pim_catalog_asset_collection",
    "data": [
        "ast_ru_d_ru_ra01_v_02034_24",
        "ast_ru_d_ru_pb68_v_00258_19",
        "ast_c_ru_pb25_v_04857",
        "ast_ru_d_ru_pb68_v_01106_20",
        "ast_ru_d_ru_ra01_v_00386_19",
        "ast_ru_d_ru_ra01_v_00456_19",
        "ast_d_ru_pb25_v_00809"
    ],
    "label": "Пожарные сертификаты и декларации (или отказные письма)"
}
```

---

### Reference entity simple link

**Код типа атрибута в API:**  
`akeneo_reference_entity`

**Описание:**  
Позволяет связать атрибут карточки продукта с справочными данными, содержащими записи с дополнительными атрибутами (текст, изображения, мультивыбор, ссылки на другие справочники и т. д.). При этом выбирается одна запись из справочника.

**Пример данных:**
```json
"atr_manufacturer_state": {
    "attribute_type": "akeneo_reference_entity",
    "data": "russia",
    "label": "Страна происхождения"
}
```

---

### Reference entity multiple links

**Код типа атрибута в API:**  
`akeneo_reference_entity_collection`

**Описание:**  
То же самое, что и `akeneo_reference_entity`, но позволяет выбирать несколько записей из справочника для значения атрибута.

**Пример данных:**
```json
"atr_construction_type": {
    "attribute_type": "akeneo_reference_entity_collection",
    "data": [
        "unused_flat_roof",
        "usable_flat_roof"
    ],
    "label": "Тип конструкции"
}
```

---

### Reference data simple select *(Не используется)*

**Описание:**  
Позволяет управлять любыми данными, имеющими собственные свойства, в виде единственного выбора.

---

### Reference data multi select *(Не используется)*

**Описание:**  
Позволяет управлять любыми данными, имеющими собственные свойства, в виде многовариантного выбора.

## Атрибуты и группы атрибутов карточек ЕКН и систем

Таблица с перечнем атрибутов продуктов и систем в разрезе семейств:

[https://docs.google.com/spreadsheets/d/1KGepTSnfsn63dR7YDWIkwEm2Wg_zrquTOSo1jYOZKqA/edit?usp=sharing](https://docs.google.com/spreadsheets/d/1KGepTSnfsn63dR7YDWIkwEm2Wg_zrquTOSo1jYOZKqA/edit?usp=sharing)

В таблице приведены данные:

- Коды атрибутов PIM (столбец A)
- Наименования атрибутов (столбец B)
- Типы атрибутов (столбец C)
- Группа атрибутов (столбец D)

<aside>
💡 Коды и описания групп атрибутов можно найти на вкладке `Группы атрибутов`

</aside>

- Описание атрибута (столбец G) - опционально
- Ссылка на справочники или карточки медиа (столбец K) - здесь можно найти атрибуты карточек медиа и атрибуты справочников
- Обязательность атрибута для семейств (типов продукции и систем):
    - 0 = атрибут не используется в семействе продукции;
    - 1 = атрибут есть в семействе, но не обязательный;
    - 2 = атрибут используется в семействе и является обязательным для заполнения.

## Атрибуты справочников

Атрибуты, которые ссылаются на справочники имеют в столбце К Опции атрибутов ссылку на таблицы с атрибутами конкретного справочника приведены в таблицах по ссылке:

[https://drive.google.com/drive/folders/1Os_LpvxUNXb5_KDzjfLtvPdiy-yZD-EL?usp=sharing](https://drive.google.com/drive/folders/1Os_LpvxUNXb5_KDzjfLtvPdiy-yZD-EL?usp=sharing)

## Атрибуты карточек медиа (ассетов)

Атрибуты, которые ссылаются на ассеты (хранилища медиаданных) имеют в столбце К Опции атрибутов ссылку на таблицы с атрибутами конкретного типа ассета приведены в таблицах по ссылке:

[https://drive.google.com/drive/folders/1nuI6CB-JaWttEZf4BTzw2rzytLTQfj64?usp=sharing](https://drive.google.com/drive/folders/1nuI6CB-JaWttEZf4BTzw2rzytLTQfj64?usp=sharing)
