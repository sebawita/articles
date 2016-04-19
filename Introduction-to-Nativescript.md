# What is NativeScript?

NativeScript is an OpenSource framework that allows you to build mobile apps with JavaScript.
Currently it has a full support for Android and iOS. As of 1.7 release (March 2016) it also includes "an early preview" support for Window Universal, which will cover both Windows Phone 10 and Windows 10.

At first sight this might seem very similar to Hybrid app development popularised by PhoneGap.
However NativeScript belongs to the JavaScript Native category of building mobile apps, where you use JavaScript to build apps with a 100% Native UI. This has a significant impact on the User Experience and Perfromance of the apps that you can build, with the outcome being almost identical to what you can achieve by building the same apps with a pure native code.

# Why NativeScript?

## Skills reuse
### ??? ver 1 ???
If you already know **JavaScript**, *(optionally)* **TypesScript** and **CSS**, then you already have a big chunk of skills required to build mobile apps. You don't need to learn **Xcode**, **Java** and **C#** to target multiple platforms.

### ??? ver 2 ???
NativeScript has a very strong appeal to people who use web based languages like **JavaScript**, **TypeScript** and **CSS**.

Further more if you are coming from the Angular backgroud, you coud use NativeScript with **Angular 2.0**.

##Code reuse
With NativeScript you can take advantage of a vast array of existing **npm modules** or just plain JavaScript libraries. Just remember that there is no DOM NativeScript, so the modules cannot be browser dependant.

If you need to deal with dates, just call **npm install moment** and then you can start using it like this:
```javascript
var moment = require("moment");
var formattedTime = new moment().format("HH:mm:ss");
```

You can also import existing 3rd party Native iOS and Android libraries. This means that you are not limited to what is in NativeScript out of the box, but you can add your own Native UI components or Native libraries (i.e. to communicate with your vendors beacons).

*As an example: there is a package called* **UI for NativeScript** *, which are simply 3rd party Native (iOS and Android) UI components packaged up as NativeScript UI components.*

## Direct access to the native APIs
NativeScript gives you direct access to the Native APIs, which is a big advantage over other JavaScript Native frameworks like React Native and Appcelerator.
This means that you can call any native function or use any native type that exists in each supported platform or any imported 3rd party library.

## Open Source
The team working on NativeScript is made of around 35 people, however the whole of the framework is OpenSource and it is available to everyone for free.
You get the best of the two worlds, a great piece of technology supported by a strong dedicated team and an active OpenSource community working to push the boundries of mobile app development.

# Abstraction Layer
> Need a segway into this section ???

### ver 1
NativeScript let's you target multiple platforms without you having to write a single line of a platform specific code. 
This can be achieved thanks to **tns-core-modules** (available from npm) an **Abstraction Layer** that contains platform specific implementation for each supported platfrom. It provides with modules that cover many different aspects of a mobile app from **UI abstraction**, through **Device Sensors** to **Hardaware Access** and many more.

### ver 2
With NativeScript you can build awesome apps without having to write a single line of platform specific code.
This can be achieved thanks to **tns-core-modules** (available from npm) an **Abstraction Layer** that contains platform specific implementation for each supported platfrom. It provides with modules that cover many different aspects of a mobile app from **UI abstraction**, through **Device Sensors** to **Hardaware Access** and many more.

![Abstraction Layer](./images/Abstraction-Layer.png "Abstraction Layer")

Thanks to the **tns core modules** you no longer need to think on how to perform the same operation using 3 different Platform APIs, but instead you can concentrate on the what and not how.

## UI Abstraction Layer
The Abstraction Layer not only lets you share the business logic for your app, but the same applies to the UI.
This is handled by the **UI Abstraction Layer**, which lets you define the UI using **XML** tags like **Button**, **TextField**, **DatePicker** and more. For example:

```xml
<Page>
  <StackLayout>
    <Label text="Name"/>
    <TextField text="{{nameAttribute}}"/>
    <Button text="Press Me" tap="doSomething" />
  </StackLayout>
</Page>
```

Then when you build the app, each of these tags will be replaced with their native equivalents. For example **TextField** will give you:
* on Android: **android.widget.EditText**
* on iOS: **UITextField**

> Do I need to summarize this point???
> Maybe something along the lines of write once and deploy everywhere

# Platform specific code
There might be some cases when you need to get to the platform specic code level. Like when you need to call a functionality that only exists on one platform or when you imported a 3rd party native library.

This is where the **Direct Access to the Native API** comes in to play. Like I mentioned before you can call any native function or use any native type. Just call it as if this was part of the JavaScript framework.

For example if needed to get the last modified date for a file and you had to do it with a platform specific code, then this is what your code would look like:

### for Android
```javascript
var javaFile = new java.io.File(path);
var lastModifiedDate = new Date(javaFile.lastModified());
```

### for iOS
```javascript
var fileManager = NSFileManager.defaultManager();
var attributes = fileManager.attributesOfItemAtPathError(path);
var lastModifiedDate = attributes.objectForKey(this.keyModificationTime);
```

The best thing is that this uses exactly the same namespace, attributes, types and the whole naming convention as you can find in the iOS or Android documentation. The same applies to the Native 3rd party libraries.

# How does this work

NativeScript uses a pre-packaged JavaScript Virtual Machine. 
To be precise:
  * For **Android** it is – Google’s v8 JavaScript Engine,
  * For **iOS** and **Windows Universal** it is – WebKit’s JavaScriptCore

![JavaScript Virtual Machine](./images/JavaScript-VM.png "JavaScript Virtual Machine")

Just like with most modern web browsers a JavaScript Virtual Machine is a piece of software that interprets and runs JavaScript code.
And in this case it runs {N} code.

1. The **JS Virtual Machine** interprets and executes the JavaScript code.
2.  All calls to the Native API are delegated to the **Metadata** - a pre-built set of all available APIs on each platform. The Metadata is used to lookup each method/type signature.
3. Then the **Type Conversion Module** handles data conversion from JavaScript to Native types and in reverse from Native to JavaScript types.
4. Then finally, the **Call Dispatcher** makes an actual call to the Native API and takes care of passing down the results.

![How Does This Work Diagram](./images/how-does-this-work.png "How does this work?")


# Metadata Generating Process
It is important to understand where the **Metadata** comes from.
There are too many functions and types in each platform for someone to manualy build the API translations.
But instead this is handled by the **Metadata generating processes** – the way it works, if you point it at Android SDK it will build the representation of everything in there. And the same thing happens for iOS as well.

Metadata gets rebuilt every time you build the app. And to make it better, if you add a 3rd party Native plugin/library, the Metadata generating process will also include the additional metadata.

So because of that mechanism {N} provides Day Zero support. So when a new version of a platform comes out, all you have to do is rebuild your app against that version of the platform. For instance if iOS were to introduce a **Teleport functionality**, then all you would need to do is to get the latest version of Xcode and you would be able to use it straight away. Just imagine, with a few lines of code your iPhone could bend the reality and travel through space and time.

# NPM modules ???

# CSS

# Animations

# CLI -> Livesync, debugging

# IDEs -> Telerik Platform (with zero setup and companion app livesync), Visual Studio Code

# Performance – Native UI ???

# Other 
## Getting started pointers -> http://docs.nativescript.org/getting-started
## Angular 2.0
## OpenSource
