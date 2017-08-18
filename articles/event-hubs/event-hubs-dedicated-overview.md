---
title: "Vue d’ensemble de la capacité Azure Event Hubs Dedicated | Microsoft Docs"
description: "Vue d’ensemble de la capacité Azure Event Hubs Dedicated."
services: event-hubs
documentationcenter: na
author: sethmanheim
manager: timlt
editor: 
ms.assetid: 
ms.service: event-hubs
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 06/01/2017
ms.author: sethm;babanisa
ms.translationtype: Human Translation
ms.sourcegitcommit: 43aab8d52e854636f7ea2ff3aae50d7827735cc7
ms.openlocfilehash: b3af61ec0923a0d9d207cee790d59aa9254a578b
ms.contentlocale: fr-fr
ms.lasthandoff: 06/03/2017


---

# <a name="overview-of-event-hubs-dedicated"></a>Vue d’ensemble d’Event Hubs Dedicated

La capacité *Event Hubs Dedicated* offre des déploiements à locataire unique pour les clients les plus exigeants. Lorsqu’ils sont complètement déployés, les Azure Event Hubs peuvent entrer plus de 2 millions d’événements par seconde ou jusqu’à 2 Go par seconde de télémétrie avec un stockage durable et une latence inférieure à une seconde. Cela permet également des solutions intégrées grâce au traitement en temps réel et par lot sur le même système. Avec la fonctionnalité Event Hubs Archive incluse dans l’offre, vous pouvez réduire la complexité de votre solution par le biais d’un seul flux prenant en charge des pipelines en temps réel et par lot.

Le tableau suivant compare les niveaux de service Event Hubs disponibles. L’offre Event Hubs Dedicated consiste en un tarif mensuel fixe par rapport à la tarification à l’utilisation pour la plupart des fonctionnalités De base et Standard. Le niveau Dedicated offre les fonctionnalités du plan Standard, mais avec la capacité de mise à l’échelle de classe entreprise pour les clients avec des charges de travail exigeantes. 

| Fonctionnalité | De base | Standard | Dédié |
| --- |:---:|:---:|:---:|
| Événements d’entrée | Paiement par million d’événements | Paiement par million d’événements | Inclus |
| Unité de débit (1 Mo/s en entrée, 2 Mo/s en sortie) | Paiement par heure | Paiement par heure | Inclus |
| Taille des messages | 256 KB | 256 KB | 1 Mo |
| Stratégies d’éditeur | N/A | Oui | Oui |     
| Groupes de consommateurs | 1 - par défaut | 20 | 20 |
| Relecture des messages | Oui | Oui | Oui |
| Unités de débit maximales | 20 | 20 (flexible jusqu’à 100)    | 1 unité de capacité≈200 |
| Connexions réparties | 100 inclus | 1 000 inclus | 100 K inclus |
| Connexions négociées supplémentaires | N/A | Oui | Oui |
| Rétention des messages | 1 jour inclus | 1 jour inclus | Jusqu’à 7 jours inclus |
| Archive (version préliminaire) | N/A    | Paiement par heure | Inclus |

## <a name="benefits-of-event-hubs-dedicated-capacity"></a>Avantages de la capacité Event Hubs Dedicated

Les avantages suivants sont disponibles lorsque vous utilisez Event Hubs Dedicated :

* Hébergement à locataire unique ne subissant pas le bruit d’autres locataires.
* Augmentation de la taille des messages jusqu’à 1 Mo contre 256 Ko pour les plans Standard et De base.
* Performances reproductibles chaque fois.
* Garantie de la capacité à répondre à vos besoins en rafale.
* Évolutivité de 1 à 8 unités de capacité : assure jusqu’à 2 millions d’événements d’entrée par seconde.
  * Les unités de capacité gèrent la mise à l’échelle des Event Hubs Dedicated, où chaque unité de capacité peut fournir approximativement l’équivalent de 200 unités de débit.
* Aucune maintenance : nous gérons l’équilibrage de la charge, les mises à jour du système d’exploitation, les correctifs de sécurité et le partitionnement.
* Tarification mensuelle fixe.

Les Event Hubs Dedicated suppriment également certaines des limites de débit de l’offre Standard. Les unités de débit des niveaux De base et Standard autorisent 1 000 événements par seconde ou 1 Mo par seconde en entrée par unité de débit, et deux fois cette quantité en sortie. L’offre de mise à l’échelle Dedicated n’impose aucune restriction sur le nombre d’événements d’entrée et de sortie. Ces limites sont uniquement régies par la capacité de traitement des concentrateurs d’événements achetés.

Ce service est destiné aux grands utilisateurs de télémétrie et est disponible pour les clients disposant d’un contrat d’entreprise.

## <a name="how-to-onboard"></a>Intégration : mode d’emploi

La plateforme Event Hubs Dedicated est proposée via un contrat Entreprise avec différentes tailles d’unités de capacité. Chaque unité de capacité fournit environ l’équivalent de 200 unités de débit. Vous pouvez faire évoluer votre capacité à la hausse ou à la baisse au cours du mois pour répondre à vos besoins en ajoutant ou en supprimant des unités de capacité. Le plan Dedicated est unique dans la mesure où vous bénéficiez d’une intégration plus pratique de la part de l’équipe produit Event Hubs pour obtenir le déploiement flexible qui vous convient. 

## <a name="next-steps"></a>Étapes suivantes
Contactez votre représentant commercial Microsoft ou le support technique Microsoft pour obtenir des informations supplémentaires sur la capacité des Event Hubs Dedicated. Vous pouvez également en apprendre plus sur Event Hubs en consultant les liens suivants :

Pour plus d’informations sur la tarification, accédez aux liens suivants :

- [Tarification d’Event Hubs Dedicated](https://azure.microsoft.com/pricing/details/event-hubs/). Vous pouvez également contacter votre représentant commercial Microsoft ou le support technique Microsoft pour obtenir des informations supplémentaires sur la capacité Event Hubs Dedicated.
- L’article [FAQ sur les hubs d’événements](event-hubs-faq.md) traite des informations de tarification et répond à certaines questions fréquemment posées sur Event Hubs. 

