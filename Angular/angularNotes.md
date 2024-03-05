# ANGULAR

## NG Directives
- `*ngIf`;
- `*ngFor`;

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
- bind an event to call the emit:
```html
<button type="button" (click)="notify.emit()">Notify Me</button>
```



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



### Managing data