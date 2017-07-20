---
title: "Maintenance de préservation pour les machines virtuelles Windows dans Azure | Microsoft Docs"
description: "Migration sur place de machines virtuelles pour la préservation des mises à jour en mémoire."
services: virtual-machines-windows
documentationcenter: 
author: zivr
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
ms.author: zivr
ms.translationtype: Human Translation
ms.sourcegitcommit: a643f139be40b9b11f865d528622bafbe7dec939
ms.openlocfilehash: 8888bafbc3aba24168312b611a9b4fbde25f376d
ms.contentlocale: fr-fr
ms.lasthandoff: 05/31/2017


---



# <a name="vm-preserving-maintenance-with-in-place-vm-migration"></a>Maintenance de préservation pour les machines virtuelles Windows dans Azure

Bien que la majorité des mises à jour n’aient aucun impact sur les machines virtuelles hébergées, il existe néanmoins des cas où les mises à jour de composants ou de services ont une interférence minimale sur les machines virtuelles en cours d’exécution (sans un redémarrage complet de la machine virtuelle).

Ces mises à jour sont réalisées avec une technologie qui permet la migration dynamique sur place, également appelée « mise à jour avec préservation de la mémoire ». Lors de la mise à jour de l’hôte, la machine virtuelle est mise en pause, ce qui préserve la mémoire dans la RAM, pendant que l’environnement d’hébergement (le système d’exploitation hôte sous-jacent) applique les correctifs et les mises à jour nécessaires.
La machine virtuelle reprend alors 30 secondes après avoir été mise en pause.
Une fois redémarrée, l’horloge de la machine virtuelle est automatiquement synchronisée.

Les mises à jour ne peuvent pas toutes êtes déployées à l’aide de ce mécanisme, mais étant donné la courte interruption, ce type de déploiement des mises à jour réduit considérablement l’impact sur les machines virtuelles.

Les mises à jour multi-instances (machines virtuelles d’un groupe à haute disponibilité) se voient appliquer un seul domaine de mise à jour à la fois.

Certaines applications peuvent être affectées plus que d’autres par ces mises à jour. Par exemple, les applications qui effectuent des scénarios de traitement d’événements en temps réel, de streaming multimédia, de transcodage multimédia ou de mise en réseau à débit élevé ne peuvent pas être conçues pour tolérer une pause de 30 secondes. Les applications qui s’exécutent sur une machine virtuelle peuvent obtenir des informations sur les mises à jour à venir en appelant l’API des [événements planifiés](../virtual-machines-scheduled-events.md) d’[Azure Metadata Service](../virtual-machines-instancemetadataservice-overview.md).

