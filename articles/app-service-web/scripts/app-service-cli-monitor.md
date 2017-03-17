---
title: Exemple de script Azure CLI - Surveiller une application web avec les journaux de serveur web | Microsoft Docs
description: Exemple de script Azure CLI - Surveiller une application web avec les journaux de serveur web
services: appservice
documentationcenter: appservice
author: syntaxc4
manager: erikre
editor: 
tags: azure-service-management
ms.assetid: 0887656f-611c-4627-8247-b5cded7cef60
ms.service: app-service
ms.devlang: multiple
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: web
ms.date: 02/21/2017
ms.author: cfowler
translationtype: Human Translation
ms.sourcegitcommit: 24d86e17a063164c31c312685c0742ec4a5c2f1b
ms.openlocfilehash: 762fb06cad3a03aa6b0c19bd277e13ffd4c03a3b
ms.lasthandoff: 03/11/2017

---

# <a name="monitor-a-web-app-with-web-server-logs"></a>Surveiller une application web avec les journaux de serveur web

Dans ce scénario, vous créez un groupe de ressources, un plan App Service, une application web, et vous configurez l’application web afin d’activer les journaux de serveur web. Vous téléchargez ensuite les fichiers journaux pour révision.

Avant d’exécuter ce script, vérifiez qu’une connexion avec Azure a bien été créée à l’aide de la commande `az login`.

Cet exemple fonctionne dans une interface d’interpréteur de commandes Bash. Pour en savoir plus les options d’exécution de scripts Azure CLI dans le client Windows, consultez la page [Running the Azure CLI in Windows (Exécution d’Azure CLI dans Windows)](../../virtual-machines/virtual-machines-windows-cli-options.md).

## <a name="sample-script"></a>Exemple de script

[!code-azurecli[principal](../../../cli_scripts/app-service/monitor-with-logs/monitor-with-logs.sh "Surveiller les journaux")]

[!INCLUDE [cli-script-clean-up](../../../includes/cli-script-clean-up.md)]

## <a name="script-explanation"></a>Explication du script

Ce script utilise les commandes suivantes pour créer un groupe de ressources, une application web et toutes les ressources associées. Chaque commande du tableau renvoie à une documentation spécifique.

| Commande | Remarques |
|---|---|
| [az group create](https://docs.microsoft.com/cli/azure/group#create) | Crée un groupe de ressources dans lequel toutes les ressources sont stockées. |
| [az appservice plan create](https://docs.microsoft.com/cli/azure/appservice/plan#create) | Crée un plan App Service. Cela équivaut à une batterie de serveurs pour votre application web Azure. |
| [az appservice web create](https://docs.microsoft.com/cli/azure/appservice/web#create) | Crée une application web Azure dans le plan App Service. |
| [az appservice web log config](https://docs.microsoft.com/cli/azure/appservice/web/log#config) | Configure les journaux dans lesquels une application web Azure est conservée. |
| [az appservice web log download](https://docs.microsoft.com/cli/azure/appservice/web/log#download) | Télécharge les journaux d’une application web Azure sur votre ordinateur local. |

## <a name="next-steps"></a>Étapes suivantes

Pour plus d’informations sur l’interface Azure CLI, consultez la [documentation relative à l’interface Azure CLI](https://docs.microsoft.com/cli/azure/overview).

Vous trouverez des exemples supplémentaires de scripts CLI App Service dans la [documentation relative à Azure App Service](../app-service-cli-samples.md).
