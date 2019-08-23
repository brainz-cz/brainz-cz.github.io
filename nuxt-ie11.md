# Nuxt IE11 Guide

## Configuring Nuxt

In most cases, there should be no need to alter Babel settings in `nuxt.config.js`. By default, Nuxt [targets](https://nuxtjs.org/api/configuration-build/#babel) IE9.

### Polyfills

While Babel transpiles ES6+ features for older browsers, you might still need to manually import some polyfills. Some of the usual ones are `Object.entries`, `Array.forEach` or `IntersectionObserver`.

Watch your IE developer console for any errors such as `Object doesn't support property or method 'entries'` and find the corresponding polyfill on [polyfill.io](polyfill.io).

You can then add your polyfills to your `nuxt.config.js` like this:

```js
const polyfillFeatures = ["IntersectionObserver", "Math.trunc", "Array.from"]
const polyfillQuery = polyfillFeatures.join("%2C")

export default {
  head: {
    script: [
      {
        src: `https://polyfill.io/v2/polyfill.min.js?features=${polyfillQuery}`,
        body: true
      }
    ]
  }
}
```

### CSS Grid

Additionally, if you use `display: grid` in your code, you will need to update your PostCSS settings:

```js
export default {
  // ...
  build: {
    extend(config, ctx) {
      // ...
    },

    postcss: {
      preset: {
        autoprefixer: {
          grid: true
        }
      }
    }
  }
}
```

Be aware that IE11 does not support auto-placement. You will need to explicitly define the position of your elements:

```css
.item:nth-child(1) {
  grid-row: 1;
  grid-column: 1;
}

.item:nth-child(2) {
  grid-row: 1;
  grid-column: 2;
}
```

You can use a SCSS `for` loop to make your code more consice:

```scss
.item {
  @for $i from 1 through 4 {
    &:nth-child(#{$i}) {
      grid-row: 1;
      grid-column: $i;
    }
  }
}
```

## Testing

First, add the `prod-remote` script to your `package.json`:

```json
"scripts": {
    "dev": "nuxt",
    "build": "nuxt build",
    "start": "nuxt start",
    "generate": "nuxt generate",
    "prod-remote": "nuxt build && HOST=0.0.0.0 nuxt start"
}
```

This script will build your application and then start the production server. Running the dev server would not be enough as Babel does not transpile in dev mode. (IE11 would throw hundreds of errors.)

After running our production server, we will get a local IP address instead of the usual `localhost`. Copy this address and use it in your local PC's browser. (Either directly or through TeamViewer.)

```
   ╭────────────────────────────────────────────────╮
   │                                                │
   │   Nuxt.js v2.7.1                               │
   │   Running in production mode (universal)       │
   │   Memory usage: 18.6 MB (RSS: 53.9 MB)         │
   │                                                │
   │   Listening on: http://192.168.111.167:3000/   │
   │                                                │
   ╰────────────────────────────────────────────────╯
```

Please note that the production server does not support hot-reloading. You will need to re-run your `prod-remote` task after any change.
