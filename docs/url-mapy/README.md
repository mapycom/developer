# URL Mapy.com Documentation

Mapy.com provides URL schemes that allow you to directly invoke specific functions such as map display, search, and route planning. These URLs can be used to open Mapy.com from your website or application, either in a web browser or in the Mapy.com mobile application.

## Key Features

- **No API key required** - These URLs are freely available
- **Deep linking** - Open directly to specific locations, searches, or routes
- **Cross-platform** - Works in web browsers and mobile apps
- **Simple integration** - Just construct the URL with appropriate parameters

## Use Cases

- Link from your website to show locations on Mapy.com
- Create "Get Directions" buttons in your app
- Share locations and routes via email or messaging
- Integrate with QR codes for physical locations
- Build location-based marketing campaigns

## Available URL Functions

### [Show Map](showmap.md)
Display a map centered at specific coordinates with an optional marker.

**Example:**
```
https://mapy.com/fnc/v1/showmap?mapset=basic&center=14.4378,50.0755&zoom=14&marker=true
```

### [Search](search.md)
Display search results for a specific query.

**Example:**
```
https://mapy.com/fnc/v1/search?query=restaurant&center=14.4378,50.0755&zoom=13
```

### [Route](route.md)
Display a planned route between specified points.

**Example:**
```
https://mapy.com/fnc/v1/route?start=14.4378,50.0755&end=16.6068,49.1951&routeType=car_fast
```

## Official Documentation

**Complete user documentation for all URL schemes is available at:**
https://developer.mapy.com/further-uses-of-mapycz/mapy-cz-url/

This official documentation includes:
- Detailed parameter descriptions
- Additional URL functions
- Best practices
- Integration examples
- Mobile app-specific features

## URL Structure

All Mapy.com URLs follow this general pattern:

```
https://mapy.com/fnc/v1/{function}?{parameters}
```

Where:
- `{function}` is the function name: `showmap`, `search`, or `route`
- `{parameters}` are URL-encoded query parameters specific to each function

## Common Parameters

Many URL functions share these common parameters:

- `mapset` - Map style: `basic`, `outdoor`, `winter`, `aerial`, `traffic`
- `center` - Map center coordinates: `lon,lat`
- `zoom` - Zoom level (1-19)

## Local Documentation Pages

- [Show Map (showmap)](showmap.md)
- [Search](search.md)
- [Route Planning (route)](route.md)

## Difference from REST API

Unlike the REST API, these URL schemes:
- Don't require an API key
- Don't return data programmatically
- Open the Mapy.com website or app instead
- Are designed for direct user interaction, not data integration

For programmatic access to mapping data, use the [REST API](../rest-api/README.md) instead.

