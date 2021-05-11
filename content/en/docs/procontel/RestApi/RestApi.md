---
Title: "REST API configuration"
Weight: 8
Description: >
  Learn how to use the REST API configuration
---

# REST API

## Introduction

_REST API_ is an exposed interface via HTTP to get some internal ProconTel information about endpoints.

## What you can find here?

This documentation contains information about REST API configuration and exposed data. 

## Table of Contents

1. [Prerequisites](#prerequisites)
2. [Common definitions](#common-definitions)
	* [Queue Info](#common-definitions-queueinfo)
	* [Endpoint Information](#common-definitions-endpointinformation)
3. [Exposed methods](#exposed-methods)
	* [Get single endpoint details](#api-method-endpoint-get)
	* [Get all endpoints details](#api-method-endpoints-browse)
	* [Get activated endpoints](#api-method-endpoints-allrunning)
	* [Get deactivated endpoints](#api-method-endpoints-alldisabled)

<div id='prerequisites'/>

## Prerequisites
To use _REST API_: 
1. Install the newest version of _ProconTEL_.
2. Configure it:
	* open your **_Server Configuration Manager_** and set property **_General\Enable statistics monitoring mechanism_** to **_True_**.  
	* optionally change default address used for _REST API_ by editing _General\Event Hub REST API address_. Notice that each ProconTel installation on a server needs its individual Event Hub REST API address.
	* **restart** following windows services: _ProconTEL Administration_, _ProconTEL Event Hub_.  

<div id='exposed-methods'/>

## Common definitions

<div id='common-definitions'/>

Definition of structures used in API methods response bodies.

<div id='common-definitions-queueinfo'/>


### QueueInfo
Contains information about endpoint's message queues.

```
QueueInfo{
	queueId         string				//id of the queue
	containerId     string				//id of communication channel
	contentId       string				//id of message
	providerId      string				//id of message provider
	limit           integer($int32)		//maximum size of the queue
	usage           integer($int32)		//current size of the queue
}
``` 

<div id='common-definitions-endpointinformation'/>

### EndpointInformation
Contains details of an endpoint.

```
EndpointInformation{
	name            string					//name of the endpoint
	id              string					//id (GUID) of the endpoint
	containerId     string					//id of channel or pool
	startTime       string($date-time)		//last activation date time
	stopTime        string($date-time)		//last deactivation date time
	queueInfo       [QueueInfo]					//endpoint message queues
	channels        [string]				//ids of available communication channels
}
```

<div id='exposed-methods'/>

## Exposed methods

There is a list of available REST API methods. 
To get familiar with response body structures, see Common definitions above.


**Disclaimer!**
There are special standard endpoints: `TcpTelegramHandler`, `UdpTelegramHandler` and `MailslotTelegramHandler`. 
For these endpoints API can return misleading information about QueueInfos when there is more than one communication partner connected.

<div id='api-method-endpoint-get'/>

### Get single endpoint details
Method returns details of a single endpoint.

Example URL: `http://localhost:9500/api/Endpoints/Get/{id}`

Type: GET

Parameters: {id} - GUID of an endpoint

Response: single `EndpointInformation`.

Response body example:
```json
{
  "name": "MyEndpoint1",
  "id": "a11d8404-0b4e-4c63-8687-c6918c8579f5",
  "containerId": "b4b99de4-2622-4e29-a664-09a8a010e7a1",
  "startTime": "2021-04-27T05:39:54.2759536",
  "stopTime": "2021-04-27T06:06:02.7027978",
  "queueInfo": [
      {
        "queueId": "6eddedab-28cc-49d9-be21-5563d2993b21BenchmarkMessagebdb3839a-6531-450d-abe7-5d84f233dd97",
        "containerId": "6eddedab-28cc-49d9-be21-5563d2993b21",
        "contentId": "BenchmarkMessage",
        "providerId": "bdb3839a-6531-450d-abe7-5d84f233dd97",
        "limit": 1000,
        "usage": 0
      },
      {
        "queueId": "6eddedab-28cc-49d9-be21-5563d2993b21AnyCustomMessageb3131272-91bc-4c04-bb8c-1b2499c2487f",
        "containerId": "6eddedab-28cc-49d9-be21-5563d2993b21",
        "contentId": "AnyCustomMessage",
        "providerId": "b3131272-91bc-4c04-bb8c-1b2499c2487f",
        "limit": 1000,
        "usage": 0
      },
      {
        "queueId": "mainQueue",
        "containerId": "",
        "contentId": "",
        "providerId": "",
        "limit": 1000,
        "usage": 0
      }
    ],
  "channels": [
    "6eddedab-28cc-49d9-be21-5563d2993b21"
  ]
}
```

<div id='api-method-endpoints-browse'/>

### Get all endpoints details
Method returns details of all added endpoints.

Example URL: `http://localhost:9500/api/Endpoints/Browse`

Type: GET

Response: collection of `EndpointInformation`.

Response body example:
```json
[
  {
    "name": "MyEndpoint1",
    "id": "a11d8404-0b4e-4c63-8687-c6918c8579f5",
    "containerId": "b4b99de4-2622-4e29-a664-09a8a010e7a1",
    "startTime": "2021-04-27T05:39:54.2759536",
    "stopTime": "2021-04-27T06:06:02.7027978",
    "queueInfo": [
      {
        "queueId": "mainQueue",
        "containerId": "",
        "contentId": "",
        "providerId": "",
        "limit": 1000,
        "usage": 0
      }
    ],
    "channels": [
      "6eddedab-28cc-49d9-be21-5563d2993b21"
    ]
  },
  {
    "name": "MyEndpoint2",
    "id": "39caada1-276b-44e6-b102-18825b633dd6",
    "containerId": "d8e52b05-be50-443d-8562-67fc89c9e508",
    "startTime": "2021-04-26T08:21:13.1265721",
    "stopTime": "2021-04-26T13:11:18.0825706",
    "queueInfo": [
      {
        "queueId": "mainQueue",
        "containerId": "",
        "contentId": "",
        "providerId": "",
        "limit": 1000,
        "usage": 0
      }
    ],
    "channels": [
      "6eddedab-28cc-49d9-be21-5563d2993b21"
    ]
  }
]
```

<div id='api-method-endpoints-allrunning'/>

### Get activated endpoints
Method returns details of all activated endpoints.

Example URL: `http://localhost:9500/api/Endpoints/AllRunningEndpoints`

Type: GET

Response: collection of `EndpointInformation`.

<div id='api-method-endpoints-alldisabled'/>

### Get deactivated endpoints
Method returns details of all not activated endpoints.

Example URL: `http://localhost:9500/api/Endpoints/AllDisabledEndpoints`

Type: GET

Response: collection of `EndpointInformation`.

