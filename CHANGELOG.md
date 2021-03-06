# Change log

All notable changes to this project will be documented in this file.
This project adheres to [Semantic Versioning](http://semver.org/).

## [v2.2.0](https://github.com/reactjs/reselect/releases/tag/v2.2.0) - 2016/03/15

Add Typescript typings to package.json (#94)

## [v2.1.0](https://github.com/reactjs/reselect/releases/tag/v2.1.0) - 2016/03/03

Add `resetRecomputations` method to selectors (#90)

## [v2.0.3](https://github.com/reactjs/reselect/releases/tag/v2.0.3) - 2016/02/01

Fix bug (#78) in `defaultMemoize` which could cause the memoized value to be mistakenly returned for variadic functions.

## [v2.0.2](https://github.com/reactjs/reselect/releases/tag/v2.0.2) - 2016/01/14

Fix IE8 support by compiling in 'loose' mode

## [v2.0.1](https://github.com/reactjs/reselect/releases/tag/v2.0.1) - 2015/11/08

Update NPM to have latest README and github links since move to reactjs.

## [v2.0.0](https://github.com/reactjs/reselect/releases/tag/v2.0.0) - 2015/10/02

## Breaking Changes

### `createSelector`, `createStructuredSelector`, and custom selector creators check arguments

Input selectors are now verified to be functions during selector creation. If verification fails an error is thrown, allowing for a useful stack trace

There is a small chance that this could cause a breaking change in code that contains a faulty selector that is never called.

## [v1.1.0](https://github.com/reactjs/reselect/releases/tag/v1.1.0) - 2015/09/16

## New features

### `createStructuredSelector`

`createStructuredSelector` is a convenience function that helps with a common pattern when using Reselect.  The selector passed to a connect decorator often just takes other selectors and maps them to keys in an object:

```js
const mySelectorA = state => state.a;
const mySelectorB = state => state.b;

const structuredSelector = createSelector(
   mySelectorA,
   mySelectorB,
   mySelectorC,
   (a, b, c) => ({
     a, 
     b,
     c
   })
);
```

`createStructuredSelector` takes an object whose properties are input-selectors and returns a structured selector. The structured selector returns an object with the same keys as the `inputSelectors` argument, but with the selectors replaced with their values.

```js
const mySelectorA = state => state.a;
const mySelectorB = state => state.b;

const structuredSelector = createStructuredSelector({
  x: mySelectorA,
  y: mySelectorB
});

const result = structuredSelector({a: 1, b: 2}); // will produce {x: 1, y: 2}
```

## [v1.0.0](https://github.com/reactjs/reselect/releases/tag/v1.0.0) - 2015/09/09

## Breaking Changes

If upgrading from 0.0.2, see the release notes for v1.0.0-alpha

## [v1.0.0-alpha2](https://github.com/reactjs/reselect/releases/tag/v1.0.0-alpha2) - 2015/09/01

## New features

src directory included in npm package
js:next field added to package.json

## [v1.0.0-alpha](https://github.com/reactjs/reselect/releases/tag/v1.0.0-alpha) - 2015/09/01

## Breaking Changes

`createSelectorCreator` takes a user specified memoize function instead of a custom `valueEqualsFunc`.

### Before

```js
import { isEqual } from 'lodash';
import { createSelectorCreator } from 'reselect';

const deepEqualsSelectorCreator = createSelectorCreator(isEqual);
```

### After

```js
import { isEqual } from 'lodash';
import { createSelectorCreator, defaultMemoize } from 'reselect';

const deepEqualsSelectorCreator = createSelectorCreator(
  defaultMemoize,
  isEqual
);
```

## New features

### Variadic Dependencies

Selector creators can receive a variadic number of dependencies as well as an array of dependencies.

#### Before

```js
const selector = createSelector(
  [state => state.a, state => state.b],
  (a, b) => a * b
);
```

#### After

```js
const selector = createSelector(
  state => state.a,
  state => state.b,
  (a, b) => a * b
);
```

### Access `ownProps` in Selector

Selector dependencies can receive a variadic number of parameters allowing a selector to receive `ownProps` passed from `mapToProps` in `connect`.

```js
const selector = createSelector(
  (state) => state.a,
  (state, props) => state.b * props.c,
  (_, props) => props.d,
  (a, bc, d) => a + bc + d
);
```

### Configurable Memoize Function

```js
import { createSelectorCreator } from 'reselect';
import memoize from 'lodash.memoize';

let called = 0;
const customSelectorCreator = createSelectorCreator(memoize, JSON.stringify);
const selector = customSelectorCreator(
  state => state.a,
  state => state.b,
  (a, b) => {
    called++;
    return a + b;
  }
);
assert.equal(selector({a: 1, b: 2}), 3);
assert.equal(selector({a: 1, b: 2}), 3);
assert.equal(called, 1);
assert.equal(selector({a: 2, b: 3}), 5);
assert.equal(called, 2);
```
