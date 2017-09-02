---
title: Exemple de suppression de script Azure Service Fabric CLI
description: "Supprimer une application d’un cluster Azure Service Fabric via l’interface CLI Azure Service Fabric"
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
ms.topic: sample
ms.date: 08/22/2017
ms.author: adegeo
ms.custom: mvc
ms.translationtype: HT
ms.sourcegitcommit: 25e4506cc2331ee016b8b365c2e1677424cf4992
ms.openlocfilehash: 6ea52da6698647bb21d8f4c9a47a73bd5eef2a29
ms.contentlocale: fr-fr
ms.lasthandoff: 08/24/2017

---

# <a name="remove-an-application-from-a-service-fabric-cluster"></a>Supprimer une application d’un cluster Service Fabric

Cet exemple de script supprime une instance d’application Service Fabric en cours d’exécution et annule l’inscription d’un type d’application et de la version du cluster.  La suppression de l’instance d’application entraîne également celle de toutes les instances de service en cours d’exécution associées à cette application. Ensuite, les fichiers d’application sont supprimés du magasin d’images. 

Si nécessaire, installez l’[interface CLI Service Fabric](../service-fabric-cli.md).

## <a name="sample-script"></a>Exemple de script

[!code-sh[main](../../../cli_scripts/service-fabric/remove-application/remove-application.sh "Supprimer une application d’un cluster")]

## <a name="next-steps"></a>Étapes suivantes

Pour plus d’informations, consultez la [documentation relative à Service Fabric CLI](../service-fabric-cli.md).

Vous trouverez des exemples Service Fabric CLI supplémentaires pour Azure Service Fabric dans la rubrique [Exemples Service Fabric CLI](../samples-cli.md).

