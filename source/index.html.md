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

There are two methods for authentication. Both methods require working with your Business Development point of contact to receive your authentication key/secret. 

## API Keys

> To authorize, use this code:

```shell

curl -X POST \
  https://api.hellobestow.com/v2/quote \
  -H 'Authorization: 82zZIHeBqUlBtICMX5li' \
  -H 'Content-Type: application/json' \
```

> Make sure to replace `82zZIHeBqUlBtICMX5li` with your API key.

The first option is to use an API key. Bestow expects for the API key to be included in all API requests to the server in a header that looks like the following:

`Authorization: 82zZIHeBqUlBtICMX5li`

<aside class="notice">
You must replace <code>82zZIHeBqUlBtICMX5li</code> with your personal API key.
</aside>

## Using Partner_Referral in POST Body 
Alternatively, you can skip the Authentication Header by instead adding your `partner_referral` parameter in the JSON within the POST body. Your unique `partner_referral` name must similarly be provided to you by Bestow.

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
	"partner_referral": "partnername",
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
    "BT1004":{
         "100000":"14.25",
         "1000000":"62.67",
         "150000":"18.38",
         "200000":"22.50",
         "250000":"24.33",
         "300000":"28.00",
         "350000":"31.67",
         "400000":"35.33",
         "450000":"39.00",
         "50000":"10.46",
         "500000":"34.33",
         "550000":"37.17",
         "600000":"40.00",
         "650000":"42.83",
         "700000":"45.67",
         "750000":"48.50",
         "800000":"51.33",
         "850000":"54.17",
         "900000":"57.00",
         "950000":"59.83"
      },
      "BT2004":{
         "100000":"16.50",
         "1000000":"76.00",
         "150000":"21.75",
         "200000":"27.00",
         "250000":"32.25",
         "300000":"37.50",
         "350000":"42.75",
         "400000":"48.00",
         "450000":"53.25",
         "50000":"11.33",
         "500000":"41.00",
         "550000":"44.50",
         "600000":"48.00",
         "650000":"51.50",
         "700000":"55.00",
         "750000":"58.50",
         "800000":"62.00",
         "850000":"65.50",
         "900000":"69.00",
         "950000":"72.50"
      }
  },
  "quote_id": "a3b4d9e4-567d-4d77-95da-4761b7985320"
}
```

### HTTP Request

`POST https://api.hellobestow.com/v2/quote`


### Query Parameters

| Parameter       | Required | Description                                                             |
| --------------- | -------- | ----------------------------------------------------------------------- |
| partner_referral| false    | “PartnerName” provided by Bestow if not authenticating with an API key  |
| birth_date      | true     | Birth date in format YYYY-MM-DD                                         |
| gender          | true     | "male" or "female"                                                      |
| height_feet     | true     | Feet part of height                                                     |
| height_inches   | true     | Inches part of height                                                   |
| state           | true     | The 2-character abbreviation of the US state                            |
| weight          | true     | Weight in lbs                                                           |
| tobacco         | false    | "yes" or "no" string, depending on whether the applicant has used tobacco in the past 12 months. Note, it is not “technically” required, but the response has a significant impact on the accuracy of the quoted price|

### Return values

The call will receive a JSON object with the prices for  for each term length for which the customer could potentially be eligible.

Product codes (e.g. `BT1004`) will always conform to the following structure:

- a two letter prefix ("BT"), followed by:
- a two digit term length in years ("10"), followed by:
- a two digit revision number ("04") that can update regularly

Accordingly, term lengths can be dynamically parsed from any arbitrary product code. For example, given the product code `BT1504`, its term length can be derived to be "15 Years". 

Note that it is possible for a user to only receive quotes for a subset of the available products. For instance, an older applicant might only get a quote for a 10 & 15 year product, but not the 20, 25, and 30 year product. 

| Length of Policy | Product                  | In Production      |
|------------------|--------------------------|--------------------|
| 10 year          | BT1003, BT1004, BT1005.. | Yes                |
| 15 year          | BT1503, BT1504, BT1505.. | Yes                |
| 20 year          | BT2003, BT2004, BT2005.. | Yes                |
| 25 year          | BT2503, BT2504, BT2505.. | Yes                |
| 30 year          | BT3003, BT3004, BT3005.. | Yes                |

Inside the Product offering, you will receive different prices for different amounts of insurance coverage the customer can buy. Maximum coverage amounts offered by Bestow are currently $1,500,000. In the example to the right, the values are

| Product | Length of Policy | Face Value of Insurance | Price per Month |
| ------- | ---------------- | ----------------------- | --------------- |
| BT1004  | 10 year          | \$100,000               | \$14.25         |
| BT2004  | 20 year          | \$150,000               | \$21.75         |
| BT2004  | 20 year          | \$200,000               | \$27.00         |

In some cases, a customer might not be eligible based on their initial inputs to the quote, and as such these customers should not be instructed to apply for Bestow life insurance. The following responses cover these situations: 

| Response               | Description                                                                                    |
| ---------------------- | ---------------------------------------------------------------------------------------------- |
| "ineligible": "bmi"    | Implies the applicant’s height/weight combination exceeds Bestow’s BMI underwriting threshold  |
| "ineligible": "age"    | Implies the applicant’s age is outside of Bestow’s eligibility range                           |
| {blank}                | Implies the applicant is from a state (e.g. NY) where Bestow currently does not offer policies |

If a user is ineligible for multiple reasons, the API will only return a single ineligible reason.

## Using Quote to Enroll

Note the below instructions for how to link your customer to then complete the life insurance application at bestow.com will vary depending on whether your partnership is structured as an “affiliate” or “licensed agent” relationship. If you are unclear on your partnership structure, please reach out to your Business Development point of contact. 

### Instructions for partnerships of Bestow structured as an “agent”

> As an Agent partner, to enroll from a quote, redirect to your unique URL typically in the format (the [agentkey] after the forward slash is a unique key given to you by the Bestow team):

> `https://agent-quote.bestow.com/[agentkey]`

When you send customers to enroll in life insurance with Bestow, redirect them to your unique Bestow-provided agent co-branded landing page. This landing page URL is in the format `agent-quote.bestow.com/[agentkey]` and passes in the various query parameters listed below. If you have not yet been provided your [agentkey], then contact your Bestow Business Development manager directly or the Agent Operations team at `agents@bestow.com`.

> Example Redirect Including Query Parameters:

> `https://agent-quote.bestow.com/[agentkey]?date_of_birth=1980-01-01&gender=male&height=72&weight=180&state=TN&tobacco=no&product=BT2004&coverage=700000&skipform=true`

| Parameter            | Required | Description                                               |
| -------------------- | -------- | ----------------------------------------------------------|
| product              | true     | The product type (e.g. "BT1004"). See full description above.|
| coverage             | true     | The integer face value of the life insurance policy in USD|
| date_of_birth        | true     | Birth date in format YYYY-MM-DD                           |
| gender               | true     | "male" or "female"                                        | 
| height               | true     | Total height in inches                                    | 
| weight               | true     | Total weight in lbs                                       | 
| state                | true     | The 2-character abbreviation of the US state              | 
| tobacco              | true     | "yes" or "no"                                             | 
| skipform             | true     | Use the value “true”                                      |
| partner_user_id      | false    | Optionally provided by you to Bestow                      |

### Instructions for partnerships of Bestow structured as an “affiliate” (non-licensed agent) established through Impact Radius

> As an affiliate partner (utilizing Impact Radius), to enroll from a quote, redirect to your unique URL found in Impact Radius that is typically in the format:

> `https://bestow.sjv.io/c/[12345678]/[123456]/[1234]`

When you send customers to enroll in life insurance with Bestow, redirect them to your unique landing page provided by Impact Radius. This landing page URL is typically in the format `https://bestow.sjv.io/c/[12345678]/[123456]/[1234]` and pass in the various query parameters listed below. If you have not yet been provided these query parameters, then contact your Bestow Business Development manager directly.

> Example Redirect Including Query Parameters:

> `https://bestow.sjv.io/c/1395229/513723/8784?p.product=BT2003&p.coverage=700000&p.date_of_birth=1980-01-01&p.gender=male&p.height=72&p.weight=180&p.state=TN&p.tobacco=no&p.skipform=true`

| Parameter            | Required | Description                                               |
| -------------------- | -------- | ----------------------------------------------------------|
| p.product            | true     | The product type (either "BT1003" or "BT2003"). See full description above.|
| p.coverage           | true     | The integer face value of the life insurance policy in USD|
| p.date_of_birth      | true     | Birth date in format YYYY-MM-DD                           |
| p.gender             | true     | "male" or "female"                                        | 
| p.height             | true     | Total height in inches                                    | 
| p.weight             | true     | Total weight in lbs                                       | 
| p.state              | true     | The 2-character abbreviation of the US state              | 
| p.tobacco            | true     | "yes" or "no"                                             | 
| p.skipform           | true     | Use the value “true”                                      | 

Note, do not include any additional UTM parameters as these are already automatically included through your Impact Radius unique URL.

### Instructions for partnerships of Bestow structured as an “affiliate” (non-licensed agent) and contracted directly

> As an affiliate partner (directly contracted), to enroll from a quote, redirect to:

> `https://enrollment.bestow.com/get-started`

When you send customers to enroll in life insurance with Bestow, redirect them to `enrollment.bestow.com/get-started`, and pass in the various query params that will link your quote to the enrollment application.
You can additionally pass in optional query params so that the enrollment application is pre-populated with that data.

> Example Redirect Including Query Parameters:

> `https://enrollment.bestow.com/get-started?widget=api&quoteid=4ea338e6-56c5-4fd2-b07b-c0f90764afa1&date_of_birth=1980-01-01&gender=male&height=72&weight=180&state=TN&product=BT2003&coverage=700000&utm_source=Source&utm_medium=Medium&utm_content=Content&utm_term=Term`

| Parameter       | Required | Description                                                                                                                  |
| --------------- | -------- | ---------------------------------------------------------------------------------------------------------------------------- |
| widget          | true     | Use the value "api". This tells Bestow's enrollment flow what method the user came from                                      |
| product         | true     | The product type (either "BT1003" or "BT2003"). See full description above                                                   |
| coverage        | true     | The integer face value of the life insurance policy in USD                                                                   |
| quoteid         | true     | The quoteid returned from the Quote API call. If this is not provided the user will have to go select a quote in enrollment  |
| date_of_birth   | false    | Birth date in format YYYY-MM-DD                                                                                              |
| gender          | false    | "male" or "female"                                                                                                           |
| height          | false    | Total height in inches                                                                                                       |
| weight          | false    | Total weight in lbs                                                                                                          |
| state           | false    | The 2-character abbreviation of the US state                                                                                 |
| zip             | false    | 5 digit zipcode value                                                                                                        |
| partner_referral| false    | String value of referring partner name. Used to determine knockout page partner rules. The name must be provided to you by Bestow.|
| email           | false    | Email in a valid string format                                                                                               |
| premium         | false    | Which monthly premium was selected by the user at quote                                                                      |
| utm_source      | true     | Provided to you by Bestow for proper tracking, including revenue share payouts                                               |
| utm_medium      | true     | Provided to you by Bestow for proper tracking, including revenue share payouts                                               |
| utm_content     | false    | Potentially provided to you by Bestow for proper affiliate tracking.                                                         |
| utm_term        | false    | Potentially provided to you by Bestow for proper affiliate tracking.                                                         |
| partner_user_id | false    | Potentially provided by you to Bestow for proper customer tracking.                                                         |

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
