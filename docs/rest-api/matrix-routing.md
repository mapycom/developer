# Matrix Routing

The Matrix Routing API calculates distances and travel times for a matrix of starts and ends. It is useful for calculating distances/durations between multiple origins and destinations simultaneously, supporting NxM matrix routing or full NxN matrix routing.

## Quick Links

- Textual documentation: https://developer.mapy.com/en/rest-api-mapy-cz/function/routing/
- Swagger UI: https://api.mapy.com/v1/docs/routing/
- OpenAPI: https://api.mapy.com/v1/docs/routing/openapi.json

## Endpoints

- `GET /v1/routing/matrix-m` - Calculate distance/time matrix between multiple points

## Key Parameters (Selection)

- `apikey` (string) — Your API key for authentication ([How to get API key](getting-access.md)) (query parameter or X-Mapy-Api-Key header)
- `starts` (array, required) — Coordinates of the beginning of routes. An array of coordinate strings. Supports semicolon-separated `?starts=14.40094,50.0711;14.40194,50.0721` or exploded array `?starts=14.40094,50.0711&starts=14.40194,50.0721` format. Maximum number of starts is 100
- `ends` (array, optional) — Coordinates of the end of routes. An array of coordinate strings. Supports semicolon-separated `?ends=14.40194,50.0721;14.3961303,50.0714094` or exploded array `?ends=14.40194,50.0721&ends=14.3961303,50.0714094` format. The ends parameter is optional when calculating full NxN matrix (all starts to all starts). Maximum number of ends is 100
- `routeType` (string, required) — Route type:
  - `car_fast` - Fastest route by car
  - `car_fast_traffic` - Fastest route by car with real-time traffic
  - `car_short` - Shortest route by car
  - `foot_fast` - Fastest walking route
  - `foot_hiking` - Hiking trail
  - `bike_road` - Road cycling
  - `bike_mountain` - Mountain biking
- `avoidToll` (boolean, optional) — Avoid toll roads (default: `false`)
- `lang` (string, optional) — Language code: `cs`, `de`, `el`, `en`, `es`, `fr`, `it`, `nl`, `pl`, `pt`, `ru`, `sk`, `tr`, `uk` (default: `cs`)

> Complete parameter list available in Swagger / OpenAPI above.

## Matrix Size Limits

The maximum size of the matrix is limited to 100:
- **NxM matrix**: `starts.length × ends.length ≤ 100` (e.g., 1×100, 10×10, 2×50)
- **NxN matrix**: `starts.length² ≤ 100` (when `ends` is not specified, calculates all starts to all starts)

**Distance Limit:**
- Maximum distance between two points is limited to 500 km by air

## Response Structure

The matrix routing endpoint returns a JSON object with:

- `matrix` (array of arrays) — Matrix of routing results, where each result contains:
  - `length` (integer) — Route length in meters (negative values indicate errors)
  - `duration` (integer) — Route duration in seconds (negative values indicate errors)

The matrix structure: `matrix[startIndex][endIndex]` corresponds to route from `starts[startIndex]` to `ends[endIndex]` (or `starts[endIndex]` in case of NxN matrix).

### Matrix Error Codes

When a route cannot be calculated, the service returns negative values:

- `-1` - General error
- `-2` - The route points are too far apart (exceeds 500 km)
- `-3` - The route could not be found (point too far from routing network)
- `-4` - Internal timeout of the service has been exceeded

## Examples

### cURL

```bash
# Calculate NxM matrix (2 starts to 2 ends)
curl "https://api.mapy.com/v1/routing/matrix-m?apikey=YOUR_API_KEY&starts=14.4378,50.0755;14.5,50.1&ends=16.6068,49.1951;16.7,49.2&routeType=car_fast"

# Calculate NxM matrix with exploded format
curl "https://api.mapy.com/v1/routing/matrix-m?apikey=YOUR_API_KEY&starts=14.4378,50.0755&starts=14.5,50.1&ends=16.6068,49.1951&ends=16.7,49.2&routeType=car_fast"

# Calculate NxN matrix (all starts to all starts, ends omitted)
curl "https://api.mapy.com/v1/routing/matrix-m?apikey=YOUR_API_KEY&starts=14.4378,50.0755;14.5,50.1;15.0,50.0&routeType=car_fast"

# Matrix with multiple points (up to matrix size limit of 100)
curl "https://api.mapy.com/v1/routing/matrix-m?apikey=YOUR_API_KEY&starts=14.4378,50.0755;14.5,50.1&ends=16.6068,49.1951&routeType=car_fast&avoidToll=true"
```

### Example Response

```json
{
  "matrix": [
    [
      {
        "length": 2351,
        "duration": 189
      },
      {
        "length": -3,
        "duration": -3
      }
    ],
    [
      {
        "length": 2456,
        "duration": 195
      },
      {
        "length": 1892,
        "duration": 152
      }
    ]
  ]
}
```

This represents:
- `matrix[0][0]`: Route from `starts[0]` to `ends[0]` = 2351 meters, 189 seconds
- `matrix[0][1]`: Route from `starts[0]` to `ends[1]` = Error (route not found)
- `matrix[1][0]`: Route from `starts[1]` to `ends[0]` = 2456 meters, 195 seconds
- `matrix[1][1]`: Route from `starts[1]` to `ends[1]` = 1892 meters, 152 seconds

## Common Errors and Limits

- **401 Unauthorized**: Invalid or missing API key
- **403 Forbidden**: API key doesn't have access to this resource
- **404 Not Found**: Route cannot be calculated
- **422 Validation Error**: Invalid parameters (missing required parameters, invalid coordinates, matrix size exceeds limit)

**Rate Limits:**
- Maximum 30 requests per second per API key

**Limitations:**
- Maximum matrix size: 100 (starts.length × ends.length ≤ 100 or starts.length² ≤ 100 for NxN)
- Maximum distance between points: 500 km by air
- Maximum number of starts: 100
- Maximum number of ends: 100

For detailed error responses and rate limits, see the [OpenAPI specification](https://api.mapy.com/v1/docs/routing/openapi.json) and [Getting Access](getting-access.md).

## Use Cases

- **Fleet Optimization**: Calculate distances from multiple vehicle locations to multiple destinations
- **Service Area Analysis**: Determine coverage areas for service providers
- **Multiple Delivery Planning**: Optimize routes for delivery services with multiple stops
- **Location Analysis**: Compare travel times from multiple origins to multiple destinations
- **Logistics Planning**: Calculate distance matrices for logistics optimization

## Related

- [Getting Access](getting-access.md)
- [Routing](routing.md)
- [Forward Geocoding](forward-geocoding.md)
- [URL Route](../url-mapy/route.md)
- [REST API Documentation](README.md)


