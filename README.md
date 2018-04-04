# KyrioServices
The 10,000 foot view of the Kyrio API

## Overview
OVERVIEW
The Kyrio Connection Suite (KCS) is a digital commerce tool that allows users to identify which multisystem operators (MSOs) may offer voice, video, data or other services to a particular business address; obtain additional details about available cable services from MSOs; and identify providers of and details about other business services as may be offered from time to time.  

This focus of this specification is the Cable Business Serviceability API which allows an authorized user to identify the cable company or companies that serve a specific business address.

The API request utilizes the RESTful GET method. The address and a valid 6-digit client ID (assigned by Kyrio) must be provided in the request.  For US addresses, the request must contain, at a minimum, the first line of the street address (street number, street name, street suffix) and either the 5-digit zip code or the city and state.  Additional address components such as suite number should be included where relevant.  Finally, the request can indicate if the response is desired in JSON or XML format.

A successful response may contain information for one or more MSOs that serve the address.  For each provider, a provider ID and provider name will be returned.  Additionally, the response will include a location type as defined by the MSO (residential, business or unknown), the site status (on-net, near-net, off-net, survey-required or proximity), and a unique location ID, if available.  A successful response containing no provider information indicates that an MSO was not found for the address.

An error response will include a status code along with a message.  A list of error codes is provided later in this document.


## Accessing the API
In order to use the API, you will need an account with Kyrio. Please contact Kyrio and specify "Online Services" as the line of business. https://www.kyrio.com/contact/

## SDK's
You can use our available SDK's that are available in C#, Java, and Node. This manages serialization/deserialization, URL management, API version and so forth.

* C#
* Java
* NodeJS

## Submitting Requests Directly
You may also code directly against the API without using the SDK if you prefer.
Request should be submitted to the following location using the GET command.  (Note: The POST command cannot be used.)

## API Description

### Request Headers

|Header|Type|Required|Default|Description|Examples|
|------|----|--------|-------|-----------|--------|
|`client-id`|string|yes||Specifies the 6-digit ID provided by Kyrio to requestor|client-id:123456|
|`accept`|string||application/json|Specifies the request format as json or xml.  The default is json.|accept:application/xml|
|`enable-test-mock`|bool||false|Enables test requests whih return mock responses. This can be used to test connectivity and that data is returned.|enable-test-mock:true|
|`enable-test-error`|bool||false|Works in conjunction with enable-test-mock. Enables random errors (up to 10% of requests) while making test requests.|enable-test-mock:true|

### Request Parameters
|Parameter Name|Type|Required|Max Length|Default|Description|
|---|---|---|---|---|---|
|`address_line1`|string|yes|60||Street number, pre-directional, street name, suffix, post-directional<br>123 N Main St<br>234 Michigan Ave SW|
|`address_line2`|string||60||Secondary address line such as Apt, Suite or Lot|
|`city`|string||60||City or town name|
|`state`|string||2||For US addresses, use the standard 2-character state abbreviation|
|`postal_code`|string||10||For US addresses, use the 5-digit ZIP code|
|`country_code`|string||2|US|Use 'US' to indicate US addresses. Refer to the [ISO 3166 Country Code Standard](https://www.iso.org/iso-3166-country-codes.html) for non-US addresses|

### Response Parameters
| JSON Element | XML Element  | Always<br>Returned| Type | Max Length | Description
|---|---|---|---|---|---|
| `provider_id` | `ProviderId`|yes |string|10|a 4-digit identifier will be returned for each provider.|
|`provider`|`Provider`|yes|string|30|Company name associated with the provider id|
|`location_id`|`LocationId`||string|40|Only returned if the provider has assigned a unique location identifier (aka housekey) for the address|
|`location_type`|`LocationType`|yes|string|40|This field will contain the value ‘residential’ or ‘business’ if the provider characterizes the location.  Otherwise a value of ‘unknown’ will be returned.|
|`site_status`|`SiteStatus`|yes|string|20|For MSO providers, this will contain one of the values in the table below:|

#### SiteStatus values
|Value|Description  |
|--|--|
|`none`	  |  Indicates that the MSO did not specify a site status  |
|`on_net`| Indicates that one or more cable services are currently available at the address|
|`near_net`|Indicates the address is near existing cable infrastructure and can likely be served at a reasonable cost/effort |
|`off_net`|Indicates the MSO has previously surveyed the site and determined it cannot be served|
|`survey_req`|Indicates the MSO must conduct additional analysis to determine if the site can be served|
|`proximity`|Indicates that the MSO serves the general area such as the 5-digit ZIP code or locations within a configurable distance (such as 60-feet or 200-feet).  The MSO should be contacted for additional information about the serviceability of a location identified as ‘proximity’.|
