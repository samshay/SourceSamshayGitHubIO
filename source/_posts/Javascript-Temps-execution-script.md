---
title: Javascript - Temps exécution script
date: 2017-12-21 15:34:52
tags:
---

Il peut être utile parfois de mesure le temps d'éxécution d'un bout de code javascript.

Pour cela, c'est assez simple, on ajoute 

``` javascript
console.time("MonMessageDeDébut");
```

et cette ligne à la fin du code :
``` javascript
console.timeEnd("MonMessageDeFin");
```
Voici un exemple pour illuster ou je compare le temps d'éxécution d'un **for** natif et d'un **angular foreach**

``` javascript
console.time("native-for");
for (var i = 0; i < vm.listePays.length;i++) {
    if (vm.listePays[i].code==='FR')
    {
        vm.data.pays=vm.listePays[i];
    //break;
    }
}
console.timeEnd("native-for");


console.time("angular-foreach");
angular.forEach(vm.listePays, function(value, key) {
    if(value.code==='FR')
    {
        vm.data.pays=value;
    }
});
console.timeEnd("angular-foreach");
```

On visualise le résultat dans la console du navigateur :
``` 
native-for: 0.072ms
angular-foreach: 0.094ms
``` 
On remarque donc que le native-for est plus rapide que l'angular-foreach.
