---
title: "Exemple de script Azure CLI - Déployer une application sur un cluster"
description: "Exemple de script Azure CLI - Déployer une application sur un cluster Service Fabric."
services: service-fabric
documentationcenter: 
author: Thraka
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
ms.openlocfilehash: de6ec4378a05656ecefefa7d5994a4f5dad404ba
ms.contentlocale: fr-fr
ms.lasthandoff: 07/25/2017

---

# <a name="deploy-an-application-to-a-service-fabric-cluster"></a>Déployer une application sur un cluster Service Fabric

Cet exemple de script copie un package d’application dans le magasin d’images d’un cluster, inscrit le type d’application dans le cluster et crée une instance d’application à partir du type d’application.  Si des services par défaut ont été définis dans le manifeste d’application du type d’application cible, ils sont également créés à ce stade.

Si nécessaire, installez l’Interface de ligne de commande Azure ([Azure CLI](../service-fabric-azure-cli-2-0.md)).

## <a name="sample-script"></a>Exemple de script

[!code-sh[main](../../../cli_scripts/service-fabric/deploy-application/deploy-application.sh "Déployer une application sur un cluster")]

## <a name="clean-up-deployment"></a>Nettoyer le déploiement 

Après avoir exécuté l’exemple de script, vous pouvez utiliser le script présenté dans l’article [Supprimer une application](cli-remove-application.md) pour supprimer l’instance d’application, désinscrire le type d’application et supprimer le package d’application du magasin d’images.

## <a name="script-explanation"></a>Explication du script

Ce script utilise les commandes suivantes. Chaque commande du tableau renvoie à une documentation spécifique.


| Commande | Remarques |
|---|---|
| [sf cluster select](/cli/azure/sf/cluster#select) | Sélectionne le cluster à utiliser. |
| [sf application upload](/cli/azure/sf/application#upload) | Charger les fichiers d’application et les manifestes. |
| [sf application provision](/cli/azure/sf/application#provision) | Inscrire l’application sur le cluster.|
| [sf application create](/cli/azure/sf/application#create) | Créer une instance de l’application et déployer les services définis sur les nœuds. |

## <a name="next-steps"></a>Étapes suivantes

Pour plus d’informations, consultez la [documentation relative à l’interface Azure CLI](../service-fabric-azure-cli-2-0.md).

Vous trouverez des exemples supplémentaires de scripts Azure CLI pour Azure Service Fabric dans [Exemples Azure CLI](../samples-cli.md).

