Dans Cloud Shell, créez une [application web](../articles/app-service/containers/app-service-linux-intro.md) dans le plan App Service `myAppServicePlan` avec la commande [az webapp create](/cli/azure/webapp#create). N’oubliez pas de remplacer `<app name>` par un nom d’application unique.

Le runtime de la commande suivante est défini sur `dotnetcore|1.1`. Pour voir tous les runtimes pris en charge, exécutez [az webapp list-runtimes](/cli/azure/webapp#list-runtimes). 

```azurecli-interactive
az webapp create --resource-group myResourceGroup --plan myAppServicePlan --name <app name> --runtime "dotnetcore|1.1" --deployment-local-git
```

Une fois l’application web créée, Azure CLI affiche une sortie similaire à celle de l’exemple suivant :

```json
Local git is configured with url of 'https://<username>@<app name>.scm.azurewebsites.net/<app name>.git'
{
  "availabilityState": "Normal",
  "clientAffinityEnabled": true,
  "clientCertEnabled": false,
  "cloningInfo": null,
  "containerSize": 0,
  "dailyMemoryTimeQuota": 0,
  "defaultHostName": "<app name>.azurewebsites.net",
  "deploymentLocalGitUrl": "https://<username>@<app name>.scm.azurewebsites.net/<app name>.git",
  "enabled": true,
  < JSON data removed for brevity. >
}
```

Vous avez créé une application web vide dans un conteneur Linux, pour lequel un déploiement Git est activé.

> [!NOTE]
> L’URL du Git distant est indiquée dans la propriété `deploymentLocalGitUrl`, avec le format `https://<username>@<app name>.scm.azurewebsites.net/<app name>.git`. Enregistrez cette URL, car vous en aurez besoin ultérieurement.
>
