# Grips Intelligence API Documentation

## Overview

This document provides an overview of the API endpoints for the Grips Intelligence data service. The API resembles GraphQL but does not strictly adhere to its specifications.

## Base URL

All URLs referenced in this documentation share the following base:

```
https://app.gripsintelligence.com/api/data/v1?provider=pg
```

The base URL is followed by query parameters to define the source and provider.

## Authentication

When making requests to the Grips API, include the API key in the request header. Add a header called `grips-api-key` with the value set to your API key.

## Error Messages

Currently, no standard structure is defined for error messages. Please contact eldar.djafarov@gripsintelligence.com for assistance with error-related issues.

## Endpoints

### 1. Domain Timeseries Performance

#### Endpoint

```
https://app.gripsintelligence.com/api/data/v1?provider=pg
```

#### Method

`POST`

#### Payload

When making a POST request, the payload should be in JSON format, containing two parameters: `query` and `variables`. The `query` parameter is a string representing the query, while the `variables` parameter is a JSON object containing any necessary data for use within the query. Ensure both parameters are correctly formatted in the JSON payload when constructing your API request.

**Query**

```graphql
    input Date {
      gte: String!
      lte: String!
    }
    input OrArray {
      in: [String!]
    }
    input Filters {
      country: String
      category: OrArray
      domain: OrArray!
      date: Date!
    }
    query ii_transactional($filters: Filters) {
      timeseries: fetch(filters: $filters) {
        date (type: Array, sort_asc: date) {
            transactionrevenue: sum(a:transactionrevenue)
            transactions: sum(a:transactions)
            sessions: sum(a:sessions)
            adcost: sum(a: adcost)
            aov: divide(a:transactionrevenue, by: transactions)
            cr: divide(a:transactions, by: sessions)
            cpc: divide(a:adcost, by: adclicks)     
        }
      }
    }
```

**Variables**

- `domain`: The domain property is an object with an `in` key that holds an array of domain names.
- `date`: The date property is an object containing two keys: `gte` (greater than or equal to) and `lte` (less than or equal to). This property is used to filter data based on a date range.
- `country` (US, GB, DE): The country property is a string representing the two-letter ISO code of a country. This property is used to filter data based on a specific country.

```json
{
  "filters": {
    "domain": {
        "in": [
        "adidas.com"
        ]
    },
    "date": {
        "gte": "2023-01-01",
        "lte": "2023-03-31"
    },
    "country": "US"
  }
}
```

#### Response


```json
{
    "timeseries": [
        {
            "date": "Sun Jan 01 2023 01:00:00 GMT+0100 (Central European Standard Time)",
            "transactionrevenue": 53145635.3783756,
            "transactions": 483886.617154171,
            "sessions": 25207627.0537442,
            "adcost": 133766.421380208,
            "aov": 109.83075917499393,
            "cr": 0.0191960392764512,
            "cpc": 0.24140119371582694
        },
        {
            "date": "Wed Feb 01 2023 01:00:00 GMT+0100 (Central European Standard Time)",
            "transactionrevenue": 52934002.3823258,
            "transactions": 474325.460941581,
            "sessions": 24811004.6978913,
            "adcost": 116699.533875954,
            "aov": 111.5984822021552,
            "cr": 0.019117544011583772,
            "cpc": 0.22570859745822527
        },
        {
            "date": "Wed Mar 01 2023 01:00:00 GMT+0100 (Central European Standard Time)",
            "transactionrevenue": 58211952.9619916,
            "transactions": 510529.997930045,
            "sessions": 28537758.6547429,
            "adcost": 110341.135904103,
            "aov": 114.02259017489982,
            "cr": 0.017889632322554735,
            "cpc": 0.2336604446794829
        }
    ]
}
```

### 2. CI Channels

#### Endpoint

```
https://app.gripsintelligence.com/api/data/v1?provider=pg
```

#### Method

`POST`

#### Payload

When making a POST request, the payload should be in JSON format, containing two parameters: `query` and `variables`. The `query` parameter is a string representing the query, while the `variables` parameter is a JSON object containing any necessary data for use within the query. Ensure both parameters are correctly formatted in the JSON

**Query**

```graphql
input Date {
   gte: String!
   lte: String!
 }


 input OrArray {
   in: [String!]
 }


 input Filters {
   country: String
   domain: OrArray!
   date: Date!
   channel: OrArray!
 }


 query domain_channels($filters: Filters) {
   pre: with(filters: $filters) {
     date {
       channel {
         sessions: sum(a: sessions)
         transactions: sum(a: transactions)
         cr: sum(a: cr)
         aov: sum(a: aov)
         transactionrevenue: sum(a: transactionrevenue)
       }
     }
   }
   timeseries: fetch(table: pre) {
     date(type: Array, sort_asc: date) {
       channel {
         sessions: sum(a: sessions)
         transactions: sum(a: transactions)
         cr: divide(a: transactions, by: sessions)
         aov: divide(a: transactionrevenue, by: transactions)
         transactionrevenue: sum(a: transactionrevenue)
       }
     }
   }
   aggregated: fetch(table: pre) {
     channel(type: Array) {
       sessions: sum(a: sessions)
       transactions: sum(a: transactions)
       cr: divide(a: transactions, by: sessions)
       aov: divide(a: transactionrevenue, by: transactions)
       transactionrevenue: sum(a: transactionrevenue)
     }
   }


 }
```

**Variables**

- `domain`: The domain property is an object with an `in` key that holds an array of domain names.
- `date`: The date property is an object containing two keys: `gte` (greater than or equal to) and `lte` (less than or equal to). This property is used to filter data based on a date range.
- `country` (US, GB, DE): The country property is a string representing the two-letter ISO code of a country. This property is used to filter data based on a specific country.
- `channel`: An object with an `in` key that holds an array of marketing channel names. The purpose of this property is to filter data based on the specified marketing channel(s).

```json
{
 "filters": {
   "date": {
     "gte": "2023-01-01",
     "lte": "2023-03-31"
   },
   "country": "US",
   "domain": {
     "in": [
       "adidas.com"
     ]
   },
   "channel": {
     "in": [
       "Direct",
       "Organic Search",
       "Paid Search",
       "Referral",
       "Social"
     ]
   }
 }
}

```

#### Response


```json
{
   "timeseries": [
       {
           "date": "Sun Jan 01 2023 01:00:00 GMT+0100 (Central European Standard Time)",
           "Organic Search": {
               "sessions": 5753185.65516056,
               "transactions": 102385.84790831,
               "cr": 0.017796375226960783,
               "aov": 111.40138393346774,
               "transactionrevenue": 11405925.5592691
           },
           "Referral": {
               "sessions": 3717082.04497535,
               "transactions": 97570.1287577284,
               "cr": 0.026249119270903464,
               "aov": 103.65343079424363,
               "transactionrevenue": 10113478.1992732
           },
           "Paid Search": {
               "sessions": 554074.815379353,
               "transactions": 8954.62988098667,
               "cr": 0.016161409396292795,
               "aov": 97.50718119046446,
               "transactionrevenue": 873140.718476946
           },
           "Direct": {
               "sessions": 14479595.0224242,
               "transactions": 265569.326613904,
               "cr": 0.018340936097584495,
               "aov": 112.63960312520935,
               "transactionrevenue": 29913621.9622347
           },
           "Social": {
               "sessions": 703689.515804712,
               "transactions": 9406.68399324287,
               "cr": 0.013367662858750727,
               "aov": 89.24175356333416,
               "transactionrevenue": 839468.939121696
           }
       },
       {
           "date": "Wed Feb 01 2023 01:00:00 GMT+0100 (Central European Standard Time)",
           "Organic Search": {
               "sessions": 6445103.04620692,
               "transactions": 112350.475805799,
               "cr": 0.017431913160394642,
               "aov": 112.65946808944939,
               "transactionrevenue": 12657344.9291274
           },
           "Referral": {
               "sessions": 3646130.49868126,
               "transactions": 94125.2555146571,
               "cr": 0.025815108788524464,
               "aov": 106.66419283373476,
               "transactionrevenue": 10039794.6498375
           },
           "Paid Search": {
               "sessions": 517003.733566648,
               "transactions": 8375.06984890917,
               "cr": 0.016199244889685178,
               "aov": 98.00105143676248,
               "transactionrevenue": 820765.696481815
           },
           "Direct": {
               "sessions": 13647848.220791,
               "transactions": 251988.633599883,
               "cr": 0.018463616652228468,
               "aov": 113.93677394447431,
               "transactionrevenue": 28710772.783466
           },
           "Social": {
               "sessions": 554919.198645556,
               "transactions": 7486.02617233323,
               "cr": 0.0134902997426688,
               "aov": 94.21878695279713,
               "transactionrevenue": 705324.323413061
           }
       }
   ],
   "aggregated": [
       {
           "channel": "Organic Search",
           "sessions": 12198288.70136748,
           "transactions": 214736.323714109,
           "cr": 0.01760380687112012,
           "aov": 112.05961608130437,
           "transactionrevenue": 24063270.488396503
       },
       {
           "channel": "Referral",
           "sessions": 7363212.54365661,
           "transactions": 191695.3842723855,
           "cr": 0.02603420507996822,
           "aov": 105.13175503804945,
           "transactionrevenue": 20153272.8491107
       },
       {
           "channel": "Paid Search",
           "sessions": 1071078.548946001,
           "transactions": 17329.699729895838,
           "cr": 0.016179672853012955,
           "aov": 97.7458635361672,
           "transactionrevenue": 1693906.414958761
       },
       {
           "channel": "Direct",
           "sessions": 28127443.243215203,
           "transactions": 517557.960213787,
           "cr": 0.018400461848356607,
           "aov": 113.27116629522614,
           "transactionrevenue": 58624394.7457007
       },
       {
           "channel": "Social",
           "sessions": 1258608.714450268,
           "transactions": 16892.7101655761,
           "cr": 0.01342173265963398,
           "aov": 91.44732708978536,
           "transactionrevenue": 1544793.2625347571
       }
   ]
}

```

### 3. RI Adwords Over Time

#### Endpoint

```
https://app.gripsintelligence.com/api/data/v1?provider=pg
```

#### Method

`POST`

#### Payload

When making a POST request, the payload should be in JSON format, containing two parameters: `query` and `variables`. The `query` parameter is a string representing the query, while the `variables` parameter is a JSON object containing any necessary data for use within the query. Ensure both parameters are correctly formatted in the JSON

**Query**

```graphql
       input Date {
       gte: String!
       lte: String!
     }


     input OrArray {
       in: [String!]
     }


     input Filters {
       country: String
       domain: OrArray!
       date: Date!
     }


     query ii_transactional($filters: Filters) {
       timeseries: fetch(filters: $filters) {
         date(type: Array, sort_asc: date) {
           adcost: sum(a: adcost)
           adclicks: sum(a: adclicks)
           cpc: divide(a: adcost, by: adclicks)
         }
       }
       aggregated: fetch(filters: $filters) {
         adcost: sum(a: adcost)
         adclicks: sum(a: adclicks)
         cpc: divide(a: adcost, by: adclicks)
       }
     }
```

**Variables**

- `domain`: The domain property is an object with an `in` key that holds an array of domain names.
- `date`: The date property is an object containing two keys: `gte` (greater than or equal to) and `lte` (less than or equal to). This property is used to filter data based on a date range.
- `country` (US, GB, DE): The country property is a string representing the two-letter ISO code of a country. This property is used to filter data based on a specific country.

```json
{
 "filters": {
   "date": {
     "gte": "2023-01-01",
     "lte": "2023-03-31"
   },
   "country": "US",
   "domain": {
     "in": [
       "adidas.com"
     ]
   }
 }
}

```

#### Response


```json
{
   "timeseries": [
       {
           "date": "Sun Jan 01 2023 01:00:00 GMT+0100 (Central European Standard Time)",
           "adcost": 133766.421380208,
           "adclicks": 554124.9333445517,
           "cpc": 0.24140119371582694
       },
       {
           "date": "Wed Feb 01 2023 01:00:00 GMT+0100 (Central European Standard Time)",
           "adcost": 116699.533875954,
           "adclicks": 517036.26658770617,
           "cpc": 0.22570859745822527
       },
       {
           "date": "Wed Mar 01 2023 01:00:00 GMT+0100 (Central European Standard Time)",
           "adcost": 110341.135904103,
           "adclicks": 472228.56262573047,
           "cpc": 0.2336604446794829
       }
   ],
   "aggregated": {
       "adcost": 360807.091160265,
       "adclicks": 1543389.7625579883,
       "cpc": 0.23377574663837503
   }
}


```

### 4. CI Devices

#### Endpoint

```
https://app.gripsintelligence.com/api/data/v1?provider=pg
```

#### Method

`POST`

#### Payload

When making a POST request, the payload should be in JSON format, containing two parameters: `query` and `variables`. The `query` parameter is a string representing the query, while the `variables` parameter is a JSON object containing any necessary data for use within the query. Ensure both parameters are correctly formatted in the JSON

**Query**

```graphql
       input Date {
       gte: String!
       lte: String!
     }


     input OrArray {
       in: [String!]
     }


     input Filters {
       country: String
       domain: OrArray!
       date: Date!
     }


     query domain_devices($filters: Filters) {
       aggregated: fetch(filters: $filters) {
         device {
           sessions: sum(a: sessions)
           transactions: sum(a: transactions)
           cr: divide(a: transactions, by: sessions)
           aov: divide(a: transactionrevenue, by: transactions)
           transactionrevenue: sum(a: transactionrevenue)
         }
       }
     }
  

```

**Variables**

- `domain`: The domain property is an object with an `in` key that holds an array of domain names.
- `date`: The date property is an object containing two keys: `gte` (greater than or equal to) and `lte` (less than or equal to). This property is used to filter data based on a date range.
- `country` (US, GB, DE): The country property is a string representing the two-letter ISO code of a country. This property is used to filter data based on a specific country.

```json
{
 "filters": {
   "date": {
     "gte": "2023-01-01",
     "lte": "2023-03-31"
   },
   "country": "US",
   "domain": {
     "in": [
       "adidas.com"
     ]
   }
 }
}


```

#### Response


```json
{
   "data": {
       "mobile": {
           "sessions": 50787066.2417455,
           "transactions": 633266.786593497,
           "cr": 0.012469055835109382,
           "aov": 99.20227082763365,
           "transactionrevenue": 62821505.8397773
       },
       "desktop": {
           "sessions": 27769322.958095577,
           "transactions": 835475.289432299,
           "cr": 0.03008626892051232,
           "aov": 121.45192486811607,
           "transactionrevenue": 101470084.8829158
       }
   }
}



```

## Rate Limiting

We have not imposed a rate limit on our API. We kindly ask that you use the API responsibly and carefully, taking into consideration the potential impact on our services and other users. Thank you for your understanding and cooperation.
