# Code-Sharing

One of the biggest benefits of using NativeScript is the ability to share the code between **Android** and **iOS** apps. This allows you to reuse a lot of the code and save a ton of time.

Since, NativeScript uses **Web Technologies** (like **JavaScript**, **CSS**, **Angular**, etc.) to build mobile apps, opens up the question of what of the code could be shared with a **Web App**, and ultimately expand the benefits of **code-sharing** to include **Web Projects**.

<!--## Overview Video-->

## Code-Sharing: Angular and NativeScript

With the combination of NativeScript and Angular's **platform agnostic architecture** we are presented with the opportunity to make it an **Android**, **iOS** and **Web** code-sharing combination.

This requires two parts:

* A **code-sharing ready** project structure - enabling you to have both **platform-agnostic** (shared) and **platform-specific** code in a clear and intuitive fashion.
* A **build process** - capable of using both **shared** and **platform-specific** pieces of code and build your **mobile** and **web** apps.

![Code-Sharing project structure](./img/code-sharing-project-structure.png?raw=true)

## Getting Started

All you need are the **Angular CLI** and the **NativeScript Schematics**.

There are a couple of ways to start building web and mobile apps from a code-sharing project:

### A new project

You can start from a new project and start building your apps from ground app.

This is covered in more depth in the [creating a new project](https://docs.nativescript.org/angular/code-sharing/creating-a-new-project) docs.

### Migrate an existing web project

Alternatively you could **migrate** an existing web project to a code-sharing structure. Then convert your **navigation**, **modules** and **components** to also work with NativeScript.

This is covered in more depth in the [migrating an existing web project](https://docs.nativescript.org/angular/code-sharing/migrating-a-web-project) docs.

## Documentation

The documentation will help you learn the ins and outs of building a code-sharing project with Angular and NativeScript.

[View the Docs --BUTTON](https://docs.nativescript.org/angular/code-sharing/)


## Why to build code-sharing projects

### Reuse more of your code

Easily 70% or more of the code can be shared between the web and mobile projects

![Benefits of a shared-code project](./img/benefits-of-shared-code.png?raw=true)

### Stay in sync

Since, you write all your code in a single project, you can keep your **Android**, **iOS** and **Web** apps in sync.

This makes it easier:

* to release new features at the same time,
* fix bugs in one place to resolve the issues across all platforms,
* maintain the code and avoid the code to that grow too far apart between different branches

### Use your Web development skills

Use your Web development skills to build not only **Mobile apps**, but also **Web apps**. This makes it easier to build a more balanced **Dev Team**, where people that work on the mobile code, could easily switch to help with the web code.

