# Time Zones

The Time Zones API provides time zone information, local time, and UTC offsets for specific geographic coordinates or IANA time zone codes. Use this to display local times for locations worldwide, convert between time zones, or get detailed time zone information.

## Quick Links

- Textual documentation: https://developer.mapy.com/en/rest-api-mapy-cz/function/api-for-time-zones/
- Swagger UI: https://api.mapy.com/v1/docs/timezone/
- OpenAPI: https://api.mapy.com/v1/docs/timezone/openapi.json

## Endpoints

- `GET /v1/timezone/list-timezones` - Get all IANA timezone names
- `GET /v1/timezone/timezone` - Get timezone information by IANA code
- `GET /v1/timezone/coordinate` - Get timezone information by coordinates

## Key Parameters (Selection)

### List Timezones

- `apikey` (string) — Your API key for authentication ([How to get API key](getting-access.md)) (alternative: X-Mapy-Api-Key header)
- `lang` (string, optional) — Language code (default: cs). Supported values: cs, de, el, en, es, fr, it, nl, pl, pt, ru, sk, tr, uk

### Get Timezone by IANA Code

- `apikey` (string) — Your API key for authentication ([How to get API key](getting-access.md)) (alternative: X-Mapy-Api-Key header)
- `timezone` (string, required) — IANA timezone name (e.g., "Europe/Prague")
- `lang` (string, optional) — Language code (default: cs)

### Get Timezone by Coordinates

- `apikey` (string) — Your API key for authentication ([How to get API key](getting-access.md)) (alternative: X-Mapy-Api-Key header)
- `lon` (number, required) — Longitude coordinate
- `lat` (number, required) — Latitude coordinate
- `lang` (string, optional) — Language code (default: cs)

> Complete parameter list available in Swagger / OpenAPI above.

## Response Format

### Timezone Information

Both `timezone` and `coordinate` endpoints return detailed timezone information:

```json
{
  "timezone": {
    "timezoneName": "Europe/Prague",
    "currentTimeAbbreviation": "CEST",
    "standardTimeAbbreviation": "CET",
    "currentLocalTime": "2024-10-16T15:03:51.248",
    "currentUtcTime": "2024-10-16T13:03:51.248Z",
    "currentUtcOffsetSeconds": 7200,
    "standardUtcOffsetSeconds": 3600,
    "hasDst": true,
    "isDstActive": true,
    "dstInfo": {
      "dstAbbreviation": "CEST",
      "dstStartUtcTime": "2024-03-31T01:00:00.000Z",
      "dstStartLocalTime": "2024-03-31T03:00:00.000",
      "dstEndUtcTime": "2024-10-27T01:00:00.000Z",
      "dstEndLocalTime": "2024-10-27T02:00:00.000",
      "dstOffsetSeconds": 3600,
      "dstDurationSeconds": 18144000
    }
  }
}
```

### List Timezones

```json
{
  "timezones": [
    "Africa/Abidjan",
    "Africa/Accra",
    "Europe/Prague",
    "America/New_York"
  ]
}
```

## Examples

### cURL

```bash
# Get all IANA timezone names
curl "https://api.mapy.com/v1/timezone/list-timezones?apikey=YOUR_API_KEY"

# Get timezone information by IANA code
curl "https://api.mapy.com/v1/timezone/timezone?apikey=YOUR_API_KEY&timezone=Europe/Prague"

# Get timezone information by coordinates
curl "https://api.mapy.com/v1/timezone/coordinate?apikey=YOUR_API_KEY&lon=14.4378&lat=50.0755"
```

## Response Fields

| Field | Description |
|-------|-------------|
| `timezoneName` | IANA timezone name (e.g., "Europe/Prague") |
| `currentTimeAbbreviation` | Current timezone abbreviation (e.g., "CEST") |
| `standardTimeAbbreviation` | Standard time abbreviation (e.g., "CET") |
| `currentLocalTime` | Current local time in ISO 8601 format |
| `currentUtcTime` | Current UTC time in ISO 8601 format |
| `currentUtcOffsetSeconds` | Current UTC offset in seconds |
| `standardUtcOffsetSeconds` | Standard UTC offset in seconds |
| `hasDst` | Whether timezone uses daylight saving time |
| `isDstActive` | Whether daylight saving time is currently active |
| `dstInfo.dstAbbreviation` | Daylight saving time abbreviation |
| `dstInfo.dstStartUtcTime` | UTC time when DST starts |
| `dstInfo.dstStartLocalTime` | Local time when DST starts |
| `dstInfo.dstEndUtcTime` | UTC time when DST ends |
| `dstInfo.dstEndLocalTime` | Local time when DST ends |
| `dstInfo.dstOffsetSeconds` | DST offset from standard time in seconds |
| `dstInfo.dstDurationSeconds` | DST duration in seconds |

## Use Cases

- **Event Scheduling**: Display event times in local time zones
- **Travel Applications**: Show local time at destinations
- **Logistics**: Calculate delivery times across time zones
- **Meeting Planners**: Find suitable times across multiple time zones
- **Global Activities**: Display local time and timezone information for activities worldwide
- **Time Conversion**: Convert times between different time zones
- **Current Time**: Get current time in any timezone

## Common Errors and Limits

- **401 Unauthorized**: Invalid or missing API key
- **400 Bad Request**: Invalid coordinates or timezone name
- **403 Forbidden**: API key doesn't have access to this resource
- **422 Validation Error**: Invalid parameter format
- **429 Too Many Requests**: Rate limit exceeded

**Limitations:**
- Maximum rate limit: 300 requests per second per API key
- Timezone information is based on IANA timezone database

For detailed error responses and rate limits, see the [OpenAPI specification](https://api.mapy.com/v1/docs/timezone/openapi.json) and [Getting Access](getting-access.md).

## Related

- [Getting Access](getting-access.md)
- [Forward Geocoding](forward-geocoding.md)
- [Reverse Geocoding](reverse-geocoding.md)
- [REST API Documentation](README.md)

