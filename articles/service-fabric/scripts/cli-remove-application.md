---
title: "Exemple de script Azure CLI - Supprimer une application d’un cluster"
description: "Exemple de script Azure CLI - Supprimer une application d’un cluster Service Fabric."
services: service-fabric
documentationcenter: 
author: thraka
manager: timlt
editor: 
tags: azure-service-management
ms.assetid: 
ms.service: service-fabric
ms.workload: multiple
ms.devlang: na
ms.topic: article
ms.date: 07/21/2017
ms.author: adegeo
ms.custom: mvc
ms.translationtype: HT
ms.sourcegitcommit: 141270c353d3fe7341dfad890162ed74495d48ac
ms.openlocfilehash: 77fa78199854d10c4897d56da12c8e359ae491f3
ms.contentlocale: fr-fr
ms.lasthandoff: 07/25/2017

---

# <a name="remove-an-application-from-a-service-fabric-cluster"></a>Supprimer une application d’un cluster Service Fabric

Cet exemple de script supprime une instance d’application Service Fabric en cours d’exécution et annule l’inscription d’un type d’application et de la version du cluster.  La suppression de l’instance d’application entraîne également celle de toutes les instances de service en cours d’exécution associées à cette application. Ensuite, les fichiers d’application sont supprimés du magasin d’images. 

Si nécessaire, installez l’Interface de ligne de commande Azure ([Azure CLI](../service-fabric-azure-cli-2-0.md)).

## <a name="sample-script"></a>Exemple de script

[!code-sh[main](../../../cli_scripts/service-fabric/remove-application/remove-application.sh "Supprimer une application d’un cluster")]

## <a name="script-explanation"></a>Explication du script

Ce script utilise les commandes suivantes. Chaque commande du tableau renvoie à une documentation spécifique.

| Commande | Remarques |
|---|---|
| [sf cluster select](/cli/azure/sf/cluster#select) | Sélectionne le cluster à utiliser. |
| [sf application delete](/cli/azure/sf/application#delete) | Supprime l’instance de l’application du cluster. |
| [sf application unprovision](/cli/azure/sf/application#unprovision) | Annule l’inscription d’un type d’application Service Fabric et de sa version dans le cluster.|
| [sf application package-delete](/cli/azure/sf/application#package-delete) | Supprime un package d’application Service Fabric du magasin d’images. |

## <a name="next-steps"></a>Étapes suivantes

Pour plus d’informations, consultez la [documentation relative à l’interface Azure CLI](../service-fabric-azure-cli-2-0.md).

Vous trouverez des exemples supplémentaires de scripts Azure CLI pour Azure Service Fabric dans [Exemples Azure CLI](../samples-cli.md).

