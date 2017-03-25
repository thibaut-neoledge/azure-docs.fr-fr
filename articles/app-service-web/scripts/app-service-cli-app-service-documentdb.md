---
title: "Exemple de script Azure CLI - Connecter une application web à une instance DocumentDB | Microsoft Docs"
description: "Exemple de script Azure CLI - Connecter une application web à une instance DocumentDB"
services: appservice
documentationcenter: appservice
author: syntaxc4
manager: erikre
editor: 
tags: azure-service-management
ms.assetid: bbbdbc42-efb5-4b4f-8ba6-c03c9d16a7ea
ms.service: app-service
ms.devlang: multiple
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: web
ms.date: 02/21/2017
ms.author: cfowler
translationtype: Human Translation
ms.sourcegitcommit: 24d86e17a063164c31c312685c0742ec4a5c2f1b
ms.openlocfilehash: 80b6d446fd9a0ac6434c8415b97ff0f07eb0da12
ms.lasthandoff: 03/11/2017

---

# <a name="connect-a-web-app-to-a-documentdb"></a>Connecter une application web à une instance DocumentDB

Dans ce scénario, vous allez apprendre à créer une instance AzureDocumentDB et une application web Azure. Ensuite, vous allez lier l’instance DocumentDB à l’application web par le biais des paramètres de l’application.

Avant d’exécuter ce script, vérifiez qu’une connexion avec Azure a bien été créée à l’aide de la commande `az login`.

Cet exemple fonctionne dans une interface d’interpréteur de commandes Bash. Pour en savoir plus les options d’exécution de scripts Azure CLI dans le client Windows, consultez la page [Running the Azure CLI in Windows (Exécution d’Azure CLI dans Windows)](../../virtual-machines/virtual-machines-windows-cli-options.md).

## <a name="sample-script"></a>Exemple de script

[!code-azurecli[main](../../../cli_scripts/app-service/connect-to-documentdb/connect-to-documentdb.sh "Azure DocumentDB")]

[!INCLUDE [cli-script-clean-up](../../../includes/cli-script-clean-up.md)]

## <a name="script-explanation"></a>Explication du script

Ce script utilise les commandes suivantes pour créer un groupe de ressources, une application web, une instance DocumentDB et toutes les ressources associées. Chaque commande du tableau renvoie à une documentation spécifique.

| Commande | Remarques |
|---|---|
| [az group create](https://docs.microsoft.com/cli/azure/group#create) | Crée un groupe de ressources dans lequel toutes les ressources sont stockées. |
| [az appservice plan create](https://docs.microsoft.com/cli/azure/appservice/plan#create) | Crée un plan App Service. Cela équivaut à une batterie de serveurs pour votre application web Azure. |
| [az appservice web create](https://docs.microsoft.com/cli/azure/appservice/web#create) | Crée une application web Azure dans le plan App Service. |
| [az documentdb create](https://docs.microsoft.com/en-us/cli/azure/documentdb#create) | Crée une instance DocumentDB. Il s’agit de l’emplacement où les données seront stockées. |
| [az documentdb list-keys](https://docs.microsoft.com/en-us/cli/azure/documentdb#list-keys) | Répertorie les touches d’accès rapide pour le compte de base de données Azure DocumentDB spécifié. |
| [az appservice web config appsetings update](https://docs.microsoft.com/cli/azure/appservice/web/config/appsettings#update) | Crée ou met à jour un paramètre d’application pour une application web Azure. Les paramètres d’application sont exposés en tant que variables d’environnement pour votre application. |

## <a name="next-steps"></a>Étapes suivantes

Pour plus d’informations sur l’interface Azure CLI, consultez la [documentation relative à l’interface Azure CLI](https://docs.microsoft.com/cli/azure/overview).

Vous trouverez des exemples supplémentaires de scripts CLI App Service dans la [documentation relative à Azure App Service](../app-service-cli-samples.md).
