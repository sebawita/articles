# Mobile Apps mit Angular 2 entwickeln

In den letzten Jahre haben mehr als eine Million Entwickler erfolgreich mit AngularJS Single-Page-Anwendungen erstellt. Angular ist damit das populärste Framework um client-seitige Webanwendungen zu entwickeln (siehe z.B.  [Stack Overflow](http://stackoverflow.com/research/developer-survey-2016#most-popular-technologies-per-occupation)). Allerdings blieb das Framework dabei bislang immer auf das Web beschränkt. Das bedeutet, dass man bis dato völlig andere Tools, Programmiersprachen und Frameworks benötigte, um Anwendungen für den Desktop oder für mobile Geräte an den Start zu bringen.


## Plattformunabhängige Architektur

Hier wird Angular 2 interessant. Der komplette Rewrite von AngularJS 1 wurde vollständig auf Plattformunabhängigkeit ausgerichtet. Das bedeutet, dass das Framework so entworfen wurde, dass diverse Plattformen angesprochen werden können (sei es Web, Mobil, Desktop und sogar [IoT-Geräte](https://medium.com/@urish/building-simon-with-angular2-iot-fceb78bb18e5)).
Vereinfacht ausgedrückt ist die Angular-2-Architektur in zwei Teile aufgeteilt:

- __Plattform-unabhängiger Teil__: hier wir das Markup (HTML) durch einen DOM-Adapter geparst und in so genannte „Proto Views“ compiliert. Dieser Prozess ist nicht spezifisch für eine Zielplattform und die meisten Funktionen können in den verschiedenen Plattformen genutzt werden
- __Plattform-spezifischer Teil__: hier geschieht die Magie. Es werden plattformspezifische Renderer verwendet, um die unterschiedlichen Zielplattform abzubilden. Jene Renderer haben die Aufgabe, aus den „Proto Views“ einen „Visual Tree“ zu generieren. Dieser kann dann verwendet werden, um die Oberfläche anzuzeigen. Der Renderer ist ebenso dafür verantwortlich, Änderungen und Events zwischen „Proto Views“ und „Visual Tree“ auszutauschen.


![Angular2 Platform Agnostic](./images/Angular2-platform-agnostic.png "Angular2 Platform Agnostic")

Durch diese durchdachte Architektur ist es möglich, neue Ziele zu definieren. Es müssen nur die notwendigen Erweiterungen implementiert werden.


## Native Mobile Anwendungen

Auf Grundlage der plattformunabhängigen Architektur kann [NativeScript](https://www.nativescript.org/) seine Stärken zeigen. NativeScript ist ein Open-Source-Framework, mit dem man native Apps für iOS, Android und [bald](https://www.nativescript.org/blog/details/nativescript-runtime-preview-for-windows-10) auch Windows 10 bzw. Windows Phone 10 entwickeln kann. „Nativ“ bedeutet, dass tatsächlich echte native UI-Elemente aus der JavaScript-Umgebung heraus angesprochen werden können. Seit Mitte 2015 arbeiten das Angular-Team und das NativeScript zusammen, um beide Frameworks miteinander zu verbinden. Das Ergebnis dieser Zusammenarbeit ist NativeScript 2.0 ([News](http://sdtimes.com/nativescript-2-0-brings-mobile-strategy-options-angularjs-developers/)).

Die Lösung für Angular 2 besteht darin, dass sehr spezifisches Markup in HTML definiert wird. Diese Markup kann dann vom DOM-Adapter „Parse5“ geparst werden. Den größten Anteil an der Umsetzung nimmt der „NativeScript Renderer“ ein. Dieser garantiert nicht zuletzt den Austausch zwischen „Proto Views“ und  den nativen UI Komponenten der jeweiligen Platform:

![Angular2 with NativeScript](./images/Angular2-with-NativeScript.png "Angular2 with NativeScript")


## Warum NativeScript?

### Kurzum: es funktioniert wunderbar mit Angular 2

Wenn man erstmal die neue [Template-Syntax](https://angular.io/docs/ts/latest/guide/template-syntax.html) von Angular gelernt hat, dann kann man das bestehende Wissen auf eine NativeScript-App übertragen. Hier ist ein einfaches Beispiel, wie ein Button verarbeitet wird. Es fällt auf, dass dies Komponente kein normales HTML beinhaltet:

```HTML
@Component({
  selector: "my-app",
  template: `
  <Page>
    <StackLayout>
      <Button text="Tap Me" (tap)="onTap()"></Button>
      <Label [text]="counter"></Label>
      <Label *ngIf="counter>10" text="WOW!!! you are really good"></Label>
    </StackLayout>
  </Page>
  `
})
export class MyComponent {
  counter: number = 0;
  onTap() {
    this.counter++;
  }
}
```

### Abstraktionsschicht

NativeScript hat eine 	beträchtliche Abstraktionsschicht an Board, welche die Unterschiede zwischen den unterstützen  Zielplattformen (iOS, Android, UWP) ausbügelt. Hierdurch kann man mit einer einzigen Code-Basis alle nennenswerten Geräte bedienen. Besonders wichtig ist eine gescheite UI-Abstraktion, bei der jede UI-Komponente eine eigene native Implementierung besitzen muss. Zum Glück müssen wir nicht diese spezifische Implantierungen selbst entwickeln. Es wurde bereits eine grundlegende Auswahl an Bedienelementen vom NativeScript-Team umsetzt. So können wir folgendes Markup definieren und erhalten eine Native Oberfläche, die in allen Betriebsystemen die jeweils zu erwarteten Bedienelemente besitzt:

NativeScript comes with an extensive abstraction layer, which allows you to build mobile apps with the single code for all supported platforms.
This applies to the UI Abstraction layer, where each [UI Component](http://docs.nativescript.org/ui/ui-views) has an underlying native implementation. For example the below code will give us a ***Native UI*** with a set of Native components.

```HTML
@Component({
  selector: "my-app",
  template: `
  <Page>
    <StackLayout>
      <Button text="GO" (tap)="onTap()" height="50" width="100"></Button>
      <Label [text]="selectedIndex" height="50" width="100"></Label>
      <ListPicker [selectedIndex]="selectedIndex" [items]="items"></ListPicker>
      <DatePicker [day]="day" [month]="month" [year]="year"></DatePicker>
      <TimePicker [hour]="hour" [minute]="minute"></TimePicker>
    </StackLayout>
  </Page>
  `
})
```

Also you get access to a rich set of [core modules](https://github.com/NativeScript/NativeScript/tree/master/tns-core-modules) that abstract out the native functionality. For example if you want to take a picture just use the [camera module](https://docs.nativescript.org/hardware/camera#using-the-camera-module-to-take-a-picture).

Just require the necessary module and call it. The module will take care of the platform specific details.

```JavaScript
import {Image} from "ui/image";
import cameraModule = require("camera");

cameraModule.takePicture().then(picture => {
    console.log("Result is an image source instance");
    var image = new Image();
    image.imageSource = picture;
});
```

And if you are curious what happens on Android ([github](https://github.com/NativeScript/NativeScript/blob/master/tns-core-modules/camera/camera.android.ts#L9-L111)) or iOS ([github](https://github.com/NativeScript/NativeScript/blob/master/tns-core-modules/camera/camera.ios.ts#L82-L126)) when you call takePicture or any of the modules, just check the [github repo](https://github.com/NativeScript/NativeScript/tree/master/tns-core-modules) with all of the core components.

### Direct access to the Native API
Obviously no abstraction layer can cover all the possible functions available on each supported platform. However that doesn't necessarily needs to be a problem, as NativeScript gives you a direct access to the Native API. This means that if you want you could make a direct call to Android or iOS APIs.

For example if you run the below ***JavaScript*** code on Android, you will get a new instance of a file object.

```JavaScript
function openFile(url) {
  var myFile = new java.io.File("filePath.txt");
  return myFile;
}
```


## Want to learn more?
There will be a couple of talks about NativeScript at DWX, which are really worth attending.
 * [Introduction to NativeScript](http://www.developer-week.de/Programm/Veranstaltung/(event)/20557) on Monday 20-June at 17:00 - in this talk you will learn what NativeScript is made of, how it works inside and you will see some JavaScript examples on how to build mobile apps with it.
 * [Native Mobile Apps mit NativeScript und Angular 2.0](http://www.developer-week.de/Programm/Veranstaltung/(event)/20683) on Wednesday 22-June at 9:00 - in this talk you will learn how we built the NativeScript 2.0, how does it work with Angular 2 and how to aim for a shared code base between mobile and web apps.

There is a great [Getting Started guide](http://docs.nativescript.org/angular/tutorial/ng-chapter-0), which you can follow to build a Native iOS and Android mobile app with Angular2 and NativeScript.

![Login Screen](./images/LoginScreen.png "Login Screen")
