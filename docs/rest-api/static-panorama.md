# Static Panorama

The Static Panorama API retrieves static images from spherical photos (360° panoramas) at specific locations. Use this to display street-level imagery in your applications without implementing a full panorama viewer. Simply embed it using `<img src="...">`, specifying the position and view direction in the parameters. The image is fixed and cannot be rotated further.

## Quick Links

- Textual documentation: https://developer.mapy.com/en/rest-api-mapy-cz/function/api-for-static-panorama/
- Swagger UI: https://api.mapy.com/v1/docs/static/
- OpenAPI: https://api.mapy.com/v1/docs/static/openapi.json

## Endpoints

- `GET /v1/static/pano` - Get a static panorama image for specific coordinates and viewing direction

## Key Parameters (Selection)

### Basic Parameters

- `apikey` (string) — Your API key for authentication ([How to get API key](getting-access.md))
- `lon` (number) — Longitude coordinate around which the nearest panorama is searched, within the specified radius
- `lat` (number) — Latitude coordinate around which the nearest panorama is searched, within the specified radius
- `width` (integer) — Image width in pixels (maximum: 1024, required)
- `height` (integer) — Image height in pixels (maximum: 1024, required)
- `radius` (integer) — Maximum radius for searching a panorama in meters (default: 50 m)

### View Direction Parameters

- `yaw` (string or number) — Orientation relative to north
  - `"auto"` (default) — Orientation in the direction of the photo
  - `"point"` — Orientation towards the input coordinates
  - Number — 0 to 2π (0 = north, in radians)
- `pitch` (number) — Tilting (– up, + down). Range: ±π (default: 0, in radians)
- `fov` (number) — Horizontal field of view (essentially zoom). Range: π/2 to π/20 (default: 1.2, in radians)

### Additional Parameters

- `lang` (string) — Preferred language, affects only the copyright text overlay
- `debug` (boolean) — If `true`, error details are returned in JSON format

> Complete parameter list available in Swagger / OpenAPI above.

**Note:** Images obtained from this function are intended for online display only. They cannot be stored or cached long-term – see the terms of service.

## Examples

### cURL

```bash
# Get a panorama view from a location
curl "https://api.mapy.com/v1/static/pano?apikey=YOUR_API_KEY&lon=16.6&lat=49.19&width=400&height=250&yaw=0" \
  --output panorama.jpg
```

### HTML (Direct Image)

```html
<!-- Basic panorama with default yaw (auto) -->
<img src="https://api.mapy.com/v1/static/pano?width=400&height=250&lon=16.6&lat=49.19&apikey=YOUR_API_KEY" 
     alt="Panorama view" />

<!-- Panorama with specific yaw direction (north) -->
<img src="https://api.mapy.com/v1/static/pano?width=400&height=250&lon=16.6&lat=49.19&yaw=0&apikey=YOUR_API_KEY" 
     alt="Panorama looking north" />

<!-- Panorama pointing towards coordinates -->
<img src="https://api.mapy.com/v1/static/pano?width=400&height=250&lon=16.6&lat=49.19&yaw=point&apikey=YOUR_API_KEY" 
     alt="Panorama pointing to location" />
```

## Understanding Parameters

### Yaw (Direction)

- `"auto"` (default) — View direction matches the photo's original orientation
- `"point"` — View direction points towards the input coordinates
- Number (0 to 2π radians):
  - `0` = North
  - `π/2` (≈1.57) = East
  - `π` (≈3.14) = South
  - `3π/2` (≈4.71) = West

### Pitch (Vertical Angle)

Values in radians (±π):
- `-π` ≈ -3.14 = Looking straight down
- `0` = Looking at horizon (default)
- `+π` ≈ 3.14 = Looking straight up

### Field of View (FOV)

Values in radians:
- `π/2` ≈ 1.57 = Wide angle view (~90°)
- `1.2` = Default (~69°)
- `π/20` ≈ 0.157 = Narrow/telephoto view (~9°)

Smaller FOV values = more zoomed in view  
Larger FOV values = wider angle view

### Radius

The `radius` parameter defines how far (in meters) the API will search for the nearest panorama from the specified coordinates. Default is 50 meters.

## Use Cases

- **Location Preview**: Display a photo of a specific location when you don't have one available
- **Quick Loading Placeholder**: Show a static view first, then load dynamic JS Panorama on user click
- **Virtual Tours**: Show street-level views without full panorama viewer
- **Reports**: Include street-level imagery in PDFs or documents
- **Email Marketing**: Embed location previews in emails
- **Real Estate**: Show property surroundings

## Common Errors and Limits

- **401 Unauthorized**: Invalid or missing API key
- **400 Bad Request**: Invalid parameter values (coordinates, angles, dimensions)
- **403 Forbidden**: API key doesn't have access to this resource
- **404 Not Found**: No panorama available at specified location within the search radius
- **429 Too Many Requests**: Rate limit exceeded
- **422 Unprocessable Entity**: Invalid parameter value or parameter combination

**Limitations:**
- Panoramas are only available for locations where they have been captured
- Maximum image dimensions: 1024x1024 pixels
- Coverage is primarily in Czech Republic and selected other areas
- Images are intended for online display only. Long-term storage or caching is not permitted – see terms of service.

For detailed error responses and rate limits, see the [OpenAPI specification](https://api.mapy.com/v1/docs/static/openapi.json) and [Getting Access](getting-access.md).

## Related

- [Getting Access](getting-access.md)
- [Static Maps](static-maps.md)
- [REST API Documentation](README.md)

