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

All you need are the [Angular CLI](https://cli.angular.io/) and the [NativeScript Schematics](https://www.npmjs.com/package/@nativescript/schematics).

There are a couple of ways to start building web and mobile apps from a code-sharing project:

### A new project

You can start from a new project and start building your apps from ground app.

Simply call:

```
ng new --collection=@nativescript/schematics app-name --shared
```

This is covered in more depth in the [creating a new project](https://docs.nativescript.org/angular/code-sharing/creating-a-new-project) docs.

### Migrate an existing web project

Alternatively you could **migrate** an existing web project to a code-sharing structure.

Simply call:

```
ng add @nativescript/schematics
```

Then convert your **navigation**, **modules** and **components** to also work with NativeScript.

This is covered in more depth in the [migrating an existing web project](https://docs.nativescript.org/angular/code-sharing/migrating-a-web-project) docs.

## Building apps

Once you have a code-sharing project, building the apps is quite simple.

**Web**

To build a web app, you just need to use the Angular CLI:

```
ng serve
```

**NativeScript**

To build a mobile app, you need to use `NativeScript CLI` and `NativeScript Webpack`, like this:

```bash
tns run android --bundle
```

```bash
tns run ios --bundle
```

You can learn more about the build process in the [docs](https://docs.nativescript.org/angular/code-sharing/build-process/).

## What can and what can't be shared

Most of the code - if not all the code - that drives the business logic of your application should be shared. This is especially true when it comes to your Services that provide your components with data and other application wide functionality.

The same applies to the internal logic of your components, and the code that defines component's behaviour - which should be shared. However, the **HTML** code that defines the **View** of the component should be different between a web and a mobile app.

![](./img/shared-sketch.jpg?raw=true)

### Example Shopping Basket

For example in order to add a **shopping basket** to our online store, we would need:

* a **Basket Service**,
* a **Payment Service** and
* a **Basket Component**

Like in this diagram:

![basket-example](./img/basket.png?raw=true)

The **BasketService** - should allow you to:

* **loadItems** - get items placed in the basket
* **updateItemQuantity** - allows to change the quantity of a given item in the basket
* **remove** - remove a given item from the basket

The **Basket Service** should be made of 100% shared code

**basket.service.ts**

```TypeScript
import { Injectable } from '@angular/core';
import { HttpClient } from '@angular/common/http';

import { Item } from './item.model.ts';

@Injectable()
export class BasketService {

  constructor(private http: HttpClient) { }
  
  public loadItems(): Observable<Items[]> {
    return this.http.get('get-data-request');
  }

  public updateItemQuantity(itemId: number, quantity: number) {
    // update item quantity code
  }

  public remove(itemId: number) {
    // remove item code
  }
}
```

**payment.service.ts**

The **PaymentService** - should allow you to:

* **processOneClickPayment** - process a payment using user's default card

Again, the **PaymentService** should be made of 100% shared code

```TypeScript
import { Injectable } from '@angular/core';

@Injectable()
export class BasketService {
  public processOneClickPayment() {
    // Payment code
  }
}
```

Next we would need to create a component, that would consume the **Basket** and the **Payment** services, and allow users to:

* see what is in the basket,
* update the quantity of its contents and
* buy the items.

One more time, the behaviour and the code of the **BasketComponent** should be 100% shared.

**basket.component.ts**

```TypeScript
@Component({...})
export class BasketComponent implements onInit {
  items$: Observable<Item[]>;
  
  constructor(
    basketService: BasketService,
    paymentService: PaymentService
  ) { }
  
  public ngOnInit() {
    this.items = this.basketService.loadItems();
  }

  public increaseQuantity(item: Item) {
    this.basketService.updateItemQuantity(item.id, item.quantity + 1);
  }

  public decreaseQuantity(item: Item) {
    if (item.quantity > 1) {
      this.basketService.updateItemQuantity(item.id, item.quantity - 1);
    } else {
      this.removeItem(item);
    }
  }
  
  public removeItem(item: Item) {
    this.basketService.remove(item.id);
  }
  
  public pay() {
    this.paymentService.processOneClickPayment();
  }
}
```

The final bit is the implementation of the view templates.
Both, templates should:

* display a list of items - their name and quantity,
* allow to increase or decrease the quantity of an item,
* remove an item and
* allow to process the payment

In this case the UI for the web and mobile apps is going to be different, and as a result the template code **cannot be shared**. However, everything underneath that drives it's behaviour is still shared.

> NEED a mock of both web and {N} screens HERE!!!

To create two separate templates, you just need to use a naming convention. Simply create two files:

* **basket.component.html** - the web template file,
* **basket.component.tns.html** - the NativeScript template file - it is the '.tns' that makes it a {N} file.

**basket.component.html**

```HTML
<div>
  <h1>Basket</h1>
  <div *ngFor="let item of items$ | async">
    <h3>{{ item.name }}</h3>
    <h4>{{ item.quantity }}</h4>
    <button (click)="increaseQuantity(item)">➕</button>
    <button (click)="decreaseQuantity(item)">➖</button>
    <button (click)="removeItem(item)">🗑</button>
  <div>
  <button (click)="pay()">BUY NOW💰</button>
</div>
```

**basket.component.tns.html**

```HTML
<ActionBar title="Basket"></ActionBar>
<StackLayout>
  <ListView [items]="items$ | async">
    <ng-template let-item="item">
      <GridLayout columns="*, auto, auto, auto, auto">
        <Label [text]="item.name"></Label>
        <Label [text]="item.quantity" col="1"></Label>
        <Button [text]="➕" (tap)="increaseQuantity(item)"></Button>
        <Button [text]="➖" (tap)="decreaseQuantity(item)"></Button>
        <Button [text]="🗑" (tap)="removeItem(item)"></Button>
      </GridLayout>
    </ng-template>
  </ListView>

  <Button text="BUY NOW💰" (tap)="pay()"></Button>
</StackLayout>
```

Even though, the contents of **basket.component.html** and **basket.component.tns.html** is not quite the same, you can see that in general they follow the same structure. Both use the **async pipe** to load the data from **items$** and both use the component methods (**increaseQuantity(item)**, **pay()**).

Check out the docs to learn more about [code splitting](https://docs.nativescript.org/angular/code-sharing/code-plitting)

## Documentation

The documentation will help you learn the ins and outs of building a code-sharing project with Angular and NativeScript.

[View the Docs --BUTTON](https://docs.nativescript.org/angular/code-sharing/intro)


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

