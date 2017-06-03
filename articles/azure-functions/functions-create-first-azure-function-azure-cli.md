---
title: "Créer votre première fonction à l’aide d’Azure CLI | Microsoft Docs"
description: "Apprenez à créer votre première fonction Azure pour une exécution sans serveur à l’aide d’Azure CLI."
services: functions
keywords: 
author: ggailey777
ms.author: glenga
ms.assetid: 674a01a7-fd34-4775-8b69-893182742ae0
ms.date: 05/02/2017
ms.topic: hero-article
ms.service: functions
ms.custom: mvc
ms.devlang: azure-cli
manager: erikre
ms.translationtype: Human Translation
ms.sourcegitcommit: 8f987d079b8658d591994ce678f4a09239270181
ms.openlocfilehash: 3dc0e1b26c95ac6583dd3b1068b36deb54f7ac5a
ms.contentlocale: fr-fr
ms.lasthandoff: 05/18/2017

---

# <a name="create-your-first-function-using-the-azure-cli"></a>Créer votre première fonction à l’aide d’Azure CLI

Ce didacticiel de démarrage rapide vous guide dans l’utilisation des fonctions Azure en vue de créer votre première fonction. Utilisez Azure CLI afin de créer une Function App, qui correspond à l’infrastructure sans serveur qui héberge votre fonction. Le code de fonction est lui-même déployé à partir d’un référentiel d’exemples GitHub.    

Vous pouvez suivre les étapes ci-dessous en utilisant un ordinateur Mac, Windows ou Linux. 

## <a name="prerequisites"></a>Composants requis 

Avant d’exécuter cet exemple, vous devez disposer des éléments suivants :

+ Un compte [GitHub](https://github.com) actif. 
+ [Azure CLI](https://docs.microsoft.com/cli/azure/install-azure-cli). 
+ Un abonnement Azure actif.

[!INCLUDE [quickstarts-free-trial-note](../../includes/quickstarts-free-trial-note.md)]

[!INCLUDE [cloud-shell-try-it.md](../../includes/cloud-shell-try-it.md)]

## <a name="log-in-to-azure"></a>Connexion à Azure

Connectez-vous à votre abonnement Azure avec la commande [az login](/cli/azure/#login) et suivez les instructions à l’écran. 

```azurecli-interactive
az login
```

## <a name="create-a-resource-group"></a>Créer un groupe de ressources

Créez un groupe de ressources avec la commande [az group create](/cli/azure/group#create). Un groupe de ressources Azure est un conteneur logique dans lequel les ressources Azure comme les Function Apps, les bases de données et les comptes de stockage sont déployées et gérées.

L’exemple suivant permet de créer un groupe de ressources nommé `myResourceGroup` :

```azurecli-interactive
az group create --name myResourceGroup --location westeurope
```
## <a name="create-an-azure-storage-account"></a>Création d'un compte Azure Storage

Functions utilise un compte de stockage Azure pour conserver l’état et d’autres informations sur vos fonctions. Créez un compte de stockage dans le groupe de ressources que vous avez créé à l’aide de la commande [az storage account create](/cli/azure/storage/account#create).

Dans la commande suivante, indiquez le nom unique de votre compte de stockage là où se trouve l’espace réservé `<storage_name>`. Les noms des comptes de stockage doivent comporter entre 3 et 24 caractères, uniquement des lettres minuscules et des chiffres.

```azurecli-interactive
az storage account create --name <storage_name> --location westeurope --resource-group myResourceGroup --sku Standard_LRS
```

Une fois le compte de stockage créé, Azure CLI affiche des informations similaires à l’exemple suivant :

```json
{
  "creationTime": "2017-04-15T17:14:39.320307+00:00",
  "id": "/subscriptions/bbbef702-e769-477b-9f16-bc4d3aa97387/resourceGroups/myresourcegroup/...",
  "kind": "Storage",
  "location": "westeurope",
  "name": "myfunctionappstorage",
  "primaryEndpoints": {
    "blob": "https://myfunctionappstorage.blob.core.windows.net/",
    "file": "https://myfunctionappstorage.file.core.windows.net/",
    "queue": "https://myfunctionappstorage.queue.core.windows.net/",
    "table": "https://myfunctionappstorage.table.core.windows.net/"
  },
     ....
    // Remaining output has been truncated for readability.
}
```

## <a name="create-a-function-app"></a>Créer une Function App

Vous devez disposer d’une Function App pour héberger l’exécution de vos fonctions. La Function App fournit un environnement d’exécution sans serveur de votre code de fonction. Elle vous permet de regrouper les fonctions en une unité logique pour faciliter la gestion, le déploiement et le partage des ressources. Créez une Function App à l’aide de la commande [az functionapp create](/cli/azure/functionapp#create). 

Dans la commande suivante, indiquez le nom unique de votre Function App là où se trouve l’espace réservé `<app_name>` et le nom du compte de stockage pour `<storage_name>`. La valeur `<app_name>` est utilisée en tant que domaine DNS par défaut pour la Function App. Pour cette raison, ce nom doit être unique sur l’ensemble des applications dans Azure. 

```azurecli-interactive
az functionapp create --name <app_name> --storage-account  <storage_name>  --resource-group myResourceGroup --consumption-plan-location westeurope
```
Par défaut, une Function App est créée avec le plan d’hébergement de consommation, ce qui signifie que les ressources sont ajoutées dynamiquement en fonction des besoins de vos fonctions, et que vous payez uniquement lorsque les fonctions sont exécutées. Pour plus d’informations, consultez [Choisir le plan d’hébergement approprié](functions-scale.md). 

Une fois la Function App créée, Azure CLI affiche des informations semblables à celles de l’exemple suivant :

```json
{
  "availabilityState": "Normal",
  "clientAffinityEnabled": true,
  "clientCertEnabled": false,
  "containerSize": 1536,
  "dailyMemoryTimeQuota": 0,
  "defaultHostName": "quickstart.azurewebsites.net",
  "enabled": true,
  "enabledHostNames": [
    "quickstart.azurewebsites.net",
    "quickstart.scm.azurewebsites.net"
  ],
   ....
    // Remaining output has been truncated for readability.
}
```

Maintenant que vous avez une Function App, vous pouvez déployer le code réel de la fonction dans le référentiel d’exemples GitHub.

## <a name="deploy-your-function-code"></a>Déployer votre code de fonction  

Il existe plusieurs façons de créer votre code de fonction dans votre nouvelle Function App. Cette rubrique utilise un référentiel d’exemples dans GitHub. Comme auparavant, dans le code suivant, remplacez l’espace réservé `<app_name>` par le nom de la Function App que vous avez créée. 

```azurecli-interactive
az functionapp deployment source config --name <app_name> --resource-group myResourceGroup --repo-url https://github.com/Azure-Samples/functions-quickstart --branch master --manual-integration
```
Une fois la source du déploiement définie, Azure CLI affiche des informations semblables à l’exemple suivant (les valeurs null sont supprimées pour une meilleure lisibilité) :

```json
{
  "branch": "master",
  "deploymentRollbackEnabled": false,
  "id": "/subscriptions/bbbef702-e769-477b-9f16-bc4d3aa97387/resourceGroups/myResourceGroup/...",
  "isManualIntegration": true,
  "isMercurial": false,
  "location": "West Europe",
  "name": "quickstart",
  "repoUrl": "https://github.com/Azure-Samples/functions-quickstart",
  "resourceGroup": "myResourceGroup",
  "type": "Microsoft.Web/sites/sourcecontrols"
}
```

## <a name="test-the-function"></a>Tester la fonction

Utilisez cURL pour tester la fonction déployée sur un ordinateur Mac ou Linux ou un interpréteur de commandes (Bash) sur Windows. Exécutez la commande cURL suivante, en remplaçant l’espace réservé `<app_name>` par le nom de votre Function App. Ajoutez la chaîne de requête `&name=<yourname>` à l’URL.

```bash
curl http://<app_name>.azurewebsites.net/api/HttpTriggerJS1?name=<yourname>
```  

![Réponse de la fonction affichée dans un navigateur.](./media/functions-create-first-azure-function-azure-cli/functions-azure-cli-function-test-curl.png)  

Si vous n’avez pas de cURL disponible dans votre ligne de commande, entrez simplement la même URL dans la barre d’adresse de votre navigateur web. Remplacez à nouveau l’espace réservé `<app_name>` par le nom de votre Function App, et ajoutez la chaîne de requête `&name=<yourname>` à l’URL avant d’exécuter la requête. 

    http://<app_name>.azurewebsites.net/api/HttpTriggerJS1?name=<yourname>
   
![Réponse de la fonction affichée dans un navigateur.](./media/functions-create-first-azure-function-azure-cli/functions-azure-cli-function-test-browser.png)  

## <a name="clean-up-resources"></a>Supprimer des ressources

Les autres démarrages rapides de cette collection reposent sur ce démarrage rapide. Si vous souhaitez continuer à utiliser d’autres démarrages rapides ou les didacticiels, ne nettoyez pas les ressources créées dans ce démarrage rapide. Sinon, utilisez la commande suivante pour supprimer toutes les ressources créées par ce démarrage rapide :

```azurecli-interactive
az group delete --name myResourceGroup
```
Quand vous y êtes invité, tapez `y`.

## <a name="next-steps"></a>Étapes suivantes

[!INCLUDE [Next steps note](../../includes/functions-quickstart-next-steps.md)]

