---
title: "Créer une fonction Azure Functions qui se connecte à une base de données Azure DocumentDB | Microsoft Docs"
description: "Exemple de script Azure CLI - Créer une fonction Azure Functions qui se connecte à une base de données Azure DocumentDB"
services: functions
documentationcenter: functions
author: rachelappel
manager: erikre
editor: 
tags: functions
ms.assetid: 
ms.service: functions
ms.devlang: azurecli
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: 
ms.date: 04/20/2017
ms.author: rachelap
ms.translationtype: Human Translation
ms.sourcegitcommit: 71fea4a41b2e3a60f2f610609a14372e678b7ec4
ms.openlocfilehash: a34793b6b16cce4031e9e871d8e3b143180593ae
ms.contentlocale: fr-fr
ms.lasthandoff: 05/10/2017

---

# <a name="integrate-function-app-into-azure-storage-account"></a>Intégrer Function App à un compte de stockage Azure

Cet exemple de script crée une Function App et un compte de stockage avec les ressources associées. 

[!INCLUDE [sample-cli-install](../../../includes/sample-cli-install.md)]

## <a name="sample-script"></a>Exemple de script 

Exemple de création d’application

[!code-azurecli[main](../../../cli_scripts/azure-functions/create-function-app-connect-to-storage/create-function-app-connect-to-storage-account.sh "Intégrer Function App à un compte de stockage Azure")]


## <a name="clean-up-deployment"></a>Nettoyer le déploiement

Une fois l’exemple de script exécuté, la commande suivante permet de supprimer le groupe de ressources, l’application App Service et toutes les ressources associées :

[!INCLUDE [cli-script-clean-up](../../../includes/cli-script-clean-up.md)]

## <a name="script-explanation"></a>Explication du script

Ce script utilise les commandes suivantes. Chaque commande du tableau renvoie à une documentation spécifique.

| Commande | Remarques |
|---|---|
| [az login](https://docs.microsoft.com/cli/azure/#login) | Connexion à Azure. |
| [az group create](https://docs.microsoft.com/cli/azure/group#create) | Crée un groupe de ressources avec un emplacement. |
| [az storage account create](https://docs.microsoft.com/cli/azure/storage/account) | Créer un compte de stockage |
| [az functionapp create](https://docs.microsoft.com/cli/azure/functionapp#create) | Crée une Function App. |
| [az group delete](https://docs.microsoft.com/cli/azure/group#delete) | Nettoyer |

## <a name="next-steps"></a>Étapes suivantes

Pour plus d’informations sur l’interface Azure CLI, consultez la [documentation relative à l’interface Azure CLI](https://docs.microsoft.com/cli/azure/overview).

Vous trouverez des exemples supplémentaires de scripts CLI Azure Functions dans la [documentation d’Azure Functions](../functions-cli-samples.md).
