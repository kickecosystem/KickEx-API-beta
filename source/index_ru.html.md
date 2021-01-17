---
title: KickEx - описание API

language_tabs: # must be one of https://git.io/vQNgJ
  - shell
  - ruby
  - python
  - javascript

toc_footers:
  - <a href='#'>Sign Up for a Developer Key</a>
  - <a href='https://github.com/slatedocs/slate'>Documentation Powered by Slate</a>
  - <a href='index.html'>API documentation in English</a>

includes:
  - errors

search: true
---

# Введение

API для внешней интеграции с биржей KickEX.

# Authentication

Для аутентификации клиента и контроля целостности принимаемых сообщений к заголовкам запроса следует добавить следующие значения:

* KICK-API-KEY - сначала нужно <a href="https://id.kickex.com/settings/api">создать новый API key</a>, а затем передавать в запросах значение атрибута **key**.
* KICK-API-PASS - парольная фраза ключа API
* KICK-API-TIMESTAMP - время формирования запроса в формате TIMESTAMP (unix timestamp, в секундах)
* KICK-SIGNATURE - цифровой отпечаток запроса
* User-Agent: любая строка
* Content-Type: application/json

```
base64_encode(hash_hmac("sha512", $api_secret,
	hash_hmac("sha512", $body, 
		hash_hmac("sha512", $request_path, 
			hash_hmac("sha512", $method, $timestamp, true),
		true),
	true),
true));
```

Цифровой отпечаток запроса предназначен для контроля целостности получаемых сервером данных. В формировании цифрового отпечатка используется **API Secret**, который не передается от клиента к серверу в процессе работы с REST API.
Цифровой отпечаток запроса создается на основе:

- времени (timestamp) формирования запроса;
- названия метода запроса (e.g. GET);
- пути запроса (e.g. /api/v1/deposit-addresses);
- тела запроса (e.g. period=5min&pairName=BTC/USDT&startTime=22814882323);


# Market
Данный раздел описывает набор методов, передающих информацию о парах и криптовалютах на платформе.
<aside class="notice">
Методы раздела <b>Market</b> доступны без авторизации.
</aside>

## Pairs

```shell
curl "https://gate.kickex.com/api/v1/market/pairs?type=market"
```

> Команда выше вернёт структуру следующего вида:

```json
{
	"pairs": [
		{
			"pairName": "BTC/USDT",
			"baseCurrency": "BTC",
			"quoteCurrencу": "USDT",
			"baseMinSIze": "0.0001",
			"quoteMinSize": "0.0001",
			"priceDecimal": 12,
			"amountDecimal": 8,
			"state": 0
		},
		{
			"pairName": "BTC/KICK",
			"baseCurrency": "BTC",
			"quoteCurrencу": "KICK",
			"baseMinSIze": "0.0001",
			"quoteMinSize": "0.0001",
			"priceDecimal": 12,
			"amountDecimal": 8,
			"state": 0
		}
	]
}
```

### HTTP Запрос

`GET https://gate.kickex.com/api/v1/market/pairs?type=market`

### Параметры URL 

Параметр | Тип | Обязательный | Описание
--------- | ----------- | ----------- | -----------
type | string | Да | Единственное корректное значение - 'market'

### Параметры ответа
Параметр | Тип | Обязательный | Описание
--------- | ----------- | ----------- | -----------
pairName | string | Да | наименование криптовалютной пары *(пример: BTC/USDT)*
baseCurrency | string | Да | наименование базовой криптовалюты *(пример: BTC)*
quoteCurrencу | string | Да | наименование котируемой криптовалюты *(пример: USDT)*
baseMinSIze | string | Да | минимальное кол-во базовой криптовалюты для выставления ордера
quoteMinSize | string | Да | минимальное количество котируемой криптовалюты для выставления ордера
priceDecimal | Number | Да | максимальное количество знаков после точки для пары при указании цены
amountDecimal | Number | Да | максимальное количество знаков после точки для пары при указании количества
state | integer | Да | Индикатор, показывающий, доступна ли торговля по паре:<br/>4 – доступна <br/>1,2,3 – недоступна

## All Tickers

```shell
curl "https://gate.kickex.com/api/v1/market/allTickers"
```

> Команда выше вернёт структуру следующего вида:

```json
{
	"tickers": [
		{
			"timestamp": 1588024908,
			"pairName": "BTC/USDT",
			"bestBid": "1234.1231",
			"bestAsk": "1245.2847",
			"changePrice": "21.2349",
			"highestPrice": "1251.9328",
			"lowestPrice": "1197.3821",
			"baseVol": "198273982",
			"quoteVol": "2348792",
			"lastPrice": "1246.0231",
			"priceDecimal": "4",
			"lastVolume": "10000",
			"bestAskVolume": "124990",
			"bestBidVolume": "95830"
		},
		{
			"timestamp": 1588024908,
			"pairName": "BTC/ETH",
			"bestBid": "1234.1231",
			"bestAsk": "1245.2847",
			"changePrice": "21.2349",
			"highestPrice": "1251.9328",
			"lowestPrice": "1197.3821",
			"baseVol": "198273982",
			"quoteVol": "2348792",
			"lastPrice": "1246.0231",
			"priceDecimal": "4",
			"lastVolume": "10000",
			"bestAskVolume": "124990",
			"bestBidVolume": "95830"
		}
	]
}
```

### HTTP Запрос

`GET https://gate.kickex.com/api/v1/market/allTickers`

### Параметры URL

*Отсутствуют.*

### Параметры ответа
Параметр | Тип | Обязательный | Описание
--------- | ----------- | ----------- | -----------
timestamp | timestamp | Да | Время, на которое отображены данные в формате timestamp в мс
pairName | string | Да | наименование криптовалютной пары (В формате BTC/USDT)
bestBid | string | Да | лучший бид на текущий момент
bestAsk | string | Да | лучший аск на текущий момент
changePrice | string | Да | Изменение цены относительно последней сделки в процентном отношении (+1,5%, например)
highestPrice | string | Да | Наивысшая стоимость в паре за 24 часа
lowestPrice | string | Да | Наименьшая стоимость в паре за 24 часа
baseVol | string | Да | Объем торгов за 24 часа в базовой валюте пары (фаткически -кол-во)
quoteVol | string | Да | Объем торгов за 24 часа в котируемой валюте пары
lastPrice | string | Да | Последняя цена сделки в паре
priceDecimal | string | Да | Количество знаков после точки для указания цены
lastVolume | string | Да | последний объем сделки
bestAskVolume | string | Да | количество криптовалюты по цене лучшего аска
bestBidVolume | string | Да | количество криптовалюты по цене лучшего бида


## 24hrs stats

Метод возвращает данные по криптовалютной паре за последние 24 часа.

```shell
curl "https://gate.kickex.com/api/v1/market/stats24?pairName=BTC/USDT"
```

> Команда выше вернёт структуру следующего вида:

```json
{
	"pairName": "BTC/USDT",
	"high24": "3425.0092",
	"low24": "3389.1294",
	"amountVol": "91572919",
	"baseVol": "27020.6311",
	"lastPrice": "3421.7623",
	"bestBid": "3420.4223",
	"bestAsk": "3401.7623",
	"averagePrice": "3407.3719",
	"priceChange": "36.0925",
	"timestamp": 1588024908,
}
```

### HTTP Запрос

`GET https://gate.kickex.com/api/v1/market/stats24?pairName=BTC/USDT`

### Параметры URL 

Параметр | Тип | Обязательный | Описание
--------- | ----------- | ----------- | -----------
pairName | string | Да | Имя криптовалютной пары *(пример: BTC/USDT)*

### Параметры ответа

Параметр | Тип | Обязательный | Описание
--------- | ----------- | ----------- | -----------
pairName | string | Да | наименование криптовалютной пары (В формате BTC/USDT)
high24 | string | Да | Наивысшая цена за 24 часа
low24 | string | Да | наименьшая цена за 24 чкаса
amountVol | string | Да | Количество криптовалюты, задействованной в сделках за 24 часа
baseVol | string | Да | Объем торгов за 24 часа выраженный в базовой криптовалюте
lastPrice | string | Да | последняя цена сделки в паре
bestBid | string | Да | лучшая цена на покупку (на момент запроса)
bestAsk | string | Да | лучшая цена на продажу (на момент запроса)
averagePrice | string | Да | средняя цена в сделках за 24 часа
priceChange | string | Да | Изменение цены за 24 часа
timestamp | timestamp | Да | Время, на которое действительна полученная информация в миллисекундах


## Currency

В случае, если параметр в запросе не отправлен, предоставляется информация по всем криптовалютам платформы.

```shell
curl "https://gate.kickex.com/api/v1/currencies?currency=ETH"
```

> Команда выше вернёт структуру следующего вида:

```json
{
	"isoCode": "KICK",
	"currencyName": "KICK",
	"fullName": "Kick Token",
	"decimal": 8,
	"minWithdawal": "1",
	"minFeeWithrawal": "0.1",
	"isWithdrawEnable": true,
	"isDepositEnable": true,
	"isExchangeEnable": true,
	"state": 4,
	"convertPath": [
				{
					"BTC/USDT": true
				},
				{
					"ETH/BTC": false
				}
			]
}
```

> Если валюта опущена в запросе, то следующего:

```json
{
	"currencies": [
		{
			"isoCode": "ETH",
			"currencyName": "ETH",
			"fullName": "Ethereum",
			"decimal": 8,
			"minWithdawal": "1",
			"minFeeWithrawal": "0.1",
			"isWithdrawEnable": true,
			"isDepositEnable": true,
			"isExchangeEnable": true,
			"state": 4,
			"convertPath": [
				{
					"BTC/USDT": true
				},
				{
					"ETH/BTC": false
				}
			]
		},
		{
			"isoCode": "KICK",
			"currencyName": "KICK",
			"fullName": "Kick Token",
			"decimal": 8,
			"minWithdawal": "1",
			"minFeeWithrawal": "0.1",
			"isWithdrawEnable": true,
			"isDepositEnable": false,
			"isExchangeEnable": true,
			"state": 4,
			"convertPath": [
				{
					"BTC/USDT": true
				},
				{
					"ETH/BTC": false
				}
			]
		}
	]
}
```

### HTTP Запрос

`GET https://gate.kickex.com/api/v1/currencies?currency=ETH`

### Параметры URL 

Параметр | Тип | Обязательный | Описание
--------- | ----------- | ----------- | -----------
currency | string | Нет | Краткое наименование криптовалюты (токена) *(пример: BTC)*

### Параметры ответа

Параметр | Тип | Обязательный | Описание
--------- | ----------- | ----------- | -----------
isoCode | string | Нет | краткое наименование криптовалюты по ISO4217
currencyName | string | Да | краткое наименование криптовалюты
fullName | string | Да | полное наименование криптовалюты
decimal | number | Да | Количество знаков после точки для этой криптовалюты
minWithdawal | string | Нет | минимальное количество криптовалюты, которое разрешено выводить с платформы
minFeeWithrawal | string | Нет | минимальный размер комиссии взимаемый за вывод средств
isWithdrawEnable | boolean | Нет | Индикатор, показывающий, доступен ли вывод (0 – доступен, 1 – недоступен)
isDepositEnable | boolean | Нет | Индикатор, показывающий, доступно ли пополнение (0 – доступно, 1 – недоступно)
isExchangeEnable | boolean | Нет | Индикатор, показывающий, доступен ли обмен на платформе этой криптовалюты (true – доступно, 1 – недоступно)
state | integer | Нет | Индикатор, показывающий, доступна ли торговля этой криптовалюты на бирже <br/>4 – доступна, <br/>1,2,3 – недоступна)
convertPath | array| Нет | Массив торговых пар. Если пустой, то конвертация не требуется. <br/>Если true, то следует умножать на курс из торговой пары. <br/>Если false, то следует делить на курс из торговой пары по модулю. <br/>```[{"BTC/USDT": true},{"ETH/BTC": false}]```

## Minibars

Запрос истории сделок по конкретной паре за последние 24 часа.

```shell
curl "https://gate.kickex.com/api/v1/minibars"
```

> Команда выше вернёт структуру следующего вида:

```json
{
	"minibars": [
		{
			"pairName": "KICK/BTC",
			"bars": [
				[
					1574168400,
					0.0010096
				],
				[
					1574172000,
					0.0010004
				]
			]
		},
		{
			"pairName": "ETH/BTC",
			"bars": [
				[
					1574168400,
					0.0010096
				],
				[
					1574172000,
					0.0010004
				]
			]
		}
	]
}
```


### HTTP Запрос

`GET https://gate.kickex.com/api/v1/minibars`

Возвращает почасовые цены для всех пар за последние сутки.

### Параметры URL 

*Отсутствуют.*

### Параметры ответа

Параметр | Тип | Обязательный | Описание
--------- | ----------- | ----------- | -----------
pairName | string | Да | Наименование криптовалютной пары, например KICK/BTC
bars | array | Да | свечи в формате <unix timestamp, цена закрытия><br/>{<br/>	"bars": [<br/>		[1574168400, 0.0010096],[1574172000,0.0010004]]<br/>}


## Trades

Запрос истории сделок по конкретной паре за последние 24 часа.

```shell
curl "https://gate.kickex.com/api/v1/market/trades?pairName=BTC/USDT&type=buy"
```

> Команда выше вернёт структуру следующего вида:

```json
{
	"price": "1124.120937",
	"baseVol": "491082.129100",
	"quoteVol": "436.858805",
	"timestamp": 1588024908,
	"type": "buy"
}
```

### HTTP Запрос

`GET https://gate.kickex.com/api/v1/market/trades?pairName=BTC/USDT&type=buy`

### Параметры URL 

Параметр | Тип | Обязательный | Описание
--------- | ----------- | ----------- | -----------
pairName | string | Да | наименование криптовалютной пары (В формате BTC/USDT)
type | string | Нет | Принимает значения buy или sell, если параметр не передается передаем и то и другое

### Параметры ответа

Параметр | Тип | Обязательный | Описание
--------- | ----------- | ----------- | -----------
price | string | Да | Цена сделки
baseVol | string | Да | Количество базовой криптовалюты, задействованное в сделке
quoteVol | string | Да | Количество котируемой криптовалюты, задействованное в сделке
timestamp | timestamp | Да | время совершения сделки в мс
type | string | Да | Тип сделки покупка/продажа. <br/>Покупка говорит о том, что после выполнения сделки в стакане убралась позиция на аск. <br/>Продажа- убралась из стакана позиция на бид.


## Orderbook

Запрос актуальных биржевых ордеров по валютной паре, может предоставлять как агрегированные, так и неагрегированные данные.

```shell
curl "https://gate.kickex.com/api/v1/market/orderbook?pairName=BTC/USDT&depth=20"
```

> Команда выше вернёт структуру следующего вида:

```json
{
	"timestamp": 1588024908,
	"bids": [
		{
			"amount": "1001.2913",
			"price": "1494.9292"
		}
	],
	"asks": [
		{
			"amount": "10421.1234",
			"price": "1231.9571"
		}
	]
}
```

### HTTP Запрос

`GET https://gate.kickex.com/api/v1/market/orderbook?pairName=BTC/USDT&depth=20`

### Параметры URL 

Параметр | Тип | Обязательный | Описание
--------- | ----------- | ----------- | -----------
pairName | string | Да | Наименование криптовалютной пары, например KICK/BTC
depth | int? | Нет | Глубина стакана <br/>0 или параметр не передан – полный стакан </br>5/10/20/50/100/500 количество позиций спроса и предложения, которые нужно передать (по 5, по 10 и т.д.)
level | int | Нет | 1- Лучшее предложение на покупку и лучшее предложение на продажу <br/>2- Весь стакан, отсортированный по лучшим бидам и аскам <br/>3- Весь стакан не отсортированный по лучшим бидам и аскам

### Параметры ответа

Параметр | Тип | Обязательный | Описание
--------- | ----------- | ----------- | -----------
timestamp | timestamp | Yes | Штамп времени в наносекундах
bids | Array of string | Yes | Содержит в себе цену и количество крпитовалюты
asks | Array of string | Yes | Содержит в себе цену и количество криптовалюты


## Candles

```shell
curl "https://gate.kickex.com/api/v1/market/bars/?period=60&pairName=KICK/USDT&startTime=1607385600000&endTIme=1607471999000"
```

> Команда выше вернёт структуру следующего вида:

```json
{
	"code": 200,
	"message": "success",
	"timestamp": 1588024908,
	"openPrice": "8392.2930",
	"closePrice": "8832.1241",
	"highPrice": "9123.2120",
	"lowPrice": "8392.2930",
	"transactionVolume": "63829012.0012",
	"transactionAmount": "271"
}
```

### HTTP Запрос

В случае, если время не указано, возвращается не более 4096 результатов.

`GET https://gate.kickex.com/api/v1/market/bars/?period=60&pairName=KICK/USDT&startTime=1607385600000&endTIme=1607471999000`

### Параметры URL 

Параметр | Тип | Обязательный | Описание
--------- | ----------- | ----------- | -----------
pairName | string | Да | Наименование криптовалютной пары, например KICK/BTC
period | string | Да | 1/3/10/60 (minutes) 1D 1M 3M
startTIme | timestamp | Нет | время начала в мс
endTime | timestamp | Нет | время окончания в мс

### Параметры ответа

Параметр | Тип | Обязательный | Описание
--------- | ----------- | ----------- | -----------
timestamp | timestamp | Да | начальное время формирования свечи
openPrice | string | Да | цена в момент открытия свечи
closePrice | string | Да | цена в момент закрытия свечи
highPrice | string | Да | наивысшая цена в свече
lowPrice | string | Да | наименьшая цена в свече
transactionVolume | string | Да | объем транзакций в свече
transactionAmount | string | Да | количество транзакций в свече


## Server time

```shell
curl "https://gate.kickex.com/api/v1/serverTime"
```

> Команда выше вернёт структуру следующего вида:

```json
{
	"code": 200,
	"message": "success",
	"time": 1588024908
}
```


### HTTP Запрос

`GET https://gate.kickex.com/api/v1/serverTime`


### Параметры URL 

*Отсутствуют.*

### Параметры ответа

Параметр | Тип | Обязательный | Описание
--------- | ----------- | ----------- | -----------
code | integer | Да | код ответа 200-ок
message | string | Да | success/error
time | timestamp | Да | текущее серверное время в мс


# Trade 

Набор методов для выставления ордеров и получения информации об ордерах.

<aside class="warning">
Для использования данных методов требуется авторизация.
</aside>

## Cancel Order 

Метод отмены ордера.

```shell
curl "https://gate.kickex.com/api/v1/orders/{orderId}"
  -X DELETE
  -H "Authorization: meowmeowmeow"
```

> Команда выше вернёт структуру следующего вида:

```json
{
	"cancelledOrderId": "111111111",
	"comment": ""
}
```

### HTTP Запрос


`DELETE https://gate.kickex.com/api/v1/orders/{orderId}`


### Параметры URL 

*Отсутствуют.*

### Параметры ответа


Параметр | Тип | Обязательный | Описание
--------- | ----------- | ----------- | -----------
cancelledOrderId | string | Да | уникальный идентификатор ордера
comment | string | Нет | ордер отменен / причина ошибки отмены


## Cancel All Orders 

Метод отмены группы ордеров или всех открытых ордеров.

```shell
curl "https://gate.kickex.com/api/v1/orders?pairName=BTC/USDT&orderType=STOP"
  -X DELETE
  -H "Authorization: meowmeowmeow"
```

> Команда выше вернёт структуру следующего вида:

```json
[
	{
		"order_id": 192,
		"error_code": 20002,
		"reason": "something went wrong"
	},
	{
		"order_id": 256,
		"error_code": 20002,
		"reason": "something went wrong"
	}
]
```

### HTTP Запрос

`DELETE https://gate.kickex.com/api/v1/orders?pairName=BTC/USDT&orderType=STOP`

### Параметры URL 

Параметр | Тип | Обязательный | Описание
--------- | ----------- | ----------- | -----------
pairName | string | Нет | Наименование криптовалютной пары в формате KICK/ETH
orderType | string | Нет | тип отмены stop/trade/all, если параметр не задан, по умолчанию работает all

### Параметры ответа

**В ответе содержатся только ордера, которые не удалось отменить**

Параметр | Тип | Обязательный | Описание
--------- | ----------- | ----------- | -----------
orderId | string | Yes | Идентификатор не отменённого ордера
error_code | string | Yes | Код ошибки
reason | string | No | Причина ошибки


## Cancel Orders 

Метод используется для отмены ордеров списком.

```shell
curl "https://gate.kickex.com/api/v1/cancelorders?orders=123456,14589655,12563369"
  -X DELETE
  -H "Authorization: meowmeowmeow"
```

> Команда выше вернёт структуру следующего вида:

```json
[
	{
		"order_id": 192,
		"error_code": 20002,
		"reason": "something went wrong"
	},
	{
		"order_id": 256,
		"error_code": 20002,
		"reason": "something went wrong"
	}
]
```

### HTTP Запрос

`DELETE https://gate.kickex.com/api/v1/cancelorders?orders=123456,14589655,12563369`

### Параметры URL 

Параметр | Тип | Обязательный | Описание
--------- | ----------- | ----------- | -----------
orders | string | Нет | id ордеров для отмены через запятую, без пробелов.
orderType | string | Нет | тип отмены stop/trade/all, если параметр не задан, по умолчанию работает all

### Параметры ответа

**В ответе содержатся только ордера, которые не удалось отменить**

Параметр | Тип | Обязательный | Описание
--------- | ----------- | ----------- | -----------
orderId | string | Yes | Идентификатор не отменённого ордера
error_code | string | Yes | Код ошибки
reason | string | No | Причина ошибки

## Orders History

Метод получения информации об истории ордеров пользователя.

```shell
curl "https://gate.kickex.com/api/v1/ordersHistory?pairName=KICK/BTC&startTime=123213123213213&endTime=32434523523535""
```

> Команда выше вернёт структуру следующего вида:

```json
{
	"orders": [
		{
			"pairName": "BTC/USDT",
			"orderId": "1232352423",
			"createdTimeStamp": 1588024908,
			"tradeIntent": 1,
			"limitPrice": "91.9012",
			"totalBuyVolume": "124124",
			"totalSellVolume": "3243",
			"orderedAmount": "150000",
			"tpActivateLevel": "90.0000",
			"tpLimitPrice": "92.0000",
			"slLimitPrice": "85.0000",
			"tpSubmitLevel": "89.0000",
			"slSubmitLevel": "86.0000"
		},
		{
			"pairName": "BTC/USDT",
			"orderId": "1232352423",
			"createdTimeStamp": 1588024908,
			"tradeIntent": 1,
			"limitPrice": "91.9012",
			"totalBuyVolume": "124124",
			"totalSellVolume": "3243",
			"orderedAmount": "150000",
			"tpActivateLevel": "90.0000",
			"tpLimitPrice": "92.0000",
			"slLimitPrice": "85.0000",
			"tpSubmitLevel": "89.0000",
			"slSubmitLevel": "86.0000"
		}
	]
}
```

### HTTP Запрос

`GET https://gate.kickex.com/api/v1/ordersHistory?pairName=KICK/BTC&startTime=123213123213213&endTime=32434523523535"`


### Параметры URL 

Параметр | Тип | Обязательный | Описание
--------- | ----------- | ----------- | -----------
pairName | string | Да | Наименование криптовалютной пары, например KICK/BTC
startTime | timestamp | Нет | Время начала периода, в мс
endTime | timestamp | Нет | время окончания периода, в мс
pageSize | string | Нет | Размер страницы, не более 100 ордеров
tradeIntent | integer | Нет | стороны ордера, 0-BUY, 1- SELL
tsnBottomOrder | timestamp | Нет | Штамп времени в наносекундах из последнего полученного на прошлой странице ордера, после которого нужно получать ордера. в том случае, если запрашивается следующая страница (старее текущей). Используется время создания ордера.
tsnTopOrder | timestamp | Нет | Штамп времени в наносекундах из первого полученного на прошлой странице ордера, до которого нужно получать ордера. в том случае, если запрашивается предыдущая страница (новее текущей). Используется время создания ордера.

### Параметры ответа

Параметр | Тип | Обязательный | Описание
--------- | ----------- | ----------- | -----------
pairName | string | Да | Наименование крпитовалютнйо пары в формате KICK/ETH
orderId | string | Yes | Идентификатор ордера
createdTimeStamp | timestamp | Да | Время создания ордера, в наносекундах
tradeIntent | integer | Да | Стороны ордера <br/>0-BUY </br>1- SELL
limitPrice | string | Нет | Возвращает значение, если при установке ордера была указана лимит цена в том случае если ордер не парный
totalBuyVolume | string | Да | итоговый купленный объем
totalSellVolume | string | Да | итоговый проданный объем
orderedVolume | string | Да | Заказанное количество криптовалюты для покупки/продажи
tpActivateLevel | string | Нет | уровень активации, параметр передается в случае, если был задан при установке ордера
tpLimitPrice | string | Нет | лимит цена блок тэйк профит, параметр передается в случае, если был задан при установке ордера
slLimitPrice | string | Нет | лимит цена блока стоп лос, параметр передается в случае, если был задан при установке ордера
tpSubmitLevel | string | Нет | стоп уровень блока тэйк профит, параметр передается в случае, если был задан при установке ордера
slSubmitLevel | string | Нет | стоп уровень блока стоп лосс, параметр передается в случае, если был задан при установке ордера


## Trade History 

Метод получения информации об истории сделок пользователя.

```shell
curl "https://gate.kickex.com/api/v1/tradesHistory?pairName=KICK/BTC&startTime=123213123213213&endTime=32434523523535"
```

> Команда выше вернёт структуру следующего вида:

```json
{
	"trades": [
		{
			"pairName": "BTC/USDT",
			"orderId": "1232352423",
			"timestamp": 1588024908,
			"tradeIntent": 0,
			"price": "732.9532",
			"feeQuoted": "3.1274",
			"feeExternal": "",
			"externalFeeCurrency": "KICK",
			"buyVolume": "14523",
			"sellVolume": "823491"
		},
		{
			"pairName": "BTC/USDT",
			"orderId": "1232352423",
			"timestamp": 1588024908,
			"tradeIntent": 0,
			"price": "732.9532",
			"feeQuoted": "3.1274",
			"feeExternal": "",
			"externalFeeCurrency": "KICK",
			"buyVolume": "14523",
			"sellVolume": "823491"
		}
	]
}
```

### HTTP Запрос

`GET https://gate.kickex.com/api/v1/tradesHistory?pairName=KICK/BTC&startTime=123213123213213&endTime=32434523523535`


### Параметры URL 

Параметр | Тип | Обязательный | Описание
--------- | ----------- | ----------- | -----------
pairName | string | Да | Наименование криптовалютной пары, например KICK/BTC
startTime | timestamp | Нет | Время начала периода, в наносекундах
endTime | timestamp | Нет | время окончания периода, в наносекундах
pageSize | string | Нет | Размер страницы, не более 100 сделок
tradeIntent | integer | Нет | сторон сделки, 0-BUY, 1- SELL
tsnBottomDeal | timestamp | Нет | Штамп времени в наносекундах последней полученной на прошлой странице сделки, после которого нужно получать сделки.
tsnTopDeal | timestamp | Нет | Штамп времени в наносекундах первой полученной на прошлой странице сделки, до которой нужно получать сделки.


### Параметры ответа

Параметр | Тип | Обязательный | Описание
--------- | ----------- | ----------- | -----------
pairName | string | Да | Наименование крпитовалютнйо пары в формате KICK/ETH
orderId | string | Да | Ордер, в рамках которого выполнена сделка
timestamp | timestamp | Да | Время сделки, в наносекундах
tradeIntent | integer | Да | Стороны ордера <br/>0-BUY </br>1- SALE
price | string | Да | цена за единицу криптовалюты в сделке, выраженная в котируемой криптовалюте
feeQuoted | string | Да | размер комиссии в котируемой криптовалюте
feeExternal | string | Нет | размер комисиии в дополнительнйо криптовалюте
externalFeeCurrency | string | Нет | наименование криптовалюты, в которой взяли дополнительную комиссию
buyVolume | string | Да | купленный объем в сделке
sellVolume | string | Да | проданный объем в сделке


## Active Orders List

Метод получения информации об активных ордерах.
Выборка сортируется по уменьшению creation_ts и ограничена сотней ордеров. Если возвращается сто или более ордеров, то вероятно наличие других - более старых. Для их получения нужно повторить запрос, указав в аргументе creation_ts самого старого из полученных ордеров.

```shell
curl "https://gate.kickex.com/api/v1/activeOrders?pairName=KICK/BTC&bottomOrderTs=1234343453"
```

> Команда выше вернёт структуру следующего вида:

```json
{
	"orders": [
		{
			"pairName": "BTC/USDT",
			"createdTimeStamp": 1588024908,
			"tradeIntent": 1,
			"limitPrice": "91.9012",
			"totalBuyVolume": "124124",
			"totalSellVolume": "3243",
			"orderedAmount": "150000",
			"tpActivateLevel": "90.0000",
			"tpLimitPrice": "92.0000",
			"slLimitPrice": "85.0000",
			"tpSubmitLevel": "89.0000",
			"slSubmitLevel": "86.0000",
			"activated": 1588022908,
			"stopTimestamp": 1588022908,
			"triggeredSide": "s"
		},
		{
			"pairName": "BTC/USDT",
			"createdTimeStamp": 1588024908,
			"tradeIntent": 1,
			"limitPrice": "91.9012",
			"totalBuyVolume": "124124",
			"totalSellVolume": "3243",
			"orderedAmount": "150000",
			"tpActivateLevel": "90.0000",
			"tpLimitPrice": "92.0000",
			"slLimitPrice": "85.0000",
			"tpSubmitLevel": "89.0000",
			"slSubmitLevel": "86.0000",
			"activated": 1588022908,
			"stopTimestamp": 1588022908,
			"triggeredSide": "l"
		}
	]
}
```

### HTTP Запрос

`GET https://gate.kickex.com/api/v1/activeOrders?pairName=KICK/BTC&bottomOrderTs=1234343453`


### Параметры URL 

Параметр | Тип | Обязательный | Описание
--------- | ----------- | ----------- | -----------
pairName | string | Нет | Наименование криптовалютной пары, например KICK/BTC
bottomOrderTs | string | Нет | Штамп времени (creation_ts, в наносекундах) из последнего видимого ордера.

### Параметры ответа

Параметр | Тип | Обязательный | Описание
--------- | ----------- | ----------- | -----------
pairName | string | Да | Наименование крпитовалютнйо пары в формате KICK/ETH
orderId | string | Yes | Идентификатор ордера
createdTimeStamp | timestamp | Да | Время создания ордера, в наносекундах
side | integer | Да | Стороны ордера <br/>0-BUY </br>1- SALE
limitPrice | string | Нет | Возвращает значение, если при установке ордера была указана лимит цена в том случае если ордер не парный
totalBuyVolume | string | Да | итоговый купленный объем
totalSellVolume | string | Да | итоговый проданный объем
orderedAmount | string | Да | Заказанное количество криптовалюты для покупки/продажи
tpActivateLevel | string | Нет | уровень активации, параметр передается в случае, если был задан при установке ордера
tpLimitPrice | string | Нет | лимит цена блок тэйк профит, параметр передается в случае, если был задан при установке ордера
slLimitPrice | string | Нет | лимит цена блока стоп лос, параметр передается в случае, если был задан при установке ордера
tpSubmitLevel | string | Нет | стоп уровень блока тэйк профит, параметр передается в случае, если был задан при установке ордера
slSubmitLevel | string | Нет | стоп уровень блока стоп лосс, параметр передается в случае, если был задан при установке ордера
activated | timestamp | Нет | время (в мс) передается если ордер активировался (если был выставлен скользящий или скользящий +сл ордер)
stopTimestamp | timestamp | Нет | Момент изменение стоп ордера в наносекундах
triggeredSide | string | Нет | Если стоп был двойной, показывает, какой сработал. p/l/?


## Create Trade Order 

установка ордера

```shell
curl "https://gate.kickex.com/api/v1/createTradeOrder"
  -X POST
  -H "Authorization: meowmeowmeow"
  -H "Content-Type: application/json"
  -d '{"pairName": "KICK/ETH", "orderedAmount": "110.000", "limitPrice": "0.12", "tradeIntent": 0, "modifier": "GTC"}'
```

> Команда выше вернёт структуру следующего вида:

```json
{
	"orderId": 123456
}
```

### HTTP Запрос

`POST https://gate.kickex.com/api/v1/createTradeOrder`


### Параметры запроса 

Параметр | Тип | Обязательный | Описание
--------- | ----------- | ----------- | -----------
pairName | string | Да | Наименование крпитовалютнйо пары (*пример: KICK/ETH*)
orderedAmount | string | Да | Заказанное количество криптовалюты для покупки/продажи
limitPrice | string | Нет | Значение указывается для непарных стоп-ордеров
tradeIntent | integer | Да | сейчас принимает значения 0 -покупка базовой и 1 - продажа базовой криптовалюты
modifier | string | Нет | сейчас принимает только значение GTC, если параметр не указан выставляется он же

### Параметры ответа

Параметр | Тип | Обязательный | Описание
--------- | ----------- | ----------- | -----------
orderId | integer | Да | Идентификатор созданного ордера


## Create Stop Order 

установка стоп-ордера

```shell
curl "https://gate.kickex.com/api/v1/createStopOrder"
  -X POST
  -H "Authorization: meowmeowmeow"
  -H "Content-Type: application/json"
  -d '{"pairName": "KICK/ETH", "orderedAmount": "110.000", "limitPrice": "0.12", "tradeIntent": 0, "modifier": "GTC", "reserves": 0, "tpActivateLevel": "0.11", "tpLimitPrice": "0.15", "slLimitPrice": "0.10", "tpSubmitLevel": "0.14", "slSubmitLevel": "0.13"}'
```

> Команда выше вернёт структуру следующего вида:

```json
{
	"orderId": 123456
}
```

### HTTP Запрос

`POST https://gate.kickex.com/api/v1/createStopOrder`

### Параметры URL 

Параметр | Тип | Обязательный | Описание
--------- | ----------- | ----------- | -----------
pairName | string | Да | Наименование крпитовалютнйо пары в формате KICK/ETH
orderedAmount | string | Да | Заказанное количество криптовалюты для покупки/продажи
limitPrice | string | Нет | Принимает значение, если создаётся одинарный ордер 
tradeIntent | integer | Да | сейчас принимает значения 0 -покупка базовой и 1 - продажа базовой криптовалюты
modifier | integer | Нет | сейчас принимает только значение GTC, если параметр не указан выставляется он же
reserves | integer | Нет | 0 (по умолчанию) резервы не делать, 1 - резервы делать. работает только в случае если указана лимит цена
tpActivateLevel | string | Нет | уровень активации,
tpLimitPrice | string | Нет | лимит цена блок тэйк профит,
slLimitPrice | string | Нет | лимит цена блока стоп лос
tpSubmitLevel | string | Нет | стоп уровень блока тэйк профит
slSubmitLevel | string | Нет | стоп уровень блока стоп лосс

### Параметры ответа

Параметр | Тип | Обязательный | Описание
--------- | ----------- | ----------- | -----------
orderId | integer | Да | Идентификатор созданного ордера

# User 

Набор методов для получения информации о пользователе и его аккаунте на бирже 

<aside class="warning">
Для использования данных методов требуется авторизация.
</aside>

## User Info 

Основные данные о пользователе.

```shell
curl "https://gate.kickex.com/api/v1/userInfo"
```

> Команда выше вернёт структуру следующего вида:

```json
{
	"userId": 111111111,
	"userName": "kickuser123",
	"platformName": "KickEX",
	"comment": "",
	"restrictions": 0
}
```

### HTTP Запрос

`GET https://gate.kickex.com/api/v1/userInfo`

### Параметры URL 

*Отсутствуют.*

### Параметры ответа

Параметр | Тип | Обязательный | Описание
--------- | ----------- | ----------- | -----------
userId | integer | Да | идентификатор Пользователя
userName | string | Нет | слаг Пользователя
platformName | string | Да | KickEX
comment | string | Нет | Комментарий, может содержать информацию, касательно ограничений на действия с АПИ Пользоватем
restrictions | integer | Да | Индикатор, показывающий, доступна ли торговля Пользователю (0 – доступна) 1 – не доступна

## Balance

Информация о балансе пользователя;

```shell
curl "https://gate.kickex.com/api/v1/user/balance"
```

> Команда выше вернёт структуру следующего вида:

```json
{
	"balances": [
		{
			"currencyName": "KICK",
			"balance": "10000000.8372",
			"available": "598292.1214",
			"inOrders": "300123.9133",
			"accountType": "2401"
		},
		{
			"currencyName": "ETH",
			"balance": "10000000.8372",
			"available": "598292.1214",
			"inOrders": "300123.9133",
			"accountType": "2401"
		}
	]
}
```

### HTTP Запрос

`GET https://gate.kickex.com/api/v1/user/balance`

### Параметры URL 

*Отсутствуют.*

### Параметры ответа

Параметр | Тип | Обязательный | Описание
--------- | ----------- | ----------- | -----------
currencyName | string | Да | наименование криптовалюты краткое
balance | string | Да | количество средств (тотал)
available | string | Да | количество средств, доступных для вывода или торговли
inOrders | string | Да | количество зарезервированных средств
accountType | string | Да | номер счета <br/>2401 - основной счет <br/>2411 - экосистемный счет в валюте брокера

## Deposit Address 

Метод получения персонального адреса для пополнения.

```shell
curl "https://gate.kickex.com/api/v1/depositAddresses?currencyName=USDT&chain=ERC20"
```

> Команда выше вернёт структуру следующего вида:

```json
{
	"currencyName": "USDT",
	"chain": "ERC20",
	"memo": "123456",
	"address": "0xc0DAa9e14343128cd50f7b934B5Bb23eddd3F246"
}
```

### HTTP Запрос

`GET https://gate.kickex.com/api/v1/depositAddresses?currencyName=USDT&chain=ERC20`

### Параметры URL 

Параметр | Тип | Обязательный | Описание
--------- | ----------- | ----------- | -----------
currencyName | string | Да | Наименование криптовалюты
chain | string | Нет | ERC20/OMNI/TRC20 какой выберем по дефолту для USDT?

### Параметры ответа

Параметр | Тип | Обязательный | Описание
--------- | ----------- | ----------- | -----------
currencyName | string | Да | Наименование криптовалюты
chain | string | Нет | ERC20/OMNI/TRC20
memo | string | Нет | необходим для xrp валют? в остальных случаях не нужен?
address | string | Да | адрес для пополнения


## Deposit History 

Метод получения истории пополнений.

```shell
curl "https://gate.kickex.com/api/v1/depositHistory?сurrencyName=KICK&startTime=1588015708&endTime=1588024908&status=success"
```

> Команда выше вернёт структуру следующего вида:

```json
{
	"deposits": [
		{
			"address": "0xc0DAa9e14343128cd50f7b934B5Bb23eddd3F246",
			"amount": "200000.0000",
			"fee": "",
			"currencyName": "KICK",
			"memo": "",
			"walletTxId": "",
			"status": "success",
			"createdAt": 1588024908,
			"updatedAt": 1588024908,
			"comment": ""
		},
		{
			"address": "0xc0DAa9e14343128cd50f7b934B5Bb23eddd3F246",
			"amount": "200000.0000",
			"fee": "",
			"currencyName": "KICK",
			"memo": "",
			"walletTxId": "0x3f9a6dc91d0ac08e2d13fc3a42a9dc4481aade8a96e10c1f497f9d6c60130a15",
			"status": "success",
			"createdAt": 1588024908,
			"updatedAt": 1588024908,
			"comment": ""
		}
	]
}
```

### HTTP Запрос

`GET https://gate.kickex.com/api/v1/depositHistory?сurrencyName=KICK&startTime=1588015708&endTime=1588024908&status=success`

### Параметры URL 

Параметр | Тип | Обязательный | Описание
--------- | ----------- | ----------- | -----------
сurrencyName | string | Нет | наименование криптовалюты, краткое, например BTC
startTime | timestamp | Нет | Время начала периода, в мс
endTime | timestamp | Нет | время окончания периода, в мс
status | string | Нет | фильтр статусов депозита (failure/success/processing)

### Параметры ответа

Параметр | Тип | Обязательный | Описание
--------- | ----------- | ----------- | -----------
address | string | Да | адрес, на который было произведено пополнение
amount | string | Да | количество пополненной криптовалюты
fee | string | Да | комиссия за пополнение
currencyName | string | Да | наименование криптовалюты (краткое), например, BTC
memo | string | Нет | мемо метка (если есть, отображаем)
walletTxId | string | Да | хэш транзакции
status | string | Да | failure/success/processing
createdAt | timestamp | Да | время создания записи в бд
updatedAt | timestamp | Да | время обновления записи в бд
comment | string | Нет | комментарий (при наличии)

## Tariff

Информация о скидках Пользователя

```shell
curl "https://gate.kickex.com/api/v1/user/tariff"
```

> Команда выше вернёт структуру следующего вида:

```json
{
	"maker": "0.0015",
	"taker": "0.0010",
	"msf": "0.5",
	"discount_regular_k": "0.2",
	"discount_special_k": "0.3",
	"discount_details": [],
	"vol_30d": "500280.1223",
	"vol_30d_dk": "0.2",
	"min_1d_rest": "12532.00",
	"min_1d_rest_dk": "0.3",
	"min_1d_kex_rest": "19732.64",
	"min_1k_kex_rest_dk": "0.45",
	"min_1d_activity_rest": "9612.14",
	"min_1d_activity_rest_dk": "0.10"
}
```

### HTTP Запрос

`GET https://gate.kickex.com/api/v1/user/tariff`

### Параметры URL 

*Отсутствуют.*

### Параметры ответа

Параметр | Тип | Обязательный | Описание
--------- | ----------- | ----------- | -----------
maker | string | Да | взимаемая комиссия мэйкера со сделки (доли, не проценты!)
taker | string | Да | взимаемая комиссия тэйкера со сделки (доли, не проценты!)
msf | string | Да | Максимальная доля от взимаемой комиссии, которую можно оплатить в валюте брокера (доли, не проценты!)
discount_regular_k | string | Да | дисконт (коэффициент) при оплате в котируемой валюте (доли, не проценты!) Чтобы вычислить скидку, нужно отнять от 1 дисконт
discount_special_k | string | Да | дисконт (коэффициент) при оплате в валютеброкера (доли, не проценты!) Чтобы вычислить скидку, нужно отнять от 1 дисконт
discount_details | object | Да | Объект, содержащий информацию о выполненных условиях для начисления скидок и о размере скидок (описание атрибутов объекта ниже)
&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;vol_30d | string | Да | объем торгов в эквиваленте USDT за предыдущие 30 суток;
&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;vol_30d_dk | string | Да | Скидка на оплату комиссии за выполнение условия по объем торгов за 30 суток (доли, не проценты!)
&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;min_1d_rest | string | Да | количество Kick токенов (приведенное к USDT) на балансе основного типа счета (минимальное количество за прошедшие сутки)
&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;min_1d_rest_dk | string | Да | Скидка на оплату комиссии за выполнение условия по количеству KICK токенов на балансе основного счета за прошедшие сутки (доли, не проценты!)
&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;min_1d_kex_rest | string | Да | количество KEX токенов (приведенное к USDT) на балансе основного типа счета (минимальное количество за прошедшие сутки)
&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;min_1k_kex_rest_dk | string | Да | Скидка на оплату комиссии за выполнение условия по количеству KEX токенов на балансе (доли, не проценты!)
&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;min_1d_activity_rest | string | Да | Минимальный остаток на экосистемном аккаунте
&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;min_1d_activity_rest_dk | string | Да | Скидка на оплату комиссий в валюте брокера (доли, не проценты!)

## Withdraw (пока не реализовано)

Метод создания запроса на вывод средств.

```shell
curl "https://gate.kickex.com/api/v1/user/withdraw"
  -X POST
  -H "Authorization: meowmeowmeow"
  -H "Content-Type: application/json"
  -d '{"address":"0xc0DAa9e14343128cd50f7b934B5Bb23eddd3F246",
	   "memo":"",
	   "amount":"1000",
	   "currency":"KICK",
	   "chain":"ERC20"}'  
```

> Команда выше вернёт структуру следующего вида:

```json
{
	"orderId": 123456
}
```

### HTTP Запрос

`POST https://gate.kickex.com/api/v1/user/withdraw`


### Параметры URL 

Параметр | Тип | Обязательный | Описание
--------- | ----------- | ----------- | -----------
address | string | Да | адрес, на который выводятся средства
memo | string | Нет | мемо тег (опционально)
amount | string | Да | количество криптовалюты для вывода
currency | string | Да | криптовалюта, в которой осуществляется вывод
сhain | string | Нет | Если чейнов несколько, как с USDT, то отображается чейн на который был вывод

### Параметры ответа

Параметр | Тип | Обязательный | Описание
--------- | ----------- | ----------- | -----------
orderId | integer | Да | Идентификатор созданного ордера

## Withdrawal History 

Метод для запроса истории вывода средств.

```shell
curl "https://gate.kickex.com/api/v1/withdrawalHistory?сurrencyName=KICK&startTime=1588015708&endTime=1588024908&status=success"
```

> Команда выше вернёт структуру следующего вида:

```json
{
	"withdrawals": [
		{
			"transactionId": "123415",
			"address": "0xc0DAa9e14343128cd50f7b934B5Bb23eddd3F246",
			"amount": "200000.0000",
			"fee": "",
			"currencyName": "KICK",
			"memo": "",
			"walletTxId": "",
			"status": "processing",
			"createdAt": 1588024908,
			"updatedAt": 1588024908,
			"comment": ""
		},
		{
			"transactionId": "123416",
			"address": "0xc0DAa9e14343128cd50f7b934B5Bb23eddd3F246",
			"amount": "200000.0000",
			"fee": "",
			"currencyName": "KICK",
			"memo": "",
			"walletTxId": "0x3f9a6dc91d0ac08e2d13fc3a42a9dc4481aade8a96e10c1f497f9d6c60130a15",
			"status": "success",
			"createdAt": 1588024908,
			"updatedAt": 1588024908,
			"comment": ""
		}
	]
}
```

### HTTP Запрос

`GET https://gate.kickex.com/api/v1/withdrawalHistory?сurrencyName=KICK&startTime=1588015708&endTime=1588024908&status=success`

### Параметры URL 

Параметр | Тип | Обязательный | Описание
--------- | ----------- | ----------- | -----------
сurrencyName | string | Нет | наименование криптовалюты, краткое, например BTC
startTime | timestamp | Нет | Время начала периода, в мс
endTime | timestamp | Нет | время окончания периода, в мс
status | string | Нет | failure/success/processing/wallet_processing

### Параметры ответа

Параметр | Тип | Обязательный | Описание
--------- | ----------- | ----------- | -----------
transactionId | string | Да | Уникальный идентификатор транзакции
address | string | Да | адрес, на который был произведен вывод
amount | string | Да | количество отправленной криптовалюты
fee | string | Да | комиссия за вывод
currencyName | string | Да | наименование криптовалюты (краткое), например, BTC
memo | string | Нет | мемо метка (если есть, отображаем)
walletTxId | string | Да | хэш транзакции
status | string | Да | failure/success/processing
createdAt | timestamp | Да | время создания записи в бд
updatedAt | timestamp | Да | время обновления записи в бд
comment | string | Нет | комментарий (при наличии)
chain | string | Нет | Если чейнов несколько, как с USDT, то отображается чейн на который был вывод


# WebSocket endpoint


## Общие определения

>Пример запроса

```json
{
    "id": "dsncjksdnc",             //идентификатор запроса
    "type": "someType",             //Тип запроса
}
```

>Ответ в случае ошибки

```json
{
    "id": "dsncjksdnc",             //идентификатор запроса (тот же, что и в запросе)
    "error": {                      //ошибка
        "code": 29000,              //код ошибки
        "reason": "Error reason"    //текст ошибки
    },
    "dev_error": {                  //отладочная информация об ошибке
        "code": 5607,               //внутренний код ошибки
        "reason": "Internal reason" //внутренний текст ошибки
    }
}
```


>Ошибка в случае слишком большого объёма данных 

```json
{
    "error": {"code": 19008, "reason": "answer is too big"}
}
```

Адрес для подключения клиента к веб-сокету: [http://gate.kickex.com](http://gate.kickex.com)

Основная часть запросов содержит два обязательных поля: **id** и **type**. 

**id** - это идентификатор запроса. Произвольная строка. Генерируется клиентом. Предполагается, что каждый запрос будет иметь уникальный идентификатор. Тот же самый идентификатор без изменения возвращается в ответе шлюза. Длина id не более 10KB.

**type** - это тип запроса

В случае ошибки, в ответе сервера присутствует **только** идентификатор запроса и поле **error** содержащее код и текст ошибки. На Dev серверах дополнительно возвращается поле **dev_error** с внутренним кодом и текстом ошибки.


Еще одна специальная ошибка возвращается, когда превышен размер буфера ответа. Это происходит, если ответ от внутреннего сервера слишком большой, либо клиент не успевает вычитывать данные. В этом ответе нет идентификатора запроса.

Как правило, получение такой ошибки означает, что были потеряны одно или более сообщений. В том числе, это могут значимые сообщения об изменении состояния ордеров, балансе пользователя.

Существует возможность увеличения размера буфера для ответов сервера, например, для целей автоматической торговли. Это можно сделать с помощью технической поддержки.


Если сервер получает невалидный JSON в качестве запроса, то в ответе придет ошибка **ER_MALFORMED_JSON** и не будет поля идентификатора запроса.

### Состояние шлюза

>Запрос

```json
{
    "id": "dsncjksdnc",             //идентификатор запроса
    "type": "status",               //Тип запроса
}
```

>Ответ

```json
{
    "id": "dsncjksdnc",
    "accounting": {                 //состояние подключения к учету
        "connected": true,
        "callbacks": 0
    },
    "kickid": {                     //состояние подключения к KICKID Tarantool
        "connected": true,
        "callbacks": 0
    },
    "statistics": {                 //состояние подключения к статистике
        "connected": true,
        "callbacks": 2
    },
    "walletbc": {                   //состояние подключения к wallet
        "status_code": 0,
        "status": "CONNECTING"
    }
}
```


### Пинг

Клиент должен постоянно посылать указанные ниже ping запросы, чтобы сервер не закрыл соединение. Соединение закрывается, если от клиента за минуту не пришло ни одного фрейма. То есть вместо ping также будет работать любой произвольный запрос.

>Запрос

```json
{
    "type": "ping",                 //Тип запроса
}
```

>Ответ

```json
{
    "pong": 0
}
```


## Orderbook API

### Получение стакана и подписка

Ответы по подписке. 

Каждый ответ это инкрементное изменение стакана. В массивах bids, asks передаются строки которые меняются.

Если в поле total находится пустая строка, то строку нужно удалить из стакана. В противном случае строка добавляется или заменяется.


>Запрос

```json
{
    "id": "dsncjksdnc",                 //идентификатор запроса
    "type": "getOrderBookAndSubscribe",     //Тип запроса
    "pair": "KICK/ETH"
}
```

>Ответ

```json
{
    "id": "dsncjksdnc",                 //идентификатор запроса
    "bids":[{
        "price": "152.7",               //цена валютной пары
        "amount": "4545.3",             //количество базовой валюты
        "total": "455.22"               //всего (в котируемой валюте)
    },
    ...],
 
    "asks": [{
        "price": "12.7",                //цена валютной пары
        "amount": "485.3",              //количество базовой валюты
        "total": "4562.55"              //всего (в котируемой валюте)
    }
    ...],
 
    "lastPrice":{
        "price": "4545,                 //цена валютной пары
        "type": -1/0/1                  // -1 цена понизилась
                                        // 0 цена не изменилась
                                        // 1 цена возросла
    }
}
```	

> Изменившиеся данные

```json
{
    "id": "dihfbeibf",      //идентификатор запроса
    "bids":[{
        "price": "152.7",               //цена валютной пары
        "amount": "4545.3",             //количество базовой валюты
        "total": "455.22"               //всего (в котируемой валюте)
    },
    ...],
 
    "asks": [{
        "price": "12.7",                //цена валютной пары
        "amount": "485.3",              //количество базовой валюты
        "total": "4562.55"              //всего (в котируемой валюте)
    },
    {                                   //Строка для удаления из стакана. Total должен быт пустой строкой
        "price": "12.7",                //цена валютной пары
        "amount": "NaN",                //количество базовой валюты
        "total": ""                     //
    }
    ...],
 
    "lastPrice":{
        "price": "4545,                 //цена валютной пары
        "type": -1/0/1                  // -1 цена понизилась
                                        // 0 цена не изменилась
                                        // 1 цена возросла
    }
}```

### Отписка от обновлений

> Запрос (отписка от обновлений)

```json
{
    "id": "dsncjksdnc",                 //идентификатор запроса
    "type": "unsubscribeOrderBook",     //Тип запроса
}
```

>Ответ

```json
{
    "id": "dsncjksdnc",                 //идентификатор запроса
}
```

## Currency Pairs

### Подписка 

> Запрос (подписка)

```json
{
    "id": "dsncjksdnc",             //идентификатор запроса
    "type": "getPairsAndSubscribe",             //Тип запроса
}
```

>Ответы (по подписке)

```json
{
    "id": "dsncjksdnc",             //идентификатор запроса
    "pairs" : [
    {
        "baseCurrency": "ETH",          //базовая валюта
        "quoteCurrency": "BTC",         //котируемая валюта
        "price": "228.5698565",         //цена валютной пары
        "price24hChange": "-1.5",       //изменение цены за 24 часа
        "volume24hChange": "1258.55",   //изменение объема за 24 часа (в котируемой валюте)
        "amount24hChange": "45.55",     //изменение количества за 24 часа (в базовой валюте)
        ?"lowPrice24h": "220.0",        //наименьшая историческая цена за 24 часа
        ?"highPrice24h": "230.0",       //наибольшая историческая цена за 24 часа
        "priceScale": 8,                //Точность цены в ордере
        "quantityScale": 4,             //Точность количества в ордере
        "volumeScale": 7,               //Точность объема в ордере
        ?"minQuantity": "0.0001",       //Минимальное количество
        ?"minVolume": "0.0001",         //Минимальный Объем
        "state": 1                      //Состояние
                                        //DRAFT    = 1, -- 1
                                        //REJECTED = 2, -- 2
                                        //BLOCKED  = 3, -- 3
                                        //COMMITED = 4  -- 4
 
    }
    , ... ]
}
```

### Отписка от обновлений

>Запрос (отписка от обновлений)

```json
{
    "id": "dsncjksdnc",                 //идентификатор запроса
    "type": "unsubscribePairs",         //Тип запроса
}
```

>Ответ

```
{
    "id": "dsncjksdnc",                 //идентификатор запроса
}
```


## История торгов по выбранной торговой паре

### Подписка 

>Запрос (подписка)

```json
{
    "id": "dsncjksdnc",                 //идентификатор запроса
    "type": "getHistoryAndSubscribe",   //Создание торгового ордера и формирование резервов.
    "pair": "KICK/ETH",                 //Идентификатор торговой пары
}
```

>Ответы

```json
{
    "id": "dsncjksdnc", //идентификатор запроса
    "history": [{
        price: 455.55,              //цена валютной пары
        amount: 228.1488,           //количество базовой валюты
        timestamp: 156454565645,    //временная метка сделки
        type: "sell/buy" },         //покупка или продажа
        ...
    ]
}
```

В первом ответе возвращается полный список последних сделок, в последующих ответах возращаются инкрементные обновления.

### Отписка от обновлений

> Запрос (отписка)

```json
{
    "id": "dsncjksdnc",                 //идентификатор запроса
    "type": "unsubscribeHistory",       //Тип запроса
}
```

>Ответ

```json
{
    "id": "dsncjksdnc",                 //идентификатор запроса
}
```
