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

```
example
 |- example.component.ts
 |- example.component.html
 |- example.component.tns.html
```

Like you probably noticed, the trick is to add `.tns` before any file extension to make it NativeScript specific, while the other file becomes web specific. 
So if you want to have two `scss` files for our module, then you should create `example.component.scss` and `example.component.tns.scss`. However if you want to share the styling file then you should have just `example.component.scss` only.

### Understanding the project structure and managing the npm modules

From the code sharing perspective the project is made of the following files and folders (to help focus on what is important I omitted a few files and folders).

```
root:
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
```

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

```
example
 |- example.component.ts
 |- example.component.tns.html
```

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

Now that we have the basics covered. Let's try to add a new `simple` component. 
We are going to use the `Angular CLI` to generate the component, then we will update it to make it available in NativeScript, then finally we will add it to the menu and navigation.

After you are finished with this exercice the file structure for the component should look as follows:

```
simple
 |- simple.component.html
 |- simple.component.scss
 |- simple.component.spec.ts
 |- simple.component.tns.html
 |- simple.component.ts (not generated by the CLI)
```

 1. Use the `ng CLI` to generate the `simple` component

```
ng g c simple
```

 2. Add the component to NativeScript `@NgModule` providers:

The Angular CLI will automatically add the component to the `app.module.ts` declarations. However this is not the case for the NativeScript counterpart.

Open `app.module.tns.ts`, import the component and add it to the declarations.

```
import { SimpleComponent } from './simple/simple.component';
...
@NgModule({
    declarations: [ AppComponent, SimpleComponent ],
```

 3. Update the component class
 
Open `simple.component.ts` add `moduleId: module.id,` to @Component. This is required by NativeScript to find the `templateUrl` and `styleUrls` paths.

Then add a function to the class with an alert.

Your `simple.component.ts` should look as follows:

```
import { Component, OnInit } from '@angular/core';

@Component({
  moduleId: module.id,
  selector: 'seed-simple',
  templateUrl: './simple.component.html',
  styleUrls: ['./simple.component.scss']
})
export class SimpleComponent implements OnInit {

  ngOnInit() {
  }

  sayHello() {
    alert('One hello to rule them all');
  }
}
```

 4. Update the UI

First let's update the web html open `simple.component.html` to display some message and add a button to call `sayHello`:
 
```
<h1 class="title">Simply Red</h1>
<p class="description">Something got me started :)</p>

<button (click)="sayHello()">Say Hello</button>
```

After that let's create the NativeScript counterpart. 
Create a new file called `simple.component.tns.html` and add the following code:

```
<GridLayout rows="auto,auto">
    <Label class="h1 title"
        text="Simply Red"></Label>
    <Label class="p description" textWrap="true" row="1"
        text="Something got me started"></Label>
    
    <Button (tap)="sayHello()" class="btn btn-primary">Say Hello</Button>
</GridLayout>
```

Note that the web button handles the `click` event, while the NativeScript project handles the `tap` event.

 5. Update navigation to allow navigating to the `SimpleComponent`

Open `home/home.routes.ts`. This is where the default navigation is configured.
And a path to the `SimpleComponent` (don't forget to import it first though):

```
{
    path: 'simple',
    component: SimpleComponent
},
```

Now let's update the navigation menu. All the menu items are located in `app.component.ts`. Let's add a new `MenuItem`:

```
{
    title: 'Simple',
    link: ['/simple']
},
```

 6. Let's test it

To run the web application run:

```
ng serve
```

and navigate to the `Simple` page.

To run the NativeScript application, navigate to the `nativescript` folder and run:

```
npm run ios
// or 
npm run android
```

### Example summary

Notice how little effort it takes to get going. At this point, we are not doing anything fancy, however we are already able to share `sayHelo` from the `SimpleComponent`. 
Also we get the bonus points for using Angular CLI. :)

The best thing is, that it takes longer to read about this example than actually build it.

## With Lazy loading

![Lazy Loading](./images/Garfield-lazy-loading.gif?raw=true "Lazy Loading")

Now let's try a bit more complicated example with a component contained in its module and with its own routing. To make it better we will add it as a lazy loaded component.

Once again we will use the Angular CLI to generate the component files for us. Then we will rearrange the component files and add routes and module files. We will finish with adding the component to the app navigation.

After you are finished with this exercice the file structure for the component should look as follows:

```
lazy-cat
 |- components
    |- lazy-cat
      |- lazy-cat.component.html
      |- lazy-cat.component.scss
      |- lazy-cat.component.spec.ts
      |- lazy-cat.component.tns.html
      |- lazy-cat.component.ts
 |- lazy-cat.module.ts
 |- lazy-cat.routes.ts
```

 1. Use the `ng CLI` to generate the `lazy-cat` component

```
ng g c lazy-cat
```

 2. Rearange the component files

Inside the `lazy-cat` folder create `components` folder, then inside `components` folder create `lazy-cat`. Finally move all the component files inside `lazy-cat/components/lazy-cat`.

 3. Update the UI files

Update `lazy-cat.component.html` with:

```
<h1 class="title">Lazy Cat</h1>
<p class="description">Garfield, you could give classes in doing nothing</p>
<p class="description">No, I couldn't</p>
```

Then create `lazy-cat.component.tns.html` with the following code:

```
<StackLayout>
    <Label class="h1 title"
        text="Simply Red"></Label>
    <Label class="p description" textWrap="true"
        text="Garfield, you could give classes in doing nothing"></Label>
    <Label class="p description" textWrap="true"
        text="No, I couldn't"></Label>
</StackLayout>
```

 4. Update the component class

Open `lazy-cat.component.ts` and add to the `@Component` definition.

```
moduleId: module.id,
```

 5. Create routes

Now we need to create the routes definition, which will work for both the web and NativeScript projects.

Add `lazy-cat.routes.ts` with a single empty path, which will navigate to `LazyCatComponent`:

```
import { Routes } from '@angular/router';
// app
import { LazyCatComponent } from './components/lazy-cat/lazy-cat.component';

export const LazyCatRoutes: Routes = [
    {
        path: '',
        component: LazyCatComponent
    }
];

```

If this module were to be made of multiple views then you would have multiple routes here.

### Code Snippet

If you are using Visual Studio Code then you could use a code snippet to help you with this taks. 

You could use the plugin [angular-native-seed Snippets](https://marketplace.visualstudio.com/items?itemName=sebawita.angular-native-seed-snippets) that provides you with snippets to quickly generare code for `routes` and `module`.

Or you could [manually add the snippets](https://code.visualstudio.com/docs/editor/userdefinedsnippets) to your project:

```
"share-router": {
  "prefix": "share-router",
  "body": [
    "import { Routes } from '@angular/router';",
    "// app",
    "import { ${Name}Component } from './components/${filename}/${filename}.component';",
    "",
    "export const ${Name}Routes: Routes = [",
    "    {",
    "        path: '',",
    "        component: ${Name}Component",
    "    }",
    "];",
    ""
  ]
},
```

To use this snippet, just start typing `share`, choose `share-router` and hit enter. Then first you will need to type `LazyCat` (as the prefix for the name of the class), then hit tab and type `lazy-cat` (as the name of the file/folder). 

![Routes Snippet](./images/snippet-lazy-cat-routes.gif?raw=true "Routes Snippet")

 6. Create module definition

Now we need to wrapp it all up in an `@NgModule` that will work for both the web and NativeScript projects. 

The thing is that while the web project uses `CommonModule`, `FormsModule` and `RouterModule`, NativeScript uses `NativeScriptModule`, `NativeScriptFormsModule` and `NativeScriptRouterModule`. To overcome we just need to use the `SharedModule` from `../shared`, which exports the core modules we need in each new module. Note that the `SharedModule` is defined in two files `shared.module.ts` (with the web modules) and `shared.module.tns.ts` (with the NativeScript modules). 

We also need to load `LazyCatRoutes` using the forChild. In order to make sure you use the right RouterModule (web vs NativeScript) import it from `../shared`.

The rest is fairly standard `@NgModule` definition.

Your `lazy-cat.module.ts` should look like this:

```
import { NgModule } from '@angular/core';
// vendor dependencies
import { TranslateModule } from '@ngx-translate/core';
// app
import { LazyCatComponent } from './components/lazy-cat/lazy-cat.component';
import { LazyCatRoutes } from './lazy-cat.routes';
// common
import { SharedModule, RouterModule } from '../shared';

@NgModule({
    imports: [
        SharedModule,

        RouterModule.forChild(LazyCatRoutes),
        TranslateModule.forChild()
    ],
    declarations: [LazyCatComponent]
})
export class LazyCatModule { }
```

### Code Snippet

Once again you can use a code snippet for this. Here is my snippet:

```
"share-module": {
    "prefix": "share-module",
    "body": [
        "import { NgModule } from '@angular/core';",
        "// vendor dependencies",
        "import { TranslateModule } from '@ngx-translate/core';",
        "// app",
        "import { ${Name}Component } from './components/${filename}/${filename}.component';",
        "import { ${Name}Routes } from './${filename}.routes';",
        "// common",
        "import { SharedModule } from '../shared';",
        "import { RouterModule } from '../common';",
        "",
        "@NgModule({",
        "    imports: [",
        "        SharedModule,",
        "",
        "        RouterModule.forChild(${Name}Routes),",
        "        TranslateModule.forChild()",
        "    ],",
        "    declarations: [${Name}Component]",
        "})",
        "export class ${Name}Module { }",
        ""
    ]
},
```

This time to use it just type `share` and select `share-module`. I believe that you know what to do next.

![Module Snippet](./images/snippet-lazy-cat-module.gif?raw=true "Module Snippet")

 7. Update navigation to allow navigating to the `LazyCatComponent` with Lazy Loading

Since we will lazy load this component we don't need to declare it in the app module. Open `app.module.ts`, remove the `LazyCatComponent` import and `remove it` from the `@NgModule declarations`.

Open `home/home.routes.ts`. To add a path to the `LazyCatComponent` (there is no need to import it here) with lazy loading:

```
{
    path: 'lazy',
    loadChildren: 'app/lazy-cat/lazy-cat.module#LazyCatModule'
},
```

Now let's update the navigation menu. Open `app.component.ts` and add a new `MenuItem`:

```
{
    title: 'Lazy',
    link: ['/lazy']
},
```

 8. Run the app

You know the drill.

`ng serve` for web
`npm run ios/android` for NativeScript

### Example summary

The structore of a module is rather clean and easy to navigate around. 
With the help of the provided code snippets, you can create a new module in a couple of minutes.

The `SharedModule` gives you a clean way of providing separate web and NativeScript modules.
If you have other modules that you expect to use with most of your compoents then you can just add them to the `SharedModule`, but make sure not to bloat it, as you should use it with most of your components.

## More complex modules

To create more complex modules, which contain multiple `Components` and even require providing different `Modules`, which don't fit in the `SharedModule` we need split the module into two files: `name.module.ts` and `name.module.tns.ts`. While the components would go into the `components` folder.

Imagine you need a page with infinite scrolling, which loads new items every time you get to the bottom of the page. For web we would need to use `InfiniteScrollModule` from npm module `ngx-infinite-scroll`. While for NativeScript we could just use the `ListView`.

In this case we need to run `npm install --save ngx-infinite-scroll` from the root of the project, to add the infinite scrolling package to the web project. 
Then we need to import `InfiniteScrollModule` add it to the `@NgModule imports` to `infinite.module.ts` (this should make it ready for the web project):

```
...
import { InfiniteScrollModule } from 'ngx-infinite-scroll';

@NgModule({
    imports: [
        SharedModule,

        RouterModule.forChild(InfiniteRoutes),
        TranslateModule.forChild(),

        InfiniteScrollModule
    ],
    declarations: [InfiniteComponent, ItemTemplateComponent]
})
```

Then for the NativeScript module we need to create `infinite.module.tns.ts`, which is exactly the same with the exception of the additional module (`no InfiniteScrollModule`):

```
...
@NgModule({
    imports: [
        SharedModule,

        RouterModule.forChild(InfiniteRoutes),
        TranslateModule.forChild()
    ],
    declarations: [InfiniteComponent, ItemTemplateComponent]
})
```

### Example summary

You can find the full example [here](https://github.com/sebawita/angular-native-seed/tree/examples/src/app/infinite).

Apart from the `InfiniteScrollModule` and multiple `Component declarations`, everything else is done in the same way as in the `LazyCat` example. 
The key lesson from this example is that every time you need to separate any chunk of code into web and NativeScript, just split the source code into two files. 
Same applies to styling. If you need two styling files - which let's face it, you probably should - then you could have them as `name.component.scss` and `name.component.tns.scss`.

## Adding translation (Bonus)

![Speaking Languages](./images/Garfield-Languages.jpg?raw=true "Speaking Languages")

As a bonus we are going to cover how to use the `i18n` module for language specific text.

This is really simple to use. You can find the English resource file at `src/assets/i18n/en.json`. 

 1. Update/create the language files

Let's add the fields for the `simple` and `lazy` examples to `src/assets/i18n/en.json`:

```
{
    "menu": {
        "home": "Home",
        "about": "About",
        "lazy": "Lazy",
        "simple": "Simple"
    },
    "home": {
        "title": "Home",
        "description": "This component is part of a module that was directly imported into the main AppModule."
    },
    "about": {
        "title": "About",
        "description": "This component was rendered from a lazy-loaded module."
    },
    "phone": {
        "edition": "Phone Edition!"
    },
    "simple": {
        "title": "Simply Red",
        "description": "Something got me started."
        "sayHello": "Say Hello"
    },
    "lazy": {
        "title": "Lazy joke",
        "jonsLine": "Garfield, you could give classes in doing nothing",
        "garfieldsLine": "No, I couldn't"
    }
}
```

To add Spanish just add a new file called `es.json` with the same fields, but with the tranlated values to Spanish, like this (please note that I used the Google Translate service, so mistakes should be expected):

```
{
    "menu": {
        "home": "Casa",
        "about": "Acerca de",
        "lazy": "Perezoso",
        "simple": "Sencillo"
    },
    "home": {
        "title": "Casa",
        "description": "Este componente es parte de un módulo que fue importado directamente en el AppModule principal."
    },
    "about": {
        "title": "Acerca de",
        "description": "Este componente se procesó desde un módulo cargado de modo lento."
    },
    "phone": {
        "edition": "¡Edición del teléfono!"
    },
    "simple": {
        "title": "Simplemente rojo",
        "description": "Algo me hizo comenzar."
        "sayHello": "Di hola"
    },
    "lazy": {
        "title": "Broma perezosa",
        "jonsLine": "Garfield, podrías dar clases sin hacer nada",
        "garfieldsLine": "No, no pude"
    }
}

```

 2. Update the navigation menu

The navigation menu already expects a translated value, so all we need to do is to update the MenuItems in `app.component.ts`. 
Change the `MenuItems` for simple and lazy to:

```
{
    title: 'menu.simple',
    link: ['/simple']
},
{
    title: 'menu.lazy',
    link: ['/lazy']
},
```

This will use the text value from `menu.simple` and `menu.lazy` from the required language.json file.

 3. Update the UI

To provide a language specific value, we are going to use the `translate` pipe, which will look for the translated value in the `language.json` file.

Open `lazy.component.html` and update it to:

<h1 class="title"
    [innerText]="'lazy.title' | translate"></h1>
<p class="description"
    [innerText]="'lazy.jonsLine' | translate"></p>
<p class="description"
    [innerText]="'lazy.garfieldsLine' | translate"></p>

The open `lazy.component.tns.html` and update it to:

```
<StackLayout>
    <Label class="h1 title"
        [text]="'lazy.title' | translate"></Label>

    <Label class="p description" textWrap="true"
        [text]="'lazy.jonsLine' | translate"></Label>
    <Label class="p description" textWrap="true"
        [text]="'lazy.garfieldsLine' | translate"></Label>
</StackLayout>
```

 4. Add language switch functionality to the project

Open `menu/components/menu/menu.component.ts` and add the following function to the `MenuComponent` class (which will switch to the provided language file):

```
switchLanguage(language: string) {
    this.translate.use(language);
}
```

Now let's add the switch buttons to the UI. 

Open `menu.component.html` and update it with two new buttons, which will switch between English and Spanish:

```
<seed-menu-item *ngFor="let item of items"
    [item]="item"></seed-menu-item>

<Button (click)="switchLanguage('en')" class="language-button">EN</Button>
<Button (click)="switchLanguage('es')" class="language-button">ES</Button>
```

We also need to something similar to the NativeScript menu. Open `menu.component.tns.html` and update it with:

```
<ScrollView orientation="horizontal">
    <StackLayout orientation="horizontal">
        <seed-menu-item *ngFor="let item of items"
            [item]="item"></seed-menu-item>
        <Button text="EN" (tap)="switchLanguage('en')" class="btn btn-primary"></Button> 
        <Button text="ES" (tap)="switchLanguage('es')" class="btn btn-primary"></Button>
    </StackLayout>
</ScrollView>
```

Now for the finishing touch let's add a bit of styling. Open `menu.component.scss` and add:

```
.language-button {
    background-color: #30bcff;
    border: none;
    color: white;
    padding: 8px 16px;
    text-align: center;
    text-decoration: none;
    display: inline-block;
    font-size: 16px;
}
```

 5. Test it

When you run the project, you should get something like this.

### Web

![Translation Web](./images/translation-web.gif?raw=true "Translation Web")

### Mobile

![Translation Mobile](./images/translation-mobile.gif?raw=true "Translation Mobile")


### Translate from TypeScript

To use the translation in TypeScript, you need to inject the `TranslationService` to your contructor.

```
import { TranslateService } from '@ngx-translate/core';
...
constructor(private translate: TranslateService) { }
```

Then you can just call `this.translate.get(_key_)`, which returns an Observable that will provide you with a result.

If we were to refactor the `SimpleComponent` then it would look something like this:

```
import { Component, OnInit } from '@angular/core';
import { TranslateService } from '@ngx-translate/core';
import 'rxjs/add/operator/toPromise';

@Component({
  moduleId: module.id,
  selector: 'seed-simple',
  templateUrl: './simple.component.html',
  styleUrls: ['./simple.component.scss']
})
export class SimpleComponent implements OnInit {

  constructor(private translate: TranslateService) { }

  ngOnInit() {
  }

  sayHello() {
    // Hello message with translation
    this.translate.get('simple.hello')
    .toPromise()
    .then(message => alert(message));
  }
}
```

Here is the full example: [simple.component.ts](https://github.com/sebawita/angular-native-seed/tree/examples/src/app/simple/simple.component.ts)

### Example summary

Using the `i18n` translation module is rather simple. Just create a `language.json` file for each language and use the `translation` pipe for each text that needs translating. 
You could even use the translation pipe to select a different image for each language.

## Summary - AKA go and use it

![Summary](./images/Garfield-summary.gif?raw=true "Summary")

Code sharing is definitely a very important challenge of the modern developemnt world. Not many companies can afford to have multiple teams to support the development for each platform (web, iOS, Android, Desktop, etc). Finally we have an option that not only offers hope, it offers a solution we can work on.  

The `TeamMastro's` seed makes code sharing accessible to anyone who has a bit of Angular and NativeScript knowledge. 
There are many things that you might want to see included out of the box in the `angular-native-seed`, but the key to a good project template is `simplicity` and `extensibility`, which is the case here.

There is a lot more that I would like to share with you, but don't worry this is just the beginning of our code sharing story. You can expect more in the near future.
Having said that, this should provide you with enough to cover most of the usual use cases. Give it a try and let us know if you have any suggestions to improve or send us questions that you might need help with.
