# Search

The `/search` URL function displays search results for a specified query on Mapy.com. Use this to link directly to search results for locations, businesses, or points of interest.

**Complete user documentation for Mapy.com URLs is available at:**  
https://developer.mapy.com/further-uses-of-mapycz/mapy-cz-url/

## URL Format

```
https://mapy.com/fnc/v1/search?{parameters}
```

## Parameters

- `query` (required) — Search query (e.g., "restaurant", "hotel", "Prague Castle")
- `center` (optional) — Map center for search results in format `lon,lat`
- `zoom` (optional) — Zoom level (1-19), default: 13
- `mapset` (optional) — Map style:
  - `basic` - Standard map (default)
  - `outdoor` - Hiking and outdoor activities
  - `winter` - Winter sports
  - `aerial` - Aerial/satellite imagery
  - `traffic` - Traffic map

## Examples

### Basic Search

Search for restaurants:

```
https://mapy.com/fnc/v1/search?query=restaurant
```

### Search Near Location

Find restaurants near Prague city center:

```
https://mapy.com/fnc/v1/search?query=restaurant&center=14.4378,50.0755&zoom=14
```

### Search with Specific Map Style

Find hiking trails on outdoor map:

```
https://mapy.com/fnc/v1/search?query=hiking+trail&mapset=outdoor&center=15.7392,50.7366&zoom=12
```

### Search for Specific Place

Show search results for a famous landmark:

```
https://mapy.com/fnc/v1/search?query=Prague+Castle&zoom=15
```

### Category Search

Search for hotels in a city:

```
https://mapy.com/fnc/v1/search?query=hotel&center=14.4378,50.0755&zoom=13
```

## Use Cases

- **Local Business Discovery**: Link to searches for nearby services
- **Tourism**: Help visitors find attractions, restaurants, hotels
- **Real Estate**: Show nearby amenities (schools, shops, parks)
- **Event Planning**: Find venues or services in specific areas
- **Travel Planning**: Pre-fill searches for users planning trips

## HTML Integration

### Simple Search Link

```html
<a href="https://mapy.com/fnc/v1/search?query=restaurant&center=14.4378,50.0755&zoom=14" 
   target="_blank">
  Find Restaurants Nearby
</a>
```

### Dynamic Search

```js
function searchNearby(query, lon, lat) {
  const encodedQuery = encodeURIComponent(query);
  const url = `https://mapy.com/fnc/v1/search?query=${encodedQuery}&center=${lon},${lat}&zoom=14`;
  window.open(url, '_blank');
}

// Usage
searchNearby('coffee shop', 14.4378, 50.0755);
```

### Search Form Integration

```html
<form onsubmit="searchOnMapy(event)">
  <input type="text" id="searchQuery" placeholder="What are you looking for?">
  <button type="submit">Search on Mapy.com</button>
</form>

<script>
function searchOnMapy(event) {
  event.preventDefault();
  const query = document.getElementById('searchQuery').value;
  const url = `https://mapy.com/fnc/v1/search?query=${encodeURIComponent(query)}`;
  window.open(url, '_blank');
}
</script>
```

## Query Encoding

Always URL-encode the query parameter to handle special characters and spaces:

```js
// Good
const query = 'café & restaurant';
const encoded = encodeURIComponent(query);
const url = `https://mapy.com/fnc/v1/search?query=${encoded}`;

// Result: query=caf%C3%A9%20%26%20restaurant
```

## Search Types

The search function can find:
- **Places**: Cities, streets, addresses
- **Businesses**: Restaurants, hotels, shops, services
- **Points of Interest**: Museums, parks, monuments, attractions
- **Categories**: Generic searches like "gas station", "pharmacy"
- **Natural Features**: Mountains, rivers, lakes

## Mobile App Support

When opened on a mobile device with the Mapy.com app installed, search URLs will open in the app, providing:
- Faster search results
- Integrated navigation options
- User reviews and ratings
- Contact information and opening hours

## Programmatic Alternative

For programmatic access to search and geocoding, use the [REST API Forward Geocoding](../rest-api/forward-geocoding.md) and [Reverse Geocoding](../rest-api/reverse-geocoding.md) endpoints which provide:
- JSON response format
- Structured data
- Autocomplete/suggestions
- API key authentication

## Related

- [Show Map](showmap.md)
- [Route Planning](route.md)
- [URL Mapy Documentation](README.md)
- [REST API Forward Geocoding](../rest-api/forward-geocoding.md) - For programmatic search and autocomplete
- [REST API Reverse Geocoding](../rest-api/reverse-geocoding.md) - For coordinates to address conversion

