# Reverse Geocoding

The Reverse Geocoding API converts geographic coordinates to addresses and regional entities. It returns regional entities based on coordinates (useful for "click on map" features where users select a location and you need to display the corresponding address).

## Quick Links

- Textual documentation: https://developer.mapy.com/en/rest-api-mapy-cz/function/geocoding/
- Swagger UI: https://api.mapy.com/v1/docs/geocode/
- OpenAPI: https://api.mapy.com/v1/docs/geocode/openapi.json

## Endpoints

- `GET /v1/rgeocode` - Reverse geocoding (coordinates → address)

## Key Parameters (Selection)

- `apikey` (string) — Your API key for authentication ([How to get API key](getting-access.md)) (query parameter or X-Mapy-Api-Key header)
- `lon` (number, required) — Longitude in degrees (-180 to 180, decimal point is ".")
- `lat` (number, required) — Latitude in degrees (-90 to 90, decimal point is ".")
- `lang` (string) — Preferred language for result entity names: `cs`, `de`, `el`, `en`, `es`, `fr`, `it`, `nl`, `pl`, `pt`, `ru`, `sk`, `tr`, `uk` (default: `cs`)

> Complete parameter list available in Swagger / OpenAPI above.

**Note:** Reverse geocoding returns only regional entity types (country, region, municipality, municipality_part, street, address), not POIs.

## Response Structure

The reverse geocode endpoint returns a JSON object with:

- `items` (array) — List of regional entities at the given location, each containing:
  - `name` (string) — Name of the entity (e.g., "Týnská ulička 610/7")
  - `label` (string) — Type label (e.g., "Adresa", "Ulice")
  - `position` (object) — Coordinates with `lon` and `lat` properties
  - `bbox` (array) — Bounding box as `[minLon, minLat, maxLon, maxLat]`
  - `type` (string) — Regional entity type (e.g., "regional.address", "regional.street", "regional.municipality")
  - `location` (string) — Short locality label (e.g., "Praha 1 - Staré Město, Česko")
  - `regionalStructure` (array) — Ordered list of parent administrative entities (smallest first), each with `name`, `type`, and optionally `isoCode` for countries
  - `zip` (string, optional) — Postal code (available only for some addresses)

## Examples

### cURL

```bash
# Find address for coordinates
curl "https://api.mapy.com/v1/rgeocode?apikey=YOUR_API_KEY&lon=14.4378&lat=50.0755"

# Get regional information for specific location
curl "https://api.mapy.com/v1/rgeocode?apikey=YOUR_API_KEY&lon=14.42212&lat=50.08861"

# Get results in English
curl "https://api.mapy.com/v1/rgeocode?apikey=YOUR_API_KEY&lon=14.4378&lat=50.0755&lang=en"
```

### JavaScript Example with Leaflet

This example demonstrates how to get address information by clicking on the map:

```html
<!DOCTYPE html>
<html>
<head>
    <meta charset="utf-8" />
    <title>Reverse Geocoding</title>
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
        const map = L.map('map').setView([49.8729317, 14.8981184], 15);

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

        map.on('click', async function (e) {
            const lat = e.latlng.lat;
            const lon = e.latlng.lng;
            const url = `https://api.mapy.com/v1/rgeocode?lon=${lon}&lat=${lat}&lang=cs&apikey=${API_KEY}`;

            try {
                const response = await fetch(url);
                if (!response.ok) {
                    console.error('Error fetching reverse geocode data');
                    return;
                }

                const data = await response.json();
                console.log(data.items);

                let html = '';
                if (data?.items?.length > 0) {
                    html = '<ul>';
                    data.items.forEach(item => {
                        html += `<li><strong>${item.label}:</strong> ${item.name}</li>`;
                    });
                    html += '</ul>';
                } else {
                    html = '<p>No results found.</p>';
                }

                L.popup().setLatLng(e.latlng).setContent(html).openOn(map);
            } catch (error) {
                console.error('Error:', error);
            }
        });
    </script>
</body>
</html>
```

## Use Cases

- **Click on Map**: Get address when user clicks on a map location
- **Location Services**: Display current location address
- **GPS Coordinates**: Convert GPS coordinates to readable addresses
- **Mapping Applications**: Show address information for selected points
- **Location Validation**: Verify if coordinates correspond to valid addresses

## Common Errors and Limits

- **422 Validation Error**: Invalid parameters (missing required parameters, invalid coordinates, invalid parameter values)
- **500 Internal Server Error**: Server-side error

**Rate Limits:**
- Reverse Geocoding (rgeocode): Maximum 200 requests per second per API key

For detailed error responses and rate limits, see the [OpenAPI specification](https://api.mapy.com/v1/docs/geocode/openapi.json) and [Getting Access](getting-access.md).

## Related

- [Getting Access](getting-access.md)
- [Forward Geocoding](forward-geocoding.md)
- [Routing](routing.md)
- [Matrix Routing](matrix-routing.md)
- [URL Search](../url-mapy/search.md)
- [REST API Documentation](README.md)

