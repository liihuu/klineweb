# Graphic mark

## Default graphic mark
`horizontalRayLine`, `horizontalSegment`, `horizontalStraightLine`, `verticalRayLine`, `verticalSegment`, `verticalStraightLine`, `rayLine`, `segment`, `horizontalSegment`, `priceLine`, `priceChannelLine`, `parallelStraightLine`, `fibonacciLine`

## Custom graphic mark
To complete a custom graphic mark, you only need to generate a graphic mark information, and then add it globally through `extension.addGraphicMark`, or add it for a single graph instance through the chart instance method `addCustomGraphicMark`. Adding to the chart can be used like the built-in graphic markers.
### Property description
#### Graphic mark information
```js
{
  // Name, a required field, as the unique identifier of the graphic mark
  name:'xxx',

  // How many steps are required to complete the drawing, the required fields
  totalStep: 3,

  // Check whether the mouse point is on the graph, it is a callback method, a required field
  // key The key given when creating the data source
  // type graphics type
  // coordinates graphic coordinate information
  // mouseCoordinate current mouse coordinate
  // Need to return boolean value
  checkMousePointOn: (key, type, coordinates, mouseCoordinate) => {},

  // Creating a graphic data source is a callback method. It must be a field and needs to return graphic data
  // step current step
  // points time price point information
  // coordinates time price point corresponding axis information
  // viewport size
  // precision precision information, including price and quantity precision
  // xAxis x-axis component
  // yAxis y axis component
  createGraphicDataSource: (step, points, coordinates, viewport, precision, xAxis, yAxis) => {},

  // Process the mouse movement operation during the drawing process, it can be defaulted, and the mouse operation is triggered during the drawing process
  // step current step
  // points graph time price point information
  // point time and price information corresponding to the mouse point
  performMouseMoveForDrawing: (step, points, point) => {},

  // Handle the mouse hold and move operation, it can be defaulted, the mouse is triggered during the movement process of holding down an operating point
  // points graph time price point information
  // pressedPointIndex The index of the pressed point
  // point time and price information corresponding to the mouse point
  performMousePressedMove: (points, pressedPointIndex, point) => {},

  // Extended drawing, default
  // ctx canvas context
  // graphicDataSources graphic data information
  // markOptions chart style configuration
  // viewport size
  // precision precision information, including price and quantity precision
  // xAxis x-axis component
  // yAxis y axis component
  drawExtend: (ctx, graphicDataSources, markOptions, viewport, precision, xAxis, yAxis) => {}
}
```
#### Method createGraphicDataSource return value sub-item information
```js
{
  // key
  key:'key'
  // Type, currently supports 'line' | 'text' | 'continuous_line' | 'polygon' | 'arc', which is a necessary field
  type:'line',
  // Whether you want to draw, you can default, draw by default
  isDraw: true,
  // Whether to check whether it is on the graph, it can be defaulted, not checked by default
  isCheck: true,
  // Style, type is 'line' and 'continuous_line' are invalid, only supports'fill' and'stroke', which can be defaulted, when type is 'text', the default is 'fill', others default to 'stroke'
  style:'stroke',
  // Data array, when type is 'line', it is a two-dimensional array
  dataSource: []
}
```
#### dataSource sub-item information
```javascript
{
  // x coordinate, required field
  x: 123,
  // y coordinate, necessary field
  y: 123,
  // radius, needed when type is 'arc'
  radius: 5,
  // Starting angle, required when type is 'arc'
  startAngle: 0,
  // End angle, required when type is 'arc'
  endAngle: 180,
  // text, required when type is 'text'
  text: '',
}
```


## Example
Use a filled circle with a border to illustrate how to configure it.
```javascript
{
  // name
  name:'circle',

  // It takes three steps to complete the drawing of a circle
  totalStep: 3,

  // Check if the mouse point is on the circle border
  checkMousePointOn: (key, type, coordinates, mouseCoordinate) => {
    const xDis = Math.abs(coordinates.x - mouseCoordinate.x)
    const yDis = Math.abs(coordinates.y - mouseCoordinate.y)
    const r = Math.sqrt(xDis * xDis + yDis * yDis)
    return Math.abs(r - coordinates.radius) < 2;
  },

  // Create graphic information
  createGraphicDataSource: (step, points, coordinates) => {
    if (coordinates.length === 2) {
      const xDis = Math.abs(coordinates[0].x - coordinates[1].x)
      const yDis = Math.abs(coordinates[0].y - coordinates[1].y)
      const radius = Math.sqrt(xDis * xDis + yDis * yDis)
      // A circle filled with a border is formed by a hollow circle and a solid circle
      return [
        // filled circle
        {
          type: 'arc',
          isDraw: true,
          // Filled circle, no need to check whether the mouse point is on the graph
          isCheck: false,
          // fill
          style: 'fill',
          // point information
          dataSource: [
            {...coordinates[0], radius, startAngle: 0, endAngle: Math.PI * 2 },
          ]
        },
        // hollow circle
        {
          type:'arc',
          isDraw: true,
          // Need to check if it is on the border
          isCheck: true,
          // point information
          dataSource: [
            {...coordinates[0], radius, startAngle: 0, endAngle: Math.PI * 2 },
          ]
        }
      ]
    }
    return []
  }
}
```
