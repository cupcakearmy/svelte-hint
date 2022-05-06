## SETUP

Shamefully copied from the [carbon team](https://github.com/carbon-design-system/carbon-charts/tree/master/packages/svelte#set-up)

### SvelteKit

[SvelteKit](https://github.com/sveltejs/kit) is fast becoming the de facto
framework for building Svelte apps that supports both client-side rendering
(CSR) and server-side rendering (SSR).

For set-ups powered by [vite](https://github.com/vitejs/vite), add
`svelte-hint` to the list of dependencies for `vite` to optimize.

If using a [SvelteKit adapter](https://kit.svelte.dev/docs#adapters), instruct
`vite` to avoid externalizing `svelte-hint` when building for production.

```js
// svelte.config.js
import adapter from '@sveltejs/adapter-node'

const production = process.env.NODE_ENV === 'production'

/** @type {import('@sveltejs/kit').Config} */
const config = {
  kit: {
    adapter: adapter(),
    target: '#svelte',
    vite: {
      optimizeDeps: {
        include: ['svelte-hint'],
      },
      ssr: {
        noExternal: [production && 'svelte-hint'].filter(Boolean),
      },
    },
  },
}

export default config
```

### Vite

[vite-plugin-svelte](https://github.com/sveltejs/vite-plugin-svelte) is an
alternative to using SvelteKit. Similarly, instruct `vite` to optimize
`svelte-hint` in vite.config.js.

Note that `@sveltejs/vite-plugin-svelte` is the official vite/svelte integration
not to be confused with [svite](https://github.com/svitejs/svite).

```js
// vite.config.js
import { svelte } from '@sveltejs/vite-plugin-svelte'
import { defineConfig } from 'vite'

export default defineConfig(({ mode }) => {
  return {
    plugins: [svelte()],
    build: { minify: mode === 'production' },
    optimizeDeps: { include: ['svelte-hint'] },
  }
})
```

### Sapper

[sapper](https://github.com/sveltejs/sapper) is another official Svelte
framework that supports server-side rendering (SSR).

Take care to install `svelte-hint-svelte` as a development dependency.

No additional configuration should be necessary.

### Rollup

#### ReferenceError: process is not defined

Install and add
[@rollup/plugin-replace](https://github.com/rollup/plugins/tree/master/packages/replace)
to the list of plugins in `rollup.config.js` to avoid the
`process is not defined` runtime error.

This plugin statically replaces strings in bundled files with the specified
value.

In the example below, all instances of `process.env.NODE_ENV` will be replaced
with `"production"` while bundling.

```js
// rollup.config.js
import replace from '@rollup/plugin-replace'

export default {
  // ...
  plugins: [
    replace({
      preventAssignment: true,
      'process.env.NODE_ENV': JSON.stringify('production'),
    }),
  ],
}
```

#### `this` has been rewritten to `undefined`

Set [`context: "window"`](https://rollupjs.org/guide/en/#context) to address the
`this has been rewritten to undefined` Rollup error.

```diff
export default {
+  context: "window",
};
```

#### Circular dependency warnings

You may see circular dependency warnings for `d3` and `svelte-hint` packages
that can be safely ignored.

Use the `onwarn` option to selectively ignore these warnings.

```js
// rollup.config.js
export default {
  onwarn: (warning, warn) => {
    // omit circular dependency warnings emitted from
    // "d3-*" packages and "svelte-hint"
    if (warning.code === 'CIRCULAR_DEPENDENCY' && /^node_modules\/(d3-|svelte-hint)/.test(warning.importer)) {
      return
    }

    // preserve all other warnings
    warn(warning)
  },
}
```

#### Dynamic imports

If using [dynamic imports](https://rollupjs.org/guide/en/#dynamic-import), set
`inlineDynamicImports: true` in `rollup.config.js` to enable code-splitting.

Otherwise, you may encounter the Rollup error
`Invalid value "iife" for option "output.format" - UMD and IIFE output formats are not supported for code-splitting builds.`

```diff
export default {
+  inlineDynamicImports: true,
};
```

### Webpack

[webpack](https://github.com/webpack/webpack) is another popular application
bundler used to build Svelte apps.

No additional configuration should be necessary.

### Snowpack

[snowpack](https://github.com/snowpackjs/snowpack) is an ESM-powered frontend
build tool.

Ensure you have
[@snowpack/plugin-svelte](https://yarnpkg.com/package/@snowpack/plugin-svelte)
added as a plugin in `snowpack.config.js`.

No additional configuration should be necessary.

```js
// snowpack.config.js

/** @type {import("snowpack").SnowpackUserConfig } */
module.exports = {
  plugins: ['@snowpack/plugin-svelte'],
}
```
