# KyrioServices
The 10,000 foot view of the Kyrio API

## Overview
The Kyrio Connection Suite (KCS) is a digital commerce tool that allows users to identify which multisystem operators (MSOs) may offer voice, video, data or other services to a particular business address; obtain additional details about available cable services from MSOs; and identify providers of and details about other business services as may be offered from time to time.  

This focus of this specification is the Cable Business Serviceability API which allows an authorized user to identify the cable company or companies that serve a specific business address.

The API request utilizes the RESTful GET method. The address and a valid 6-digit client ID (assigned by Kyrio) must be provided in the request.  For US addresses, the request must contain, at a minimum, the first line of the street address (street number, street name, street suffix) and either the 5-digit zip code or the city and state.  Additional address components such as suite number should be included where relevant.  Finally, the request can indicate if the response is desired in JSON or XML format.

A successful response may contain information for one or more MSOs that serve the address.  For each provider, a provider ID and provider name will be returned.  Additionally, the response will include a location type as defined by the MSO (residential, business or unknown), the site status (on-net, near-net, off-net, survey-required or proximity), and a unique location ID, if available.  A successful response containing no provider information indicates that an MSO was not found for the address.

An error response will include a status code along with a message.  A list of error codes is provided later in this document.


## Accessing the API
In order to use the API, you will need an account with Kyrio. Please contact Kyrio and specify "Online Services" as the line of business. https://www.kyrio.com/contact/

## SDK's
You can use our available SDK's that are available in C#, Java, and Node. This manages serialization/deserialization, URL management, API version and so forth.

* [C#](https://github.com/KyrioServices/kyrio-services-sdk-dotnet ".Net SDK")
* [Java](https://github.com/KyrioServices/kyrio-services-sdk-java "Java SDK")
* [NodeJS](https://github.com/KyrioServices/kyrio-services-sdk-node "Node SDK")

## Submitting Requests Directly
You may also code directly against the API without using the SDK if you prefer.
Request should be submitted to the API URL using the GET command.  (The POST command cannot be used.)

## API Description

### Request Headers

|Header|Type|Required|Default|Description|Examples|
|------|----|--------|-------|-----------|--------|
|`client-id`|string|yes||Specifies the 6-digit ID provided by Kyrio to requestor|client-id:123456|
|`accept`|string||application/json|Specifies the request format as json or xml.  The default is json.|accept:application/xml|
|`enable-test-mock`|bool||false|Enables test requests whih return mock responses. This can be used to test connectivity and that data is returned.|enable-test-mock:true|
|`enable-test-error`|bool||false|Works in conjunction with enable-test-mock. Enables random errors (up to 10% of requests) while making test requests.|enable-test-error:true|

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

## Sample Messages

The following examples can be used for troubleshooting or developing against the API directly. The SDK's automatically serialize request and response objects, construct the url, and manage headers.

### Request Examples

The following examples show a request to the QA environment using:
* a client-id of **123456**
* a business address of **123 E Main St, Suite 3, Louisville, CO 80021**.

*Important:  It is not possible to run the sample requests as-is.  Call-specific parameters such as client-id must be replaced with your own values.*

#### JSON Request
```
GET https://api.qa.kyrioconnectionsuite.com/business/api/v1/serviceability?address_line1=123%20E%20Main%20St&address_line2=Suite%203&city=Louisville&state=CO&postal_code=80021
client-id: 123456
accept: application/json
```

#### XML Request
```
GET 
https://api.qa.kyrioconnectionsuite.com/business/api/v1/serviceability?address_line1=123%20E%20Main%20St&address_line2=Suite%203&city=Louisville&state=CO&postal_code=80021 
client-id: 123456
accept: application/xml
```

### Response Examples

The following examples show potential responses.  Note that successful responses use HTTP status code 200.

#### Response containing a single provider
JSON
```
[
 {
 "provider_id":"1005",
 "provider":"Comcast",
 "location_id":"123Ab5",
 "location_type":"business",
 "site_status":"on_net"
 }
]
```
XML
```
<ArrayOfServiceabilityResult>
	<ServiceabilityResult>
		<ProviderId>1005</ProviderId>	
		<Provider>Comcast</Provider>	
		<LocationId>123Ab5</LocationId>
 		<LocationType>business</LocationType>
		<SiteStatus>on_net</SiteStatus>
	</ServiceabilityResult>
</ArrayOfServiceabilityResult> 
```
#### Response containing multiple providers
JSON
```
[
	{
		"provider_id":"1011",
		"provider":"Charter",
		"location_id":"11299833",
		"location_type":"business",
		"site_status":"near_net"	
	},
	{
		"provider_id":"1005",
		"provider":"Comcast",
		"location_id":"123Ab5",
		"location_type":"business",
		"site_status":"on_net"	
	}
] 
```
XML
```
<ArrayOfServiceabilityResult>
	<ServiceabilityResult>
		<ProviderId>1011</ProviderId>	
		<Provider>Charter</Provider>	
		<LocationId>11299833</LocationId>
 		<LocationType>business</LocationType>
		<SiteStatus>near_net</SiteStatus>
	</ServiceabilityResult>
	<ServiceabilityResult>
	      <ProviderId>1005</ProviderId>	
		<Provider>Comcast</Provider>	
		<LocationId>123Ab5</LocationId>
 		<LocationType>business</LocationType>
		<SiteStatus>on_net</SiteStatus>
	</ServiceabilityResult>
</ArrayOfServiceabilityResult>
```


#### Response containing no providers
JSON
```
[]
```
XML
```
<ArrayOfServiceabilityResult/>
```

#### Error Responses
An error response will contain a non-200 HTTP status code and an informational message as a string.  Please see the next section for a list of HTTP status codes.

JSON
```
Bad Request: Missing address_line1 parameter
```

XML
```
<string>Bad Request: Missing address_line1 parameter</string>
```
### HTTP STATUS CODES & ERROR MESSAGES
The table below lists the various errors that may be contained in the response.

|HTTP Status Code|Message|Examples|
|----|----|---|
|`200` |Success||
|`400` |Bad Request: {additional details}|Missing address_line1 parameter||
|`401` |Unauthorized||
|`403` |Forbidden||
|`404` |Not Found||
|`405` |Method Not Allowed||
|`500` |Server Error||
|`504` |Timeout||

### Provider IDs
The following table lists the four-digit ID for participating cable providers (MSOs). This ID corresponds to the provider_id value contained in the response message.  Although some MSOs have merged (such as Charter, Time Warner Cable & Bright House Network), the legacy companies are still represented individually in this system.  Over time, these will likely merge into a single ID.

|Provider ID|Cable Company Name|
|---|---|
|`1021`	|Armstrong|
|`1028`	|Atlantic Broadband|
|`1022`	|BendBroadband|
|`1017`	|Bright House|
|`1016`	|Cable ONE|
|`1015`	|Cablevision|
|`1011`	|Charter|
|`1005`	|Comcast|
|`1010`	|Cox|
|`1024`	|Massillion Cable (aka MCTV)|
|`1014`	|Mediacom|
|`1030`	|MetroCast|
|`1025`	|Midco|
|`1018`	|Suddenlink|
|`1002`	|Time Warner Cable|
