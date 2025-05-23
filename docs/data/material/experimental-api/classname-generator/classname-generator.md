# ClassName generator

<p class="description">Configure classname generation at build time.</p>

This API is introduced in `@mui/material` (v5.0.5) as a replacement of deprecated [`createGenerateClassName`](https://v6.mui.com/system/styles/api/#creategenerateclassname-options-class-name-generator).

:::warning
This API is at an unstable stage and is subject to change in the future.
:::

## Setup

By default, Material UI generates a global class name for each component slot. For example, `<Button>Text</Button>` generates HTML as:

```html
<button
  class="MuiButton-root MuiButton-text MuiButton-textPrimary MuiButton-sizeMedium MuiButton-textSizeMedium MuiButtonBase-root css-1ujsas3"
>
  Text
</button>
```

To customize all the class names generated by Material UI components, create a separate JavaScript file to use the `ClassNameGenerator` API.

```js
// create a new file called `MuiClassNameSetup.js` at the root or src folder.
import { unstable_ClassNameGenerator as ClassNameGenerator } from '@mui/material/className';

ClassNameGenerator.configure(
  // Do something with the componentName
  (componentName) => componentName,
);
```

and then import the file at the root of the index before any `@mui/*` imports.

```js
import './MuiClassNameSetup';
import Button from '@mui/material/Button';
// ...other component imports

function App() {
  return <Button>Text</Button>;
}
```

Here are some configuration examples:

### Change class name prefix

```js
// MuiClassNameSetup.js
import { unstable_ClassNameGenerator as ClassNameGenerator } from '@mui/material/className';

ClassNameGenerator.configure((componentName) => `foo-bar-${componentName}`);
```

As a result, the HTML result changes to the following:

```html
<button
  class="foo-bar-MuiButton-root foo-bar-MuiButton-text foo-bar-MuiButton-textPrimary foo-bar-MuiButton-sizeMedium foo-bar-MuiButton-textSizeMedium foo-bar-MuiButtonBase-root css-1ujsas3"
>
  Button
</button>
```

### Rename component class name

Every Material UI component has `${componentName}-${slot}` classname format. For example, the component name of [`Chip`](/material-ui/react-chip/) is `MuiChip`, which is used as a global class name for every `<Chip />` element. You can remove/change the `Mui` prefix as follows:

```js
// MuiClassNameSetup.js
import { unstable_ClassNameGenerator as ClassNameGenerator } from '@mui/material/className';

ClassNameGenerator.configure((componentName) => componentName.replace('Mui', ''));
```

Now, the `Mui` class is gone.

```html
<div
  class="Chip-root Chip-filled Chip-sizeMedium Chip-colorDefault Chip-filledDefault css-mttbc0"
>
  Chip
</div>
```

:::warning
[State classes](/material-ui/customization/how-to-customize/#state-classes) are not component names and therefore cannot be changed or removed.
:::

## Caveat

- `ClassNameGenerator.configure` must be called before any Material UI components import.
- you should always use `[component]Classes` for theming/customization to get the correct generated class name.

  ```diff
  +import { outlinedInputClasses } from '@mui/material/OutlinedInput';

   const theme = createTheme({
     components: {
       MuiOutlinedInput: {
         styleOverrides: {
           root: {
  -          '& .MuiOutlinedInput-notchedOutline': {
  +          // the result will contain the prefix.
  +          [`& .${outlinedInputClasses.notchedOutline}`]: {
               borderWidth: 1,
             }
           }
         }
       }
     }
   });
  ```

- This API should only be used at build-time.
- The configuration is applied to all of the components across the application. You cannot target a specific part of the application.

## Framework examples

Always create an initializer file to hoist the `ClassNameGenerator` call to the top.

```js
// create a new file called `MuiClassNameSetup.js` at the root or src folder.
import { unstable_ClassNameGenerator as ClassNameGenerator } from '@mui/material/className';

ClassNameGenerator.configure(
  // Do something with the componentName
  (componentName) => componentName,
);
```

Then import the file in the main JavaScript source based on the framework.

### Next.js Pages Router

Import the initializer in `/pages/_app.js`.

```diff
+import './MuiClassNameSetup';
 import * as React from 'react';
 import PropTypes from 'prop-types';
 import Head from 'next/head';

 export default function MyApp(props) {
   const { Component, pageProps } = props;

   return (
     <Component {...pageProps} />
   );
 }
```

### Create React App

Import the initializer in `/src/index.js`.

```diff
+import './MuiClassNameSetup';
 import * as React from 'react';
 import * as ReactDOM from 'react-dom';
 import App from './App';

 ReactDOM.render(<App />);
```
