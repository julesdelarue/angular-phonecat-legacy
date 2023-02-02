# Cheats Démo

## Doc 
https://angular.io/guide/upgrade#using-upgrademodule-with-angular-ngmodules

## Ajout de la dépendance angular upgrade

npm i @angular/upgrade

## Dépendance protractor

Suppression ou upgrade en v7

## Modifications dans angular.json pour charger les images et templates

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
