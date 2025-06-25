---
title: API Reference

language_tabs: # must be one of https://github.com/rouge-ruby/rouge/wiki/List-of-supported-languages-and-lexers
  - shell
  - ruby
  - python
  - javascript

toc_footers:
  - <a href='#'>Sign Up for a Developer Key</a>
  - <a href='https://github.com/slatedocs/slate'>Documentation Powered by Slate</a>

includes:
  - errors

search: true

code_clipboard: true

meta:
  - name: description
    content: Documentation for the Kittn API
---

# Introduction

Welcome to the Kittn API! You can use our API to access Kittn API endpoints, which can get information on various cats, kittens, and breeds in our database.

We have language bindings in Shell, Ruby, Python, and JavaScript! You can view code examples in the dark area to the right, and you can switch the programming language of the examples with the tabs in the top right.

This example API documentation page was created with [Slate](https://github.com/slatedocs/slate). Feel free to edit it and use it as a base for your own API's documentation.

# Authentication

## Endpoint security type

API requests are likely to be tampered during transmission through the internet. To ensure that the request remains unchanged, all private interfaces other than public interfaces (basic information, market data) must be verified by signature authentication via API-KEY to make sure the parameters or configurations are unchanged during transmission.

Each created API-KEY need to be assigned with appropriate permissions in order to access the corresponding interface. Before using the interface, users is required to check the permission type for each interface and confirm there is appropriate permissions.

| Authentication Type | Description                                              |
|:--------------------| :------------------------------------------------------- |
| NONE                | Endpoints are freely accessible                          |
| TRADE               | Endpoints requires sending a valid API-KEY and signature |
| USER_DATA           | Endpoints requires sending a valid API-KEY and signature |
| USER_STREAM         | The endpoints requires sending a valid API-KEY           |
| MARKET_DATA         | The endpoints requires sending a valid API-KEY           |

## Signature Authentication

### Signature (TRADE & USER_DATA)

* The SIGNED (signature required) endpoint needs to send a parameter, signature, in the **query string** or **request body**.

* The endpoint is signed with HMAC SHA256. The HMAC SHA256 signature is the result of **HMAC SHA256** encryption of the key. Use your secretKey as the key and totalParams as the value to complete this encryption process.

* Signature is not case sensitive.

* totalParams refers to concatenation of the query string and the request body.

All HTTP requests to API endpoints require authentication and authorization. The following headers should be added to all HTTP requests:

| Key         | Value   | Type   | Description                        |
| ----------- | ------- | :----- | ---------------------------------- |
| X-HK-APIKEY | API-KEY | string | The API Access Key you applied for |

### Time-base security requirement

>📘 If your timestamp is ahead of serverTime it needs to be within 1 second + serverTime

>The logic of this parameter is as follows:

```java
 if (timestamp < (serverTime + 1000) && (serverTime - timestamp) <= recvWindow)
    // process request
  } else {
    // reject request
  }
```

>📘 A relatively small recvWindow (5000 or less) is recommended!

* For a SIGNED endpoint, an additional parameter "timestamp" needs to be included in the request. This timestamp is in milliseconds and reflect the time when the request was initiated.
* An optional parameter (not mandatory) `recvWindow` can be used to specify the validity period of the request in milliseconds. If recvWindow is not sent as part of the request, the default value is **5000**
* Trading and timeliness are closely interconnected. Network can sometimes be unstable or unreliable, which can lead to inconsistent times when requests are sent to the server.
* With recvWindow, you can specify how many milliseconds the request is valid, otherwise it will be rejected by the server.

### Example 1: In queryString

* queryString: 

`symbol=ETHBTC&side=BUY&type=LIMIT&timeInForce=GTC&quantity=1&price=0.1&recvWindow=5000&timestamp=1538323200000`

* HMAC SHA256 signature:

`echo -n "symbol=ETHBTC&side=BUY&type=LIMIT&timeInForce=GTC&quantity=1&price=0.1&recvWindow=5000&timestamp=1538323200000" | openssl dgst -sha256 -hmac "lH3ELTNiFxCQTmi9pPcWWikhsjO04Yoqw3euoHUuOLC3GYBW64ZqzQsiOEHXQS76"`

Shell standard output: 

`5f2750ad7589d1d40757a55342e621a44037dad23b5128cc70e18ec1d1c3f4c6`

* curl command:

`curl -H "X-HK-APIKEY: tAQfOrPIZAhym0qHISRt8EFvxPemdBm5j5WMlkm3Ke9aFp0EGWC2CGM8GHV4kCYW" -X POST 'https://$HOST/api/v1/spot/order?symbol=ETHBTC&side=BUY&type=LIMIT&timeInForce=GTC&quantity=1&price=0.1&recvWindow=5000&timestamp=1538323200000&signature=5f2750ad7589d1d40757a55342e621a44037dad23b5128cc70e18ec1d1c3f4c6'`

### Example 2: In the request body

* requestBody: 

`symbol=ETHBTC&side=BUY&type=LIMIT&timeInForce=GTC&quantity=1&price=0.1&recvWindow=5000&timestamp=1538323200000`

* HMAC SHA256 signature:

`echo -n "symbol=ETHBTC&side=BUY&type=LIMIT&timeInForce=GTC&quantity=1&price=0.1&recvWindow=5000&timestamp=1538323200000" | openssl dgst -sha256 -hmac "lH3ELTNiFxCQTmi9pPcWWikhsjO04Yoqw3euoHUuOLC3GYBW64ZqzQsiOEHXQS76"`

Shell standard output: 

`5f2750ad7589d1d40757a55342e621a44037dad23b5128cc70e18ec1d1c3f4c6`

* curl command:

`curl -H "X-HK-APIKEY: tAQfOrPIZAhym0qHISRt8EFvxPemdBm5j5WMlkm3Ke9aFp0EGWC2CGM8GHV4kCYW" -X POST 'https://$HOST/api/v1/spot/order' -d 'symbol=ETHBTC&side=BUY&type=LIMIT&timeInForce=GTC&quantity=1&price=0.1&recvWindow=5000&timestamp=1538323200000&signature=5f2750ad7589d1d40757a55342e621a44037dad23b5128cc70e18ec1d1c3f4c6'`

### Example 3: mixing queryString and request body

* queryString:

`symbol=ETHBTC&side=BUY&type=LIMIT&timeInForce=GTC`

* requestBody:

`quantity=1&price=0.1&recvWindow=5000×tamp=1538323200000`

* HMAC SHA256 signature:

`echo -n "symbol=ETHBTC&side=BUY&type=LIMIT&timeInForce=GTCquantity=1&price=0.1&recvWindow=5000&timestamp=1538323200000" | openssl dgst -sha256 -hmac "lH3ELTNiFxCQTmi9pPcWWikhsjO04Yoqw3euoHUuOLC3GYBW64ZqzQsiOEHXQS76"`

Shell standard output: 

`885c9e3dd89ccd13408b25e6d54c2330703759d7494bea6dd5a3d1fd16ba3afa`

* curl command:

`curl -H "X-HK-APIKEY: tAQfOrPIZAhym0qHISRt8EFvxPemdBm5j5WMlkm3Ke9aFp0EGWC2CGM8GHV4kCYW" -X POST 'https://$HOST/openapi/v1/order?symbol=ETHBTC&side=BUY&type=LIMIT&timeInForce=GTC' -d 'quantity=1&price=0.1&recvWindow=5000&timestamp=1538323200000&signature=885c9e3dd89ccd13408b25e6d54c2330703759d7494bea6dd5a3d1fd16ba3afa'`

<aside class="notice">
Note the difference in Example 3, where there is no & between "GTC" and "quantity = 1".
</aside>

## Getting Started with Postman

Postman is a popular plug and play API test environment that allows implementing and testing REST and API endpoints.

Postman provides the HTTP networking to connect to the REST API, allows the HTTP header and GET/POST data to be customised and allows for custom code to be executed using Javascript variant.

> Pre-request Script

```java
var timestamp = new Date().getTime().toString();
pm.environment.set("timestamp", timestamp);

var api_secret = pm.environment.get("secretKey");
var parameters = pm.request.url.query.toObject(excludeDisabled=true);

var paramsObject = {};

Object.keys(parameters).forEach((paramKey) => {
  var paramValue = parameters[paramKey];
  var disabled = false;

  if (paramKey !== 'signature' && paramValue && !disabled) {
    paramsObject[paramKey] = paramValue;
  }
});

paramsObject.timestamp = timestamp;

var requestString = Object.keys(paramsObject).map((key) => `${key}=${paramsObject[key]}`).join('&');

var signature = CryptoJS.HmacSHA256(requestString, api_secret).toString();
pm.environment.set("signature", signature);
```

1. Create a new environment and add environment variables

![](https://files.readme.io/594b27d-image.png)

2. Add in the header "X-HK-APIKEY"

![](https://files.readme.io/c187483-image.png)

3. Add in params for the required endpoint and most important the timestamp and signature

<Image align="center" src="https://files.readme.io/d653c6c-image.png" />

4. Insert the Pre-request script in the above run the API request and press "Send"

![](https://files.readme.io/bb08972-image.png)

# Kittens

## Get All Kittens

```ruby
require 'kittn'

api = Kittn::APIClient.authorize!('meowmeowmeow')
api.kittens.get
```

```python
import kittn

api = kittn.authorize('meowmeowmeow')
api.kittens.get()
```

```shell
curl "http://example.com/api/kittens" \
  -H "Authorization: meowmeowmeow"
```

```javascript
const kittn = require('kittn');

let api = kittn.authorize('meowmeowmeow');
let kittens = api.kittens.get();
```

> The above command returns JSON structured like this:

```json
[
  {
    "id": 1,
    "name": "Fluffums",
    "breed": "calico",
    "fluffiness": 6,
    "cuteness": 7
  },
  {
    "id": 2,
    "name": "Max",
    "breed": "unknown",
    "fluffiness": 5,
    "cuteness": 10
  }
]
```

This endpoint retrieves all kittens.

### HTTP Request

`GET http://example.com/api/kittens`

### Query Parameters

Parameter | Default | Description
--------- | ------- | -----------
include_cats | false | If set to true, the result will also include cats.
available | true | If set to false, the result will include kittens that have already been adopted.

<aside class="success">
Remember — a happy kitten is an authenticated kitten!
</aside>

## Get a Specific Kitten

```ruby
require 'kittn'

api = Kittn::APIClient.authorize!('meowmeowmeow')
api.kittens.get(2)
```

```python
import kittn

api = kittn.authorize('meowmeowmeow')
api.kittens.get(2)
```

```shell
curl "http://example.com/api/kittens/2" \
  -H "Authorization: meowmeowmeow"
```

```javascript
const kittn = require('kittn');

let api = kittn.authorize('meowmeowmeow');
let max = api.kittens.get(2);
```

> The above command returns JSON structured like this:

```json
{
  "id": 2,
  "name": "Max",
  "breed": "unknown",
  "fluffiness": 5,
  "cuteness": 10
}
```

This endpoint retrieves a specific kitten.

<aside class="warning">Inside HTML code blocks like this one, you can't use Markdown, so use <code>&lt;code&gt;</code> blocks to denote code.</aside>

### HTTP Request

`GET http://example.com/kittens/<ID>`

### URL Parameters

Parameter | Description
--------- | -----------
ID | The ID of the kitten to retrieve

## Delete a Specific Kitten

```ruby
require 'kittn'

api = Kittn::APIClient.authorize!('meowmeowmeow')
api.kittens.delete(2)
```

```python
import kittn

api = kittn.authorize('meowmeowmeow')
api.kittens.delete(2)
```

```shell
curl "http://example.com/api/kittens/2" \
  -X DELETE \
  -H "Authorization: meowmeowmeow"
```

```javascript
const kittn = require('kittn');

let api = kittn.authorize('meowmeowmeow');
let max = api.kittens.delete(2);
```

> The above command returns JSON structured like this:

```json
{
  "id": 2,
  "deleted" : ":("
}
```

This endpoint deletes a specific kitten.

### HTTP Request

`DELETE http://example.com/kittens/<ID>`

### URL Parameters

Parameter | Description
--------- | -----------
ID | The ID of the kitten to delete

# EXAMPLE


