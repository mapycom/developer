# Forward Geocoding

The Forward Geocoding API converts textual location queries into geographic coordinates and structured address information. It searches for entities (addresses) based on a text query and returns coordinates and additional data, such as the surrounding regional hierarchy (e.g., city, district, country).

## Quick Links

- Textual documentation: https://developer.mapy.com/en/rest-api-mapy-cz/function/geocoding/
- Swagger UI: https://api.mapy.com/v1/docs/geocode/
- OpenAPI: https://api.mapy.com/v1/docs/geocode/openapi.json

## Endpoints

- `GET /v1/geocode` - Forward geocoding (address → coordinates)
- `GET /v1/suggest` - Autocomplete suggestions for address search

## Key Parameters (Selection)

### Geocode Endpoint

- `apikey` (string) — Your API key for authentication ([How to get API key](getting-access.md)) (query parameter or X-Mapy-Api-Key header)
- `query` (string) — Geographic entity name or coordinates to resolve
- `limit` (integer) — Maximum number of results (default: 5, upper limit: 15)
- `lang` (string) — Preferred language for result entity names: `cs`, `de`, `el`, `en`, `es`, `fr`, `it`, `nl`, `pl`, `pt`, `ru`, `sk`, `tr`, `uk` (default: `cs`)
- `type` (array) — Filter by entity types (default: `["regional","poi"]`). Available types: `regional`, `regional.country`, `regional.region`, `regional.municipality`, `regional.municipality_part`, `regional.street`, `regional.address`, `poi`, `coordinate`
- `locality` (array) — Return results only from these localities (locality names, country codes, or BOX format)
- `preferBBox` (array) — Prefer results from this bounding box (not a filter). Format: `[minLon,minLat,maxLon,maxLat]`
- `preferNear` (array) — Prefer results near this position (not a filter). Format: `[lon,lat]`
- `preferNearPrecision` (number) — Precision of preferNear parameter in meters

### Suggest Endpoint

- `apikey` (string) — Your API key for authentication ([How to get API key](getting-access.md)) (query parameter or X-Mapy-Api-Key header)
- `query` (string) — Geographic entity name or coordinates to resolve (max 150 characters)
- `limit` (integer) — Maximum number of results (default: 5, upper limit: 15)
- `lang` (string) — Preferred language for result entity names: `cs`, `de`, `el`, `en`, `es`, `fr`, `it`, `nl`, `pl`, `pt`, `ru`, `sk`, `tr`, `uk` (default: `cs`)
- `type` (array) — Filter by entity types (default: `["regional","poi"]`). Available types: `regional`, `regional.country`, `regional.region`, `regional.municipality`, `regional.municipality_part`, `regional.street`, `regional.address`, `poi`, `coordinate`
- `locality` (array) — Return results only from these localities (locality names, country codes, or BOX format)
- `preferBBox` (array) — Prefer results from this bounding box (not a filter). Format: `[minLon,minLat,maxLon,maxLat]`
- `preferNear` (array) — Prefer results near this position (not a filter). Format: `[lon,lat]`
- `preferNearPrecision` (number) — Precision of preferNear parameter in meters

> Complete parameter list available in Swagger / OpenAPI above.

## Response Structure

Both endpoints return a JSON object with the following structure:

- `items` (array) — List of matching geographical entities, each containing:
  - `name` (string) — Name of the entity (e.g., "Týnská ulička 610/7")
  - `label` (string) — Type label (e.g., "Adresa", "Město")
  - `position` (object) — Coordinates with `lon` and `lat` properties
  - `bbox` (array) — Bounding box as `[minLon, minLat, maxLon, maxLat]`
  - `type` (string) — Entity type (e.g., "regional.address", "regional.municipality", "poi")
  - `location` (string) — Short locality label (e.g., "Praha 1 - Staré Město, Česko")
  - `regionalStructure` (array) — Ordered list of parent administrative entities (smallest first), each with `name`, `type`, and optionally `isoCode` for countries
  - `zip` (string, optional) — Postal code (available only for some addresses)
- `locality` (array, optional) — Resolved bounding boxes for localities used in the `locality` parameter

## Examples

### cURL

```bash
# Find coordinates for an address
curl "https://api.mapy.com/v1/geocode?apikey=YOUR_API_KEY&query=Prague+Castle&limit=5"

# Get autocomplete suggestions
curl "https://api.mapy.com/v1/suggest?apikey=YOUR_API_KEY&query=Prag&limit=10"

# Search with specific entity types
curl "https://api.mapy.com/v1/geocode?apikey=YOUR_API_KEY&query=Praha&type=regional.municipality&limit=5"

# Search within specific locality
curl "https://api.mapy.com/v1/geocode?apikey=YOUR_API_KEY&query=Hlavní+náměstí&locality=Praha&limit=5"

# Search with bounding box preference
curl "https://api.mapy.com/v1/geocode?apikey=YOUR_API_KEY&query=restaurant&preferBBox=14.4,50.0,14.5,50.1&limit=5"

# Search near specific position
curl "https://api.mapy.com/v1/geocode?apikey=YOUR_API_KEY&query=hotel&preferNear=14.4378,50.0755&preferNearPrecision=1000&limit=5"
```

### HTML Example with Autocomplete

This example shows integration with the external autocomplete component [autoComplete.js](https://tarekraafat.github.io/autoComplete.js/):

```html
<!DOCTYPE html>
<html lang="en">
<head>
    <link rel="stylesheet" href="https://cdn.jsdelivr.net/npm/@tarekraafat/autocomplete.js@10.2.7/dist/css/autoComplete.02.min.css">
</head>
<body>
    <div class="autoComplete_wrapper">
        <input id="autoComplete" type="search" dir="ltr" spellcheck=false autocorrect="off" autocomplete="off" autocapitalize="off">
    </div>

    <script src="https://cdn.jsdelivr.net/npm/@tarekraafat/autocomplete.js@10.2.7/dist/autoComplete.min.js"></script>
    <script>
        const API_KEY = 'YOUR_API_KEY';
        const inputElem = document.querySelector("#autoComplete");
        // cache - [key: query] = suggest items
        const queryCache = {};
        // get items by query
        const getItems = async(query) => {
          if (queryCache[query]) {
            return queryCache[query];
          }
          
          try {
            const fetchData = await fetch(`https://api.mapy.cz/v1/suggest?lang=cs&limit=5&type=regional.address&apikey=${API_KEY}&query=${query}`);
            const jsonData = await fetchData.json();
            // map values to { value, data }
            const items = jsonData.items.map(item => ({
              value: item.name,
              data: item,
            }));
            
            // save to cache
            queryCache[query] = items;
            
            return items;
          } catch (exc) {
            return [];
          }
        };

        const autoCompleteJS = new autoComplete({
          selector: () => inputElem,
          placeHolder: "Enter your address...",
          searchEngine: (query, record) => `<mark>${record}</mark>`,
          data: {
            keys: ["value"],
            src: async(query) => {
              // get items for current query
              const items = await getItems(query);
              
              // cache hit? - there is a problem, because this provider needs to get items
              // for each query and cannot handle different timeouts for different query.
              // if previous query was completed - it's already in the cache, and some
              // old query is completed, we test it againts current query and returns correct items.
              if (queryCache[inputElem.value]) {
                return queryCache[inputElem.value];
              }
              
              return items;
            },
            cache: false,
          },
          resultItem: {
            element: (item, data) => {
              const itemData = data.value.data;
              const desc = document.createElement("div");
              
              desc.style = "overflow: hidden; white-space: nowrap; text-overflow: ellipsis;";
              desc.innerHTML = `${itemData.label}, ${itemData.location}`;
              item.append(desc);
            },
            highlight: true
          },
          resultsList: {
            element: (list, data) => {
              list.style.maxHeight = "max-content";
              list.style.overflow = "hidden";
            
              if (!data.results.length) {
                const message = document.createElement("div");
                
                message.setAttribute("class", "no_result");
                message.style = "padding: 5px";
                message.innerHTML = `<span>Found No Results for "${data.query}"</span>`;
                list.prepend(message);
              } else {
                const logoHolder = document.createElement("div");
                const text = document.createElement("span");
                const img = new Image();
                
                logoHolder.style = "padding: 5px; display: flex; align-items: center; justify-content: end; gap: 5px; font-size: 12px;";
                text.textContent = "Powered by";
                img.src = "https://api.mapy.cz/img/api/logo-small.svg";
                img.style = "width: 60px";
                logoHolder.append(text, img);
                list.append(logoHolder);
              }
            },
            noResults: true,
          },
        });
        inputElem.addEventListener("selection", event => {
            // "event.detail" carries the autoComplete.js "feedback" object
            // saved data from mapping
            const origData = event.detail.selection.value.data;
            // data to debug
            console.log(origData);
            inputElem.value = origData.name;
        });
    </script>
</body>
</html>
```

## Use Cases

- **Address Search**: Convert street addresses to coordinates
- **Place Search**: Find coordinates for cities, landmarks, and POIs
- **Autocomplete**: Provide search suggestions as users type
- **Location Services**: Enable location-based features in applications
- **Mapping Applications**: Display search results on maps

## Common Errors and Limits

- **422 Validation Error**: Invalid parameters (missing required parameters, invalid coordinates, invalid parameter values)
- **500 Internal Server Error**: Server-side error

**Rate Limits:**
- Forward Geocoding (geocode): Maximum 100 requests per second per API key
- Suggest: Maximum 100 requests per second per API key

For detailed error responses and rate limits, see the [OpenAPI specification](https://api.mapy.com/v1/docs/geocode/openapi.json) and [Getting Access](getting-access.md).

## Related

- [Getting Access](getting-access.md)
- [Reverse Geocoding](reverse-geocoding.md)
- [Routing](routing.md)
- [Matrix Routing](matrix-routing.md)
- [URL Search](../url-mapy/search.md)
- [REST API Documentation](README.md)


