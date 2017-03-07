---
title: "Exemple de script Azure CLI - Déployer une application web dans un environnement intermédiaire | Microsoft Docs"
description: "Exemple de script Azure CLI - Déployer une application web dans un environnement intermédiaire"
services: app-service\web
documentationcenter: 
author: cephalin
manager: erikre
editor: 
tags: azure-service-management
ms.assetid: 
ms.service: app-service-web
ms.workload: web
ms.devlang: na
ms.topic: article
ms.date: 02/01/2017
ms.author: cephalin
translationtype: Human Translation
ms.sourcegitcommit: 343f7509e43c2305e6fa7d5e5cb9e6772e1a7e01
ms.openlocfilehash: 6e24be1f9525b976864d63dc433e853f399a545d
ms.lasthandoff: 02/27/2017

---

# <a name="deploy-a-web-app-to-a-staging-environment"></a>Déployer une application web dans un environnement intermédiaire

Cet exemple de script effectue les opérations suivantes à l’aide d’Azure CLI 2.0 : 

* Créer une application web dans Azure App Service dans la région Europe de l’Ouest Azure.
* Mettre le plan App Service au niveau STANDARD (minimum pour les emplacements de déploiement).
* Définir un emplacement de déploiement nommé « staging ».
* Déployer votre code d’application web à partir de GitHub dans l’emplacement intermédiaire.
* Ouvrir l’emplacement intermédiaire déployé dans le navigateur à des fins de vérification.
* Échanger l’emplacement intermédiaire en production.

## <a name="prerequisites"></a>Composants requis

* Exécutez `az login` pour vous connecter à Azure.
* Placez votre code d’application web dans un référentiel GitHub dont vous êtes propriétaire.

> [!NOTE]
> Si vous utilisez un référentiel GitHub public dont vous n’êtes pas propriétaire, App Service déploie le code à partir de ce référentiel GitHub, mais ne peut pas configurer la clé SSH ni les webhooks requis pour le déploiement continu.
>
>

## <a name="create-vm-sample"></a>Exemple de création de machine virtuelle

[!code-azurecli[principal](../../cli_scripts/app-service/deploy-deployment-slot/deploy-deployment-slot.sh "Déployer une application web dans un environnement intermédiaire")]

## <a name="clean-up-deployment"></a>Nettoyer le déploiement 

Une fois l’exemple de script exécuté, la commande suivante permet de supprimer le groupe de ressources, l’application web et toutes les ressources associées.

```azurecli
az group delete --name $webappname
```

## <a name="script-explanation"></a>Explication du script

Ce script utilise les commandes suivantes. Chaque commande du tableau renvoie à une documentation spécifique.

| Commande | Remarques |
|---|---|
| [az group create](https://docs.microsoft.com/en-us/cli/azure/group#create) | Crée un groupe de ressources dans lequel toutes les ressources sont stockées. |
| [az appservice plan create](https://docs.microsoft.com/en-us/cli/azure/appservice/plan#create) | Crée un plan App Service. |
| [az appservice web create](https://docs.microsoft.com/en-us/cli/azure/appservice/web#delete) | Crée une application web. |
| [az appservice plan update](https://docs.microsoft.com/en-us/cli/azure/appservice/plan#update) | Met à jour un plan App Service en fonction du niveau tarifaire. |
| [az appservice web deployment slot create](https://docs.microsoft.com/en-us/cli/azure/appservice/web/deployment/slot#create) | Crée un emplacement de déploiement. |
| [az appservice web source-control config](https://docs.microsoft.com/en-us/cli/azure/appservice/web/source-control#config) | Associe une application web à un référentiel Git ou Mercurial. |
| [az appservice web browse](https://docs.microsoft.com/en-us/cli/azure/appservice/web#browse) | Ouvre une application web dans un navigateur. |
| [az appservice web deployment slot swap](https://docs.microsoft.com/en-us/cli/azure/appservice/web/deployment/slot#swap) | Bascule un déploiement spécifié en production. |

## <a name="next-steps"></a>Étapes suivantes

Pour plus d’informations sur l’interface Azure CLI, consultez la [documentation relative à l’interface Azure CLI](https://docs.microsoft.com/en-us/cli/azure/overview).

Vous trouverez des exemples supplémentaires de scripts CLI pour les applications web Azure App Service dans les [exemples d’interface CLI Azure]().

