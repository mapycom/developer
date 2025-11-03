# Static Maps

The Static Maps API generates static map images without requiring interactive map libraries or JavaScript. You can call this function directly in an `<img src="...">` tag. Using the parameters, you can choose the image size, map type, displayed area, and add markers to the map.

## Quick Links

- Textual documentation: https://developer.mapy.com/en/rest-api-mapy-cz/function/static-maps/
- Swagger UI: https://api.mapy.com/v1/docs/static/
- OpenAPI: https://api.mapy.com/v1/docs/static/openapi.json

## Endpoints

- `GET /v1/static/map` - Generate a static map image with optional markers and overlays

## Key Parameters (Selection)

### Basic Parameters

- `apikey` (string) — Your API key for authentication ([How to get API key](getting-access.md))
- `mapset` (string) — Map style: `basic`, `outdoor`, `winter`, `aerial`, `aerial-names-overlay`
- `width` (integer) — Image width in pixels (maximum: 1024)
- `height` (integer) — Image height in pixels (maximum: 1024)
- `scale` (integer) — `1` for standard resolution, `2` for retina resolution (resulting image will have double size – only for `basic` and `outdoor` mapset)
- `format` (string) — Output format: `png`, `jpg`, `webp`, `gif`
- `lang` (string) — Preferred language. Affects only zoom <= 6. Country names are displayed in English for all languages except `cs`, `sk`
- `debug` (boolean) — If `true`, error details are returned in JSON format

### Parameters for Selecting the Displayed Area

- `lon`, `lat` (number) — Three ways to use:
  1. Single pair: Center coordinates used with `zoom` parameter
  2. Two pairs: Bounding box (top-left and bottom-right corners). `zoom` is calculated automatically
  3. None: Best fit for overlay layers (e.g., markers)
- `zoom` (integer) — Desired map zoom level (used with single center coordinates)
- `padding` (integer) — Margin in pixels around displayed area (used with options 2 or 3)

### Displaying Additional Layers

- `markers` (string) — Add markers on the map. Format: `color:red;size:large;label:R;14.422,50.089`
  - Mandatory: Coordinates (multiple coordinates can be specified in succession for multiple markers in same style)
  - Optional: `color` (marker color), `size` (marker size), `label` (max. two-character label), `label-color` (label color)
  - Parameter can be repeated for multiple markers in different styles
- `shapes` (string) — Add lines or closed polygons above the map. Format: `color:red;path:[(lon1,lat1;lon2,lat2;...)]` or `color:blue;fill:%230000FF80;polygon:[(lon1,lat1;lon2,lat2;...)]`
  - Mandatory: `path` or `polygon` + coordinate field
  - Optional: `color` (line color), `fill` (fill color), `width` (line width)
  - Parameter can be repeated for multiple shapes in different styles

> Complete parameter list available in Swagger / OpenAPI above.

## Examples

### cURL

```bash
# Simple tourist map centered on Špindlerův Mlýn
curl "https://api.mapy.com/v1/static/map?lon=15.608&lat=50.725&zoom=16&width=300&height=200&mapset=outdoor&apikey=YOUR_API_KEY" \
  --output map.png
```

### HTML (Direct Image)

```html
<!-- Simple tourist map -->
<img src="https://api.mapy.com/v1/static/map?lon=15.608&lat=50.725&zoom=16&width=300&height=200&mapset=outdoor&apikey=YOUR_API_KEY" 
     alt="Tourist map" />

<!-- Aerial map with marker -->
<img src="https://api.mapy.com/v1/static/map?lon=15.608&lat=50.725&zoom=16&width=300&height=200&mapset=aerial&markers=color:red;size:normal;label:A;15.608,50.725&apikey=YOUR_API_KEY" 
     alt="Aerial map with marker" />
```

## Advanced Examples

### Multiple Markers with Best Fit

When no coordinates are provided, the map automatically adjusts to show all markers:

```html
<img src="https://api.mapy.com/v1/static/map?width=300&height=200&mapset=winter&markers=color:red;size:normal;label:A;15.6051,50.7270&markers=color:green;size:normal;label:B;15.6111,50.7230&markers=color:blue;size:normal;label:C;15.6031,50.7220&apikey=YOUR_API_KEY" 
     alt="Map with multiple markers" />
```

### Using Bounding Box

Specify `lon` and `lat` twice to define a bounding box:

```html
<img src="https://api.mapy.com/v1/static/map?lon=12.09&lat=48.55&lon=18.87&lat=51.05&width=300&height=200&mapset=basic&apikey=YOUR_API_KEY" 
     alt="Map of Czech Republic" />
```

### Adding Shapes

```html
<img src="https://api.mapy.com/v1/static/map?width=500&height=450&mapset=aerial&shapes=color:red;path:[(15.6054,50.7260;15.6074,50.7260;15.6074,50.7240;15.6054,50.7240;15.6054,50.7260)]&shapes=color:blue;fill:%230000FF80;polygon:[(15.6112,50.7255;15.6132,50.7250;15.6152,50.7255;15.6112,50.7255)]&apikey=YOUR_API_KEY" 
     alt="Map with shapes" />
```

### Retina Resolution

```html
<img src="https://api.mapy.com/v1/static/map?lon=15.742&lat=50.735&zoom=14&width=200&height=200&scale=2&mapset=outdoor&markers=color:red;size:normal;label:1;15.742,50.735&apikey=YOUR_API_KEY" 
     alt="High-resolution map" />
```

## Common Errors and Limits

- **401 Unauthorized**: Invalid or missing API key
- **400 Bad Request**: Invalid parameter values (coordinates, dimensions)
- **403 Forbidden**: API key doesn't have access to this resource
- **429 Too Many Requests**: Rate limit exceeded

**Important Notes:**
- Maximum image dimensions: 1024x1024 pixels
- Images are intended for online display only. Long-term storage or caching is not permitted – see terms of service.
- For detailed error responses and rate limits, see the [OpenAPI specification](https://api.mapy.com/v1/docs/static/openapi.json) and [Getting Access](getting-access.md).

## Related

- [Getting Access](getting-access.md)
- [Map Tiles](map-tiles.md)
- [Static Panorama](static-panorama.md)
- [REST API Documentation](README.md)

