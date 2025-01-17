# mocha-tags-ultra



```bash
npm install mocha-tags-ultra --save-dev
```

## Quick example

```js
tags('network')
.describe(/* ... */)

tags('integration', 'fast')
.it(/* ... */)
```

By default all tests will run as usual,
but you can use the `--tags` option to filter them:

```bash
mocha --tags "is:integration not:slow not:network"
```

Tests that don't match the criteria are skipped with the `xdescribe` or `xit` commands,
and appear as `pending` in the test output.

## Filtering

*Note*: because of the way filtering works, tags have to be a single word, without any spaces.

### `is:<tag>`

Run tests with the `X` tag

```bash
mocha --tags "is:X"
```

Run tests with the `X` or `Y` tags

```bash
mocha --tags "is:X is:Y"
```

Run tests with the `X` and `Y` tag

```bash
mocha --tags "is:X+Y"
```

### `not:<tag>`

Don't run tests with the X flag

```bash
mocha --tags "not:X"
```

Don't run tests with the X or Y flag

```bash
mocha --tags "not:X not:Y"
```

Don't run tests with the X and Y flag

```bash
mocha --tags "not:X+Y"
```

## Programmatic usage

You might want to exclude certain tags based on complex logic.
This is not easy to define from the command line, so `mocha-tags` exposes its filter programmatically.

```js
var tags = require('mocha-tags-ultra');

// either replace the entire filter
tags.filter = new tags.Filter('not:trading-hours');

// or simply modify the existing one
if (moment().hours() < 8 || moment().hours() > 18) {
  tags.filter.remove('not:unit');
  tags.filter.add('not:trading-hours');
}

tags('trading-hours').it(
  // some integration test than can only run during core hours
);
```

## Troubleshooting

Skipped tests appear as `pending` in the test output,
so you should always notice any test that was skipped by accident.

It also helps to add the following at the top of your main test file / spec helper.

```js
var tags = require('mocha-tags-ultra');
console.log('Test filter: ', tags.filter);
```

## Custom test hooks

`mocha-tags` supports the following keywords:

- `tags().describe` : normal usage
- `tags().describe.only` : ignores any tags and filters, and runs by itself
- `tags().xdescribe` : ignored regardless of tags. Also can be used as `tags().describe.skip`.

...and the same pattern for `it`.
You can also add custom test hooks by setting the following property:

```js
tags.hooks = function(either) {
  this.mytest = either(fnMatch, fnSkip);
};

// to be used as
tags('hello', 'world').mytest(/* arguments */);

// which will call either
fnMatch(/* arguments */);
fnSkip(/* arguments */);
```
