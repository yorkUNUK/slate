---
title: HashKey API Reference

language_tabs: # must be one of https://github.com/rouge-ruby/rouge/wiki/List-of-supported-languages-and-lexers
  - shell
  - java
  - python
  - javascript
  - ruby

toc_footers:
  - <a href='https://hk.sim.hashkeydev.com' target='_blank'>Try it out in sandbox</a>

includes:
  - restapi
  - errors

search: true

code_clipboard: true

meta:
  - name: description
    content: Documentation for the HashKey API
---

# Overview

## Introduction

**Welcome to HashKey Exchange API Documentation!**

HashKey Exchange offers a comprehensive suite of APIs including FIX, REST and Websocket API tailored to cater the needs of institutional-grade clients. Our Exchange API solutions are highly scalable, providing clients with access to real-time market data feeds and insights. Client can easily integrate our Exchange API with their existing systems and applications, enabling them to streamline their workflows.

Exchange offers three types of API Connectivity methods

### RestAPI

Our REST API offers a simple and secure way to manage orders and monitor your digital asset portfolios. The REST API can be publicly access endpoints via market data endpoints and private authenticated endpoints for trading, funding and account data which require requests to be signed.

### Websocket

Use an asynchronous method (pub/sub) approach to efficiently deliver push notifications for orders, transactions, market and other pertinent information. By subscribing to specific topics of interest, users can receive real-time updates without the need for constant polling.

### FIX Protocol (Current available to our Omnibus institutional clients)

HashKey Exchange FIX connection is an industry-standard solution for our institutional clients to take advantage of our high availability system. It is specifically engineered to handle large amount of throughput supporting end-to-end order entry, order management and access to our rich liquidity feeds.

## Test our sandbox

Please direct any questions or feedback to \[[api@hashkey.com](mailto:api@hashkey.com)]

📘 All 2FA/SMS/Email verification code is defaulted to "123456" in Sandbox environment for ease of use

### 1. Go to our sandbox website page:

📘 [hk.sim.hashkeydev.com](https://hk.sim.hashkeydev.com)

![](images/TestSandbox/sandbox1.png)

### 2. To register a new account, simply click on "Sign Up Now" button. If you already have an account, you can log in using your credentials

![](images/TestSandbox/sandbox2.png)

### 3. Activate your account

To activate your account, please reach out to our customer support team via Live Chat. Our team will assist you in the activation process.

![](images/TestSandbox/sandbox3.png)

Our dedicated team is here to assist you with the following

* Completing the KYC verification process
* Granting the necessary API permission for your account
* Providing test coins to your account for testing purposes

### 4. Go to Settings ->API Management -> Create API

![](images/TestSandbox/sandbox4.png)

### 5. Enter API Key name, select API permission and setup IP Access Restriction

![](images/TestSandbox/sandbox5.png)

## Technical Support

### General Inquiry

Operating hours: Monday to Friday, 9:00 AM to 6:00 PM HKT

Preferred method of contact： [api@hashkey.com](mailto:api@hashkey.com)

Please provide your inquiry in the following format:

Subject:

Environment: Production / Sandbox Inquiry

Identity: UID / Email

Request Body:

Question:

### Emergency Production Trading issue

Operating hours: 7 * 24

For urgent matters during non-office hours, please log in to hashkey.com and contact our online Customer Support team via instant message widget.

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


