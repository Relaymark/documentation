
# Relaymark Data engine REST API
<!-- TOC depthFrom:1 depthTo:6 insertAnchor:false orderedList:false updateOnSave:true withLinks:true -->

- [Relaymark Data engine REST API](#relaymark-data-engine-rest-api)
	- [Connect to the Relaymark API](#connect-to-the-relaymark-api)
		- [Request format](#request-format)
		- [Response format](#response-format)
	- [Declaration API](#declaration-api)
		- [DataSource](#datasource)
			- [Add a DataSource](#add-a-datasource)
			- [Update a DataSource](#update-a-datasource)
			- [List DataSources](#list-datasources)
			- [Get DataSource By Id](#get-datasource-by-id)
			- [Get DataSource By Sid](#get-datasource-by-sid)
			- [Delete a DataSource](#delete-a-datasource)
			- [Counter types](#counter-types)
			- [Event types](#event-types)
			- [DataChunk types](#datachunk-types)
		- [Registration](#registration)
			- [Add a registration](#add-a-registration)
			- [Update a registration](#update-a-registration)
			- [List registrations](#list-registrations)
			- [Get a registration By Id](#get-a-registration-by-id)
			- [Delete a registration](#delete-a-registration)
	- [Populate API](#populate-api)
		- [CounterSample](#countersample)
			- [Add or update a counterSample](#add-or-update-a-countersample)
			- [Add or update a batch of counterSamples](#add-or-update-a-batch-of-countersamples)
			- [List counterSamples](#list-countersamples)
			- [Delete a counterSample](#delete-a-countersample)
		- [Event](#event)
			- [Add an event](#add-an-event)
			- [Get an event](#get-an-event)
			- [List events](#list-events)
		- [DataChunk](#datachunk)
			- [Add a dataChunk](#add-a-datachunk)
			- [Update a dataChunk](#update-a-datachunk)
			- [List dataChunks](#list-datachunks)
			- [Get a dataChunk by Id](#get-a-datachunk-by-id)
			- [Delete a dataChunk by Id](#delete-a-datachunk-by-id)
	- [Consume API](#consume-api)
		- [Counters](#counters)
			- [List counters](#list-counters)
			- [Query counter data](#query-counter-data)
		- [Events](#events)
			- [List accessible eventTypes](#list-accessible-eventtypes)
			- [Query events](#query-events)
		- [Search](#search)
			- [Query](#query)
			- [Definition](#definition)
	- [Annex](#annex)
		- [Ids and short ids](#ids-and-short-ids)
		- [LocalizedString object](#localizedstring-object)
		- [Paged requests](#paged-requests)

<!-- /TOC -->

Relaymark Data Engine is a project that reconcile the complex organisational structure of retail networks
with the management and sharing of statistical data between the actors of these networks. 

The project has 3 main objectives:

- to be able to render counters of a specific retail store as graphs.
A counter is a flow of numerical values over time. For example, the number of visitors of a particular retail store over time.
- to provide a generic solution for the management of events.
An event can be anything that happens to a registered target at a precise date. 
For example, A customer "A" entered the retail store "B". Or, the retail store "B" opened.
- to be able to make complex queries over the client's database of Customers, Companies, Networks. 
The client is also able to extend the search engine by providing its own set of data via the dataChunk system.

The API is separated into 3 group:

- The declaration API, where you define the structure of the data you want to push.
- The populate API, dedicated to pushing and administrating the data.
- The consume API, dedicated to the consumption of the data you have access to.

> ## Disclaimer
>
> Relaymark Data engine is still in it's early phase of development and is not ready for comercial use yet.
>
> Some part of it (or all) may radically change as the development continues.

## Connect to the Relaymark API

### Request format

### Response format

## Declaration API

It is the part of the API where you define the structure of the data you want to push.

> ### Requirements
> 
> You need to be connected as a user to the relaymark API (using oauth2 [Authorization code grant type](https://tools.ietf.org/html/rfc6749))
> and this user needs to be the manager of at least one company to be able to create a dataSource.

### DataSource

The DataSource represents the description of company's data flow. 
It describes what your company can provide for a particular target.

> A 'target' can be a customer, a company, a network, a location or a division present in the Relaymark Database.

#### Add a DataSource

- Path: /data-engine/declaration/data-sources
- HTTP verb: POST
- Description: This method add a new DataSource.

The post JSON Object can contain the following attributes:

| Name           | Type                   | Description                    |
|:-------------- |:---------------------- |:------------------------------ |
| sid            | String                 | The unique identifier for the DataSource (it must be in [kebab-case](http://c2.com/cgi/wiki?KebabCase)) |
| name           | LocalizedString object | The display name of the DataSource (see [LocalizedString object](#localizedstring-object)) |
| ownershipType  | String                 | Describe to what type of target the DataSource is owned. It can only have one of the following values: <ul><li>none (reserved for internal use)</li><li>company</li></ul>|
| ownerId        | String              	  | The owner of the DataSource. It must be the Id of an existing target (the type of the target is specified by OwnershipType). For more information about Ids, see [Ids and short ids](#ids-and-short-ids) |
| eventTypes     | Hash table (optional)  | A hashtable containing the descriptions of the events you can provide. the key is the eventType unique identifier (it must be in [kebab-case](http://c2.com/cgi/wiki?KebabCase)). The value is the eventType itself (see [Event types](#event-types)). |
| counterTypes   | Hash table (optional)  | A hashtable containing the descriptions of the counters you can provide. the key is the counterType unique identifier (it must be in [kebab-case](http://c2.com/cgi/wiki?KebabCase)). The value is the counterType itself (see [Counter types](#counter-types)). |
| dataChunkTypes | Hash table (optional)  | A hashtable containing the descriptions of the dataChunks you can provide. the key is the dataChunkType unique identifier (it must be in [kebab-case](http://c2.com/cgi/wiki?KebabCase)). The value is the dataChunkType itself (see [DataChunk types](#datachunk-types)). |

```
insert example request 
```

When the query is successful, the HTTP response is a 200 OK and returns the new DataSource:

```
insert successful response 
```

Errors:

| Error code     | Reason                 |
| -------------- |:---------------------- |
| 400            | invalid JSON. returns a JSON object describing the error. |

#### Update a DataSource

- Path: /data-engine/declaration/data-sources/{dataSourceShortId}
- HTTP verb: PUT
- Description: This method updates a DataSource.

The put JSON Object can contain the following attributes:

| Name                 | Type                   | Description                    |
|:-------------------- |:---------------------- |:------------------------------ |
| name                 | LocalizedString object | The display name of the DataSource (see [LocalizedString object](#localizedstring-object)) |
| eventTypes           | Hash table (optional)  | A hashtable containing the descriptions of the events you can provide. the key is the eventType unique identifier (it must be in [kebab-case](http://c2.com/cgi/wiki?KebabCase)). The value is the eventType itself (see [Event types](#event-types)). |
| counterTypes   	   | Hash table (optional)  | A hashtable containing the descriptions of the counters you can provide. the key is the counterType unique identifier (it must be in [kebab-case](http://c2.com/cgi/wiki?KebabCase)). The value is the counterType itself (see [Counter types](#counter-types)). |
| dataChunkTypes       | Hash table (optional)  | A hashtable containing the descriptions of the dataChunks you can provide. the key is the dataChunkType unique identifier (it must be in [kebab-case](http://c2.com/cgi/wiki?KebabCase)). The value is the dataChunkType itself (see [DataChunk types](#datachunk-types)). |
| allowHistoryDeletion | boolean                | if the value is false the call will returns an error whenever you make a change in the datasource that result to suppression of data (for example if you remove a counterType) |

```
insert example request 
```

When the query is successful, the HTTP response is a 200 OK and returns the updated DataSource:

```
insert successful response 
```

Errors:

| Error code     | Reason                 |
| -------------- |:---------------------- |
| 400            | invalid JSON. returns a JSON object describing the error. |

#### List DataSources

- Path: /data-engine/declaration/data-sources
- HTTP verb: GET
- Description: This method lists the DataSources you have access to. That includes the DataSources you are managing but also the DataSources other users have shared with you and all the global dataSources.

The query string accepts the following parameters:

| Name                 | Type                   | Description                    |
|:-------------------- |:---------------------- |:------------------------------ |
| page                 | Integer (default: 1)   | The number of the requested page (see [Paged requests](#paged-requests)). |
| pageSize             | Integer (default: 20)  | The size of the requested page (see [Paged requests](#paged-requests)). |

```
insert example request 
```

When the query is successful, the HTTP response is a 200 OK and returns a [paged list](#paged-requests) of DataSource:

```
{
	"totalResults": 366,
	"totalPages": 19,
	"page": 1,
	"results": [
		//... The objects your queried
	],
} 
```

#### Get DataSource By Id

- Path: /data-engine/declaration/data-sources/{dataSourceShortId}
- HTTP verb: GET
- Description: This method returns a single DataSource searched by its short id.

```
insert example request 
```

When the query is successful, the HTTP response is a 200 OK and returns the DataSource:

```
insert successful response 
```
Errors:

| Error code     | Reason                 |
| -------------- |:---------------------- |
| 404            | The specified resource was not found. |

#### Get DataSource By Sid

- Path: /data-engine/declaration/data-sources/sid/{sid}
- HTTP verb: GET
- Description: This method returns a single DataSource searched by its Sid.

```
insert example request 
```

When the query is successful, the HTTP response is a 200 OK and returns the DataSource:

```
insert successful response 
```
Errors:

| Error code     | Reason                 |
| -------------- |:---------------------- |
| 404            | The specified resource was not found. |

#### Delete a DataSource

- Path: /data-engine/declaration/data-sources/{dataSourceShortId}
- HTTP verb: DELETE
- Description: This method deletes a specified DataSource

> ## Important
> When you delete a DataSource, you will also delete EVERYTHING that is attached to it.
> All of the provided data will be lost without the possibility of recovery.

```
insert example request 
```

When the query is successful, the HTTP response is a 200 OK and returns nothing.

Errors:

| Error code     | Reason                 |
| -------------- |:---------------------- |
| 404            | The specified resource was not found. |

#### Counter types

A counter is a flow of numerical values over time. For example, the number of visitors of a particular retail store over time.

A counterType describes a counter and holds its configuration.

The counterType JSON Object contains the following attributes:

| Name                                | Type                   | Description                    |
|:----------------------------------- |:---------------------- |:------------------------------ |
| displayName                         | LocalizedString object | The display name of the CounterType (see [LocalizedString object](#localizedstring-object)). |
| description                         | LocalizedString object | The description of the CounterType (see [LocalizedString object](#localizedstring-object)). |
| categoryName   	                  | String                 | The category of the counterType. It is used to filter the counters in the list requests. |
| valueType                           | String                 | A hashtable containing the descriptions of the dataChunks you can provide. the key is the dataChunkType unique identifier (it must be in [kebab-case](http://c2.com/cgi/wiki?KebabCase)). The value is the dataChunkType itself (see [DataChunk types](#datachunk-types)). |
| frequencies                         | Array of strings       | An array of the frequencies that the client will provide. The specified values must be chosen from the list below: <ul><li>hourly</li><li>daily</li><li>weekly</li><li>monthly</li><li>quarterly</li><li>halfYearly</li><li>yearly</li></ul> |
| dynamicallyGenerateOtherFrequencies | Boolean                | If the value is true the engine will try to dynamically generate undeclared frequencies that are requested by aggregating other lower frequencies |
| allowedTargetTypes                  | Array of strings       | An array of the targetTypes for this counterType. The specified values must be chosen from the list below: <ul><li>company</li><li>network</li><li>location</li><li>customer</li></ul> |

```
insert example json object 
```

#### Event types

An event can be anything that happens to a registered target at a precise date. 
For example, A customer "A" entered the retail store "B". Or, the retail store "B" opened.

An eventType describes a particular type of event and holds its configuration.

The eventType JSON Object contains the following attributes:

| Name            | Type                   | Description                    |
|:--------------- |:---------------------- |:------------------------------ |
| displayName     | LocalizedString object | The display name of the eventType (see [LocalizedString object](#localizedstring-object)). |
| description     | LocalizedString object | The description of the eventType (see [LocalizedString object](#localizedstring-object)). |
| categoryName    | String                 | The category of the eventType. It is used to filter the events in the list requests. |
| targetType      | String                 | The targetType for this eventType. The specified value must be chosen from the list below: <ul><li>company</li><li>network</li><li>location</li><li>customer</li></ul>|
| displayTemplate | LocalizedString object | The template of the eventType. We use the [SmartFormat](https://github.com/scottrippey/SmartFormat) project to compile the template using the event object as data. |

```
insert example json object 
```

#### DataChunk types

A dataChunk is a set of data completing a targeted object.
For example you can add a dataChunk for the customer "A" specifiyng that its gender is "female", 
its favorite color is "blue" and last bought car model is "TESLA Model S".

A dataChunkType describes a particular set of dataChunks and holds its configuration.

The eventType JSON Object contains the following attributes:

| Name                                | Type                      | Description                    |
|:----------------------------------- |:------------------------- |:------------------------------ |
| targetType                          | String                    | The targetType for this dataChunkType. The specified value must be chosen from the list below: <ul><li>company</li><li>network</li><li>location</li><li>customer</li></ul> |
| properties                          | Array of Property objects | An array describing all the properties the dataChunk will specify. |

The property JSON Object contains the following attributes:

| Name | Type   | Description |
|:---- |:------ |:----------- |
| name | String | the name of the declared property. It must be a valid camelCase variable identifier. |
| type | String | the type of the declared property. It can be one of the following values: <ul><li>string</li><li>number</li><li>date</li></ul> |

```
insert example json object 
```

### Registration

A Registration is an implementation of a dataSource. It specifies that the owner of this registration will generate data described by the dataSource.

#### Add a registration

- Path: /data-engine/declaration/registrations
- HTTP verb: POST
- Description: This method add a new registration.

The post JSON Object can contain the following attributes:

| Name      | Type                    | Description                    |
|:--------- |:----------------------- |:------------------------------ |
| sourceSid | String                  | The Sid of a datasource you have access to (see [DataSource](#datasource)). |
| ownerType | String                  | The type of owner for the registration. It can be one of the following values: <ul><li>company</li><li>network</li><li>location</li><li>customer</li></ul> |
| ownerId   | String                  | The id of the owner (For more information about Ids, see [Ids and short ids](#ids-and-short-ids)). |
| counters  | Array of counter object | The counters declared by the registration. |

The counter JSON Object contains the following attributes:

| Name        | Type                   | Description |
|:----------- |:---------------------- |:----------- |
| name        | String                 | The internal identifier of the declared counter (it must be in [kebab-case](http://c2.com/cgi/wiki?KebabCase)). |
| type        | String                 | The type of the declared counter. It must a [counterType](#counter-types) declared by the registration's dataSource. |
| displayName | LocalizedString object | The display name of the counter (see [LocalizedString object](#localizedstring-object)). |

```
insert example request 
```

When the query is successful, the HTTP response is a 200 OK and returns the new registration:

```
insert successful response 
```

#### Update a registration

- Path: /data-engine/declaration/registrations/{registrationShortId}
- HTTP verb: PUT
- Description: This method update a registration.

The put JSON Object can contain the following attributes:

| Name      | Type                    | Description                    |
|:--------- |:----------------------- |:------------------------------ |
| counters  | Array of counter object | The counters declared by the registration. |

The counter JSON Object contains the following attributes:

| Name        | Type                   | Description |
|:----------- |:---------------------- |:----------- |
| name        | String                 | The internal identifier of the declared counter (it must be in [kebab-case](http://c2.com/cgi/wiki?KebabCase)). |
| type        | String                 | The type of the declared counter. It must a [counterType](#counter-types) declared by the registration's dataSource. |
| displayName | LocalizedString object | The display name of the counter (see [LocalizedString object](#localizedstring-object)). |

> ## Important
> When you delete a counter, you will also delete all the counterSamples attached to it.
> All of the provided data will be lost without the possibility of recovery.

```
insert example request 
```

When the query is successful, the HTTP response is a 200 OK and returns the updated registration:

```
insert successful response 
```

#### List registrations

- Path: /data-engine/declaration/registrations
- HTTP verb: GET
- Description: This method lists the registration you have access to.

The query string accepts the following parameters:

| Name                 | Type                   | Description                    |
|:-------------------- |:---------------------- |:------------------------------ |
| page                 | Integer (default: 1)   | The number of the requested page (see [Paged requests](#paged-requests)). |
| pageSize             | Integer (default: 20)  | The size of the requested page (see [Paged requests](#paged-requests)). |

```
insert example request 
```

When the query is successful, the HTTP response is a 200 OK and returns a [paged list](#paged-requests) of registrations:

```
{
	"totalResults": 366,
	"totalPages": 19,
	"page": 1,
	"results": [
		//... The objects your queried
	],
} 
```

#### Get a registration By Id

- Path: /data-engine/declaration/registrations/{registrationShortId}
- HTTP verb: GET
- Description: This method returns a single registration by its short id.

```
insert example request 
```

When the query is successful, the HTTP response is a 200 OK and returns the registration:

```
insert successful response 
```

#### Delete a registration

- Path: /data-engine/declaration/registrations/{registrationShortId}
- HTTP verb: DELETE
- Description: This method deletes a registration.

```
insert example request 
```

When the query is successful, the HTTP response is a 200 OK and returns nothing.

## Populate API

This Api is dedicated to pushing and administrating the data.

> ### Requirements
>  
> You need to be connected as a client to the Relaymark Api (using oauth2 [Client Credentials grant type](https://tools.ietf.org/html/rfc6749))
>
> There is no implementation yet that enables you to register a client to use the Populate Api.
> Contact us and we will create a client for you.
> 
> Once you are connected to relaymark, this client will have:
> - full access to the Populate Api.
> - read access to the dataSource it's attached to.
> - read access to all resources registered within the Api (companies, networks, location, customers, ...).
>

### CounterSample

A counterSample is a single value in a counter's flow of values.

#### Add or update a counterSample

- Path: /data-engine/populate/registrations/{registrationShortId}/counters/{counterName}/samples
- HTTP verb: POST or PUT
- Description: This method add or update a single counterSample.

The URL specify the following parameters:

| Name                | Type    | Description                    |
|:------------------- |:------- |:------------------------------ |
| registrationShortId | Integer | The short id of the registration owning the counter. |
| counterName         | String  | The counter's name. |

The JSON Object can contain the following attributes:

| Name           | Type   | Description                    |
|:-------------- |:------ |:------------------------------ |
| registrationId | String | The id of the registration. |
| counterName    | String | The name of the counter. It must be the name of a registered counter in the specified registration. |
| scalarValue    | Number | The numerical value of the sample. |
| frequency      | String | The frequency identifier of the counterSample. It must be one of the allowed frequencies defined in the counter's [counterTypes](#counter-types). |
| sampleDate     | Date   | The date identifier of the sample. |

> ### Sample date
> 
> Every date must respect the [ISO 8601](https://fr.wikipedia.org/wiki/ISO_8601) standard and must be specified in UTC (e.g. 2016-07-19T10:00:00.0000000Z)
> 
> The sampleDate doesn't represent a specific time, it targets a cycle in the specified frequency.
> 
> For example, when I create a counterSample for the counter numberOfVisits, 
> I don't specify that there is 19 visits at 2 o'clock PM 46 minutes and 25 seconds. 
> I specify that there has been 19 visits in the hour 2 o'clock PM (between 02:00:00PM and 02:59:59PM)
>
> To be sure there is no ambiguity, we have enforced a strict validation for this particular attribute depending on the frequency attribute.
> The table below shows, for each frequency, which date format is expected.
> 
> | Frequency  | Date format                  | Additional rules |
> |:---------- |:---------------------------- |:---------------- |
> | Hourly     | YYYY-MM-DDTHH:00:00.0000000Z |                  |
> | Daily      | YYYY-MM-DDT00:00:00.0000000Z |                  |
> | Weekly     | YYYY-MM-DDT00:00:00.0000000Z | The targeted day must be a monday. |
> | Monthly    | YYYY-MM-01T00:00:00.0000000Z |                  |
> | Quarterly  | YYYY-MM-01T00:00:00.0000000Z | The targeted month must be 1, 4, 7 or 10. |
> | HalfYearly | YYYY-MM-01T00:00:00.0000000Z | The targeted month must be 1, or 7. |
> | Yearly     | YYYY-01-01T00:00:00.0000000Z |                  |
> 

```
insert example request 
```

When the query is successful, the HTTP response is a 200 OK and returns the new/updated counterSample:

```
insert successful response 
```

#### Add or update a batch of counterSamples

- Path: /data-engine/populate/registrations/{registrationShortId}/counters/{counterName}/samples/batch
- HTTP verb: POST or PUT
- Description: This method add or update a list of counterSamples.

The URL specify the following parameters:

| Name                | Type    | Description                    |
|:------------------- |:------- |:------------------------------ |
| registrationShortId | Integer | The short id of the registration owning the counter. |
| counterName         | String  | The counter's name. |

The JSON object is an array of counterSample object (see [Add or update a counterSample](#add-or-update-a-countersample) for more informations aboute the counterSample object).

```
insert example request 
```

When the query is successful, the HTTP response is a 200 OK and returns the array of new/updated counterSamples:

```
insert successful response 
```

#### List counterSamples

- Path: /data-engine/populate/registrations/{registrationShortId}/counters/{counterName}/samples/batch
- HTTP verb: GET
- Description: This method lists the counterSamples of a counter.

The URL specify the following parameters:

| Name                | Type    | Description                    |
|:------------------- |:------- |:------------------------------ |
| registrationShortId | Integer | The short id of the registration owning the counter. |
| counterName         | String  | The counter's name. |

The query string accepts the following parameters:

| Name     | Type                   | Description                    |
|:-------- |:---------------------- |:------------------------------ |
| page     | Integer (default: 1)   | The number of the requested page (see [Paged requests](#paged-requests)). |
| pageSize | Integer (default: 20)  | The size of the requested page (see [Paged requests](#paged-requests)). |
| from     | Date (optional)        | The query will return counterSamples younger than this date. |
| to       | Date (optional)        | The query will return counterSamples older than this date. |

> 
> Every date must respect the [ISO 8601](https://fr.wikipedia.org/wiki/ISO_8601) standard and must be specified in UTC (e.g. 2016-07-19T10:00:00.0000000Z)
> 

```
insert example request 
```

When the query is successful, the HTTP response is a 200 OK and returns a [paged list](#paged-requests) of counterSmaples:

```
{
	"totalResults": 366,
	"totalPages": 19,
	"page": 1,
	"results": [
		//... The objects your queried
	],
} 
```

#### Delete a counterSample

- Path: /data-engine/populate/registrations/{registrationShortId}/counters/{counterName}/samples/{counterSampleShortId}
- HTTP verb: DELETE
- Description: This method deletes a counterSample.

```
insert example request 
```

When the query is successful, the HTTP response is a 200 OK and returns nothing.

### Event

An event can be anything that happens to a registered target at a precise date. 
For example, A customer "A" entered the retail store "B". Or, the retail store "B" opened.

> An event is based on an [eventTypes](#event-types), 
> is attached to a target and is generated by a [registration](#registration).
> 
> The eventType describes a particular group of event.
> 
> The target is a registered object within the Api and is the principal concerned about the event.
> the type of the target is restricted by the [eventTypes](#event-types).

#### Add an event

- Path: /data-engine/populate/events
- HTTP verb: POST
- Description: This method add an event.

The JSON Object can contain the following attributes:

| Name           | Type        | Description                    |
|:-------------- |:----------- |:------------------------------ |
| eventTypeName  | String      | The name of the eventType this event is based on. It must be the name of a registered eventType in the linked DataSource (see [Event types](#event-types)). |
| targetId       | String      | The target id for the event. The target must be a registered object in the Api and it must be an object of type defined by the eventType (see [Event types](#event-types)). |
| registrationId | String      | The id of the registration generating the event. |
| level          | String      | The importance of the message. It can be one of the following values: <ul><li>information</li><li>warning</li><li>problem</li></ul> |
| data           | JSON object | A JSON object that can contain anything. In this object you can define any data that is relevant to the event. |

```
insert example request 
```

When the query is successful, the HTTP response is a 200 OK and returns the event:

```
insert successful response 
```

#### Get an event

- Path: /data-engine/populate/events/{eventShortId}
- HTTP verb: GET
- Description: This method returns a single event.

```
insert example request 
```

When the query is successful, the HTTP response is a 200 OK and returns the event:

```
insert successful response 
```

Errors:

| Error code     | Reason                 |
| -------------- |:---------------------- |
| 404            | The specified resource was not found. |

#### List events

- Path: /data-engine/populate/events
- HTTP verb: GET
- Description: This method lists the events.

The query string accepts the following parameters:

| Name            | Type                       | Description                    |
|:--------------- |:-------------------------- |:------------------------------ |
| page            | Integer (default: 1)       | The number of the requested page (see [Paged requests](#paged-requests)). |
| pageSize        | Integer (default: 20)      | The size of the requested page (see [Paged requests](#paged-requests)). |
| registrationIds | Array of string (optional) |  |
| eventTypeNames  | Array of string (optional) |  |

```
insert example request 
```

When the query is successful, the HTTP response is a 200 OK and returns a [paged list](#paged-requests) of events:

```
{
	"totalResults": 366,
	"totalPages": 19,
	"page": 1,
	"results": [
		//... The objects your queried
	],
} 
```

### DataChunk

A dataChunk is a set of data completing a targeted object.
For example you can add a dataChunk for the customer "A" specifiyng that its gender is "female", 
its favorite color is "blue" and last bought car model is "TESLA Model S".

A DataChunk's main purpose is extend the search engine of the Api. 
For more information about how to query your data, see the [consume Api](#search).

#### Add a dataChunk

- Path: /data-engine/populate/data-chunks
- HTTP verb: POST
- Description: This method adds a data-chunk.

The JSON Object can contain the following attributes:

| Name              | Type        | Description                    |
|:----------------- |:----------- |:------------------------------ |
| dataChunkTypeName | String      | The name of the dataChunkType this dataChunk is based on (see [DataChunk types](#datachunk-types)). |
| dataSourceSid     | String      | The sid of the dataSource containig the dataChunkType. |
| targetId          | String      | The target id for the dataChunk. The target must be a registered object in the Api and it must be an object of type defined by the eventType (see [DataChunk types](#datachunk-types)). |
| ownerId           | String      | The id of the company or network that owns the dataChunk. |
| data              | JSON object | A JSON object that can contains the dataChunk's data. It must contain all the properties described by the [DataChunk types](#datachunk-types). |

```
insert example request 
```

When the query is successful, the HTTP response is a 200 OK and returns the new dataChunk:

```
insert successful response 
```

#### Update a dataChunk

- Path: /data-engine/populate/data-chunks/{dataChunkShortId}
- HTTP verb: PUT
- Description: This method updates a data-chunk.

The JSON Object can contain the following attributes:

| Name              | Type        | Description                    |
|:----------------- |:----------- |:------------------------------ |
| data              | JSON object | A JSON object that can contains the dataChunk's data. It must contain all the properties described by the [DataChunk types](#datachunk-types). |

```
insert example request 
```

When the query is successful, the HTTP response is a 200 OK and returns the updated of dataChunk:

```
insert successful response 
```

#### List dataChunks

- Path: /data-engine/populate/data-chunks
- HTTP verb: GET
- Description: This method returns list of dataChunks.

The query string accepts the following parameters:

| Name            | Type                       | Description                    |
|:--------------- |:-------------------------- |:------------------------------ |
| page            | Integer (default: 1)       | The number of the requested page (see [Paged requests](#paged-requests)). |
| pageSize        | Integer (default: 20)      | The size of the requested page (see [Paged requests](#paged-requests)). |

```
insert example request 
```

When the query is successful, the HTTP response is a 200 OK and returns a [paged list](#paged-requests) of dataChunks:

```
{
	"totalResults": 366,
	"totalPages": 19,
	"page": 1,
	"results": [
		//... The objects your queried
	],
} 
```

#### Get a dataChunk by Id

- Path: /data-engine/populate/data-chunks/{dataChunkShortId}
- HTTP verb: GET
- Description: This method returns a single dataChunk.

```
insert example request 
```

When the query is successful, the HTTP response is a 200 OK and returns the dataChunk:

```
insert successful response 
```

Errors:

| Error code     | Reason                 |
| -------------- |:---------------------- |
| 404            | The specified resource was not found. |

#### Delete a dataChunk by Id

- Path: /data-engine/populate/data-chunks/{dataChunkShortId}
- HTTP verb: DELETE
- Description: This method returns a single dataChunk.

```
insert example request 
```

When the query is successful, the HTTP response is a 200 OK and returns nothing.

```
insert successful response 
```

Errors:

| Error code     | Reason                 |
| -------------- |:---------------------- |
| 404            | The specified resource was not found. |

## Consume API

This Api is dedicated to the consumption of the data you have access to. 

### Counters

A counter is a flow of numerical values over time. For example, the number of visitors of a particular retail store over time.

As a user, you have access to all the counters linked directly or indirectly to one of your company or network.

The counter objects returned by the different calls of the consume API 
is a compilation of the [counter](#registration) object defined in the populate API, and the [counterType](#counter-types) object defined in the declaration API.

The resulting JSON object contains the following attributes:

| Name                   | Type                   | Description                    |
|:---------------------- |:---------------------- |:------------------------------ |
| dataSourceSid          | String                 | The sid of the [dataSource](#datasource). |
| counterTypeName        | String                 | The name of the [counterType](#counter-types). |
| counterTypeDisplayName | LocalizedString object | The counterType's display name (defined by the [counterType](#counter-types)). |
| counterTypeDescription | LocalizedString object | The counterType's description (defined by the [counterType](#counter-types)). |
| categoryName           | String                 | The counter's category (defined by the [counterType](#counter-types)). |
| valueType              | String                 | The counter's value type (defined by the [counterType](#counter-types)). |
| frequencies            | Array of strings       | The list of available frequencies. |
| registrationId         | String (optional)      | The counter's registration id. |
| counterName            | String                 | The counter's internal name (defined in the [registration](#registration)). |
| counterDisplayName     | LocalizedString object | The counter's display name (defined in the [registration](#registration)). |
| ownerType              | String                 | The counter's owner type  (defined by the [registration](#registration)). |
| ownerId                | String                 | The counter's owner id  (defined by the [registration](#registration)). |

> 
> The frequency attribute gives you all the frequencies that you can query. 
> That includes the fequencies declared in the [counterType](#counter-types) and, 
> if the dynamicallyGenerateOtherFrequencies attribute in the [counterType](#counter-types) is true, 
> all the frequencies that are generated.
> 

#### List counters

- Path: /data-engine/consume/counters/
- HTTP verb: GET
- Description: This method returns a list of counters.

The query string accepts the following parameters:

| Name            | Type                  | Description                    |
|:--------------- |:--------------------- |:------------------------------ |
| page            | Integer (default: 1)  | The number of the requested page (see [Paged requests](#paged-requests)). |
| pageSize        | Integer (default: 20) | The size of the requested page (see [Paged requests](#paged-requests)). |
| dataSourceSid   | String (optional)     | The [dataSource](#datasource)'s sid. |
| counterTypeName | String (optional)     | The [counterType](#counter-types)'s name. |
| categoryname    | String (optional)     | The counter's category. |
| frequency       | String (optional)     | The counter's frequency. |
| ownerType       | String (optional)     | The counter's owner type. |
| ownerId         | String (optional)     | The counter's owner id. |

```
insert example request 
```

When the query is successful, the HTTP response is a 200 OK and returns a [paged list](#paged-requests) of counters:

```
{
	"totalResults": 366,
	"totalPages": 19,
	"page": 1,
	"results": [
		//... The objects your queried
	],
} 
```

#### Query counter data

- Path: /data-engine/consume/counters/{registrationShortId}/{counterName}/data
- HTTP verb: GET
- Description: This method returns the data of a single counter.

The URL specify the following parameters:

| Name                | Type    | Description                    |
|:------------------- |:------- |:------------------------------ |
| registrationShortId | Integer | The short id of the registration owning the counter. |
| counterName         | String  | The counter's name. |

The query string accepts the following parameters:

| Name      | Type            | Description                    |
|:--------- |:--------------- |:------------------------------ |
| interval  | String          | The time interval you want to query. It can be one of the following values: <ul><li>today</li><li>yesterday</li><li>last7Days</li><li>last30Days</li><li>last4Weeks</li><li>custom</li></ul> |
| frequency | String          | The counter's frequency, it must be an available frequency (see [Counters](#counters)). It can be one of the following values: <ul><li>hourly</li><li>daily</li><li>weekly</li><li>monthly</li><li>quarterly</li><li>halfYearly</li><li>yearly</li></ul> |
| from      | String          | The begin date of the interval (used only when the specified interval value is custom, this parameter is then required). |
| to        | String          | The end date of the interval (used only when the specified interval value is custom, if this parameter is not specified the API will use the current time).|

> 
> Every date must respect the [ISO 8601](https://fr.wikipedia.org/wiki/ISO_8601) standard and must be specified in UTC (e.g. 2016-07-19T10:00:00.0000000Z)
> 

> #### Interval and frequency
> 
> Depending on the interval you choose there can be more restrictions on the frequency attribute, other than its availability. 
> In fact you cannot select a higher frequency than your interval describes.
>
> For example, you cannot query an interval "last7Days" and define its frequency at "monthly", in this particular case frequency can only be "hourly" or "daily".
> 

```
insert example request 
```

When the query is successful, the HTTP response is a 200 OK and returns the counter's data in the form of an array of sample JSON objects.

A sample JSON object has the following attributes:

| Name        | Type    | Description                    |
|:----------- |:------- |:------------------------------ |
| date        | Date    | The cycle denominator of this sample (For more information, see [Add or update a counterSample](#add-or-update-a-countersample)). |
| scalarValue | Number  | The sample's value. |
| hasValue    | Boolean | |

>
> The query will always return every cycle of the asked interval, even when there is no actual data.
> The hasValue attribute has been added to differentiate the case when the returns the value 0 and the case when there is no data.
>

```
insert successful response 
```

### Events

An event can be anything that happens to a registered target at a precise date. 
For example, A customer "A" entered the retail store "B". Or, the retail store "B" opened.

As a user, you have access to every event generated by directly or indirectly by your companies, your networks 
and the companies affiliated to your network (if those companies decide to share them).

#### List accessible eventTypes

- Path: /data-engine/consume/event-types/
- HTTP verb: GET
- Description: This method returns a list of eventTypes.

The query string accepts the following parameters:

| Name            | Type                  | Description                    |
|:--------------- |:--------------------- |:------------------------------ |
| page            | Integer (default: 1)  | The number of the requested page (see [Paged requests](#paged-requests)). |
| pageSize        | Integer (default: 20) | The size of the requested page (see [Paged requests](#paged-requests)). |
| categoryname    | String (optional)     | The eventType's category. |
| targetType      | String (optional)     | The eventType's target type. It can be one of the following values: <ul><li>company</li><li>network</li><li>location</li><li>customer</li></ul> |

```
insert example request 
```

When the query is successful, the HTTP response is a 200 OK and returns a [paged list](#paged-requests) of eventType:

An eventType JSON object has the following attributes:

| Name            | Type                   | Description                    |
|:--------------- |:---------------------- |:------------------------------ |
| dataSourceSid   | String                 | The eventType's [dataSource](#datasource) sid. |
| name            | String                 | The eventType's name (see [dataSource](#datasource)). |
| displayName     | LocalizedString object | The display name of the eventType (see [LocalizedString object](#localizedstring-object)). |
| description     | LocalizedString object | The description of the eventType (see [LocalizedString object](#localizedstring-object)). |
| categoryName    | String                 | The category of the eventType. It is used to filter the events in the list requests. |
| targetType      | String                 | The targetType for this eventType. It can be one of the following values: <ul><li>company</li><li>network</li><li>location</li><li>customer</li></ul>|
| displayTemplate | LocalizedString object | The template of the eventType. We use the [SmartFormat](https://github.com/scottrippey/SmartFormat) project to compile the template using the event object as data. |

```
{
	"totalResults": 366,
	"totalPages": 19,
	"page": 1,
	"results": [
		//... The objects your queried
	],
} 
```

#### Query events

- Path: /data-engine/consume/events
- HTTP verb: GET
- Description: This method enables you to query the events.

The query string accepts the following parameters:

| Name          | Type                  | Description                    |
|:------------- |:--------------------- |:------------------------------ |
| page          | Integer (default: 1)  | The number of the requested page (see [Paged requests](#paged-requests)). |
| pageSize      | Integer (default: 20) | The size of the requested page (see [Paged requests](#paged-requests)). |
| dataSourceSid | String (optional)     | The eventType's [dataSource](#datasource) sid (if this parameter is specified, eventTypeName must be specified). |
| eventTypeName | String (optional)     | The eventType's name (if this parameter is specified, dataSourceSid must be specified). |
| categoryname  | String (optional)     | The event's category. |
| targetType    | String (optional)     | The event's target type. It can be one of the following values: <ul><li>company</li><li>network</li><li>location</li><li>customer</li></ul> |
| targetId      | String (optional)     | The event's target id. |
| from          | String (optional)     | The begin date of the interval. |
| to            | String (optional)     | The end date of the interval (if this parameter is not specified the API will use the current time).|

```
insert example request 
```

When the query is successful, the HTTP response is a 200 OK and returns a [paged list](#paged-requests) of events:

```
{
	"totalResults": 366,
	"totalPages": 19,
	"page": 1,
	"results": [
		{
			"targetType": "customer",
			"targetId": "customers/13216",
			"target": {
				"firstName": "phillip",
				"lastName": "osorio",
				"email": "phillip.osorio@gmail.com",
				"phone": null,
				"gender": "male",
				"profilePicture": "equipmag/customerpictures/frankchartrand",
				"shortId": 13216
			},
			"dataSourceSid": "relaybox",
			"eventTypeName": "customerVisit",
			"eventType": {
				"displayName": {
					"en": "Known customer shopping",
					"fr": "Un client est présent"
				},
				"description": {
					"en": "This event is triggered whenever a known customer entered your venue.",
					"fr": "Cet évènement se déclenche lorsqu'un client vient d'entrer dans votre point de vente."
				},
				"categoryName": "Monitoring",
				"displayTemplate": "{{target.firstname}} has entered the {{owner.locationName}} venue",
				"targetType": "customer"
			},
			"dataSourceRegistrationId": "DataSourceRegistrations/33",
			"ownerType": "company",
			"ownerId": "CompanyLocations/194",
			"owner": {
				"companyId": "companies/513",
				"locationName": "La floriste du coin",
				"isPublic": false,
				"address": {
					"number": null,
					"street": "159 rue des peupliers",
					"complement": null,
					"zipCode": "94800",
					"city": "Villejuif",
					"country": null,
					"formatted": null,
					"coordinates": {
						"Latitude": 48.9155548,
						"Longitude": 2.226638399999956
					},
					"autoGeneratedCoordinates": null
				},
				"fixedLine": null,
				"mobileLine": null,
				"faxLine": null,
				"email": "benjaminspeth@hotmail.fr",
				"shortId": 194
			},
			"level": "information",
			"data": {
				"visitDuration": 325
			},
			"modifiedAt": "2016-07-04T12:52:36.7218261Z",
			"modifiedBy": null,
			"createdAt": "2016-07-04T12:52:36.7218261Z",
			"createdBy": "users/1249",
			"shortId": 577
		}
	],
} 
```

### Search

The Data Engine gives you the possibility to extend the data of some objects within 
the Relaymark API by attaching a [dataChunk](#datachunk) to it.

This section describes the API calls that enables you to query those extended objects.

#### Query

- Path: /data-engine/consume/{targetType}/search
- HTTP verb: GET
- Description: This method returns a list of augmented target objects.

The URL specify the following parameters:

| Name       | Type   | Description                    |
|:---------- |:------ |:------------------------------ |
| targetType | String | The object's type you want to query. It can be one of the following values: <ul><li>company</li><li>network</li><li>location</li><li>customer</li></ul> |

The query string accepts the following parameters:

| Name      | Type                  | Description                    |
|:--------- |:--------------------- |:------------------------------ |
| page      | Integer (default: 1)  | The number of the requested page (see [Paged requests](#paged-requests)). |
| pageSize  | Integer (default: 20) | The size of the requested page (see [Paged requests](#paged-requests)). |
| query     | String                | A ravenDb/lucene query to filter the results. |

> See the [RavenDB](https://ravendb.net/docs/article-page/3.0/csharp/indexes/querying/full-query-syntax) documentation 
> and the [Lucene](http://lucene.apache.org/core/2_9_4/queryparsersyntax.html) documentation fo more information about the query syntax.

> You can get the description of all the returned object properties (and what you can query) by using the [Definition](#definition) call.

```
insert example request 
```

When the query is successful, the HTTP response is a 200 OK and returns a [paged list](#paged-requests) of augmented target objects:

```
{
	"totalResults": 366,
	"totalPages": 19,
	"page": 1,
	"results": [
		//... The objects your queried
	],
} 
```

#### Definition

- Path: /data-engine/consume/{targetType}/search/definition
- HTTP verb: GET
- Description: This method returns a list of augmented target objects.

The URL specify the following parameters:

| Name       | Type   | Description                    |
|:---------- |:------ |:------------------------------ |
| targetType | String | The object's type you want to get the defenition from. It can be one of the following values: <ul><li>company</li><li>network</li><li>location</li><li>customer</li></ul> |

```
insert example request 
```

When the query is successful, the HTTP response is a 200 OK and returns a JSON object describing all the properties of the targeted object type:

```
{
	"properties": [
		{
			"name": "Gender",
			"propertyType": "string"
		},
		{
			"name": "totalNumberOfVisits",
			"propertyType": "number"
		},
		{
			"name": "lastVisitDate",
			"propertyType": "date"
		},
	]
} 
```

## Annex

### Ids and short ids

Most objects returned by our Api has an id. 
Those ids are composed by the type of the object followed by a slash and a unique number.

At some point when you use the Api, you will be asked for the shortId of an object (mostly when there are ids in the url itself).
a short id is just the numerical part of the base id.

For example :
```
Id: 		companies/314
ShortId:	314
```

To save you the trouble of constantly convert an id into a short id every object in the Api 
that has an id also has a short id property defined.

### LocalizedString object

Some attributes in our resources will be displayed in our applications 
(those are mostly name or description attributes that describes what your resource is/can do).
Those attributes needs to be localized in order to be comprehend by the most people.

A localizedString object is a hash table which contains strings in different language as values, 
and the language code as keys.

> The keys of the localizedString object must follow the [ISO 639-1](https://en.wikipedia.org/wiki/ISO_639) standard 
and be a 2 letter language code.

```
{
	"en": "My event type",
	"fr" : "Mon type d'événement"
}
```

### Paged requests

Most of the list calls in our API implement a paging system. 
You cannot list all objects of your query in one call. 
Instead You have to get pages of your query.

Each query implement the following query string attributes:

| Name          | Type                  | Description                    |
|:------------- |:--------------------- |:------------------------------ |
| page          | Integer (default: 1)  | The number of the requested page. |
| pageSize      | Integer (default: 20) | The size of the requested page. |

Also, those calls will always return a page list object:

```
{
	"totalResults": 366,
	"totalPages": 19,
	"page": 1,
	"results": [
		//... The objects your queried
	],
}
```
