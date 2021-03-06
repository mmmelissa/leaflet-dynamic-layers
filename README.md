A Leaflet plugin to make adding map layers easier. Users just need to describe their data with an array of JSON 'metadata' objects rather than adding each layer with redundant imperative code. Access to map layer references are available, so a user may still access full Leaflet layer methods and features. This project is definitely a work-in-progress and open to contributions. 

<div class="panel panel-warning">
**Warning**
{: .panel-heading}
    <div class="panel-body">

    This documentation is also a work-in-progress. The intention is to keep it current, but its likely already out of date. Please submit an issue or pull request if you notice any needed improvements! 

    </div>
</div>

![](https://github.com/mmnoo/leaflet-declarative-layers/blob/master/.github/workflows/nodejs.yml/badge.svg)

## Project setup

```
npm install leaflet
npm install
```

### Lint, test, bundle (minification is a to do item still)

```npm run build```

### Bundle only
 
```npm run bundle```

### Run unit tests

```npm run test```

### Lints and fixes files

```npm run lint```

## Usage

### Add the script to your file:
```<script src="../dist/leafletDeclarativeLayers.js"></script>```

(For now, first, you may need to run ```npm run build``` to compile the ```leafletDeclarativeLayers.js``` script. Eventually we will make the library available by other conventional means.)

### Describe your data with a 'metadata' JSON object. Available properties described below in the Properties section.
```
// data to be added, in this case, geoJSON
const geojsonFeatureCollection = {
    type: 'FeatureCollection',
    features: [
        {
            type: 'Feature',
            properties: {
                popupContent: '18th & California Light Rail Stop'
            },
            geometry: {
                type: 'Point',
                coordinates: [-115, 49.5]
            },
        },
    ],
};
// the 'metadata'. An array of JSON objects that describes the data to be added to the map.
const layerMetadata = [
    {
        id: 'tileLayer',
        label: 'Tile Layer Example',
        url: 'https://stamen-tiles.a.ssl.fastly.net/toner-lite/{z}/{x}/{y}.png',
        visibleInitially: true,
    }, 
    {
        id: 'geoJsonLayer',
        label: 'GeoJSON Layer Example',
        data: geojsonFeatureCollection,
        visibleInitially: true,
        generateFeaturePopupContent: (feature) => {
        return feature.properties.popupContent;
        },

    }
];

```
### Initialize a Leaflet map, and then a new DeclarativeLayers instance
```// regular Leaflet set up. Initialize a map, add a basemap
const map = new L.Map('map', { zoom: 8, center: new L.LatLng(49.5, -115)});
const basemap = new L.TileLayer('https://{s}.tile.openstreetmap.org/{z}/{x}/{y}.png', {minZoom: 8, maxZoom: 12});		
map.addLayer(basemap); 

// initialize leaflet-declarative-layers with the map, and the metadata array
const declaredLayers = new L.DeclarativeLayers(map, layerMetadata); 
```

### One can access any of the layer references by the id used in the metadata
```declaredLayers.getLayerReferences().tileLayer```
This means you can use it like you would any other Leaflet layer. For example:
```declaredLayers.getLayerReferences().tileLayer.redraw()```

## Methods
|Method|Returns|Description|
|---|---|---|
|getLayerReferences()| `Object` of  references to Leaflet layers|Returns an `Object` containing references to all of the Leaflet layers added via the `DeclarativeLayers` class. Individual layers will be accessible by their id as specified in the metadata. 
|addLayer(`{metadata}`)| Leaflet layer reference for added layer|Adds a single layer to the map. A single metadata object parameter is required.|
|removeLayer(`Leaflet layer reference`) |`void`| Removes a layer from the map. The required Leaflet layer reference parameter can be obtained from the `getLayerReference` method or via other Leaflet methods.  

## Currently Available Metadata Properties 
### Tile Layers (documentation to come)
### Image Layers (documentation to come)
### GeoJson Layers
|Property|Required/Optional|Description|
|---|---|---|
|data|Required| A `GeoJson` object|
|options| Optional|A Leaflet `GeoJsonOptions` object. See Leaflet documentation.|
|generateFeaturePopupContent(`function(feature){}`)|Optional| A function to generate a Leaflet pop up window for each GeoJson feature in a layer when it is clicked. The single required parameter is a function that returns either a `string` or `HTML element`. E.g.: `generateFeaturePopupContent: (feature) => { return feature.properties.popupContent;},` or `generateFeaturePopupContent: () => { return 'I'm a string'},`
