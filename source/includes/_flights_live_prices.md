# Flights Live Prices

The Live Pricing Service Session must be created before any pricing data can be obtained. The request contains details of the locations, dates, passengers, cabin class and user details. These parameters define the session, and cannot be changed within the session (with the exception of passenger numbers).

![diagram](/images/Skyscanner_UMLDiagram_v3_EJ-01-01.png)

<aside class="warning">
We do not support client-side implementation of our Live Prices API using CORS requests due to the potential security
risk of exposing account API keys.
</aside>

## Creating the session

### Request

```shell
curl "http://partners.api.skyscanner.net/apiservices/pricing/v1.0"
    -X POST
    -H "Content-Type: application/x-www-form-urlencoded"
    -d 'cabinclass=Economy
    &country=UK
    &currency=GBP
    &locale=en-GB
    &locationSchema=iata
    &originplace=EDI
    &destinationplace=LHR
    &outbounddate=2017-05-30
    &inbounddate=2017-06-02
    &adults=1
    &children=0
    &infants=0
    &apikey=prtl6749387986743898559646983194'

```

*API endpoint*

`POST /pricing/v1.0`


*TRY IT OUT*

[![Run in Postman](https://run.pstmn.io/button.svg)](https://app.getpostman.com/run-collection/31ff523d2ff9186107e1)

or go to our [test harness](http://business.skyscanner.net/portal/en-GB/Documentation/FlightsLivePricingQuickStart)


*HEADER VALUES*

| Header | Value |
| --- | --- |
| `Content-Type` <br><span class="required">REQUIRED</span> | `application/x-www-form-urlencoded` |
| `X-Forwarded-For` <br><span class="required">REQUIRED</span> | user's IP address |
| `Accept` <br><span class="optional">OPTIONAL</span> | `application/json` or `application/xml` <br>The default response format is XML |

*REQUEST PARAMETERS (FORM)*

| Parameter | Description |
| --------- | ------- |
| ```country``` <br><span class="required">REQUIRED</span> | The [market/country](#markets) your user is in |
| ```currency``` <br><span class="required">REQUIRED</span> | The [currency](#currencies) you want the prices in |
| ```locale``` <br><span class="required">REQUIRED</span> | The [locale](#locales) you want the results in (ISO locale) |
| ```originPlace``` <br><span class="required">REQUIRED</span> | The origin place (see [places](#places)) |
| ```destinationPlace``` <br><span class="required">REQUIRED</span> | The destination place (see [places](#places)) |
| ```outboundDate``` <br><span class="required">REQUIRED</span> | The outbound date. Format "yyyy-mm-dd". |
| ```inboundDate``` <br><span class="optional">OPTIONAL</span> | The return date. Format "yyyy-mm-dd". Use empty string for oneway trip. |
| ```cabinClass``` <br><span class="optional">OPTIONAL</span> | The cabin class. Can be "economy", "premiumeconomy", "business", "first"  |
| ```adults``` <br><span class="required">REQUIRED</span> | Number of adults (16+ years). Must be between 1 and 8.  |
| ```children``` <br><span class="optional">OPTIONAL</span> | Number of children (1-16 years). Can be between 0 and 8.  |
| ```infants``` <br><span class="optional">OPTIONAL</span> | Number of infants (under 12 months). Can be between 0 and 8.  |
| ```apiKey``` <br><span class="required">REQUIRED</span> | Your API Key. |


### Response

> Example response with polling url:

```shell
Location "http://partners.api.skyscanner.net/apiservices/pricing/uk1/v1.0/
    {SessionKey}"
```

A successful response contains no content. The URL to poll the results is provided in the Location header of the response.

<aside class="warning">
Please refer to our <a href="#response-codes">response codes</a> in case of unsuccessful response.
</aside>

*RESPONSE PARAMETERS*

| Element | Detail |
| ------- | ------ |
| `Location Header` | Contains the URL for polling the results in the newly created session |







## Polling the results

### Request

> Example request with polling url:

```shell
Location "http://partners.api.skyscanner.net/apiservices/pricing/uk1/v1.0/
    {SessionKey}?apiKey={apiKey}
    &stops=0
    &duration=360
    &includeCarriers=ba;u2;af"
```

*API endpoint*

`GET /pricing/v1.0/{SessionKey}`


*TRY IT OUT*

[![Run in Postman](https://run.pstmn.io/button.svg)](https://app.getpostman.com/run-collection/31ff523d2ff9186107e1)

or go to our [test harness](http://business.skyscanner.net/portal/en-GB/Documentation/FlightsLivePricingQuickStart)


*HEADER VALUES*

| Header | Value |
| --- | --- |
| `Accept` <br><span class="optional">OPTIONAL</span> | `application/json` or `application/xml` <br>The default response format is XML |

*REQUEST PARAMETERS (FORM)*

| Parameter | Description |
| --------- | ------- |
| ```sortType``` <br><span class="optional">OPTIONAL</span> | The parameter to sort results on. Can be carrier, duration, outboundarrivetime, outbounddeparttime, inboundarrivetime, inbounddeparttime, price* |
| ```sortOrder``` <br><span class="optional">OPTIONAL</span> | The sort order. 'asc' or 'desc' |
| ```duration``` <br><span class="optional">OPTIONAL</span> | Filter for maximum duration in minutes. Integer between 0 and 1800  |
| ```includeCarriers``` <br><span class="optional">OPTIONAL</span> | Filter flights by the specified carriers. Must be semicolon-separated [IATA codes](http://www.iata.org/publications/Pages/code-search.aspx). |
| ```excludeCarriers``` <br><span class="optional">OPTIONAL</span> | Filter flights by any but the specified carriers. Must be semicolon-separated [IATA codes](http://www.iata.org/publications/Pages/code-search.aspx). |
| ```originAirports``` <br><span class="optional">OPTIONAL</span> | Origin airports to filter on. List of airport codes delimited by ‘;’  |
| ```destinationAirports``` <br><span class="optional">OPTIONAL</span> | Destination airports to filter on. List of airport codes delimited by ‘;’  |
| ```stops``` <br><span class="optional">OPTIONAL</span> | Filter for maximum number of stops.<br>0 for direct flights only<br>1 for flights with maximum one stop<br>omit for all flights (direct and indirect)  |
| ```outboundDepartTime``` <br><span class="optional">OPTIONAL</span> | Filter for outbound departure time by time period of the day (i.e. morning, afternoon, evening). List of day time period delimited by ‘;’ (acceptable values are M, A, E) |
| ```outboundDepartStartTime``` <br><span class="optional">OPTIONAL</span> | Filter for start of range for outbound departure time. Format ‘hh:mm’. |
| ```outboundDepartEndTime``` <br><span class="optional">OPTIONAL</span> | Filter for end of range for outbound departure time. Format ‘hh:mm’. |
| ```outboundArriveStartTime``` <br><span class="optional">OPTIONAL</span> | Filter for start of range for outbound arrival time. Format ‘hh:mm’. |
| ```outboundArriveEndTime``` <br><span class="optional">OPTIONAL</span> | Filter for end of range for outbound arrival time. Format ‘hh:mm’. |
| ```inboundDepartTime``` <br><span class="optional">OPTIONAL</span> | Filter for inbound departure time by time period of the day (i.e. morning, afternoon, evening). List of day time period delimited by ‘;’ (acceptable values are M, A, E)  |
| ```inboundDepartStartTime``` <br><span class="optional">OPTIONAL</span> | Filter for start of range for inbound departure time. Format ‘hh:mm’. |
| ```inboundDepartEndTime``` <br><span class="optional">OPTIONAL</span> | Filter for start of range for inbound departure time. Format ‘hh:mm’. |
| ```inboundArriveStartTime``` <br><span class="optional">OPTIONAL</span> | Filter for start of range for inbound departure time. Format ‘hh:mm’. |
| ```inboundArriveEndTime``` <br><span class="optional">OPTIONAL</span> | Filter for end of range for inbound arrival time. Format ‘hh:mm’. |
| ```apiKey``` <br><span class="required">REQUIRED</span> | Your API Key. |


### Response

> Example request with polling url:

```json
{
  "SessionKey": "ab5b948d616e41fb954a4a2f6b8dde1a_ecilpojl_7CAAD17D0CFC34BFDE68DEBFDFD548C7",
  "Query": {
    "Country": "GB",
    "Currency": "GBP",
    "Locale": "en-gb",
    "Adults": 1,
    "Children": 0,
    "Infants": 0,
    "OriginPlace": "2343",
    "DestinationPlace": "13554",
    "OutboundDate": "2017-05-30",
    "InboundDate": "2017-06-02",
    "LocationSchema": "Default",
    "CabinClass": "Economy",
    "GroupPricing": false
  },
  "Status": "UpdatesComplete",
  "Itineraries": [
    {
      "OutboundLegId": "11235-1705301925--32480-0-13554-1705302055",
      "InboundLegId": "13554-1706020700--32480-0-11235-1706020820",
      "PricingOptions": [
        {
          "Agents": [
            4499211
          ],
          "QuoteAgeInMinutes": 0,
          "Price": 83.41,
          "DeeplinkUrl": "http://partners.api.skyscanner.net/apiservices/deeplink/v2?_cje=jzj5DawL5zJyT%2bnfe1..."
        },
        ...
        ]
      "BookingDetailsLink": {
        "Uri": "/apiservices/pricing/v1.0/ab5b948d616e41fb954a4a2f6b8dde1a_ecilpojl_7CAAD17D0CFC34BFDE68DEBFDFD548C7/booking",
        "Body": "OutboundLegId=11235-1705301925--32480-0-13554-1705302055&InboundLegId=13554-1706020700--32480-0-11235-1706020820",
        "Method": "PUT"
      }
    },
    ...
   ],
  "Legs": [
    {
      "Id": "11235-1705300650--32302,-32480-1-13554-1705301100",
      "SegmentIds": [
        0,
        1
      ],
      "OriginStation": 11235,
      "DestinationStation": 13554,
      "Departure": "2017-05-30T06:50:00",
      "Arrival": "2017-05-30T11:00:00",
      "Duration": 250,
      "JourneyMode": "Flight",
      "Stops": [
        13880
      ],
      "Carriers": [
        885,
        881
      ],
      "OperatingCarriers": [
        885,
        881
      ],
      "Directionality": "Outbound",
      "FlightNumbers": [
        {
          "FlightNumber": "290",
          "CarrierId": 885
        },
        {
          "FlightNumber": "1389",
          "CarrierId": 881
        }
      ]
    },
    ...
   ],
   "Segments": [
    {
      "Id": 0,
      "OriginStation": 11235,
      "DestinationStation": 13880,
      "DepartureDateTime": "2017-05-30T06:50:00",
      "ArrivalDateTime": "2017-05-30T07:55:00",
      "Carrier": 885,
      "OperatingCarrier": 885,
      "Duration": 65,
      "FlightNumber": "290",
      "JourneyMode": "Flight",
      "Directionality": "Outbound"
    },
    ...
  ],
    "Carriers": [
    {
      "Id": 885,
      "Code": "BE",
      "Name": "Flybe",
      "ImageUrl": "http://s1.apideeplink.com/images/airlines/BE.png",
      "DisplayCode": "BE"
    },
    ...
  ],
  "Agents": [
    {
      "Id": 1963108,
      "Name": "Mytrip",
      "ImageUrl": "http://s1.apideeplink.com/images/websites/at24.png",
      "Status": "UpdatesComplete",
      "OptimisedForMobile": true,
      "BookingNumber": "+448447747881",
      "Type": "TravelAgent"
    },
    ...
  ],
  "Places": [
    {
      "Id": 11235,
      "ParentId": 2343,
      "Code": "EDI",
      "Type": "Airport",
      "Name": "Edinburgh"
    },
    ...
  ],
  "Currencies": [
    {
      "Code": "GBP",
      "Symbol": "£",
      "ThousandsSeparator": ",",
      "DecimalSeparator": ".",
      "SymbolOnLeft": true,
      "SpaceBetweenAmountAndSymbol": false,
      "RoundingCoefficient": 0,
      "DecimalDigits": 2
    },
    ...
  ]
}
```

The response contains a list of itineraries with, for each one, a list of pricing options containing:

* the price
* the quote age
* the agent selling the itinerary
* the deeplink to the agent


*RESPONSE PARAMETERS*

| Parameter | Description |
| ------- | ------ |
| `SessionKey` | The Session key to identify the session. |
| `Query` | A copy of the query which was submitted. |
| `Status` | The status of the session – ‘UpdatesPending’ or ‘UpdatesComplete’. |
| `Itineraries` | A list of itineraries - see below for the itinerary object. |
| `Legs` | Details of the legs that make up the itineraries: airports, times, overall duration, stops and carrier ids. |
| `Segements` | Details of the segments of each leg. Including the carrier (or marketing carrier) and the operating carrier. |
| `Carriers` | Details of the carriers.  |
| `Agents` | Details of the agents who sell the tickets. Can be an airline or a travel agent. |
| `Places` | A list of all the places that appear in the itineraries. |
| `Currencies` | A list of the currencies shown in the response. |

<aside class="notice">
You must keep polling the results until you get 'UpdatesComplete'. The results may take up to a minute to update.
</aside>

*ITINERARY PARAMETERS*

| Element | Description |
| ------- | ------ |
| `OutboundLegId` | Id of the Outbound Leg |
| `InboundLegId` | Id of the Inbound Leg |
| `PricingOptions` | pricing options with agent(s)<br>the quote age<br> price (total for all passengers)<br>deeplink URL (the absolute URL needed to make the booking).  The deeplink URL is not supplied for multi-booking itineraries.<br>In the case where deeplinks are not supplied, you can obtain them with a further step. Refer to the Create/Poll Booking Details documentation. |
| `BookingDetailsLink` | In some cases (multiple bookings or group prices) you will need to make a second call to retrieve the deeplinks. See the next section [Get booking details](#get-booking-details) for details |



Please note that you must not force users to your deeplink. Once they have made a search you should provide them with a list of results, and an option to filter that list, in order to pick the flight that best suits their needs.

<aside class="warning">
Please add the no-follow attribute when you link to the deeplink. See <a href="https://support.business.skyscanner.net/hc/en-us/articles/206800309-How-do-I-add-the-nofollow-attribute-to-the-deeplinks-calls-" target="_blank">How do I add the nofollow attribute?</a> in the FAQ section.
</aside>



## Get booking details

### Request

```shell
curl "http://partners.api.skyscanner.net/apiservices/pricing/v1.0/
    {SessionKey}/booking&apikey={apiKey}"
    -d 'OutboundLegId={OutboundLegId}&InboundLegId={InboundLegId}'
    -X PUT
    -H "Content-Type: application/x-www-form-urlencoded"
```

In cases where more than one booking is required (for example when buying from 2 or more different airlines or travel agents) or for group pricing,
a Booking Details request must be made to get the list of all the deeplinks or update the deeplink with additional information such as number of passengers.

> The url is provided in the response of the live prices:

```json
"BookingDetailsLink": {
        "Uri": "/apiservices/pricing/v1.0/abb2a69708624a7ca82762ed73493598_ecilpojl_DCE634A426CBDA30CE7EA3E9068CD053/booking",
        "Body": "OutboundLegId=11235-1705301925--32480-0-13554-1705302055&InboundLegId=13554-1706020700--32480-0-11235-1706020820",
        "Method": "PUT"
      }
```

The full url and body content are provided in the response from the live pricing results.



*API endpoint*

`PUT /pricing/v1.0/{SessionKey}/booking`


*TRY IT OUT*

[![Run in Postman](https://run.pstmn.io/button.svg)](https://app.getpostman.com/run-collection/31ff523d2ff9186107e1)
 
 or go to our [test harness](http://business.skyscanner.net/portal/en-GB/Documentation/FlightsLivePricingQuickStart)

*REQUEST PARAMETERS*

| Parameter | Description |
| --------- | ------- |
| ```apiKey``` <br><span class="required">REQUIRED</span> | Your API Key. |

*REQUEST BODY (from live pricing response)*

| Parameter | Description |
| --------- | ------- |
| ```OutboundLegId``` <br><span class="required">REQUIRED</span> | The outbound leg Id of the itinerary |
| ```InboundLegId``` <br><span class="optional">OPTIONAL</span> | The inbound leg Id of the itinerary for return flights |
| ```adults``` <br><span class="optional">OPTIONAL</span> | Number of adults (16+ years). Must be between 1 and 8.  |
| ```children``` <br><span class="optional">OPTIONAL</span> | Number of children (1-16 years). Can be between 0 and 8.  |
| ```infants``` <br><span class="optional">OPTIONAL</span> | Number of infants (under 12 months). Can be between 0 and 8.  |


### Response

> Example response:

```shell
Location "http://partners.api.skyscanner.net/apiservices/pricing/uk1/v1.0/
    {SessionKey}/booking/
    {OutboundLegId};{InboundLegId}"
```

A successful response contains no content. The URL to poll the booking details is specified in the Location header of the response.

<aside class="warning">
Please refer to our <a href="#response-codes">response codes</a> in case of unsuccessful response.
</aside>

*RESPONSE PARAMETERS*

| Element | Detail |
| ------- | ------ |
| `Location Header` | Contains the URL for polling the booking details results |



## Polling the booking details

### Request

```shell
curl "http://partners.api.skyscanner.net/apiservices/pricing/uk1/v1.0/
    {SessionKey}/booking/
    {OutboundLegId};{InboundLegId}
    ?apiKey={apiKey}"
    -X GET
```

*API endpoint*

`GET /pricing/v1.0/{SessionKey}/booking/{OutboundLegId};{InboundLegId}`


*TRY IT OUT*

[![Run in Postman](https://run.pstmn.io/button.svg)](https://app.getpostman.com/run-collection/31ff523d2ff9186107e1)

or go to our [test harness](http://business.skyscanner.net/portal/en-GB/Documentation/FlightsLivePricingQuickStart)


*REQUEST PARAMETERS*

| Parameter | Description |
| --------- | ------- |
| ```apiKey``` <br><span class="required">REQUIRED</span> | Your API Key. |

*REQUEST BODY (from live pricing response)*

| Parameter | Description |
| --------- | ------- |
| ```OutboundLegId``` <br><span class="required">REQUIRED</span> | The outbound leg Id of the itinerary |
| ```InboundLegId``` <br><span class="optional">OPTIONAL</span> | The inbound leg Id of the itinerary for return flights |
| ```adults``` <br><span class="optional">OPTIONAL</span> | Number of adults (16+ years). Must be between 1 and 8.  |
| ```children``` <br><span class="optional">OPTIONAL</span> | Number of children (1-16 years). Can be between 0 and 8.  |
| ```infants``` <br><span class="optional">OPTIONAL</span> | Number of infants (under 12 months). Can be between 0 and 8.  |




### Response

```json
{
  "Segments": [
    {
      "Id": 1,
      "OriginStation": 11235,
      "DestinationStation": 13554,
      "DepartureDateTime": "2017-05-30T19:25:00",
      "ArrivalDateTime": "2017-05-30T20:55:00",
      "Carrier": 881,
      "OperatingCarrier": 881,
      "Duration": 90,
      "FlightNumber": "1463",
      "JourneyMode": "Flight",
      "Directionality": "Outbound"
    },
    ...
  ],
  "BookingOptions": [
    {
      "BookingItems": [
        {
          "AgentID": 4499211,
          "Status": "Current",
          "Price": 83.41,
          "Deeplink": "http://partners.api.skyscanner.net/apiservices/deeplink/v2?_cje=jzj5DawL5[...]",
          "SegmentIds": [
            1,
            2
          ]
        }
      ]
    },
  ],
    "Places": [
    {
      "Id": 13554,
      "ParentId": 4698,
      "Code": "LHR",
      "Type": "Airport",
      "Name": "London Heathrow"
    },
    ...
  ],
  "Carriers": [
    {
      "Id": 881,
      "Code": "BA",
      "Name": "British Airways",
      "ImageUrl": "http://s1.apideeplink.com/images/airlines/BA.png"
    }
  ],
  "Query": {
    "Country": "GB",
    "Currency": "GBP",
    "Locale": "en-gb",
    "Adults": 1,
    "Children": 0,
    "Infants": 0,
    "OriginPlace": "2343",
    "DestinationPlace": "13554",
    "OutboundDate": "2017-05-30",
    "InboundDate": "2017-06-02",
    "LocationSchema": "Default",
    "CabinClass": "Economy",
    "GroupPricing": false
  }
 }
```

The response contains a list of itineraries with, for each one, a list of pricing options containing:

* the price
* the quote age
* the agent selling the itinerary
* the deeplink to the agent


*RESPONSE PARAMETERS*

| Parameter | Description |
| ------- | ------ |
| `SessionKey` | The Session key to identify the session. |
| `Query` | A copy of the query which was submitted. |
| `Status` | The status of the session – ‘UpdatesPending’ or ‘UpdatesComplete’. |
| `Itineraries` | A list of itineraries - see below for the itinerary object. |
| `Legs` | Details of the legs that make up the itineraries: airports, times, overall duration, stops and carrier ids. |
| `Segements` | Details of the segments of each leg. Including the carrier (or marketing carrier) and the operating carrier. |
| `Carriers` | Details of the carriers.  |
| `Agents` | Details of the agents who sell the tickets. Can be an airline or a travel agent. |
| `Places` | A list of all the places that appear in the itineraries. |
| `Currencies` | A list of the currencies shown in the response. |
