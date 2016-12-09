---
title: "Guide de résilience des services | Microsoft Azure"
description: "Liens vers des recommandations en matière de récupération d’urgence, de résilience et de disponibilité applicables aux services Microsoft Azure."
services: 
documentationcenter: na
author: adamglick
manager: saladki
editor: 
ms.assetid: 919a197d-ba47-4e49-a64c-118e27d5a7df
ms.service: resiliency
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 08/18/2016
ms.author: aglick
translationtype: Human Translation
ms.sourcegitcommit: aafb03c86884d1af3e2cae1134c9e6d7ac42397b
ms.openlocfilehash: 4186211af944c0c0109799a94e4163cfceeee4c4


---
# <a name="microsoft-azure-service-resiliency-guidance"></a>Guide de résilience des services Microsoft Azure
Microsoft Azure a été conçu pour mettre à votre disposition les ressources dont vous avez besoin, quand vous en avez besoin. Dans le cadre des bonnes pratiques de conception et d’exploitation, vous devriez non seulement savoir comment utiliser les services Azure pour en garantir la haute disponibilité, mais également connaître les mesures à prendre dans l’éventualité où votre application serait touchée par une interruption de service. Pour vous aider dans ce processus, ce document contient des liens vers des recommandations en matière de récupération d’urgence, ainsi que des conseils de conception pour divers services Azure.

## <a name="disaster-recovery-guidance"></a>Guide de récupération d’urgence
Les liens ci-dessous peuvent vous fournir toutes les informations nécessaires pour vous aider à restaurer rapidement votre application en cas d’interruption d’un service Azure. Ces liens ont été créés pour vous aider à répondre à la question suivante : « Je suis touché par une interruption du service Azure. Que dois-je faire ? »

## <a name="design-guidance"></a>Guide de conception
Les liens ci-dessous contiennent des recommandations de conception et d’architecture qui vous aident à déterminer la meilleure façon d’utiliser chaque service Azure de manière à optimiser le temps de fonctionnement de votre application. Ces liens ont été créés pour vous aider à répondre à la question suivante : « Comment faire en sorte qu’un bogue, une défaillance matérielle, une interruption de service ou une autre défaillance n’affecte pas la disponibilité globale de mon application ? » S’il n’existe aucune aide spécifique pour le service que vous cherchez, l’article [Haute disponibilité pour les applications conçues sur Microsoft Azure](resiliency-high-availability-azure-applications.md) peut contenir des informations supplémentaires susceptibles de vous aider dans votre conception.

## <a name="service-guidance"></a>Guide des services
| de diffusion en continu | Guide de récupération d’urgence | Guide de conception |
|:--- |:---:|:---:|
| [Cloud Services](https://azure.microsoft.com/services/cloud-services/ "Services cloud Azure") |[link](../cloud-services/cloud-services-disaster-recovery-guidance.md "Guide de récupération d’urgence d’Azure Cloud Services") |Non disponible |
| [Key Vault](https://azure.microsoft.com/services/key-vault/ "Azure Key Vault") |[link](../key-vault/key-vault-disaster-recovery-guidance.md "Guide de récupération d’urgence d’Azure Key Vault") |Non disponible |
| [Stockage](https://azure.microsoft.com/services/storage/ "Azure Stockage") |[link](../storage/storage-disaster-recovery-guidance.md "Guide de récupération d’urgence d’Azure Storage") |Non disponible |
| [Bases de données SQL](https://azure.microsoft.com/services/sql-database/ "Azure Bases de données SQL") |[link](../sql-database/sql-database-disaster-recovery.md "Guide de récupération d’urgence de la base de données SQL Azure") |[link](../sql-database/sql-database-performance-guidance.md "Guide de conception des bases de données SQL Azure") |
| [Machines virtuelles](https://azure.microsoft.com/services/virtual-machines/ "Azure Machines virtuelles") |[link](../virtual-machines/virtual-machines-disaster-recovery-guidance.md "Guide de récupération d’urgence d’Azure Virtual Machines") |Non disponible |
| [Réseau virtuel](https://azure.microsoft.com/services/virtual-network/ "Azure Réseau virtuel") |[link](../virtual-network/virtual-network-disaster-recovery-guidance.md "Guide de récupération d’urgence de réseau virtuel Azure") |Non disponible |

## <a name="next-steps"></a>Étapes suivantes
Si vous cherchez des conseils axés plus largement sur les systèmes et les solutions, consultez la page [Récupération d’urgence et haute disponibilité des applications développées sur Microsoft Azure](https://aka.ms/drtechguide).



<!--HONumber=Nov16_HO3-->


