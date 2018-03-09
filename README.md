# hy-drawer

[![npm version](https://badge.fury.io/js/hy-drawer.svg)](https://badge.fury.io/js/hy-drawer)

**hy-drawer** is a touch-enabled drawer component for the modern web.
It focuses on providing a fun, natural feel in both the Android and iOS stock browser,
while being performant and easy to use.
It is the perfect companion for mobile-first web pages and progressive web apps.

> A touch-enabled drawer component for the modern web.
{:.lead}

**hy-drawer** can be used in a variety of ways:
* As **Vanilla** JavaScript class
* As **jQuery** plugin
* As **WebComponent**, both as *ES6 Module* and *HTML Import*
* Possibly as part of your own component hierarchy via [ES6 Mixin][esmixins].

**hy-drawer** is used by thousands of sites as part of the [Hydejack]{:.external} Jekyll theme.

**NOTE**: The current version is a pre-release. The public API may still change in important ways.
{:.message}


## Examples
* [Mixin Example](example/mixin/index.html){:.external}
* [Vanilla JS Example](example/vanilla/index.html){:.external}
* [jQuery Example](example/jquery/index.html){:.external}
* [WebComponent Example](example/webcomponent/index.html){:.external}


## License

|            | Personal           | Startup            | Enterprise         |
|:-----------|:------------------:|:------------------:|:------------------:|
| Developers | 2                  | 15                 | ∞                  |
| License    | [Personal][pl]     | [Startup][sl]      | [Enterprise][el]   |
| Price      | $29                | $249               | $499               |
| Buy        | [**Buy this**][bp] | [**Buy this**][bs] | [**Buy this**][be] |
{:.stretch-table}

[pl]: licenses/personal.md
[sl]: licenses/startup.md
[el]: licenses/enterprise.md
[bp]: https://gumroad.com/l/hy-drawer-personal
[bs]: https://gumroad.com/l/hy-drawer-startup
[be]: https://gumroad.com/l/hy-drawer-enterprise

Unless you've obtained one of the licenses above, **hy-drawer** must be used in accordance with the [GPL-3.0](LICENSE.md) license.


## Usage
The most straight-forward way to use **hy-drawer** is by using the vanilla JS version and load it from a CDN:

~~~html
<link rel="stylesheet" href="https://unpkg.com/hy-drawer/dist/vanilla/style.css">
<script src="https://unpkg.com/hy-drawer/dist/vanilla/hy-drawer.min.js"></script>
~~~

~~~html
<aside id="drawerEl"><!--content--></aside>
<script>
  var Drawer = window.hyDrawer.Drawer;
  var drawer = new Drawer(window.drawerEl, { /* options */ });
</script>
~~~


## Documentation

* [Options](doc/options.md)
* [Methods](doc/methods.md)
* [Events](doc/events.md)
* [Styling](doc/styling.md)


## Gold Standard
This component follows the [Web Components Gold Standard](doc/gold-standard.md).


## Source
The source code is written in a *literal programming* style, and should be reasonably approachable.
However, some knowledge of [RxJS] is required.

The core functionality is implemented in [`mixin / index.js`](doc/source/mixin/index.md),
which is used to create the framework-specific versions of the component.

* `jquery`
  * [`index.js`](doc/source/jquery/index.md)
* `mixin`
  * [`calc.js`](doc/source/mixin/calc.md)
  * [`constants.js`](doc/source/mixin/constants.md)
  * [`index.js`](doc/source/mixin/index.md)
  * [`observables.js`](doc/source/mixin/observables.md)
  * [`operators.js`](doc/source/mixin/operators.md)
  * [`setup.js`](doc/source/mixin/setup.md)
  * [`update.js`](doc/source/mixin/update.md)
* `vanilla`
  * [`index.js`](doc/source/vanilla/index.md)
* `webcomponent`
  * [`html-import.js`](doc/source/webcomponent/html-import.md)
  * [`index.js`](doc/source/webcomponent/index.md)
  * [`module.js`](doc/source/webcomponent/module.md)
* [`common.js`](doc/source/common.md)


[esmixins]: http://justinfagnani.com/2015/12/21/real-mixins-with-javascript-classes/
[rxjs]: https://github.com/ReactiveX/rxjs
[hydejack]: https://qwtel.com/hydejack/
