# Coupon Redemption API for Sovendus Voucher Network and Checkout Benefits

## Introduction

As Sovendus relies on tracking all coupon redemptions for the distributed codes to provide its services, it is crucial to make this process as robust as possible against external influences.

For this purpose, Sovendus provides an API for coupon redemptions.

## Key Advantages of API Integration

- **Reliable Tracking:** No tracking deviations with correct integration
- **Resilient Solution:** Not affected by browsers, ad blockers, or script errors
- **Data Efficiency:** Only necessary data for billing purposes is transmitted
- **Reduced Workload:** No manual reconciliation efforts needed
- **Improved Performance:** Sovendus algorithms work with accurate data, benefiting your ranking
- **Optimized Processing:** API calls occur only when a voucher code is used for a purchase

## Recommended Integration Points

The API call should be made at the moment of purchase completion:

- When the "Order" button is clicked in your shop
- After successfully completing a transaction in your shop system/app
- During data export/transmission to a connected ERP system
- When inserting the transaction into your shop system's 'Order DB'

> **Important:** The API call must happen just-in-time with the order and not be deferred, such as during a monthly reconciliation.

## Technical Implementation

### Access Data (Example)

Your Sovendus representative will provide you with the following credentials:

| Key                      | Value                                |
| ------------------------ | ------------------------------------ |
| trafficSourceNumber      | 1234                                 |
| traffic_medium_public_id | ce5c91d3-1ed9-46dc-ab5b-1c0b117cb874 |

### API Endpoint

POST request to `https://coupon-api.sovendus.com/redeem/{trafficSourceNumber}`

### Required Headers

| Header         | Value              |
| -------------- | ------------------ |
| Content-Type   | application/json   |

### Parameters

| Parameter                | Description                                                                                                                       | Mandatory                                                                              |
| ------------------------ | --------------------------------------------------------------------------------------------------------------------------------- | ---------------------------------------------------------------------------------------|
| email_hash               | Hash value of the customer’s email, used for unique identification of the redemption. Calculated as: md5(lowercase(trim(e-mail))) | Optional to identify recurring users, ensuring they receive varied offers              |
| traffic_medium_public_id | Integration ID provided by Sovendus                                                                                               | Optional to determine the traffic medium channel used for statistics                   |
| order_value              | Net order value excluding VAT and shipping                                                                                        | Required for percentage-based billing, otherwise optional                              |
| order_currency           | Currency of the order, ISO code (e.g., EUR, GBP)                                                                                  | Required for percentage-based billing, otherwise optional                              |
| order_id                 | Order number                                                                                                                      | Required in case the voucher code is not unique for each customer, otherwise optional  |
| coupon_code              | Voucher code used during the order                                                                                                | Required                                                                               |

### Response

| Description                                                 | Response Code | Response Body                             |
| ----------------------------------------------------------- | ------------- | ----------------------------------------- |
| Success                                                     | 200           | {"message":"Coupon redemption received."} |
| Incorrect Traffic Source Number or Traffic Medium Public ID | 500           | {"error":"Internal error"}                |
| Traffic Source Number Format                                | 404           | n.a.                                      |
| Missing Code / malformed Email Hash                         | 422           | {"error":"Input validation failed."}      |
| Malformed Traffic Medium Public ID                          | 422           | {"error":"Input validation failed."}      |
| Incorrect Order Value/Order Currency Format                 | 422           | {"error":"Input validation failed."}      |

### Example Post Request

#### Example URL

`https://coupon-api.sovendus.com/redeem/1234`

#### Example Body

```json
{
  "email_hash": "09a3c337b6587938d2253ac1462d7e13",
  "traffic_medium_public_id": "e59c9966-f9ad-477b-8df8-826fb9a690f7",
  "order_value": "12.34",
  "order_currency": "EUR",
  "order_id": "123abc123",
  "coupon_code": "SKDXXR4324"
}
```

## F.A.Q

### Does the API need to be called for every order?

No, the call should only be made if a voucher code is used for the order. We will handle the distinction whether it is a code issued by Sovendus.

### I use other distribution channels in addition to my online shop, how should I proceed?

It depends on the integration point. For example, if the app and the shop are two separate platforms, you can integrate the call where the transactions converge. The same principle applies to orders via a hotline.

### I have previously integrated the Sovendus script, will redemptions be double-billed in the future?

No, redemptions will either go through our API or the script in our system. After a short testing period, including reconciliation of your numbers, we will switch tracking to the API.
