# JS Panorama

JS Panorama is a JavaScript component that provides users with an experience similar to Mapy.com Panorama. It enables free navigation and movement within the panoramic view directly in your application.

## Quick Links

- Official documentation: https://developer.mapy.com/js-panorama-2/
- Panorama tutorials: https://developer.mapy.com/rest-api-mapy-cz/tutorials/js-panorama/

## Overview

Panorama on Mapy.com is a feature similar to Street View that allows you to explore most streets in the Czech Republic and look around in all directions.

For developers, there are currently two ways to use Panorama:

- **JS Panorama** (described in this chapter) - A JavaScript component that provides your users with an experience similar to Mapy.com. It enables free navigation and movement within the panoramic view directly in your application.
- **Static Panorama** - A REST API that returns a static image of the view from a selected location. You can simply embed it using `<img src="...">`, specifying the location and direction in the parameters. The image is fixed and cannot be rotated further. Read more in the [Static Panorama API](../rest-api/static-panorama.md) chapter.

## Getting Started

To use the panorama component, add this script to the header of your HTML page:

```html
<script type="text/javascript" src='https://api.mapy.cz/js/panorama/v1/panorama.js'></script>
```

With this script, you have access to the `Panorama` class, which exposes functions for working with panoramas.

## Panorama Controls

The user can look around in the panorama using:
- **Mouse**: Click and drag to rotate the view
- **Keyboard**: Arrow keys left/right or "a"/"d" keys

If navigation between images is enabled (`showNavigation: true`), the user can move using:
- **Mouse**: Click navigation arrows or areas of other images
- **Keyboard**: Arrow keys up/down or "w"/"s" keys

## Panorama Data Consumption

A panorama (one sphere) is not downloaded as one large photo, but is composed of individual panorama tiles. The usual size of a panorama is 16×8 tiles.

**The JS viewer automatically manages downloading the necessary tiles**. It does not download the entire sphere, but only those tiles that are in the displayed view.

Panorama usage (in tiles) is then charged according to the REST API price list.

## API Reference

### panoramaFromPosition()

This function is used for displaying the panorama at specific coordinates. It searches for the nearest panorama to the given coordinates within a specified radius.

#### Syntax

```js
const panoData = await Panorama.panoramaFromPosition({
  parent: HTMLElement,
  lon: number,
  lat: number,
  apiKey: string,
  // Optional parameters
  radius?: number,
  yaw?: "auto" | "point" | number,
  pitch?: number,
  fov?: number,
  showNavigation?: boolean,
  lang?: string,
  hideErrors?: boolean
});
```

#### Parameters

| Parameter | Type | Required | Description |
|-----------|------|----------|-------------|
| `parent` | HTMLElement | Yes | Parent HTML element for attaching the panorama |
| `lon` | number | Yes | WGS84 longitude coordinate around which the nearest panorama is searched |
| `lat` | number | Yes | WGS84 latitude coordinate around which the nearest panorama is searched |
| `apiKey` | string | Yes | API key for REST API |
| `radius` | number | No | Maximum radius for finding the panorama in meters (default: 50 m) |
| `yaw` | string \| number | No | Orientation relative to north. Possible values:<br>- `"auto"` (default) - Orientation follows the direction of the panorama<br>- `"point"` - Orientation towards the input coordinates<br>- Number - Custom value within the range 0 - 2π (0 = north, in radians) |
| `pitch` | number | No | Tilt (- up, + down). Range: ±π (default: 0, in radians) |
| `fov` | number | No | Horizontal field of view (essentially zoom). Range: π/2 - π/20 (default: 1.2, in radians) |
| `showNavigation` | boolean | No | Enables or disables the option to move to neighboring snapshots (default: false) |
| `lang` | string | No | Language (used for tooltips, error messages, etc.). Supported values: cs, de, el, en, es, fr, it, nl, pl, pt, ru, sk, tr, uk (default: cs) |
| `hideErrors` | boolean | No | Hide errors output (default: false) |

#### Return Value

The function returns a Promise that resolves to an object with the following structure:

```typescript
interface ICreatePanoFromPositionOutput {
  /** Panorama meta information */
  info?: {
    /** Pano wgs84 longitude coordinate */
    lon: number;
    /** Pano wgs84 latitude coordinate */
    lat: number;
    /** Create date YYYY-MM-DD hh:mm:ss */
    date: string;
  };
  /** Error message */
  error: string;
  /** Error code */
  errorCode: "NONE" | "PANORAMA_NOT_FOUND" | "MISSING_API_KEY" | "WRONG_API_KEY";
  /** Add listener */
  addListener: (name: string, callback: (data: any) => void) => void;
  /** Remove listener */
  removeListener: (name: string, callback: (data: any) => void) => void;
  /** Get panorama camera */
  getCamera: () => ICamera;
  /** Set panorama camera */
  setCamera: (camera: ICamera) => void;
  /** Destroy panorama */
  destroy: () => void;
}

interface ICamera {
  yaw: number;
  pitch: number;
  fov: number;
}
```

#### Methods

- **`destroy()`** - Removes the panorama from the DOM
- **`addListener(name, callback)`** - Add event listener for panorama events
  - `"pano-view"` - Triggered when the view changes
  - `"pano-place"` - Triggered when the panorama moves to another location
- **`removeListener(name, callback)`** - Remove event listener
- **`getCamera()`** - Get current camera settings (yaw, pitch, fov)
- **`setCamera(camera)`** - Set camera settings programmatically

### panoramaExists()

This function checks if a panorama is available at the given location.

#### Syntax

```js
const result = await Panorama.panoramaExists({
  lon: number,
  lat: number,
  apiKey: string,
  radius?: number
});
```

#### Parameters

| Parameter | Type | Required | Description |
|-----------|------|----------|-------------|
| `lon` | number | Yes | WGS84 longitude coordinate |
| `lat` | number | Yes | WGS84 latitude coordinate |
| `apiKey` | string | Yes | API key for REST API |
| `radius` | number | No | Maximum radius for finding the panorama in meters (default: 50 m) |

#### Return Value

Returns a Promise that resolves to an object with:

```typescript
interface IPanoramaExistsOutput {
  /** Panorama meta information */
  info?: {
    lon: number;
    lat: number;
    date: string;
  };
  /** Panorama exists? */
  exists: boolean;
}
```

## Examples

### Basic Usage

```html
<!DOCTYPE html>
<html>
<head>
  <meta charset="utf-8" />
  <title>JS Panorama Example</title>
  <script type="text/javascript" src='https://api.mapy.cz/js/panorama/v1/panorama.js'></script>
</head>
<body>
  <div id="panoContainer" style="height: 500px;"></div>
  
  <script>
    async function loadPanorama() {
      const container = document.getElementById('panoContainer');
      
      const panoData = await Panorama.panoramaFromPosition({
        parent: container,
        lon: 16.6080370,
        lat: 49.1949758,
        apiKey: 'YOUR_API_KEY'
      });
      
      if (panoData.errorCode !== 'NONE') {
        console.error('Error:', panoData.error);
      }
    }
    
    // Wait for script to load
    window.addEventListener('load', loadPanorama);
  </script>
</body>
</html>
```

### With Custom View Settings

```js
const panoData = await Panorama.panoramaFromPosition({
  parent: document.getElementById('panoContainer'),
  lon: 16.6080370,
  lat: 49.1949758,
  apiKey: 'YOUR_API_KEY',
  yaw: 5.43,              // Custom orientation
  pitch: Math.PI / 6,     // Tilted up
  fov: Math.PI / 1,       // Wide field of view
  showNavigation: true    // Enable navigation to adjacent panoramas
});
```

### Event Listening

```js
// Listen for view changes
panoData.addListener("pano-view", (viewData) => {
  console.log("View changed:", viewData);
  const camera = panoData.getCamera();
  console.log("Current camera:", camera);
});

// Listen for location changes
panoData.addListener("pano-place", (placeData) => {
  console.log("Location changed:", placeData);
  console.log("New coordinates:", placeData.info.lon, placeData.info.lat);
});
```

### Programmatic Camera Control

```js
// Get current camera settings
const camera = panoData.getCamera();
console.log("Current yaw:", camera.yaw);
console.log("Current pitch:", camera.pitch);
console.log("Current fov:", camera.fov);

// Set new camera position
panoData.setCamera({
  yaw: Math.PI / 2,      // Look east
  pitch: 0,              // Horizontal
  fov: 1.2               // Default FOV
});
```

### Check if Panorama Exists

```js
const result = await Panorama.panoramaExists({
  lon: 16.6080370,
  lat: 49.1949758,
  apiKey: 'YOUR_API_KEY',
  radius: 100  // Search within 100 meters
});

if (result.exists) {
  console.log("Panorama found at:", result.info.lon, result.info.lat);
  console.log("Date:", result.info.date);
} else {
  console.log("No panorama found in the specified area");
}
```

### Cleanup

```js
// Remove panorama when done
panoData.destroy();
```

## Panorama Coverage

The most recent Panorama capturing on Mapy.com took place in 2021–2023, when the entire Czech Republic was gradually updated. Approximately one third of the territory was captured each year, and every year, 21 selected cities—including Prague, Brno, and Ostrava—were repeatedly recaptured.

Panoramas are primarily available for locations in the Czech Republic and selected other areas.

## Common Errors

- **`PANORAMA_NOT_FOUND`** - No panorama available at the specified location within the search radius
- **`MISSING_API_KEY`** - API key parameter was not provided
- **`WRONG_API_KEY`** - Invalid API key

## Related

- [Getting Access](../rest-api/getting-access.md)
- [Static Panorama API](../rest-api/static-panorama.md)
- [REST API Documentation](../rest-api/README.md)


