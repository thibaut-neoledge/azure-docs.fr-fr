---
title: "Exemple de script Azure CLI - Déployer une application web à partir d’un référentiel Git local | Microsoft Docs"
description: "Exemple de script Azure CLI - Déployer une application web à partir d’un référentiel Git local"
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
ms.openlocfilehash: 7c970e6d4447fed96d91143a502ec823eb60df62
ms.lasthandoff: 02/27/2017

---

# <a name="deploy-a-web-app-from-a-local-git-repository"></a>Déployer une application web à partir d’un référentiel Git local

Cet exemple de script effectue les opérations suivantes à l’aide d’Azure CLI 2.0 : 

* Créer une application web dans Azure App Service dans la région Europe de l’Ouest Azure.
* Déployer un code d’application web à partir d’un référentiel Git local.
* Afficher l’application web déployée dans le navigateur.

## <a name="prerequisites"></a>Composants requis

* Exécutez `az login` pour vous connecter à Azure.
* Validez votre code d’application web dans un référentiel Git local.

## <a name="create-vm-sample"></a>Exemple de création de machine virtuelle

[!code-azurecli[principal](../../cli_scripts/app-service/deploy-local-git/deploy-local-git.sh "Déployer une application web à partir d’un référentiel Git local")]

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
| [az appservice web create](https://docs.microsoft.com/en-us/cli/azure/appservice/web#delete) | Crée une application web |
| [az appservice web source-control config-local-git](https://docs.microsoft.com/en-us/cli/azure/appservice/web/source-control#config-local-git) | Crée une configuration de contrôle source pour un référentiel Git local. |
| [az appservice web browse](https://docs.microsoft.com/en-us/cli/azure/appservice/web#browse) | Ouvre une application web dans un navigateur. |

## <a name="next-steps"></a>Étapes suivantes

Pour plus d’informations sur l’interface Azure CLI, consultez la [documentation relative à l’interface Azure CLI](https://docs.microsoft.com/en-us/cli/azure/overview).

Vous trouverez des exemples supplémentaires de scripts CLI pour les applications web Azure App Service dans les [exemples d’interface CLI Azure]().
