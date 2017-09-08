Créez une [application web](../articles/app-service-web/app-service-web-overview.md) dans le plan App Service `myAppServicePlan` avec la commande [az webapp create](/cli/azure/webapp#create). 

L’application web offre un espace d’hébergement pour votre code et fournit une URL permettant de visualiser l’application déployée.

Dans la commande ci-après, remplacez *\<app_name>* par un nom unique (les caractères autorisés sont `a-z`, `0-9` et `-`). Si `<app_name>` n’est pas une valeur unique, un message d’erreur s’affiche : « Un site web avec ce nom <app_name> existe déjà.» L’URL par défaut de l’application web est `https://<app_name>.azurewebsites.net`. 

```azurecli-interactive
az webapp create --name <app_name> --resource-group myResourceGroup --plan myAppServicePlan --deployment-local-git
```

Une fois l’application web créée, Azure CLI affiche des informations similaires à celles de l’exemple suivant :

```json
{
  "availabilityState": "Normal",
  "clientAffinityEnabled": true,
  "clientCertEnabled": false,
  "cloningInfo": null,
  "containerSize": 0,
  "dailyMemoryTimeQuota": 0,
  "defaultHostName": "<app_name>.azurewebsites.net",
  "enabled": true,
  "enabledHostNames": [
    "<app_name>.azurewebsites.net",
    "<app_name>.scm.azurewebsites.net"
  ],
  "gatewaySiteName": null,
  "hostNameSslStates": [
    {
      "hostType": "Standard",
      "name": "<app_name>.azurewebsites.net",
      "sslState": "Disabled",
      "thumbprint": null,
      "toUpdate": null,
      "virtualIp": null
    }
    < JSON data removed for brevity. >
}
```

Accédez au site pour voir votre nouvelle application web.

```bash
http://<app_name>.azurewebsites.net
```
