# Routing

The Routing API provides route calculation between two points, optionally via up to fifteen waypoints. It supports different travel modes and returns route length, estimated duration, and geometry in the selected format.

## Quick Links

- Textual documentation: https://developer.mapy.com/en/rest-api-mapy-cz/function/routing/
- Swagger UI: https://api.mapy.com/v1/docs/routing/
- OpenAPI: https://api.mapy.com/v1/docs/routing/openapi.json

## Endpoints

- `GET /v1/routing/route` - Calculate a route between two points with optional waypoints

## Key Parameters (Selection)

- `apikey` (string) — Your API key for authentication ([How to get API key](getting-access.md)) (query parameter or X-Mapy-Api-Key header)
- `start` (array, required) — Coordinates of the beginning of the route `[longitude, latitude]`. Supports exploded `?start=14.40094&start=50.0711` and unexploded `?start=14.40094,50.0711` format
- `end` (array, required) — Coordinates of the end of the route `[longitude, latitude]`. Supports exploded `?end=14.40094&end=50.0711` and unexploded `?end=14.40094,50.0711` format
- `routeType` (string, required) — Route type:
  - `car_fast` - Fastest route by car
  - `car_fast_traffic` - Fastest route by car with real-time traffic
  - `car_short` - Shortest route by car
  - `foot_fast` - Fastest walking route
  - `foot_hiking` - Hiking trail
  - `bike_road` - Road cycling
  - `bike_mountain` - Mountain biking
- `waypoints` (array, optional) — Up to 15 optional waypoint coordinates between start and end. Supports semicolon-separated `?waypoints=14.4009400,50.0711000;14.3951303,50.0704094` or exploded array `?waypoints=14.4009400,50.0711000&waypoints=14.3951303,50.0704094` format
- `lang` (string, optional) — Language code: `cs`, `de`, `el`, `en`, `es`, `fr`, `it`, `nl`, `pl`, `pt`, `ru`, `sk`, `tr`, `uk` (default: `cs`)
- `format` (string, optional) — Geometry format: `geojson` (default), `polyline`, `polyline6`
- `avoidToll` (boolean, optional) — Avoid toll roads (default: `false`)
- `avoidHighways` (boolean, optional) — Avoid highways/motorways (default: `false`)
- `departure` (string, optional) — Departure time in local time (ISO-8601 format), e.g. `2025-01-01T00:00:00.000`. If not provided, planning uses "now". Departure time affects time-based closures and restrictions.

> Complete parameter list available in Swagger / OpenAPI above.

## Response Structure

The routing endpoint returns a JSON object with:

- `length` (integer) — Route length in meters
- `duration` (integer) — Route duration in seconds
- `geometry` — Planned route in the selected format (GeoJSON, polyline, or polyline6)
- `parts` (array, optional) — Individual route segments between waypoints, each containing:
  - `length` (integer) — Segment length in meters
  - `duration` (integer) — Segment duration in seconds
- `routePoints` (array, optional) — Detailed data about the start, destination and waypoints, each containing:
  - `originalPosition` (array) — Original input coordinates `[longitude, latitude]`
  - `mappedPosition` (array) — Routing network mapped position coordinates `[longitude, latitude]`
  - `snapDistance` (integer) — Distance in meters between original and mapped position
  - `restricted` (boolean) — Indicates that the point lies in an area where routing would not normally be planned, but the route was still calculated because the user explicitly forced this point (default: `false`)
  - `restrictionType` (string, optional) — Type of restriction (only present when `restricted` is `true`):
    - `NO_ENTRY` — No access / No vehicles or pedestrians allowed
    - `RESTRICTED_ENTRY` — Restricted access or entry (e.g. permit or payment required)
    - `PEDESTRIAN_ZONE` — Pedestrian zone
    - `CLOSURE` — Temporary closure
    - `OTHER_RESTRICTION` — Other traffic or planning restriction

### Response Format Examples

#### GeoJSON Format (`format=geojson`)

When using `format=geojson` (default), the response structure is:

```json
{
  "length": 8948,
  "duration": 1125,
  "geometry": {
    "type": "Feature",
    "geometry": {
      "type": "LineString",
      "coordinates": [[14.4378, 50.0755], [14.4400, 50.0780], [14.4500, 50.0800]]
    },
    "properties": {}
  },
  "parts": [
    {
      "length": 5000,
      "duration": 600
    },
    {
      "length": 3948,
      "duration": 525
    }
  ],
  "routePoints": [
    {
      "originalPosition": [14.4378, 50.0755],
      "mappedPosition": [14.4379, 50.0756],
      "snapDistance": 12,
      "restricted": false
    },
    {
      "originalPosition": [14.4500, 50.0800],
      "mappedPosition": [14.4501, 50.0801],
      "snapDistance": 8,
      "restricted": true,
      "restrictionType": "PEDESTRIAN_ZONE"
    }
  ]
}
```

**Important:** The `geometry` field contains a complete GeoJSON Feature object, not just the geometry. The actual LineString coordinates are nested in `geometry.geometry.coordinates`. When using mapping libraries like Leaflet, you can pass `data.geometry` directly to GeoJSON parsers.

**Example parsing in JavaScript:**
```javascript
fetch(`https://api.mapy.com/v1/routing/route?apikey=YOUR_API_KEY&start=14.4378,50.0755&end=16.6068,49.1951&routeType=car_fast&format=geojson`)
  .then(response => response.json())
  .then(data => {
    // data.geometry is already a GeoJSON Feature object
    const routeLayer = L.geoJSON(data.geometry, {
      style: { color: '#2196F3', weight: 5, opacity: 0.7 }
    }).addTo(map);
    
    // Get route info
    const distance = data.length / 1000; // km
    const duration = data.duration / 60; // minutes
    console.log(`Distance: ${distance.toFixed(2)} km, Duration: ${duration.toFixed(0)} min`);
  });
```

#### Polyline Format (`format=polyline`)

When using `format=polyline`, the `geometry` field contains an encoded polyline string:

```json
{
  "length": 8948,
  "duration": 1125,
  "geometry": "encoded_polyline_string_here"
}
```

#### Polyline6 Format (`format=polyline6`)

When using `format=polyline6`, the `geometry` field contains a polyline6 encoded string.

## Examples

### cURL

```bash
# Calculate a car route from Prague to Brno
curl "https://api.mapy.com/v1/routing/route?apikey=YOUR_API_KEY&start=14.4378,50.0755&end=16.6068,49.1951&routeType=car_fast"

# Calculate route with waypoints (unexploded format)
curl "https://api.mapy.com/v1/routing/route?apikey=YOUR_API_KEY&start=14.4378,50.0755&end=16.6068,49.1951&routeType=car_fast&waypoints=15.0,50.0&waypoints=15.5,49.8"

# Calculate route with waypoints (semicolon-separated format)
curl "https://api.mapy.com/v1/routing/route?apikey=YOUR_API_KEY&start=14.4378,50.0755&end=16.6068,49.1951&routeType=car_fast&waypoints=15.0,50.0;15.5,49.8"

# Calculate walking route
curl "https://api.mapy.com/v1/routing/route?apikey=YOUR_API_KEY&start=14.4378,50.0755&end=16.6068,49.1951&routeType=foot_fast"

# Calculate route avoiding toll roads
curl "https://api.mapy.com/v1/routing/route?apikey=YOUR_API_KEY&start=14.4378,50.0755&end=16.6068,49.1951&routeType=car_fast&avoidToll=true"

# Calculate route avoiding highways
curl "https://api.mapy.com/v1/routing/route?apikey=YOUR_API_KEY&start=14.4378,50.0755&end=16.6068,49.1951&routeType=car_fast&avoidHighways=true"

# Calculate route with departure time
curl "https://api.mapy.com/v1/routing/route?apikey=YOUR_API_KEY&start=14.4378,50.0755&end=16.6068,49.1951&routeType=car_fast&departure=2025-01-01T00:00:00.000"

# Get route in polyline format
curl "https://api.mapy.com/v1/routing/route?apikey=YOUR_API_KEY&start=14.4378,50.0755&end=16.6068,49.1951&routeType=car_fast&format=polyline"

# Calculate route with exploded start/end coordinates
curl "https://api.mapy.com/v1/routing/route?apikey=YOUR_API_KEY&start=14.4378&start=50.0755&end=16.6068&end=49.1951&routeType=car_fast"
```

### JavaScript Example with Leaflet

This example demonstrates how to calculate and display a route from Prague to Brno using Leaflet.js:

```html
<!DOCTYPE html>
<html>
<head>
    <meta charset="utf-8" />
    <title>Route from Prague to Brno</title>
    <link rel="stylesheet" href="https://unpkg.com/leaflet@1.9.4/dist/leaflet.css" />
    <script src="https://unpkg.com/leaflet@1.9.4/dist/leaflet.js"></script>
    <style>
        #map { height: 500px; }
    </style>
</head>
<body>
    <div id="map"></div>
    <script>
        const API_KEY = 'YOUR_API_KEY';
        const map = L.map('map').setView([50.0755, 14.4378], 8);

        L.tileLayer(`https://api.mapy.com/v1/maptiles/basic/256/{z}/{x}/{y}?apikey=${API_KEY}`, {
            attribution: '<a href="https://api.mapy.com/copyright" target="_blank">&copy; Seznam.cz a.s. a další</a>',
        }).addTo(map);

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

        const coordsStart = [14.4378, 50.0755]; // Prague
        const coordsEnd = [16.6068, 49.1951];   // Brno

        async function calculateRoute() {
            const url = new URL('https://api.mapy.com/v1/routing/route');
            url.searchParams.set('apikey', API_KEY);
            url.searchParams.set('start', coordsStart.join(','));
            url.searchParams.set('end', coordsEnd.join(','));
            url.searchParams.set('routeType', 'car_fast');
            url.searchParams.set('format', 'geojson');

            const response = await fetch(url.toString());
            if (!response.ok) {
                console.error('Error calculating route');
                return null;
            }

            const data = await response.json();
            
            const routeLayer = L.geoJSON(data.geometry, {
                style: {
                    color: '#2196F3',
                    weight: 5,
                    opacity: 0.7
                }
            }).addTo(map);

            map.fitBounds(routeLayer.getBounds());

            const distance = (data.length / 1000).toFixed(2);
            const duration = Math.floor(data.duration / 60);
            console.log(`Route: ${distance} km, Duration: ${duration} min`);

            return data;
        }

        map.whenReady(calculateRoute);
    </script>
</body>
</html>
```

## Route Errors

If it is not possible to plan the route for the selected mode of transport, the service will return HTTP response code `404` with error details:

```json
{
  "detail": [
    {
      "msg": "Edge not found",
      "errorCode": 7
    }
  ]
}
```

### Possible Routing Errors

- `errorCode: 7` - The route point is outside the available route network for the selected type of transport
- `errorCode: 9` - The route point is located in an area to which is not connected by the selected mode of transport (e.g., another continent)

## Common Errors and Limits

- **401 Unauthorized**: Invalid or missing API key
- **403 Forbidden**: API key doesn't have access to this resource
- **404 Not Found**: Route cannot be calculated (point outside network, disconnected area)
- **422 Validation Error**: Invalid parameters (missing required parameters, invalid coordinates, invalid parameter values)

**Rate Limits:**
- Maximum 30 requests per second per API key

**Limitations:**
- Maximum 15 waypoints per route
- Coordinates must be valid WGS84 format (longitude: -180 to 180, latitude: -90 to 90)

For detailed error responses and rate limits, see the [OpenAPI specification](https://api.mapy.com/v1/docs/routing/openapi.json) and [Getting Access](getting-access.md).

## Use Cases

- **Navigation**: Calculate routes for navigation applications
- **Trip Planning**: Plan multi-stop trips with waypoints
- **Routing Analysis**: Analyze routes for different transport modes
- **Logistics**: Optimize delivery routes with multiple stops
- **Mapping Applications**: Display routes on maps

## Related

- [Getting Access](getting-access.md)
- [Matrix Routing](matrix-routing.md)
- [Forward Geocoding](forward-geocoding.md)
- [URL Route](../url-mapy/route.md)
- [REST API Documentation](README.md)



