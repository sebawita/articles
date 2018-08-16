# Apps That Work Natively on the Web and Mobile

![code-sharing](./images/code-sharing.png?raw=true)

Today we’re happy to announce an exciting new way to build web and mobile apps with Angular.

First, some background: since the beginning of Angular, you could use [NativeScript](https://www.nativescript.org/) with Angular to build mobile apps.

> NativeScript is an open source framework for building truly native mobile apps with JavaScript. It lets you use your existing Angular skills, and as a result you get a native UI & performance on iOS and Android.

However, when you needed to build **both** a web and a native mobile app, you had to create, develop and maintain two separate projects. Although this got the job done, it quickly became apparent that we could do better than that.

## The Dream of Code Sharing

This challenge led to a dream of a **Code-Sharing Project**. One that would allow you to keep the code for the web and mobile apps in one place. One that would allow us to share the business logic between web, iOS and Android, but still be flexible enough to include platform-specific code where necessary.

With the dawn of **Schematics** and **ng add** we now had a whole new set of possibilities.

To realise the Code-Sharing Dream the Angular and NativeScript teams teamed up to create [nativescript-schematics](https://github.com/nativescript/nativescript-schematics), a schematic that enables you to build both web and mobile apps from a single project.

> Please note that **@nativescript/schematics** works with **@angular/cli 6.1.0** or newer.

<!-- Should this stay here, move somewhere or delete completely-->
We are looking at a scenario where you can use the Angular CLI with the {N} Schematics to, either:

 * create a new project with a code-sharing structure, or
 * convert an existing web project to a code-sharing structure.

Then: 
 
 * convert existing components and modules to a code-sharing format, and
 * generate new components and modules in a code-sharing format.

### To share or not to share

The objective is to share as much code as possible, and break the platform-specific code into separate files.

This usually means that we can share the code for:

 * the **Routes** for the navigation,
 * the **Services** for the common business logic,
 *  and the **Component Class definition** for the common behaviour of each component.

While, separating the code for:

 * the **UI Layer** (**stylesheets** and **html**) - as you need to use different user interface components in web and NativeScript-built native apps,
 * and the **NgModules** - so that we can import platform-specific modules, without creating conflicts (i.e. Angular Material Design - which is web only) between web and mobile.

Here’s a diagram to show you what that looks like at a high level.

![project-structure](./images/project-structure.png?raw=true)

### Getting started
<!--#### Create project-->

To get started you could either create a new project with a code-sharing structure. Just run `ng new` with **@nativescript/schematics** as the collection.

Just like this:

```
ng new --collection=@nativescript/schematics --name=app-name --shared
```
> Note, you may need to install **@nativescript/schematics** first:
> 
> ```bash
> npm i -g @nativescript/schematics
> ```

<!--#### Migrating an existing web project-->

Or we could extend an existing web projects with the capability to build mobile apps with NativeScript, by calling:

```bash
ng add @nativescript/schematics
```

This adds the NativeScript-specific npm modules, add a default **AppModule** and **AppComponent** definition (or whatever you called your entry module and entry component) and adds another **tsconfig.json** (for NativeScript).

### Code Separation

Before you can start code sharing, we need to know how to separate the web code from the mobile code. This is important, so that we could easily create platform-specific code without creating conflicts.

To do that we can use a simple **naming convention**. By adding a **.tns** before the file extension, you can indicate that this file is NativeScript-specific, while the same file without the **.tns** extension is marked as web-specific. If we have just one file without the **.tns** extension, then this makes it a shared file.

### Component - Code-Sharing Format

The most common scenario is a component code. Usually we would have:

 * **name.component.ts** -  a shared file for the Component Class definition,
 * **name.component.html** - a web-specific template
 * **name.component.tns.html** - a mobile-specific template
 * **name.component.css** - a web-specific stylesheet
 * **name.component.tns.css** - a mobile-specific stylesheet

![code-separating](./images/code-separation.png?raw=true)

It is also worth noting that in your **@Component** Decorator the **templateUrl** and **styleUrls** point to files without including the **.tns** extension, as this is handled by the build process.

```ts
@Component({
  selector: 'app-name',
  templateUrl: './name.component.html',
  styleUrls: ['./name.component.css'],
})
```

### Build Process

To complete the story, we need a build process that is capable of using the shared files and the platform-specific ones, and provide a web or mobile app as a result.

![build-process](./images/build-process.png?raw=true)

#### Building for Web

To build a web app, it is "Business as usual", just use the Angular CLI to do the job.
When you call `ng serve` or `ng build`, the Angular CLI will ignore all NativeScript-specific files - as none of the web files would directly reference any **.tns** files.

 > `ng serve` -> builds a web app from a code-sharing project

For AOT builds, you may need to give TypeScript a helping hand, by adding NativeScript extensions to your **tsconfig.json** exclude list.

```ts
"exclude": [
  "**/*.ns.ts",
  "**/*.tns.ts",
  "**/*.android.ts",
  "**/*.ios.ts"
]
```

#### Building for Mobile

In order to build an iOS or an Android app with NativeScript, we need to use the [NativeScript CLI](https://www.npmjs.com/package/nativescript) with the [NativeScript Webpack plugin](https://www.npmjs.com/package/nativescript-dev-webpack).

Call:

 * `tns run ios --bundle` - to build an iOS app from the code-sharing project
 * `tns run android --bundle` - to build an Android app from the code-sharing project

During the build process, Webpack takes care of providing the **.tns** files whenever they are available, and as a result hiding the web versions of the same files (virtually, files like **home.component.tns.html** become **home.component.html**). While the NativeScript CLI is responsible for building a native mobile app.

### Code Separation for NgModules: HttpClient
Code separation is useful when working with NgModules, as often you need to import web- or NativeScript-specific modules.

A very good example is when you need to make an http call. In a web app you need to import the **HttpClientModule**, which  provides you with the implementation for the **HttpClient**.

However, the way http calls are executed in a browser is different to how they work in iOS and Android. In NativeScript you should use the **NativeScriptHttpClientModule**, which provides us with the equivalent implementation for the **HttpClient**.

Now, you can use the code separation technique to create two versions of the **@NgModule** - each using a different version of the **HttpClientModule** - and then with the help of Dependency Injection provide the right implementation of the **HttpClient** into your service.

![module-separation](./images/ngmodule-http.png?raw=true)

 * **my.module.ts** - a web module file that imports the **HttpClientModule**

```ts
import { HttpClientModule } from '@angular/common/http'; 

@NgModule({
  imports: [
    HttpClientModule,
    ...
  ]
  ...
})
export class MyModule { }
```

 * **my.module.tns.ts** - a mobile module file that imports the **NativeScriptHttpClientModule**

```ts
import { NativeScriptHttpClientModule } from 'nativescript-angular/http-client';

@NgModule({
  imports: [
    NativeScriptHttpClientModule,
    ...
  ]
  ...
})
export class MyModule { }
```

 * **my.service.ts** - a shared service file that injects the **HttpClient**

```ts
import { Injectable } from '@angular/core';
import { HttpClient } from '@angular/common/http';

@Injectable()
export class MyService {

  constructor(private http: HttpClient) { }
  ...
}
```

Code separation for modules makes up for a simple and a quite elegant solution. It enables you to bring in functionality that has different implementation (for the web and mobile apps) under one hood and as a result maximise the shared code.

## Summary

As you can see, building for both the web and mobile from a single project is fairly straightforward. You can either start with a fresh project (using `ng new`), or add mobile to an existing project (using `ng add`). There is also a simple naming convention to enable code separation, which allows you to cover many scenarios.

[Go ahead and give it a try](https://docs.nativescript.org/code-sharing/), as it really is that simple. You are one simple `ng add` or `ng new` away from the wonders of code sharing in Angular.

We would love to hear from you, let us know what works for you, what doesn't, and how things could be improved. Any constructive feedback is always welcome. Please, [send feedback here](https://github.com/NativeScript/nativescript-schematics/issues/new?labels=feedback).

### Learn more

This article covers the core functionality around sharing code between the web and mobile apps with Angular and NativeScript. There is still more that you might be interested in, like:

 * how to develop in a code-sharing project,
 * the migration process - with **migrate-component** and **migrate-module** commands,
 * the NativeScript-specific generators,
 * or handling libraries with mismatched APIs - that is when we have two similar libraries with a different API interface for web and mobile

To learn more, see our documentation at [docs.nativescript.org/angular/code-sharing/intro](https://docs.nativescript.org/angular/code-sharing/intro)
