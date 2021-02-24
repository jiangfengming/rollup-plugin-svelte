# vite-plugin-svelte-ssr-hot

Compile Svelte components for Vite.
Forks from the official [rollup-plugin-svelte](https://github.com/sveltejs/rollup-plugin-svelte).
Supports HMR (Hot Module Replacement) and SSR (Server-Side Rendering).


## Installation

```bash
npm install --save-dev svelte vite-plugin-svelte-ssr-hot
```

Note that we need to install Svelte as well as the plugin, as it's a 'peer dependency'.


## Usage

```js
// vite.config.js
import svelte from 'vite-plugin-svelte-ssr-hot';

export default ({ command }) => {
  const isDev = command === 'serve';

  return {
    plugins: [
      svelte({
        hot: isDev,

        compilerOptions: {
          hydratable: true // true for SSR, false for CSR (Client-Side Rendering)
          // `generate` option will be auto set.
        }
      })
    ],
    
    resolve: {
      dedupe: ['svelte']
    },

    ssr: {
      external: ['svelte']
    },

    optimizeDeps: {
      exclude: ['svelte']
    }
  };
};

```


## Preprocessing and dependencies

If you are using the `preprocess` feature, then your callback responses may — in addition to the `code` and `map` values described in the Svelte compile docs — also optionally include a `dependencies` array. This should be the paths of additional files that the preprocessor result in some way depends upon. In Rollup 0.61+ in watch mode, any changes to these additional files will also trigger re-builds.


## `pkg.svelte`

If you're importing a component from your node_modules folder, and that component's package.json has a `"svelte"` property...

```js
{
  "name": "some-component",

  // this means 'some-component' resolves to 'some-component/src/SomeComponent.svelte'
  "svelte": "src/MyComponent.svelte"
}
```

...then this plugin will ensure that your app imports the *uncompiled* component source code. That will result in a smaller, faster app (because code is deduplicated, and shared functions get optimized quicker), and makes it less likely that you'll run into bugs caused by your app using a different version of Svelte to the component.

Conversely, if you're *publishing* a component to npm, you should ship the uncompiled source (together with the compiled distributable, for people who aren't using Svelte elsewhere in their app) and include the `"svelte"` property in your package.json.

If you are publishing a package containing multiple components, you can create an `index.js` file that re-exports all the components, like this:

```js
export { default as Component1 } from './Component1.svelte';
export { default as Component2 } from './Component2.svelte';
```

and so on. Then, in `package.json`, set the `svelte` property to point to this `index.js` file.

## License

MIT
