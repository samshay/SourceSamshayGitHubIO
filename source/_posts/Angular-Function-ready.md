---
title: Angular - Function ready
date: 2018-01-09 15:07:47
tags:
---

Pour un cas assez spécifique, j'ai eu besoin d'éxécuter du code javascript une fois que mon application angular soit chargé ( enfin sur tout que le template html soit affiché ), car je venais appliquer des éléments de styles sur les éléments graphiques affichés dans le template html.

Mais je me suis posé la question de savoir si la fonction `$(document).ready` éxécutait bien après que le html soit chargé.

Pour cela, rien ne vaut mieux qu'un exemple :

<script async src="//jsfiddle.net/hvf6X/56/embed/js,html,css/dark/"></script>

Dans ce JsFiddle, on remarque que l'on a l'enchainement suivant :
1. Debut ready angular
2. Controller
3. Fin ready angular
4. Ready document

Donc en mettant mon code dans `$(document).ready`, il s'éxécutera bien après que mon angular soit chargé.

> Attention : Si vous faites du traitement asynchrone, le `$(document).ready` n'attendra pas que celui-ci soit fini pour s'éxécuter.

