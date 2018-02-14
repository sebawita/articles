

## Adding translation (Bonus)

![Speaking Languages](./images/Garfield-Languages.jpg?raw=true "Speaking Languages")

As a bonus we are going to cover how to use the `i18n` module for language specific text.

The way it works is rather straight forward. 
For every supported language you need a json resource file, which should contain key-translation value pairs for all the required phrases. 
Then to translate a phrase we use the `translate pipe`, which will look up the key and return the translated value. 

Let's try it out.

You can find the English resource file at `src/assets/i18n/en.json`. 

 1. Update/create the language files

Let's find the existing English resource file at `src/assets/i18n/en.json` and add the fields for the `simple` and `lazy` examples:

```
{
    "menu": {
        "home": "Home",
        "about": "About",
        "lazy": "Lazy",
        "simple": "Simple"
    },
    "home": {
        "title": "Home",
        "description": "This component is part of a module that was directly imported into the main AppModule."
    },
    "about": {
        "title": "About",
        "description": "This component was rendered from a lazy-loaded module."
    },
    "phone": {
        "edition": "Phone Edition!"
    },
    "simple": {
        "title": "Simply Red",
        "description": "Something got me started."
        "sayHello": "Say Hello"
    },
    "lazy": {
        "title": "Lazy joke",
        "jonsLine": "Jon: Garfield, you could give classes in doing nothing",
        "garfieldsLine": "Garfield: No, I couldn't"
    }
}
```

To add Spanish just add a new file called `es.json` with the same fields, but with the translated values to Spanish, like this (please note that I used the Google Translate service, so mistakes should be expected):

```
{
    "menu": {
        "home": "Casa",
        "about": "Acerca de",
        "lazy": "Perezoso",
        "simple": "Sencillo"
    },
    "home": {
        "title": "Casa",
        "description": "Este componente es parte de un módulo que fue importado directamente en el AppModule principal."
    },
    "about": {
        "title": "Acerca de",
        "description": "Este componente se procesó desde un módulo cargado de modo lento."
    },
    "phone": {
        "edition": "¡Edición del teléfono!"
    },
    "simple": {
        "title": "Simplemente rojo",
        "description": "Algo me hizo comenzar."
        "sayHello": "Di hola"
    },
    "lazy": {
        "title": "Broma perezosa",
        "jonsLine": "Jon: Garfield, podrías dar clases sin hacer nada",
        "garfieldsLine": "Garfield: No, no pude"
    }
}

```

 2. Update the navigation menu

The navigation menu already expects a translated value, so all we need to do is to update the MenuItems in `app.component.ts`. 
Change the `MenuItems` for simple and lazy to:

```
{
    title: 'menu.simple',
    link: ['/simple']
},
{
    title: 'menu.lazy',
    link: ['/lazy']
},
```

This will use the text value from `menu.simple` and `menu.lazy` from the required language.json file.

 3. Update the UI

To provide a language specific value, we are going to use the `translate` pipe, which will look for the translated value in the `language.json` file.

Open `lazy.component.html` and update it to:

<h1 class="title"
    [innerText]="'lazy.title' | translate"></h1>
<p class="description"
    [innerText]="'lazy.jonsLine' | translate"></p>
<p class="description"
    [innerText]="'lazy.garfieldsLine' | translate"></p>

The open `lazy.component.tns.html` and update it to:

```
<StackLayout>
    <Label class="h1 title"
        [text]="'lazy.title' | translate"></Label>

    <Label class="p description" textWrap="true"
        [text]="'lazy.jonsLine' | translate"></Label>
    <Label class="p description" textWrap="true"
        [text]="'lazy.garfieldsLine' | translate"></Label>
</StackLayout>
```

 4. Add language switch functionality to the project

Open `menu/components/menu/menu.component.ts` and add the following function to the `MenuComponent` class (which will switch to the provided language file):

```
switchLanguage(language: string) {
    this.translate.use(language);
}
```

Now let's add the switch buttons to the UI. 

Open `menu.component.html` and update it with two new buttons, which will switch between English and Spanish:

```
<seed-menu-item *ngFor="let item of items"
    [item]="item"></seed-menu-item>

<Button (click)="switchLanguage('en')" class="language-button">EN</Button>
<Button (click)="switchLanguage('es')" class="language-button">ES</Button>
```

We also need to something similar to the NativeScript menu. Open `menu.component.tns.html` and update it with:

```
<ScrollView orientation="horizontal">
    <StackLayout orientation="horizontal">
        <seed-menu-item *ngFor="let item of items"
            [item]="item"></seed-menu-item>
        <Button text="EN" (tap)="switchLanguage('en')" class="btn btn-primary"></Button> 
        <Button text="ES" (tap)="switchLanguage('es')" class="btn btn-primary"></Button>
    </StackLayout>
</ScrollView>
```

Now for the finishing touch let's add a bit of styling. Open `menu.component.scss` and add:

```
.language-button {
    background-color: #30bcff;
    border: none;
    color: white;
    padding: 8px 16px;
    text-align: center;
    text-decoration: none;
    display: inline-block;
    font-size: 16px;
}
```

 5. Test it

When you run the project, you should get something like this.

### Web

![Translation Web](./images/translation-web.gif?raw=true "Translation Web")

### Mobile

![Translation Mobile](./images/translation-mobile.gif?raw=true "Translation Mobile")


### Translate from TypeScript

To use the translation in TypeScript, you need to inject the `TranslationService` to your contructor.

```
import { TranslateService } from '@ngx-translate/core';
...
constructor(private translate: TranslateService) { }
```

Then you can just call `this.translate.get(_key_)`, which returns an Observable that will provide you with a result.

If we were to refactor the `SimpleComponent` then it would look something like this:

```
import { Component, OnInit } from '@angular/core';
import { TranslateService } from '@ngx-translate/core';
import 'rxjs/add/operator/first';

@Component({
  moduleId: module.id,
  selector: 'seed-simple',
  templateUrl: './simple.component.html',
  styleUrls: ['./simple.component.scss']
})
export class SimpleComponent implements OnInit {

  constructor(private translate: TranslateService) { }

  ngOnInit() {
  }

  sayHello() {
    // Hello message with translation
    this.translate.get('simple.hello').first()
    .subscribe(message => alert(message));
  }
}
```

Here is the full example: [simple.component.ts](https://github.com/sebawita/angular-native-seed/tree/examples/src/app/simple/simple.component.ts)

### Translate images

ASSETS_PIPE => SHARED_MODULE => TRASLATE|ASSETS

### Example summary

Using the `i18n` translation module is rather simple. Just create a `language.json` file for each language and use the `translation` pipe for each text that needs translating. 
You could even use the translation pipe to select a different image for each language.