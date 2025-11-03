# Map Tiles

The Map Tiles API provides access to raster map tiles in various formats and mapsets. Use this API to create interactive maps with custom tile layers in your applications using libraries like Leaflet or MapLibre.

## Quick Links

- Textual documentation: https://developer.mapy.com/en/rest-api-mapy-cz/function/map-tiles/
- Swagger UI: https://api.mapy.com/v1/docs/maptiles/
- OpenAPI (YAML): https://api.mapy.com/v1/docs/maptiles/openapi.yaml

## Endpoints

- `GET /v1/maptiles/{mapset}/{tileSize}/{z}/{x}/{y}` - Get map tile image

Get individual raster map tiles for use in mapping applications.

### Path Parameters

- `mapset` (string, required) — Map layer name: `basic`, `outdoor`, `winter`, `aerial`, `names-overlay`
- `tileSize` (string, required) — Tile image size: `256` (standard) or `256@2x` (retina/high-DPI, only for basic and outdoor)
- `z` (integer, required) — Zoom level (0-20), where higher values show more detail
- `x` (integer, required) — Tile X coordinate in xyz TileJSON scheme
- `y` (integer, required) — Tile Y coordinate in xyz TileJSON scheme

### Query Parameters

- `apikey` (string) — Your API key for authentication ([How to get API key](getting-access.md)) (alternative: X-Mapy-Api-Key header)
- `lang` (string) — Preferred language for labels: `cs`, `de`, `el`, `en`, `es`, `fr`, `it`, `nl`, `pl`, `pt`, `ru`, `sk`, `tr`, `uk` (default: `cs`). Affects only tiles with z ≤ 6

> Complete parameter list available in Swagger / YAML above.

## Examples

### cURL

```bash
# Get a basic map tile for Prague area (standard resolution)
curl "https://api.mapy.com/v1/maptiles/basic/256/14/8956/5513?apikey=YOUR_API_KEY" \
  --output tile.png
```

### Leaflet Example

```html
<!DOCTYPE html>
<html>
  <head>
    <meta charset="utf-8" />
    <title>Simple map in Leaflet</title>
    <meta name="viewport" content="initial-scale=1,maximum-scale=1,user-scalable=no" />
    <link rel="stylesheet" href="https://unpkg.com/leaflet@1.9.4/dist/leaflet.css" />
    <script src="https://unpkg.com/leaflet@1.9.4/dist/leaflet.js"></script>
  </head>
  <style>
      body {
          margin: 0;
          padding: 0;
      }

      #map {
          position: absolute;
          top: 0;
          bottom: 0;
          width: 100%;
      }
  </style>
  <body>      
      <div id="map"></div>
      <script>
          // replace with your own API key
          const API_KEY = 'YOUR_API_KEY';

          /*
          We create the map and set its initial coordinates and zoom.
          */
          const map = L.map('map').setView([49.8729317, 14.8981184], 16);

          /*
          Then we add a raster tile layer with REST API Mapy.cz tiles
          */
          L.tileLayer(`https://api.mapy.com/v1/maptiles/basic/256/{z}/{x}/{y}?apikey=${API_KEY}`, {
              minZoom: 0,
              maxZoom: 20,
              attribution: '<a href="https://api.mapy.com/copyright" target="_blank">&copy; Seznam.cz a.s. a další</a>',
          }).addTo(map);

          /*
          We also require you to include our logo somewhere over the map.
          We create our own map control implementing a documented interface,
          that shows a clickable logo.
          */
          const LogoControl = L.Control.extend({
              options: {
                  position: 'bottomleft',
              },

              onAdd: function (map) {
                  const container = L.DomUtil.create('div');
                  const link = L.DomUtil.create('a', '', container);

                  link.setAttribute('href', 'http://mapy.com/');
                  link.setAttribute('target', '_blank');
                  link.innerHTML = '<img src="https://api.mapy.com/img/api/logo.svg" />';
                  L.DomEvent.disableClickPropagation(link);

                  return container;
              },
          });

          // finally we add our LogoControl to the map
          new LogoControl().addTo(map);
      </script>
  </body>
</html>
```

### MapLibre GL JS Example

```html
<!DOCTYPE html>
<html>
  <head>
    <meta charset="utf-8" />
    <title>Simple map in MapLibre GL JS</title>
    <meta name="viewport" content="initial-scale=1,maximum-scale=1,user-scalable=no" />
    <link href="https://unpkg.com/maplibre-gl@3.6.2/dist/maplibre-gl.css" rel="stylesheet" />
    <script src="https://unpkg.com/maplibre-gl@3.6.2/dist/maplibre-gl.js"></script>
  </head>
  <style>
      body {
          margin: 0;
          padding: 0;
      }

      #map {
          position: absolute;
          top: 0;
          bottom: 0;
          width: 100%;
      }
  </style>
  <body>      
      <div id="map"></div>
      <script>
          // replace with your own API key
          const API_KEY = 'YOUR_API_KEY';

          /*
          We create a map with initial coordinates, zoom, a raster tile source and a layer using that source.
          */
          const map = new maplibregl.Map({
              container: 'map',
              center: [14.8981184, 49.8729317],
              zoom: 15,
              style: {
                  version: 8,
                  sources: {
                      'basic-tiles': {
                          type: 'raster',
                          tiles: [
                              `https://api.mapy.com/v1/maptiles/basic/256/{z}/{x}/{y}?apikey=${API_KEY}`
                          ],
                          tileSize: 256,
                          minzoom: 0,
                          maxzoom: 20,
                      },
                  },
                  layers: [{
                      id: 'tiles',
                      type: 'raster',
                      source: 'basic-tiles',
                  }],
              },
          });

          /*
          We also require you to include our logo somewhere over the map.
          We create our own map control implementing a documented interface,
          that shows a clickable logo.
          */
          class LogoControl {
              onAdd(map) {
                  this._map = map;
                  this._container = document.createElement('div');
                  this._container.className = 'maplibregl-ctrl';
                  this._container.innerHTML = '<a href="http://mapy.com/" target="_blank"><img src="https://api.mapy.com/img/api/logo.svg" /></a>';

                  return this._container;
              }
           
              onRemove() {
                  this._container.parentNode.removeChild(this._container);
                  this._map = undefined;
              }
          }

          // finally we add our LogoControl to the map
          map.on('load', () => {
              map.addControl(new LogoControl(), 'bottom-left');
          });
      </script>
  </body>
</html>
```

## Common Errors and Limits

- **401 Unauthorized**: apikey parameter was not provided
- **403 Forbidden**: Invalid apikey or not permitted to access given resource
- **404 Not Found**: Invalid parameter value, parameter combination, or mapset name

**Rate Limits:**
- Tile images: Maximum 500 requests per second per API key

For detailed error responses and rate limits, see the [OpenAPI specification](https://api.mapy.com/v1/docs/maptiles/openapi.yaml) and [Getting Access](getting-access.md).

## Related

- [Getting Access](getting-access.md)
- [Static Maps](static-maps.md)
- [REST API Documentation](README.md)

