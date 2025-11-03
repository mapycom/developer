# Show Map (showmap)

The `/showmap` URL function displays a map at specified coordinates with an optional marker. Use this to link directly to locations on Mapy.com from your website or application.

**Complete user documentation for Mapy.com URLs is available at:**  
https://developer.mapy.com/further-uses-of-mapycz/mapy-cz-url/

## URL Format

```
https://mapy.com/fnc/v1/showmap?{parameters}
```

## Parameters

- `center` (required) — Map center in format `lon,lat` (e.g., `14.4378,50.0755`)
- `zoom` (optional) — Zoom level (1-19), default: 13
- `marker` (optional) — Show marker at center: `true` or `false`, default: `false`
- `mapset` (optional) — Map style:
  - `basic` - Standard map (default)
  - `outdoor` - Hiking and outdoor activities
  - `winter` - Winter sports
  - `aerial` - Aerial/satellite imagery
  - `traffic` - Traffic map with current conditions

## Examples

### Basic Location with Marker

Show Prague Castle with a marker:

```
https://mapy.com/fnc/v1/showmap?center=14.4003,50.0907&zoom=16&marker=true
```

### Outdoor Map

Show a hiking area with outdoor map style:

```
https://mapy.com/fnc/v1/showmap?mapset=outdoor&center=15.7392,50.7366&zoom=14&marker=true
```

### Aerial View

Show location with satellite imagery:

```
https://mapy.com/fnc/v1/showmap?mapset=aerial&center=14.4378,50.0755&zoom=15
```

### Traffic Map

Show current traffic conditions in a city:

```
https://mapy.com/fnc/v1/showmap?mapset=traffic&center=14.4378,50.0755&zoom=13
```

## Use Cases

- **Business Locations**: Link to your store or office location
- **Event Venues**: Show event locations on your website
- **Real Estate**: Display property locations
- **Tourism**: Highlight points of interest
- **Emergency Information**: Show assembly points or facilities

## HTML Integration

### Simple Link

```html
<a href="https://mapy.com/fnc/v1/showmap?center=14.4378,50.0755&zoom=15&marker=true" 
   target="_blank">
  View on Mapy.com
</a>
```

### Button Example

```html
<button onclick="window.open('https://mapy.com/fnc/v1/showmap?center=14.4378,50.0755&zoom=15&marker=true', '_blank')">
  Show Location
</button>
```

### Dynamic URL from Coordinates

```js
function showOnMap(lon, lat, label) {
  const url = `https://mapy.com/fnc/v1/showmap?center=${lon},${lat}&zoom=15&marker=true`;
  window.open(url, '_blank');
}

// Usage
showOnMap(14.4378, 50.0755, 'Prague');
```

## Coordinate Format

Coordinates must be in WGS84 format:
- Longitude first, then latitude
- Use decimal degrees (not degrees/minutes/seconds)
- Example: `14.4378,50.0755` (Prague)

To convert from latitude/longitude order used in some systems:
```js
const lat = 50.0755;
const lon = 14.4378;
const mapyFormat = `${lon},${lat}`;  // lon,lat for Mapy.com
```

## Mobile App Support

When opened on a mobile device with the Mapy.com app installed, these URLs will automatically open in the app, providing a better user experience with:
- Faster loading
- Native navigation features
- Offline map support
- GPS integration

## Related

- [Search](search.md)
- [Route Planning](route.md)
- [URL Mapy Documentation](README.md)
- [REST API Forward Geocoding](../rest-api/forward-geocoding.md) - For converting addresses to coordinates programmatically
- [REST API Reverse Geocoding](../rest-api/reverse-geocoding.md) - For converting coordinates to addresses programmatically

