# Formatting

## Data Transformation

Data values are converted to string (`'' + value`). If `value` is an object, the following rules apply first:

- `value = value.label` if defined and not null
- else `value = value.r` if defined and not null
- else `value = 'key[0]: value[key[0]], key[1]: value[key[1]], ...'`

This default behavior can be overridden thanks to the `formatter` option. It accepts a function called for every data and that takes two arguments:

- `value`: the current data value
- `context`: contextual information (see [option context](options.md#option-context))

Example:

```javascript
formatter: function(value, context) {
    return [ { text: context.dataIndex + ': ' + Math.round(value*100) + '%' } ];
}

// label for data at index 0 with value 0.23: "0: 23%"
// label for data at index 1 with value 0.42: "1: 42%"
// ...
```

::: tip
The first argument being the value, you can directly use generic methods:
:::

```javascript
formatter: Math.round
formatter: Math.floor
formatter: Math.ceil
// ...
```

## Custom Labels

It's also possible to display text other than the data values, for example, the associated labels:

```javascript
new Chart('id', {
    type: 'bar',
    data: {
        labels: ['foo', 'bar'],
        datasets: [{
            data: [42, 24]
        }]
    },
    options: {
        plugins: {
            datalabels: {
                formatter: function(value, context) {
                    return [ { text: context.chart.data.labels[context.dataIndex] } ];
                }
            }
        }
    }
});

// label for data at index 0: "foo"
// label for data at index 1: "bar"
// ...
```

::: tip
`chart.data.labels` is given as an example but it works with any source:
:::

```javascript
context.dataset.data[context.dataIndex].label;  // labels in each data object
context.dataset.labels[context.dataIndex];      // labels store in the dataset
globalLabels[context.dataIndex];                // labels store outside the chart
// ...
```

## Multiline Labels

Labels can be displayed on multiple lines by providing an array of objects where each object represents a new line. Each line object has a `text` and `color` property. If the color property is not defined, the color will be inherited from the `color` property of the main datalabels options object.

Example:

```javascript
formatter: function(value) {
    return [
        {
            text: 'line1',
            color: 'blue'
        },
        { text: 'line2' },
        { text: 'line3' }
    ];
}
```

::: tip
The space between each line can be adjusted using the `font.lineHeight` option.
:::

## Text Alignment

The `textAlign` option only applies to [multiline labels](#multiline-labels) and specifies the text alignment being used when drawing the label text (see [`CanvasRenderingContext2D.textAlign`](https://developer.mozilla.org/en-US/docs/Web/API/CanvasRenderingContext2D/textAlign)). Note that  right-to-left text detection based on the current locale is not currently implemented.

Supported values for `textAlign`:

- `'start'` (default): the text is left-aligned
- `'center'`: the text is centered
- `'end'`: the text is right-aligned
- `'left'`: alias of `'start'`
- `'right'`: alias of `'right'`
