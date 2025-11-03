# Elevation

The Elevation API returns elevation data for given coordinates. It can return elevation for a single point or for a set of points (up to 256 at once – maximum within a 1×1 degree range). Use this to obtain elevation information for points or to create elevation profiles for routes.

## Quick Links

- Textual documentation: https://developer.mapy.com/en/rest-api-mapy-cz/function/elevation-api/
- Swagger UI: https://api.mapy.com/v1/docs/elevation/
- OpenAPI: https://api.mapy.com/v1/docs/elevation/openapi.json

## Endpoints

- `GET /v1/elevation` - Get elevation for given coordinates

## Key Parameters (Selection)

- `apikey` (string) — Your API key for authentication ([How to get API key](getting-access.md)) (alternative: X-Mapy-Api-Key header)
- `positions` (array, required) — Up to 256 position coordinates within a 1×1 degree bounding box. Each position is represented by a pair of float numbers, denoting longitude and latitude respectively, separated by a comma.
  
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
- Positions must be within a 1×1 degree bounding box
- Maximum rate limit: 30 requests per second per API key
- Coverage: Almost entire world, except areas around North and South Poles

For detailed error responses and rate limits, see the [OpenAPI specification](https://api.mapy.com/v1/docs/elevation/openapi.json) and [Getting Access](getting-access.md).

## Related

- [Getting Access](getting-access.md)
- [Routing](routing.md)
- [Matrix Routing](matrix-routing.md)
- [REST API Documentation](README.md)

