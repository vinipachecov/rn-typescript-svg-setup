I've faced some struggle using svg files with React-Native so I decided to give my 2 cents on how to configure it without headaches.
The idea for it is that with typescript I've some trouble doing the whole setup so I thought it could be a good idea to document my exact steps.

For this tutorial I'll use [react-native-svg](https://github.com/react-native-community/react-native-svg#installation) for svg files and use 0.60+ version of React-Native.


## Steps
        * [Setup from Scratch](#setup-from-scratch)
        * [Svg setup](#svg-setup)

### Setup from Scratch 

Let's start with a blank new project and setup all the way until importing and using svg modules into an app.

```
react-native init myappwithsvg
```

Now to add [typescript](https://reactnative.dev/docs/typescript) to the project:
```
yarn add --dev typescript @types/jest @types/react @types/react-native @types/react-test-renderer
# or for npm
npm install --save-dev typescript @types/jest @types/react @types/react-native @types/react-test-renderer
```

Next we need to create a tsconfig.json file and add:
```json
{
  "compilerOptions": {
    "allowJs": true,
    "allowSyntheticDefaultImports": true,
    "esModuleInterop": true,
    "isolatedModules": true,
    "jsx": "react",
    "lib": ["es6"],
    "moduleResolution": "node",
    "noEmit": true,
    "strict": true,
    "target": "esnext"
  },
  "exclude": [
    "node_modules",
    "babel.config.js",
    "metro.config.js",
    "jest.config.js"
  ]
}
```

Create a separate jest.config.js file:

```js
module.exports = {
  preset: 'react-native',
  moduleFileExtensions: ['ts', 'tsx', 'js', 'jsx', 'json', 'node'],
};
```

And finally change the App.js to App.tsx. From this step forward we are done setting up typescript and we can go to the second step that is to setup the svg modules that allow us to use svg files.


### Svg setup

We will install two libraries, [react-native-svg](https://github.com/react-native-community/react-native-svg#installation) and [react-native-svg-transformer](https://github.com/kristerkari/react-native-svg-transformer#installation-and-configuration) and configure one after another. 
Let's start installing the react-native-svg:

```
yarn add react-native-svg
``` 

Install the pod related to it:
```
cd ios && pod install
```

Now we need to install the react-native-svg-transformer to make react-native able to import those files:

```
yarn add -D react-native-svg-transformer
```

Replace the metro.config.js code with:
```js
const { getDefaultConfig } = require('metro-config');

module.exports = (async () => {
  const {
    resolver: { sourceExts, assetExts },
  } = await getDefaultConfig();
  return {
    transformer: {
      babelTransformerPath: require.resolve('react-native-svg-transformer'),
    },
    resolver: {
      assetExts: assetExts.filter(ext => ext !== 'svg'),
      sourceExts: [...sourceExts, 'svg'],
    },
  };
})();
```
If you try to import files from here you will be able but typescript will give you ts error saying that your svg file could not be found.
Create a declations.d.ts file in the root of your project if you don't have yet. Paste the following code:
```ts
declare module "*.svg" {
  import { SvgProps } from "react-native-svg";
  const content: React.FC<SvgProps>;
  export default content;
}
```

