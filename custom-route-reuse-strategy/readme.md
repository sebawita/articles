# How to extend Custom Router Reuse Strategy

Every now and then I am asked to help with various NativeScript challenges. I wanted to share the last one - which came up few times in the past. The issue was as follows:

> **The Challenge**
> I have a Newspaper type Angular Master-Detail app. The Master page displays a list of articles to read. When you tap on one of the articles, then the app navigates - with a fancy page transition - passing an articleID to the Details page. 
> 
> And here is the twist. In the Details page my app displays other related articles. When the user taps on another article, the app re-navigates to the Details page with the new articleId. At this point I can retrieve the new articleId and load the new article. However - and here is the problem - because the app navigates to the same component I cannot use page transition, and also on iOS the **back button** in the **ActionBar** still points to the Master page.

## What is happening?

![What is happening](./images/what-is-happening.gif?raw=true)

Before anyone starts a new Twitter war, this **is** the **expected behaviour**. Let me explain:

In NativeScript Angular we use a **Custom Router Reuse Strategy** (**CRRS**). Yes, we did change the **Default Router Reuse Strategy**. We did it to help you optimise your app, so that it is optimised like a mobile app, and not like a web app.

Note, creating a Component comes with a cost, as it a significant number of processing cycles to create the page and each Native UI Component. So, ideally we should want to avoid that when possible.

So, without any surprises we decided to implement the CRRS, so that:

* it reuses the existing components when you re-navigate to itself
* and also when you navigate back - for example: from detail back to master - instead of creating the Master Component again, it brings back the Master Component from the cache, and as a result speeding up the navigation.

<!--SHOULD I have this paragraph?-->
So, you can see this is all done for your own good. You can probably agree, that you want this behaviour... most of the time (let's call it the DEFAULT behaviour), and sometimes you need the behaviour from the scenario above (let's call it an EXCEPTION).

## First Instinct - The Hack

![Hack](./images/hack.gif?raw=true)

My first instinct was to create a quick hack. If navigating from a DetailComponent to DetailComponent causes the problem, then why not create a copy of the same component and make the navigation from DetailA to DetailB to DetailA to...

Something like this:

```
export class ItemDetailComponent implements OnInit {
    protected container = 'a';
    private item: Item;

    get detailsPath(): string {
        return (this.container === 'a') ? '/item-b' : '/item-a'
    }

    constructor(protected itemService: ItemService, protected route: ActivatedRoute, protected router: RouterExtensions) { }

    ngOnInit(): void {
        const id = +this.route.snapshot.params['id'];
        this.item = this.itemService.getItem(id);
    }

    navigate(id: number) {
        this.router.navigate([this.detailsPath, id]);
    }
}

@Component({
    selector: 'ns-details-a',
    moduleId: module.id,
    templateUrl: './item-detail.component.html',
})
export class ItemDetailComponentA extends ItemDetailComponent {

    constructor(itemService: ItemService, route: ActivatedRoute, router: RouterExtensions) {
        super(itemService, route, router);
        this.container = 'a';
    }
}

@Component({
    selector: 'ns-details-b',
    moduleId: module.id,
    templateUrl: './item-detail.component.html',
})
export class ItemDetailComponentB extends ItemDetailComponent {

    constructor(itemService: ItemService, route: ActivatedRoute, router: RouterExtensions) {
        super(itemService, route, router);
        this.container = 'b';
    }
}
```

See the full example in [Playground](https://play.nativescript.org/?template=play-ng&id=LBuyAm&v=1).

### Conclusion to the hack

Even though this works, it is not a great solution, it is a great hack, but not a great solution. Most of all:

* your code becomes bloated,
* tries to trick the framework into doing something else,
* in 2 weeks you won't even remember why you did that,
* it probably causes more issues than it solves


![Bad solution](./images/bad-solution.gif?raw=true)


What if you have more than one details component like that? Are we going to start questioning whether Angular and NativeScript are the right frameworks for the job that we need them for?


![Nope](./images/nope.gif?raw=true)
![Nope](./images/nope.gif?raw=true)
![Nope](./images/nope.gif?raw=true)

## "Custom" Custom Route Reuse Strategy - The proper solution

Then I talked Alex Vakrilov from the NativeScript Core team...
and he said:

![Nope](./images/slap.gif?raw=true)

OK, so back to the drawing board. Instead, we should **extend** the existing **Custom Route Reuse Strategy**, and make it fulfil the requirements of our app.

### Configure Paths - noReuse flag 

First, in the **Routes** configuration, we need to add a flag, that will help us identify, which paths shouldn't be reused. In our case this is the **DetailComponent**. This can be done be adding the `data: { noReuse: true }` to the details path. Like this:

**app-routing.module.ts**

```
const routes: Routes = [
    { path: '', redirectTo: '/items', pathMatch: 'full' },
    { path: 'items', component: ItemsComponent },
    { path: 'item/:id', component: ItemDetailComponent, data: { noReuse: true } },
];
```

Then each time the app navigates, we will be able to look for the `noReuse` flag.

### Create a new Custom Route Reuse Strategy

Next, we need to create a new Custom Route Reuse Strategy class.

It should extend the `NSRouteReuseStrategy`:

```
export class CustomRouteReuseStrategy extends NSRouteReuseStrategy {
```

Use the Dependency Injection to get `NSLocationStrategy`, and pass it to the parent constructor:

```
constructor(location: NSLocationStrategy) {
    super(location);
}
```

Finally, it should implement the `shouldReuseRoute` function, which should return `true` or `false`:

```
shouldReuseRoute(future: ActivatedRouteSnapshot, current: ActivatedRouteSnapshot): boolean {
    // return (shouldIReuse) ? true : false;
}
```

#### Implementing shouldReuseRoute()

This is where we need to focus on the proper solution.

We **MUST ALWAYS** call `shouldReuseRoute()` from the parent, as it used to **mark** the activated routes that are page-specific, and also the result will let us know when it intends to reuse the current route.

```
let shouldReuse = super.shouldReuseRoute(future, current);
```

If `shouldReuse` is true, then we are possibly in the scenario when we are about to re-navigate from our `ItemDetailComponent` to itself. At this point, we need to check if the `noRouse` flag is set to `true`. And if it is, then the app is about to navigate from `ItemDetailComponent` to itself. To avoid reusing this path just change `shouldReuse` to `false`.

```
if (shouldReuse && current.data.noReuse) {
    shouldReuse = false;
}
```

Finally, we need to return the `shouldReuse` value.

```
return shouldReuse;
```

#### Here is the full custom-route-reuse-strategy.ts

```
import { Injectable } from '@angular/core';
import { ActivatedRouteSnapshot } from '@angular/router';
import { NSLocationStrategy } from 'nativescript-angular/router/ns-location-strategy';
import { NSRouteReuseStrategy } from 'nativescript-angular/router/ns-route-reuse-strategy';

@Injectable()
export class CustomRouteReuseStrategy extends NSRouteReuseStrategy {

    constructor(location: NSLocationStrategy) {
        super(location);
    }

    shouldReuseRoute(future: ActivatedRouteSnapshot, current: ActivatedRouteSnapshot): boolean {
        // first use the global Reuse Strategy evaluation function,
        // which will return true, when we are navigating from the same component to itself
        let shouldReuse = super.shouldReuseRoute(future, current);

        // then check if the noReuse flag is set to true
        if (shouldReuse && current.data.noReuse) {
            // if true, then don't reuse this component
            shouldReuse = false;
        }

        console.log(`Should Reuse: ${shouldReuse}`);
        return shouldReuse;
    }
}
```

### Provide the new CRRS in AppModule 

The last step is to let Angular know that we want to use our new `CustomRouteReuseStrategy`.

This is done by adding our new CRRS class as `RouteReuseStrategy` to the `AppModule`, like this:

**app.module.ts**

```
@NgModule({
    ...
    providers: [
        {
            provide: RouteReuseStrategy,
            useClass: CustomRouteReuseStrategy
        }
    ],
    ...
})
```


### The full solution

You can see the full solution in [Playground](https://play.nativescript.org/?template=play-ng&id=LBuyAm)

## Conclusion

![well done](./images/well-done.gif?raw=true)

As you can see, implementing a Custom Route Reuse Strategy, is not only the best solution, but it is also simple and quite elegant. You can very easily add the `noReuse` flag to any path you don't want to reuse, while keeping it for all the other paths.

Let us know in comments what you think.

Did you face this challenge before?

Are there any scenarios that this doesn't cover?