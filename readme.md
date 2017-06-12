# mri [![Build Status](https://travis-ci.org/lukeed/mri.svg?branch=master)](https://travis-ci.org/lukeed/mri)

> Quickly scan for CLI flags and arguments

This is a [fast](#benchmarks) and lightweight alternative to [`minimist`](https://github.com/substack/minimist) and [`yargs-parser`](https://github.com/yargs/yargs-parser).

It only exists because I find that I usually don't need most of what `minimist` and `yargs-parser` have to offer. However, `mri` is similar _enough_ that it might function as a "drop-in replacement" for you, too!

See [Comparisons](#comparisons) for more info.


## Install

```
$ npm install --save mri
```


## Usage

```sh
$ demo-cli --foo --bar=baz -mtv -- hello world
```

```js
const mri = require('mri');

const args = process.argv.slice(2);

mri(args);
//=> { _: ['hello', 'world'], foo:true, bar:'baz', m:true, t:true, v:true }

mri(args, { boolean:['bar'] });
//=> { _: ['baz', 'hello', 'world'], foo:true, bar:true, m:true, t:true, v:true }

mri(args, {
  alias: {
    b: 'bar',
    foo: ['f', 'fuz']
  }
});
//=> { _: ['hello', 'world'], foo:true, f:true, fuz:true, b:'baz', bar:'baz', m:true, t:true, v:true }
```

## API

### mri(args, options)

#### args

Type: `array`<br>
Default: `[]`

An array of arguments to parse. For CLI usage, send `process.argv.slice(2)`. See [`process.argv`](https://nodejs.org/docs/latest/api/process.html#process_process_argv) for info.

#### options.alias

Type: `object`<br>
Default: `{}`

An object of keys whose values are a `string` or `array` of aliases. These will be added to the parsed output with matching values.

#### options.boolean

Type: `array|string`<br>
Default: `[]`

A single key (or array of keys) that should be parsed as `Boolean`s.

#### options.default

Type: `object`<br>
Default: `{}`

An `key:value` object of defaults. If a default is provided for a key, its type (`typeof`) will be used to cast parsed arguments.

```js
mri(['--foo', 'bar']);
//=> { _:[], foo:'bar' }

mri(['--foo', 'bar'], {
  default:{ foo:true, baz:'hello', bat:42 }
});
//=> { _:['bar'], foo:true, baz:'hello', bat:42 }
```

> **Note:** Because `--foo` has a default of `true`, its output is cast to a boolean. This means that `foo:true`, which makes `'bar'` an extra argument (`_` key).

#### options.string

Type: `array|string`<br>
Default: `[]`

A single key (or array of keys) that should be parsed as `String`s.


## Comparisons

#### minimist

- `mri` is 2.5x faster (see [benchmarks](#benchmarks))
- Numerical values are cast as `Number`s when possible
  - A key (and its aliases) will always honor `opts.boolean` or `opts.string`
- Short flag groups are treated as `Boolean`s by default:
    ```js
    minimist(['-abc', 'hello']);
    //=> { _:[], a:'', b:'', c:'hello' }

    mri(['-abc', 'hello']);
    //=> { _:[], a:true, b:true, c:'hello' }
    ```
- Missing `options`:
  - `opts.stopEarly`
  - `opts.unknown`
  - `opts['--']`
- Ignores newlines (`\n`) within args (see [test](https://github.com/substack/minimist/blob/master/test/parse.js#L69-L80))
- Ignores slashBreaks within args (see [test](https://github.com/substack/minimist/blob/master/test/parse.js#L147-L157))
- Ignores dot-nested flags (see [test](https://github.com/substack/minimist/blob/master/test/parse.js#L180-L197))

#### yargs-parser

- `mri` is 20x faster (see [benchmarks](#benchmarks))
- Numerical values are cast as `Number`s when possible
  - A key (and its aliases) will always honor `opts.boolean` or `opts.string`
- Missing `options`:
  - `opts.array`
  - `opts.config`
  - `opts.coerce`
  - `opts.count`
  - `opts.envPrefix`
  - `opts.narg`
  - `opts.normalize`
  - `opts.configuration`
  - `opts.number`
  - `opts['--']`
- Missing [`parser.detailed()`](https://github.com/yargs/yargs-parser#requireyargs-parserdetailedargs-opts) method
- No [additional configuration](https://github.com/yargs/yargs-parser#configuration) object


## Benchmarks

```
mri
  --> 328,452 ops/sec ±0.28% (92 runs sampled)
yargs
  --> 16,014 ops/sec ±0.69% (94 runs sampled)
minimist
  --> 131,896 ops/sec ±0.92% (90 runs sampled)
```


## License

MIT © [Luke Edwards](https://lukeed.com)
