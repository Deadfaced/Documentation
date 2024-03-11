<h1> ANGULAR </h1>

<h1>Table of Contents</h1>

- [Setup](#setup)
- [Create Project](#create-project)
  - [Description](#description)
  - [**HTML example file**](#html-example-file)
- [Passing data to a child component](#passing-data-to-a-child-component)
- [Passing data to a parent component](#passing-data-to-a-parent-component)
    - [**EXAMPLE**](#example)
  - [Creating custom events](#creating-custom-events)
- [NAVIGATION](#navigation)
  - [Creating a Basic Attribute Directive](#creating-a-basic-attribute-directive)
    - [Using Renderer](#using-renderer)
  - [Using HostListener to listen to host events](#using-hostlistener-to-listen-to-host-events)
  - [Using HostBinding to Bind to Host Properties](#using-hostbinding-to-bind-to-host-properties)
    - [**Binding to Directive Properties**](#binding-to-directive-properties)
- [IMPORTANT COMMANDS](#important-commands)
  - [Creating new component](#creating-new-component)
  - [Creating new directive](#creating-new-directive)
  - [Adding Bootstrap](#adding-bootstrap)


## Setup
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



## Create Project
- ```
  ng new "projectName" --no-strict --standalone false --routing false (optional) --skip-tests
  ```

description:<br>
    - `no-strict`: disable strict mode<br>
    - `standalone false`: <br>
    - `routing false`: <br>
    - `skip-tests`: doesn't create `component.specs.ts` file; <br>
- `cd "projectName"`;
- `code .`
- `ng serve`

### NG Directives 
- `*ngIf` (replaced by `@if` in newer versions);
- `*ngFor` (replaced by `@for` in newer versions);
    - get the index when using ngFor: `*ngFor="let item of items; let i = index"`
- `ngStyle` - `[ngStyle]="{style: condition}"` example: `[ngStyle]="{backgroundColor: getColor()}"`;
- `ngClass` - `[ngClass]="{'className': %condition%}"` example: `[ngClass]="{online: serverStatus === 'online'}"`;

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
### Description
- `standalone`: if 'true' it's not needed to add in NG Modules
- `imports`: like 'Components' property in Vue
- `selector`: defines the name of this component's tag
- `template`/`templateUrl`: HTML code or link to HTML file
- `style`/`styleUrl`: CSS code or link to CSS stylesheet

### **HTML example file**
```html
<button type="button" (click)="alertMe()">
    Notify Me!
</button>
```



## Passing data to a child component
- open terminal and create new component with the following command: `ng generate component product-alerts`;
- import `Input` from `@angular/core`;
- define a property with an `@Input` decorator:
```ts
export class ProductAlertsComponent {
    @Input() product: Product | undefined;
}
```
- remove `standalone` and `imports` properties from child component;
- add component's class name (`ProductAlertsComponent` in the above example) to 'declarations' in `AppModule` and import the child component:
```ts
import { ProductAlertsComponent } from './product-alerts/product-alerts.component';
```



## Passing data to a parent component
- import `Output` and `EventEmitter` from `@angular/core`;
- define a property with an `@Output` decorator and an instance of `EventEmitter()`;
- add a click event (the data is passed by adding `$event`as a parameter):

#### **EXAMPLE**

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
- we then create a property ("dataSender") as a new `EventEmitter` to tell the parente component that something has changed and inside the '<>' we tell the data type of the data we want to pass to the parent component;
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

### Creating custom events
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

## NAVIGATION
- in `AppModule` add a route with a path
```ts
RouterModule.forRoot([
    { path: 'products/:roductId', component: ProductDetailsComponent },
])
```
- in parent component include `routerLink`:
```ts
<a [routerLink]="['/products', product.id]">
    {{ product.name }}
</a>
```
- in child component import `ActivatedRoute` from `@angular/router` and `OnInit` from `@angular/core`
```ts
import { Component, OnInit } from '@angular/core';
import { ActivatedRoute } from '@angular/router';
```
- define the property implementing `OnInit` interface (this requires the `ngOnInit` method)
```ts
export class ProductDetailsComponent implements OnInit {
    product: Product | undefined;
}
```
- inject `ActivatedRoute` into the constructor
```ts
export class ProductDetailsComponent implements OnInit {

    product: Product | undefined;

    constructor(private route: ActivatedRoute) { }

}
```
- in the child component add the `ngOnInit` method and extract the id from the route parameters and find said id in the array
```ts
ngOnInit() {
    // First get the product id from the current route.
    const routeParams = this.route.snapshot.paramMap; //'route.snapshot' accesses the route parameters
    const productIdFromRoute = Number(routeParams.get('productId'));

    // Find the product that correspond with the id provided in route.
    this.product = products.find(product => product.id === productIdFromRoute);
}
```



### Creating a Basic Attribute Directive

- create a new folder like `basic-attribute` and inside create a directive file `basic-attribute.directive.ts`;
- this file must have the `@Directive` operator which must be imported from `@angular/core` and it will receive an object that must contain the 'selector' name (example: "[appBasicAttribute]"). This 'selector' name should have '[]' to tell that this 'selector' will add an attribute to the element and now when we call this directive on an element we don't need to put '[]' around its name because it's already declared in the 'selector' name;
- this class' constructor must receive the `ElementRef` as parameter and the onInit method should set the functionality of this directive;
- add this new directive in 'declarations' in `app.module.ts` and import it;
- now we can add this new directive in an element like this: `<p appBasicAttribute>I have a new directive!</p>

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

#### Using Renderer

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



### Using HostListener to listen to host events
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



### Using HostBinding to Bind to Host Properties
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



#### **Binding to Directive Properties**
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



## IMPORTANT COMMANDS

### Creating new component

- ```cmd
  ng generate component 'componentName'
  ```

- ```cmd
  ng g c 'componentName'
  ```

### Creating new directive

- ```cmd
  ng generate directive ´directiveName'
  ```

- ```cmd
  ng g d ´directiveName'
  ```

### Adding Bootstrap
- ```
  npm install --save bootstrap@5
  ```
    (@5 refers to version 5 of Bootstrap)
- in file `angular.json` add the bootstrap file in "styles" section: 
```
"node_modules/bootstrap/dist/css/bootstrap.min.css"
```