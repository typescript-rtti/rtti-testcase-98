Repro repository: https://github.com/aquilae/typescript-rtti-relative-path-issue

Doing `reflect<T>()` where `T` is imported from a referenced composite project emits incorrect relative path to `T` source from importing module:

```ts
/* folder structure:
- packages // src dir
 - export
 - import
- .typescript // out dir
 - packages
  - export
  - import
*/

// Importing.ts
import { reflect } from 'typescript-rtti'
import type Exported from '../export/Exported'

reflect<Exported>()

// Importing.js
{ LΦ: function (t) { return require("../../.typescript/packages/export/Exported")["default"]; } }
//                                        ^ we're already in .typescript folder here
...
```

From what little investigation I could do, `tsc` treats composite projects as external modules, and the source file of symbol for `Exported` is `.d.ts` file from the `export` project out dir, not the source `Exported.ts`.

That confuses RTTI which seemingly treats everything not under `node_modules` as "internal" files that don't move relative to each other.
