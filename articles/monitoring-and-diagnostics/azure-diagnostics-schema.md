---
title: "Liste des versions de schémas de configuration Azure Diagnostics | Microsoft Docs"
description: Elle permet de configurer la collection de compteurs de performances dans Azure Virtual Machines, VM Scale Sets, Service Fabric et Cloud Services.
services: multiple
documentationcenter: .net
author: rboucher
manager: carmonm
editor: 
ms.assetid: 
ms.service: multiple
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: dotnet
ms.topic: article
ms.date: 01/23/2017
ms.author: robb
translationtype: Human Translation
ms.sourcegitcommit: 8c9c9dea1248205aa6303e11e1166d5d38786c1b
ms.openlocfilehash: 2e1bc45b55cd79af8579a5ddaf43cf0db019c92f


---
# <a name="list-of-azure-diagnostics-versions"></a>Liste des versions Azure Diagnostics
Cette page est un index des versions de schémas Azure Diagnostics fournies avec le Kit de développement logiciel (SDK) Microsoft Azure.  

> [!NOTE]
> Azure Diagnostics est le composant utilisé pour collecter les compteurs de performances et d’autres statistiques d’Azure Virtual Machines, de Virtual Machine Scale Sets, de Service Fabric et de Cloud Services.  Cette page vous concerne uniquement si vous utilisez l’un de ces services.
>

Azure Diagnostics est utilisé conjointement avec d’autres produits de diagnostic Microsoft comme Azure Monitor, Application Insights et Log Analytics.

## <a name="azure-sdk-and-diagnostics-versions-shipping-chart"></a>Graphique de commercialisation des versions des diagnostics et du kit de développement logiciel (SDK) Azure  

|Version du SDK Azure | Version des diagnostics Azure | Modèle|  
|------------------|---------------------------|------|  
|1.x               |1.0                         | plug-in|  
|2.0 - 2.4         |1.0                         |"|  
|2.5               |1.2                         |extension|  
|2.6               |1.3                         |"|  
|2.7               |1.4                         |"|  
|2.8               |1.5                         |"|  

 La version des diagnostics Azure 1.0 est fournie avec un modèle de plug-in, ce qui signifie que lorsque vous avez installé le Kit de développement logiciel (SDK) Azure, vous disposez de la version des diagnostics Azure.  

 À compter de la version 2.5 du Kit de développement logiciel (SDK, version 1.2 d’Azure Diagnostics), Azure Diagnostics est passé à un modèle d’extension. Les outils permettant d’utiliser les nouvelles fonctionnalités sont disponibles uniquement dans les kits de développement logiciel (SDK) Azure plus récents, toutefois n’importe quel service cloud ou machine virtuelle utilisant des diagnostics récupère la dernière version commercialisée directement dans Azure.  

 Par exemple, toute personne utilisant encore le Kit de développement logiciel (SDK) 2.5 chargerait Diagnostics 1.5, qu’elle utilise ou non les fonctionnalités plus récentes.  

## <a name="azure-diagnostics-schemas-index"></a>Index de schémas de diagnostics Azure  
[Schéma de configuration des diagnostics 1.0](azure-diagnostics-schema-1dot0.md)  

[Schéma de configuration des diagnostics 1.2](azure-diagnostics-schema-1dot2.md)  

[Schéma de configuration des diagnostics 1.3 à 1.5](azure-diagnostics-schema-1dot3-to-1dot5.md)  



<!--HONumber=Jan17_HO5-->


