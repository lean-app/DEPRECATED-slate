---
title: Lean API Docs

language_tabs: # must be one of https://git.io/vQNgJ
  - shell

toc_footers:

search: true

code_clipboard: true
---

# Get Started

## Introduction

Lean is a fintech and benefits platform allowing marketplaces to unlock financial benefits for their workforce, such as instant pay, accounts, credit and more. You can instantly send funds to your workers' Lean account with no transaction cost. Pay workers for any purpose you'd like, such as after a delivery is completed, a bonus is earned or for business expenses.

This documentation should serve as a guide to help you through the process of building the first integration. If you have questions, ideas, or needs please reach out to us at [support@withlean.com](support@withlean.com)

Partners can manage their organization settings and view platform usage from the [Admin Dashboard](http://admin.withlean.com).

View the complete API reference documentation here: [Lean API reference](https://admin.withlean.com/docs)

## What does Lean do

Lean handles everything behind-the-scenes, from opening bank accounts, issuing cards, funding payouts, offering financial products and handling customer support. In addition, workers get onboarded to the Lean app, available on Android and iOS, where they can access all the benefits and services. Getting up and running with Lean takes just days and a few lines of code.

## How It Works

There are three steps to integrate with Lean:

1. Notify workers about Lean
2. Tell Lean about worker details
3. Start paying workers through Lean

### Notify workers about Lean

We will support you in sharing the option to get paid with Lean in whichever channel you communicate with your workers: in-app, web, email, SMS, chat, social media or anywhere else.

Workers are asked to install the Lean app, available for Android and iOS, to get started.

### Tell Lean about worker details

> One call to Lean customer API:

```shell
curl POST "https://app.withlean.com/api/customer" \
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

You will tell Lean which workers are interested to get paid with Lean by calling the customer endpoint. One call is all you need to inform Lean about the profile details and we handle the rest. We recommend making this call at the same time as notifying workers, to ensure a smooth on-boarding process. A customer will still need to confirm their details from the Lean app before they can receive any payouts.


### Activate Lean payouts for the worker

The worker is required to complete their registration in the Lean app. This takes less than 2 minutes for the worker, and upon an account being opened, Lean will send a `customer.active` webhook to know when to switch the worker's payout method.

You can start making payouts immediately, using our `gig` and `specialpayment` endpoints.

> Making a gig payout:

```shell
curl POST "https://app.withlean.com/api/gig" \
  -u <API-KEY>:
  -H 'Content-Type: application/json'
  -d '{
  "partnerUserId": "uid_847834395730535",
  "totalAmount": "30.00",
  "description": "Job #481254",
  "gigId": "gigId_94792357823953532",
  "startTime": "2021-03-01T21:04:04",
  "endTime": "2021-03-01T21:31:05",
  "tips": "3.00",
  "expenses": "2.00"
  }'
```

> Making a special payment payout:

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

*Gig*: These are a unit of work where you can specify the amount to be paid, time period, breakdown and additional metadata.

*Special Payment*: These are ad hoc payments for any purpose, such as a bonus, wait time, cancellation and more. They can be linked or not linked to a gig. You can also pass a negative number in the event of making a correction to a prior payout.
