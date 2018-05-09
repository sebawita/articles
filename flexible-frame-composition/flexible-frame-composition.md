# Intro
<!-- talk about the problem -->

One of the most significant changes introduced in `NativeScript 4.0` is the way we interact with the main Frame of the app. Making it more flexible (hence the title) and a lot more intuitive.

# The problem

> TLTR: Old NativeScript can't share content between pages - more memory required, more CPU usage and slower performance

In previous versions of NativeScript we would always create a PageView (is it a PageView? - ask Alex) and place the content of the app in there. Then when the app would navigate to another view, NativeScript would create another PageView and swap it in for the old one.

At first, this doesn't sound like a big deal. I am on page A, so the main Frame contains A, then I navigate to page B, and now the main Frame contains B. 
The problem starts when you need to add a SideDrawer (or a TabView) for Navigation. Since each time the app navigates, all of its content gets replaced, that means that we have to add a side drawer to each page. As a result the app creates a new instance of the side drawer every time it navigates, meaning more RAM used, more CPU cycles spent and slower performance.

# The Solution: Flexible Frame Composition

> TLTR: NativeScript 4.0 can share content between pages - no RAM or CPU cycles waste and better performance

!!!!!!!!!!!In `NativeScript 4.0` the FrameView hosts navigation and pages

!!! Need a good explanation from Alex

As a result we can easily split the content of the page into separate Frames:
 * static - which would remain the same for each page - this is where the side drawer (or the tab view) goes
 * dynamic - which is the part that we would use to host the content for each page

This way the side drawer (or the tab view) needs to be created only once. The outcome? Less resource hungry app with a more pleasant user experience.

# How to use it

## NativeScript Core

All projects created with `tns create` using `NativeScript 4+` are Flexible Frame Composition ready.

One of the changes introduced, is the way we bootstrap the app. Before in `app.ts`, we used to call `app.start()` (as of {N} 4, it is marked as `deprecated`), now we need to call `app.run()`.

The other subtle difference is, that before `app.start()` would be take `moduleName` with the page we want to load on start.

```
app.start({ moduleName: 'home/home-page' });
```

While, `app.run()` takes a `moduleName` pointing to the `app-root` page, which should contain a `navigation Frame`

```
app.run({ moduleName: "app-root/app-root" });
```

It is that frame that provides the path to initial page to load. See the `defaultPage`.

```
<!-- other content -->
	<Frame defaultPage="home/home-page"></Frame>
<!-- other content -->
```  

> If you are upgrading an existing project remember to also, update `nativescript` and `tns-core-modules` to `"^4.0.0"`.

### SideDrawer

Now that we know the basics, let's see how to use this with a side drawer.

In `app-root.xml`, we just need to add a `RadSideDrawer` with the usual parts:
 * `drawerTransition` - to specify where the drawer should come from
 * `drawerContent` - to specify the drawer content (read. here goes your navigation)
 * `mainContent` - here is where we provide the `Navigation Frame` together with a default page

#### app-root.xml

```
<nsDrawer:RadSideDrawer id="sideDrawer" xmlns:nsDrawer="nativescript-ui-sidedrawer" loaded="onLoaded">
  <nsDrawer:RadSideDrawer.drawerTransition>
    <nsDrawer:SlideInOnTopTransition/>
  </nsDrawer:RadSideDrawer.drawerTransition>

  <nsDrawer:RadSideDrawer.drawerContent>
    <!-- Here go the side drawer items -->
    <Button text="Home" tap="goHome" />
    <Button text="Browse" tap="goBrowse" />
    <Button text="Search" tap="goSearch" />
  </nsDrawer:RadSideDrawer.drawerContent>

  <nsDrawer:RadSideDrawer.mainContent>
    <!-- This is the navigation frame -->
    <Frame defaultPage="home/home-page"></Frame> 
  </nsDrawer:RadSideDrawer.mainContent>
</nsDrawer:RadSideDrawer>
```

This is the first step, to structure your `app-root`. Next we need a way to navigate the `Frame` from `home-page` to another page.

This is quite simple actually. You just need to call `topmost()` to get the `Frame` and then call `navigate()` and provide the path to your component as `modulePath`.

After that, we need to get the `drawerComponent` and hide it with `closeDrawer()`. 

#### app-root.ts

```
import * as app from "application";
import { RadSideDrawer } from "nativescript-ui-sidedrawer";
import { topmost } from "ui/frame";

export function goBrowse() {
    topmost().navigate({
        moduleName: "browse/browse-page"
    });

    const drawerComponent = <RadSideDrawer>app.getRootView();
    drawerComponent.closeDrawer();
}
```



#### Examples

You can see this in action in [Playground](https://play.nativescript.org/?id=UnEWR2).


#### Template 

For your convenience, there is already a project template with a SideDrawer already configured.
You can find it [in the marketplace](https://market.nativescript.org/plugins/tns-template-drawer-navigation-ts). 
<!--You can preview it in [Playground - tns-template-drawer-navigation-ts](https://play.nativescript.org/?id=f290eJ).-->


To create a new project with a SideDrawer, call `tns create my-app-name --template tns-template-drawer-navigation-ts`.


### TabView

To use 

Root page

```<Page></Page>``` => 


## Angular

## Modals
## SideDrawer

## TabView
### New behaviour

## Multiple frames in one page
### Core: Frame id
### Angular: Named Page-Router-Outlet
nativescript-angular 6.0

## Navigation


## Where to find more examples