# Elevation

The Elevation API returns elevation data for given coordinates. It can return elevation for a single point or for a set of points (up to 256 at once). Use this to obtain elevation information for points or to create elevation profiles for routes.

## Quick Links

- Textual documentation: https://developer.mapy.com/en/rest-api-mapy-cz/function/elevation-api/
- Swagger UI: https://api.mapy.com/v1/docs/elevation/
- OpenAPI: https://api.mapy.com/v1/docs/elevation/openapi.json

## Endpoints

- `GET /v1/elevation` - Get elevation for given coordinates

## Key Parameters (Selection)

- `apikey` (string) — Your API key for authentication ([How to get API key](getting-access.md)) (alternative: X-Mapy-Api-Key header)
- `positions` (array, required) — Up to 256 position coordinates. Each position is represented by a pair of float numbers, denoting longitude and latitude respectively, separated by a comma.
  
  Multiple positions can be provided in two ways:
  1. As a semicolon-separated list: `positions=14.4009400,50.0711000;14.3951303,50.0704094`
  2. As an exploded array: `positions=14.4009400,50.0711000&positions=14.3951303,50.0704094`
  
  **Note:** The order is important: first number is longitude, second is latitude.
- `lang` (string, optional) — Preferred language (does not affect this function). Supported values: cs, de, el, en, es, fr, it, nl, pl, pt, ru, sk, tr, uk (default: cs)

> Complete parameter list available in Swagger / OpenAPI above.

## Response Format

The API returns an array with elevation data for each input position:

```json
{
  "items": [
    {
      "elevation": 198.37,
      "position": {
        "lon": 14.40094,
        "lat": 50.0711
      }
    }
  ]
}
```

**Elevation values:**
- Elevation is returned in meters above sea level
- If elevation information is not available for a given position:
  - For a single position query: API returns 404 Not Found error
  - For multiple positions query: API returns -100,000 for that position

## Examples

### cURL

```bash
# Get elevation for a single point
curl "https://api.mapy.com/v1/elevation?apikey=YOUR_API_KEY&positions=14.4009400,50.0711000"

# Get elevation for multiple points (semicolon-separated)
curl "https://api.mapy.com/v1/elevation?apikey=YOUR_API_KEY&positions=14.4009400,50.0711000;14.3951303,50.0704094"

# Get elevation for multiple points (exploded array)
curl "https://api.mapy.com/v1/elevation?apikey=YOUR_API_KEY&positions=14.4009400,50.0711000&positions=14.3951303,50.0704094"
```

### JavaScript Example: Route Elevation Profile with Leaflet

This example demonstrates how to calculate a route, get elevation data for it, and display an elevation profile on a Leaflet map. The example includes validation to ensure the route fits within the Elevation API limits (max 256 points).

```html
<!DOCTYPE html>
<html>
<head>
    <meta charset="utf-8" />
    <title>Route Elevation Profile</title>
    <link rel="stylesheet" href="https://unpkg.com/leaflet@1.9.4/dist/leaflet.css" />
    <script src="https://unpkg.com/leaflet@1.9.4/dist/leaflet.js"></script>
    <!-- leaflet-elevation -->
    <link rel="stylesheet" href="https://unpkg.com/@raruto/leaflet-elevation/dist/leaflet-elevation.css" />
    <script src="https://unpkg.com/@raruto/leaflet-elevation/dist/leaflet-elevation.js"></script>
    <style>
        #map { height: 500px; }
    </style>
</head>
<body>
    <div id="map"></div>
    <script>
        const API_KEY = 'YOUR_API_KEY';
        const map = L.map('map').setView([50.6968506, 15.7378861], 13);

        L.tileLayer(`https://api.mapy.com/v1/maptiles/basic/256/{z}/{x}/{y}?apikey=${API_KEY}`, {
            attribution: '<a href="https://api.mapy.com/copyright" target="_blank">&copy; Seznam.cz a.s. a další</a>',
        }).addTo(map);

        const controlElevation = L.control.elevation({ height: 200 }).addTo(map);

        const LogoControl = L.Control.extend({
            options: { position: 'bottomleft' },
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
        new LogoControl().addTo(map);

        const coordsStart = [15.7378861, 50.6968506];
        const coordsEnd = [15.6070481, 50.7251503];

        async function calculateRoute() {
            const url = new URL('https://api.mapy.com/v1/routing/route');
            url.searchParams.set('apikey', API_KEY);
            url.searchParams.set('start', coordsStart.join(','));
            url.searchParams.set('end', coordsEnd.join(','));
            url.searchParams.set('routeType', 'foot_fast');
            url.searchParams.set('format', 'geojson');

            const response = await fetch(url.toString());
            if (!response.ok) {
                console.error('Error calculating route');
                return null;
            }

            const data = await response.json();
            L.geoJSON(data.geometry, { style: { color: '#2196F3', weight: 5 } }).addTo(map);
            map.fitBounds(L.geoJSON(data.geometry).getBounds());
            return data.geometry;
        }

        function selectPoints(geometry) {
            const points = geometry.geometry.coordinates;
            if (points.length <= 256) return points;
            const step = Math.ceil(points.length / 256);
            const selected = [];
            for (let i = 0; i < points.length; i += step) {
                selected.push(points[i]);
            }
            if (selected[selected.length - 1] !== points[points.length - 1]) {
                selected.push(points[points.length - 1]);
            }
            return selected;
        }

        async function getElevationGeoJson(points) {
            const positions = points.map(p => p.join(',')).join(';');
            const url = `https://api.mapy.com/v1/elevation?positions=${positions}&apikey=${API_KEY}`;
            const response = await fetch(url);
            if (!response.ok) {
                console.error('Error getting elevation');
                return null;
            }
            const data = await response.json();
            return JSON.stringify({
                type: "FeatureCollection",
                features: [{
                    type: "Feature",
                    geometry: {
                        type: "LineString",
                        coordinates: data.items.map(item => [item.position.lon, item.position.lat, item.elevation])
                    }
                }]
            });
        }

        async function main() {
            const routeGeometry = await calculateRoute();
            if (!routeGeometry) return;

            const points = selectPoints(routeGeometry);
            const elevationGeoJson = await getElevationGeoJson(points);
            if (elevationGeoJson) {
                controlElevation.load(elevationGeoJson);
            }
        }

        map.whenReady(main);
    </script>
</body>
</html>
```

**Key features:**

- Calculates route using Routing API
- Selects up to 256 evenly distributed points
- Gets elevation data and displays profile using `leaflet-elevation` plugin
- Error handling (errors logged to console)

**Note:** This example requires the `leaflet-elevation` plugin. The plugin creates an elevation chart from GeoJSON LineString coordinates with elevation (Z) values.

## Elevation Model

The elevation model covers almost the entire world, except for areas around the North and South Poles.

The model is a combination of several elevation models with varying accuracy. For the world, a global model is used, and for Central European countries, national models created from highly accurate lidar data are used. Similar models for European countries are continuously added, with data being continually refined.

**Note:** This is still a model with varying accuracy, and height data may not necessarily correspond to reality.

## Use Cases

- **Route Elevation Profiles**: Get elevation profile along a planned route
- **Hiking Applications**: Display elevation information for hiking trails
- **Terrain Analysis**: Analyze topography for construction or planning
- **Fitness Apps**: Track altitude changes during activities

## Common Errors and Limits

- **401 Unauthorized**: Invalid or missing API key
- **400 Bad Request**: Invalid coordinates or parameters
- **403 Forbidden**: API key doesn't have access to this resource
- **404 Not Found**: Elevation data not available for specified location (single position only)
- **422 Validation Error**: Invalid parameter format or too many positions
- **429 Too Many Requests**: Rate limit exceeded

**Limitations:**
- Maximum 256 positions per request
- Maximum rate limit: 30 requests per second per API key
- Coverage: Almost entire world, except areas around North and South Poles

For detailed error responses and rate limits, see the [OpenAPI specification](https://api.mapy.com/v1/docs/elevation/openapi.json) and [Getting Access](getting-access.md).

## Related

- [Getting Access](getting-access.md)
- [Routing](routing.md)
- [Matrix Routing](matrix-routing.md)
- [REST API Documentation](README.md)

