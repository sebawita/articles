# Intro to code sharing of web and mobile code with Angular and NativeScript

NativeScript solves one of the big problems of mobile development. That is building for both iOS and Android with a single source code.

One of the big challenges that NativeScript is designed to solve is the code sharing between iOS and Android projects. Meaning you can build mobile apps with JavaScript/TypeScript or Angular, which use the same code for both platforms. 
However the marriage of Angular with NativeScript allows us to take it a step further, which is the code sharing of the NativeScript and Web applications.

## How is that possible?

![How is that possible](./images/Garfield-how-is-that-possible.gif?raw=true "How is that possible")

Angular is a platform agnostic framework, where most of it's building blocks are not specific to Web, mobile or desktop. 
Let's have a look at an example of a simple `WeatherService`, which has one function that returns an Observable with the weather for a given location.

```
@Injectable()
export class CheckWeatherService {
  constructor(private http: Http) { }

  checkWeather(location: string): Observable<any> {
    return this.http.get(`http://api.magicweather.org/${location}`)
      .map(response => response.json());
  }
}
```

This service could be perfectly used with both Web and Mobile applications. The only piece that you would expect to behave different between various platforms (iOS, Android, Web) is the `Http` module. 
Here is where the first piece of magic starts. NativeScript has an `Http module` that provides the abstraction for the calls required to issue an `http.get` command (amongst all the other http commands) natively on iOS and Android. So that when you build the above service for an iOS app the `http.get` call will result in native iOS calls being issued, while building it for Android will result in native Android calls.

![Http NativeScript](./images/Http-NativeScript.png?raw=true "Http NativeScript")

However from the perspective of web/mobile code sharing we just use Angular's `Dependency Injection` to provide the right `HttpModule` into our project. 
So for a web project we use `import { HttpModule } from '@angular/http';` while for a NativeScript project we use `import { NativeScriptHttpModule } from 'nativescript-angular/http';`. 
 
This takes to the bigger picture where we look at the `HttpModule` as one module that provides us with everything we need to make http calls, which will work exactly the same for both web and mobile projects.

![Http Angular](./images/Http-Angular.png?raw=true "Http Angular")

## What can be shared

![Sharing](./images/Garfield-sharing.jpg?raw=true "Sharing")

Sharing services is not where this all ends. You can easily share:
 * Services
 * Component class definition - that is the `xyz.component.ts`
 * Pipes
 * Router configuration
 * SCSS variables

## What can't be shared

![Can't be shared](./images/Garfield-cant-share.png?raw=true "Can't be shared")

Obviously not everything can be shared. The main idea is that you cannot share anything that specific to Web or Mobile projects.

From the perspective of an Angular project, your differences will be mostly around:
 * Component UI definitions - that are the `xyz.component.html` and `xyz.component.scss`
 * Modules that have different implementation for web or mobile, like [angularfire2](https://www.npmjs.com/package/angularfire2) vs [nativescript-plugin-firebase](https://www.npmjs.com/package/nativescript-plugin-firebase)
 * DOM dependent operations - these will work in a browser, but not in {N}
 * Mobile specific UX - like page transition effects
 * UI components - for example you would need to use two different libraries to add data charts to your mobile/web apps.

## Stating the challenge

![The Challenge](./images/Garfield-challenge.jpg?raw=true "The Challenge")

There seems to be a lot of overlap between Angular for web and Angular with NativeScript.
So what is stopping us from diving into it? 

The thing is that at the moment the Angular CLI can be used to generate web projects only and for NativeScript you need to use the NativeScript CLI. This is a problem because the CLI's don't provide us with a mechanism that would let us use a single project capable of switching between web and mobile. 

<SarcasticTone>Sure we could have two separate projects and copy and paste the shareable files between the two.</SarcasticTone> However that would only work for small projects.

So what we need is an `Angular Seed` with a `build process` that would help us manage both web and NativeScript projects. 
It should allow us to:
 * define shared and platform specific files,
 * easily navigate the project folder structure,
 * manage npm packages separately for web and mobile,
 * automate the build processes for both web and mobile,
 * use the productivity tools - like Angular CLI for web or live sync for NativeScript

## The hero emerges

![Hero](./images/Garfield-hero.gif?raw=true "Hero")

There is a number of such seeds, like [angular-seed-advanced](https://github.com/NathanWalker/angular-seed-advanced), [peek-web-ns](https://github.com/Synerty/peek-web-ns), [angular2-webpack-advance-starter](https://github.com/JonnyBGod/angular2-webpack-advance-starter), [angular-starter](https://github.com/jlooper/angular-starter), [nativescript-ng2-starter-kit](https://github.com/dlucidone/nativescript-ng2-starter-kit). So it is really easy to get lost in choice. 

However my favourite seed project is [angular-native-seed](https://github.com/TeamMaestro/angular-native-seed) from `TeamMaestro`.

### Shared and platform specific files

Like most similar seeds it uses a naming convention to define which files should be shared or not. Let's have a look at an `ExampleComponent` with one shared `ts` file and two `html` files. The shared `ts` file should be simply named: `example.component.ts`, while the two `html` files should be named `example.component.html` (for the web version) and `example.component.tns.html` (for the mobile version).

example
 |- example.component.ts
 |- example.component.html
 |- example.component.tns.html

Like you probably noticed, the trick is to add `.tns` before any file extension to make it NativeScript specific, while the other file becomes web specific. 
So if you want to have two `scss` files for our module, then you should create `example.component.scss` and `example.component.tns.scss`. However if you want to share the styling file then you should have just `example.component.scss` only.

### Understanding the project structure and managing the npm modules

From the code sharing perspective the project is made of the following files and folders (to help focus on what is important I omitted a few files and folders).

seed-root:
 |- src
    |- app.module.ts
    |- app.module.tns.ts
 |- nativescript
    |- app
    |- src
    |- node_modules
    |- package.json
 |- node_modules
 |- package.json

We have two pairs of `node_modules` and `package.json`. The first pair at the root is designated for the `web` project, while the one in the `nativescript` folder is used for the `NativeScript` project. 
To install an npm package for the web project, just run all `npm commands` from the `root` folder and for the NativeScript project run the commands from the `nativescript` folder. If you need the same npm module in both, then you need to run the npm command twice.

The `src` folder at the `root` of our project is where all of our code goes into - both for web and mobile (.tns) - as this is our working folder. Here you will find services, components, pipes, etc. which form your application.

Also amongst others we have two versions of the `app.module`, which are used to provide all the shared and platform specific modules. A good example for platform specific module is the `HttpModule`. Where `app.module.ts` imports:

```
import { HttpModule } from '@angular/http';

@NgModule({
  ...
  imports: [
    HttpModule
    ...
  ]
```

while `app.module.tns.ts` imports:

```
import { NativeScriptHttpModule } from 'nativescript-angular/http';

@NgModule({
  ...
  imports: [
    NativeScriptHttpModule
    ...
  ]
```

Next there is the `nativescript` folder with the `app` and `src` folders. 
The `nativescript/src` folder is symlinked to the `root/src`, which displays all the files that are specific to the NativeScript project, while ignoring the web only files. For example the `ExampleComponent` would look like this (notice that there is no `example.component.html):

example
 |- example.component.ts
 |- example.component.tns.html

If you are new to `symlink`, it is worth understanding that symlink creates a different view of one folder to another. 
This means that if you make any changes to any of the files in this folder, this will reflect in their `roor/src` counterparts being updated as well. As a matter of fact these are not actually counterparts, these ARE the same files (not copies).

Finally we have the `nativescript/app` folder. This is where the gulp build process (more on it below) outputs the NativeScript ready files, which comes from `nativescript/src`. This is a good place to come to see what ends up in your nativescript folder and in what form. Also this is `THE` folder that is actually used for the NativeScript build processes (read: all tns commands run on the code here).

### Using the productivity tools

The way this seed is structured makes it really easy to use the best of Angular and NativeScript `productivity tools`. 
There is virtaully no limit in terms of which tools you can use. The only thing is where to run the tools from. 
So for all web tools (like Angular CLI) run them from the `root` folder and for all NativeScript tools -> run them from the `nativescript` folder.

Would you like to use Angular CLI to create a service? Just run `ng g s service-name` from the `root` and you will get all the building blocks in place. Just note that only the `app.module.ts` providers will be updated, so you will have to manually update `app.module.tns.ts` to also make this service available in the NativeScript app.

To debug a NativeScript application, just go into the NativeScript folder and run `npm run livesync`, then in a separate terminal (command line) run `tns debug ios` or `tns debug android`. This will not only run the debugging tools for NativeScript, but it will also refresh your project every time you make any changes inside the `<root>/src` or `nativescript/src` folders.

What about the Angular Language Service? Well... just install it and you are ready to go.

### Automated build processes

What makes all of the above possible is the automated build process, which is performed with a few easy to read [gulp tasks](https://github.com/TeamMaestro/angular-native-seed/blob/master/nativescript/gulpfile.js) managed by [npm scripts](https://github.com/TeamMaestro/angular-native-seed/blob/master/nativescript/package.json#L54-L79).

The npm scripts + gulp tasks are only required for building the NativeScript project, as the `root/src` already contains everything that is needed for the web project.

In a few words the essence of the gulp tasks is to:
 1. look for any `.tns` files in `nativescript/src`
 2. rename them by removing the `.tns` part (which as a result overwrites the web specific files)
 3. move them into the `nativescript/app` folder

Then the gulp tasks are bundled with a couple of npm scripts, where:
 1. the first step is to run gulp steps
 2. the second step is to run the NativeScript CLI

For example, to run an ios build you need to call `npm run ios`, which underneath [runs](https://github.com/TeamMaestro/angular-native-seed/blob/master/nativescript/package.json#L59):
 1. gulp: `gulp build.cli.Default`
 2. tns: `tns run ios`

## Getting started

![Getting started](./images/Garfield-getting-started.jpg?raw=true "Getting started")

To get started all you need to do is to:

 1. Clone the repo

```
git clone https://github.com/TeamMaestro/angular-native-seed
```

 2. Initialise the web project

```
npm i
```

 3. Initialise the NativeScript project (make sure you have all the NativeScript bits ready. [installation instructions](http://docs.nativescript.org/start/quick-setup))

```
cd nativescript
npm i
```

 4. Run the web project

From the `root` folder run:

```
ng serve
```

 5. Run the NativeScript project

From the `nativescript` folder run: 

```
npm run ios
```

or

```
npm run android
```

 6. To use NativeScript live sync

Run the following two commands from the `nativescript` folder, each in a separate terminal.

```
npm run livesync
```

and

```
tns run android/ios
```

## Simple

![Simple Example](./images/Garfield-simple.jpg?raw=true "Simple Example")

ng g c simple

go to src/app/simple
  open simple.component.ts
    add  moduleId: module.id, to @Component
  add simple.component.tns.html
    add html
  update simple.component.html
open app.component.ts
  add menuItem for simple
open app.module.tns.ts
  add SimpleComponent Import and add it to declarations

To test web run:
  ng serve from the root folder

To test NativeScript run:
  npm run ios or npm run android from the NativeScript folder

## With Lazy loading

![Lazy Loading](./images/Garfield-lazy-loading.gif?raw=true "Lazy Loading")

ng g c lazy-cat

go to src/app/lazy-cat
  create folder components/lazy-cat and move all the component files there
open lazy.component.ts
  add  moduleId: module.id, to @Component
create lazy-cat.routes.ts in src/app/lazy-cat
  add code (possibly use the VS Code snippet)
create lazy-cat.module.ts in src/app/lazy-cat
  add code (possibly use the VS Code snippet)
-- show folder structure to make sure all is created correctly
open src/app/home.routes
  add path and loadChildren for lazy-cat
open app.component.ts
  add menuItem for lazy-cat

## Adding translation

![Speaking Languages](./images/Garfield-Languages.jpg?raw=true "Speaking Languages")

go to src/assets/i18n
  open `en.json` file
    update menu
    add lazy
  add `es.json` file
    copy from `en.json` and translate all
open `app.component.ts`
  update menu items to use the translation items
open `lazy.component.html`
  update to use the translation
open `lazy.component.tns.html`
  update to use the translation

test :D


## Full complexity component
ng g c infinite
// to infinity and beyond :D