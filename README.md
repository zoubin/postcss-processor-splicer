# postcss-processor-splicer
Allow your postcss plugin pipeline to be modified like an array.

## Example

```javascript
var Splicer = require('postcss-processor-splicer')
var postcss = require('postcss')
var noop = function () {
  return function () {}
}
var A = postcss.plugin('A', noop)
var B = postcss.plugin('B', noop)
var C = postcss.plugin('C', noop)
var D = postcss.plugin('D', noop)
var E = postcss.plugin('E', noop)

var pipeline = Splicer([
  'a', [ A() ],
  'b', [ B() ],
  'c', [ postcss(C()) ],
  'd', [
    'a', [ A() ],
    'b', [ B() ],
  ],
])

console.log(
  pipeline.toProcessor()  // create a processor
  .plugins.map(getName)
)
// [ 'A', 'B', 'C', 'A', 'B' ]

pipeline.splice('c', 1) // delete C
console.log(
  pipeline.toProcessor().plugins.map(getName)
)
// [ 'A', 'B', 'A', 'B' ]

pipeline.get('d').splice('a', 0, D(), E())
console.log(
  pipeline.toProcessor().plugins.map(getName)
)
// [ 'A', 'B', 'D', 'E', 'A', 'B' ]

function getName(plugin) {
  return plugin.postcssPlugin
}

```

## pipeline = Splicer(processors)

### processors

Type: `Array`

### pipeline.[ArrayLikeMethods]
`ArrayLikeMethods` could be one of the following:

* `splice`
* `push`
* `pop`
* `shift`
* `unshift`

### pipeline.get(label)
### pipeline.indexOf(label)

#### label

Type: `String`, `Number`

### pipeline.toProcessor()

Return a postcss processor.
