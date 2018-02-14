Just like all mushrooms are edible, except some only once in your lifetime, so are the NgModules - some you can import everywhere, and some only once per app.

Do you know, which modules you should import everywhere and which only once?

Do you know what happens every time you add `NativeScriptModule` to each of your `@NgModule`?

Let me help you.


## Once per App (root modules)

***NativeScriptModule*** and ***NativeScriptAnimationsModule*** should only ever be imported in your `AppModule`.
If you import them multiple times then the performance of your app might suffer.

### NativeScriptModule

***NativeScriptModule*** - provides services that are essential to run a NativeScript app. Most importantly it provides the renderer.
Since each lazy module has it's own dependency injector, we'll end up with a bunch of renderers if we import the NativeScriptModule in each lazy module. This would additionally cause a lot of problems with Angular Animations.


### NativeScriptAnimationsModule

***NativeScriptAnimationsModule*** - starts by [initialising crucial animation elements](https://github.com/NativeScript/nativescript-angular/blob/master/nativescript-angular/animations/animations.module.ts#L41)  `AnimationDriver`, `AnimationBuilder `, `AnimationStyleNormalizer `, `AnimationEngine`, but most importantly it [creates a wrapper around the renderer](https://github.com/NativeScript/nativescript-angular/blob/master/nativescript-angular/animations/animations.module.ts#L48). 

### Errors
To help you get it right, we are going to add some changes to these modules, so that when you import either of them more than once you will see an error message like this:

- for ***NativeScriptModule*** - `NativeScriptAnimationsModule has already been loaded.`
- for ***NativeScriptAnimationsModule*** - `Import NativeScriptAnimationsModule in the AppModule only.`

## Everywhere

Some modules can be (or even should be) imported with every feature and lazy-loaded module.

### NativeScriptCommonModule

You should import `NativeScriptCommonModule` in every feature and lazy-loaded module. However you don't need to import it in `AppModule`, as it should be already exported by `NativeScriptModule`.

***NativeScriptCommonModule*** - gives you access to the common angular directives (i.e. `*ngFor` and `*ngIf` and more), but also it provides you with NativeScript UI components (i.e. `ListViewComponent` and `ActionBarComponent`).

## When needed

There are also some modules that you should only import when needed.

### NativeScriptHttpClientModule

***NativeScriptHttpClientModule*** (and its ***deprecated*** older cousin `NativeScriptHttpModule`) - allows you to make `http` calls.

***The best practice:*** only import `NativeScriptHttpClientModule` (or `NativeScriptHttpModule`) in modules that:

 * provide services that inject `HttpClient` (or `Http`)
 * declare components that inject `HttpClient` (or `Http`), but not when they just inject services that use `HttpClient` (or `Http`)

### NativeScriptFormsModule

***NativeScriptFormsModule*** - provides you with two-way binding using `[(ngModule)]`.

***The best practice is like:***

>  "where there's smoke, there's fire"

or in Angular terms:

> where there's `[(ngModule)]` there's `NativeScriptFormsModule`

### NativeScriptRouterModule

***NativeScriptRouterModule*** - provides you with the necessary items to navigate in a NativeScript application: `RouterExtensions` and `[nsRouterLink]`.

***The best practice:*** it should be imported in the `AppModule` and once per each lazily-loaded bundle, which contains components that either:

 * inject `Router` or `RouterExtensions` (in the constructor) or

```
@Component({...})
export class MyComponent {	
	constructor(private router: RouterExtensions) { }
}
```

 * use `[nsRouterLink]` directive in the template

```
	<label text="Go Home" [nsRouterLink]="['/home']"></label>
```
 

## The final advice - services

> Never provide the same service in more than one module, as this will create a new copy of the service for each module.

## Cheatsheet

| NgModule                     | Import from                              | How to use it           |
|------------------------------|------------------------------------------|-------------------------|
| NativeScriptModule           | nativescript-angular/nativescript.module | Always add to AppModule |
| NativeScriptCommonModule     | nativescript-angular/common              | Never add to AppModule <br> Add to the remaining Modules |
| NativeScriptAnimationsModule | nativescript-angular/animations          | Only add to AppModule <br> Use only if you need Angular Animations |
| NativeScriptHttpClientModule | nativescript-angular/http-client         | Use only when any of the Module's services or components inject HttpClient |
| NativeScriptFormsModule      | nativescript-angular/forms               | Use in each module that use two-way bindings with [(ngModule)] |
| NativeScriptRouterModule     |  nativescript-angular/router             | Use when the Module's services or components use navigation |

## Further reading

It is also important to understand about Sharing Modules, which allow you to prepackage commonly used directives and pipes.

You can read more about it [in the Angular Documentation](https://angular.io/guide/sharing-ngmodules)

