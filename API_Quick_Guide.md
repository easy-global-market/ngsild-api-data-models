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

For brevity and clarity, the `Authorization` header is not displayed in the sample HTTP requests described below.

## API usage examples

### Notes on `@context` resolution

Multiple namespaces are allowed (`ngsild` is the mandatory core context, fiware is a frequently used namespace, others are considered).

Most of the HTTP requests need to specify to which contexts they are referring. This to prevent namespaces collisions 
(e.g. a Vehicle entity definition that would exist in two different contexts).

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

* Search entities of type Vehicle

```
http https://data-hub.eglobalmark.com/ngsi-ld/v1/entities type==Vehicle Link:"<https://schema.lab.fiware.org/ld/context>; rel=http://www.w3.org/ns/json-ld#context; type=application/ld+json" Content-Type:application/json
```

* Search entities of type Vehicle having a given relationship

```
http https://data-hub.eglobalmark.com/ngsi-ld/v1/entities type==Vehicle q==isParked==urn:ngsi-ld:OffStreetParking:Downtown1 Link:"<https://schema.lab.fiware.org/ld/context>; rel=http://www.w3.org/ns/json-ld#context; type=application/ld+json" Content-Type:application/json
```

* Search entities of type Vehicle having a given property

```
http https://data-hub.eglobalmark.com/ngsi-ld/v1/entities type==Vehicle q==brandName==Tesla Link:"<https://schema.lab.fiware.org/ld/context>; rel=http://www.w3.org/ns/json-ld#context; type=application/ld+json" Content-Type:application/json
```

* Update a property of an entity

```
http PATCH https://data-hub.eglobalmark.com/ngsi-ld/v1/entities/urn:ngsi-ld:Vehicle:A1234/attrs/brandName value=Toyota Link:"<https://schema.lab.fiware.org/ld/context>; rel=http://www.w3.org/ns/json-ld#context; type=application/ld+json"
```

* Update some properties of an entity

```
http PATCH https://data-hub.eglobalmark.com/ngsi-ld/v1/entities/urn:ngsi-ld:Vehicle:A1234/attrs brandName=Toyota name=NewName Link:"<https://schema.lab.fiware.org/ld/context>; rel=http://www.w3.org/ns/json-ld#context; type=application/ld+json"
```

* Add a relationship to an entity

```
http POST http://localhost:8082/ngsi-ld/v1/entities/urn:ngsi-ld:BreedingService:0214/attrs Content-Type:application/json Link:"<https://gist.githubusercontent.com/bobeal/292f6ddf453bf3c427fb7206a2b5638a/raw/ae9b947caa0761b22a7c8a4078741d52a1f8c651/aquac.jsonld>; rel=http://www.w3.org/ns/json-ld#context; type=application/ld+json" < src/test/resources/ngsild/aquac/fragments/BreedingService_newRelationshipWithFeeder.json
```
