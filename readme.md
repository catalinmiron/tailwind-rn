# tailwind-rn ![Status](https://github.com/vadimdemedes/tailwind-rn/workflows/test/badge.svg)

> Use [Tailwind](https://tailwindcss.com) in [React Native](https://reactnative.dev) projects

![](header.jpg)

All styles are generated directly from Tailwind's output, so they're always up-to-date.

- JIT mode
- Responsive breakpoints (e.g. `sm`, `md`, `lg`)
- Dark mode
- Custom configuration

[Migrating from v3.x?](migrate.md)

## Install

```
$ npm install tailwind-rn
```

## Getting Started

Run the following command to automatically add `tailwind-rn` to your React Native project:

```
$ npx setup-tailwind-rn
```

It will do most of the setup for you, but you'll have to follow a few more instructions from `setup-tailwind-rn` to finish the process.

<details>
<summary>Manual setup</summary>

1. Install `tailwind-rn`.

```
$ npm install tailwind-rn
```

2. Install Tailwind and `concurrently`.

```
$ npm install --save-dev tailwindcss concurrently
```

3. Create Tailwind config and necessary files.

```
$ npx tailwindcss init
$ echo '@tailwind utilities;' > input.css
```

These commands will create the following files:

- **tailwind.config.js** - Tailwind configuration file.
- **input.css** - Entrypoint for Tailwind compiler. It's required to override the output of Tailwind, so that it doesn't generate CSS for resetting browser styles, which will cause `tailwind-rn` to fail.

Make sure to configure [`content`](https://tailwindcss.com/docs/content-configuration) part of the config in **tailwind.config.js** to point to your JavaScript files to speed up compilation.

4. Add scripts to build Tailwind styles in package.json.

```diff
{
	"scripts": {
+		"build:tailwind": "tailwindcss --input input.css --output tailwind.css --no-autoprefixer && tailwind-rn",
+		"dev:tailwind": "concurrently "tailwindcss --input input.css --output tailwind.css --no-autoprefixer --watch" "tailwind-rn --watch"
	}
}
```

5. Build Tailwind styles in watch mode.

```
$ npm run dev:tailwind
```

After styles are built, you'll see two more files:

- **tailwind.css** - CSS generated by Tailwind.
- **tailwind.json** - The same CSS, but converted into JSON, so that `tailwind-rn` can understand it.

6. Import [`TailwindProvider`](#tailwindprovider) and `tailwind.json` in the root of your app and wrap the root of your app with it:

```jsx
import {TailwindProvider} from 'tailwind-rn';
import utilities from './tailwind.json';

const App = () => (
	<TailwindProvider utilities={utilities}>
		<MyComponent />
	</TailwindProvider>
);

export default App;
```

7. Use Tailwind in React Native!

```jsx
import {useTailwind} from 'tailwind-rn';

const MyComponent = () => {
	const tailwind = useTailwind();

	return <Text style={tailwind('text-blue-600')}>Hello world</Text>;
};
```

</details>

## Usage

Use [`useTailwind`](#usetailwind) React hook and apply any of the [supported utilities](#supported-utilities) from Tailwind in your React Native views.

```jsx
import React from 'react';
import {SafeAreaView, View, Text} from 'react-native';
import {useTailwind} from 'tailwind-rn';

const Hello = () => {
	const tailwind = useTailwind();

	return (
		<SafeAreaView style={tailwind('h-full')}>
			<View style={tailwind('pt-12 items-center')}>
				<View style={tailwind('bg-blue-200 px-3 py-1 rounded-full')}>
					<Text style={tailwind('text-blue-800 font-semibold')}>
						Hello Tailwind
					</Text>
				</View>
			</View>
		</SafeAreaView>
	);
};

export default Hello;
```

<img src="screenshot.jpg" width="544">

`tailwind` function returns a simple object with styles, which can be used in any React Native view, such as `<View>`, `<Text>` and others.

```js
tailwind('pt-12 items-center');
//=> {
//     paddingTop: 48,
//     alignItems: 'center'
//   }
```

## CLI

```
$ tailwind-rn --help

  Use Tailwind CSS in React Native projects

  Usage
    $ tailwind-rn [options]

  Options
    -i, --input    Path to CSS file that Tailwind generates (default: tailwind.css)
    -o, --output   Output file (default: tailwind.json)
    -w, --watch    Watch for changes and rebuild as needed

```

Run `tailwind-rn` CLI to transform the CSS generated by Tailwind into a JSON file that can be consumed by [`TailwindProvider`](#tailwindprovider). Add `--watch` flag to watch for changes and build styles continuously, which is useful for development.

## API

### TailwindProvider

#### utilities

Type: `object`

Parsed JSON object of styles generated by `tailwind-rn` CLI stored in `tailwind.json` by default.

```jsx
import {TailwindProvider} from 'tailwind-rn';
import utilities from './tailwind.json';

const App = () => (
	<TailwindProvider utilities={utilities} theme="dark">
		<MyComponent />
	</TailwindProvider>
);
```

#### theme

Type: `string | undefined | null`

Manually set theme model `light` or `dark`.

```jsx
import {TailwindProvider} from 'tailwind-rn';
import utilities from './tailwind.json';

const App = () => (
	<TailwindProvider utilities={utilities} theme="dark">
		<MyComponent />
	</TailwindProvider>
);
```

### useTailwind

React hook, which returns a `tailwind` function, that accepts a string with class names. This function returns an object of styles, which can be applied to a React Native view via `style` property.

**Note:** Add [`TailwindProvider`](#tailwindprovider) above the component where you're using this hook.

```jsx
import {useTailwind} from 'tailwind-rn';

const MyComponent = () => {
	const tailwind = useTailwind();

	return <Text style={tailwind('text-blue-600')}>Hello world</Text>;
};
```

## Supported Utilities

### Modifiers

- [Responsive Breakpoints](https://tailwindcss.com/docs/responsive-design) (all except [multi-range breakpoints](https://tailwindcss.com/docs/responsive-design))
- [Dark Mode](https://tailwindcss.com/docs/dark-mode)
- [Prefers Reduced Motion](https://tailwindcss.com/docs/hover-focus-and-other-states#prefers-reduced-motion)
- [Viewport Orientation](https://tailwindcss.com/docs/hover-focus-and-other-states#viewport-orientation)

### Layout

- [Display](https://tailwindcss.com/docs/display) (only `hidden` and `flex`)
- [Overflow](https://tailwindcss.com/docs/overflow) (only `overflow-hidden`, `overflow-scroll` and `overflow-visible`)
- [Position](https://tailwindcss.com/docs/position) (only `relative` and `absolute`)
- [Top / Right / Bottom / Left](https://tailwindcss.com/docs/top-right-bottom-left) (all except `*-auto`)
- [Z-Index](https://tailwindcss.com/docs/z-index) (all except `z-auto`)

### Flexbox

- [Flex Direction](https://tailwindcss.com/docs/flex-direction)
- [Flex Wrap](https://tailwindcss.com/docs/flex-wrap)
- [Align Items](https://tailwindcss.com/docs/align-items)
- [Align Content](https://tailwindcss.com/docs/align-content)
- [Align Self](https://tailwindcss.com/docs/align-self)
- [Justify Content](https://tailwindcss.com/docs/justify-content)
- [Flex](https://tailwindcss.com/docs/flex)
- [Flex Grow](https://tailwindcss.com/docs/flex-grow)
- [Flex Shrink](https://tailwindcss.com/docs/flex-shrink)

### Spacing

- [Padding](https://tailwindcss.com/docs/padding)
- [Margin](https://tailwindcss.com/docs/margin)

### Sizing

- [Width](https://tailwindcss.com/docs/width) (all except `w-auto` and `w-screen`)
- [Min-Width](https://tailwindcss.com/docs/min-width)
- [Max-Width](https://tailwindcss.com/docs/max-width)
- [Height](https://tailwindcss.com/docs/height) (all except `h-auto` and `h-screen`)
- [Min-Height](https://tailwindcss.com/docs/min-height) (all except `min-h-screen`)
- [Max-Height](https://tailwindcss.com/docs/max-height) (only `max-h-full`)

### Typography

- [Font Family](https://tailwindcss.com/docs/font-family) (only custom font families that you defined in `tailwind.config.js`)
- [Font Size](https://tailwindcss.com/docs/font-size)
- [Font Style](https://tailwindcss.com/docs/font-style)
- [Font Weight](https://tailwindcss.com/docs/font-weight)
- [Font Variant Numeric](https://tailwindcss.com/docs/font-variant-numeric) (only `oldstyle-nums`, `lining-nums`, `tabular-nums` and `proportional-nums`)
- [Letter Spacing](https://tailwindcss.com/docs/letter-spacing) (must be used with font size utilities, e.g. `text-lg`)
- [Line Height](https://tailwindcss.com/docs/line-height)
- [Text Align](https://tailwindcss.com/docs/text-align)
- [Text Color](https://tailwindcss.com/docs/text-color) (all except `text-current`)
- [Text Decoration](https://tailwindcss.com/docs/text-decoration)
- [Text Decoration Color](https://tailwindcss.com/docs/text-decoration-color)
- [Text Decoration Style](https://tailwindcss.com/docs/text-decoration-style) (all except `decoration-wavy`)
- [Text Transform](https://tailwindcss.com/docs/text-transform)

### Backgrounds

- [Background Color](https://tailwindcss.com/docs/background-color)

### Borders

- [Border Color](https://tailwindcss.com/docs/border-color)
- [Border Style](https://tailwindcss.com/docs/border-style) (all except `border-current`)
- [Border Width](https://tailwindcss.com/docs/border-width)
- [Border Radius](https://tailwindcss.com/docs/border-radius)

### Effects

- [Opacity](https://tailwindcss.com/docs/opacity)

### Interactivity

- [Pointer Events](https://tailwindcss.com/docs/pointer-events)
