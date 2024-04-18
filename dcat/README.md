# Definition of a DataService

The definition of a `DataService` is a direct application of the definition given in the [DCAT specification](https://www.w3.org/TR/vocab-dcat-3/#dcat-scope). As such, the [JSON-LD context](https://github.com/easy-global-market/ngsild-api-data-models/blob/master/dcat/jsonld-contexts/dcat.jsonld) used to define and interact with `DataService` entities uses the same vocabulary as the one used in the DCAT specification.

More specifically, the following attributes are used:
- `isReferencedBy`: the use-case in which it can be used (mandatory)
- `title`: title given to the data service (mandatory)
- `description`: a more detailed description of what the data service is about (mandatory)
- `imageSource`: link to a logo to be displayed in the list of data services and in the configuration of a data service (mandatory)
- `inputEntityTypes`: type of entities that can be used as input by the data service (mandatory)
- `inputAttributes`: attributes that should be present on the selected entities for the data service to achieve its processing (mandatory)
- `outputAttributes`: attributes used by the data service to store the results of its processing (mandatory)
- `endpointDescription`: technical description of the behavior of the data service (mandatory)
- `endpointURL`: URL where the data service will be notified of updates related to the selected entities