---
title: "Maintenance planifiée pour les machines virtuelles Windows dans Azure | Microsoft Docs"
description: "Découvrez en quoi consiste la maintenance planifiée Azure et son incidence possible sur vos machines virtuelles Windows s’exécutant dans Azure."
services: virtual-machines-windows
documentationcenter: 
author: 
manager: timlt
editor: 
tags: azure-service-management,azure-resource-manager
ms.assetid: eb4b92d8-be0f-44f6-a6c3-f8f7efab09fe
ms.service: virtual-machines-windows
ms.workload: infrastructure-services
ms.tgt_pltfrm: vm-windows
ms.devlang: na
ms.topic: article
ms.date: 03/27/2017
ms.author: 
translationtype: Human Translation
ms.sourcegitcommit: 5cce99eff6ed75636399153a846654f56fb64a68
ms.openlocfilehash: 198c1804b342b8faf406a08eff7bc42994b9dd0d
ms.lasthandoff: 03/31/2017


---
# <a name="planned-maintenance-for-windows-virtual-machines"></a>Maintenance planifiée pour les machines virtuelles Windows 

Microsoft Azure exécute régulièrement des mises à jour afin d’améliorer la fiabilité, les performances et la sécurité de l’infrastructure hôte qui supporte les machines virtuelles. Ces mises à jour vont de la mise à jour corrective de composants logiciels dans l’environnement d’hébergement (système d’exploitation, hyperviseur et différents agents déployés sur l’hôte) en passant par la mise à niveau des composants réseau, jusqu’à la désaffectation de matériel.

La majorité de ces mises à jour ont lieu sans affecter les machines virtuelles hébergées et les services cloud.

Cependant, il existe des cas où les mises à jour ont un impact sur les machines virtuelles hébergées :

-   La maintenance préservant les machines virtuelles à l’aide de la migration de machines virtuelles sur place décrit une classe de mises à jour où les machines virtuelles ne sont pas redémarrées pendant la maintenance.

-   La maintenance avec redémarrage des machines virtuelles, qui nécessite un redémarrage ou un redéploiement sur les machines virtuelles hébergées.

Notez que cette page décrit comment Microsoft Azure effectue une maintenance planifiée. Pour plus d’informations sur les événements non planifiés (interruptions), consultez [Gérer la disponibilité des machines virtuelles](manage-availability.md).
