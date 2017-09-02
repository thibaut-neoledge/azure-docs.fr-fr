---
title: "Créer des référentiels Azure Container Registry | Microsoft Docs"
description: "Utiliser des référentiels Azure Container Registry pour gérer des images Docker"
services: container-registry
documentationcenter: 
author: cristy
manager: balans
editor: dlepow
ms.service: container-registry
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 05/22/2017
ms.author: cristyg
ms.translationtype: Human Translation
ms.sourcegitcommit: 3bbc9e9a22d962a6ee20ead05f728a2b706aee19
ms.openlocfilehash: dd4feff057269ed7106990bb63eed7fcffa2dbec
ms.contentlocale: fr-fr
ms.lasthandoff: 06/10/2017

---
# <a name="azure-container-registry-repositories"></a>Référentiels Azure Container Registry

Les référentiels Azure Container Registry sont compatibles avec une multitude de services et d’orchestrators. Pour faciliter le suivi des services et des agents sources à partir desquels l’ACR est utilisé, nous avons commencé par utiliser le champ d’en-tête Docker dans le fichier Docker.config.



## <a name="viewing-repositories-in-the-portal"></a>Affichage de référentiels dans le portail

Les en-têtes ACR présentent le format suivant :
```
X-Meta-Source-Client: <cloud>/<service>/<optionalservicename>
```

* Cloud : Azure, Azure Stack ou tout autre cloud Azure propre au secteur public ou à un pays. Même si Azure Stack et les clouds pour le secteur public ne sont actuellement pas pris en charge, ce paramètre permet d’envisager une prise en charge future.
* Service : nom du service.
* Optionalservicename : paramètre facultatif pour les services comprenant des sous-services ou pour spécifier une référence SKU (ex : les applications web correspondent à Azure/app-service/web-apps).

Les services et orchestrators partenaires sont encouragés à utiliser des valeurs d’en-tête spécifiques pour faciliter notre télémétrie. S’ils le souhaitent, les utilisateurs peuvent aussi modifier la valeur passée à l’en-tête.

Les valeurs que nous souhaitons que les partenaires ACR utilisent pour remplir le champ « X-Meta-Source-Client » sont les suivantes :

| Nom du service              | En-tête                                |
| ------------------------- | ------------------------------------- |
| Azure Container Service   | azure/compute/azure-container-service |
| App Service - Web Apps    | azure/app-service/web-apps            |
| App Service - Logic Apps  | azure/app-service/logic-apps          |
| Batch                     | azure/compute/batch                   |
| Cloud Console             | azure/cloud-console                   |
| Functions                 | azure/compute/functions               |
| Internet des objets - Hub  | azure/iot/hub                         |
| HDInsight                 | azure/data/hdinsight                  |
| Jenkins                   | azure/jenkins                         |
| Machine Learning          | azure/data/machile-learning           |
| Service Fabric            | azure/compute/service-fabric          |
| VSTS                      | azure/vsts                            |


## <a name="next-steps"></a>Étapes suivantes
[En savoir plus sur les registres et les services et orchestrators pris en charge](container-registry-intro.md)

