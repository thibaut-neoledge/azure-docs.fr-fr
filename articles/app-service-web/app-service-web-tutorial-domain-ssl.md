---
title: "Ajouter un domaine personnalisé et un protocole SSL à une application web Azure | Microsoft Docs"
description: "Découvrez comment préparer votre application web Azure à passer en mode production en ajoutant votre marque de société. Mappez le nom de domaine personnalisé (domaine personnel) à votre application web et protégez-le avec un certificat SSL personnalisé."
services: app-service\web
documentationcenter: nodejs
author: cephalin
manager: erikre
editor: 
ms.assetid: dc446e0e-0958-48ea-8d99-441d2b947a7c
ms.service: app-service-web
ms.workload: web
ms.tgt_pltfrm: na
ms.devlang: nodejs
ms.topic: article
ms.date: 03/29/2017
ms.author: cephalin
ms.translationtype: Human Translation
ms.sourcegitcommit: a30a90682948b657fb31dd14101172282988cbf0
ms.openlocfilehash: 535a7dbe24a1badc8539b61d34c09bdeda41ad40
ms.contentlocale: fr-fr
ms.lasthandoff: 05/25/2017


---
# <a name="add-custom-domain-and-ssl-to-an-azure-web-app"></a>Ajouter un domaine personnalisé et un protocole SSL à une application web Azure

Ce didacticiel vous montre comment mapper rapidement un nom de domaine personnalisé à votre application web Azure, puis le sécuriser avec un certificat SSL personnalisé. 

## <a name="before-you-begin"></a>Avant de commencer

Avant d’exécuter cet exemple, installez [Azure CLI 2.0](https://docs.microsoft.com/en-us/cli/azure/install-azure-cli) localement.

Vous avez également besoin d’un accès administratif à la page de configuration DNS de votre fournisseur de domaine respectif. Par exemple, pour ajouter `www.contoso.com`, vous devez être en mesure de configurer les entrées DNS pour `contoso.com`.

Enfin, vous avez besoin d’un fichier .PFX valide _et_ de son mot de passe pour le certificat SSL que vous souhaitez charger et lier. Ce certificat SSL doit être configuré de manière à sécuriser le nom de domaine personnalisé de votre choix. Dans l’exemple ci-dessus, votre certificat SSL doit sécuriser `www.contoso.com`. 

## <a name="step-1---create-an-azure-web-app"></a>Étape 1 : Créer une application web Azure

### <a name="log-in-to-azure"></a>Connexion à Azure

Nous allons maintenant utiliser l’interface Azure CLI 2.0 dans une fenêtre de terminal pour créer les ressources nécessaires pour héberger notre application Node.js dans Azure.  Connectez-vous à votre abonnement Azure avec la commande [az login](/cli/azure/#login) et suivez les instructions à l’écran. 

```azurecli 
az login 
``` 
   
### <a name="create-a-resource-group"></a>Créer un groupe de ressources   
Créez un groupe de ressources avec la commande [az group create](/cli/azure/group#create). Un groupe de ressources Azure est un conteneur logique dans lequel les ressources Azure comme les applications web, les bases de données et les comptes de stockage sont déployées et gérées. 

```azurecli
az group create --name myResourceGroup --location westeurope 
```

Pour connaître les valeurs que vous pouvez utiliser pour `---location`, utilisez la commande d’interface de ligne de commande Azure `az appservice list-locations`.

## <a name="create-an-app-service-plan"></a>Créer un plan App Service

Créez un plan App Service avec la commande [az appservice plan create](/cli/azure/appservice/plan#create). 

[!INCLUDE [app-service-plan](../../includes/app-service-plan.md)]

L’exemple suivant crée un plan App Service nommé `myAppServicePlan` en appliquant le niveau tarifaire **De base**.

az appservice plan create --name myAppServicePlan --resource-group myResourceGroup --sku B1

Lorsque le plan App Service est créé, l’interface Azure CLI affiche des informations semblables à l’exemple suivant. 

```json 
{ 
    "id": "/subscriptions/00000000-0000-0000-0000-000000000000/resourceGroups/myResourceGroup/providers/Microsoft.Web/serverfarms/myAppServicePlan", 
    "kind": "app", 
    "location": "West Europe", 
    "sku": { 
    "capacity": 1, 
    "family": "B", 
    "name": "B1", 
    "tier": "Basic" 
    }, 
    "status": "Ready", 
    "type": "Microsoft.Web/serverfarms" 
} 
``` 

## <a name="create-a-web-app"></a>Créer une application web

Maintenant qu’un plan App Service est créé, générez une application web dans le plan App Service `myAppServicePlan`. L’application web vous offre un espace d’hébergement pour déployer votre code, et fournit une URL pour vous permettre d’afficher l’application déployée. Utilisez la commande [az appservice web create](/cli/azure/appservice/web#create) pour créer l’application web. 

Dans la commande ci-dessous, indiquez le nom unique de votre propre application là où se trouve l’espace réservé `<app_name>`. Ce nom unique sera utilisé dans le nom de domaine par défaut de l’application web. Pour cette raison, ce nom doit être unique sur l’ensemble des applications dans Azure. Vous pouvez ultérieurement mapper toute entrée DNS personnalisée vers l’application web avant de l’exposer à vos utilisateurs. 

```azurecli
az appservice web create --name <app_name> --resource-group myResourceGroup --plan myAppServicePlan 
```

Une fois l’application web créée, Azure CLI affiche des informations semblables à celles de l’exemple suivant. 

```json 
{ 
    "clientAffinityEnabled": true, 
    "defaultHostName": "<app_name>.azurewebsites.net", 
    "id": "/subscriptions/00000000-0000-0000-0000-000000000000/resourceGroups/myResourceGroup/providers/Microsoft.Web/sites/<app_name>", 
    "isDefaultContainer": null, 
    "kind": "app", 
    "location": "West Europe", 
    "name": "<app_name>", 
    "repositorySiteName": "<app_name>", 
    "reserved": true, 
    "resourceGroup": "myResourceGroup", 
    "serverFarmId": "/subscriptions/00000000-0000-0000-0000-000000000000/resourceGroups/myResourceGroup/providers/Microsoft.Web/serverfarms/myAppServicePlan", 
    "state": "Running", 
    "type": "Microsoft.Web/sites", 
} 
```

À partir du résultat JSON, `defaultHostName` affiche le nom de domaine par défaut de votre application web. Dans votre navigateur, accédez à cette adresse.

```
http://<app_name>.azurewebsites.net 
``` 
 
![app-service-web-service-created](media/app-service-web-tutorial-domain-ssl/web-app-created.png)  

## <a name="step-2---configure-dns-mapping"></a>Étape 2 : Configurer le mappage DNS

Lors de cette étape, vous ajoutez un mappage d’un domaine personnalisé au nom de domaine par défaut de votre application web, `<app_name>.azurewebsites.net`. En général, vous effectuez cette étape sur le site web du fournisseur de votre domaine. Comme le site web de chaque bureau d’enregistrement des domaines est légèrement différent, vous devez consulter la documentation de votre fournisseur. Voici néanmoins quelques recommandations générales. 

### <a name="navigate-to-to-dns-management-page"></a>Accédez à la page de gestion DNS

Tout d’abord, connectez-vous au site web de votre bureau d’enregistrement des domaines.  

Puis recherchez la page de gestion des enregistrements DNS. Recherchez la mention **Nom de domaine**, **DNS** ou **Gestion du nom de serveur**. Vous trouvez généralement un lien en affichant vos informations de compte, puis en recherchant un lien comme **Mes domaines**.

Une fois que vous avez trouvé la page, recherchez un lien vous permettant d’ajouter ou de modifier des enregistrements DNS. Il s’agit probablement d’un lien de **fichier de zone** ou d’**enregistrements DNS**, ou encore d’un lien de **configuration avancé**.

### <a name="create-a-cname-record"></a>Créer un enregistrement CNAME

Ajoutez un enregistrement CNAME qui mappe le nom de sous-domaine souhaité au nom de domaine par défaut de votre application web (`<app_name>.azurewebsites.net`, où `<app_name>` est le nom unique de votre application).

Pour l’exemple `www.contoso.com`, vous créez un enregistrement CNAME qui mappe le nom d’hôte `www` à `<app_name>.azurewebsites.net`.

## <a name="step-3---configure-the-custom-domain-on-your-web-app"></a>Étape 3 : Configurer le domaine personnalisé sur votre application web

Une fois que vous avez terminé la configuration du mappage de nom d’hôte dans le site web du fournisseur de votre domaine, vous êtes prêt à configurer le domaine personnalisé dans votre application web. Utilisez la commande [az appservice web config hostname add](/cli/azure/appservice/web/config/hostname#add) pour ajouter cette configuration. 

Dans la commande ci-dessous, remplacez `<app_name>` par votre nom d’application unique et <your_custom_domain> par le nom de domaine complet personnalisé (par exemple, `www.contoso.com`). 

```azurecli
az appservice web config hostname add --webapp <app_name> --resource-group myResourceGroup --name <your_custom_domain>
```

Le domaine personnalisé est maintenant entièrement mappé à votre application web. Dans votre navigateur, accédez au nom de domaine personnalisé. Par exemple :

```
http://www.contoso.com 
``` 

![app-service-web-service-created](media/app-service-web-tutorial-domain-ssl/web-app-custom-domain.png)  

## <a name="step-4---bind-a-custom-ssl-certificate-to-your-web-app"></a>Étape 4 : Lier un certificat SSL personnalisé à votre application web

Vous disposez désormais d’une application web Azure, avec le nom de domaine de votre choix dans la barre d’adresses du navigateur. Toutefois, si vous accédez à `https://<your_custom_domain>` maintenant, une erreur de certificat s’affiche. 

![app-service-web-service-created](media/app-service-web-tutorial-domain-ssl/web-app-cert-error.png)  

Cette erreur est due au fait que votre application web ne dispose pas encore d’une liaison de certificat SSL correspondant à votre nom de domaine personnalisé. Toutefois, aucune erreur ne s’affiche lorsque vous accédez à `https://<app_name>.azurewebsites.net`. En effet, votre application, comme l’ensemble des applications Azure App Service, est sécurisée par le biais du certificat SSL pour le domaine générique `*.azurewebsites.net` par défaut. 

Pour accéder à votre application web au moyen de votre nom de domaine personnalisé, vous devez lier le certificat SSL de votre domaine personnalisé à l’application web. Vous le ferez lors de cette étape. 

### <a name="upload-the-ssl-certificate"></a>Charger le certificat SSL

Téléchargez le certificat SSL de votre domaine personnalisé pour votre application web à l’aide de la commande [az appservice web config ssl upload](/cli/azure/appservice/web/config/ssl#upload).

Dans la commande ci-dessous, remplacez `<app_name>` par votre nom d’application unique, `<path_to_ptx_file>` par le chemin d’accès à votre fichier .PFX, et `<password>` par le mot de passe de votre certificat. 

```azurecli
az appservice web config ssl upload --name <app_name> --resource-group myResourceGroup --certificate-file <path_to_pfx_file> --certificate-password <password> 
```

Lorsque le certificat est chargé, Azure CLI affiche des informations semblables à ce qui suit :

```json
{
  "cerBlob": null,
  "expirationDate": "2018-03-29T14:12:57+00:00",
  "friendlyName": "",
  "hostNames": [
    "www.contoso.com"
  ],
  "hostingEnvironmentProfile": null,
  "id": "/subscriptions/00000000-0000-0000-0000-000000000000/resourceGroups/myResourceGroup/providers/Microsoft.Web/cert
ificates/9FD1D2D06E2293673E2A8D1CA484A092BD016D00__West Europe_myResourceGroup",
  "issueDate": "2017-03-29T14:12:57+00:00",
  "issuer": "www.contoso.com",
  "keyVaultId": null,
  "keyVaultSecretName": null,
  "keyVaultSecretStatus": "Initialized",
  "kind": null,
  "location": "West Europe",
  "name": "9FD1D2D06E2293673E2A8D1CA484A092BD016D00__West Europe_myResourceGroup",
  "password": null,
 "pfxBlob": null,
  "publicKeyHash": null,
  "resourceGroup": "myResourceGroup",
  "selfLink": null,
  "serverFarmId": null,
  "siteName": null,
  "subjectName": "www.contoso.com",
  "tags": null,
  "thumbprint": "9FD1D2D06E2293673E2A8D1CA484A092BD016D00",
  "type": "Microsoft.Web/certificates",
  "valid": null
}
```

À partir du résultat JSON, `thumbprint` affiche l’empreinte du certificat que vous avez chargé. Copiez sa valeur pour l’étape suivante.

### <a name="bind-the-uploaded-ssl-certificate-to-the-web-app"></a>Lier le certificat SSL chargé à l’application web

Votre application web dispose désormais du nom de domaine personnalisé de votre choix et d’un certificat SSL qui sécurise ce domaine personnalisé. Il ne vous reste plus qu’à lier le certificat que vous avez chargé à l’application web. Pour cela, utilisez la commande [az appservice web config ssl bind](/cli/azure/appservice/web/config/ssl#bind).

Dans la commande ci-dessous, remplacez `<app_name>` par votre nom d’application unique, et `<thumbprint-from-previous-output>` par l’empreinte du certificat obtenu à partir de la commande précédente. 

az appservice web config ssl bind --name <app_name> --resource-group myResourceGroup --certificate-thumbprint <thumbprint-from-previous-output> --ssl-type SNI

Une fois le certificat lié à votre application web, Azure CLI affiche des informations semblables à ce qui suit :

{ "availabilityState": "Normal", "clientAffinityEnabled": true, "clientCertEnabled": false, "cloningInfo": null, "containerSize": 0, "dailyMemoryTimeQuota": 0, "defaultHostName": "<app_name>.azurewebsites.net", "enabled": true, "enabledHostNames": [ "www.contoso.com", "<app_name>.azurewebsites.net", "<app_name>.scm.azurewebsites.net" ], "gatewaySiteName": null, "hostNameSslStates": [ { "hostType": "Standard", "name": "<app_name>.azurewebsites.net", "sslState": "Disabled", "thumbprint": null, "toUpdate": null, "virtualIp": null }, { "hostType": "Repository", "name": "<app_name>.scm.azurewebsites.net", "sslState": "Disabled", "thumbprint": null, "toUpdate": null, "virtualIp": null }, { "hostType": "Standard", "name": "www.contoso.com", "sslState": "SniEnabled", "thumbprint": "9FD1D2D06E2293673E2A8D1CA484A092BD016D00", "toUpdate": null, "virtualIp": null } ], "hostNames": [ "www.contoso.com", "<app_name>.azurewebsites.net" ], "hostNamesDisabled": false, "hostingEnvironmentProfile": null, "id": "/subscriptions/00000000-0000-0000-0000-000000000000/resourceGroups/myResourceGroup/providers/Microsoft.Web/site s/<app_name>", "isDefaultContainer": null, "kind": "WebApp", "lastModifiedTimeUtc": "2017-03-29T14:36:18.803333", "location": "West Europe", "maxNumberOfWorkers": null, "microService": "false", "name": "<app_name>", "outboundIpAddresses": "13.94.143.57,13.94.136.57,40.68.199.146,13.94.138.55,13.94.140.1", "premiumAppDeployed": null, "repositorySiteName": "<app_name>", "reserved": false, "resourceGroup": "myResourceGroup", "scmSiteAlsoStopped": false, "serverFarmId": "/subscriptions/00000000-0000-0000-0000-000000000000/resourceGroups/myResourceGroup/providers/Microsof t.Web/serverfarms/myAppServicePlan", "siteConfig": null, "slotSwapStatus": null, "state": "Running", "suspendedTill": null, "tags": null, "targetSwapSlot": null, "trafficManagerHostNames": null, "type": "Microsoft.Web/sites", "usageState": "Normal" }

Dans votre navigateur, accédez au point de terminaison HTTPS de votre nom de domaine personnalisé. Par exemple :

```
https://www.contoso.com 
``` 

![app-service-web-service-created](media/app-service-web-tutorial-domain-ssl/web-app-ssl-success.png)  

## <a name="more-resources"></a>Autres ressources

[Acheter et configurer un nom de domaine personnalisé dans Azure App Service](custom-dns-web-site-buydomains-web-app.md)
[acheter et configurer un certificat SSL pour votre service Azure App Service](web-sites-purchase-ssl-web-site.md)

