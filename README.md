# rollup-plugin-svelte

Compile Svelte components.


## Installation

```bash
npm install --save-dev svelte rollup-plugin-svelte
```

Note that we need to install Svelte as well as the plugin, as it's a 'peer dependency'.


## Usage

```js
// rollup.config.js
import * as fs from 'fs';
import svelte from 'rollup-plugin-svelte';

export default {
  input: 'src/main.js',
  output: {
    file: 'public/bundle.js',
    format: 'iife'
  },
  plugins: [
    svelte({
      // By default, all .html and .svelte files are compiled
      extensions: [ '.my-custom-extension' ],

      // You can restrict which files are compiled
      // using `include` and `exclude`
      include: 'src/components/**/*.html',

      // By default, the client-side compiler is used. You
      // can also use the server-side rendering compiler
      generate: 'ssr',

      // Optionally, preprocess components with svelte.preprocess:
      // https://github.com/sveltejs/svelte#preprocessor-options
      preprocess: {
        style: ({ content }) => {
          return transformStyles(content);
        }
      },

      // Extract CSS into a separate file (recommended).
      // See note below
      css: function (css) {
        console.log(css.code); // the concatenated CSS
        console.log(css.map); // a sourcemap

        // creates `main.css` and `main.css.map` — pass `false`
        // as the second argument if you don't want the sourcemap
        css.write('public/main.css');
      }
    })
  ]
}
```


## `pkg.svelte`

If you're importing a component from your node_modules folder, and that component's package.json has a `"svelte"` property...

```js
{
  "name": "some-component",

  // this means 'some-component' resolves to 'some-component/src/SomeComponent.html'
  "svelte": "src/MyComponent.html"
}
```

...then this plugin will ensure that your app imports the *uncompiled* component source code. That will result in a smaller, faster app (because code is deduplicated, and shared functions get optimized quicker), and makes it less likely that you'll run into bugs caused by your app using a different version of Svelte to the component.

Conversely, if you're *publishing* a component to npm, you should ship the uncompiled source (together with the compiled distributable, for people who aren't using Svelte elsewhere in their app) and include these properties in your package.json.


## Extracting CSS

If your Svelte components contain `<style>` tags, by default the compiler will add JavaScript that injects those styles into the page when the component is rendered. That's not ideal, because it adds weight to your JavaScript, prevents styles from being fetched in parallel with your code, and can even cause CSP violations.

A better option is to extract the CSS into a separate file. Using the `css` option as shown above would cause a `public/main.css` file to be generated each time the bundle is built (or rebuilt, if you're using rollup-watch), with the normal scoping rules applied.

Alternatively, if you're handling styles in some other way and just want to prevent the CSS being added to your JavaScript bundle, use `css: false`.


## License

MIT
