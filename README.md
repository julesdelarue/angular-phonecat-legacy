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

## styles et le scripts

```json
"assets": [
    "src/favicon.ico",
    "src/assets",
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
],
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
# Partie 2

## Ajouter les types angular
```shell
npm i --save-dev @types/angular@1.5.x @types/angular-route@^1.7.2
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
  additionalFeatures: string;
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

### main.ts
```typescript
import {IDirectiveFactory, module} from "angular";

module('phonecatApp')
    .directive("appPhoneDetail", downgradeComponent({component: PhoneDetailComponent}) as IDirectiveFactory)
```



### phone-detail.component.html
```html
    <div class="phone-images">
        <img [src]="mainImageUrl ? mainImageUrl : phone.images[0]" class="phone"/>
    </div>

    <h1>{{phone.name}}</h1>

    <p>{{phone.description}}</p>

    <ul class="phone-thumbs">
        <li *ngFor="let img of phone.images">
            <img [src]="img" (click)="setImage(img)"/>
        </li>
    </ul>

    <ul class="specs">
        <li>
            <span>Availability and Networks</span>
            <dl>
                <dt>Availability</dt>
                <dd *ngFor="let availability of phone.availability">{{availability}}</dd>
            </dl>
        </li>
        <li>
            <span>Battery</span>
            <dl>
                <dt>Type</dt>
                <dd>{{phone.battery.type}}</dd>
                <dt>Talk Time</dt>
                <dd>{{phone.battery.talkTime}}</dd>
                <dt>Standby time (max)</dt>
                <dd>{{phone.battery.standbyTime}}</dd>
            </dl>
        </li>
        <li>
            <span>Storage and Memory</span>
            <dl>
                <dt>RAM</dt>
                <dd>{{phone.storage.ram}}</dd>
                <dt>Internal Storage</dt>
                <dd>{{phone.storage.flash}}</dd>
            </dl>
        </li>
        <li>
            <span>Connectivity</span>
            <dl>
                <dt>Network Support</dt>
                <dd>{{phone.connectivity.cell}}</dd>
                <dt>WiFi</dt>
                <dd>{{phone.connectivity.wifi}}</dd>
                <dt>Bluetooth</dt>
                <dd>{{phone.connectivity.bluetooth}}</dd>
                <dt>Infrared</dt>
                <dd>{{phone.connectivity.infrared }}</dd>
                <dt>GPS</dt>
                <dd>{{phone.connectivity.gps}}</dd>
            </dl>
        </li>
        <li>
            <span>Android</span>
            <dl>
                <dt>OS Version</dt>
                <dd>{{phone.android.os}}</dd>
                <dt>UI</dt>
                <dd>{{phone.android.ui}}</dd>
            </dl>
        </li>
        <li>
            <span>Size and Weight</span>
            <dl>
                <dt>Dimensions</dt>
                <dd *ngFor="let dim of phone.sizeAndWeight.dimensions">{{dim}}</dd>
                <dt>Weight</dt>
                <dd>{{phone.sizeAndWeight.weight}}</dd>
            </dl>
        </li>
        <li>
            <span>Display</span>
            <dl>
                <dt>Screen size</dt>
                <dd>{{phone.display.screenSize}}</dd>
                <dt>Screen resolution</dt>
                <dd>{{phone.display.screenResolution}}</dd>
                <dt>Touch screen</dt>
                <dd>{{phone.display.touchScreen }}</dd>
            </dl>
        </li>
        <li>
            <span>Hardware</span>
            <dl>
                <dt>CPU</dt>
                <dd>{{phone.hardware.cpu}}</dd>
                <dt>USB</dt>
                <dd>{{phone.hardware.usb}}</dd>
                <dt>Audio / headphone jack</dt>
                <dd>{{phone.hardware.audioJack}}</dd>
                <dt>FM Radio</dt>
                <dd>{{phone.hardware.fmRadio}}</dd>
                <dt>Accelerometer</dt>
                <dd>{{phone.hardware.accelerometer}}</dd>
            </dl>
        </li>
        <li>
            <span>Camera</span>
            <dl>
                <dt>Primary</dt>
                <dd>{{phone.camera.primary}}</dd>
                <dt>Features</dt>
                <dd>{{phone.camera.features.join(', ')}}</dd>
            </dl>
        </li>
        <li>
            <span>Additional Features</span>
            <dd>{{phone.additionalFeatures}}</dd>
        </li>
    </ul>
```



### phone-detail.component.ts
```typescript
import { Component } from '@angular/core';
import {IPhoneDetail} from "../phone.model";
import {PhoneService} from "../phone.service";
import {LegacyRouteService} from "../app.module";

@Component({
  selector: 'app-phone-detail',
  templateUrl: './phone-detail.component.html',
  styleUrls: ['./phone-detail.component.css']
})
export class PhoneDetailComponent {

  phone?:IPhoneDetail;

  mainImageUrl?:string;
 
  // constructor(private phoneService:PhoneService,
  //             private legacyRoutingService:LegacyRouteService) { }
  //
  // ngOnInit(): void {
  //   if(this.legacyRoutingService.current){
  //     this.phoneService.get(this.legacyRoutingService.current.params['phoneId']).subscribe(s => this.phone = s.body!)
  //   }
  // }

  setImage(img: string) {
    this.mainImageUrl = img;
  }
}
```

### phone.service.ts
```typescript
import { Injectable } from '@angular/core';
import {HttpClient, HttpResponse} from "@angular/common/http";
import {IPhone, IPhoneDetail} from "./phone.model";
import {Observable} from "rxjs";

@Injectable({
    providedIn: 'root'
})
export class PhoneService {

    constructor(protected httpClient:HttpClient) { }

    query():Observable<HttpResponse<IPhone[]>>{
        return this.httpClient.get<IPhone[]>('phones/phones.json', {observe:"response"});
    }

    get(id:string):Observable<HttpResponse<IPhoneDetail>>{
        return this.httpClient.get<IPhoneDetail>(`phones/${id}.json `, {observe:"response"});
    }
}
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

### app.module.ts pour service
```typescript
import { NgModule } from '@angular/core';
import { BrowserModule } from '@angular/platform-browser';

import { AppComponent } from './app.component';
import {UpgradeModule} from "@angular/upgrade/static";
import { PhoneDetailComponent } from './phone-detail/phone-detail.component';

import 'angular-route';
import {route} from 'angular';
import {HttpClientModule} from "@angular/common/http";

export abstract class LegacyRouteService implements route.IRouteService {
  abstract reload(): void;
  routes: any;
  current?: route.ICurrentRoute | undefined;
  abstract updateParams(newParams: { [key: string]: string; }): void;
}

@NgModule({
  declarations: [
    AppComponent,
    PhoneDetailComponent
  ],
  imports: [
    BrowserModule,
    UpgradeModule,
    HttpClientModule
  ],
  providers:[
    {provide: LegacyRouteService, useFactory: (i: any) => i.get('$route'), deps: ['$injector']},
  ],
  bootstrap: [AppComponent]
})
export class AppModule { }
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
