---
id: styling-layout
title: Styling and Layout
description: A Docusaurus site is a pre-rendered single-page React application. You can style it the way you style React apps.
---

import ColorGenerator from '@site/src/components/ColorGenerator';

:::tip

This section is focused on styling through stylesheets. If you find yourself needing to update the DOM structure, you can refer to [swizzling](./using-themes.md#swizzling-theme-components).

:::

A Docusaurus site is a single-page React application. You can style it the way you style React apps.

There are a few approaches/frameworks which will work, depending on your preferences and the type of website you are trying to build. Websites that are highly interactive and behave more like web apps will benefit from more modern styling approaches that co-locate styles with the components. Component styling can also be particularly useful when you wish to customize or swizzle a component.

## Global styles {#global-styles}

This is the most traditional way of styling that most developers (including non-front-end developers) would be familiar with. It works fine for small websites that do not have much customization.

If you're using `@docusaurus/preset-classic`, you can create your own CSS files (e.g. `/src/css/custom.css`) and import them globally by passing them as an option into the preset.

```js title="docusaurus.config.js"
module.exports = {
  // ...
  presets: [
    [
      '@docusaurus/preset-classic',
      {
        // highlight-start
        theme: {
          customCss: [require.resolve('./src/css/custom.css')],
        },
        // highlight-end
      },
    ],
  ],
};
```

Any CSS you write within that file will be available globally and can be referenced directly using string literals.

```css title="/src/css/custom.css"
.purple-text {
  color: rebeccapurple;
}
```

```jsx
function MyComponent() {
  return (
    <main>
      <h1 className="purple-text">Purple Heading!</h1>
    </main>
  );
}
```

If you want to add CSS to any element, you can open the DevTools in your browser to inspect its class names. Class names come in several kinds:

- **Theme class names**. These class names are listed exhaustively in [the next subsection](#theme-class-names). They don't have any default properties. You should always prioritize targeting stable class names in your custom CSS.
- **Infima class names**. These class names usually follow the [BEM convention](http://getbem.com/naming/) of `block__element--modifier`. They are usually stable but are still considered implementation details, so you should generally avoid targeting them. However, you can [modify Infima CSS variables](#styling-your-site-with-infima).
- **CSS module class names**. These class names have a hash in production (`codeBlockContainer_RIuc`) and are appended with a long file path in development. They are considered implementation details and you should almost always avoid targeting them in your custom CSS. If you must, you can use an [attribute selector](https://developer.mozilla.org/en-US/docs/Web/CSS/Attribute_selectors) (`[class*='codeBlockContainer']`) that ignores the hash.

### Theme Class Names

We provide some predefined CSS class names for global layout styling. These names are theme-agnostic and meant to be targeted by custom CSS.

<details>

<summary>Exhaustive list of stable class names</summary>

```mdx-code-block
import ThemeClassNamesCode from '!!raw-loader!@site/../packages/docusaurus-theme-common/src/utils/ThemeClassNames.ts';
import CodeBlock from '@theme/CodeBlock';

<CodeBlock className="language-ts">
  {ThemeClassNamesCode
    // remove source comments
    .replace(/\/\*[\s\S]*?\*\/|\/\/.*/g,'')
    .replace(/^ *\n/gm,'')
    .trim()}
</CodeBlock>
```

</details>

### Styling your site with Infima {#styling-your-site-with-infima}

`@docusaurus/preset-classic` uses [Infima](https://infima.dev/) as the underlying styling framework. Infima provides a flexible layout and common UI components styling suitable for content-centric websites (blogs, documentation, landing pages). For more details, check out the [Infima website](https://infima.dev/).

When you scaffold your Docusaurus project with `create-docusaurus`, the website will be generated with basic Infima stylesheets and default styling. You can override Infima CSS variables globally.

```css title="/src/css/custom.css"
/**
 * You can override the default Infima variables here.
 * Note: this is not a complete list of --ifm- variables.
 */
:root {
  --ifm-color-primary: #25c2a0;
  --ifm-code-font-size: 95%;
}
```

Infima uses 7 shades of each color. We recommend using [ColorBox](https://www.colorbox.io/) to find the different shades of colors for your chosen primary color.

Alternatively, use the following tool to generate the different shades for your website and copy the variables into `/src/css/custom.css`.

<ColorGenerator/>

### Dark Mode {#dark-mode}

In light mode, the `<html>` element has a `data-theme="light"` attribute; and in dark mode, it's `data-theme="dark"`. Therefore, you can scope your CSS to dark-mode-only by targeting `html` with a specific attribute.

```css
/* Overriding root Infima variables */
html[data-theme='dark'] {
  --ifm-color-primary: #4e89e8;
}
/* Styling one class specially in dark mode */
html[data-theme='dark'] .purple-text {
  color: plum;
}
```

### Mobile View {#mobile-view}

Docusaurus uses `966px` as the cutoff between mobile screen width and desktop. If you want your layout to be different in the mobile view, you can use media queries.

```css
.banner {
  padding: 4rem;
}
/** In mobile view, reduce the padding */
@media screen and (max-width: 966px) {
  .heroBanner {
    padding: 2rem;
  }
}
```

## CSS modules {#css-modules}

To style your components using [CSS Modules](https://github.com/css-modules/css-modules), name your stylesheet files with the `.module.css` suffix (e.g. `welcome.module.css`). webpack will load such CSS files as CSS modules and you have to reference the class names from the imported CSS module (as opposed to using plain strings). This is similar to the convention used in [Create React App](https://facebook.github.io/create-react-app/docs/adding-a-css-modules-stylesheet).

```css title="styles.module.css"
.main {
  padding: 12px;
}

.heading {
  font-weight: bold;
}

.contents {
  color: #ccc;
}
```

```jsx
import styles from './styles.module.css';

function MyComponent() {
  return (
    <main className={styles.main}>
      <h1 className={styles.heading}>Hello!</h1>
      <article className={styles.contents}>Lorem Ipsum</article>
    </main>
  );
}
```

The class names will be processed by webpack into a globally unique class name during build.

## CSS-in-JS {#css-in-js}

:::caution

This section is a work in progress. [Welcoming PRs](https://github.com/facebook/docusaurus/issues/1640).

:::

## Sass/SCSS {#sassscss}

To use Sass/SCSS as your CSS preprocessor, install the unofficial Docusaurus 2 plugin [`docusaurus-plugin-sass`](https://github.com/rlamana/docusaurus-plugin-sass). This plugin works for both global styles and the CSS modules approach:

1. Install [`docusaurus-plugin-sass`](https://github.com/rlamana/docusaurus-plugin-sass):

```bash npm2yarn
npm install --save docusaurus-plugin-sass sass
```

2. Include the plugin in your `docusaurus.config.js` file:

```js title="docusaurus.config.js"
module.exports = {
  // ...
  // highlight-next-line
  plugins: ['docusaurus-plugin-sass'],
  // ...
};
```

3. Write and import your stylesheets in Sass/SCSS as normal.

### Global styles using Sass/SCSS {#global-styles-using-sassscss}

You can now set the `customCss` property of `@docusaurus/preset-classic` to point to your Sass/SCSS file:

```js title="docusaurus.config.js"
module.exports = {
  presets: [
    [
      '@docusaurus/preset-classic',
      {
        // ...
        theme: {
          // highlight-next-line
          customCss: [require.resolve('./src/css/custom.scss')],
        },
        // ...
      },
    ],
  ],
};
```

### Modules using Sass/SCSS {#modules-using-sassscss}

Name your stylesheet files with the `.module.scss` suffix (e.g. `welcome.module.scss`) instead of `.css`. Webpack will use `sass-loader` to preprocess your stylesheets and load them as CSS modules.

```scss title="styles.module.scss"
.main {
  padding: 12px;

  article {
    color: #ccc;
  }
}
```

```jsx
import styles from './styles.module.scss';

function MyComponent() {
  return (
    <main className={styles.main}>
      <article>Lorem Ipsum</article>
    </main>
  );
}
```
