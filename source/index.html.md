---
title: KickEx API Reference

language_tabs: # must be one of https://git.io/vQNgJ
  - shell
  - ruby
  - python
  - javascript

toc_footers:
  - <a href='#'>Sign Up for a Developer Key</a>
  - <a href='https://github.com/slatedocs/slate'>Documentation Powered by Slate</a>
  - <a href='index_ru.html'>Описание API на русском языке</a>

includes:
  - errors

search: true
---

# Introduction

Welcome to the External API for KickEX exchange.


# Authentication

For client authentication and integrity control the following attributes should be added to the request headers:

* KICK-API-KEY - API key (it is provided in *base64url* format and needs to be decoded to binary format before KICK-SIGNATURE generation)
* KICK-API-PASS - API key passphrase (format (?))
* KICK-API-TIMESTAMP - TIMESTAMP of the request (unix timestamp, seconds)
* KICK-SIGNATURE - request signature (format (?))

```
base64_encode(hash_hmac("sha512",
	hash_hmac("sha512",
		hash_hmac("sha512",
			hash_hmac("sha512", $timestamp, $method, true),
		$request_path, true),
	$body, true),
$api_secret, true));
```
Request signature is need for the integrity control of the request on server-side. To create the request signature you need to use **API Secret** that is not included in the request headers.
To create the request signature you need:

- timestamp of the request;
- method name used (e.g. GET);
- request path (e.g. /api/v1/deposit-addresses);
- request body (e.g. period=5min&pairName=BTC/USDT&startTime=22814882323);

# Market
This section covers methods that provide data regarding available currencies and currency pairs and rates.
<aside class="notice">
You don't need to authorize to use methods from <b>Market</b> section.
</aside>


## Pairs

```shell
curl "http://example.com/api/v1/market/pairs?type=market"
```

> The above command returns JSON structured like this:

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

### HTTP Request

`GET https://example.com/api/v1/market/pairs?type=market`

### URL Parameters

Parameter | Type | Required | Description
--------- | ----------- | ----------- | -----------
type | string | Yes | Currently the only valid value is 'market'

### Response Parameters
Parameter | Type | Required | Description
--------- | ----------- | ----------- | -----------
pairName | string | Yes | Currency pair name *(ex: BTC/USDT)*
baseCurrency | string | Yes | Base currency name *(ex: BTC)*
quoteCurrencу | string | Yes | Quotes currency name *(ex: USDT)*
baseMinSIze | string | Yes | Minimum amount of base currency for order creation
quoteMinSize | string | Yes | Minimum amount of quote currency for order creation
priceDecimal | Number | Yes | Price fraction digits 
amountDecimal | Number | Yes | Amount fraction digits
state | integer | Yes | Attribute showing if trading on this currency pair or not: <br/>4 – trading is available <br/>1,2,3 – traiding is unavailable


## All Tickers

```shell
curl "http://example.com/api/v1/market/allTickers"
```

> The above command returns JSON structured like this:

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

### HTTP Request

`GET https://example.com/api/v1/market/allTickers`

### URL Parameters

*None.*

### Response Parameters
Parameter | Type | Required | Description
--------- | ----------- | ----------- | ----------- 
timestamp | timestamp | Yes | Server timestamp
pairName | string | Yes | Currency pair name *(ex: BTC/USDT)*
bestBid | string | Yes | Best bid price
bestAsk | string | Yes | Best ask price
changePrice | string | Yes | Price change during last 24 hours
highestPrice | string | Yes | Maximum price during last 24 hours
lowestPrice | string | Yes | Minimum price during last 24 hours
baseVol | string | Yes | Aggregated deals volume during last 24 hours in base currency
quoteVol | string | Yes | Aggregated deals volume during last 24 hours in quotes currency
lastPrice | string | Yes | Last deal price
priceDecimal | string | Yes | Price fraction digits
lastVolume | string | Yes | Last deal volume
bestAskVolume | string | Yes | Best ask volume
bestBidVolume | string | Yes | Best bid volume


## 24hrs stats

The method provides statistics on a currency pair during the last 24 hours.

```shell
curl "http://example.com/api/v1/market/stats24?pairName=BTC/USDT"
```

> The above command returns JSON structured like this:

```json
{
	"pairName": "BTC/USDT",
	"high24": "3425.0092",
	"low24": "3389.1294",
	"amountVol": "91582919",
	"baseVol": "27020.6311",
	"lastPrice": "3421.7623",
	"bestBid": "3420.4223",
	"bestAsk": "3401.7623",
	"averagePrice": "3407.3719",
	"priceChange": "36.0925",
	"timestamp": 1588024908
}
```

### HTTP Request

`GET https://example.com/api/v1/market/stats24?pairName=BTC/USDT`

### URL Parameters

Parameter | Type | Required | Description
--------- | ----------- | ----------- | -----------
pairName | string | Yes | Currency pair name *(ex: BTC/USDT)*

### Response Parameters
Parameter | Type | Required | Description
--------- | ----------- | ----------- | -----------
pairName | string | Yes | Currency pair name *(ex: BTC/USDT)*
high24 | string | Yes | Maximum price during last 24 hours
low24 | string | Yes | Minimum price during last 24 hours
amountVol | string | Yes | Aggregated deals volume during last 24 hours
baseVol | string | Yes | Aggregated deals volume during last 24 hours in base currency
lastPrice | string | Yes | Last deal price
bestBid | string | Yes | Best bid price (at the time of the request)
bestAsk | string | Yes | Best ask price (at the time of the request)
averagePrice | string | Yes | Average price during last 24 hours
priceChange | string | Yes | Price change during last 24 hours
timestamp | timestamp | Yes | Timestamp


## Currency

The method provides data regarding specified currency. If *currency* parameter is not provided, returns data regarding all available currencies.

```shell
curl "http://example.com/api/v1/currencies?currency=ETH"
```

> The above command returns JSON structured like this:

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

> Or like this (if the currency attributes is ommitted):

```json
{
	"currencies": [
		{
			"currencyCode???": "ETH",
			"currencyName": "ETH",
			"fullName": "Ethereum",
			"decimal": 8,
			"minWithdawal???": "1",
			"minFeeWithrawal???": "0.1",
			"isWithdrawEnable???": true,
			"isDepositEnable???": true,
			"isExchangeEnable???": true,
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
			"currencyCode???": "KICK",
			"currencyName": "KICK",
			"fullName": "Kick Token",
			"decimal": 8,
			"minWithdawal???": "1",
			"minFeeWithrawal???": "0.1",
			"isWithdrawEnable???": true,
			"isDepositEnable???": false,
			"isExchangeEnable???": true,
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

### HTTP Request

`GET https://example.com/api/v1/currencies?currency=ETH`

### URL Parameters

Parameter | Type | Required | Description
--------- | ----------- | ----------- | -----------
currency | string | Yes | Currency short name *(ex: BTC)*

### Response Parameters
Parameter | Type | Required | Description
--------- | ----------- | ----------- | -----------
isoCode | string | ? | Currency code
currencyName | string | Yes | Currency short name
fullName | string | Yes | Currency full name
decimal | number | Yes | Currency fraction digits
minWithdawal | string | ? | Minimum withdrawal amount
minFeeWithrawal | string | ? | Minimum withdrawal fee
isWithdrawEnable | boolean | ? | Is withdrawal available?
isDepositEnable | boolean | ? | Is deposit available?
isExchangeEnable | boolean | ? | Is exhange available?
state | integer | ? | Attribute showing if trading on this currency pair or not: <br/>4 – trading is available <br/>1,2,3 – traiding is unavailable
convertPath | array | No | Array of currency pairs needed for convertion. Empty array means the conversion is not needed. <br/>true - means that conversion rate should be multiplied by currency pair rate <br/>false - means that convetion rate should be divided by currency pair rate absolute value <br/>```[{"BTC/USDT": true},{"ETH/BTC": false}]```

## Minibars

The method provides closure prices for last 24 hours for all currency pairs.

```shell
curl "http://example.com/api/v1/minibars"
```

> The above command returns JSON structured like this:

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

### HTTP Request

`GET https://example.com/api/v1/minibars`

### URL Parameters

*None.*

### Response Parameters
Parameter | Type | Required | Description
--------- | ----------- | ----------- | -----------
pairName | string | Yes | Currency pair name *(ex: KICK/BTC)*
bars | array | Yes | Candles in the following format <unix timestamp, close price><br/>{<br/>	"bars": [<br/>		[1574168400, 0.0010096],[1574172000,0.0010004]]<br/>}

## Trades

This method returns deals history during last 24 hours.

```shell
curl "http://example.com/api/v1/market/trades?pairName=BTC/USDT&type=buy"
```

> The above command returns JSON structured like this:

```json
{
	"price": "1124.120937",
	"baseVol": "491082.129100",
	"quoteVol": "436.858805",
	"timestamp": 1588024908,
	"type": "buy"
}
```

### HTTP Request

`GET https://example.com/api/v1/market/trades?pairName=BTC/USDT&type=buy`

### URL Parameters

Parameter | Type | Required | Description
--------- | ----------- | ----------- | -----------
pairName | string | Yes | Currency pair name *(ex: KICK/BTC)*
type | string | Нет | Should be "buy" or "sell", if missing all trades are returned.

### Response Parameters
Parameter | Type | Required | Description
--------- | ----------- | ----------- | -----------
price | string | Yes | Trade price
baseVol | string | Yes | Base currency volume
quoteVol | string | Yes | Quotes currency volume
timestamp | timestamp | Yes | Trade timestamp
type | string | Yes | Trade type: <br/>buy - means that an ask position was removed from the exchange order book; <br/>sell - means that a bid position was removed from the exchange order book.


## Orderbook

This request is used to get current exchange orders by currency pair name.
Provides both aggregated and particular data.

```shell
curl "http://example.com/api/v1/market/orderbook?pairName=BTC/USDT&depth=20"
```

> The above command returns JSON structured like this:

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

### HTTP Request

`GET https://example.com/api/v1/market/orderbook?pairName=BTC/USDT&depth=20`

### URL Parameters

Parameter | Type | Required | Description
--------- | ----------- | ----------- | -----------
pairName | string | Yes | Currency pair name *(ex: KICK/BTC)*
depth | int? | No | Orderbook depth.<br/>0 or null - all available data <br/>5/10/20/50/100/500 - number of bid and ask positions to show
level | int | No | 1 - Best buy and sell open trades <br>2 - Whole orderbook sorted by best bid and ask prices <br> 3 - Whole orderbook (not sorted)

### Response Parameters
Parameter | Type | Required | Description
--------- | ----------- | ----------- | -----------
timestamp | timestamp | Yes | Timestamp
bids | Array of string | Yes | List of bids (price and amount)
asks | Array of string | Yes | List of asks (price and amount)


## Candles

```shell
curl "http://example.com/api/v1/market/bars/?period=5min&pairName=BTC/USDT&startTime=22814882323&endTIme32222869898"
```

> The above command returns JSON structured like this:

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

### HTTP Request

If `startTime` or `endTime` are not provided, not more than **???** results are returned.

`GET https://example.com/api/v1/market/bars/?period=5min&pairName=BTC/USDT&startTime=22814882323&endTIme32222869898`

### URL Parameters

Parameter | Type | Required | Description
--------- | ----------- | ----------- | -----------
pairName | string |  | Currency pair name *(ex: KICK/BTC)*
period | string |  | 1/3/10/60 (minutes) 1D 1M 3M
startTIme | timestamp |  | Start time in milliseconds
endTime | timestamp |  | End time in milliseconds

### Response Parameters
Parameter | Type | Required | Description
--------- | ----------- | ----------- | -----------
timestamp | timestamp | Yes | Timestamp of candle creation
openPrice | string | Yes | Open price of the candle
closePrice | string | Yes | Close price of the candle
highPrice | string | Yes | Highest price
lowPrice | string | Yes | Lowest price
transactionVolume | string | Yes | Trades volume within the candle
transactionAmount | string | Yes | Trades number within the candle


## Server time

```shell
curl "http://example.com/api/v1/serverTime"
```

> The above command returns JSON structured like this:

```json
{
	"code": 200,
	"message": "success",
	"time": 1588024908
}
```

### HTTP Request

`GET https://example.com/api/v1/serverTime`

### URL Parameters

*None.*

### Response Parameters
Parameter | Type | Required | Description
--------- | ----------- | ----------- | -----------
code | integer | Yes | Response code (200 for OK)
message | string | Yes | *success*/*error*
time | timestamp | Yes | Current server time (in milliseconds)


# Trade 

This section covers methods that operate with exchange orders.

<aside class="warning">
Authorization is needed to use these methods.
</aside>

## Cancel Order 

Exchange order cancellation method.

```shell
curl "http://example.com/api/v1/orders/{orderId}"
  -X DELETE
  -H "Authorization: meowmeowmeow"
```

> The above command returns JSON structured like this:

```json
{
	"cancelledOrderId": "111111111",
	"comment": ""
}
```

### HTTP Request

`DELETE https://example.com/api/v1/orders/{orderId}`

### URL Parameters

*None.*


### Response Parameters
Parameter | Type | Required | Description
--------- | ----------- | ----------- | -----------
cancelledOrderId | string | Yes | Cancelled exchange order identifier
comment | string | No | Contains result: order cancelled or error

## Cancel All Orders

Method used to cancel a group of orders or all the open orders.

```shell
curl "http://example.com/api/v1/orders?pairName=BTC/USDT&orderType=STOP"
  -X DELETE
  -H "Authorization: meowmeowmeow"
```

> The above command returns JSON structured like this:

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

### HTTP Request

`DELETE https://example.com/api/v1/orders?pairName=BTC/USDT&orderType=STOP`

### URL Parameters

Parameter | Type | Required | Description
--------- | ----------- | ----------- | -----------
pairName | string | No | Currency pair name *(ex: KICK/ETH)*
orderType | string | No | Type of the orders to cancel (stop/trade/all), missing order type is treated as "all".

### Response Parameters

**Please be informed that response includes only orders that were not cancelled.**

Parameter | Type | Required | Description
--------- | ----------- | ----------- | -----------
orderId | string | Yes | Not cancelled exchange order identifier
error_code | string | Yes | Error code
reason | string | No | Error reason

## Cancel Orders

Method used to cancel a group of open orders.


```shell
curl "http://example.com/api/v1/cancelorders?orders=123456,14589655,12563369"
  -X DELETE
  -H "Authorization: meowmeowmeow"
```

> The above command returns JSON structured like this:

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

### HTTP Request

`DELETE https://example.com/api/v1/cancelorders?orders=123456,14589655,12563369`

### URL Parameters

Parameter | Type | Required | Description
--------- | ----------- | ----------- | -----------
orders | string | No | Order identifiers divided by commas (without spaces)

### Response Parameters
**Please be informed that response includes only orders that were not cancelled.**

Parameter | Type | Required | Description
--------- | ----------- | ----------- | -----------
orderId | string | Yes | Not cancelled exchange order identifier
error_code | string | Yes | Error code
reason | string | No | Error reason

## Orders History 

This method is used to get data on the user's orders.

```shell
curl "http://example.com/api/v1/ordersHistory?pairName=KICK/BTC&startTime=123213123213213&endTime=32434523523535"
```

> The above command returns JSON structured like this:

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

### HTTP Request

`GET https://example.com/api/v1/ordersHistory?pairName=KICK/BTC&startTime=123213123213213&endTime=32434523523535"`

### URL Parameters

Parameter | Type | Required | Description
--------- | ----------- | ----------- | -----------
pairName | string | Yes | Currency pair name *(ex: KICK/BTC)*
startTIme | timestamp | No | Start time in milliseconds
endTime | timestamp | No | End time in milliseconds
pageSize | string | No | Page size, up to 100 orders
tradeIntent | integer | No | Trade side <br/> 0 - BUY <br/>1 - SELL
tsnBottomOrder | timestamp | No | Timestamp of order creation in nanoseconds (10^-9) of the last order on previous page (after which orders should be requested), if not the first page is requested.
tsnTopOrder | timestamp | No | Timestamp of order creation in nanoseconds (10^-9) of the first order on previous page (if newer page is requested).

### Response Parameters
Parameter | Type | Required | Description
--------- | ----------- | ----------- | -----------
pairName | string | Yes | Currency pair name *(ex: KICK/ETH)*
orderId | string | Yes | Order unique identifier
createdTimeStamp | timestamp | Yes | Order creation timestamp in nanoseconds
tradeIntent | integer | Yes | Trade side <br/> 0 - BUY <br/>1 - SELL
limitPrice | string | No | Will be returned for orders where limit price was set on creation.
totalBuyVolume | string | Yes | Total bought volume **???**
totalSellVolume | string | Yes | Total sold volume **???**
orderedVolume | string | Yes | Ordered volume of currency
tpActivateLevel | string | No | Take profit activation level, will be returned if set on order creation.
tpLimitPrice | string | No | Take profit limit price, will be returned if it was set on order creation.
slLimitPrice | string | No | Stop loss limit price, will be returned if it was set on order creation.
tpSubmitLevel | string | No | Take profit stop level, will be returned if it was set on order creation.
slSubmitLevel | string | No | Stop loss stop level, will be returned if it was set on order creation.


## Trade History 

This method is used to get data on the user's trades.

```shell
curl "http://example.com/api/v1/tradesHistory?pairName=KICK/BTC&startTime=123213123213213&endTime=32434523523535"
```

> The above command returns JSON structured like this:

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

### HTTP Request

`GET https://example.com/api/v1/tradesHistory?pairName=KICK/BTC&startTime=123213123213213&endTime=32434523523535`

### URL Parameters

Parameter | Type | Required | Description
--------- | ----------- | ----------- | -----------
pairName | string | Yes | Currency pair name *(ex: KICK/BTC)*
startTIme | timestamp |  | Start time in milliseconds
endTime | timestamp |  | End time in milliseconds
pageSize | string | No | Page size, up to 100 orders
tradeIntent | integer | No | Trade side <br/> 0 - BUY <br/>1 - SELL
tsnBottomDeal | timestamp | No | Timestamp of the trade in nanoseconds (10^-9) of the last trade on previous page (after which deals should be requested).
tsnTopDeal | timestamp | No | Timestamp in nanoseconds (10^-9) of the first trade on previous page (if newer page is requested).


### Response Parameters
Parameter | Type | Required | Description
--------- | ----------- | ----------- | -----------
pairName | string | Yes | Currency pair name *(ex: KICK/ETH)*
orderId | string | Yes | Order unique identifier (for current deal)
timestamp | timestamp | Yes | Trade timestamp in nanoseconds
tradeIntent | integer | Yes | Trade side <br/> 0 - BUY <br/>1 - SALE
price | string | Yes | Price per currency unit (in quotes currency)
feeQuoted | string | Yes | Fee (in quoted currency)
feeExternal | string | No | Fee (in additional currency)
externalFeeCurrency | string | No | Additional fee currency
buyVolume | string | Yes | Trade buy volume
sellVolume | string | Yes | Trade sell volume


## Active Orders List

Method used to get information regarding all user's open (active) exchange orders.

```shell
curl "http://example.com/api/v1/activeOrders?pairName=KICK/BTC"
```

> The above command returns JSON structured like this:

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
			"tpActivateLeve": "90.0000",
			"tpLimitPrice": "92.0000",
			"slLimitPrice": "85.0000",
			"tpSubmitLevel": "89.0000",
			"slSubmitLevel": "86.0000",
			"activated": 1588022908,
			"stopTimestamp": 1588022908,
			"triggeredSide": "l"
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
			"tpActivateLeve": "90.0000",
			"tpLimitPrice": "92.0000",
			"slLimitPrice": "85.0000",
			"tpSubmitLevel": "89.0000",
			"slSubmitLevel": "86.0000",
			"activated": 1588022908,
			"stopTimestamp": 1588022908,
			"triggeredSide": "s"
		}
	]
}
```

### HTTP Request

`GET https://example.com/api/v1/activeOrders?pairName=KICK/BTC`

### URL Parameters

Parameter | Type | Required | Description
--------- | ----------- | ----------- | -----------
pairName | string | No | Currency pair name *(ex: KICK/BTC)*


### Response Parameters
Parameter | Type | Required | Description
--------- | ----------- | ----------- | -----------
pairName | string | Yes | Currency pair name *(ex: KICK/ETH)*
orderId | string | Yes | Order unique identifier
createdTimeStamp | timestamp | Yes | Order creation timestamp in nanoseconds
side | integer | Yes | Trade side <br/> 0 - BUY <br/>1 - SALE
limitPrice | string | No | Will be returned for orders where limit price was set on creation.
totalBuyVolume | string | Yes | Total bought volume **???**
totalSellVolume | string | Yes | Total sold volume **???**
orderedAmount | string | Yes | Ordered amount of currency
tpActivateLeve | string | No | Take profit activation level, will be returned if set on order creation.
tpLimitPrice | string | No | Take profit limit price, will be returned if it was set on order creation.
slLimitPrice | string | No | Stop loss limit price, will be returned if it was set on order creation.
tpSubmitLevel | string | No | Take profit stop level, will be returned if it was set on order creation.
slSubmitLevel | string | No | Stop loss stop level, will be returned if it was set on order creation.
activated | timestamp | No | Will be returned if the order was activated (for sliding orders) 
stopTimestamp | timestamp | No | Timestamp of stop order change in nanoseconds
triggeredSide | string | No | If it is a double stop order, this attribute shows which of them were fired: p/l/?


## Create Trade Order 

Method for trade order creation.

```shell
curl "http://example.com/api/v1/createTradeOrder"
  -X POST
  -H "Authorization: meowmeowmeow"
  -H "Content-Type: application/json"
  -d '{"pairName": "KICK/ETH", "orderedAmount": "110.000", "limitPrice": "0.12", "tradeIntent": 0, "modifier": "GTC"}'
```

> The above command returns JSON structured like this:

```json
{
	"orderId": 123456
}
```

### HTTP Request

`POST https://example.com/api/v1/createTradeOrder`

### Request Parameters

Parameter | Type | Required | Description
--------- | ----------- | ----------- | -----------
pairName | string | Yes | Currency pair name *(ex: KICK/ETH)*
orderedAmount | string | Yes | Ordered currency amount
limitPrice | string | No | **???**
tradeIntent | integer | Yes | Possible values:<br/>0 - buy base currency, <br/>1 - sell base currency
modifier | string | No | Possible value: GTC (if ommitted, the same is used)

### Response Parameters

Parameter | Type | Required | Description
--------- | ----------- | ----------- | -----------
orderId | integer | Yes | Created trade order identifier


## Create Stop Order 

Method for stop order creation.

```shell
curl "http://example.com/api/v1/createStopOrder"
  -X POST
  -H "Authorization: meowmeowmeow"
  -H "Content-Type: application/json"
  -d '{"pairName": "KICK/ETH", "orderedAmount": "110.000", "limitPrice": "0.12", "tradeIntent": 0, "modifier": "GTC", "reserves": 0, "tpActivateLevel": "0.11", "tpLimitPrice": "0.15", "slLimitPrice": "0.10", "tpSubmitLevel": "0.14", "slSubmitLevel": "0.13"}'
```

> The above command returns JSON structured like this:

```json
{
	"orderId": 123456
}
```

### HTTP Request

`POST https://example.com/api/v1/createStopOrder`

### Request Parameters

Parameter | Type | Required | Description
--------- | ----------- | ----------- | -----------
pairName | string | Yes | Currency pair name *(ex: KICK/ETH)*
orderedAmount | string | Yes | Ordered currency amount
limitPrice | string | No | Should be provided if single stop order is created
tradeIntent | integer | Yes | Currently two possible values are accepted: <br/>0 - buying base currency <br/>1 - selling base currency
modifier | integer | No | Possible value: GTC (if ommitted, the same is used)
reserves | integer | No | 0 (default) - don't create reserves, <br/>1 - create reserves (valid only if limitPrice is set)
tpActivateLevel | string | No | Take profit activation level
tpLimitPrice | string | No | Take profit stop level
slLimitPrice | string | No | Stop loss limit price
tpSubmitLevel | string | No | Take profit submit level
slSubmitLevel | string | No | Stop loss submit level

### Response Parameters

Parameter | Type | Required | Description
--------- | ----------- | ----------- | -----------
orderId | integer | Yes | Created trade order identifier

# User 

This section covers methods getting information regarding user and his or her account on the Exchange.

<aside class="warning">
Authorization is needed to use these methods.
</aside>


## User Info 

Main user data.

```shell
curl "http://example.com/api/v1/userInfo"
```

> The above command returns JSON structured like this:

```json
{
	"userId": 111111111,
	"userName": "kickuser123",
	"platformName": "KickEX",
	"comment": "",
	"restrictions": 0
}
```

### HTTP Request

`GET https://example.com/api/v1/userInfo`

### URL Parameters

*None.*

### Response Parameters
Parameter | Type | Required | Description
--------- | ----------- | ----------- | -----------
userId | integer | Yes | User identifier
userName | string | No | User nickname
platformName | string | Yes | KickEX
comment | string | No | Human-readable (?) comment that may contain restrictions applied to using API by the user.
restrictions | integer | Yes | Attribute showing if trading is blocked for the user (0 - available, 1 - blocked)

## Balance

User balance data.

```shell
curl "http://example.com/api/v1/user/balance"
```

> The above command returns JSON structured like this:

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

### HTTP Request

`GET https://example.com/api/v1/user/balance`

### URL Parameters

*None.*

### Response Parameters
Parameter | Type | Required | Description
--------- | ----------- | ----------- | -----------
currencyName | string | Yes | Currency short name
balance | string | Yes | Total balance
available | string | Yes | Balance available for withdrawal
inOrders | string | Yes | Reserved balance
accountType | string | Yes | Account type:<br/> 2401 - ordinary account <br/> 2411 - Ecosystem account

## Deposit Address 

Method for getting personal deposit address.

```shell
curl "http://example.com/api/v1/depositAddresses?currencyName=USDT&chain=ERC20"
```

> The above command returns JSON structured like this:

```json
{
	"currencyName": "USDT",
	"chain": "ERC20",
	"memo": "123456",
	"address": "0xc0DAa9e14343128cd50f7b934B5Bb23eddd3F246"
}
```

### HTTP Request

`GET https://example.com/api/v1/depositAddresses?currencyName=USDT&chain=ERC20`

### URL Parameters

Parameter | Type | Required | Description
--------- | ----------- | ----------- | -----------
currencyName | string | Yes | Currency short name
chain | string | No | Blockchain short name: ERC20/OMNI/TRC20 (required for cryptocurrencies available in several blockchains)

### Response Parameters
Parameter | Type | Required | Description
--------- | ----------- | ----------- | -----------
currencyName | string | Yes | Currency short name
chain | string | No | Blockchain short name (required for cryptocurrencies available in several blockchains)
memo | string | No | Required for XRP
address | string | Yes | Address in specified blockchain

## Deposit History 

This method provides deposit history.

```shell
curl "http://example.com/api/v1/depositHistory?сurrencyName=KICK&startTime=1588015708&endTime=1588024908&status=success"
```

> The above command returns JSON structured like this:

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

### HTTP Request

`GET https://example.com/api/v1/depositHistory?сurrencyName=KICK&startTime=1588015708&endTime=1588024908&status=success`

### URL Parameters

Parameter | Type | Required | Description
--------- | ----------- | ----------- | -----------
сurrencyName | string | No | Currency short name
startTime | timestamp | No | Start time of the report
endTime | timestamp | No | End time of the report
status | string | No | Deposit status filtering (failure/success/processing)

### Response Parameters
Parameter | Type | Required | Description
--------- | ----------- | ----------- | -----------
address | string | Yes | Deposit address
amount | string | Yes | Deposit amount
fee | string | Yes | Deposit commission (if any)
currencyName | string | Yes | Currency short name
memo | string | No | Memo or tag (if present)
walletTxId | string | Yes | Transaction hash
status | string | Yes | Transaction status (failure/success/processing)
createdAt | timestamp | Yes | Database record creation timestamp
updatedAt | timestamp | Yes | Database record update timestamp
comment | string | No | Comment (if any)

## User tariff

Method that returns current user's discounts information

```shell
curl "http://example.com/api/v1/user/tariff"
```

> The above command returns JSON structured like this:

```json
{
	"maker": "0.0015",
	"taker": "0.0010",
	"msf": "0.5",
	"discount_regular_k": "0.2",
	"discount_special_k": "0.3",
	"discount_details": {
		"vol_30d": "500280.1223",
		"vol_30d_dk": "0.2",
		"min_1d_rest": "12532.00",
		"min_1d_rest_dk": "0.3",
		"min_1d_kex_rest": "19732.64",
		"min_1k_kex_rest_dk": "0.45",
		"min_1d_activity_rest": "9612.14",
		"min_1d_activity_rest_dk": "0.10"
	}
}
```

### HTTP Request

`GET https://example.com/api/v1/user/tariff`

### URL Parameters

*None.*

### Response Parameters
Parameter | Type | Required | Description
--------- | ----------- | ----------- | ----------- 
maker | string | Yes | maker fee (like 0.0015, not 0.15%)
taker | string | Yes | taker fee
msf | string | Yes | share of the fee that could be payed in broker's currency
discount_regular_k | string | Yes | discount factor when payed in quotes currency
discount_special_k | string | Yes | discount factor when payed in broker's currency
discount_details | object | Yes | object containing detailed information regarding gathered discounts conditions and amount of discounts (object structure below)
&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;vol_30d | string | Yes | trading volume in USDT for the previous 30 days
&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;vol_30d_dk | string | Yes | trading volume discount rate 
&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;min_1d_rest | string | Yes | balance of KICK (in USDT) on main account
&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;min_1d_rest_dk | string | Yes | KICK on main account discount rate
&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;min_1d_kex_rest | string | Yes | Minimum balance of KICK (in USDT) on Ecosystem account for the last 24 hours
&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;min_1k_kex_rest_dk | string | Yes | KICK on Ecosystem account discount rate
&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;min_1d_activity_rest | string | Yes | Minimum balance on Ecosystem account
&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;min_1d_activity_rest_dk | string | Yes | Discount on fees paid in broker's currency

## Withdraw (under construction)

Method for withdrawal request creation.

```shell
curl "http://example.com/api/v1/user/withdraw"
  -X POST
  -H "Authorization: meowmeowmeow"
  -H "Content-Type: application/json"
  -d '{"address":"0xc0DAa9e14343128cd50f7b934B5Bb23eddd3F246",
	   "memo":"",
	   "amount":"1000",
	   "currency":"KICK",
	   "chain":"ERC20"}'  
```

> The above command returns JSON structured like this:

```json
{
	"orderId": 123456
}
```

### HTTP Request


`POST https://example.com/api/v1/user/withdraw`

### Request Parameters

Parameter | Type | Required | Description
--------- | ----------- | ----------- | -----------
address | string | Yes | External address for withdrawal
memo | string | No | Memo tag
amount | string | Yes | Amount to withdraw
currency | string | Yes | Currency *(example: USDT)*
сhain | string | No | Blockchain (needed for currencies like USDT that are present in several blockchains)

### Response Parameters

Parameter | Type | Required | Description
--------- | ----------- | ----------- | -----------
orderId | integer | Yes | Created trade order identifier

## Withdrawal History 

Method to get withdrawal history.

```shell
curl "http://example.com/api/v1/withdrawalHistory?сurrencyName=KICK&startTime=1588015708&endTime=1588024908&status=success"
```

> The above command returns JSON structured like this:

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

### HTTP Request

`GET https://example.com/api/v1/withdrawalHistory?сurrencyName=KICK&startTime=1588015708&endTime=1588024908&status=success`

### URL Parameters

Parameter | Type | Required | Description
--------- | ----------- | ----------- | -----------
сurrencyName | string | No | Currency short name
startTime | timestamp | No | Start time of the report
endTime | timestamp | No | End time of the report
status | string | No | Withdrawal status filtering (failure/success/processing/wallet_processing)

### Response Parameters
Parameter | Type | Required | Description
--------- | ----------- | ----------- | -----------
transactionId | string | Yes | Unique identifier of withdrawal transaction
address | string | Yes | Withdrawal address
amount | string | Yes | Withdrawal amount
fee | string | Yes | Withdrawal commission
currencyName | string | Yes | Currency short name
memo | string | No | Memo or tag (if any)
walletTxId | string | Yes | Transaction hash
status | string | Yes | Transaction status (failure/success/processing)
createdAt | timestamp | Yes | Database record creation timestamp
updatedAt | timestamp | Yes | Database record update timestamp
comment | string | No | Comment (if any)
chain | string | No | Blockchain short name (required for cryptocurrencies available in several blockchains)


# WebSocket endpoint


## Common

>Sample request

```json
{
    "id": "dsncjksdnc",             // Request identifier
    "type": "someType",             // Request type
}
```

>Sample response (error)

```json
{
    "id": "dsncjksdnc",             // Request identifier
    "error": {                      // 
        "code": 29000,              // Error code
        "reason": "Error reason"    // Error description
    },
    "dev_error": {                  // Debugger level information
        "code": 5607,               // Internal error code
        "reason": "Internal reason" // Internal error description
    }
}
```

>Error (data exceeds limit)

```json
{
    "error": {"code": 19008, "reason": "answer is too big"}
}
```

WebSocket client should be connected to: [http://gate.kickex.com](http://gate.kickex.com)

Common part of all requests has two required attributes: **id** and **type**. 

**id** is a request identifier, unformatted string generated by the client. Supposed to be unique, the same identifier will be returned in the response. **id** length should be less than 10KB.

**type** is the type of the request.

In case of error server reponse contains only **id** and **error** object with error code and error reason (description). On development environment **dev_error** object is returned.

There is a special error in case when the server answer exceeds internal buffer limit or when client didn't manage to fetch the data. This response doesn't contain **id** field.

Usually getting this response means that one or more messages (including important messages regarding orders or user's balance) were lost.

There is a possibility to extend the buffer limit via the technical support.

If an invalid JSON was sent as a request, the response will contain **ER_MALFORMED_JSON** error and won't contain **id**.

### Gateway status

>Request
```json
{
    "id": "dsncjksdnc",             // Request identifier
    "type": "status",               // Request type
}
```

>Ответ

```json
{
    "id": "dsncjksdnc",
    "accounting": {                 // Accounting connection status
        "connected": true,
        "callbacks": 0
    },
    "kickid": {                     // KickID Tarantool connection status
        "connected": true,
        "callbacks": 0
    },
    "statistics": {                 // Statistics connection status
        "connected": true,
        "callbacks": 2
    },
    "walletbc": {                   // Wallet connection status
        "status_code": 0,
        "status": "CONNECTING"
    }
}
```


### Ping

Client should be sending the following **ping** to prevent closing the connection from the server. Connection gets closed if the client doesn't send any frames within 1 minute. Instead of **ping** request any other request coud be sent.

>Request

```json
{
    "type": "ping",                 // Request type
}
```

>Response

```json
{
    "pong": 0
}
```


## Orderbook API

### Get orderbook and subscribe

Responses by subscription.

Every new response is an incremental change of the orderbook. Strings that were changed since the previous update are contained in **bids** and **asks** arrays.

If **total** attribute contains an empty string then the line should be removed from the orderbook. Otherwise the line is added or updated.

>Request

```json
{
    "id": "dsncjksdnc",                 	// Request identifier
    "type": "getOrderBookAndSubscribe",     // Request type
    "pair": "KICK/ETH"
}
```

>Response

```json
{
    "id": "dsncjksdnc",                 // Request identifier
    "bids":[{
        "price": "152.7",               // Currency pair price
        "amount": "4545.3",             // Base currency amount
        "total": "455.22"               // Total (in quote currency)
    },
    ...],
 
    "asks": [{
        "price": "12.7",                // Currency pair price
        "amount": "485.3",              // Base currency amount
        "total": "4562.55"              // Total (in quote currency)
    }
    ...],
 
    "lastPrice":{
        "price": "4545,                 // Currency pair price
        "type": -1/0/1                  // -1 price became lower
                                        // 0 price didn't change
                                        // 1 price became higher
    }
}
```	

> Updated data

```json
{
    "id": "dihfbeibf",      			// Request identifier
    "bids":[{
        "price": "152.7",               // Currency pair price
        "amount": "4545.3",             // Base currency amount
        "total": "455.22"               // Total (in quote currency)
    },
    ...],
 
    "asks": [{
        "price": "12.7",                // Currency pair price
        "amount": "485.3",              // Base currency amount
        "total": "4562.55"              // Total (in quote currency)
    },
    {                                   // Line should be removed from the orderbook. Total is an empty string.
        "price": "12.7",                // Currency pair price
        "amount": "NaN",                // Base currency amount
        "total": ""                     // Total (in quote currency)
    }
    ...],
 
    "lastPrice":{
        "price": "4545,                 // Currency pair price
        "type": -1/0/1                  // -1 price became lower
                                        // 0 price didn't change
                                        // 1 price became higher
    }
}```

### Unsubscribe from updates

> Request (unsubscribe)

```json
{
    "id": "dsncjksdnc",                 // Request identifier
    "type": "unsubscribeOrderBook",     // Request type
}
```

>Response

```json
{
    "id": "dsncjksdnc",                 // Request identifier
}
```

## Currency Pairs

### Subscribe 

> Request (subscribe)

```json
{
    "id": "dsncjksdnc",             	// Request identifier
    "type": "getPairsAndSubscribe",     // Request type
}
```

>Ответы (по подписке)

```json
{
    "id": "dsncjksdnc",             	// Request identifier
    "pairs" : [
    {
        "baseCurrency": "ETH",          // Base currency
        "quoteCurrency": "BTC",         // Quote currency
        "price": "228.5698565",         // Currency pair price
        "price24hChange": "-1.5",       // Price change during last 24 hours
        "volume24hChange": "1258.55",   // Volume change during last 24 hours (in quote currency)
        "amount24hChange": "45.55",     // Amount change during last 24 hours (in base currency)
        ?"lowPrice24h": "220.0",        // Lowest price during last 24 hours
        ?"highPrice24h": "230.0",       // Highest price during last 24 hours
        "priceScale": 8,                // Order price precision
        "quantityScale": 4,             // Order quantity precision
        "volumeScale": 7,               // Order volume precision
        ?"minQuantity": "0.0001",       // Minimum quantity
        ?"minVolume": "0.0001",         // Minimum volume
        "state": 1                      // State
                                        // DRAFT    = 1, -- 1
                                        // REJECTED = 2, -- 2
                                        // BLOCKED  = 3, -- 3
                                        // COMMITED = 4  -- 4
 
    }
    , ... ]
}
```

### Unsubscribe

>Request (unsubscribe)

```json
{
    "id": "dsncjksdnc",                 // Request identifier
    "type": "unsubscribePairs",         // Request type
}
```

>Response

```
{
    "id": "dsncjksdnc",                 // Request identifier
}
```


## Trade history for currency pair

### Subscribe

>Request (subscribe)

```json
{
    "id": "dsncjksdnc",                 // Request identifier
    "type": "getHistoryAndSubscribe",   // Request type
    "pair": "KICK/ETH",                 // Currency pair identifier
}
```

>Response

```json
{
    "id": "dsncjksdnc", 			// Request identifier
    "history": [{
        price: 455.55,              // Currency pair price
        amount: 228.1488,           // Base currency amount
        timestamp: 156454565645,    // Trade timestamp
        type: "sell/buy" },         // Trade type (sell/buy)
        ...
    ]
}
```

First response contains full list of latest deals, the following responses contain incremental updates.


### Unsubscribe

> Request unsubscribe

```json
{
    "id": "dsncjlksdnc",                 // Request identifier
    "type": "unsubscribeHistory",       // Request type
}
```

>Ответ

```json
{
    "id": "dsncjksdnc",                 // Request identifier
}
```

