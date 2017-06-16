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


## Getting started

![Getting started](./images/Garfield-getting-started.jpg?raw=true "Getting started")

So what we need is an `Angular Seed` that would have a build process that would help us manage both web and NativeScript projects. 
There is a number of such seeds, but my current favourite is [angular-native-seed](https://github.com/TeamMaestro/angular-native-seed) from `TeamMaestro`. 

To get started all you need to do is to:

 1. Clone the repo

```
git clone https://github.com/TeamMaestro/angular-native-seed
```

 2. Initialise the web project

```
npm i
```

 3. Initialise the NativeScript project

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

![Simple Example](./images/Garfield-simple.gif?raw=true "Simple Example")

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