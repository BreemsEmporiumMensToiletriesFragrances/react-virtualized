CellMeasurer
---------------

High-order component for automatically measuring a cell's contents by rendering it in a way that is not visible to the user.
Specify a fixed width or height constraint if you only want to measure one dimension.

This HOC is mainly intended for use with a `Grid`. If you want to use it with `VirtualScroll` or `FlexTable`, you'll need to provide an adapter method that converts the incoming `rowRenderer` params to those expected by `cellRenderer`. For example:

```js
function rowRenderer ({ index }) {
  return cellRenderer({
    columnIndex: 1,
    rowIndex: index
  })
}
```

Also note that in order to measure a column's width for a `Grid`, that column's content must be rendered for all rows in order to determine the maximum width.
For this reason it may not be a good idea to use this HOC for `Grid`s containing a large number of both columns _and_ cells.

**Warning**: Certain box-sizing settings (eg `box-sizing: border-box`) may cause slight discrepencies if borders are applied to a `Grid` whose cells are being measured. For this reason, it is recommended that you avoid placing borders on a `Grid` that uses a `CellMeasurer` and instead style its parent container. (See [issue 338](https://github.com/bvaughn/react-virtualized/issues/338) for more background information.)

### Prop Types
| Property | Type | Required? | Description |
|:---|:---|:---:|:---|
| cellRenderer | Function | ✓ | Renders a cell given its indices. `({ columnIndex: number, rowIndex: number }): PropTypes.node` |
| cellSizeCache | Object |  | Optional, custom caching strategy for cell sizes. |
| children | Function | ✓ | Function respondible for rendering a virtualized component; `({ getColumnWidth: Function, getRowHeight: Function, resetMeasurements: Function }) => PropTypes.element` |
| columnCount | number | ✓ | Number of columns in the `Grid`; in order to measure a row's height, all of that row's columns must be rendered. |
| container |  |  | A Node, Component instance, or function that returns either. If this property is not specified the document body will be used. |
| height | number |  | Fixed height; specify this property to measure cell-width only. |
| rowCount | number | ✓ | Number of rows in the `Grid`; in order to measure a column's width, all of that column's rows must be rendered. |
| width | number |  | Fixed width; specify this property to measure cell-height only. |

### Children function

The child function is passed the following named parameters:

| Parameter | Type | Description |
|:---|:---|:---|
| getColumnWidth | Function | Callback to set as the `columnWidth` property of a `Grid` |
| getRowHeight | Function | Callback to set as the `rowHeight` property of a `Grid` |
| resetMeasurementForColumn(index) | Function | Use this function to clear cached measurements for specific column in `CellRenderer`; its size will be remeasured the next time it is requested. |
| resetMeasurementForRow(index) | Function | Use this function to clear cached measurements for specific row in `CellRenderer`; its size will be remeasured the next time it is requested. |
| resetMeasurements | Function | Use this function to clear cached measurements in `CellRenderer`; each cell will be remeasured the next time its size is requested. |

### CellSizeCache

If you choose to override the `cellSizeCache` property your cache should support the following operations:

```js
class CellSizeCache {
  clearAllColumnWidths (): void;
  clearAllRowHeights (): void;
  clearColumnWidth (index: number): void;
  clearRowHeight (index: number): void;
  getColumnWidth (index: number): number;
  getRowHeight (index: number): number;
  hasColumnWidth (index: number): boolean;
  hasRowHeight (index: number): boolean;
  setColumnWidth (index: number, width: number): void;
  setRowHeight (index: number, height: number): void;
}
```

The [default caching strategy](https://github.com/bvaughn/react-virtualized/blob/master/source/CellMeasurer/defaultCellSizeCache.js) is exported as `defaultCellMeasurerCellSizeCache` should you wish to decorate it.

### Examples

This example shows a `Grid` with fixed column widths and dynamic row heights.
For more examples check out the component [demo page](https://bvaughn.github.io/react-virtualized/?component=CellMeasurer).

```javascript
import React from 'react';
import ReactDOM from 'react-dom';
import { CellMeasurer, Grid } from 'react-virtualized';
import 'react-virtualized/styles.css'; // only needs to be imported once

ReactDOM.render(
  <CellMeasurer
    cellRenderer={cellRenderer}
    columnCount={columnCount}
    height={height}
    rowCount={rowCount}
  >
    {({ getColumnWidth }) => (
      <Grid
        columnCount={columnCount}
        columnWidth={getColumnWidth}
        height={height}
        cellRenderer={cellRenderer}
        rowCount={rowCount}
        rowHeight={fixedRowHeight}
        width={width}
      />
    )}
  </CellMeasurer>,
  document.getElementById('example')
);
```
