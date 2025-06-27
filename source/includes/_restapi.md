# REST API

## Authentication

### Endpoint security type

API requests are likely to be tampered during transmission through the internet. To ensure that the request remains unchanged, all private interfaces other than public interfaces (basic information, market data) must be verified by signature authentication via API-KEY to make sure the parameters or configurations are unchanged during transmission.

Each created API-KEY need to be assigned with appropriate permissions in order to access the corresponding interface. Before using the interface, users is required to check the permission type for each interface and confirm there is appropriate permissions.

| Authentication Type | Description                                              |
|:--------------------| :------------------------------------------------------- |
| NONE                | Endpoints are freely accessible                          |
| TRADE               | Endpoints requires sending a valid API-KEY and signature |
| USER_DATA           | Endpoints requires sending a valid API-KEY and signature |
| USER_STREAM         | The endpoints requires sending a valid API-KEY           |
| MARKET_DATA         | The endpoints requires sending a valid API-KEY           |

### Signature Authentication

#### Signature (TRADE & USER_DATA)

* The SIGNED (signature required) endpoint needs to send a parameter, signature, in the **query string** or **request body**.

* The endpoint is signed with HMAC SHA256. The HMAC SHA256 signature is the result of **HMAC SHA256** encryption of the key. Use your secretKey as the key and totalParams as the value to complete this encryption process.

* Signature is not case sensitive.

* totalParams refers to concatenation of the query string and the request body.

All HTTP requests to API endpoints require authentication and authorization. The following headers should be added to all HTTP requests:

| Key         | Value   | Type   | Description                        |
| ----------- | ------- | :----- | ---------------------------------- |
| X-HK-APIKEY | API-KEY | string | The API Access Key you applied for |

#### Time-base security requirement

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

### Getting Started with Postman

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
   ![](images/Authentication/postman1.png)
2. Add in the header "X-HK-APIKEY"
   ![](images/Authentication/postman2.png)
3. Add in params for the required endpoint and most important the timestamp and signature
   ![](images/Authentication/postman3.png)
4. Insert the Pre-request script in the above run the API request and press "Send"
   ![](images/Authentication/postman4.png)
