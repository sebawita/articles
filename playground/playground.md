# Life is easier with NativeScript Playground

<img src="./playground.png?raw=true" width=100% />

The NativeScript team have released a nifty little tool that is going to change the world. Well... maybe not the whole world, but at least it will help us share {N} code examples. The tool is called Playground and as the name indicates it is all "Fun and Games".

Let me give you a quick overview first, before I jump in how you could use it to make your life easier.

## What is NativeScript Playground in a sentence?

{N} Playground is a solution that allows you to write your {N} code in a web client at [play.nativescript.org](https://play.nativescript.org/) and deploy it to your device with the help of the `NativeScript Playground app` ([Android](https://play.google.com/store/apps/details?id=org.nativescript.play), [iOS](https://itunes.apple.com/us/app/nativescript-playground/id1263543946)) and the `NativeScript Preview app` ([Android](https://play.google.com/store/apps/details?id=org.nativescript.preview), [iOS](https://itunes.apple.com/us/app/nativescript-preview/id1264484702))

## The setup

### NativeScript Playground Web Client

Here comes the first surprise. 

There is no setup required to start writing your code. All you need is a modern web browser and a smile on your face (the last one is optional, but unavoidable).

> Just made sure that your browser provides support for Monaco Editor

### Your Smart Device (Android)

You just need to install:

 * [NativeScript Playground](https://play.google.com/store/apps/details?id=org.nativescript.play)
 * [NativeScript Preview](https://play.google.com/store/apps/details?id=org.nativescript.preview)

### Your Smart Device (iOS)

You just need to install:

 * [NativeScript Playground](https://itunes.apple.com/us/app/nativescript-playground/id1263543946)
 * [NativeScript Preview](https://itunes.apple.com/us/app/nativescript-preview/id1264484702)

## How to use it

### Pick your template

When you navigate to [https://play.nativescript.org/](https://play.nativescript.org/), you will be presented with a choice of 3 different templates.

<img src="./playground-templates.png?raw=true" width=600 />

### Scan the code

Once you choose the template, you will be asked to scan the QR Code with the `NativeScript Playground` app. 
This will open the app in `NativeScript Preview` app. 

<img src="./playground-scan-the-code.gif?raw=true" height=500/>

### Make changes

Now you can make changes to the code. You can easily edit all available files (`html`, `css` and `ts`).
Whenever you are ready just save the files (⌘ + S or CTRL + S) and this will automatically and immediately update your app in the `Preview`.

> In case you ask. Nope! You don't need to scan the code over and over again.

### Editing the UI

You can either insert all html tags manually or better still drag them from the `List of Components` on the left pane. This will automatically generate the necessary code. And if you drag a button then `Playground` will also generate a `onButtonTap` function in the `TypeScript` file.

### Printing to the console log

Almost every {N} app I've seen uses `console.log()` everywhere. But how do you access the terminal, if you don't use the Terminal (Mac) / Console Window (Windows)? 
The answer is, you don't need to, because Playground has a `Logs` panel, which prints all `console.log()` messages from the `NativeScript Preview` app. I've battle tested it, and it works even if you console.log 1000+ lines in one go.

### Playground in action

It is too fast to believe it is true.

<img src="./I-love-Playground.gif?raw=true" height=500/>


## Limitations

### Debugging

Currently there is no way of debugging your code with Playground. 
But who knows... the Playground team is full of talent and I wouldn't be surprised if one day they would add debugging with Chrome Dev Tools.

### Plugins

As you probably noticed, the app refreshes really quickly whenever you make changes. This is because we are only synchronising the app code. We are not however rebuilding the full app package.
This means that there is no way for you to add NativeScript plugins to a Playground project.

Last time I've checked, Playground had the following plugins:

 * nativescript-accelerometer
 * nativescript-geolocation
 * nativescript-pro-ui
 * nativescript-theme-core
 * rxjs

So you still can play with some of the plugins.

## What is Playground for?

This might surprise you a bit, however Playground is not designed to be a fully blow IDE or Development Studio. 
Like the name implies, it is a solution that allows you to play with NativeScript. Try things quickly and see the result quickly. 

Having said that this is still quite a serious tool, which can help you in many ways.

### "Help me with my code"

If you have an issue and you need help. The best way to get a good answer is to share your code. 

> However this is where the trouble starts. Because often people will share only a small part of the code and miss out on half of the important pieces; or if you upload the project to GitHub, then whoever is willing to help you, still needs to clone your repo and build the project (but this can take quite a while).

This is where Playground comes in handy.

Just create a new project and add the changes to recreate the issue. Then press the `Share` button, `Playground` will create a `snapshot` of your code and you will be presented with a new `URL`. Anyone with that URL will be able to access your `snapshot`.

If they find a good solution to your problem, then they can press the `Share` button create a new snippet and share it with you.

And this is not how it works in theory. I've helped a bunch of people who have shared their `Playground` snippets with me.

<img src="./Playground-Share.gif?raw=true" height=500/>

### Getting hands on

Playground also makes for quite a powerful tool to help anyone just dive into `NativeScript`. 
In the past it would take an hour or so to get the whole tooling installed and configured, but with Playground anyone could dive into `NativeScript` in less than 5 minutes.

And if you are an advanced `NativeScript` developer, then you could use `Playground` to teach others how to build mobile apps.

### Workshops, tutorials, documentation

Following on the previous point, we are going to use Playground to help us share knowledge. So expect to see a lot of Playground `QR codes` everywhere where we are. Be it in workshops, tutorials or documentation. This is simply the easiest and quickest way for us to share snack-bite size code examples with you.

### Vue

<img src="./Vue.png?raw=true" height=200/>

Don't tell anyone (who am I kidding?), but you can even play with `NativeScript Vue` in Playground. Insane!!! Right?

Just open this url [https://play.nativescript.org/?template=play-vue](https://play.nativescript.org/?template=play-vue) and you are good to go.

To learn more about `NativeScript Vue` read
[native-ios-android-vue-nativescript](https://developer.telerik.com/products/nativescript/native-ios-android-vue-nativescript/) article by @tjvantoll.

## Final word

Give it a try to Playground and you will immediately see why we are so excited about it. 

We are still developing it and we hope to add more cool features to Playground. Like soon we are planning to add a functionality to allow you to add new files.

Do you have any suggestions on how we could make this a better tool for everyone? Maybe there is a plugin that you think we need? Give us a shout in the comments section.