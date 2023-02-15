# Cheats Démo

# Partie 1
## Doc
https://angular.io/guide/upgrade#using-upgrademodule-with-angular-ngmodules

## Ajout de la dépendance angular upgrade

npm i @angular/upgrade@15.1.2

## Dépendance protractor

Suppression ou upgrade en v7
```json
"protractor": "^7.0.0",
```

## Modifications dans les assets angular.json pour charger les images et templates

```json
{
  "glob": "**/*.html",
  "input": "src/legacy",
  "output": "/"
},
{
"glob": "**/*.json",
"input": "src/legacy/phones",
"output": "/phones"
},
{
"glob": "**/*.jpg",
"input": "src/legacy/img/phones",
"output": "/img/phones"
}
```

## styles et le scripts

```json
"styles": [
  "src/styles.css",
  "node_modules/bootstrap/dist/css/bootstrap.css",
  "src/legacy/app.css",
  "src/legacy/app.animations.css"
],
"scripts": [
  "node_modules/jquery/dist/jquery.js",
  "node_modules/angular/angular.js",
  "node_modules/angular-animate/angular-animate.js",
  "node_modules/angular-resource/angular-resource.js",
  "node_modules/angular-route/angular-route.js",
  "src/legacy/app.module.js",
  "src/legacy/app.config.js",
  "src/legacy/app.animations.js",
  "src/legacy/core/core.module.js",
  "src/legacy/core/checkmark/checkmark.filter.js",
  "src/legacy/core/phone/phone.module.js",
  "src/legacy/core/phone/phone.service.js",
  "src/legacy/phone-list/phone-list.module.js",
  "src/legacy/phone-list/phone-list.component.js",
  "src/legacy/phone-detail/phone-detail.module.js",
  "src/legacy/phone-detail/phone-detail.component.js"
]
```

## app.module.ts
```typescript
  imports: [
    BrowserModule,
    UpgradeModule
  ],
```

## main.ts
```typescript
platformBrowserDynamic().bootstrapModule(AppModule)
  .then(plateformeRef => {
    const upgrade = plateformeRef.injector.get(UpgradeModule) as UpgradeModule;
    upgrade.bootstrap(document.body, ['phonecatApp'], {strictDi:false});
  })
  .catch(err => console.error(err));
```

# Partie 2

## Ajouter les types angular 
permet de faire les imports sans erreur
import {IAngularStatic, IDirectiveFactory} from "angular";
```shell
npm i --save-dev @types/angular@1.5.x
```
## phone.model.ts
```typescript
export interface IPhone{
  age: number;
  id:string;
  name:string;
  imageUrl:string;
  snippet:string;
}

export interface IPhoneDetail{
  additionalFeatures: number;
  android:{
    os:string;
    ui:string;
  };
  availability:string[];
  battery:{
    standbyTime:string;
    talkTime:string;
    type:string;
  };
  camera:{
    features:string[];
    primary:string;
  };
  connectivity:{
    bluetooth:string;
    cell:string;
    gps:boolean
    infrared:boolean
    wifi:string;
  };
  description:string;
  display:{
    screenResolution:string;
    screenSize:string;
    touchScreen:boolean;
  };
  hardware:{
    accelerometer:boolean;
    audioJack:string;
    cpu:string;
    fmRadio:boolean;
    physicalKeyboard:boolean;
    usb:string;
  };
  id:string;
  images:string[];
  name:string;
  sizeAndWeight:{
    dimensions:string[];
    weight:string;
  };
  storage:{
    flash:string;
    ram:string;
  };
}



```

### phone.service.ts
```typescript
  constructor(protected httpClient:HttpClient) { }

  query():Observable<HttpResponse<IPhone[]>>{
    return this.httpClient.get<IPhone[]>('phones/phones.json', {observe:"response"});
  }

  get(id:string):Observable<HttpResponse<IPhoneDetail>>{
    return this.httpClient.get<IPhoneDetail>(`phones/${id}.json `, {observe:"response"});
  }
```

### app.module.ts
```typescript
@NgModule({
  declarations: [
    AppComponent,
    PhoneListComponent,
    PhoneDetailComponent,
    CheckmarkPipe
  ],
  imports: [
    BrowserModule,
    UpgradeModule,
    FormsModule,
    ReactiveFormsModule,
    HttpClientModule
  ],
  bootstrap: [AppComponent]
})
export class AppModule { }
```

### main.ts
```typescript
declare const angular: angular.IAngularStatic;
interface DowngradeComponent {
  alias: string;
  ngComponent: any;
}

const ngDowngradeComponents: ReadonlyArray<DowngradeComponent> = [
  // alias must match selector name as CamelCase
  // otherwise angularjs won't load them
  {alias: 'appPhoneList', ngComponent: PhoneListComponent},
  {alias: 'appPhoneDetail', ngComponent: PhoneDetailComponent},
];

ngDowngradeComponents.forEach(dgComponent =>
  angular
    .module('phonecatApp')
    .directive(dgComponent.alias, downgradeComponent({component: dgComponent.ngComponent}) as angular.IDirectiveFactory)
);
```

### phone-list.component.html
```html
<div class="container-fluid">
  <div class="row">
    <form class="col-md-2" [formGroup]="criteriaForm">
      <!--Sidebar content-->

      <p>
        Search:
        <input formControlName="query"/>
      </p>

      <p>
        Sort by:
        <select formControlName="sort">
          <option value="name">Alphabetical</option>
          <option value="age">Newest</option>
        </select>
      </p>

    </form>
    <div class="col-md-10">
      <!--Body content-->

      <ul class="phones">
        <li *ngFor="let phone of phones | async" class="thumbnail phone-list-item">
          <a href="#!/phones/{{phone.id}}" class="thumb">
            <img [src]="phone.imageUrl" alt="{{phone.name}}" />
          </a>
          <a href="#!/phones/{{phone.id}}">{{phone.name}}</a>
          <p>{{phone.snippet}}</p>
        </li>
      </ul>

    </div>
  </div>
</div>
```

### phone-list.components.ts
```typescript
import { Component, OnInit } from '@angular/core';
import {BehaviorSubject, combineLatest, tap} from "rxjs";
import {FormBuilder, FormControl} from "@angular/forms";
import {IPhone} from "../phone/phone.model";
import {PhoneService} from "../phone/phone.service";

@Component({
  selector: 'app-phone-list',
  templateUrl: './phone-list.component.html',
  styleUrls: ['./phone-list.component.css']
})
export class PhoneListComponent implements OnInit {

  phones: BehaviorSubject<IPhone[]> = new BehaviorSubject<IPhone[]>([]);

  criteriaForm = this.fb.group({
    query: new FormControl(''),
    sort: new FormControl('age'),
  });

  constructor(private phoneService:PhoneService, private fb: FormBuilder) { }

  ngOnInit(): void {

    combineLatest([this.phoneService.query().pipe(tap(() =>  this.criteriaForm.updateValueAndValidity())),
      this.criteriaForm.valueChanges]).subscribe(v => {
        let phones = v[0].body ? [...v[0].body] : [];

        if(v[1].query){
          phones = phones.filter(p => p.name.toUpperCase().includes(v[1].query!.toUpperCase()));
        }

        this.phones.next(phones.sort(this.sortPhones(v[1].sort ?? "age")))
    });
  }

  sortPhones = (v:string) => (a:IPhone, b:IPhone)  => {
    if(v === "age") return a.age - b.age;
    if(v === "name") return a.name.localeCompare(b.name)
    return a.id.localeCompare(b.id)
  }
}
```

### checkmark.pipe.ts
```typescript
import { Pipe, PipeTransform } from '@angular/core';

@Pipe({
    name: 'checkmark'
})
export class CheckmarkPipe implements PipeTransform {

    transform(value: unknown, ...args: unknown[]): unknown {
        return value ? '\u2713' : '\u2718'
    }

}
```
