---
title: "Maintenance de préservation pour les machines virtuelles Windows dans Azure | Microsoft Docs"
description: "Migration sur place de machines virtuelles pour la préservation des mises à jour en mémoire."
services: virtual-machines-windows
documentationcenter: 
author: 
manager: timlt
editor: 
tags: azure-service-management,azure-resource-manager
ms.assetid: 
ms.service: virtual-machines-windows
ms.workload: infrastructure-services
ms.tgt_pltfrm: vm-windows
ms.devlang: na
ms.topic: article
ms.date: 03/27/2017
ms.author: 
translationtype: Human Translation
ms.sourcegitcommit: 5cce99eff6ed75636399153a846654f56fb64a68
ms.openlocfilehash: bc903f7523295da704ea8f0128dd553e3fdd96a9
ms.lasthandoff: 03/31/2017


---



# <a name="vm-preserving-maintenance-with-in-place-vm-migration"></a>Maintenance de préservation pour les machines virtuelles Windows dans Azure

Bien que la majorité des mises à jour n’aient aucun impact sur les machines virtuelles hébergées, il existe néanmoins des cas où les mises à jour de composants ou de services ont une interférence minimale sur les machines virtuelles en cours d’exécution (sans un redémarrage complet de la machine virtuelle).

Ces mises à jour sont réalisées avec une technologie qui permet la migration dynamique sur place, également appelée « mise à jour avec préservation de la mémoire ». Lors de la mise à jour de l’hôte, la machine virtuelle est mise en pause, ce qui préserve la mémoire dans la RAM, pendant que l’environnement d’hébergement (le système d’exploitation hôte sous-jacent) applique les correctifs et les mises à jour nécessaires.
La machine virtuelle reprend alors 30 secondes après avoir été mise en pause.
Une fois redémarrée, l’horloge de la machine virtuelle est automatiquement synchronisée.

Les mises à jour ne peuvent pas toutes êtes déployées à l’aide de ce mécanisme, mais étant donné la courte interruption, ce type de déploiement des mises à jour réduit considérablement l’impact sur les machines virtuelles.

Les mises à jour multi-instances (machines virtuelles d’un groupe à haute disponibilité) se voient appliquer un seul domaine de mise à jour à la fois.

Les applications qui s’exécutent sur une machine virtuelle peuvent obtenir des informations sur les mises à jour à venir en appelant les événements planifiés du service de métadonnées. Pour plus d’informations sur les événements planifiés, consultez [Azure Metadata Service - Événements planifiés](../virtual-machines-scheduled-events.md).
