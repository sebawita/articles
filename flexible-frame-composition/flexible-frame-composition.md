# Intro
<!-- talk about the problem -->

One of the most significant changes introduced in `NativeScript 4.0` is the way we interact with the main Frame of the app. Making it more flexible (hence the title) and a lot more intuitive.

# The problem

> TLTR: Old NativeScript can't share content between pages - more memory required, more CPU usage and slower performance

In previous versions of NativeScript we would always create a Page and place the content of the app in there. Then when the app would navigate to another view, NativeScript would create another Page and swap it in for the old one.

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

In order to use `Flexible Frame Composition` with Angular, you need to be using:

 * `nativescript-angular: 5.3.0` or `nativescript-angular: next` or newer (`6.0.0` is coming soon)
 * `@angular/x: 6.0.0`
 * all `NativeScript 4` prerequisites


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

With the SideDrawer Example we only needed one Page Router Outlet. However, for TabView Navigation, we need multiple `Named Page Router Outlets`, this feature is not part of `nativescript-angular: 5.3`, which is the latest official version of this npm module at the time of writing this article. 
This is going to be officially available in `nativescript-angular: 6.0`, so for now we are going to use `nativescript-angular@next`.

#### Named Page Router Outlet

Named Page Router Outlet is simply a `page-router-outlet` with a `name` property, like this:

```
<page-router-outlet name="myTab"></page-router-outlet>
```

The name allows us to link each Page Router Outlet to an `outlet` in our `Routes configuration`. (See the `app.routing.ts` section for more details)


#### app.component.html

So, for example if envisioned an app to work with two tabs:

 * `playerTab`
 * `teamTab`

Then we would need to add a TabView to `app.component.html` with two named `page-router-outlet` components.

```
<TabView androidTabsPosition="bottom">
  <page-router-outlet 
    *tabItem="{title: 'Players'}" 
    name="playerTab">
  </page-router-outlet>

  <page-router-outlet
    *tabItem="{title: 'Teams'}" 
    name="teamTab">
  </page-router-outlet>
</TabView>
```

#### app.routing.ts

Now we need to configure our routes. This is very similar to a regular `routes configuration`.

Here are our paths without `multiple` page-router-outlets:

```
const routes: Routes = [
  { path: '', redirectTo: '/players', pathMatch: 'full' },
  
  { path: 'players', component: PlayerComponent },
  { path: 'player/:id', component: PlayerDetailComponent },
  
  { path: 'teams', component: TeamsComponent},
  { path: 'team/:id', component: TeamDetailComponent },
];
```

First we need to add the `outlet` property to each path, to assign it to a specific router outlet. For example, for the `players` path, we need to assign it to `playerTab`, like this:

```
{ path: 'players', component: PlayerComponent, outlet: 'playerTab' },
```

Then we need to update the default (`redirectTo`) path. Like this:

```
{ path: '', redirectTo: '/(playerTab:players//teamTab:teams)', pathMatch: 'full' },
```

Note, that:

 * first we navigate to '/', which is for AppComponent,
 * then in `( )`, we define:
 	* each outlet default using syntax of: `tabname:path`
 	* each outlet default is separated with `//`


Here is the full configuration, for our example:

```
const routes: Routes = [
  { path: '', redirectTo: '/(playerTab:players//teamTab:teams)', pathMatch: 'full' },
  
  { path: 'players', component: PlayerComponent, outlet: 'playerTab'  },
  { path: 'player/:id', component: PlayerDetailComponent, outlet: 'playerTab'  },
  
  { path: 'teams', component: TeamsComponent, outlet: 'teamTab' },
  { path: 'team/:id', component: TeamDetailComponent, outlet: 'teamTab' },
];
```

Now, we just need to create all 4 components and we should be ready to go.

#### app.routing.ts - (other example)

If we had an app with a `Login Page`, and a `Tabs Page`, then our configuration would look something like this:

```
const routes: Routes = [
  { path: '', redirectTo: '/login', pathMatch: 'full' },
  //{ path: '', redirectTo: '/tabs/(playerTab:players//teamTab:teams)', pathMatch: 'full' },

  { path: 'login', component: LoginComponent },
  { path: 'tabs', component: TabsComponent, children: [
    { path: 'players', component: PlayerComponent, outlet: 'playerTab'  },
    { path: 'player/:id', component: PlayerDetailComponent, outlet: 'playerTab'  },
  
    { path: 'teams', component: TeamsComponent, outlet: 'teamTab' },
    { path: 'team/:id', component: TeamDetailComponent, outlet: 'teamTab' },
  ]}
];
```

#### Navigation - from Component Class

The final piece of the puzzle is navigation.

To navigate from component class we need to inject `RouterExtensions` and `ActivateRoute`,

```
import { RouterExtensions } from 'nativescript-angular/router';
import { ActivatedRoute } from '@angular/router';
...
export class PlayerComponent {
...
  constructor(
    private router: RouterExtensions,
    private route: ActivatedRoute
  ) { }
...
}
```

 * navigate - using relative path - from `players` to `player/:id`:

When we navigate, we can provide `{ relativeTo: this.route }`, this gives the router the context to know which `page-router-outlet` it should be navigating. As a result we can simply navigate by calling this:


```
navigateToPlayer(id: number) {
  this.router.navigate(['../player', id], { relativeTo: this.route });
}
```

> Note: `../<sibling>` is a way to reference a sibling path. This is how you can navigate from `home/a` to `home/b`, by providing the `../b` path.

 * navigate - clear history - from `player/x` to `players`:

Each time you navigate, the `navigation stack` gets updated. As a result iOS gives you a back button in ActionBar, and Android allows you to go back with the System Back Button.

This is not useful when you are just navigating back to the home or default page. Luckily, you can avoid that by adding the `clearHistory: true` property. 
```
navigateToPlayers() {
  this.router.navigate(['../../players'], { relativeTo: this.route, clearHistory: true });
}
```

> Note #1: `clearHistory` will only clear the history for the `page-router-outlets` that are navigated. So when you navigate in the `playersTab outlet`, `teamsTab outlet` remains unchanged. 

<!---->

> Note #2: when you try to navigate from `pageA/param` to `../pageB` will take you to `pageA/pageB` and fail. Since we have a parameter in our current path, we need to also escape it by navigating to `../../pageB`.

 * navigate - using absolute path:

We can also navigate using absolute path. This is done with the `outlets` property, and the name of the outlet we need, together with the path we want it to navigate to, like this:

```
this.router.navigate([{
  outlets: {
    outletName: ['path']
  }
}]);
```

 * navigate - using absolute path - from `players` to `player/x`:

```
navigatePlayerOutlet(id: number) {
  this.router.navigate([{
    outlets: {
      playerTab: ['player', id] 
    }
  }]);
}
```

> Note: each param of the route have to provided separately:
> 
> Good: `playerTab: ['player', id]`
> 
> Bad: `playerTab: ['player/id'] `

 * navigate - using absolute path - in another tab - from `players` to `team/x`:

You can also navigate in another outlet, like this:

```
navigateTeamOutlet(id: number) {
  this.router.navigate([{
    outlets: {
      teamTab: ['team', id] 
    }
  }]);
}
```

> Note: this is not going to switch the tab to the `Team Tab`, however after you navigate there, the `Team Tab` will be showing `team/id`.

 * navigate - using absolute path - multiple outlets: from `players` to `player/x` and `team/x`

```
navigateOutlets(playerId: number, teamId: number) {
  this.router.navigate([{
    outlets: { 
      playerTab: ['player', playerId],
      teamTab: ['team', teamId],
    }
  }]);
}
```

 * navigate with Url - from `players` to `team/x`

This approach is usually used when we are going to update all outlets. The syntax is the same the one we used for `redirectTo` in `app.routing.ts`.

In this case it allows us to navigate in the teamTab to `team/id`, while keeping the playerTab at `players`.

```
navigateTeamUrl(id: number) {
  this.router.navigateByUrl(`/(playerTab:players//teamTab:team/${id})`);
}
```

 * navigate - using relative path - to the same path, but with a different param - from `player/x` to `player/y`

To do that, we just need to go back one step `../`, which will take us to `/player`, and then provide the new id. Like this:

```
navigateToPlayer(id: number) {
  this.router.navigate(['../', id], { relativeTo: this.route });
}
```

 * navigate - using absolute path - to the same path, but with a different param - from `player/x` to `player/x+1`

When navigating using absolute path, there is no way for us to tell the router to stay at the same path. Therefore we have to provide the full path each time.

```
navigateNext() {
  this.router.navigate([{
    outlets: {
      playerTab: ['player', this.player.id + 1]
    }
  }]);
}
```


#### Navigation - from Component Template

We can also navigate directly from html. This is done with the support of the `nsRouterLink` property.

> Note: nsRouterLink can be used on any component: Button, Label or even StackLayout.

 * navigate - using fixed relative path - from `teams` to `team/1`:

When we use relative path, the router will automatically assume that you want to navigate in the same `page-router-outlet`.

Here is how we navigate from `teams` to `team/1`. First we need `../` to move one step back, and then add `team/1`, like this: 

```
<Button
  text="Open Team One"
  nsRouterLink="../team/1">
</Button>
```

* navigate - with pageTransition - from `teams` to `team/2`

```
<Button
  text="Open Team Two with Animation"
  nsRouterLink="../team/2"
  pageTransition="flip"
  pageTransitionDuration="3000">
</Button>
```

 * navigate - using relative path - with a param - from `teams` to `team/x`:

When you want to add a param, that should be extracted from a variable, we need to use `[ ]` around the `nsRouterLink`, and then provide the navigation as an array of items.

To navigate from `teams` to `team/id`, first we need `['../team'` path to the team, and then the param value `team.id]`, like this: 

```
<ListView [items]="items" class="list-group">
  <ng-template let-team="item">
    <Label
      [text]="team.name"
      [nsRouterLink]="['../team', team.id]">
    </Label>
  </ng-template>
</ListView>
```

 * navigate - using absolute path - from `teams` to `team/5`:

To navigate using relative paths, we need to provide an array with:

   1. path to where the component with our tabview is: `'/'`
   2. an object with `outlets` configuration, with the outlet name that we need to navigate

```
<Button
  text="Navigate using Outlet"
  [nsRouterLink]="['/', { outlets: { teamTab: ['team', 5] } }]">
</Button>
```

 * navigate - using absolute path - in another outlet - from `teams` to `player/5`:

This also works, when we want to navigate in `page-router-outlet` that is in another tab, like this:

```
<Button
  text="Navigate in Players Tab"
  [nsRouterLink]="['/', { outlets: { playerTab: ['player', 5] } }]">
</Button>
```

> Note: that this will change the content of the `playerTab`, but it won't change the current tab to the `Players Tab`.

 * navigate - using absolute path - with multiple outlets - from `teams` to `team/6` and `player/6`:

To make it better, you can even navigate multiple outlets in one go. Like this:

```
<Button
  text="Navigate in Two Outlets"
  [nsRouterLink]="['/', { outlets: { teamTab: ['team', 6], playerTab: ['player', 6] } }]">
</Button>
```

 * navigate - using relative path - to the same path, but with a different param - from `team/x` to `team/x+1`

When you need to re-navigate to the same page, but with a different param, you just need to go back one step with `../` and then provide the new param. Like this:

```
<Button
  text="prev"
  [nsRouterLink]="['../', team.id - 1]">
</Button>
```

 * navigate - using absolute path - to the same path, but with a different param - from `team/x` to `team/x+1`

When you need to re-navigate to the same page, using absolute path, you need to provide the full path each time. Like this:

```
<Button
  text="next"
  [nsRouterLink]="['/', { outlets: { teamTab: ['team', team.id + 1] } }]">
</Button>
```

 * navigate - back home - clear history - from `team/x` to `teams`

Finally, when you need to navigate back to the home component, you also might want to clear the navigation stack, so that iOS will remove the back button. This is done by adding `clearHistory="true", like this:

```
<Button
  text="Back to Teams"
  [nsRouterLink]="['../../teams']"
  clearHistory="true">
</Button>
```

#### Example

At the time of writing the article, Playground is using `nativescript-angular: 5.3`, which doesn't support `Named Page Router Outlets. So there is no Playground demo.

You can see my example project in [GitHub - frame-tabview-example](https://github.com/sebawita/frame-tabview-example).

<!--You can see this in action in [Playground - Basic Example](https://play.nativescript.org/?id=rnHxbr)-->


#### Template

The NativeScript team is working to update the current `tab-navigation-ng` template, which you should be able to get [from the marketplace](https://market.nativescript.org/plugins/tns-template-tab-navigation-ng).

 > To check if the template is updated, just check [app.component.html in GitHub](https://github.com/NativeScript/template-tab-navigation-ng/blob/master/app.component.html). It should be ready soon.

To create a new project with a TabView, call `tns create my-tab-ng --template tns-template-tab-navigation-ng`.

## Other usage

There are other scenarios that you could use:

 * navigation inside `Modals` - see example [here](https://github.com/NativeScript/NativeScript/tree/master/e2e/modal-navigation)
 * splitting screen between multiple frames - which can be achieved with the help of: 
 	* `frameModule.getFrameById(id)` for TS and,
 	* `Named Page Router Outlet` for NG

## Final

I hope that you found it useful and that I managed to cover most of the scenarios that you might need.

Please let me know if there are any scenarios that I didn't cover, or share any projects in GitHub or Playground where you used the Flexible Frame Composition.

Or just share your feedback in the comments.

