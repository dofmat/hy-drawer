# src / mixin / index.js
Copyright (c) 2018 Florian Klampfer <https://qwtel.com/>

This program is free software: you can redistribute it and/or modify
it under the terms of the GNU General Public License as published by
the Free Software Foundation, either version 3 of the License, or
(at your option) any later version.

This program is distributed in the hope that it will be useful,
but WITHOUT ANY WARRANTY; without even the implied warranty of
MERCHANTABILITY or FITNESS FOR A PARTICULAR PURPOSE.  See the
GNU General Public License for more details.

You should have received a copy of the GNU General Public License
along with this program.  If not, see <http://www.gnu.org/licenses/>.

## Overview
This component is written in [RxJS] and reading its code requires some basic understanding
of how RxJS works. It may also serve as an example of how to use RxJS.

Other than RxJS, you should be familiar with [ES6 Mixin][esmixins],
which is a clever way of using the ES6 class syntax to achieve inheritance-based mixins.
The mixin in the main export of this file.

## Imports
ES6+ functions that we use.
import 'core-js/fn/array/from';
import 'core-js/fn/function/bind';
import 'core-js/fn/object/assign';

Importing the hy-compontent base libary,
which helps with making multiple versions of the component (Vanilla JS, WebComponent, etc...).


```js
import { componentMixin, COMPONENT_FEATURE_TESTS, Set } from 'hy-component/esm/component';
import { arrayOf, bool, number, oneOf } from 'hy-component/esm/types';

import { Subject } from 'rxjs/_esm5/Subject';
```

TODO


```js
import { setupObservables } from './setup';
```

A set of [Modernizr] tests that are required for this component to work.


```js
export const MIXIN_FEATURE_TESTS = new Set([
  ...COMPONENT_FEATURE_TESTS,
  'eventlistener',
  'queryselector',
  'requestanimationframe',
  'classlist',
  'opacity',
  'csstransforms',
  'csspointerevents',
]);

export { Set };
```

## Drawer Mixin


```js
export function drawerMixin(C) {
```

TODO: see ES6 mixins...


```js
  return class extends componentMixin(C) {
```

The name of the component (required by hy-component)


```js
    static get componentName() { return 'hy-drawer'; }
```

### Options
The default values (and types) of the configuration options (required by hy-component)
See [Options](../../options.md) for usage information.


```js
    static get defaults() {
      return {
        opened: false,
        align: 'left',
        persistent: false,
        range: [0, 100],
        threshold: 10,
        preventDefault: false,
        mouseEvents: false,
        /* _backButton: false, */
      };
    }

    static get types() {
      return {
        opened: bool,
        align: oneOf(['left', 'right']),
        persistent: bool,
        range: arrayOf(number),
        threshold: number,
        preventDefault: bool,
        mouseEvents: bool,
        /* _backButton: bool, */
      };
    }
```

Side effects of changing configuration options (if any).
Mostly we just put the value on an observable and deal with it from there.


```js
    static get sideEffects() {
      return {
        opened(x) { this.opened$.next(x); },
        align(x) { this.align$.next(x); },
        persistent(x) { this.persitent$.next(x); },
        preventDefault(x) { this.preventDefault$.next(x); },
        mouseEvents(x) { this.mouseEvents$.next(x); },
        /* _backButton(x) { this.backButton$.next(x); }, */
      };
    }
```

### Setup
Overriding the setup function.


```js
    setupComponent(el, props) {
      super.setupComponent(el, props);
```

Observables used for side effects caused by changing settings on the component.
The are used to emit the new vale whenever properties get changed on the component.


```js
      this.opened$ = new Subject();
      this.align$ = new Subject();
      this.persitent$ = new Subject();
      this.preventDefault$ = new Subject();
      this.mouseEvents$ = new Subject();
      this.animateTo$ = new Subject();
      this.teardown$ = new Subject();
      this.document$ = new Subject();
      /* this.backButton$ = new Subject(); */
```

Cache DOM elements.


```js
      this.scrimEl = this.root.querySelector('.hy-drawer-scrim');
      this.contentEl = this.root.querySelector('.hy-drawer-content');
```

Set the initial alignment class.


```js
      this.contentEl.classList.add(`hy-drawer-${this.align}`);
    }
```

Calling the [setup observables function](./setup.md) function.


```js
    connectComponent() {
      setupObservables.call(this);
```

Firing an event to let the outside world know the drawer is ready.


```js
      this.fireEvent('init', { detail: this.opened });
    }

    disconnectComponent() {
      this.teardown$.next({});
    }

    adoptComponent() {
      this.document$.next(document);
    }
```

### Methods
Public methods of this component. See [Methods](../../methods.md) for more.


```js
    open(animated = true) {
      if (animated) this.animateTo$.next(true);
      else this.opened = true;
    }

    close(animated = true) {
      if (animated) this.animateTo$.next(false);
      else this.opened = false;
    }

    toggle(animated = true) {
      if (animated) this.animateTo$.next(!this.opened);
      else this.opened = !this.opened;
    }
  };
}
```

[rxjs]: https://github.com/ReactiveX/rxjs
[esmixins]: http://justinfagnani.com/2015/12/21/real-mixins-with-javascript-classes/
[modernizr]: https://modernizr.com/

