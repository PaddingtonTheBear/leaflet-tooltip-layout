# leaflet-tooltip-layout

This plugin is designed to avoid tooltip overlapping and make users find out the relationship between each tooltip and marker easily. It is based on [Force-Directed Drawing Algorithms](http://cs.brown.edu/people/rtamassi/gdhandbook/chapters/force-directed.pdf) in the chapter 12 of the book Handbook of Graph Drawing and Visualization written by Stephen G. Kobourov.

[Here is the demo](https://zijingpeng.github.io/overlapping-avoided-tooltip/)

**FORKED** From the original, https://github.com/ZijingPeng/leaflet-tooltip-layout, with a reworked TypeScript / Rxjs version of the main tooltip layout functionality. 

- Changed the flow of using the plugin so that you first create the LeafletTooltipLayout class
  - Added an option to the constructor, `{useInternalEvents: boolean}` to disable the internal map events  if you plan on adding your own for determining tooltip visibility; improves performance
- Changed how markers are added and removed from TooltipLayout
  - `resetMarker` now takes an optional "show" parameter which allows you to control if a marker is added to the map, useful if you are limiting the number of markers on the map for performance and visibility reasons
  - Added a `removeAllMakers` method which will remove all polylines, set the visibility of all tooltips to hidden, and empty the markerList array
  - `setMarkers(arr)` method will fire the resetMarker method for every marker in the `arr` array
- Utilizes *RxJS* for `debounceTime` capability to improve performance on map
  - Waits `333ms` before firing `redrawLines` or `removeAllPolyline` and will cancel previous requests until events are stopped firing. Improves performance so that these expensive redrawing methods aren't constantly fired on map move / zooms.
  - Internally uses `Subjects` for `redrawLines` and `removeAllPolyline` to call internal versions of these methods.


## Installation

You can just copy `./lib/leaflet.tooltip.layout.ts` to your project and rename it to what you want, assuming you have TypeScript transpilation in your project (or use Angular).


## Getting Started

### *ES6 / TS*

```js
import 'leaflet';
import { LeafletTooltipLayout } from './leaflet.tooltip.layout';
```

## Example

```ts
const map = L.map('mapId', mapOptions);

const tooltipLayout = new LeafletTooltipLayout({useInternalEvents: true});
tooltipLayout.initialize(map);

// Generate Markers 
const markers = [];
for (let i = 0; i < 100; i++) {
  const marker = L.marker({})); // not fully implemented in this example, but it's standard Leaflet marker creation into an array.
  markers.push(marker);
  marker.addTo(map);
}

// Add all markers to map
tooltipLayout.setMarkers(markers);

// Hide all markers / remove from layout
tooltipLayout.removeAllMarkers();

// Toggle a marker's visibility
const marker = markers[0];

tooltipLayout.resetMarker(marker); // shows marker
tooltipLayout.resetMarker(marker, false); // hides marker
```

## API Reference

### OLD REFERENCES

1. `L.tooltipLayout.resetMarker(marker)`

   Create the marker, bind tooltip to the marker, then use this function.

   Usage example:

   ```js
   var marker = L.marker(coord, {
     icon: icon
   }).addTo(map);
   marker.bindTooltip('Hello world!');
   L.tooltipLayout.resetMarker(marker);
   ```

2. `L.tooltipLayout.getMarkers()`

   Get the all the markers in this layout.

   Usage example:

   ```js
   var markerList = getMarkers();
   for (i = 0; i < markerList.length; i++) {
     marker = markerList[i];
     tooltip = marker.getTooltip();
     marker._icon.addEventListener('mouseover', function (){
       // your code
     });
     tooltip._container.addEventListener('mouseover', function (){
       // your code
     });
   }
   ```

3. `L.tooltipLayout.getLine(marker)`

   Get the line between one marker and its tooltip.

4. `L.tooltipLayout.initialize(map, onPolylineCreated)`

   After adding all the markers and tooltips, use this function to create the layout.

   `onPolylineCreated` is a callback function that allows you to define the style of the line between markers and tooltips, if you want the default one, let this parameter `null`. 

   Or you can define the function like this:

   ```js
   function onPolylineCreated(ply) {
     ply.setStyle({
       color: '#90A4AE'
     })
   }
   ```

## License

MIT License

