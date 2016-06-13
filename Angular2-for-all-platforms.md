
Angular is the most popular framework for building clinet side web applications (for more info see [Stack Overflow](http://stackoverflow.com/research/developer-survey-2016#most-popular-technologies-per-occupation)).
Over the last few years it enabled over 1 million developers to build great Single Page Web Applications.
However the framework was always limited to the web only, which meant that you would have to use totally different tools/languages/frameworks if you needed to target desktop and mobile applications as well. 

# Platform agnostic architecture

This is where Angular 2 comes into play. The new version is completely platform agnostic, which means that it is designed to be used with various different platforms (be it web, mobile or desktop).
In simple terms the architecture is split into two parts:
- Platform agnostic - where your markup (HTML) is parsed by a Dom Adapter and then compiled into a set of Proto Views. This process is not specific to any platforms and most of its pieces can be shared between platforms.
- Platform specific - here is where the magic happens. To target each platfrom you need a Platform Specific Renderer, which based on the Proto Views generates a Visual Tree (used to display the UI). The Renderer is also responsible for propagating the changes and events between the Proto Views and the Visual Tree.
![Angular2 Platform Agnostic](./images/Angular2-platform-agnostic.png "Angular2 Platform Agnostic")


# Adding mobile to the picture
## NativeScript

# Single codebase for all

# Examples
## Groceries
