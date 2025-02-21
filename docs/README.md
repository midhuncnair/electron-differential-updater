# Quick start

## Install npm module

Install electron-differential-updater from npm.

```sh
  npm i @imjs/electron-differential-updater
```

or through yarn

```sh
  yarn add @imjs/electron-differential-updater
```

## Configure Publishing

Same as electron-updater [publish](https://www.electron.build/configuration/publish)

<!--2. `useAppSupportCache` : enable updater cache location inside user app support directory instead of `~Library/Application Support/Caches/Electron-updater` for mac and same for win.
"useAppSupportCache": true
-->

For Example

```json
...
"publish": [
  {
    "provider": "generic",
    "url": "https://s3-us-west-2.amazonaws.com/test",
    "channel": "latest",
    "useMultipleRangeRequest": false,

  }
],
...
```

## Import electron-differential-updater

```js
const { autoUpdater } = require("electron-differential-updater");
```

All the functionality is the same as [electron-updater](https://github.com/electron-userland/electron-builder/tree/master/packages/electron-updater) with the following added features:

1. Mac differential update for `zip`
2. `download-progress` event for differential update on win (`nsis`) and mac (`zip`).
   <!--3. `useAppSupportCache` option enables updater cache location to user app support directory.-->

## Main Issues/Features Implemented

1.  [2114](https://github.com/electron-userland/electron-builder/issues/2114) - Mac Differential update
2.  [2521](https://github.com/electron-userland/electron-builder/issues/2521) - Progress bar events for differential update

# Mac differential update(zip supported)

For differential updates to work, the module needs a blockmap. The blockmap can be generated after the successful build of mac (`zip`) through [electron-builder](https://github.com/electron-userland/electron-builder).

The `generateZipandBlockmap` function assumes the app location will be `/dist/mac/*.app` and the app name will be [productName](https://www.electron.build/configuration/configuration) configured inside build object in package.json or other forms of the electron-builder configuration.

```js
const {
  generateZipandBlockmap
} = require("@imjs/electron-differential-updater");
generateZipandBlockmap();
```

This function will do 3 things:-

1. Generate the zip with ditto(as the zip generated by electron builder does not work). [Issue 4229](https://github.com/electron-userland/electron-builder/issues/4299)
2. Generate a blockmap for the differential update.
3. Update `latest-mac.yml` sha with newly generated zip sha.

When opening \*.app (version) for first time, the module will make the zip of the app and move to cache location. For that time being `autoUpdater.checkForUpdates()` promise will throw the error saying **`Configuring update for differential download. Please try after some time`**

<!-- ```sh
ditto -c -k --sequesterRsrc --keepParent  "{appName}.app" "{appName}-{appVersion}-mac.zip"
``` -->

# Differential update download time is more than full download

Differential update donwload time is directly propertional to number of blocks change. Hence the same number of connection request will go to the server. **'So there can be cases where the whole packages(.zip,exe---> one connection request) takes less time than the differential one if the blockmaps changed are large in number.`**

# Future Roadmap

1. [4769](https://github.com/electron-userland/electron-builder/issues/4769) - Update location can be moved to user App support path
2. Differential update for dmg.

# Contributions

Please raise an issue and make a pull request if you want to contribute.

Local development environment

```sh
#Install Deps
yarn

##Compile
yarn:win or yarn:mac

##Link locally
yarn link
yarn link @imjs/electron-differential-updater
```

# Special Thanks

To [Develar](https://github.com/develar) and [electron-builder](https://github.com/electron-userland/electron-builder) community.

# Maintainers 🚀

People maintaining this project.

<!-- prettier-ignore -->
<table>
<tr>
 <td align="center"><a href="https://github.com/akshay-shrivastava"><img src="https://avatars0.githubusercontent.com/u/26062438?s=460&v=4" width="100px;" alt="Akshay Shrivastava"/><br /><sub><b>Akshay Shrivastava</b></sub></a></td>
  <td align="center"><a href="https://github.com/harshitsilly"><img src="https://avatars1.githubusercontent.com/u/9112946?s=460&v=4" width="100px;" alt="Harshit Sinha"/><br /><sub><b>harshitsilly</b></sub></a></td>
</tr>
</table>
