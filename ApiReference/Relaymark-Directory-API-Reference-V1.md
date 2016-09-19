# Relaymark Directory REST API
<!-- TOC depthFrom:1 depthTo:6 insertAnchor:false orderedList:false updateOnSave:true withLinks:true -->

- [Relaymark Directory REST API](#relaymark-directory-rest-api)
    - [Connect to the Relaymark API](#connect-to-the-relaymark-api)
        - [Register your application](#register-your-application)
    - [Users](#users)
        - [Get connected user informations](#get-connected-user-informations)
            - [His companies](#his-companies)
            - [His networks](#his-networks)
            - [His location assignments](#his-location-assignments)
            - [His applications](#his-applications)
            - [His application setups](#his-application-setups)
    - [Companies](#companies)
        - [Create a company](#create-a-company)
        - [Update a company](#update-a-company)
        - [List companies](#list-companies)
        - [Get a company by id](#get-a-company-by-id)
        - [Delete a company](#delete-a-company)
    - [Networks](#networks)
        - [Create a network](#create-a-network)
        - [Update a network](#update-a-network)
        - [List networks](#list-networks)
        - [Get a network by id](#get-a-network-by-id)
        - [Delete a network](#delete-a-network)
    - [Applications](#applications)
        - [Create an application](#create-an-application)
        - [List applications](#list-applications)
        - [Get an application by name](#get-an-application-by-name)
        - [Delete an application](#delete-an-application)
    - [Annex](#annex)
        - [Ids and short ids](#ids-and-short-ids)
        - [LocalizedString object](#localizedstring-object)
        - [Paged requests](#paged-requests)

<!-- /TOC -->

There are 4 main resources in the directory API:
- Users
- Companies
- Networks
- Applications

## Connect to the Relaymark API

Our API is only accessible using the [OAuth2](https://tools.ietf.org/html/rfc6749) protocol 
using the [authorization code flow](https://tools.ietf.org/html/rfc6749#page-24).

To make the connection you will need to provide the following informations:
| Name              | Type              | Description                    |
|:----------------- |:----------------- |:------------------------------ |
| baseUrl           | Uri               | The base Url of your application. |
| clientId          | String            | The id of the client registered within our API. |
| clientSecret      | String            | The secret of the client registered within our API. |
| scope             | Array of Strings  | The list of scopes your client wants to access (a scope is an identifier to a particular type of resource within our API). |
| redirectUri       | String            | This Uri will be called once the authorization process is finished. |
| logoutRedirectUri | String (optional) | This Uri will be called once the user is succesfully disconnected. |

The scopes available for your application are:
- offline_access (required)
- openid
- email
- profile
- rm
- rm_directory

Here is an example of configuration using the [relaymark.oauth2](https://github.com/Relaymark/Relaymark.oauth2) github project :

```
angular
  .module('YourAwesomeApp', ['relaymark.oauth2'])
  .config(function(svOAuth2Provider, AppConstants, $httpProvider) {
      svOAuth2Provider.configure({
          baseUrl: "www.beta.relaymark.com",
          clientId: "MyApp_AuthorizationCode@FRAK123456789",
          clientSecret: "bf33511e-9d62-46a8-965c-2c1ac732e621",
          scope: ["offline_access", "openid", "email", "profile", "rm", "rm_directory"],
          redirectUri: "www.beta.relaymark.com"
      });
  );
```

### Register your application

To register your application within our API, you will have to our main site [http://beta.relaymark.com/](http://beta.relaymark.com/),
register yourself as a user, create a company and finally go the application interface.

All the informations needed to make the connection to our API will be given to you 
in the [application details page](http://beta.relaymark.com/apps). 

## Users

### Get connected user informations

- Path: /directory/users/me
- HTTP verb: GET
- Description: This method returns the connected user profile informations.

When the query is successful, the HTTP response is a 200 OK and connected user profile informations:

```
{
  "avatarId": "users/1121/AvatarId/460149041691361280",
  "firstName": "gerard",
  "lastName": "dupont",
  "fullName": "gerard dupont",
  "email": "gerard.dupont@relaymark.com",
  "password": "notasecret",
  "isActive": true,
  "id": "users/1121",
  "shortId": 1121,
  "modifiedAt": "2016-04-22T09:20:41.1843813Z",
  "createdAt": "2016-03-23T15:07:32.7246879Z"
}
```

#### His companies

- Path: /directory/users/me/companies
- HTTP verb: GET
- Description: This method returns the list of companies the connected user has access to.

When the query is successful, the HTTP response is a 200 OK and returns the list of companies the connected user has access to:

```
{
  "totalResults": 1,
  "totalPages": 1,
  "page": 1,
  "results": [
    {
      "company": {
        "employeesCount": 1,
        "managersCount": 3,
        "companyName": "L'Empire inter-galactique",
        "headquarterLocation": {
          "address": {
            "street": "12 rue des peupliers",
            "complement": "",
            "zipCode": "94800",
            "city": "Villejuif",
            "formatted": "12 rue des peupliers, 94800 Villejuif, France",
            "autoGeneratedCoordinates": {
              "latitude": 49.9155548,
              "longitude": 1.2266384
            }
          },
          "fixedLine": {
            "countryCode": 0,
            "phoneType": 0
          }
        },
        "legalForm": {
          "id": "legalforms/SAS",
          "shortId": "SAS"
        },
        "country": {
          "id": "countries/FRANCE",
          "shortId": "FRANCE"
        },
        "id": "companies/513",
        "shortId": 513,
        "modifiedAt": "2016-03-23T15:07:32.4966123Z",
        "createdAt": "2016-03-23T15:07:32.4966123Z"
      },
      "employeeType": 2
    }
  ]
}
```
> 
> employee type can have 2 values:
> - 1: employee
> - 2: manager
> 

#### His networks

- Path: /directory/users/me/networks
- HTTP verb: GET
- Description: This method returns the list of networks the connected user has access to.

When the query is successful, the HTTP response is a 200 OK and returns the list of networks the connected user has access to:

```
{
  "totalResults": 2,
  "totalPages": 1,
  "page": 1,
  "results": [
    {
      "highestRole": "networkAdmin",
      "affiliations": [
        {
          "affiliationType": "administrator",
          "company": {
            "euVatNumber": "FRAK123456789",
            "companyName": "L'Empire inter-galactique",
            "headquarterLocation": {
              "address": {
                "street": "159 bvd charles de gaulle",
                "complement": "",
                "zipCode": "92700",
                "city": "colombes",
                "formatted": "Résidence Autrement, 159 Boulevard Charles de Gaulle, 92700 Colombes, France",
                "autoGeneratedCoordinates": {
                  "latitude": 48.9155548,
                  "longitude": 2.2266384
                }
              },
              "fixedLine": {
                "countryCode": 0,
                "phoneType": 0
              }
            },
            "legalForm": {
              "shortId": "SAS",
              "id": "legalforms/SAS"
            },
            "country": {
              "shortId": "FRANCE",
              "id": "countries/FRANCE"
            },
            "shortId": 513,
            "id": "companies/513"
          },
          "companyId": "companies/513",
          "function": "Network Administrator",
          "isDefault": true,
          "organizationUnit": {
            "name": "Voie Marchande Perlemienne",
            "shortId": "457683516344238080",
            "id": "OrganizationUnits/457683516344238080"
          },
          "role": "networkAdmin"
        }
      ],
      "defaultAffiliation": "companies/513",
      "network": {
        "logos": [
          "networks/705/Logos/457683519892619264"
        ],
        "networkName": "Voie Marchande Perlemienne",
        "shortId": 705,
        "id": "networks/705"
      },
      "networkId": "networks/705",
      "userId": "users/1121"
    },
    {
      "highestRole": "networkAdmin",
      "affiliations": [
        {
          "affiliationType": "administrator",
          "company": {
            "euVatNumber": "FRAK123456789",
            "companyName": "L'Empire inter-galactique",
            "headquarterLocation": {
              "address": {
                "street": "159 bvd charles de gaulle",
                "complement": "",
                "zipCode": "92700",
                "city": "colombes",
                "formatted": "Résidence Autrement, 159 Boulevard Charles de Gaulle, 92700 Colombes, France",
                "autoGeneratedCoordinates": {
                  "latitude": 48.9155548,
                  "longitude": 2.2266384
                }
              },
              "fixedLine": {
                "countryCode": 0,
                "phoneType": 0
              }
            },
            "legalForm": {
              "shortId": "SAS",
              "id": "legalforms/SAS"
            },
            "country": {
              "shortId": "FRANCE",
              "id": "countries/FRANCE"
            },
            "shortId": 513,
            "id": "companies/513"
          },
          "companyId": "companies/513",
          "function": "Network Administrator",
          "isDefault": true,
          "organizationUnit": {
            "name": "Voie Marchande Corellienne",
            "shortId": "449373818650099712",
            "id": "OrganizationUnits/449373818650099712"
          },
          "role": "networkAdmin"
        }
      ],
      "defaultAffiliation": "companies/513",
      "network": {
        "networkName": "Voie Marchande Corellienne",
        "shortId": 673,
        "id": "networks/673"
      },
      "networkId": "networks/673",
      "userId": "users/1121"
    }
  ]
}
```
> 
> affiliation type and highest role design the role that the connected user has in the network. It can take 4 values:
> - networkAdmin
> - organizationUnitManager
> - supervisor
> - assignee
> 

#### His location assignments

- Path: /directory/users/me/location-assignments
- HTTP verb: GET
- Description: This method returns the list of networks the connected user has access to.

When the query is successful, the HTTP response is a 200 OK and returns the list of networks the connected user has access to:

```
{
  "totalResults": 1,
  "totalPages": 1,
  "page": 1,
  "results": [
    {
      "userId": "users/1121",
      "locationAssignmentId": "LocationAssignments/129",
      "assignmentType": "companyLocationManager",
      "position": "Membre",
      "affiliationId": "affiliations/65",
      "organizationUnitId": "OrganizationUnits/449373818650099712",
      "companyLocationId": "CompanyLocations/194",
      "customPermissionIds": [
        "ApplicationPermissions/stimuland/peut_commander"
      ],
      "networkId": "networks/673",
      "companyId": "companies/513",
      "affiliation": {
        "networkId": "networks/673",
        "companyId": "companies/513",
        "status": 0,
        "type": 2,
        "id": "affiliations/65",
        "shortId": 65
      },
      "companyLocation": {
        "locationName": "Corellia",
        "address": {
          "street": "12 rue des peupliers",
          "zipCode": "92700",
          "city": "colombes",
          "coordinates": {
            "latitude": 48.9155548,
            "longitude": 2.2266383999999562
          }
        },
        "isPublic": false,
        "id": "CompanyLocations/194",
        "shortId": 194
      },
      "organizationUnit": {
        "id": "OrganizationUnits/449373818650099712",
        "shortId": "449373818650099712",
        "name": "Voie Marchande Corellienne"
      },
      "customPermissions": [
        {
          "id": "ApplicationPermissions/stimuland/peut_commander",
          "shortId": "peut_commander",
          "application": {
            "description": {
              "en": "Le Stimuland"
            },
            "displayName": {
              "en": "Stimuland"
            },
            "name": "stimuland",
            "applicationType": "custom",
            "contextType": "network",
            "id": "applications/stimuland",
            "shortId": "stimuland"
          },
          "name": "peut_commander",
          "displayName": {
            "en": "Peut commander"
          },
          "description": {
            "en": "Peut commander"
          }
        }
      ]
    }
  ]
}
```

#### His applications

- Path: /directory/users/me/applications
- HTTP verb: GET
- Description: This method returns the list of applications the connected user has access to.

When the query is successful, the HTTP response is a 200 OK and returns 
the list of applications the connected user has access to:

```
{
  "totalResults": 1,
  "totalPages": 1,
  "page": 1,
  "results": [
    {
      "name": "myapp",
      "displayName": {
        "en": "mon app 1"
      },
      "description": {
        "en": "mon app 1 desc"
      },
      "applicationType": "custom",
      "contextType": "company",
      "ownerCompanyId": "companies/513",
      "applicationFrontUri": "http://www.google.fr/",
      "canManageApplication": true
    }
  ]
}
```

#### His application setups

- Path: /directory/users/me/application-setups
- HTTP verb: GET
- Description: This method returns the list of application setups the connected user has access to.

When the query is successful, the HTTP response is a 200 OK and returns 
the list of application setups the connected user has access to:

```
{
  "totalResults": 1,
  "totalPages": 1,
  "page": 1,
  "results": [
    {
      "applicationId": "applications/myapp",
      "application": {
          "name": "myapp",
          "displayName": {
              "en": "mon app 1"
          },
          "description": {
              "en": "mon app 1 desc"
          },
          "applicationType": "custom",
          "contextType": "network"
      },
      "contextType": "network",
      "contextId": "networks/954",
      "context": {
        "networkName": "Voie Marchande Corellienne",
        "shortId": 954,
        "id": "networks/954",
        "logos": [
            "networks/954/Logos/457683519892619264"
        ],
        "description": "",
        "networkFunctions": [
            "member"
        ]
      },
      expireAt: null
    }
  ]
}
```

## Companies

### Create a company

### Update a company

### List companies

### Get a company by id

### Delete a company

## Networks

### Create a network

### Update a network

### List networks

### Get a network by id

### Delete a network

## Applications

### Create an application

### List applications

### Get an application by name

### Delete an application

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
