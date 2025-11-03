# Route Planning (route)

The `/route` URL function displays a planned route between specified points on Mapy.com. Use this to provide direct "Get Directions" links from your website or application.

**Complete user documentation for Mapy.com URLs is available at:**  
https://developer.mapy.com/further-uses-of-mapycz/mapy-cz-url/

## URL Format

```
https://mapy.com/fnc/v1/route?{parameters}
```

## Parameters

- `start` (required) — Starting point in format `lon,lat`
- `end` (required) — Destination point in format `lon,lat`
- `routeType` (optional) — Route optimization type:
  - `car_fast` - Fastest route by car (default)
  - `car_short` - Shortest route by car
  - `car_fast_traffic` - Fastest with real-time traffic
  - `foot_fast` - Walking route
  - `foot_hiking` - Hiking trail
  - `bike_road` - Road cycling
  - `bike_mountain` - Mountain biking
- `waypoints` (optional) — Intermediate points in format `lon1,lat1;lon2,lat2;...` (max 15)
- `mapset` (optional) — Map style: `basic`, `outdoor`, `winter`, `aerial`, `traffic`
- `navigate` (optional) — Start navigation immediately in mobile app: `true` or `false`

## Examples

### Simple Car Route

Route from Prague to Brno:

```
https://mapy.com/fnc/v1/route?start=14.4378,50.0755&end=16.6068,49.1951&routeType=car_fast
```

### Route with Traffic

Fastest route considering current traffic:

```
https://mapy.com/fnc/v1/route?start=14.4378,50.0755&end=14.5,50.08&routeType=car_fast_traffic&mapset=traffic
```

### Walking Route

Walking directions in city center:

```
https://mapy.com/fnc/v1/route?start=14.4203,50.0875&end=14.4378,50.0755&routeType=foot_fast
```

### Hiking Route

Hiking trail with outdoor map:

```
https://mapy.com/fnc/v1/route?start=15.7392,50.7366&end=15.7500,50.7400&routeType=foot_hiking&mapset=outdoor
```

### Cycling Route

Bike route with waypoints:

```
https://mapy.com/fnc/v1/route?start=14.4,50.07&end=14.5,50.09&waypoints=14.45,50.08&routeType=bike_road
```

### Mobile Navigation

Open route and start navigation immediately:

```
https://mapy.com/fnc/v1/route?start=14.4378,50.0755&end=14.5,50.08&routeType=car_fast&navigate=true
```

## Use Cases

- **Business Locations**: "Get Directions" to your store or office
- **Event Planning**: Provide routes to event venues
- **Delivery Services**: Share delivery routes with customers
- **Tourism**: Guide visitors to attractions
- **Real Estate**: Show routes to properties from city center
- **Outdoor Activities**: Share hiking or cycling routes

## HTML Integration

### Simple "Get Directions" Link

```html
<a href="https://mapy.com/fnc/v1/route?start=14.4378,50.0755&end=14.5,50.08&routeType=car_fast" 
   target="_blank">
  Get Directions
</a>
```

### Dynamic Route Link

```js
function getDirections(fromLon, fromLat, toLon, toLat, type = 'car_fast') {
  const url = `https://mapy.com/fnc/v1/route?start=${fromLon},${fromLat}&end=${toLon},${toLat}&routeType=${type}`;
  window.open(url, '_blank');
}

// Usage - Route to business location
const businessLocation = { lon: 14.5, lat: 50.08 };
getDirections(14.4378, 50.0755, businessLocation.lon, businessLocation.lat);
```

### Route with Current Location

```js
// Get user's current location and plan route
function routeFromCurrentLocation(destLon, destLat) {
  if (navigator.geolocation) {
    navigator.geolocation.getCurrentPosition(position => {
      const start = `${position.coords.longitude},${position.coords.latitude}`;
      const end = `${destLon},${destLat}`;
      const url = `https://mapy.com/fnc/v1/route?start=${start}&end=${end}&routeType=car_fast`;
      window.open(url, '_blank');
    });
  }
}

// Usage
routeFromCurrentLocation(14.5, 50.08);
```

### Multiple Transport Options

```html
<div class="directions">
  <h3>Get Directions to Our Location</h3>
  <button onclick="getRoute('car_fast')">By Car</button>
  <button onclick="getRoute('foot_fast')">Walking</button>
  <button onclick="getRoute('bike_road')">By Bike</button>
</div>

<script>
function getRoute(type) {
  const dest = { lon: 14.5, lat: 50.08 };
  const url = `https://mapy.com/fnc/v1/route?end=${dest.lon},${dest.lat}&routeType=${type}`;
  window.open(url, '_blank');
  // Note: start is omitted - Mapy.com will prompt user to enter starting location
}
</script>
```

## Route Types Explained

### Car Routes
- `car_fast` - Optimized for time, may use highways
- `car_short` - Optimized for distance
- `car_fast_traffic` - Uses real-time traffic data for best current route

### Walking Routes
- `foot_fast` - Direct walking routes on streets and paths
- `foot_hiking` - Prefers hiking trails and scenic routes

### Cycling Routes
- `bike_road` - Road cycling, prefers bike lanes and paved roads
- `bike_mountain` - Mountain biking, includes trails and unpaved paths

## Waypoints

Add up to 15 intermediate points:

```
waypoints=14.45,50.08;14.47,50.085;14.48,50.09
```

Waypoints are visited in the order specified.

## Coordinate Format

- Use WGS84 decimal coordinates
- Format: `longitude,latitude`
- Example: `14.4378,50.0755` (Prague)

## Mobile App Integration

When the URL is opened on a mobile device with the Mapy.com app installed:
- Route is displayed in the app
- If `navigate=true`, navigation starts immediately
- Access to turn-by-turn directions
- Real-time position tracking
- Voice guidance

## Programmatic Alternative

For programmatic route calculation with JSON responses, use the [REST API Routing](../rest-api/routing.md) and [Matrix Routing](../rest-api/matrix-routing.md) endpoints which provide:
- Route geometry (coordinates)
- Distance and duration
- Turn-by-turn instructions
- Elevation profile
- Matrix routing for multiple origins/destinations

## Related

- [Show Map](showmap.md)
- [Search](search.md)
- [URL Mapy Documentation](README.md)
- [REST API Routing](../rest-api/routing.md) - For programmatic route calculation
- [REST API Matrix Routing](../rest-api/matrix-routing.md) - For distance/time matrices

