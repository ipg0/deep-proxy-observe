# deep-proxy-observe

## Zero dependency implementation of deep observable objects

This module implements the Observable pattern on deeply nested objects using the
Proxy API.

The module exposes a single method that creates the deeply observable object.

## Disclaimer

I created this module for a project at work. It is a solution
created to get around a particular problem in a large codebase full of legacy
code and outdated technologies. Some might find it useful, but I cannot
guarantee proper maintenance of this project, or that this module will work in
any given environment. The intended purpose of this module is highly specific,
so it might break your code if you use it on some edge case I didn't take into
account.

I may introduce breaking changes to this module without any prior announcement.

I may close issues and reject pull requests without an objective reason.

**Use at your own risk.**

## `deepObserve` function

Creates a deep/nested Proxy (observable) from a given object.
A deep Proxy listens to any changes within a nested object
by turning every object in the nested tree into a Proxy.
Any changes to objects created within the tree during its lifetime
will also trigger the callback.

The Proxy created by this module only listens to changes (set and delete)
and executes the callback after the action, therefore it becomes an
observable.
Modifying the object in the callback function can be implemented,
but it is not the intended use case.

_Parameters_

- `object: object` the object to create an observable from

- `callback: function` the function to call when the object is changed

  _Parameters_

  - `target: object` the object in the tree, a property of which is being
    modified
  - `path: string[]` the path of the modified property (for example, the
    property accessed by `observableObject.a.b.c` will have the path of
    `["a", "b", "c"]`)
  - `value: any` the new value of the property. In case the property is being
    deleted, this argument will not be passed,
    so it is possible to check for it.

  _Returns_

  `any` the result of the callback function does not matter.

_Returns_

`Proxy` the observable Proxy with the callback attached.
Otherwise behaves like a normal `object`.

## Usage

### Log any modification of the object to the console:

```js
import { deepObserve } = from 'deep-proxy-observe'

const observable = deepObserve(
  {},
  (t, p, v) =>
    console.log(
      `property ${p.join('.')} modified to ${JSON.stringify(v)}`
    )
)

observable.p = { a: { b: { c: 'd' } } }
// => property p modified to {"a":{"b":{"c":"d"}}}

observable.p.a.b.c = 'f'
// => property p.a.b.c modified to "f"
```
