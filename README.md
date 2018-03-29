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

## Submitting Requests

To retrieve all providers that serve a particular business address, the request should be submitted to the following location using the GET command.  (Note: The POST command cannot be used.)

## API Description

### Request Headers

|Header|Type|Required|Default|Description|Examples|
|------|----|--------|-------|-----------|--------|
|`client-id`|string|yes||Specifies the 6-digit ID provided by Kyrio to requestor|client-id:123456|
|`accept`|string|yes|application/json|Specifies the request format as json or xml.  The default is json.|accept:application/xml|
|`enable-test-mock`|bool||false|Enables test requests whih return mock responses. This can be used to test connectivity and that data is returned.|enable-test-mock:true|
|`enable-test-error`|bool||false|Works in conjunction with enable-test-mock. Enables random errors (up to 10% of requests) while making test requests.|enable-test-mock:true|
