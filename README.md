# react-mobx-translatable

Make React components translatable using MobX. Can be used both on the server (SSR) and in the browser.

Note: This plugin depends on ``mobx-react`` features that are currently marked as experimental: ``Provider`` and ``inject``.

[![Build Status](https://travis-ci.org/infinum/react-mobx-translatable.svg?branch=master)](https://travis-ci.org/infinum/react-mobx-translatable)
[![Dependency Status](https://david-dm.org/infinum/react-mobx-translatable.svg)](https://david-dm.org/infinum/react-mobx-translatable)
[![devDependency Status](https://david-dm.org/infinum/react-mobx-translatable/dev-status.svg)](https://david-dm.org/infinum/react-mobx-translatable#info=devDependencies)

## Installation

```Bash
npm install react-mobx-translatable
```

## Usage

### Checklist (see the example for more details)

* Setup the i18n object in store
* Initialize the [i18n-harmony lib](https://github.com/DarkoKukovec/i18n-harmony) and translatable
* Wrap your components with ``Provider`` component
* Set the ``@translatable`` decorator on your components
* Call ``this.t(translationKey, options)`` from the component
* To change the language, change the locale variable in the store

### Methods

#### init(injectFn)

Method receives ``injectFn`` - function that maps the i18n object from the store
* The function receives the whole store object (from ``Provider``)
* The function should return an object that contains an ``i18n`` key with the i18n object from store (see the example)
* Default: ``(store) => {i18n: i18n.store}``
* If you have the i18n object in the root of the store (the default function can map the value), you don't need to call ``init``

#### translatable(Component|String[])

Method can receive either an array of strings or a React component.

* Array of strings - Used for connecting the store from ``Provider`` to the component. Translatable is returning a new function that accepts a React component.
* React component - The function will wrap the passed component

In both cases, the wrapped component will also be an observer. If using with other decorators, ``translatable`` should be the innermost one.

## Example

The example assumes you're using the following:
* [ES2015](https://babeljs.io/docs/plugins/preset-es2015/)
* [object rest spread](http://babeljs.io/docs/plugins/transform-object-rest-spread/)
* [decorators](https://github.com/loganfsmyth/babel-plugin-transform-decorators-legacy)

This is however not a requirement.

### Initialize store, i18n, and translatable

```JavaScript
import {observable} from 'mobx';
import i18n from 'i18n-harmony';
import {init} from 'translatable';

const defaultLocale = 'en'; // Can be based on browser language or user selection (localStorage, cookies)

const store = {
  i18n: observable({locale: defaultLocale})
};

// For details, see i18n-harmony: https://github.com/DarkoKukovec/i18n-harmony
i18n.init({
  translations: {
    en: {hello: 'Hello world!'}
  }
});

init((store) => ({i18n: store.i18n}));
```

### Wrap your React components inside of the ``Provider`` component and pass it the store

```JavaScript
import {Provider} from 'mobx-react';
import store from './store';

ReactDOM.render(<Provider {...store}>
  <Router {...renderProps} />
</Provider>, document.getElementById('app'));
```

### Translatable component

``` JavaScript
import {Component} from 'react';
import {translatable} from 'translatable';

@translatable
export default class MyComponent extends Component {
  render() {
    return <div>{this.t('hello')}</div>
  }
}
```

### `has` method

``` JavaScript
import {Component} from 'react';
import {translatable} from 'translatable';

@translatable
export default class MyComponent extends Component {
  render() {
    return <div>{this.has('hello') && this.t('hello')}</div>
  }
}
```

## Changelog

### v1.2.0

* Expose `has` from [i18n-harmony lib](https://github.com/DarkoKukovec/i18n-harmony)

### v1.1.0

* Add ability to connect the store with the component

### v1.0.0

* Initial release

## License
[MIT License](LICENSE)
