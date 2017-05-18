---
title: "Exemple de script Azure CLI - Mapper un domaine personnalisé à une application web | Microsoft Docs"
description: "Exemple de script Azure CLI - Mapper un domaine personnalisé à une application web"
services: app-service\web
documentationcenter: 
author: cephalin
manager: erikre
editor: 
tags: azure-service-management
ms.assetid: 5ac4a680-cc73-4578-bcd6-8668c08802c2
ms.service: app-service-web
ms.workload: web
ms.devlang: azurecli
ms.tgt_pltfrm: na
ms.topic: sample
ms.date: 04/09/2017
ms.author: cephalin
ms.translationtype: Human Translation
ms.sourcegitcommit: 9568210d4df6cfcf5b89ba8154a11ad9322fa9cc
ms.openlocfilehash: f3c8b50f14e21dfef6fec9aa34244f425808f1bc
ms.contentlocale: fr-fr
ms.lasthandoff: 05/15/2017

---
# <a name="map-a-custom-domain-to-a-web-app"></a>Mapper un domaine personnalisé à une application web

Cet exemple de script crée une application web dans App Service avec ses ressources associées, puis la mappe à `www.<yourdomain>`.

[!INCLUDE [sample-cli-install](../../../includes/sample-cli-install.md)]

## <a name="sample-script"></a>Exemple de script

[!code-azurecli-interactive[principal](../../../cli_scripts/app-service/configure-custom-domain/configure-custom-domain.sh?highlight=3 "Mapper un domaine personnalisé à une application web")]

[!INCLUDE [cli-script-clean-up](../../../includes/cli-script-clean-up.md)]

## <a name="script-explanation"></a>Explication du script

Ce script utilise les commandes suivantes. Chaque commande du tableau renvoie à une documentation spécifique.

| Commande | Remarques |
|---|---|
| [az group create](https://docs.microsoft.com/cli/azure/group#create) | Crée un groupe de ressources dans lequel toutes les ressources sont stockées. |
| [az appservice plan create](https://docs.microsoft.com/cli/azure/appservice/plan#create) | Crée un plan App Service. |
| [az appservice web create](https://docs.microsoft.com/cli/azure/appservice/web#delete) | Crée une application web. |
| [az appservice web config hostname add](https://docs.microsoft.com/cli/azure/appservice/web/config/hostname#add) | Mappez un domaine personnalisé à une application web. |

## <a name="next-steps"></a>Étapes suivantes

Pour plus d’informations sur l’interface Azure CLI, consultez la [documentation relative à l’interface Azure CLI](https://docs.microsoft.com/cli/azure/overview).

Vous trouverez des exemples supplémentaires de scripts CLI App Service dans la [documentation relative à Azure App Service](../app-service-cli-samples.md).

