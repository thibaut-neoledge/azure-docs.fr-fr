## <a name="create-a-resource-group"></a>Créer un groupe de ressources

 Un [groupe de ressources Azure](https://docs.microsoft.com/azure/azure-resource-manager/resource-group-overview#resource-groups) est un conteneur logique dans lequel sont gérées les ressources telles que les applications web et les bases de données. Vous déployez, mettez à jour et supprimez les ressources dans un groupe de ressources.

Créez un groupe de ressources avec la commande [az group create](/cli/azure/group#create).

```azurecli
az group create --name myResourceGroup --location westeurope
```

Pour afficher les emplacements disponibles, utilisez la commande `az appservice list-locations`. Vous créez généralement des ressources dans une région proche de chez vous.

## <a name="create-an-azure-app-service-plan"></a>Créer un plan Azure App Service

Créez un [plan App Service](../articles/app-service/azure-web-sites-web-hosting-plans-in-depth-overview.md) « GRATUIT » avec la commande [az appservice plan create](/cli/azure/appservice/plan#create).

[!INCLUDE [app-service-plan](app-service-plan.md)]

La commande suivante crée un plan App Service nommé `quickStartPlan` en appliquant le niveau tarifaire **Gratuit**.

```azurecli
az appservice plan create --name quickStartPlan --resource-group myResourceGroup --sku FREE
```

Lorsque le plan App Service est créé, l’interface Azure CLI affiche des informations similaires à l’exemple suivant :

```json
{ 
  "adminSiteName": null,
  "appServicePlanName": "quickStartPlan",
  "geoRegion": "West Europe",
  "hostingEnvironmentProfile": null,
  "id": "/subscriptions/0000-0000/resourceGroups/myResourceGroup/providers/Microsoft.Web/serverfarms/quickStartPlan",
  "kind": "app",
  "location": "West Europe",
  "maximumNumberOfWorkers": 1,
  "name": "quickStartPlan",
  < JSON data removed for brevity. >
  "targetWorkerSizeId": 0,
  "type": "Microsoft.Web/serverfarms",
  "workerTierName": null
} 
```

## <a name="create-a-web-app"></a>Créer une application web