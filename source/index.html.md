---
title: Lean API Reference

language_tabs: # must be one of https://git.io/vQNgJ
  - shell

toc_footers:

search: true

code_clipboard: true
---

# Introduction

Welcome to the Lean API. Our REST API allows Lean’s partners to easily integrate Lean financial services into their payment flow to workers.

Partners can manage their organization settings and view platform usage from the [Admin Dashboard](http://admin.withlean.com).

## Schema

Requests must be sent via HTTPS using JSON. Responses will always be returned in JSON format. Dates and times are expected and returned as [ISO 8601](http://www.w3.org/TR/NOTE-datetime) formatted strings. 

All requests to the API must have URLs relative to the base API URL:

Sandbox: `https://app.staging.withlean.com/api`

Production: `https://app.withlean.com/api`

## Authentication

> To authorize, use this code:

```shell
curl "https://app.withlean.com/api" \
  -u <API-KEY>:
```

> Replace `<API-KEY>` with your API key and add the colon to prevent curl from asking for a password

Before using the Lean API, you will receive an API Key from Lean. Authentication to the API is performed via HTTP Basic Auth. Provide your API key as the basic auth username value. You do not need to provide a password.

For security in the production environment, Lean restricts the IP Addresses from which to accept requests related to each API Key. Prior to going live, you must specify all authorized IP Addresses to include. In the sandbox environment, there is no restriction on IP Addresses making requests.

It is your responsibility to safely store your API keys. Lean will not have access to your production API keys once handed over.

## Response

The Lean API uses HTTP response status codes to indicate the success or failure of your API request. You can expect the following status codes:

Status Code | Description
--------- | -------
200 | Request was successful
400 | Bad request
401 | Unauthorized request
404 | Resource not found
503 | Service is unavailable

# Customer

> An example customer object looks like:

```json
{
  "firstName": "Jane",
  "middleName": null,
  "lastName": "Doe",
  "birthday": "1980-01-01",
  "phoneNumber": "4088888888",
  "email": "janedoesit@example.com",
  "street": "45 Merryweather Lane",
  "street2": null,
  "city": "San Francisco",
  "state": "CA",
  "postalCode": "94110",
  "partnerUserId": "uid_444444444",
  "registrationDate": "2020-06-30",
  "createdAt": "2021-04-01T18:01:50.739Z",
  "updatedAt": "2021-04-04T22:06:01.766Z",
  "status": "ACTIVE"
}
```

The Customer API allows you to register workers from your platform who will receive payouts. The customer will not yet have a bank account or active Lean account until the customer finishes the onboarding application through the Lean mobile app. Only active customers can receive payments. In the staging environment, you can simulate application submittion using the `POST /application` endpoint.

| Property | Description |
| --------- | ----------- |
| firstName *string* | First (given) name of the person. Restricted to at most 40 [printable ASCII characters](https://en.wikipedia.org/wiki/ASCII#Printable_characters): alphanumeric, space, and simple punctuation. |
| middleName *string* | Middle name of the person, if given. Restricted to at most 40 [printable ASCII characters](https://en.wikipedia.org/wiki/ASCII#Printable_characters): alphanumeric, space, and simple punctuation. |
| lastName *string* | Last name (surname) of the person. Restricted to at most 40 [printable ASCII characters](https://en.wikipedia.org/wiki/ASCII#Printable_characters): alphanumeric, space, and simple punctuation. |
| birthday *string* | Date of birth of the person, ISO 8601 format ("YYYY-MM-DD"). |
| phoneNumber *string* | Phone number of the person. |
| email *string* | Email address of the person. Restricted to at most 50 characters. |
| street *string* | Street address (maximum 35 characters) |
| street2 *string* | Second line of street address, if given (maximum 35 characters) |
| city *string* | City (maximum 21 characters) |
| state *string* | U.S. State (two letter abbreviation) |
| postalCode *string* | Postal code (5-digit or 5+4 Zip Code for U.S. addresses). |
| status *string* | Current status of the customer: `PENDING`, `ACTIVE`, `INACTIVE`. The initial status of `PENDING` will change to `ACTIVE` when a customer has completed the onboarding and is ready to receive gig and special payments. If the customer’s account is deactivated, suspended or otherwise becomes unable to receive gig or special payments for any reason, the status will change to `INACTIVE`.|
| partnerUserId *string* | Your unique identifier of this user. |
| registrationDate *string* | Registration Date of the user on the partner’s platform, ISO 8601 format ("YYYY-MM-DD"). |
| createdAt *string* | Timestamp of object creation. ISO 8601 format. |
| updatedAt *string* | Timestamp of when object was last updated. ISO 8601 format. |

## Create a customer

> Sending the request:

```shell
curl POST "https://app.staging.withlean.com/api/customer" \
  -H 'Content-Type: application/json'
  -u <API-KEY>:
  -d '{
  "firstName": "Jane",
  "lastName": "Doe",
  "birthday": "1980-12-31",
  "phoneNumber": "4085008000",
  "email": "janedoe@gmail.com",
  "street": "700 Treehouse Drive",
  "city": "San Jose",
  "state": "CA",
  "postalCode": "95101",
  "partnerUserId": "uid_847834395730535",
  "registrationDate": "2020-01-01"
  }'
```

> The response containing the customer:

```json
{
  "firstName": "Jane",
  "middleName": null,
  "lastName": "Doe",
  "birthday": "1980-12-31",
  "phoneNumber": "4085008000",
  "email": "janedoe@gmail.com",
  "street": "700 Treehouse Drive",
  "street2": null,
  "city": "San Jose",
  "state": "CA",
  "postalCode": "95101",
  "partnerUserId": "uid_847834395730535",
  "registrationDate": "2020-01-01",
  "status": "PENDING",
  "createdAt": "2021-04-01T19:53:36.869Z",
  "updatedAt": "2021-04-01T19:53:36.869Z"
}
```

### Request

`POST https://app.withlean.com/api/customer`

### Parameters

| Name | Required | Description |
|-------|--------|-------|
| firstName *string* | Y | |
| middleName *string* | | |
| lastName *string* | Y | |
| birthday *string* | Y | |
| phoneNumber *string* | Y | Must be unique, otherwise an error will be returned. |
| email *string* | Y | Must be unique, otherwise an error will be returned. |
| street *string* | Y | |
| street2 *string* | | |
| city *string* | Y | |
| state *string* | Y | |
| postalCode *string* | Y | |
| partnerUserId *string* | Y | Must be unique, otherwise an error will be returned. |
| registrationDate *string* | Y | |

## Retrieve a customer

> Sending the request:

```shell
curl GET "https://staging.app.withlean.com/api/customer/:partnerUserId" \ 
-u <API-KEY>:
```

> The response containing the customer:

```json
{
  "firstName": "Jane",
  "middleName": null,
  "lastName": "Doe",
  "birthday": "1980-12-31",
  "phoneNumber": "4085008000",
  "email": "janedoe@gmail.com",
  "street": "700 Treehouse Drive",
  "street2": null,
  "city": "San Jose",
  "state": "CA",
  "postalCode": "95101",
  "partnerUserId": "uid_847834395730535",
  "registrationDate": "2020-01-01",
  "status": "PENDING",
  "createdAt": "2021-04-01T19:53:36.869Z",
  "updatedAt": "2021-04-01T19:53:36.869Z"
}
```

### Request

`GET https://app.withlean.com/api/customer/:partnerUserId`

# Application (staging only)

The Application API allows you to submit a Customer’s application for approval. Only once the application is approved will the Customer’s Lean account be opened and the account may receive payments. Please note that account approval can take up to a minute.

This API exists only in sandbox for testing purposes. In the live environment, each Lean customer must submit their application within the mobile app in order to comply with legal and regulatory requirements.

| Property | Description |
| --------- | ----------- |
| partnerUserId *string* | Your unique identifier of this user |
| status *string* | Current status of the application: `submitted`, `approved`, `rejected`. The initial status is `submitted` until the account has been fully vetted, then it becomes `approved`. In staging, all applications are `approved` and take roughly 1 minute to process. In production, this endpoint is not available (the customer submits their own application using the mobile app) therefore use the customer’s status to determine whether an account is active and ready. |

> An example application object looks like:

```json
{
  "partnerUserId": "uid_847834395730535",
  "status": "submitted"
}
```

## Submit an application

> Sending the request:

```shell
curl POST "https://app.withlean.com/api/application" \
  -u <API-KEY>:
  -H 'Content-Type: application/json'
  -d '{
    partnerUserID: "uid_847834395730535"
    }'
```

> The response containing the application:

```json
{
 "status": "submitted",  
 "partnerUserId": "uid_847834395730535"
}
```

### Request

`POST https://app.withlean.com/api/application`

### Parameters

| Name | Required | Description |
|-------|--------|-------|
| partnerUserId *string* | Y | |

# Gig

A gig is a unit of work that you are compensating for. Using this endpoint communicates to Lean that work was performed and a payout should be initiated to the worker.

| Property | Description |
| --------- | ----------- |
| id *string* | Unique identifier for this gig |
| partnerUserId *string* | Unique identifier of this user |
| totalAmount *string* | Total amount paid out to the user. This should be inclusive of all wages, tips, expenses and reimbursements. Must be a positive value with two decimal precision. |
| tips *string* | Total amount paid out to the user. This should be inclusive of all wages, tips, expenses and reimbursements. Must be a positive value with two decimal precision. |
| expenses *string* | Expenses to be reimbursed to the user. Must be equal or greater than 0. Currency type with two decimal precision. |
| description *string* | Description of the gig, which will be displayed to the user in the mobile app. Example: “SFO Airport to 410 Market Street”. |
| gigId *string* | Your own unique identifier of this gig. |
| startTime *string* | Start time of the gig, ISO 8601 format (YYYY-MM-DDTHH:mm:ss.sssZ). |
| endTime *string* | End time of the gig, ISO 8601 format (YYYY-MM-DDTHH:mm:ss.sssZ). |
| status *string* | Current status of the gig: pending, success, failed. The initial status is pending until the payment has been deposited into the customer's account, which then changes to success. Under rare circumstances, if the payment could not be completed and there is no remedy, the status will be failed. |
| invoiceId *string* | Identifier of the linked invoice. | 
| createdAt *string* | Timestamp of object creation. ISO 8601 format. |
| updatedAt *string* | Timestamp of when object was last updated. ISO 8601 format. |

## Create a gig

### Request

`POST https://app.withlean.com/api/gig`

> Sending the request:

```shell
curl POST "https://app.withlean.com/api/gig" \
  -u <API-KEY>:
  -H 'Content-Type: application/json'
  -d '{
  "partnerUserId": "uid_847834395730535",
  "totalAmount": "15.00",
  "description": "Gig Payout",
  "gigId": "gigId_94792357823953532",
  "startTime": "2021-03-01T21:04:04",
  "endTime": "2021-03-01T21:31:05",
  "tips": "3.00",
  "expenses": "2.00"
  }'
```

> The response containing the gig:

```json
{
  "id": "uid_847834395730535",
  "description": "Gig Payout",
  "totalAmount": "15.00",
  "startTime": "2021-03-01T21:04:04",
  "endTime": "2021-03-01T21:31:05",
  "gigId": "gigId_94792357823953532",
  "expenses": "2.00",
  "tips": "3.00",
  "status": "PENDING",
  "invoiceId": "invoice_5dSpWwt5aX2nAR8kBA0Xs",
  "createdAt": "2021-04-01T19:58:11.651Z",
  "updatedAt": "2021-04-01T19:58:11.651Z"
}
```

### Parameters

| Name | Required | Description |
|-------|--------|-------|
| partnerUserId *string* | Y | Unique identifier of this user. |
| totalAmount *string* | | Total amount paid out to the user. This should be inclusive of all wages, tips, expenses and reimbursements. Must be a positive value with two decimal precision. |
| tips *string* | Y | Amount of tips paid out to the user. Must be equal or greater than 0. Currency type with two decimal precision. |
| expenses *string* | Y | Expenses to be reimbursed to the user. Must be equal or greater than 0. Currency type with two decimal precision. |
| description *string* | Y | Description of the gig, which will be displayed to the user in the mobile app. Example: “SFO Airport to 410 Market Street”. |
| gigId *string* | Y | Your own unique identifier of this gig. Must be unique, otherwise an error will be returned. |
| startTime *string* | Y | Start time of the gig, ISO 8601 format (YYYY-MM-DDTHH:mm:ss.sssZ). Use UTC standard. |
| endTime *string* | Y | End time of the gig, ISO 8601 format (YYYY-MM-DDTHH:mm:ss.sssZ). Use UTC standard. |

## Get a gig

### Request

> Sending the request:

```shell
curl GET "https://staging.app.withlean.com/api/gig/:gigId" \ 
-u <API-KEY>:
```

Returns a JSON object containing a gig object related to the given `gigID`.

`GET https://app.withlean.com/api/gig/:gigId`

## List gigs

> Sending the request:

```shell
curl GET "https://staging.app.withlean.com/api/gig" \ 
-u <API-KEY>:
```

Returns a JSON array containing a list of specialpayment objects.

`GET https://app.withlean.com/api/gig`

# Special Payment

A special payment is an ad hoc payment that is made to or from the customer that may or may not be related to a specific gig. For example, use this endpoint for managing reversals, cancellations, wait time, bonuses, reimbursements or any number of use cases.

| Property | Description |
| --------- | ----------- |
| id *string* | Unique identifier for this gig |
| partnerUserId *string* | Unique identifier of this user |
| amount *string* | Total amount paid to or collected from the user. |
| type *string* | One of: `bonus`, `reimbursement`, `cancellation`, `wait time`, `correction`, `flagged`, or `other`. |
| description *string* | Description of the gig, which will be displayed to the user in the mobile app. Example: “Parking receipt from 3/1/2021”. |
| gigId *string* | Unique identifier of the linked gig. |
| status *string* | Current status of the special payment: `pending`, `success`, `failed`. The initial status is `pending` until the payment has been deposited into the customer's account, which then changes to `success`. Under rare circumstances, if the payment could not be completed and there is no remedy, the status will be `failed`. |
| userdata *object* | JSON object for storing additional information. |
| invoiceId *string* | Identifier of the linked invoice. |
| createdAt *string* | Timestamp of object creation. ISO 8601 format. |
| updatedAt *string* | Timestamp of when object was last updated. ISO 8601 format. |

## Idempotency Header

The Special Payment API supports idempotency to prevent unintended duplicate payments. Unlike the Gig API, which requires a unique identifier, the Special Payment API does not require a unique identifier in the POST request. Passing the Idempotency Key is optional, but recommended. The key is checked for uniqueness within the prior 24 hours.

To make an idempotent request, provide an additional `Idempotency-Key: <key>` header to the request. Subsequent requests made within 24 hours with the same key return the same result as the original request.

## Create a special payment

### Request

`POST https://app.withlean.com/api/specialpayment`

> Sending the request:

```shell
curl POST "https://app.withlean.com/api/specialpayment" \
  -u <API-KEY>:
  -H 'Content-Type: application/json'
  -d '{
  "partnerUserId": "uid_847834395730535",
  "amount": "4.56",
  "type": "reimbursement",
  "gigId": "gigId_94792357823953532",
  "description": "Parking receipt from 3/1/2021",
  "userdata": { "receiptId": "receipt_4242551" }
  }'
```

> The response containing the special payment:

```json
{ 
  "id": "special_payment_9384127512", 
  "type": "reimbursement", 
  "invoiceId": "invoice_3957103571", 
  "description": "Parking receipt from 3/1/2021", 
  "amount": "4.56", 
  "createdAt": "2021-04-13T02:53:37.341Z", 
  "updatedAt": "2021-04-13T02:53:37.341Z", 
  "partnerUserId": "uid_847834395730535", 
  "gigId": "gigId_94792357823953532", 
  "userdata": { "receiptId": "receipt_4242551" }, 
  "status": "PENDING" 
}
```

### Parameters

| Name | Required | Description |
|-------|--------|-------|
| partnerUserId *string* | Y | Unique identifier of this user. |
| amount *string* | Y | Total amount to pay or collect from the user. Use a value with two decimal precision. A positive value will pay out to the user. A negative value will deduct funds from the user in cases such as a correction or reversal is needed. A negative amount presents as a credit to the invoice. |
| type *string* | Y | One of: `bonus`, `reimbursement`, `cancellation`, `wait time`, `correction`, `flagged`, `other` |
| gigId *string* | Y | Optional; Unique identifier of the linked gig. This field is validated that the gig was previously created. |
| description *string* |  | Description of the gig, which will be displayed to the user in the mobile app. Example: “Parking receipt from 3/1/2021”. |
| userdata *object* |  | Optional; JSON object for storing additional information. |

## Get a special payment

### Request

> Sending the request:

```shell
curl GET "https://staging.app.withlean.com/api/specialpayment/:id" \ 
-u <API-KEY>:
```

Returns a JSON object containing a specialpayment object related to the given `id`.

`GET https://app.withlean.com/api/specialpayment/:id`

## List special payments

> Sending the request:

```shell
curl GET "https://staging.app.withlean.com/api/specialpayment" \ 
-u <API-KEY>:
```

Returns a JSON array containing a list of specialpayment objects.

### Request

`GET https://app.withlean.com/api/specialpayment`

# Webhooks

Lean uses webhooks to notify you in real-time when an event occurs, in the form of HTTP POST payload. To begin receiving webhooks, you must register a URL with Lean. If a valid HTTP response is not received, the API will retry up to 5 times.

The supported events are:

`customer.active`

`customer.inactive`

`gig.completed`

`gig.failed`

`specialpayment.completed`

`specialpayment.failed`

## Verification

> Example of how to verify the signature with NodeJS:

```javascript
const express = require('express')
const crypto = require('crypto')
const app = express()
const port = 4000
app.post('/my-webhook', (request, response) => {
    response.send('request passed signature validation...')
})
app.use(express.json());
app.use(express.urlencoded({
    extended: true
}));
app.use((request, response, next) => {
    var signature = request.header("x-lean-signature")
    var hmac = crypto.createHmac('sha1', <api-key>)
    if(hmac.digest('base64') == signature) {
        next()
    }
    else {
        response.status(500).send("Signatures didn't match!")
    }
})
app.listen(port, (err) => {
    console.log(`server is listening on ${port}`)
})
```

When Lean sends a webhook, the payload is authenticated with a HMAC. The key used to create the HMAC is your api key, and you verify it by running the HMAC-SHA1 algorithm yourself with payload and the key to re-create the HMAC. Lean's computed HMAC is attached in the `x-lean-signature` request header.
