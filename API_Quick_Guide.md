# Quick start

## API Summary

The API currently exposes the following endpoints:

| Object                                        | Method | Path                                                     |
| --------------------------------------------- | ------ | -------------------------------------------------------- | 
| Create an entity                              | POST   | /ngsi-ld/v1/entities                                     |
| Search entities                               | GET    | /ngsi-ld/v1/entities                                     |
| Get an entity by id                           | GET    | /ngsi-ld/v1/entities/{entityId}                          |
| Append an attribute to an entity              | POST   | /ngsi-ld/v1/entities/{entityId}/attrs                    |
| Update attributes of an entity                | PATCH  | /ngsi-ld/v1/entities/{entityId}/attrs                    |
| Partial update of an entity attribute         | PATCH  | /ngsi-ld/v1/entities/{entityId}/attrs/{attrId}           |
| Delete an entity                              | DELETE | /ngsi-ld/v1/entities/{entityId}                          |
| Create a batch of entities                    | POST   | /ngsi-ld/v1/entityOperations/create                      |
| Get the temporal evolution of an entity       | GET    | /ngsi-ld/v1/temporal/entities/{entityId}                 |
| Create a subscription                         | POST   | /ngsi-ld/v1/subscriptions                                |
| Query subscriptions                           | GET    | /ngsi-ld/v1/subscriptions                                |
| Get a subscription by id                      | GET    | /ngsi-ld/v1/subscriptions/{subscriptionId}               |
| Update a subscription                         | PATCH  | /ngsi-ld/v1/subscriptions/{subscriptionId}               |
| Delete a subscription                         | DELETE | /ngsi-ld/v1/subscriptions/{subscriptionId}               |

## NGSI-LD Entity structure

An NGSI-LD entity is serialized in JSON-LD format. The structure has to comply with some requirements amongst them:

- an entity must have an id (represented by `uri:ngsi-ld:<EntityType>:<UUID>`) 
- an entity must have a type
- an attribute denotes a property or a relationship
- an entity may have properties
- an entity may have relationships with other entities
- a property may have properties and relationships with other entities
- a relationship may have properties and relationships with other entities

For instance, here is an example of a Vehicle entity :

```json
{
  "id": "urn:ngsi-ld:Vehicle:A1234",
  "type": "Vehicle",
  "brandName": {
    "type": "Property",
    "value": "Tesla"
  },
  "maxSpeed": {
    "type": "Property",
    "value": 130
  },
  "name": "a sample name",
  "isParked": {
    "type": "Relationship",
    "object": "urn:ngsi-ld:OffStreetParking:Downtown2",
    "observedAt": "2019-10-22T12:00:04Z",
    "providedBy": {
      "type": "Relationship",
      "object": "urn:ngsi-ld:Org:Bob"
    }
  },
  "hasSensor": {
    "type": "Relationship",
    "object": "urn:ngsi-ld:Sensor:1234567890"
  },
  "@context": [
      "https://schema.lab.fiware.org/ld/context",
      "http://uri.etsi.org/ngsi-ld/v1/ngsi-ld-core-context.jsonld"
  ]
}
```

## NGSI-LD Subscription structure

An NGSI-LD Subscription is serialized in JSON-LD format. The structure has to comply with some requirements amongst them:

- a subscription must have an id (represented by `uri:ngsi-ld:Subscription:<UUID>`) 
- a subscription must have a type that shall be equal to "Subscription"
- a subscription must have a notification containing the parameters that allow to convey the details of a notification (details in the following example)
- a subscription may have other attributes: name, description, entities, q (query), geoQ (geo query) ...

For instance, here is an example of a Subscription to the previous Vehicule entity that sends a notification when the maxSpeed exceeds 180:

Note: The `endpoint.info` field contains optional information that may be needed when contacting the notification endpoint, the key/value pairs are added in the header of the HTTP POST request. For instance this could be Authorization headers in case of HTTP binding of the API. 
```json
{
  "id":"urn:ngsi-ld:Subscription:S1234",
  "type":"Subscription",
  "entities": [
    { "id": "urn:ngsi-ld:Vehicle:A1234",
      "type": "Vehicle"
    }
  ],
  "q": "maxSpeed>180",
  "notification": {
    "attributes": ["maxSpeed"],
    "format": "normalized",
    "endpoint": {
      "uri": "http://my-domain-name",
      "accept": "application/json",
      "info": [
          {
            "key": "Authorization-token",
            "value": "Authorization-token-value"
          }
      ]
    },
  },
  "@context": [
        "https://schema.lab.fiware.org/ld/context",
        "http://uri.etsi.org/ngsi-ld/v1/ngsi-ld-core-context.jsonld"
  ]
}
```

## Authentication

The API is protected by an OpenID Connect compliant authentication server (precisely a [Keycloak](https://www.keycloak.org) server).

Thus, any call made to the API must include an `Authorization` header containing a Bearer access token. It takes the following form:

```
Authorization: Bearer <access token>
```

An access token can be obtained in two ways:

- If a client has its service account enabled, an access token can be obtained with the following request:

```
http --form POST https://data-hub.eglobalmark.com/auth/realms/datahub/protocol/openid-connect/token client_id=<client_id> client_secret=<client_secret> grant_type=client_credentials
```

- If a client wants to make API calls on behalf of an end user, an access token can be obtained in exchange of the authorization code contained in the redirect URL after an user authenticates on the authentication server. This process, called the Authorization Code Flow, is described exhaustively in the OpenID Connect specification: https://openid.net/specs/openid-connect-core-1_0.html#CodeFlowAuth.

One simple way to have the access token without copy-pasting is to keep it in a variable:

```
export TOKEN=$(http --form POST https://data-hub.eglobalmark.com/auth/realms/datahub/protocol/openid-connect/token client_id=<client_id> client_secret=<client_secret> grant_type=client_credentials | jq -r .access_token)
```

Then to simply use it the HTTP requests:

```
http https://data-hub.eglobalmark.com/... Authorization:"Bearer $TOKEN" ...
```

For brevity and clarity, the `Authorization` header is not displayed in the sample HTTP requests described below.

## Notes on `@context` resolution

Multiple namespaces are allowed (`ngsild` is the mandatory core context, fiware is a frequently used namespace, others are defined in this project's sub-directories).

Most of the HTTP requests need to specify the contexts they are referring to, in order to prevent namespaces collisions (e.g. a Vehicle entity definition that would exist in two different contexts).

## API queries examples

The provided examples make use of the [HTTPie](https://httpie.org/) command line tool

* Create an entity (with the above Vehicle example)

```
http POST https://data-hub.eglobalmark.com/ngsi-ld/v1/entities < vehicle.jsonld
```

* Get an entity by URI

```
http https://data-hub.eglobalmark.com/ngsi-ld/v1/entities/urn:ngsi-ld:Vehicle:A1234 Content-Type:application/json
```

* Search Vehicle entities

```
http https://data-hub.eglobalmark.com/ngsi-ld/v1/entities type==Vehicle Link:"<https://schema.lab.fiware.org/ld/context>; rel=http://www.w3.org/ns/json-ld#context; type=application/ld+json" Content-Type:application/json
```

* Search Vehicle entities parked in the Downtown1 offstreet parking

```
http https://data-hub.eglobalmark.com/ngsi-ld/v1/entities type==Vehicle q==isParked==urn:ngsi-ld:OffStreetParking:Downtown1 Link:"<https://schema.lab.fiware.org/ld/context>; rel=http://www.w3.org/ns/json-ld#context; type=application/ld+json" Content-Type:application/json
```

* Search Vehicle entities whose brand is Tesla

```
http https://data-hub.eglobalmark.com/ngsi-ld/v1/entities type==Vehicle q==brandName==Tesla Link:"<https://schema.lab.fiware.org/ld/context>; rel=http://www.w3.org/ns/json-ld#context; type=application/ld+json" Content-Type:application/json
```

* Search Vehicle entities whose max speed is above 110

```
http https://data-hub.eglobalmark.com/ngsi-ld/v1/entities type==Vehicle q==maxSpeed>110 Link:"<https://schema.lab.fiware.org/ld/context>; rel=http://www.w3.org/ns/json-ld#context; type=application/ld+json" Content-Type:application/json
```

* Partial update of the property of an entity

```
http PATCH https://data-hub.eglobalmark.com/ngsi-ld/v1/entities/urn:ngsi-ld:Vehicle:A1234/attrs/brandName value=Toyota Link:"<https://schema.lab.fiware.org/ld/context>; rel=http://www.w3.org/ns/json-ld#context; type=application/ld+json"
```

* Update some properties of an entity

```
http PATCH https://data-hub.eglobalmark.com/ngsi-ld/v1/entities/urn:ngsi-ld:Vehicle:A1234/attrs Link:"<https://schema.lab.fiware.org/ld/context>; rel=http://www.w3.org/ns/json-ld#context; type=application/ld+json" < vehicle_newBrandName.json
```

Where `vehicle_newBrandName.json` is the following:

```json
{
  "brandName": {
    "type": "Property",
    "value": "Toyota"
  }
}
```

* Add a relationship to an entity

```
http POST https://data-hub.eglobalmark.com/ngsi-ld/v1/entities/urn:ngsi-ld:Vehicle:A1234/attrs Content-Type:application/json Link:"<https://schema.lab.fiware.org/ld/context>; rel=http://www.w3.org/ns/json-ld#context; type=application/ld+json" < vehicle_addOwner.json
```

Where `vehicle_addOwner.json` is the following:

```json
{
  "ownedBy": {
    "type": "Relationship",
    "object": "urn:ngsi-ld:Org:Ada"
  }
}
```

* Get the temporal evolution of a property

```
http https://data-hub.eglobalmark.com/ngsi-ld/v1/temporal/entities/urn:ngsi-ld:Vehicle:A1234 timerel==between time==2020-02-01T12:00:00Z endTime==2020-02-10T12:00:00Z Link:"<https://gist.githubusercontent.com/bobeal/2e5905a069ad534b4919839b6b4c1245/raw/ed0b0103c8b498c034d8ad367d3494d02a9ad28b/apic.jsonld>; rel=http://www.w3.org/ns/json-ld#context; type=application/ld+json" Content-Type:application/json
```

Sample payload returned:

```json
{
    "id": "urn:ngsi-ld:BeeHive:Diat-BH-000001",
    "type": "BeeHive",
    "@context": [
        "http://uri.etsi.org/ngsi-ld/v1/ngsi-ld-core-context.jsonld",
        "https://gist.githubusercontent.com/bobeal/2e5905a069ad534b4919839b6b4c1245/raw/ed0b0103c8b498c034d8ad367d3494d02a9ad28b/apic.jsonld",
        "https://gist.githubusercontent.com/bobeal/4a836c81b837673b12e9db9916b1cd35/raw/6c7b234b76f86eb66ece385bc93c3f139f8e8cad/egm.jsonld"
    ],
    "createdAt": "2020-01-24T13:08:32.327Z",
    "speed": {
        "type": "Property",
        "createdAt": "2020-01-24T13:08:32.614Z",
        "observedAt": "2020-02-14T16:28:24.89Z",
        "observedBy": {
            "object": "urn:ngsi-ld:Sensor:Diat-SS-000002",
            "type": "Relationship"
        },
        "unitCode": "E54",
        "value:s": [
            65.0,
            "2020-02-12T08:00:02.86Z",
            68.0,
            "2020-02-12T08:01:03.02Z"
        ]
    },
    "location": {
        "type": "GeoProperty",
        "value": {
            "coordinates": [
                43.627622,
                7.015441
            ],
            "type": "Point"
        }
    }
}
```

* Create a subscription (with the above Subscription example)

```
http POST https://data-hub.eglobalmark.com/ngsi-ld/v1/subscriptions < subscription_S1234.jsonld
```

* Query subscriptions

```
http https://data-hub.eglobalmark.com/ngsi-ld/v1/subscriptions Content-Type:application/json
```

* Get a subscription by URI

```
http https://data-hub.eglobalmark.com/ngsi-ld/v1/subscriptions/urn:ngsi-ld:Subscriptions:S1234 Content-Type:application/json
```

* Update a subscription

```
http PATCH https://data-hub.eglobalmark.com/ngsi-ld/v1/subscriptions/urn:ngsi-ld:Subscriptions:S1234 Content-Type:application/json < subscription__S1234_newQuery.json
```

Where `subscription__S1234_newQuery.json` is the following:

```json
{
  "id":"urn:ngsi-ld:Subscription:S1234",
  "type":"Subscription",
  "q": "maxSpeed>200",
  "@context": [
        "https://schema.lab.fiware.org/ld/context",
        "http://uri.etsi.org/ngsi-ld/v1/ngsi-ld-core-context.jsonld"
  ]
}
```

* Update a subscription Endpoint

```
http PATCH https://data-hub.eglobalmark.com/ngsi-ld/v1/subscriptions/urn:ngsi-ld:Subscriptions:S1234 Content-Type:application/json < subscription__S1234_newEndpoint.json
```

Where `subscription__S1234_newEndpoint.json` is the following:

```json
{
  "id":"urn:ngsi-ld:Subscription:S1234",
  "type":"Subscription",
  "notification": {
    "endpoint": {
      "uri": "http://my-domain-name",
      "accept": "application/json",
      "info": [
          {
            "key": "Authorization-token",
            "value": "New-Authorization-token-value"
          }
      ]
    }
  },
  "@context": [
        "https://schema.lab.fiware.org/ld/context",
        "http://uri.etsi.org/ngsi-ld/v1/ngsi-ld-core-context.jsonld"
  ]
}
```


* Delete a subscription

```
http DELETE https://data-hub.eglobalmark.com/ngsi-ld/v1/subscriptions/urn:ngsi-ld:Subscriptions:S1234 Content-Type:application/json
```

Other resources:

- Reference specification of the NGSI-LD API: https://www.etsi.org/deliver/etsi_gs/CIM/001_099/009/01.02.01_60/gs_CIM009v010201p.pdf
- NGSI-LD FAQ by FIWARE Foundation: https://fiware-datamodels.readthedocs.io/en/latest/ngsi-ld_faq/index.html
