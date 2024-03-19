<h1> ANGULAR </h1>

<h1>Table of Contents</h1>

- [1. Setup](#1-setup)
- [2. Create Project](#2-create-project)
- [3. NG Directives](#3-ng-directives)
  - [3.1. Building a Structural Directive](#31-building-a-structural-directive)
  - [3.2. Creating a Basic Attribute Directive](#32-creating-a-basic-attribute-directive)
    - [3.2.1. Using Renderer](#321-using-renderer)
  - [3.3. Using HostListener to listen to host events](#33-using-hostlistener-to-listen-to-host-events)
  - [3.4. Using HostBinding to Bind to Host Properties](#34-using-hostbinding-to-bind-to-host-properties)
    - [3.4.1. Binding to Directive Properties](#341-binding-to-directive-properties)
  - [3.5. Creating a class toggle using HostBinding and HostListener in a custom directive](#35-creating-a-class-toggle-using-hostbinding-and-hostlistener-in-a-custom-directive)
- [4. Passing data to a child component](#4-passing-data-to-a-child-component)
- [5. Passing data to a parent component](#5-passing-data-to-a-parent-component)
  - [5.1. Creating custom events](#51-creating-custom-events)
- [6. NAVIGATION / ROUTING](#6-navigation--routing)
  - [6.1. Passing and fetching parameters](#61-passing-and-fetching-parameters)
  - [6.2. Updating parameters reactively](#62-updating-parameters-reactively)
  - [6.3. Navigating programatically](#63-navigating-programatically)
    - [6.3.1. Navigating programatically with relative paths](#631-navigating-programatically-with-relative-paths)
- [7. Services](#7-services)
  - [7.1. Creating a Data Service](#71-creating-a-data-service)
- [8. Deploying to Firebase](#8-deploying-to-firebase)
  - [8.1. Updating a deployed site](#81-updating-a-deployed-site)
- [9. IMPORTANT COMMANDS](#9-important-commands)
  - [9.1. Creating new component](#91-creating-new-component)
  - [9.2. Creating new directive](#92-creating-new-directive)
  - [9.3. Adding Bootstrap](#93-adding-bootstrap)



## 1. Setup
- node latest version
    - install: https://nodejs.org/en
    - update: unistall node first, then install from the website 
- angular cli:
    - install:
    ```
    npm install -g @angular/cli
    ```
    - update: 
        - ```
          npm uninstall -g angular-cli @angular/cli
          ```              
        - ```
          npm cache verify
          ```
        - ```
          npm install -g @angular/cli
          ```



## 2. Create Project
- ```
  ng new "project-name" --no-strict --standalone false --routing false (optional) --skip-tests
  ```

**IMPORTANT:** when creating a new component it should be named without capital letters and separated by dashes (`-`);

description:<br>
    - `no-strict`: disable strict mode<br>
    - `standalone false`: <br>
    - `routing false`: <br>
    - `skip-tests`: doesn't create `component.specs.ts` file; <br>
- `cd "project-name"`;
- `code .`;
- `ng serve`.



### **TS example file**
```ts
import { Component, Input } from '@angular/core';
import { Product } from '../products';

@Component({
    standalone: true,
    imports: [],
    selector: 'app-product-alerts',
    templateUrl: './product-alerts.component.html',
    styleUrl: './product-alerts.component.css'
})
export class ProductAlertsComponent {

  @Input() product: Product | undefined;
  alertMe(){
    alert("SUCCESS!");
  }
}
```
Description
- `standalone`: if 'true' it's not needed to add in NG Modules
- `imports`: like 'Components' property in Vue
- `selector`: defines the name of this component's tag
- `template`/`templateUrl`: HTML code or link to HTML file
- `style`/`styleUrl`: CSS code or link to CSS stylesheet

**HTML example file**
```html
<button type="button" (click)="alertMe()">
    Notify Me!
</button>
```



## 3. NG Directives
Structural directives:
- `*ngIf` (replaced by `@if` in newer versions);
- `*ngFor` (replaced by `@for` in newer versions);
    - get the index when using ngFor: `*ngFor="let item of items; let i = index"`
- `ngSwitch`: must be used with `ngSwitchCase` and/or `ngSwitchDefault`.

ngSwitch example:
```html
<div [ngSwitch]="value">
  <p *ngSwitchCase="5">5</p>
  <p *ngSwitchCase="10">10</p>
  <p *ngSwitchCase="20">20</p>
  <p *ngSwitchDefault>0</p>
</div>
```

Built-in directives:
- `ngStyle` - `[ngStyle]="{style: condition}"` example: `[ngStyle]="{backgroundColor: getColor()}"`;
- `ngClass` - `[ngClass]="{'className': %condition%}"` example: `[ngClass]="{online: serverStatus === 'online'}"`;

### 3.1. Building a Structural Directive
For this example I will be creating an 'unless' directive that will be a negation of an ngIf
- create a new directive:

```cmd
  ng g d unless
```

Templates:

`unless.directive.ts`
```ts
import { Directive, Input, TemplateRef, ViewContainerRef } from '@angular/core';

@Directive({
  selector: '[appUnless]'
})

export class UnlessDirective{
  @Input() set appUnless(condition: boolean){
    if (!condition){
      this.vcRef.createEmbbededView(this.templateRef);
    } else{
      this.vcRef.clear();
    }
  }

  constructor(private templateRef: TemplateRef<any>, private vcRef: ViewContainerRef){ }
}
```

`main.component.html`
```html
<div *ngIf="condition">
  <p>True</p>
</div>
<div *appUnless="condition">
  <p>False</p>
</div>
```

Explanation>

- 'appUnless' is a property bound to the condition. We add a setter with the keyword `set` to execute a method whenever the condition changes;
- since we are working with boolean conditions this method will receive a boolean as parameter `(condition: boolean)`;
- we use `*appUnless="condition"` instead of `*appUnless="!condition"` because we are already checking the negation of the condition inside of the directive;
- angular automatically transforms `<div *ngIf="" ...>` to `<ng-template [ngIf]="" ...>`, which means 'ngIf' is a property bound to a condition just like 'appUnless'. So when we write `<div *appUnless="">` angular is transforming this to `<ng-template [app-Unless]=""`;
- since this is basically creating an `<ng-template />` we need to get this template in our directive so we pass it as parameter in its constructor as a `TemplateRef`;
- the `ViewComponentRef` gets the reference for the component it is inserted in. This is used to mark the place where angular will insert the template;
- the `createEmbeddedView()` method creates an embedded view (in this case the `TemplateRef`) inside the `ViewComponentRef`;



### 3.2. Creating a Basic Attribute Directive

- create a new folder like `basic-attribute` and inside create a directive file `basic-attribute.directive.ts`;
- this file must have the `@Directive` operator which must be imported from `@angular/core` and it will receive an object that must contain the 'selector' name (example: "[appBasicAttribute]"). This 'selector' name should have '[ ]' to tell that this 'selector' will add an attribute to the element and now when we call this directive on an element we don't need to put '[ ]' around its name because it's already declared in the 'selector' name;
- this class' constructor must receive the `ElementRef` as parameter and the onInit method should set the functionality of this directive;
- add this new directive in 'declarations' in `app.module.ts` and import it;
- now we can add this new directive in an element like this: `<p appBasicAttribute>I have a new directive!</p>`

`basic-attribute.directive.ts` example:
```ts
import { Directive, ElementRef, OnInit } from '@angular/core';

@Directive({
  selector: 'appBasicAttribute'
})

export class BasicAttributeDirective implements OnInit{
  constructor(private elRef: ElementRef){

  }

  ngOnInit(){
    this.elRef.nativeElement.style.backgroundColor = 'green';
  }
}
```

*notes:
- 'elRef' is any name you want to give. `ElementRef` is a reference to the element that this directive will receive as parameter in order to alter its attributes;
- `nativeElement` holds the underlying element of the DOM object;
- this is NOT a good practice. It's only mentioned for reference purposes.

#### 3.2.1. Using Renderer

Example:
```ts
import { Directive, OnInit, ElementRef, Renderer2 } from '@angular/core';

@Directive({
  selector: '[appBetterHighlight]'
})
export class BetterHighlightDirective implements OnInit{
  constructor(private elRef: ElementRef, private renderer: Renderer2){}

  ngOnInit(){
    this.renderer.setStyle(this.elRef.nativeElement, 'background-color', 'blue');
  }
}
```
Changes:
- we now import 'Renderer2' and also add it to the parameters of the constructor;
- in the `ngOnInit()` method we now call the renderer to access its methods like `setStyle()` to change the DOM element.



### 3.3. Using HostListener to listen to host events
- import 'HostListener' from '@angular/core';
- call the HostListener and specify the reserved name of the event (example: 'mouseover');
- we can pass the event data as parameter;
- inside the 'HostListener' method we add what we want to change when the event is triggered.

Example:
```ts
@HostListener('mouseenter') mouseOver(eventData: Event){
  this.renderer.setStyle(this.elRef.nativeElement, 'background-color', 'blue');
}

@HostListener('mouseleave') mouseLeave(eventData: Event){
  this.renderer.setStyle(this.elRef.nativeElement, 'background-color', 'transparent');
}
```



### 3.4. Using HostBinding to Bind to Host Properties
Usage:
```ts
@HostBinding('style.backgroundColor') bgColor: string = 'transparent';

@HostListener('mouseenter') mouseOver(eventData: Event){
  this.bgColor = 'blue';
}

@HostListener('mouseleave') mouseLeave(eventData: Event){
  this.bgColor = 'transparent';
}
```
*notes:
- HostBinding accepts 'style' (as seen above), 'class' (`@HostBinding('class.hidden')`), and 'attr' (`@HostBinding('attr.aria-required)`);
- also supports a style unit extension: `@HostBinding('style.width.px')`



#### 3.4.1. Binding to Directive Properties
By now we are hard coding the change of the background but we can bind it to a directive property to change it from outside the directive.

To do that we need to assign it to a variable with the @Input decorator so that it can be changed from outside

Usage:
```ts
@Input() defaultColor: string = 'transparent';
@Input() highlightColor: string = 'blue';

ngOnInit(){
  @HostBinding('style.backgroundColor') bgColor: string = this.defaultColor;
}

@HostListener('mouseenter') mouseOver(eventData: Event){
  this.bgColor = this.highlightColor;
}
@HostListener('mouseleave') mouseLeave(eventData: Event){
  this.bgColor = this.defaultColor;
}
```



### 3.5. Creating a class toggle using HostBinding and HostListener in a custom directive
`custom.directive.ts`
```ts
export class customDirective{
  @HostBinding('class.open') toggle = false;

  @HostListener('click') toggleDropdown(){
    this.toggle = !this.toggle;
  }
}
```

`app.component.html`
```html
<div class="dropdown btn-group" customDirective>
  ...
</div>
```



## 4. Passing data to a child component
- open terminal and create new component with the following command: `ng generate component user`;
- in the child component:
  - import `Input` from `@angular/core`;
  - define a property with an `@Input` decorator:
```ts
export class UserComponent {
    @Input() user: User;
}
```
- add component's class name (`UserComponent` in the above example) to 'declarations' in `AppModule`;
- in the parent component:
  - import the child component:
```ts
import { UserComponent } from './user.component';
```
  - (optional) create a property to save the data to be passed on to the child component:
```ts
export class UsersComponent {
    users: User[] = [
      {
        id: 1,
        name: 'John Doe'
      },
      ...
    ];

    firstUser: User = this.users[0];
}
```
  - pass the value when calling the component in the parent's html file by passing the property defined earlier:
```html
  <app-user user="firstUser">
```



## 5. Passing data to a parent component
- import `Output` and `EventEmitter` from `@angular/core`;
- define a property with an `@Output` decorator and an instance of `EventEmitter()`;
- add a click event (the data is passed by adding `$event`as a parameter):

**EXAMPLE**

`child.component.html`
```html
<button type="button" (click)="sendData($event)">Notify Me</button>
```

`child.component.ts`
```ts
export class ChildComponent{
  @Output() dataSender = new EventEmitter<dataType>

  sendData(event){
    this.dataSender.emit(event);
  }
}
```
Description:
- the `@Output` declarator allows data to be able to be passed to its parent component;
- we then create a property ("dataSender") as a new `EventEmitter` to tell the parent component that something has changed and inside the '<>' we tell the data type of the data we want to pass to the parent component;
- the `sendData()` is the method that is going to be called on click;
- then the `dataSender` event emitter needs to emit to the parent component the data we want to pass

`parent.component.html`
```html
<app-child-component (dataSender)="onDataSent($event)" />
```

`parent.component.ts`
```ts
export class ParentComponent{
  onDataSent(event){
    console.log(event);
  }
}
```
Description:
- in the parent component when we call its child component we add to it a 'dataSender' event instead of a click event and we assign it to a method created in the parent component (this 'dataSender' event needs to have the exact same name as an EventeEmitter from its child component);
- since we are passing the `$event` data in the method we can already access it in the parent component as shown in the `console.log(event)`;

### 5.1. Creating custom events
- creation example:

```html
<button type="button" (click)="onCreateServer($event)">Create Server</button>
```

```ts
serverCreated = new EventEmitter<{serverName: string, serverContent: string}>();

onCreateServer(event){
  this.serverCreated.emit(event);
}
```
- usage example:
```html
<app-server (serverCreated)="onServerCreation($event)" />
```

```ts
onServerCreation(event){
  ...
}
```
Explanation:

- 'serverCreated' is the name of the custom event; the `EventEmitter` is of generic type, hence the usage of '<>'; then it needs receive to some information and its data type, in this case it is receiving an object that contains two elements of type string; the '()' in the end serves to call the EventEmitter constructor so that the `new` keyword in the beginning of the statement can create a new object of EventEmitter to be stored in 'serverCreated';
- since we are assigning this EventEmitter to 'serverCreated' we then call it in the parent component inside the child component tag and assign it to an existing method in the parent component;

## 6. NAVIGATION / ROUTING
- in `AppModule` import 'RouterModule' and add a route with the desired paths
```ts
import { RouterModule } from '@angular/router';

...

imports:[
  BrowserModule,
  RouterModule.forRoot([
      {
        path: 'katakana-page',
        component: KatakanaPageComponent
      },
      {
        path: 'hiragana-page',
        component: HiraganaPageComponent
      },
      {
        path: 'products/:productId', //can pass an id
        component: ProductDetailsComponent
      },

      {//in case you want to add a 404 page
        path: '**',
        component: PageNotFoundComponent, //ng g c page-not-found
      }//useful when the user tries to brute force a route
  ]);
];
```

- replace the content in the parent component to have a router-outlet:

`app-parent.component.html` delete this:
```html
<app-hiragana-page />
<app-katakana-page />
```

`app-parent.component.html` add this:
```html
<router-outlet />
```

- in parent component include `routerLink`:
```html
<a routerLink="/hiragana-page">
  Hiragana
</a>
```

or passing a bindable property (variable):
```html
<a [routerLink]="['/products', product.id]">
    {{ product.name }}
</a>
```

**note:* you can add the `routerLinkActive` directive to add a class to the `<a>` or `<button>` when that routerLink is selected. Example:
```html
<a routerLink="/hiragana-page" routerLinkActive="text-danger">
  Hiragana
</a>
```

### 6.1. Passing and fetching parameters
- in case we want to pass parameters (such as an id for example) we first need to add the new path to our Routes array and add a variable as parameter:
```ts
Routes = [
  ...
  {
    path: 'products/:id',
    component: ProductComponent
  }
]
```
*the ':' means that 'id' will be a variable*

- then we need to add a way to get to that route like adding a routerLink to an anchor tag:
```html
<a *ngFor="product of products" [routerLink]="['/products'], product.id">
```

- after that in the child component we have to import `ActivatedRoute` from `@angular/router`, which will tell angular what is the current route, and `OnInit` from `@angular/core` as well as define the property implementing `OnInit` interface (this requires the `ngOnInit` method):
```ts
import { Component, OnInit } from '@angular/core';
import { ActivatedRoute } from '@angular/router';

export class ProductDetailsComponent implements OnInit {
    product: Product;

    constructor(private route: ActivatedRoute) { }

}
```

- for the last step we add the `ngOnInit` method and extract the id from the current route's parameters which we will then use to find said id in the array to retrieve its' content:
```ts
ngOnInit() {
    // First get the product id from the current route.
    const routeParams = this.route.snapshot.paramMap; //'route.snapshot' accesses the route parameters
    const productIdFromRoute = Number(routeParams.get('productId'));

    // Find the product that correspond with the id provided in route.
    this.product = products.find(product => product.id === productIdFromRoute);
}
```

### 6.2. Updating parameters reactively

The above code lets us receive the route parameters but only when its' directive is instantiated. To reactively update these parameters we need to use an observable.

Observables are a way to deal with asyncronous operations as you can subscribe to them to be on the lookout for any changes that may occur and act reactively.

For this we need to subscribe to the route's params (which are observables):
```ts
ngOnInit(){
  this.route.params
    .subscribe(
      (params: Params) => { //'Params' needs to be imported from @angular/router
        this.product.id = params['id'];
      }
    );
}
```

### 6.3. Navigating programatically
In case we want to navigate to a certain link after performing an action (like a method) we can navigate programatically:
- import `Router` from `@angular/router` and call it in the constructor:
```ts
constructor(private router: Router) { }
```
- use `router.navigate` method to signal to which route you want to navigate to:
```ts
onLoadServer(){
  this.router.navigate(['/servers'];)
}
```
The example above is using an absolute path.

#### 6.3.1. Navigating programatically with relative paths

To navigate to a relative path we must first remove the slash ('/') from the path on the navigate method. Then we need to tell angular that the path we are passing is a relative path to the activated route aka the route we are currently in. For that we need to import `Activated Route` from `@angular/router` and call it in the constructor:
```ts
constructor(private router: Router, private route: ActivatedRoute) {}

onCreateServer(){
  this.router.navigate(['newServer'], {relativeTo: this.route});
}
```
This method is telling angular that we want to navigate to the path `newServer` which is relative to the current path, so the url would become something like `/servers/newServer`.



## 7. Services
A service is a class that acts as a central storage that can be accessed anywhere

Creation:

create a file name `fileName.service.ts` in the `app` folder or the `app/shared` folder or somewhere central

`logging.service.ts`
```ts
export class LoggingService{
  logStatusChange(status: string){
    console.log('A server status has changed, new status: ' + status);
  }
}
```

Usage:

`new-server.component.ts`
```ts
import { LoggingService } from '../logging.service'

@Component({
  ...
  providers: [LoggingService], //tells the component what services will be provided
})

export class NewServerComponent{
  constructor(private logService: LoggingService){}

  onServerCreate(serverStatus: string){
    this.logService.logStatusChange(serverStatus);
  }
}
```
*don't forget to:
- import the service;
- declare the provider;
- add the data type as a constructor parameter;

**Additional notes:**
- when injecting a service into another service we should add the declarator `@Injectable()` to the service receiving the injection;
- we should also add the service in the 'providers' array in `app.module.ts`;
- when we want to use the same service/s in sibling components we can import those services in the parent component. There's no harm in it and it will propagate to children components

### 7.1. Creating a Data Service
`shopping-list.service.ts`
```ts
ingredientsChanged = new EventEmitter<Ingredient[]>();

private ingredients: Ingredient[] = [
    new Ingredient('Apples', 5),
    new Ingredient('Strawberries', 17),
    new Ingredient('Bananas', 12),
    new Ingredient('Pineapples', 2),
];

getIngredients(){
    return this.ingredients.slice();
}
```

`shopping-list.component.ts`
```ts
import { Ingredient } from ...;
import { ShoppingListService } from ...;

export class ShoppingListComponent implements OnInit{
  ingredients = Ingredient[];

  constructor(private shoppingListService: ShoppingListService){}
.
  ngOnInit(){
    this.ingredients = this.shoppingListService.getIngredients();
    this.shoppingListService.ingredientsChanged.subscribe((ingredients: Ingredient[]) => {
      this.ingredients = ingredients;
    })
  }
}
```
**IMPORTANT**

The subscribe method is linked to an EventEmitter to update whenever changes were made and should be called in `ngOnInit()`



## 8. Deploying to Firebase
- use the command `ng build` to convert everything to JS code to be ran in the browser, this will create the folder `dist` in your root folder;
- install firebase's CLI with `npm i -g firebase-tools`;
- login to https://console.firebase.google.com/ and create a new project;
- login to firebase's CLI with `firebase login`;
- navigate to the project's directory, run the command `firebase init` and follow the next steps:
  - "Are you ready to proceed?" `Yes`;
  - "Which Firebase features do you want to set up for this directory? ..." `Hosting: Configure files for Firebase Hosting and (optionally) set GitHub Actions deploy`;
  - `Use an existing option` and select the project you previously set up in firebase's web site;
  - "What do you want to use as your public directory?" `dist/jp-practice/browser` **NOTE:** this will look for the file `index.html` in your `dist` folder, so the exact directory may vary and 'jp-practice' is the name of my folder and will be named according to your project's name;
  - "Configure as a single-page app (rewrite all urls to /index.html)?" `Yes` this is crucial for single-page applications (Vue, Angular, etc.);
  - "Set up automatic builds and deploys with GitHub?" `No`;
  - "File 'dist/jp-practice/browser/index.html' already exists. Overwrite?" `No`;
- lastly run the command `firebase deploy` and CTRL + Click the url given in "Hosting URL".

### 8.1. Updating a deployed site
After all the changes are made simply run these commands:
- `ng build`;
- `firebase deploy`.



## 9. IMPORTANT COMMANDS

### 9.1. Creating new component

- ```cmd
  ng generate component 'componentName'
  ```

- ```cmd
  ng g c 'componentName'
  ```

### 9.2. Creating new directive

- ```cmd
  ng generate directive ´directiveName'
  ```

- ```cmd
  ng g d ´directiveName'
  ```

### 9.3. Adding Bootstrap
- ```
  npm install --save bootstrap@5
  ```
    (@5 refers to version 5 of Bootstrap)
- in file `angular.json` add the bootstrap file in "styles" section: 
```
"node_modules/bootstrap/dist/css/bootstrap.min.css"
```