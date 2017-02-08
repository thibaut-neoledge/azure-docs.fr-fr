
Le code de toutes les fonctions dans une application de fonction donnée se trouve dans un dossier racine contenant un fichier de configuration d’hôte et un ou plusieurs sous-dossiers, chacun d’entre eux contenant le code pour une fonction distincte, comme dans l’exemple suivant

```
wwwroot
 | - host.json
 | - mynodefunction
 | | - function.json
 | | - index.js
 | | - node_modules
 | | | - ... packages ...
 | | - package.json
 | - mycsharpfunction
 | | - function.json
 | | - run.csx
```

Le fichier *host.json* contient une configuration spécifique de l’exécution et se trouve dans le dossier racine de l’application de fonction. Pour plus d’informations sur les paramètres disponibles, voir [host.json](https://github.com/Azure/azure-webjobs-sdk-script/wiki/host.json) dans le Wiki du référentiel WebJobs.Script.

Chaque fonction a un dossier contenant un ou plusieurs fichiers de code, la configuration de function.json et d’autres dépendances.



<!--HONumber=Nov16_HO3-->


