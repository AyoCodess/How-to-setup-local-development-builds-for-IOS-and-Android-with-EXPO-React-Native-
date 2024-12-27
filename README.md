##### By Ayo Adesanya

## Before you get started... 


1. This guide is was created with a Apple Mac. If you have a windows machine, terminal commands may be different.
2. i have use `yarn` to install packages but you can use any package manager
3. A reference to all the package.json scripts are at the bottom of this document.

## Content Navigation

- [Getting Started](#getting-started)
  - [Quick Start!](#quick-start)
  - [Create icon and splash screen graphics for app ](#create-icons-for-app)
  - [IOS - Setup local environment for builds and E2E testing](#ios-setup-local-environment-for-builds-and-e2e-testing-mac)
    - [Trouble-shooting](#ios-trouble-shooting)
    - [Run tests](#ios-run-tests)
  - [Android - Setup local environment for builds and E2E testing](#android-setup-local-environment-for-builds-and-e2e-testing)
  - [Setup Android emulator for development builds](#setup-android-emulator-for-development-builds)
    - [Run tests](#android-run-tests)
    - [Create service account](#create-service-account)
  - [Tests - CI/CD](#tests-ci-cd)
  - [Android Internal Testing set-up](#android-internal-testing-set-up)
  - [Known issues](#known-issues)
  - [Package.json script definitions](#packagejson-script-definitions)
    - [Build your app locally](#build-your-app-locally)
    - [Build your app with EAS services](#build-your-app-with-eas-services)
    - [Submit builds to app stores](#submit-builds-to-app-stores)
    - [Deploy & submit to app stores](#deploy--submit-to-app-stores)
    - [Tests](#tests)
  - [View UI components in isolation](#view-ui-components-in-isolation)
  - [Setting up Clerk Auth for Production](#setting-up-clerk)
  - [General trouble shooting](#general-trouble-shooting)
- [de-bug production IOS app](#debug-production-ios)
- [de-bug production Android app](#debug-production-android)

### Quick Start! - Run app in Expo go without dev client server and e2e testing. {#quick-start}

Note: You must have Xcode and Android studio installed on your machine. The links below provide the Expo instructions.

- [Install Xcode](https://docs.expo.dev/workflow/ios-simulator/)
- [Install Android Studio](https://docs.expo.dev/workflow/android-studio-emulator/)

install EAS client globally

```bash
npm install --global eas-cli
```

After you have installed the tools above then run the commands below.

```bash
yarn install
yarn start
```

### Create icon and splash screen graphics for app {#create-icons-for-app}

Download the figma template, use the guide to help you if necessary. Once you have created your icons replace the icons in the `assets` folder with your own.

- [Download Figma Template](https://www.figma.com/community/file/1155362909441341285)
  - [Figma Expo Reference Guide ](https://docs.expo.io/guides/app-icons/)

Replace your the images in the `/assets` folder with your new images.

## IOS - Setup local environment for builds and E2E testing (MAC) {#ios-setup-local-environment-for-builds-and-e2e-testing-mac}

Before you try to build the IOS app on your machines infrastructure you must have the following packages installed on your machine.

We may need to create the same environment as the EAS build environment which runs on the following package versions. You can try to start a build without these versions but if you run into issues you may need to install these versions.

```
- Xcode 14.2 (14C18)
- Node.js 16.18.1
- npm 8.1.2
- fastlane 2.211.0
- CocoaPods 1.11.3
- Ruby 2.7
- Expo CLI 6.0.0
```

1. Xcode
   [Install Xcode](https://docs.expo.dev/workflow/ios-simulator/)

2. Cocopods

```bash
sudo gem install cocoapods
```

````bash

3. Fastlane

```bash
sudo gem install fastlane -NV
````

##### Now you can run `yarn local-dev-i` to build the IOS app with expo dev client.

4. Maestro (E2E Testing)

Follow the documentation and install all the dependencies for maestro.

[Install maestro](https://maestro.mobile.dev/getting-started/installing-maestro)

add this to your `.zshrc` or `.bashrc` files.

```bash
export PATH=$PATH:$HOME/.maestro/bin
```

##### Trouble shooting {#ios-trouble-shooting}

#### Trouble-shooting-cocoapods

If you run `yarn local-dev-i` and you get an error like this `error: The sandbox is not in sync with the Podfile.lock...` or if you run into an issue concerning `SocketRocket` run this command `pod repo update` or `pod install --repo-update`

You will get some feedback in the terminal which may ask you to run something like this `gem pristine ffi --version 1.15.5`, run it. and that should fix the error.

##### Note: you need to have login credentials for EAS services! before you can do the next step.

5. Now attempt to run `yarn local-dev-i` to build the IOS app with expo dev client.

We need to build an app with dev client enabled so we can e2e test with maestro while live coding.

If you run into a credential issue while running `yarn local-dev-i` check this out and [update your keychain apple credentials](https://github.com/expo/eas-cli/issues/1331).

6. Once the `yarn local-dev-i` command is complete you will get a `.tar.gz` file. The path for the file will be displayed in your terminal. Unzip it, and drag the .app file into your IOS emulator. It will install. You can also install this app on a physical device via itunes.

7. Now you can run `yarn dev` and run the development build (the build you just created).

we use `yarn dev` instead of `yarn start` so we run tests while we code using maestro. Testing only works on IOS simulator as of 1/5/2023

```bash
yarn dev
```

8. Once the expo dev-client server is running on your IOS simulator you can run `yarn cosmos`, This will allow you to open cosmos playground at http://localhost:500\* and view components in isolation. The URL will appear in the terminal.

```bash
yarn cosmos
```

###### Note: to run `yarn dev` you need to have built a development build via `yarn local-dev-a` and installed the build on the simulator or physical device.

### IOS - Run tests {#ios-run-test}

Open a new terminal; If you want to run tests while you develop you must run `yarn dev` NOT `yarn start`. This will start a dev-client-sever allowing maestro to orchestrate tests against the app.

cd into the app root directory and run the example test file to see this in action `yarn t`. The test should run in your terminal and should pass.

```bash
yarn t
```

to run all tests in the `tests` folder at the same time run `yarn test`

```bash
yarn test
```

With the help of maestro studio you can build tests easily and quickly! [Read about maestro studio](https://maestro.mobile.dev/getting-started/maestro-studio)

```bash
yarn studio
```

## Android - Setup local environment for builds and E2E testing (MAC) {#android-setup-local-environment-for-builds-and-e2e-testing}

##### Note: Depending on your machines current environment state you may need to uninstall your version of Java or Gradle and set paths to the android SDK in your `.zshrc` or `.bashrc` files.

We may need to create the same environment as the EAS build environment which runs on the following package versions. You can try to start a build without these versions but if you run into issues you may need to install these versions.

```
- NDK 21.4.7075529
- Node.js 16.18.1
- Java 11
- Expo CLI 6.0.0
```

1. You must have android studio installed on your machine and at least one android emulator installed.

- [Install Android Studio](https://docs.expo.dev/workflow/android-studio-emulator/)

2. Check you have the right gradle version installed

```bash
gradle -v
```

If you do not have gradle 7.5.1 uninstall your current version and install gradle via SDKMAN

```bash
brew uninstall gradle
```

- DO THIS BEFORE YOU INSTALL GRADEL! [install SDKMAN first!](https://sdkman.io/install)

```bash
sdk install gradle 7.5.1
```

3. Now we need to check what Java version you are running on your machine. It's likely that it's not Java 11 which is what we need to build andriod apps locally.

```bash
java -version
```

To uninstall Java correctly you need to [download the Java installer](https://www.java.com/en/download/) run the file and select `remove`. This option will uninstall your current version if Java.

4. Once it Java is removed you can install the version needed to build. Download the version of Java 11 needed for your MAC machine.

You may need to sign up or oracle for free before you can download the installer.

- [Java 11 Installer](https://www.oracle.com/java/technologies/javase/jdk11-archive-downloads.html)

7. Now we must add the paths to the android SDK to your your `.zshrc` or `.bashrc` files

copy and paste this code into your file and save

```bash
 export ANDROID_HOME=~/Library/Android/Sdk
 export PATH=$PATH:$ANDROID_HOME/emulator
 export PATH=$PATH:$ANDROID_HOME/tools
 export PATH=$PATH:$ANDROID_HOME/tools/bin
 export PATH=$PATH:$ANDROID_HOME/platform-tools
```

8. In your terminal run `source ~/.zshrc` or `source ~/.bashrc`

##### Note: you need to have login credentials for EAS services! before you can do the next step.

9. Finally you can run the command below and see if you can build a android app successfully.

```bash
yarn local-dev-a
```

10. Once the command is finished it will install the app on your emulator and give you a QR code and URL to open the app on a physical android device.

11. Run these commands to start the dev-client-server and cosmos playground.

Now you can run `yarn dev` and run the development build.

```bash
yarn dev
```

After the dev-client server is running in your android simulator you can run `yarn cosmos`, This will allow you to open cosmos playground at http://localhost:500\* and view components in isolation. The URL will appear in the terminal.

```bash
yarn cosmos
```

###### Note: to run `yarn dev` you need to have built a development build via `yarn local-dev-a` and installed the build on the simulator or physical device.

### Android - Run tests {#android-run-tests}

Open a new terminal; If you want to run tests while you develop you must run `yarn dev` NOT `yarn start`. This will start a dev-client-sever allowing maestro to orchestrate tests against the app.

cd into the app root directory and run the example test file to see this in action `yarn t`. The test should run in your terminal and should pass.

```bash
yarn t
```

to run all tests in the `tests` folder at the same time run `yarn test`

```bash
yarn test
```

With the help of maestro studio you can build tests easily and quickly! [Read about maestro studio](https://maestro.mobile.dev/getting-started/maestro-studio)

```bash
yarn studio

```

### Setup Android emulator for development builds {#setup-android-emulator-for-development-builds}

1. Open Android studio
2. Open of virtual devices manager
3. Create a new virtual device
4. Select a device with the google play store icon

[follow this guide if you need more help](https://stackoverflow.com/questions/38593738/is-google-play-store-supported-in-avd-emulators)

### Tests CI/CD {#tests-ci-cd}

TBA
Reference: https://cloud.mobile.dev/

### Android internal testing set up {#android-internal-testing-set-up}

to set up internal testing go into your google play store app and enable internal testing for the app.

1. If you do not have developer options enabled on your android device you will need to enable them. To do this go to `settings > about phone > software information > tap build number 7 times`. This will enable developer options on your device.
2. Now go to `settings > developer options > internal app sharing`
3. Enable internal app sharing and copy the link to the app from [Google Play Console](https://play.google.com/console/)
4. You need to accept the invite via your phone into the developer program. You can do this by clicking the link in the email you received from google play console.
5. Once you have accepted the invite you can open the link in your phone and install the app. To get the link use the `web version` from the `internal test` section, click the `testers` tab and copy the link from the there, remember to access these links from your phone.

### Known issues {#known-issues}

2. As Cosmos is still in alpha you may encounter a `cannot connect to renderer` error. If you get any errors you can run `yarn clean` and then `yarn i`

### Package.json script definitions {packagejson-script-definitions}

package manager we recommend is `yarn`

- `i`: Installs dependencies
- `start`: Starts the app via Expo Go
- `dev`: Starts the app via Expo Go using the starts the dev-client-server
- `clean`: Deletes node_modules
- `doctor`: Checks for issues with your project and dependencies
- `studio`: Opens maestro studio (GUI for building tests)
- `rm-build`: Delete all app builds from file system
- `build`: runs the shell script `build.sh` that updates the app version in package.json, creates a versioned git commit, creates a versioned git tag and builds a app store ready app locally for IOS ready for deployment to the app store. (run `yarn local-dev-a`) after there script has executed in a different terminal window to build the android app.

##### Build your app locally {#build-your-app-locally}

- `local-dev-i`: Builds a development build for iOS using your local machine infrastructure
- `local-dev-a`: Builds a development build for Android using your local machine infrastructure
- `local-prod-i`: Builds a production build for iOS using your local machine infrastructure
- `local-prod-a`: Builds a production build for Android using your local machine infrastructure
- `local-app-store`: Builds a production build for iOS app store distribution using your local machine infrastructure.
- `local-google-store`: Builds a production build for Android play store distribution using your local machine infrastructure.

##### Build your app with EAS services {#build-your-app-with-eas-services}

prefix these commands with `yarn`.

- `cloud-dev-i`: Builds a development build for iOS using EAS infrastructure
- `cloud-dev-a`: Builds a development build for Android using EAS infrastructure
- `cloud-prod-i`: Builds a production build for iOS using EAS infrastructure
- `cloud-prod-a`: Builds a production build for Android using EAS infrastructure
- `cloud-app-store`: Builds a production build for iOS app store distribution using EAS infrastructure.
- `cloud-google-store`: Builds a production build for Android play store distribution using EAS infrastructure.

##### Submit builds to app stores {#submit-builds-to-app-stores}

`submit` commands require you to have a build already built and ready to submit in EAS cloud.

(you need to have a build already in EAS services to use these commands)

- `submit-i`: Submits a production build for iOS app store distribution using EAS infrastructure.
- `submit-a`: Submits a production build for Android play store distribution using EAS infrastructure.
- `submit-all`: Submits a production build for iOS and Android app store distribution using EAS infrastructure.

#### Deploy & submit to app stores {#deploy--submit-to-app-stores}

(creates builds using cloud services and submits to app stores automatically)

- `deploy-i`: Build IOS app and submit to app store
- `deploy-a`: Build Android app and submit to play store

##### Tests {#tests}

- `test:` Runs all tests in the tests folder
- `t`: Runs the example test file in the tests folder
- `watch-fix`: Fixes any issues with the watchman package

##### View UI components in isolation {#view-ui-components-in-isolatio}

- `cosmos`: Runs cosmos playground (view components in isolation)

### Android - Create service account {#create-service-account}

You need to generate a service account to use the `submit-a` and `submit-all` commands.

- https://github.com/expo/fyi/blob/main/creating-google-service-account.md

##### Setting up Clerk Auth for Production - version: 0.18.17 {#setting-up-clerk}

1. [Follow the setup guide for expo](https://docs.clerk.dev/guides/expo)
2. Login to your Clerk account and go to the `social connections` tab and set up your auth as you want.
3. Click the `sessions` tab and add these to `redirect URLs`

```
acme://oauth-callback
acme://oauth-native-callback
acme://oauth-native-callback#
exp://192.168.1.1/oauth-callback

```

These will allow you to use the production setup in the the dev-client-server and in production.

### General trouble shooting {#general-trouble-shooting}

##### If `Expo go` hangs on the splash screen run

```bash
yarn watch-fix
```

##### If you get a watchman error and the app errors out

```bash

watchman shutdown-server
```

and restart your server.

##### AsyncStorage issues

1. yarn remove `@react-native-async-storage/async-storage`
2. yarn remove cache
3. yarn clean
4. yarn add `@react-native-async-storage/async-storage`
5. delete development build and create a new one
6. run app

### de-bug production IOS app {#debug-production-ios}

1. Open mac's `console` app
2. Connect your phone to your computer
3. Open the app on your phone
4. In the `console` app, search for your apps process name in to focus in on the apps activity.
5. start debugging.

### de-bug production android app {#debug-production-android}

1. Make sure you have `developer options` enabled and enable "USB Debugging"
2. run while your phone is connected to your computer `adb devices`
3. run `adb logcat` to see the logs from your phone as you interact with the app.
4. start debugging.

####  Please leave comments if you have any issues, questions or suggestions.

## Translations

1. `npm install -g i18nexus-cli`
2. `yarn translations` (You do not have to run this, unless you are working with translations or need to re-download them)
