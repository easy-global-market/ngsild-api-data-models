# Context files for agrifood
Contains:
* context file from smartdata models **dataModel.Agrifood** for `['hasAgriCrop', 'cropStatus', 'lastPlantedAt', 'relativeHumidity', 'temperature', 'co2', 'agroVocConcept']` (https://raw.githubusercontent.com/smart-data-models/dataModel.Agrifood/master/context.jsonld)
* context files from smartdata models **dataModel.Device** for `['controlledAsset', 'controlledProperty', 'serialNumber', 'dateInstalled']` (https://raw.githubusercontent.com/smart-data-models/dataModel.Device/master/context.jsonld)
* a custom weather.jsonld file that contains properties not present in the above standard context files: `['OffGroundParcel', 'harvestWeight', 'hasOffGroundParcel', 'observedBy', 'seededAt', 'spawn']` (https://easy-global-market.github.io/ngsild-api-data-models/agrifood/jsonld-contexts/agrifood.jsonld).

    *pull request to be created on smartdata model to add these properties.*