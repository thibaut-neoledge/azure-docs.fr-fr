---
title: "Créer une application hyperscale dans Azure | Microsoft Docs"
description: "Découvrez comment utiliser différents services Azure pour optimiser les performances d’une application ASP.NET dans Azure."
services: app-service\web
documentationcenter: dotnet
author: cephalin
manager: erikre
editor: 
ms.assetid: a4d49ac7-0f97-4997-84c5-cdb9c4465757
ms.service: app-service-web
ms.workload: web
ms.tgt_pltfrm: na
ms.devlang: nodejs
ms.topic: article
ms.date: 03/23/2017
ms.author: cephalin
translationtype: Human Translation
ms.sourcegitcommit: 26d460a699e31f6c19e3b282fa589ed07ce4a068
ms.openlocfilehash: 48ce80d2a25c6ad55e0520ea4cbdbc82db23f919
ms.lasthandoff: 04/04/2017


---
# <a name="build-a-hyper-scale-web-app-in-azure"></a>Créer une application hyperscale dans Azure

Ce didacticiel vous montre comment faire évoluer une application web ASP.NET dans Azure afin d’optimiser les requêtes d’utilisateurs.

Avant de commencer, vérifiez que [l’interface de ligne de commande Azure est installée](https://docs.microsoft.com/en-us/cli/azure/install-azure-cli) sur votre ordinateur. [Visual Studio](https://www.visualstudio.com/vs/) doit par ailleurs exécuter l’exemple d’application sur votre ordinateur local.

## <a name="step-1---get-sample-application"></a>Étape 1 : obtenir l’exemple d’application
Cette étape consiste à définir le projet ASP.NET local.

### <a name="clone-the-application-repository"></a>Cloner le référentiel de l’application

Ouvrez le terminal de ligne de commande de votre choix et tapez la commande `CD` pointant vers un répertoire de travail. Exécutez ensuite les commandes suivantes pour cloner l’exemple d’application. 

```powershell
git clone https://github.com/cephalin/HighScaleApp.git
```

### <a name="run-the-sample-application-in-visual-studio"></a>Exécuter l’exemple d’application dans Visual Studio

Ouvrez la solution dans Visual Studio.

```powershell
cd HighScaleApp
.\HighScaleApp.sln
```

Saisissez `F5` pour exécuter l’application.

Cet exemple d’application web ASP.NET est fourni à partir du modèle par défaut, persiste à travers différentes sessions d’utilisateurs et utilise le cache de sortie. Examinons `HighScaleApp\Controllers\HomeController.cs`. La méthode `Index()` ajoute un élément de données à la session.

```csharp
Session.Add("visited", "true"); 
```

Quant aux méthodes `About()` et `Contact()`, elles mettent leur sortie en cache.

```csharp
[OutputCache(Duration = 60)]
```

## <a name="step-2---deploy-to-azure"></a>Étape 2 : déployer dans Azure
Lors de cette étape, vous créez une application web Azure et y déployez votre exemple d’application ASP.NET.

### <a name="create-a-resource-group"></a>Créer un groupe de ressources   
Utilisez [az groupe create](https://docs.microsoft.com/cli/azure/group#create) pour créer un [groupe de ressources](../azure-resource-manager/resource-group-overview.md) dans la région Europe de l’Ouest. Un groupe de ressources est un emplacement où vous placez toutes les ressources Azure que vous souhaitez gérer ensemble, telles que l’application web et tout serveur principal SQL Database.

```azurecli
az group create --location "West Europe" --name myResourceGroup
```

Pour connaître les valeurs que vous pouvez utiliser pour `---location`, utilisez la commande [az appservice list-locations](https://docs.microsoft.com/en-us/cli/azure/appservice#list-locations).

### <a name="create-an-app-service-plan"></a>Créer un plan App Service
Utilisez [az appservice plan create](https://docs.microsoft.com/en-us/cli/azure/appservice/plan#create) pour créer un [plan App Service](../app-service/azure-web-sites-web-hosting-plans-in-depth-overview.md) « B1 ». 

```azurecli
az appservice plan create --name myAppServicePlan --resource-group myResourceGroup --sku B1
```

Un plan App Service est une unité d’échelle pouvant inclure autant d’applications que vous souhaitez monter en puissance ou en charge sur la même infrastructure App Service. Chaque plan est également affecté à un [niveau tarifaire](https://azure.microsoft.com/en-us/pricing/details/app-service/). Les niveaux supérieurs incluent un matériel plus performant et des fonctionnalités supplémentaires telles que des instances de montée en puissance parallèle supplémentaires.

Pour ce didacticiel, B1 est le niveau minimal qui permet une augmentation de la taille à trois instances. Vous pouvez toujours déplacer ultérieurement votre application vers le haut ou vers le bas de l’échelle de tarification en exécutant [az appservice plan update](https://docs.microsoft.com/cli/azure/appservice/plan#update). 

### <a name="create-a-web-app"></a>Créer une application web
Utilisez [az appservice web create](https://docs.microsoft.com/en-us/cli/azure/appservice/web#create) pour créer une application web avec un nom unique dans `$appName`.

```azurecli
$appName = "<replace-with-a-unique-name>"
az appservice web create --name $appName --resource-group myResourceGroup --plan myAppServicePlan
```

### <a name="set-deployment-credentials"></a>Définir les informations d’identification de déploiement
Utilisez [az appservice web deployment user set](https://docs.microsoft.com/en-us/cli/azure/appservice/web/deployment/user#set) pour définir vos informations d’identification pour le déploiement au niveau des comptes pour App Service.

```azurecli
az appservice web deployment user set --user-name <letters-numbers> --password <mininum-8-char-captital-lowercase-letters-numbers>
```

### <a name="configure-git-deployment"></a>Configuration du déploiement Git
Utilisez [az appservice web source-control config-local-git](https://docs.microsoft.com/en-us/cli/azure/appservice/web/source-control#config-local-git) pour configurer le déploiement de Git local.

```azurecli
az appservice web source-control config-local-git --name $appName --resource-group myResourceGroup
```

Cette commande vous fournit une sortie similaire à ce qui suit :

```json
{
  "url": "https://user123@myuniqueappname.scm.azurewebsites.net/myuniqueappname.git"
}
```

Utilisez l’URL retournée pour configurer votre Git distant. La commande suivante utilise l’exemple de sortie précédent.

```powershell
git remote add azure https://user123@myuniqueappname.scm.azurewebsites.net/myuniqueappname.git
```

### <a name="deploy-the-sample-application"></a>Déployer l’exemple d’application
Vous êtes maintenant prêt à déployer votre exemple d’application. Exécutez `git push`.

```powershell
git push azure master
```

À l’invite de mot de passe, utilisez le mot de passe que vous avez spécifié lors de l’exécution de `az appservice web deployment user set`.

### <a name="browse-to-azure-web-app"></a>Accéder à l’application web Azure
Pour exécuter votre application en temps réel dans Azure, exécutez la commande [az appservice web browse](https://docs.microsoft.com/en-us/cli/azure/appservice/web#browse).

```azurecli
az appservice web browse --name $appName --resource-group myResourceGroup
```

## <a name="step-3---connect-to-redis"></a>Étape 3 : connexion à Redis
Dans cette étape, vous configurez le Cache Redis Azure comme cache externe colocalisé dans votre application web Azure. Vous pouvez rapidement utiliser Redis pour mettre en cache la sortie de page. En outre, lorsque vous montez vos applications web en charge par la suite , Redis vous permet de conserver les sessions utilisateur sur plusieurs instances en toute sécurité.

### <a name="create-an-azure-redis-cache"></a>Création d'un cache Redis Azure
Utilisez [az redis create](https://docs.microsoft.com/en-us/cli/azure/redis#create) pour créer un Cache Redis Azure et enregistrer la sortie JSON. Utilisez un nom unique dans `$cacheName`.

```powershell
$cacheName = "<replace-with-a-unique-cache-name>"
$redis = (az redis create --name $cacheName --resource-group myResourceGroup --location "West Europe" --sku-capacity 0 --sku-family C --sku-name Basic | ConvertFrom-Json)
```

### <a name="configure-the-application-to-use-redis"></a>Configurer l’application pour utiliser Redis
Formatez la chaîne de connexion pour votre cache.

```powershell
$connstring = "$($redis.hostname):$($redis.sslPort),password=$($redis.accessKeys.primaryKey),ssl=True,abortConnect=False"
$connstring 
```

La deuxième ligne devrait vous donner une sortie qui ressemble à ceci :

```
mycachename.redis.cache.windows.net:6380,password=/rQP/TLz1mrEPpmh9b/gnfns/t9vBRXqXn3i1RwBjGA=,ssl=True,abortConnect=False
```

Dans Visual Studio, créez un fichier de configuration web à la racine de votre projet du nom de `redis.config` et collez-y le code suivant. Dans `value`, utilisez la chaîne de connexion à partir de la sortie de PowerShell.

```xml
<appSettings>
  <add key="RedisConnection" value="your-azure-redis-cache-connection-string"/>
</appSettings>
```

Si vous examinez le fichier `.gitignore` dans votre référentiel Git, vous verrez que ce fichier est exclu du contrôle de code source. Cela permet de conserver vos informations sensibles en sécurité. 

Ouvrez `Web.config`. Vous remarquerez l’élément `<appSettings file="redis.config">`, qui obtient le paramètre que vous avez créé dans `redis.config`. 

Recherchez la section commentée incluant `<sessionState>` et `<caching>`. Supprimez les commentaires de cette section.

![](./media/app-service-web-tutorial-hyper-scale-app/redisproviders.png)

Ce code recherche la chaîne de connexion Redis définie dans `RedisConnection`. 

À présent, votre application utilise Redis pour gérer les sessions et la mise en cache. Saisissez `F5` pour exécuter l’application. Si vous le souhaitez, vous pouvez télécharger un client de gestion Redis pour visualiser les données qui sont désormais enregistrées dans le cache.

### <a name="configure-the-connection-string-in-azure"></a>Configurer la chaîne de connexion dans Azure

Pour que votre application fonctionne dans Azure, vous devez configurer la même chaîne de connexion Redis dans votre application web Azure. Dans la mesure où l’élément `redis.config` n’est pas conservé dans le contrôle de code source, il n’est pas déployé sur Azure lorsque vous exécutez un déploiement Git.

Utilisez [az appservice web config appsettings update](https://docs.microsoft.com/cli/azure/appservice/web/config/appsettings#update) pour ajouter la chaîne de connexion avec le même nom (`RedisConnection`).

az appservice web config appsettings update--paramètres « RedisConnection = $connstring »--nom $appName--groupe de ressources myResourceGroup

N’oubliez pas que `$connstring` contient la chaîne de connexion formatée.

### <a name="redeploy-the-application-to-azure"></a>Redéployer l’application dans Azure
Utilisez les commandes Git pour envoyer vos modifications vers Azure

```bash
git add .
git commit -m "now use Redis providers"
git push azure master
```

À l’invite de mot de passe, utilisez le mot de passe que vous avez spécifié lors de l’exécution de `az appservice web deployment user set`.

### <a name="browse-to-the-azure-web-app"></a>Accéder à l’application web Azure
Utilisez [az appservice web browse](https://docs.microsoft.com/en-us/cli/azure/appservice/web#browse) pour voir les modifications appliquées dans Azure.

```azurecli
az appservice web browse --name $appName --resource-group myResourceGroup
```

## <a name="step-4---scale-to-multiple-instances"></a>Étape 4 - mise à l’échelle à plusieurs instances
Le plan App Service est l’unité d’échelle de vos applications web Azure. Pour faire évoluer votre application web, vous faites évoluer le plan App Service.

Utilisez [az appservice plan update](https://docs.microsoft.com/cli/azure/appservice/plan#update) pour faire évoluer le plan App Service en trois instances, qui est le nombre maximal autorisé par le niveau de tarification B1. N’oubliez pas que B1 est le niveau de tarification que vous avez choisi lorsque vous avez créé le plan App Service. 

```azurecli
az appservice plan update --name myAppServicePlan --resource-group myResourceGroup --number-of-workers 3 
```

## <a name="step-5---scale-geographically"></a>Étape 5 : mise à échelle géographique
Lors de la mise à l’échelle géographique, vous exécutez votre application dans plusieurs régions du cloud Azure. Cette configuration équilibre encore davantage la charge de votre application en fonction de la géographie et réduit le temps de réponse en rapprochant votre application des navigateurs clients.

Dans cette étape, vous faites évoluer votre application web ASP.NET dans une seconde région avec [Azure Traffic Manager](https://docs.microsoft.com/en-us/azure/traffic-manager/). Une fois cette étape terminée, l’une de vos applications web sera exécutée en Europe de l’Ouest (déjà créée) et une autre en Asie du Sud-Est (pas encore créée). Les deux applications seront traitées par la même URL de Traffic Manager.

### <a name="scale-up-the-europe-app-to-standard-tier"></a>Mettre à l’échelle l’application en Europe au niveau Standard
Dans App Service, l’intégration avec Azure Traffic Manager requiert le niveau de tarification Standard. Utilisez [az appservice plan update](https://docs.microsoft.com/cli/azure/appservice/plan#update) pour faire évoluer votre plan App Service vers S1. 

```azurecli
az appservice plan update --name myAppServicePlan --resource-group myResourceGroup --sku S1
```
### <a name="create-a-traffic-manager-profile"></a>Créer un profil Traffic Manager 
Utilisez [az network traffic-manager profile create](https://docs.microsoft.com/cli/azure/network/traffic-manager/profile#create) pour créer un profil Traffic Manager et l’ajouter à votre groupe de ressources. Utilisez un nom DNS unique dans $dnsName.

```azurecli
$dnsName = "<replace-with-unique-dns-name>"
az network traffic-manager profile create --name myTrafficManagerProfile --resource-group myResourceGroup --routing-method Performance --unique-dns-name $dnsName
```

> [!NOTE]
> `--routing-method Performance` spécifie que ce profil [dirige le trafic utilisateur vers le point de terminaison le plus proche](../traffic-manager/traffic-manager-routing-methods.md).

### <a name="get-the-resource-id-of-the-europe-app"></a>Obtenir l’ID de ressource de l’application en Europe
Utilisez [az appservice web show](https://docs.microsoft.com/en-us/cli/azure/appservice/web#show) pour obtenir l’ID de ressource de votre application web.

```azurecli
$appId = az appservice web show --name $appName --resource-group myResourceGroup --query id --output tsv
```

### <a name="add-a-traffic-manager-endpoint-for-the-europe-app"></a>Ajouter un point de terminaison Traffic Manager pour l’application en Europe
Utilisez [az network traffic-manager endpoint create](https://docs.microsoft.com/cli/azure/network/traffic-manager/endpoint#create) pour ajouter un point de terminaison à votre profil Traffic Manager et utiliser l’ID de ressource de votre application web comme cible.

```azurecli
az network traffic-manager endpoint create --name myWestEuropeEndpoint --profile-name myTrafficManagerProfile --resource-group myResourceGroup --type azureEndpoints --target-resource-id $appId
```

### <a name="get-the-traffic-manager-endpoint-url"></a>Obtenir l’URL du point de terminaison Traffic Manager
Votre profil Traffic Manager dispose désormais d’un point de terminaison qui pointe vers votre application web existante. Utilisez [az network traffic-manager profile show](https://docs.microsoft.com/cli/azure/network/traffic-manager/profile#show) pour en obtenir l’URL. 

```azurecli
az network traffic-manager profile show --name myTrafficManagerProfile --resource-group myResourceGroup --query dnsConfig.fqdn --output tsv
```

Copiez la sortie dans votre navigateur. Vous devriez voir votre application web à nouveau.

### <a name="create-an-azure-redis-cache-in-asia"></a>Créer un cache Redis Azure en Asie
À présent, vous répliquez votre application web Azure pour la région Asie du Sud-Est. Pour commencer, utilisez [az redis create](https://docs.microsoft.com/en-us/cli/azure/redis#create) pour créer un deuxième Cache Redis Azure en Asie du Sud-Est. Ce cache doit être colocalisé avec votre application en Asie.

```powershell
$redis = (az redis create --name $cacheName-asia --resource-group myResourceGroup --location "Southeast Asia" --sku-capacity 0 --sku-family C --sku-name Basic | ConvertFrom-Json)
```

`--name $cacheName-asia` donne au cache le nom du cache d’Europe de l’ouest, avec le suffixe `-asia`.

### <a name="create-an-app-service-plan-in-asia"></a>Créer un plan App Service en Asie
Utilisez [az appservice plan create](https://docs.microsoft.com/cli/azure/appservice/plan#create) pour créer un second plan App Service dans la région Asie du Sud-Est à l’aide du même niveau S1 que le plan d’Europe de l’Ouest.

```azurecli
az appservice plan create --name myAppServicePlanAsia --resource-group myResourceGroup --location "Southeast Asia" --sku S1
```

### <a name="create-a-web-app-in-asia"></a>Créer une application web en Asie
Utilisez [az appservice web create](https://docs.microsoft.com/en-us/cli/azure/appservice/web#create) pour créer une seconde application web.

```azurecli
az appservice web create --name $appName-asia --resource-group myResourceGroup --plan myAppServicePlanAsia
```

`--name $appName-asia` donne à l’application le nom de l’application d’Europe de l’Ouest, avec le suffixe `-asia`.

### <a name="configure-the-connection-string-for-redis"></a>Configurer la chaîne de connexion pour Redis
Utilisez [az appservice web config appsettings update](https://docs.microsoft.com/cli/azure/appservice/web/config/appsettings#update) pour ajouter la chaîne de connexion à l’application web pour le cache d’Asie du Sud-Est.

az appservice web config appsettings update --paramètres « RedisConnection = $($redis.hostname) : $($redis.sslPort), mot de passe = $($redis.accessKeys.primaryKey), ssl = vrai, abortConnect = faux » --nom $appName-asia --groupe de ressources myResourceGroup

### <a name="configure-git-deployment-for-the-asia-app"></a>Configurez le déploiement Git pour l’application en Asie.
Utilisez [az appservice web source-control config-local-git](https://docs.microsoft.com/en-us/cli/azure/appservice/web/source-control#config-local-git) pour configurer le déploiement de Git local pour la seconde application web.

```azurecli
az appservice web source-control config-local-git --name $appName-asia --resource-group myResourceGroup
```

Cette commande vous fournit une sortie similaire à ce qui suit :

```json
{
  "url": "https://user123@myuniqueappname-asia.scm.azurewebsites.net/myuniqueappname.git"
}
```

Utilisez l’URL retournée pour configurer un second Git distant pour votre référentiel local. La commande suivante utilise l’exemple de sortie précédent.

```bash
git remote add azure-asia https://user123@myuniqueappname-asia.scm.azurewebsites.net/myuniqueappname.git
```

### <a name="deploy-your-sample-application"></a>Déployer votre exemple d’application
Exécutez `git push` pour déployer votre exemple d’application pour le deuxième Git distant. 

```bash
git push azure-asia master
```

À l’invite de mot de passe, utilisez le mot de passe que vous avez spécifié lors de l’exécution de `az appservice web deployment user set`.

### <a name="browse-to-the-asia-app"></a>Accéder à l’application en Asie
Pour vérifier que votre application est exécutée en temps réel dans Azure, utilisez [az appservice web browse](https://docs.microsoft.com/en-us/cli/azure/appservice/web#browse).

```azurecli
az appservice web browse --name $appName-asia --resource-group myResourceGroup
```

### <a name="get-the-resource-id-of-the-asia-app"></a>Obtenir l’ID de ressource de l’application en Asie
Utilisez [az appservice web show](https://docs.microsoft.com/en-us/cli/azure/appservice/web#show) pour obtenir l’ID de ressource de votre application web en Asie du Sud-Est.

```azurecli
$appIdAsia = az appservice web show --name $appName-asia --resource-group myResourceGroup --query id --output tsv
```

### <a name="add-a-traffic-manager-endpoint-for-the-asia-app"></a>Ajouter un point de terminaison Traffic Manager pour l’application en Asie
Utilisez [az network traffic-manager endpoint create](https://docs.microsoft.com/cli/azure/network/traffic-manager/endpoint#create) pour ajouter un deuxième point de terminaison au profil Traffic Manager.

```azurecli
az network traffic-manager endpoint create --name myAsiaEndpoint --profile-name myTrafficManagerProfile --resource-group myResourceGroup --type azureEndpoints --target-resource-id $appIdAsia
```

### <a name="add-region-identifier-to-web-apps"></a>Ajouter un identificateur de région aux applications web
Utilisez [az appservice web config appsettings update](https://docs.microsoft.com/cli/azure/appservice/web/config/appsettings#update) pour ajouter une variable d’environnement propre à la région.

```azurecli
az appservice web config appsettings update --settings "Region=West Europe" --name $appName --resource-group myResourceGroup
az appservice web config appsettings update --settings "Region=Southeast Asia" --name $appName-asia --resource-group myResourceGroup
```

Votre code d’application utilise déjà ce paramètre d’application. Examinons `HighScaleApp\Views\Home\Index.cshtml`.

### <a name="complete"></a>Terminé !

Essayez à présent d’accéder à l’URL de votre profil Traffic Manager à partir de navigateurs dans différentes régions géographiques. Les navigateurs clients d’Europe doivent afficher « ASP.NET Europe de l’Ouest », et les navigateurs clients d’Asie doit afficher « ASP.NET Asie du Sud-Est. »

## <a name="more-resources"></a>Autres ressources

