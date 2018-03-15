# src / mixin / observables.js
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


```js

import { combineLatest } from 'rxjs/_esm5/observable/combineLatest';
import { fromEvent } from 'rxjs/_esm5/observable/fromEvent';
import { merge } from 'rxjs/_esm5/observable/merge';

import { tap } from 'rxjs/_esm5/operators/tap';
import { filter } from 'rxjs/_esm5/operators/filter';
import { map } from 'rxjs/_esm5/operators/map';
import { mapTo } from 'rxjs/_esm5/operators/mapTo';
import { skipWhile } from 'rxjs/_esm5/operators/skipWhile';
import { switchMap } from 'rxjs/_esm5/operators/switchMap';
import { withLatestFrom } from 'rxjs/_esm5/operators/withLatestFrom';

import { subscribeWhen } from './operators';
```

Using shorthands for common functions


```js
const assign = Object.assign.bind(this);
const abs = Math.abs.bind(this);
```

#### Get start observable
The following function returns an observable of all "start" events.
Usually, that's just `touchstart` event of the first finger touching the screen,
however since the compontent also supports mouse events,
we may listen for `mousedown` events.


```js
export function getStartObservable() {
```

Since the `mouseEvents` option may change at any point, we `switchMap` to reflect the changes.


```js
  return combineLatest(this.document$, this.mouseEvents$)
    .pipe(switchMap(([doc, mouseEvents]) => {
```

The touchstart observable is passive since we won't be calling `preventDefault`.
Also, we're only interested in the first `touchstart`.


```js
      const touchstart$ = fromEvent(doc, 'touchstart', { passive: true }).pipe(
        filter(({ touches }) => touches.length === 1),
        map(({ touches }) => touches[0]),
      );
```

If mouse events aren't enabled, we're done here.


```js
      if (!mouseEvents) return touchstart$;
```

Otherwise we also include `mousedown` events in the output.


```js
      const mousedown$ = fromEvent(doc, 'mousedown')
        .pipe(tap(event => assign(event, { event })));

      return merge(touchstart$, mousedown$);
    }));
}
```

#### Get move observable
This function returns an observable of all move events. Usually that's just `touchmove`,
but may also include `mousemove` events while the mouse button is down.


```js
export function getMoveObservable(start$, end$) {
```

Since the `mouseEvents` or `preventDefault` option may change at any point,
we `switchMap` to reflect the changes.
Nice: `combineLatest` provides us with the functionality of emitting
when either of the inputs change, but not before all inputs have their first value set.


```js
  return combineLatest(this.document$, this.mouseEvents$, this.preventDefault$)
    .pipe(switchMap(([doc, mouseEvents, preventDefault]) => {
```

We're only keeping track of the first finger.
Should the user remove the finger that started the interaction, we use the next instead.
Note that this doesn't occur under normal circumstances,
and exists primarliy to ensure that the interaction continues without hiccups.
Note that the event listener is only passive when the `preventDefault` option is falsy.


```js
      const touchmove$ = fromEvent(doc, 'touchmove', { passive: !preventDefault })
        .pipe(map(event => assign(event.touches[0], { event })));
```

If mouse events aren't enabled, we're done here.


```js
      if (!mouseEvents) return touchmove$;
```

Otherwise we listen for `mousemove` events,
but only those between a `start` and `end` event, i.e. while the user is sliding.
We unsubscribe form the source observable outside of those contraints.
Again, the listener is only marked as passive when the `preventDefault` option is falsy.


```js
      const mousemove$ = fromEvent(doc, 'mousemove', { passive: !preventDefault }).pipe(
        subscribeWhen(merge(
          start$.pipe(mapTo(true)),
          end$.pipe(mapTo(false)),
        )),
        map(event => assign(event, { event })),
      );

      return merge(touchmove$, mousemove$);
    }));
}
```

#### Get end observable
This function returns an observable of end events.
Usually, this is the `touchend` event of the last finger, but may also include `mouseup` events,
when the `mouseEvents` option is enabled.


```js
export function getEndObservable() {
```

Since the `mouseEvents` option may change at any point, we `switchMap` to reflect the changes.


```js
  return combineLatest(this.document$, this.mouseEvents$)
    .pipe(switchMap(([doc, mouseEvents]) => {
```

We're only interested in the last `touchend`.
Otherwise there's at least one finger left on the screen,
that can be used to slide the drawer.


```js
      const touchend$ = fromEvent(doc, 'touchend', { passive: true }).pipe(
        filter(({ touches }) => touches.length === 0),
        map(event => event.changedTouches[0]),
      );
```

If mouse events aren't enabled, we're done here.


```js
      if (!mouseEvents) return touchend$;
```

Otherwise we include `mouseup` events.


```js
      const mouseup$ = fromEvent(doc, 'mouseup', { passive: true });
      return merge(touchend$, mouseup$);
    }));
}
```

#### Get "Is sliding?" observable
An observable that emits `true` when the user is *sliding* the drawer,
(i.e. moving the finger along the x-axis), or `false` when *scrolling* the page
(i.e. moving the finger along the y-axis).


```js
export function getIsSlidingObservable(move$, start$) {
```

If the threshold options is set, we delay the decision until
the finger has moved at least `threshold` pixels in either direction.


```js
  if (this.threshold) {
    return move$.pipe(
      withLatestFrom(start$),
      skipWhile(([{ clientX, clientY }, { clientX: startX, clientY: startY }]) =>
        abs(startY - clientY) < this.threshold && abs(startX - clientX) < this.threshold),
      map(([{ clientX, clientY }, { clientX: startX, clientY: startY }]) =>
        abs(startX - clientX) >= abs(startY - clientY)),
    );
```

If the threshold option is set to `0` (or `false`) we make a decision immediately.
This is intended for Safari and possibly other browsers that have a built-in threshold.
Additionally, Safari ignores all calls to `preventDefault`, except on the first move event
after a start event, so that we *have to* make a decision immediately.


```js
  } else {
    return move$.pipe(
      withLatestFrom(start$),
      map(([{ clientX, clientY, event }, { clientX: startX, clientY: startY }]) => {
        const isSliding = abs(startX - clientX) >= abs(startY - clientY);
        if (this.preventDefault && isSliding) event.preventDefault();
        return isSliding;
      }),
    );
  }
}
```

