---
title: "Maintenance planifiée pour des machines virtuelles Linux dans Azure | Microsoft Docs"
description: "Découvrez en quoi consiste la maintenance planifiée Azure et son incidence possible sur vos machines virtuelles Windows s’exécutant dans Azure."
services: virtual-machines-linux
documentationcenter: 
author: zivr
manager: timlt
editor: 
tags: azure-resource-manager,azure-service-management
ms.assetid: 
ms.service: virtual-machines-linux
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: vm-linux
ms.workload: infrastructure-services
ms.date: 03/27/2017
ms.author: zivr
ms.translationtype: Human Translation
ms.sourcegitcommit: eeb56316b337c90cc83455be11917674eba898a3
ms.openlocfilehash: 28c2fb5a67eca0c5ab2f0299bba7c11375e10558
ms.contentlocale: fr-fr
ms.lasthandoff: 04/03/2017


---

# <a name="planned-maintenance-for-linux-virtual-machines"></a>Maintenance planifiée pour les machines virtuelles Linux 

Microsoft Azure exécute régulièrement des mises à jour afin d’améliorer la fiabilité, les performances et la sécurité de l’infrastructure hôte qui supporte les machines virtuelles. Ces mises à jour vont de la mise à jour corrective de composants logiciels dans l’environnement d’hébergement (système d’exploitation, hyperviseur et différents agents déployés sur l’hôte) en passant par la mise à niveau des composants réseau, jusqu’à la désaffectation de matériel.

La majorité de ces mises à jour ont lieu sans affecter les machines virtuelles hébergées et les services cloud.

Cependant, il existe des cas où les mises à jour ont un impact sur les machines virtuelles hébergées :

-   La maintenance préservant les machines virtuelles à l’aide de la migration de machines virtuelles sur place décrit une classe de mises à jour où les machines virtuelles ne sont pas redémarrées pendant la maintenance.

-   La maintenance avec redémarrage des machines virtuelles, qui nécessite un redémarrage ou un redéploiement sur les machines virtuelles hébergées.

Notez que cette page décrit comment Microsoft Azure effectue une maintenance planifiée. Pour plus d’informations sur les événements non planifiés (interruptions), consultez [Gérer la disponibilité des machines virtuelles](../windows/manage-availability.md).
