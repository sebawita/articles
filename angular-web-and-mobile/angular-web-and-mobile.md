# Angular Web and Mobile

![nativescript-loves-angular](./images/nativescript-loves-angular.png?raw=true)

Since the beginning of Angular (even back in the days when we were calling it Angular 2), you could use [NativeScript](https://www.nativescript.org/) with Angular to build mobile apps.

> NativeScript is an open source framework for building truly native mobile apps with JavaScript. It let's you use your existing Angular skills, and as a result you get native UI & performance on iOS and Android.

However, when you needed to build both a web and a native mobile app, you had to create two separate projects. With the dawn of *Schematics* and *ng add* this has changed.

The Angular and NativeScript teams teamed up to create [nativescript-schematics](https://github.com/nativescript/nativescript-schematics), a schematic that enables you to build both web and mobile apps from a single project.

> Please note that *@nativescript/schematics* works with *@angular/cli 6.1.0* or newer.

We are looking here at scenario where you can use Angular CLI with the {N} schematic to:

 1. create new projects with a code-sharing structure,
 2. convert existing web projects to a code-sharing structure,
 3. convert components and modules to a code-sharing format,
 4. generate components and modules in a code-sharing format

## Code-Sharing Project

A code-sharing project is one where we keep the code for the web and mobile apps in one place. Here’s a quick diagram to show you what that looks like at a high level.

![project-structure](./images/project-structure.png?raw=true)

The objective is to share as much code as possible, and split the platform-specific code into separate files.

This usually means that we can share the code for:

 * **Routes** for navigation,
 * **Services** for common business logic,
 *  and **Component Class definition** for common behaviour of a component

While, splitting the code for:

 * **UI Layer** (**css** and **html**) - as most of the time what you want to show in a browser is not quite the same to what an app with Native UI should display,
 * and **NgModules``` - so that we can import platform specific modules, without creating conflicts (i.e. Angular Material Design - which is web only) between web and mobile (more on that later) <!--!!!TODO: provide link-->

### Create project

To get started we could either create a new project with a code-sharing structure. Just run *ng new* with *@nativescript/schematics* as the collection.

Just like this:

```
ng new --collection=@nativescript/schematics --name=app-name --shared
```
> Note, you may need to install *@nativescript/schematics* first:
> 
> ```bash
> npm i -g @nativescript/schematics
> ```

### Extend existing web project

Or we could extend an existing web projects with capability to build Mobile apps with NativeScript, by simply calling:

```bash
ng add @nativescript/schematics
```

This will add NativeScript specific npm modules, add a default *AppModule* and *AppComponent* definition (or whatever you called your entry module and entry component) and add another tsconfig.json.

## Code Splitting

Before we can start code-sharing, we need to know how to split the code. This is important, so that we could easily create platform specific code without creating conflicts.

To do that we can use a simple *naming convention*. By adding a *.tns* before the file extension, you can indicate that this file is NativeScript specific, while the same file without *.tns* is web specific. If we have just one file without the *.tns* extension, then this is a shared file.

## Component - Code-Sharing Format

![code-splitting](./images/code-splitting.png?raw=true)

The most common scenario is a component code. Usually we would have:

 * *name.component.ts* -  a shared file for the Component Class definition,
 * *name.component.html* - a web specific template
 * *name.component.tns.html* - a mobile specific template
 * *name.component.css* - a web specific stylesheet
 * *name.component.tns.css* - a mobile specific stylesheet

What is interesting here, that in our *@Component* Decorator references the template and the stylesheet without the *.tns* extension, as this is something that is handled by the build process.

```ts
@Component({
  selector: 'app-name',
  templateUrl: './name.component.html',
  styleUrls: ['./name.component.css'],
})
```

<!--## Example - App With Login

Let's consider a scenario for an app with a Login and Home Components, User Service and Routes configuration for Navigation purposes.-->


## Build Process

![build-process](./images/build-process.png?raw=true)

To complete the story, we need a build process that is capable of using the shared files and the platform specific ones, and provide a web or mobile app as a result.

### Web Build

To build a web app, it is "Business as usual", just use Angular CLI to do the job.
When you call *ng serve* or *ng build*, Angular CLI will ignore all NativeScript specific files - as none of the web files would directly reference any *.tns* files.

 > *ng serve* -> builds a web app from a code-sharing project

For AOT builds, you may need to give TypeScript a helping hand, by adding NativeScript extensions to *tsconfig.json* exclude list.

```ts
"exclude": [
  "**/*.ns.ts",
  "**/*.tns.ts",
  "**/*.android.ts",
  "**/*.ios.ts"
]
```

### Mobile Build

In order to build an iOS or an Android app with NativeScript, we need to use [NativeScript CLI](https://www.npmjs.com/package/nativescript) with [NativeScript Webpack plugin](https://www.npmjs.com/package/nativescript-dev-webpack).

Call:

 * *tns run ios --bundle* - to build an iOS app from a code-sharing project
 * *tns run android --bundle* - to build an Android app from a code-sharing project

First, Webpack looks for any *.tns* files, and rename them by removing the *.tns* part (i.e. *home.component.tns.html* => *home.component.html*), as a result hiding the web specific files. 

Once that is complete, the NativeScript build process takes the new image of the project and runs a build process with XCode for iOS or Gradle for Android, and as a result producing a Native Mobile app.

## Splitting NgModules: HttpClient
A very good example where splitting NgModules comes in handy, is when we want to make http calls. In a web app we need to import *HttpClientModule*, which will provide us with the implementation for the *HttpClient*.

However, the way http calls are executed in a browser is different to how they work in iOS and Android. In NativeScript we can use *NativeScriptHttpClientModule*, which provides us with the equivalent implementation for the *HttpClient*.

Now, we can use the code splitting technique to create two versions of the *@NgModule* - each using a different version of *HttpClientModule* - and then with the help of Dependency Injection provide the right implementation of the *HttpClient* into our service.

![module-splitting](./images/ngmodule-http.png?raw=true)

 * *my.module.ts* - web module file that imports *HttpClientModule*

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

 * *my.module.tns.ts* - mobile module file that imports *NativeScriptHttpClientModule*

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

 * *my.service.ts* - shared service file that injects the *HttpClient*

```ts
import { Injectable } from '@angular/core';
import { HttpClient } from '@angular/common/http';

@Injectable()
export class MyService {

  constructor(private http: HttpClient) { }
  ...
}
```

 > Code splitting for modules makes for a simple and quite elegant solution. It enables us to bring in functionality that has different implementation (for web and mobile) under one hood and as a result maximise the shared code.

## Summary

As you could see, building for both web and mobile from a single project is fairly straight forward. You can either start with a fresh project (using ng new) or add mobile to an existing project (using ng add). There is also a simple naming convention to enable code splitting, which allows you to cover many scenarios.

Go ahead and give it a try. It is really that simple. You are only one simple *ng add* or *ng new* away from the wonders of code-sharing in Angular.

I would love to hear from you, let me know what works for you, what doesn't and how things could be improved. Any constructive feedback is always welcome. 

### Learn more

This article covers the core functionality around sharing code between web and mobile with Angular and NativeScript. There is still more that you might be interested in, like:

 * how to develop in a code-sharing project,
 * the migration process - with *migrate-component* and *migrate-module* commands,
 * extended Angular CLI generators,
 * or handling libraries with mismatched APIs - different API interface for web and mobile

To learn more see our documentation at [docs.nativescript.org/angular/code-sharing/](https://docs.nativescript.org/code-sharing/)
