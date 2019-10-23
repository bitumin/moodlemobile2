## Moodle Mobile - Notes for developers

#### Clone repository

We use a simplified gitflow branches workflow:

* `master` branch for the past and currently released versions.
* `develop` branch is for future releases (always the most up to date code).
* Create new branches for new features and common fixes with the name of the
related Jira issue, for example: COFAGORA-12.
* Feature and common fixes branches are merged into `develop` branch when 
considered stable.
* When `develop` branch itself is also tested and considered stable,
it can be merged into `master` in order to prepare for a new release.
* Whenever a new version of Moodle Mobile is published, it can be merged into
a new feature branch, tested for stability and then merged to `develop` and `master`
(the remote name for the official repository is `upstream`, the usual remote is `origin`).
 
#### Prepare local enviroment before building the app

nvm (node version manager), re-open the terminal after installation:

```
curl -o- https://raw.githubusercontent.com/nvm-sh/nvm/v0.34.0/install.sh | bash
```

Install preferred node version (Moodle HQ recommends v8.12.x or v11.12.0): 

```
$ nvm ls-remote
$ nvm install [chosen version]
$ nvm use [chosen version]
```

Update npm (or install it if you don't have it yet):

```
$ npm i -g npm@latest
```

Install global node packages (important to use Cordova version 8.1.2 and not >=9.0.0):

```
$ npm i -g cordova@8.1.2
$ npm i -g ionic
```

Examples of my versions output (June 2019):
```
$ node -v       # v8.16.0 (latest LTS: Carbon)
$ npm -v        # 6.9.0
$ cordova -v    # 8.1.2 (cordova-lib@8.1.1)
$ ionic -v      # v5.0.2
```

To build using Windows you may need to install building tools dependencies if you don't have
them yet (the compilation of these tools for your system may take a long time):

```
$ npm install --global --production windows-build-tools
```

To build for iOS (with a Mac) it may be needed to install cocoapods (apart from XCode):

```
$ sudo gem install cocoapods
$ pod setup
```

At this point, do not forget to change the icons, logos, app name, etc. before preparing
for building the app, otherwise you may need to unnecessarily redo the following steps. 

Prepare the local environment (run within the project folder before trying the first build):

``` 
$ npm install
$ cordova prepare
$ gulp
```

Pay attention if there is any error output of the previous commands, specially to the 
`cordova prepare`, in case any cordova plugin is not correctly installed for Android or iOS,
settings are not correctly applied, dependencies are missing, etc.

Now you can try to build/run for iOS or Android or serve to browser for testing:

```
# Recommended steps:
$ npm run ionic:build -- --prod     # build the app for production for all platforms
$ cordova run android               # runs without rebuilding for the specified platform
# Alternative steps:
$ ionic cordova run android         # builds and runs for the specified platform (use XCode to run for iOS instead)
$ ionic cordova build android       # builds for the specified platform
```

More info, notes about AOT compilation and about other Operative Systems:
https://docs.moodle.org/dev/Setting_up_your_development_environment_for_Moodle_Mobile_2

#### Prepare new icon and splash resources for Android and iOS

Prepare icon.png and splash.png for Android and iOS
(see existing ones as a guideline in `resources` directory), then run:

```
$ ionic cordova resources
```

To build the notification icons for Android use the tool from 
https://github.com/romannurik/AndroidAssetStudio

Clone the tool repo and add support for the notification icon
densities that the current mobile app needs (for example, ldpi may be missing).
Then rebuild the tool and serve it (see the README of the tool for the how-to).
Don't forget to rename the resulting images like the existing ones (xxx-xxx-smallicon.png)
and replace them.

#### Replace Moodle app name, styles and logos within the app

It is possible to inherit a css file from the web server, but to apply styles consistently
and to avoid the user to see the Moodle app name and the Moodle icon and the orange 
corporate Moodle colors on first load it is necessary to modify the app.

More info about CSS styling: https://docs.moodle.org/dev/Moodle_Mobile_Themes

Add custom branded styles (see `src/theme/variables.scss` for info about what styles
to override):

`src/theme/bmma.scss`

Moodle icons and logos:

```
src/assets/icon/favicon.ico
src/assets/icon/icon.png
src/assets/img/login_logo.png
src/assets/img/splash_logo.png
```

Change app name, app id, user agent append, siteurl, policy url,
status bar colors, etc.:

```
desktop/assets/mac/parent.plist
desktop/assets/mac/sign.sh
desktop/assets/windows/AppXManifest.xml
desktop/electron.js
src/configconstants.ts
src/config.json
src/index.html
google-services.json
GoogleService-Info.plist
ionic.config.json
package.json
config.xml
```

### Moodle Web App

1. Build the development version of the project into www/

```
$ ionic serve browser
```

2. Copy or move the www/ within your Moodle backend directories (the usual php Moodle),
or serve the folder using the same local domain than the Moodle backend (to avoid CORS errors).

3. Copy the manifest.json and the icons pack within webresources to the root of the web app folder
(we may automate this steps in future).

To build the production version do the same but use this build command instead:

```
$ ionic build browser
```
