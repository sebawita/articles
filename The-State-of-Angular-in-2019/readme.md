
Angular is one of the most popular front-end frameworks out there, which enjoys a significant growth year on year.

In November 2018, the traffic to the Angular Docs reached 1.5M monthly visitors, which increased by more than 50% from the previous year.
![angular growth stats](./img/angular-growth-stats.jpg?raw=true)
<!--source https://twitter.com/angular/status/1069727630734151680 -->

This continuous growth of the Angular Community doesn't surprise me at all. A short look at **The Angular team values**:

>
* Apps that users love to use
* Apps that developers love to build
* A community where everyone feels welcome

Tells you the full story that Angular team looks after the **users**, the **developers** and the **community** that makes Angular great.

# The big selling points

To me the biggest selling point - this is based on my personal experience and also based on conversations I had with other developers - is that Angular out of the box provides me with everything I need to build a high performance app with a great User Experience.

Unlike in some other cases, I don't have to start by making a lot of choices, like "which router to use?" or "whether to declare my components with Classes or Functions". All I need is to run a simple `ng new [name]` and the Angular CLI will create a project with everything that I need.

> The only choice you have to make is whether to use tabs or spaces (SPACES 🤩).

Trust me, I am not having a go at other frameworks, it is that Angular addresses my specific needs.

Now combine the boilerplate provided by the Angular CLI, together with the [Angular Style Guide](https://angular.io/guide/styleguide) and you get a recipe for a great "Team Scalability". I found it fairly easy to expand a team for ongoing Angular projects. You add a new \<A> dev to a team and immediately this person knows what is what, and where to find everything. All you need to take care is transferring the application domain knowledge.

This consistency is even more important when building Enterprise applications. You can trust Angular to provide you with all the right tools and design patterns to be successful.

Even more importantly Angular offers [Active and Long Term Support](https://angular.io/guide/releases#support-policy-and-schedule) for all major releases.

> With 6 months of active support, during which regularly-scheduled updates and patches are released.
>
> 12 months of long-term support (LTS), during which only critical fixes and security patches are released.

# What changed in 2018

In 2018 the Angular team introduced many interesting updates.

Some of them were enabled by an introduction of [Schematics](https://blog.angular.io/schematics-an-introduction-dc1dfbc2a2b2) - a workflow tool for transforming your project.

> In simple terms Schematics, allows us to create special scripts (referred to as Schematics), that can analyse existing files, and make changes to them and/or generate brand new files (`ng generate` uses Schematics to do the job).

## From the Angular Team 

Here are my top 3 changes in the Angular world:

### ng update + Angular Update Guide
<!--1. **ng update** - Changing the way developers relate to their dependencies with Schematics-->

In the past, many would be worried about upgrading their Angular projects (and the same goes for other frameworks as well) to the next major version of the framework.
But let's be honest the amount of changes required to actually update from Angular 5 to 6 to 7 has been really minimal.

To aid us in this, the Angular Team published the [Angular Update Guide](https://update.angular.io/) - a dynamic guide on how to upgrade an Angular Project - gives us clear step by step instructions on how to perform a clean and successful upgrade.

To take it to the next level, the Angular Team enhanced the Angular CLI with a new command: [ng update](https://angular.io/cli/update), which updates your application and its dependencies.

The `ng update` does a lot more than just updating the versions of your npm packages. It can also take care of fixing breaking changes in your code. I mean 🤯🤩😎

To make it better, `ng update` is open to 3rd party library vendors, which allows them to [implement their own Schematics](https://www.softwarearchitekt.at/post/2018/04/17/seamlessly-updating-your-angular-libraries-with-ng-update.aspx) for a smooth upgrade process.

### ng add
<!--2. **ng add** - Move faster, automate away all of the setup steps that typically come with new depenencies-->

Another great enhancement to the Angular CLI is the `ng add` command. It enables plugin authors to implement Schematics that take care of the necessary steps to use their library in Angular projects.

> Stephen Fluin: 
> It allows you to move faster, automate away all of the setup steps that typically come with new dependencies.

Would you like to add Angular Material components to your project. Run:

```
ng add @angular/material
```

and boom, your project is ready to go.

Want to use Kendo UI components in your project as well. Run:

```
ng add @progress/kendo-angular-buttons
```

And bada boom, now you have Kendo UI buttons ready to rock.

Want to add native mobile to your project? Read more on it below... but yeah:

```
ng add @nativescript/schematics
```

will do the trick.

### cdk improvements

The final top 3 pick goes to the Component Dev Kit (CDK). It is a set tools that allows you to use the core functionalities found in the Angular Material library, but without the styling that comes with Material Design.

CDK gives you all the [benefits that the Angular Material brings to the table](https://material.angular.io/cdk/categories/component-composition) (bullet proof functionality like: Accessibility, Drag and Drop, etc), while you retain the full control of the look and feel of your UI components.

## From the community

Every year there many great tools and libraries coming from the community, making Angular even greater.

Here are my top <!--TODO top how many?-->picks:

### Angular Console

One of my favourites is [the Angular Console](https://angularconsole.com/), it provides a GUI on top of the Angular CLI, which allows you to use the Angular CLI in a visual way. You don't have to remember any of the commands or flags that come with the CLI.

But the Angular Console is a lot more than that. It basically takes care of most of the tasks that you used to trigger manually via a stylish UI. Need to update your npm packages: done, click here. Need to run a schematic: done, click there. Need to find other Schematics: done, look at all these babies. Need to kick off your tests: yup, just click over there. The list goes on.

Sure, not everyone likes to work outside of their console, many still haven't figured out how to exit their Vims. (This is how I explain to myself why people still use Vim... just restart your computer, jk). However, there are a lot of people that like to work with visual tools.

### Code Sharing: Web + Mobile

Another great community contribution worth noting is the new [Code Sharing with NativeScript](https://blog.angular.io/apps-that-work-natively-on-the-web-and-mobile-9b26852495e7). We are talking about building both Web and Native Mobile apps from a single Angular project.

![Code Sharing](./img/code-sharing.png?raw=true)

You could either start with a brand new project, by using the Angular CLI and `@nativescript/schematics`, like this:

```
ng new app-name --collection=@nativescript/schematics --shared
```

Or you could convert an existing Angular project to a code-sharing project, by running:

```
ng add @nativescript/schematics
```

Then to serve a web project, you run the usual:

```
ng serve
```

and to build a mobile project you can run (note, you need to install the NativeScript CLI for this):

```
npm run [android | ios]
```

In most cases you could easily reuse most of your TypeScript code (for your services, components, navigations and pipes), while providing different code for the UI definition (**.html** and **.css** files).

The code splitting is quite simple, just add a **.tns** extension to a file that you want to mark for mobile and you are good to go.

![Component Code Splitting](./img/component-code-splitting.png?raw=true)

Check out the [NativeScript docs](https://docs.nativescript.org/angular/code-sharing/intro) for more info.

# What to expect in 2019

In 2019 I expect Angular to reach get even more exciting. The Angular team is working on some really great initiatives.

## Ivy

At [ng-conf 2018 Miško Hevery and Kara Erikson](https://www.youtube.com/watch?v=dIxknqPOWms&feature=youtu.be&t=22m37s) talked about a new initiative called Ivy - the next rendering pipeline for Angular.

Ivy will make Angular output smaller, faster and simpler than it is today, and early experiments with it show that you could shrink the Angular bundle to as little as 3.5KB.

![Ivy bundle size](./img/Ivy-bundle-size.png?raw=true)
You can try out the [Ivy Demo](https://ng-ivy-demo.firebaseapp.com/) yourself.

## Bazel

The Angular team has been working on some experiments with [Bazel](https://bazel.build/) and Angular. It is really interesting to see what they come up with in 2019.

Bazel is a tool that automates builds and tests.

Bazel is language agnostic, it provides multi-language support. It doesn't matter whether you need to build your projects with Java or C++ or ObjectiveC or any other programming language or all of them combined. Bazel can handle them all.

Bazel uses a really clever algorithm to construct the build pipeline.
Bazel starts with the root Bazel **Rule** (a Bazel Rule is like a build step), it looks at rule **Dependencies** (basically anything it needs to execute the rule), and it will move onto building each dependency (each dependency is a Bazel Rule itself). It then recurrently checks what these dependencies need and so on. This results in a tree-like pipeline where each new branch is a dependency of a parent rule and each leaf is a rule with no further dependencies. Once we have the full tree, Bazel can execute the full build, starting at the leafs and finishing at the root. For added extra performance boost, Rules that are ready for build get executed in parallel.

But this is how it works the first time. Next time you perform the build again, Bazel doesn't need to rebuild each leaf and branch from scratch. Instead it will only rebuild those that changed and the branches that depend on the changed nodes. Meaning you won't see much of build time boost on the first round, but each consecutive build will be lightning fast.

Additionally, because Bazel can build all of its dependencies, that means it could even build a specific version of Node, and then perform a build with that specific version of Node. The result, no more issues with conflicting versions of tooling between machines, as they would all get what they need. How cool is that?

This is just a tip of the iceberg.

> Alex Eagle from the Angular team said:
> "The JavaScript community has gotten into the pattern that every devserver, bundler, or test runner tool has to accept the original program sources as inputs, and that means each tool gradually expands its scope to include a build system. It starts trivial (just call the Babel API) and expands to include ESnext transpilers, CSS preprocessors, framework compilers, html injection, and more.
We are fortunate to have a community of developers contributing all these plugins to each tool. However, the resulting system inhibits innovation. A new language tool needs to have plugins for every popular builder, and a new builder tool needs to have plugins for every popular language.
We'd be better served by keeping layering distinct, so that bundlers, devservers, and test runners can re-use a composable build system that already has an ecosystem of language plugins."

You could already use Bazel to build Angular, to learn more check out the [Angular Bazel Example Wiki](https://github.com/angular/angular-bazel-example/wiki). You could also see the [canonical example](https://github.com/alexeagle/angular-bazel-example) maintained by the Angular team at Google.

You can find out more about ABC (Angular Buildtools Convergence) from this [document](http://g.co/ng/abc).