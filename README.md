# @embroider/util

> **This is a fork.** It is `@embroider/util` 1.13.5 from
> [embroider-build/embroider](https://github.com/embroider-build/embroider)
> (tag `v1.13.5-@embroider/util`, `packages/util`), with only
> `package.json` changed:
>
> - **ember-cli-babel 8** instead of 7. 1.13.0 already used it; 1.13.1 reverted
>   that only to keep Node 12 working for ember-auto-import's tests
>   ([#1891](https://github.com/embroider-build/embroider/pull/1891)). With
>   ember-cli-babel 7, installing this package pulls in `@babel/runtime@7.12.18`
>   and `tmp@0.0.x`, which have known vulnerabilities.
> - **`@embroider/macros` `>=1.19.7 <1.21.0`.** That keeps the lazy `importSync` fix from
>   1.18.1, allows ember-cli-babel 8 (1.19.7+), and stays below the v2-addon
>   rewrite in 1.21.
> - Node.js 20 or above. Node 18 is end-of-life, and the dependency tree no longer
>   installs on it with yarn: `pkg-entry-points@1.1.2` (from `@embroider/macros`)
>   and `mktemp@2` (from the broccoli plugin chain) both require Node 20+.
>
> The runtime code (`addon/`, `app/`) is identical to upstream 1.13.0–1.13.5.
> The package keeps the name `@embroider/util`, so an app can switch to it with one
> resolution. Upstream no longer publishes this package; on its `main` branch,
> `packages/util` has been reduced to an empty private stub.
>
> This repository is an interim home, used while testing ember-bootstrap 5.
>
> Install it as a git dependency:
>
> ```json
> "@embroider/util": "Gaurav0/embroider-util#v1.13.5-scf.1"
> ```

Utilities to help apps and addons with Embroider support.

## Compatibility

- Ember.js v3.13 or above (this fork is tested on 4.8, 4.12, 5.4, 5.8 and release)
- Ember CLI v2.13 or above
- Node.js v20 or above

## Installation

```
ember install @embroider/util
```

## The Utilities

### `ensureSafeComponent`

**This function is intended to help addon authors who still need to support Ember < 3.25**. In all other cases, instead of using this you should directly pass components around as values (not as strings) and invoke them directly with angle brackets (not the `{{component}}` helper).

For the full explanation of why and how you would use this, see [the Addon Author Guide](https://github.com/embroider-build/embroider/blob/main/docs/replacing-component-helper.md).

Example usage in Javascript:

```js
import { ensureSafeComponent } from '@embroider/util';
import Component from '@glimmer/component';
import DefaultTitleComponent from './default-title';

export default class extends Component {
  get title() {
    return ensureSafeComponent(this.args.title || DefaultTitleComponent, this);
  }
}
```

```hbs
<this.title />
```

Example usage in a template:

```hbs
{{#let
  (ensure-safe-component (or @title (component 'default-title')))
  as |Title|
}}
  <Title />
{{/let}}
```

The first argument is allowed to be:

- a string. If we see a string, we will emit a deprecation warning because passing components-as-strings doesn't work safely under Embroider with `staticComponents` enabled. We will return a value that is safe to invoke (via angle brackets) on your current Ember version.
- a curried component definition (which is the kind of value you receive when someone does `<YourComponent @customThing={{component "fancy"}}/>`). These are returned unchanged, because they're always safe to invoke.
- a component class, in which case if your ember version does not yet support directly invoking component classes, we will convert it to a curried component definition for you.

In the Javascript version, you must pass a second argument that is any object with an owner (a `Component` instance works great).

## Glint usage
If you are using [Glint](https://typed-ember.gitbook.io/glint/) and `environment-ember-loose`, you can add all the macros to your app at once by adding

```ts
import type { EmbroiderUtilRegistry } from "@embroider/util";
```
to your app's e.g. `types/glint.d.ts` file, and making sure your registry extends from EmbroiderMacrosRegistry:

```ts
declare module '@glint/environment-ember-loose/registry' {
  export default interface Registry
    extends EmbroiderUtilRegistry, /* other registries here */ {
      // ...
    }
}
```

## Contributing

See the [Contributing](CONTRIBUTING.md) guide for details.

## License

This project is licensed under the [MIT License](LICENSE.md).
