---
title: API Reference

language_tabs: # must be one of https://git.io/vQNgJ
  - shell

toc_footers:
  - Contact your business development <br> manager to get an API key.

search: true
---

# Introduction

Welcome to the Bestow Quote API. Users of the API can get price quotes for Bestow life insurance products by passing in a few customer parameters.

# Authentication

> To authorize, use this code:

```shell

curl -X POST \
  https://api.hellobestow.com/v2/quote \
  -H 'Authorization: 82zZIHeBqUlBtICMX5li' \
  -H 'Content-Type: application/json' \
```

> Make sure to replace `82zZIHeBqUlBtICMX5li` with your API key.

Bestow uses API keys to allow access to the API. Contact your business development manager to get an API key.

Bestow expects for the API key to be included in all API requests to the server in a header that looks like the following:

`Authorization: 82zZIHeBqUlBtICMX5li`

<aside class="notice">
You must replace <code>82zZIHeBqUlBtICMX5li</code> with your personal API key.
</aside>

# Quote

## Creating a Quote

Every insurance policy begins with a quote. You can think of quotes as templates that eventually turn into policies if a user decides to buy. Quotes are used to indicate the terms of the policy and the price (premium) associated with it.

When calculating policy prices, we take many attributes into consideration, such as the user's age, gender, height, weight and status. You’ll need to include this information when creating a quote through the API.

> To get a quote, use this code:

```shell

curl -X POST \
  https://api.hellobestow.com/v2/quote \
  -H 'Authorization: 82zZIHeBqUlBtICMX5li' \
  -H 'Content-Type: application/json' \
  -H 'cache-control: no-cache' \
  -d '{
	"birth_date": "1980-01-01",
	"gender": "male",
	"height_feet": 6,
	"height_inches": 0,
	"state": "TX",
	"weight": 180,
  "tobacco": "no"
}
'
```

> The above command returns JSON structured like this:

```json
{
  "product_rates": {
    "BT1003": {
      "100000": "11.25",
      "1000000": "55.83",
      "150000": "14.38",
      "200000": "17.50",
      "250000": "16.66",
      "300000": "19.33",
      "350000": "22.00",
      "400000": "24.66",
      "450000": "27.33",
      "50000": "11.38",
      "500000": "29.58",
      "550000": "32.21",
      "600000": "34.83",
      "650000": "37.46",
      "700000": "40.08",
      "750000": "42.71",
      "800000": "45.33",
      "850000": "47.96",
      "900000": "50.58",
      "950000": "53.21"
    },
    "BT2003": {
      "100000": "12.75",
      "1000000": "69.16",
      "150000": "16.63",
      "200000": "20.50",
      "250000": "21.04",
      "300000": "24.58",
      "350000": "28.12",
      "400000": "31.66",
      "450000": "35.21",
      "50000": "10.58",
      "500000": "36.25",
      "550000": "39.54",
      "600000": "42.83",
      "650000": "46.12",
      "700000": "49.41",
      "750000": "52.71",
      "800000": "56.00",
      "850000": "59.29",
      "900000": "62.58",
      "950000": "65.87"
    }
  },
  "quote_id": "a3b4d9e4-567d-4d77-95da-4761b7985320"
}
```

### HTTP Request

`POST https://api.hellobestow.com/v2/quote`

### Query Parameters

| Parameter       | Required | Description                                  |
| --------------- | -------- | -------------------------------------------- |
| birth_date      | true     | Birth date in format YYYY-MM-DD              |
| gender          | true     | "male" or "female"                           |
| height_feet     | true     | Feet part of height                          |
| height_inches   | true     | Inches part of height                        |
| state           | true     | The 2-character abbreviation of the US state |
| weight          | true     | Weight in lbs                                |
| tobacco         | false    | "yes" or "no" string                         |

### Return values

The call will receive a JSON object with a prices for a 10-year product (BT1003) and a 20-year product (BT2003)

| Product | Length of Policy |
| ------- | ---------------- |
| BT1003  | 10 year          |
| BT2003  | 20 year          |

Inside the Product offering, you will receive different prices for different amounts of insurance the customer can buy. In the example to the right, the values are

| Product | Length of Policy | Face Value of Insurance | Price per Month |
| ------- | ---------------- | ----------------------- | --------------- |
| BT2003  | 20 year          | \$100,000               | \$8.33          |
| BT2003  | 20 year          | \$150,000               | \$12.50         |
| BT2003  | 20 year          | \$200,000               | \$16.67         |

## Using Quote to Enroll

> To enroll from a quote, redirect to:

> `https://enrollment.bestow.com/get-started`


When you send customers to enroll in life insurance with Bestow, redirect them to `enrollment.bestow.com/get-started`, and pass in the various query params that will link your quote to the enrollment application.
You can additionally pass in optional query params so that the enrollment application is pre-populated with that data.

### Query Params

> Example Redirect Using Query Parameters:

> `https://enrollment.bestow.com/get-started?widget=api&quoteid=4ea338e6-56c5-4fd2-b07b-c0f90764afa1&date_of_birth=01/01/1980&gender=male&height=72&weight=180&state=TN&product=BT2003&coverage=700000&utm_source=Source&utm_name=Name&utm_medium=Medium&utm_content=Content&utm_term=Term`

| Parameter       | Required | Description                                                                                                                  |
| --------------- | -------- | ---------------------------------------------------------------------------------------------------------------------------- |
| widget          | true     | Use the value `api`. This tells Bestow's enrollment flow what method the user came from                                      |
| product         | true     | The product type ([either "BT1003" or "BT2003"]). See full description above                                                 |
| coverage        | true     | The integer face value of the life insurance policy in USD                                                                   |
| quoteid         | true     | The quoteid returned from the Quote API call. If this is not provided the user will have to go select a quote in enrollment  |
| date_of_birth   | false    | Birth date in format YYYY-MM-DD                                                                                              |
| gender          | false    | "male" or "female"                                                                                                           |
| height          | false    | Total height in inches                                                                                                       |
| weight          | false    | Total weight in lbs                                                                                                          |
| state           | false    | The 2-character abbreviation of the US state                                                                                 |
| zip             | false    | 5 digit zipcode value                                                                                                        |
| partner_referral| false    | String value of referring partner name. Used to determine knockout page partner rules                                        |
| email           | false    | Email in a valid string format                                                                                               |
| premium         | false    | Which monthly premium was selected by the user at quote                                                                      |

### Campaign Params

You can provide `utm_` parameters for proper affiliate tracking. All parameters below are supported.

| Parameter   |
| ----------- |
| utm_source  |
| utm_name    |
| utm_medium  |
| utm_content |
| utm_term    |

### Other Params

Please note that passing any URL parameters other than those explicitly listed above is not supported!

# Errors

The Bestow API uses the following error codes:

| Error Code | Meaning                                                                                   |
| ---------- | ----------------------------------------------------------------------------------------- |
| 400        | Bad Request -- Your request is invalid.                                                   |
| 401        | Unauthorized -- Your API key is wrong.                                                    |
| 404        | Not Found -- The specified url could not be found.                                        |
| 405        | Method Not Allowed -- You tried to access a quote with an invalid method.                 |
| 429        | Too Many Requests -- You're requesting too many quotes! Slow down!                        |
| 500        | Internal Server Error -- We had a problem with our server. Try again later.               |
| 503        | Service Unavailable -- We're temporarily offline for maintenance. Please try again later. |
