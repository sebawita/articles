# Using CodePush with NativeScript

## Introduction

Would you like to be able to dynamically update your NativeScript app that is already deployed to a phone and/or a tablet?
Think of it for a moment, sometimes you could just push a quick update without having to go through Google Play or Apple AppStore.

This is where [nativescript-code-push](https://www.npmjs.com/package/nativescript-code-push) comes in. It enables your apps to communicate with [CodePush](https://microsoft.github.io/code-push/) services (where you can push updates to the app) and dynamically update the code of the app.

CodePush allows you to push all JS, HTML/XML and CSS file updates together with all images and other resources that get bundled at time of `tns build`. 
The main limitation is around pushing native code updates that would require the app to be recompiled (like updating the NativeScript version and tns-android and tns-ios version). In this case you just need to rebuild the app and publish the update via the usual channels (app stores).

## Explaining the workflow

Here does it work:

<TURN THIS INTO A DIAGRAM>
1. Create a CodePush app - which is a placeholder where you can push all your updates

2. Build an app with NativeScript and push the v2 changes to your CodePush app

3. Your app checks for updates
3b. if updates available - download and update the code

4. The user restarts the app. 
The app starts as v2
The app checks for updates -> App is up to date

## CodePush Setup

In order to make it work we need to first setup CodePush CLI:

```
npm install -g code-push-cli
```

and then using the `code-push CLI` create a new CodePush account:

```
code-push register
```

If you already have an account with CodePush then just call the below command, which will let you authenticate with your web browser. 

```
code-push login
```

## Preparing your project for CodePush

Now that the `code-push CLI` is up and running, it is time to add it to your project.

Let's start with adding the `nativescript-code-push` module to our NativeScript project with:

```
tns plugin add nativescript-code-push
```

Then we need to use the `CodePush CLI` to register our app.
Please note that we need to do it for each platform. This is because of all the shared modules (like `tns-core-modules`), which have different implementation for iOS and Android (after all this is how we share the iOS and Android code :D ).

It is up to you how you call the CodePush app projects, but the way I like to do it is: `projectName-ios` and `projectName-android`.

```
code-push app add footy-ios
code-push app add footy-android
```

Calling each of these command will provide you with two keys, one for `Production` and one for `Staging`.
In this tutorial we will use the `Staging` keys.

![code-push-app-add](./code-push-app-add.gif?raw=true "CodePush CLI app add")

Btw. if you forget your keys you can retrieve them by calling

```
code-push deployment ls footy-ios -k
```

![code-push-find-key](./code-push-find-key.gif?raw=true "CodePush CLI find key")

## Enabling CodePush in your project

Now it comes the fun part where we can update our project to do "the thing". 

Let's start with importing the necessary `CodePush` bits and `isIOS` (to help us deliver the right key)

```
import { CodePush, SyncStatus } from 'nativescript-code-push';
import { isIOS } from 'platform';
```

Then we just need call CodePush to see if there are any updates available.

### The basic call

The simples way to do it is by calling:

```
public syncWithCodePushServer() {
  const syncKey = (isIOS) ? 'codepush-key-here-IOS' : 'codepush-key-here-ANDROID';

  CodePush.sync({ deploymentKey: syncKey });
}
```

which is enough to call the Service to look for updates, download them and update the app.

Use the `Staging` key if you want to try it out with your project.

### With the callback

You can also provide a callback function, which gets triggered for each stage of the CodePush update cycle.
The callback will provide you with a sync status, which you could use to call alert to see what is happening.

```
public syncWithCodePushServer() {
  const syncKey = (isIOS) ? 'codepush-key-here-IOS' : 'codepush-key-here-ANDROID';

  CodePush.sync(
    { deploymentKey: 'codepush-key-here' }, 
    (syncStatus: SyncStatus) => {
      alert('Sync Status:' + syncStatus);
    }
  );
}
```

This approach is good when you are adding CodePush to your project to make sure that it all works as expected. However for a production ready app, you shouldn't overwhelm your users with a ton of messages that they might not be interested in. 
You should however look for `SyncStatus.UP_TO_DATE` to prompt the user to restart the app.

```
public syncWithCodePushServer() {
  const syncKey = (isIOS) ? 'codepush-key-here-IOS' : 'codepush-key-here-ANDROID';

  CodePush.sync(
    { deploymentKey: syncKey }, 
    (syncStatus: SyncStatus) => {
      console.log('Sync Status:' + syncStatus);
      if (syncStatus === SyncStatus.UPDATE_INSTALLED) {
        alert("CodePush: update installed, kill/restart your app to see the changes");
      }
  });
}
```

Please note that it is entirely up to you how you notify the user to restart the app.
You could even use the [nativescript-toast](https://www.npmjs.com/package/nativescript-toast) plugin to make it look nicer.

## Triggering the sync process

Depending on when and how often you would want to your app to look for updates you might want to hook to different event.


You could simply add a button in the UI to let the user check for updates, which would call `codePushSync`.

Or you could try to tie in to some default events.

For example you could use the on `resumeEvent`, which would trigger the process each time the app is resumed.

```
import * as application from 'application';

application.on(application.resumeEvent, () => {
  this.syncWithCodePushServer();
});
```


### Angular

In Angular the most common location for a quick `CodePush.sync` call would be `ngOnInit` of your first view component. This way your app would run the sync process just at the beginning.

```
export class HomeComponent implements OnInit {
  // the contents of the component

  ngOnInit() {
    this.syncWithCodePushServer();
  }
}
```

## Deploying the app

First we need to deploy the app to your device. 
To deploy the app you should build it with the `--no-watch` flag. This is needed as NativeScript livesync might conflict with the CodePush sync.

```
tns run android --no-watch
tns run ios --no-watch
```

Once the app is deployed you can kill your current `tns run` process.

## Pushing the updates

Now it is the moment of truth. Let's make sine changes to the app and rebuild it.

```
tns build android --clean
tns build ios --clean
```

To push updates we need to go the respective Android and iOS build folders and push all the assets from there. Using the `release command`:

```
code-push release code-push-app-name-here app "*"
```

### Android 

Go to `platforms/android/src/main/assets/` and run

```
cd platforms/android/src/main/assets/
code-push release footy-android app "*"
```

![code-push-build-release-android](./code-push-build-release-android.gif?raw=true "CodePush CLI release android")

### iOS

Go to `platforms/ios/<nativescript-appname>/`

```
cd platforms/ios/nativescriptfooty/
code-push release footy-ios app "*"
```

![code-push-build-release-ios](./code-push-build-release-ios.gif?raw=true "CodePush CLI release ios")

## Testing

Now go back to your device. Make the app check for updates and when the updates are ready, kill the app and restart it.

## Conclusion

Using CodePush is super easy. Following this guide you should be able to have it working in less than 20 minutes.

This is definitely a great way to streamline the way you provide your users with the latest updates. However you shouldn't try to use CodePush as a replacement for Google Play or Apple AppStore.

Also please make sure that the updates that you are providing don't change the domain of the app (i.e. changing your app from picture gallery to fitness tracker), as described in `Section 3.3.2` of [iOS Program Information](https://developer.apple.com/programs/ios/information/iOS_Program_Information_4_3_15.pdf) 
