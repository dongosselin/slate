---
title: Configuration API

language_tabs:
  - http
  - json

toc_footers:
  - <a href='https://github.com/tripit/slate'>Documentation Powered by Slate</a>

search: true
---


# Configuration API

The Configuration API is a secure service requiring an authentication token. These service API convert the incoming JSON objects to REST Configuration Service, call the IS Tier Configuration (REST) service. Upon received response from Configuration Service, Same will be sent back to Clients.
The API supports these functions:

* Validate incoming request and throw appropriate errors.
* Returns available configurations based on input REST request.

## Version
1.0 (Targeted for Nov 2016 release) 

## Usage
Applications need the ability to change their behavior without changing the code. This is done though configurations. These configurations can be done locally or remotely. While local configurations are very effective at storing end user preferences, remote configurations are generally a better choice for storing global configurations that remain under control of the system administrators. The Configurations API is a tool than can be used by multiple consumers (ex KP Mobile, RWD) to retrieve such global configuration values that are under the control of KP DPT admins.

The configuration values returned by the API are global, in that all users of the system will see the same value. They are identified by a specific configuration name. Each application has it's own list of configurations. So, Mobile Flagship and RWD KP.org may both have a configuration (Time Out Period) but maintain different values appropriate for the different channels.

## API Behavior
This API calls the Configuration Service to retrieve values for given configuration name. This API will support both Single and Multiple results mode for given query.
Single Result Mode: For a given configuration and application name, the API will retrieve one configuration value. The Service/API will fetch that value and provide it to the client in JSON format.
Multi result Mode: Will allow multiple configuration values to be returned in a single API call. Ex : X-configName = Records_Per_Page, NumberOfBillsToShow (comma separated config names) (OR) X-configName = ALL ( All configurations and corresponding values available for provided application name (Flagship/KP Mobile)
The various operations generally require the following information to work:

* `X-configName`: This defines the configuration name to be queried. ex : Records_Per_Page

* `X-appName`: This defines application name to be queried. This is standard API header. Current version of API/Service will support only for Mobile consumers and accepts stranded mobile app name as Flagship.

# Request Response Elements

## HTTP Request
API URI: https://api.kaiserpermanente.org/mycare/v1.0/configurations - GET

## HTTP Request Headers

Header Name | Required | Supported Values | Description
----------- | -------- | ---------------- | -----------
`X-useragentcategory` | Yes | I for iOS, A for Android, M for mobile web | User Agent Category
`X-osversion` | Yes | For example 5.1. | Device OS Version.
`X-useragenttype` | Yes | For example: iPhone5, Samsung etc. | Type of device for thick clients or user agent type in case of browser based Responsive Web Apps.
`X-appName` | Yes | For example KP Mobile. | Name of the App.
`ssosession` | Yes | Valid OAM token | ssosession header received from sign-on
`X-configName` | Yes | For example: Records_Per_Page,NumberOfBillsToShow	Generic configuration name

## HTTP Response Headers

Response Field | Description
-------------- | -----------
`configName` | Given configuration name
`listOfConfigs` | JSON Array which consists of List of configurations and corresponding values
`listOfConfigs`[i].configName | Given configuration name.
`listOfConfigs[i].application` | Given Application name.
`listOfConfigs[i].value` | Value of the specific configuration 

## HTTP Response Codes

HTTP Code | Description
--------- | -----------
200 | Success.
400 | Invalid {Input Field}, Please check the request.
403 | Access Denied. User is not entitled to access the requested API.
404 | Un-supported URI is requested.
412 | PreCondition failed. One or more required params or header missing.
503 | System error. Something went wrong while processing the request. Could be API or backend error.

# Sample Request Headers
The sample request headers are in addition to the standard API headers.


```http
>Single result mode request headers
X-configName = NumberOfBillsToShow
X-appName = Flagship

> Multi result mode sample request headers
X-configName = NumberOfBillsToShow,VideoVisitTypes
X-appName = Flagship

```

```json
> Single result mode sample response

[{
   "configName": "NumberOfBillsToShow",
   "listOfConfigs":    [
            {
         "configName": "NumberOfBillsToShow",
         "application": "Flagship",
         "value": "15"
      }
   ]
}]

> Multi result mode sample sample response

[
      {
      "configName": "NumberOfBillsToShow",
      "listOfConfigs":       [
                  {
            "configName": "NumberOfBillsToShow",
            "application": "Flagship",
            "value": "15"
         }
      ]
   },
      {
      "configName": "VideoVisitTypes",
      "listOfConfigs":       [
                  {
            "configName": "VideoVisitTypes",
            "application": "Flagship",
            "value": "[ “VideoVisit”, “Video Visit”, “Group Video Visit”]"
         }
      ]
   }
]
```





