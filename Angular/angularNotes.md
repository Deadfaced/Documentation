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
