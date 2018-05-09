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
 * `drawerTransition` - to specify the behaviour of the Drawer when it appears
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

This is quite simple actually. You just need to call `topmost()` to get the main `Frame` and then call `navigate()` and provide the path to your component as `modulePath`.

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

You can see this in action in [Playground - Basic Example](https://play.nativescript.org/?id=UnEWR2) or [Playground - template example](https://play.nativescript.org/?id=f290eJ).

#### Template 

For your convenience, there is already a project template with a SideDrawer already configured.
You can find it [in the marketplace](https://market.nativescript.org/plugins/tns-template-drawer-navigation-ts). 

To create a new project with a SideDrawer, call `tns create my-drawer-ts --template tns-template-drawer-navigation-ts`.


### TabView

If TabView is your preferred way for app navigation, then the process is slightly different.

In `app-root.xml` we need a `TabView` component with the `androidTabsPosition` property set to `bottom`: ```<TabView androidTabsPosition="bottom">```
> `androidTabsPosition` not only changes the location of the TabView (from top to bottom for Android), but it also changes its behaviour. So that when the TabView is loaded, only the content of the currently visible tab is loaded into the memory.

Then for each tab, we need a `TabViewItem` with a `Frame` component inside - note that each frame will have its own default page.

#### app-root.xml

```
 <TabView androidTabsPosition="bottom">
	<TabViewItem title="Home">
		<Frame defaultPage="home/home-page"></Frame>
	</TabViewItem>

	<TabViewItem title="Browse">
		<Frame defaultPage="browse/browse-page"></Frame>
	</TabViewItem>

	<TabViewItem title="Search">
		<Frame defaultPage="search/search-page"></Frame>
	</TabViewItem>
</TabView>
```

This will take care of navigating between different tabs, and each Frame will load its Page when required.

#### Navigating in a Frame

You can also navigate in each Frame independently of all other frames.


All we need is to do, is to get the current page object and then grab its frame.
Then once we have the frame, we can just call `navigate`, like this:

```
<Button text="Navigate" tap="onItemTap"></Button>
```

```
export function onItemTap(args: ItemEventData) {
    const frame = args.view.page.frame;
    
    frame.navigate({
    	moduleName: "home/another-page",
    })
}
```

Now, if you run an app with code like this, you will be able to navigate to `another-page`, and when you move to `Browse` or `Search` tab and come back to the `Home` tab, the `Home tab` will still be showing `another-page`.

> On iOS, tapping on the currently open tab, will automatically navigate to its `defaultPage`. So if you are in the `Home tab` and you tap on the `Home tab` again, the tab will go back to `home/home-page`.

#### Navigating back

iOS will automatically add a `back button` to the `ActionBar`. However for Android, you need to take care of it yourself.

This can be easily solved by adding a `NavigationButton` to your `ActionBar`:

```
<ActionBar class="action-bar">
	<NavigationButton tap="onBackButtonTap" android.systemIcon="ic_menu_back" />
	<Label class="action-bar-title" text="{{ name }}"></Label>
</ActionBar>
```

And then on `onBackButtonTap` you need to get the `Frame` and call `goBack()`. Like this:

```
export function onBackButtonTap(args: EventData) {
    const view = args.object as View;
    const frame = view.page.frame;

    frame.goBack();
}
```

#### Example

You can see this in action in [Playground](https://play.nativescript.org/?id=GDpmAk).

#### Template 

Or you can use a [ready made template from the marketplace](https://market.nativescript.org/plugins/tns-template-tab-navigation-ts)


To create a new project with a TabView, call `tns create my-tab-ts --template tns-template-tab-navigation-ts`.

### Frame Retrieval APIs

Prior to 4.0 all NativeScript applications had one topmost Frame implicitly created by the `application.start()` method. The `frameModule.topmost()` method returned this Frame. In 4.0 we now allow your app to have `multiple Frame instances`. This calls for a different API for Frame retrieval. Here is how you can get the Frame you want in 4.0:

 * `frameModule.topmost()` - The old method still exists and not only for backwards compatibility. It now returns the last navigated Frame or in case you are in a TabView, the currently selected tab item's Frame. This means that in straightforward cases you should be able to still rely on this method. However, for more complex cases or simply more control, you should use the methods below.
> `frameModule.topmost()` is great for `TabView Navigation`

 * `eventArgs.object.page.frame` - This can be used in event handlers. All event args objects should have a reference to their Page which in turn has a reference to its Frame instance. This allows you to retrieve the exact Frame that the event object is displayed in. 
> `eventArgs.object.page.frame` works well with `SideDrawer Navigation`

 * `frameModule.getFrameById(id)` - This is a new method. It allows you to get a reference to a Frame by an id that you specified on the element. Note that this searches for already navigated Frames and won't find Frames that are not yet displayed like in a modal view for example.
> `frameModule.getFrameById(id)` useful when more than one frame is on the Page, like for split screen scenarios.

## Angular

In order to use `Flexible Frame Composition` with Angular, you just need `nativescript-angular`: `5.3.0` or newer. Once that is done we are ready to go.

### SideDrawer

As compared to using `SideDrawer` in previous versions of NativeScript, the only change we need to make is to move all `SideDrawer` instances into your main component (by default that is `AppComponent`), which should contain:

 * a layout container marked with `tkDrawerContent` directive - which holds the drawer content
 * `page-router-outlet` marked with `tkMainContent` directive - which holds the app main content

 > Note, that `page-router-outlet` is the equivalent to NativeScript Core's `Frame Component`.
 
Like this:

#### app.component.html

```
<RadSideDrawer [drawerTransition]="sideDrawerTransition">
	<GridLayout tkDrawerContent rows="auto, *" class="sidedrawer sidedrawer-left">
		<StackLayout row="0" class="sidedrawer-header">
			<Label class="sidedrawer-header-image fa" text="&#xf2bd;"></Label>
			<Label class="sidedrawer-header-brand" text="User Name"></Label>
			<Label class="footnote" text="username@mail.com"></Label>
		</StackLayout>

		<ScrollView row="1">
			<StackLayout class="sidedrawer-content">
                <Button text="home" [nsRouterLink]="['/home']" (tap)="hideDrawer()" class="btn btn-primary"></Button>
                <Button text="browse" [nsRouterLink]="['/browse']" (tap)="hideDrawer()" class="btn btn-primary"></Button>
                <Button text="search" (tap)="goSearch()" class="btn btn-primary"></Button>
			</StackLayout>
		</ScrollView>
	</GridLayout>

	<page-router-outlet tkMainContent class="page page-content"></page-router-outlet>
</RadSideDrawer>
```

#### Navigation

Navigating now is a fairly straight forward Angular experience.

You can either do it:

 * from TypeScript:

 By injecting `RouterExtensions` to `AppComponent` and then calling `this.routerExtensions.navigate()`. Finally to tidy up, we need to get the drawer and close it. Like this:

```
constructor(private routerExtensions: RouterExtensions) {}

goSearch() {
	// navigate
    this.routerExtensions.navigate(['/search'], {
        transition: { name: "flip" }
    });
    
    // hide drawer
    const sideDrawer = <RadSideDrawer>app.getRootView();
    sideDrawer.closeDrawer();
}
```

 * or HTML:

By using `nsRouterLink` to provide the navigation path and (optional) `pageTransition` to provide type of transition. Finally to tidy up, we need to close the drawer.

```
<Button text="browse" [nsRouterLink]="['/browse']" (tap)="hideDrawer()" pageTransition="flip" class="btn btn-primary"></Button>
```

```
hideDrawer() {
    const sideDrawer = <RadSideDrawer>app.getRootView();
    sideDrawer.closeDrawer();
}
```

#### Example

You can see this in action in [Playground - Basic Example](https://play.nativescript.org/?id=l4du7Z) or [Playground - template example](https://play.nativescript.org/?id=1tISDO)

See `app.component` for all the magic 😉

#### Template

Or you can use a [ready made template from the marketplace](https://market.nativescript.org/plugins/tns-template-drawer-navigation-ng)

To create a new project with a SideDrawer, call `tns create my-drawer-ng --template tns-template-drawer-navigation-ng`.


### TabView




#### Example

You can see this in action in [Playground - Basic Example](https://play.nativescript.org/?id=)
<!--You can see this in action in [Based on the template](https://play.nativescript.org/?id=).-->

#### Template

Or you can use a [ready made template from the marketplace](https://market.nativescript.org/plugins/tns-template-tab-navigation-ng)

To create a new project with a TabView, call `tns create my-tab-ng --template tns-template-tab-navigation-ng`.

## Modals


## Multiple frames in one page
### Core: Frame id
### Angular: Named Page-Router-Outlet
nativescript-angular 6.0

## Navigation


## Where to find more examples