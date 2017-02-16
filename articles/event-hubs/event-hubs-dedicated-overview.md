---
title: "Vue d’ensemble des Azure Event Hubs Dedicated | Microsoft Docs"
description: Microsoft Azure Event Hubs
services: event-hubs
author: banisadr
ms.service: event-hubs
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 01/23/2017
ms.author: babanisa
translationtype: Human Translation
ms.sourcegitcommit: 6aebaeccc502d20c8a80297972b8f3b5e8e363c6
ms.openlocfilehash: 3cd3d3fa7a540f9608ac07172bb78ee719d6b0a6


---


# <a name="dedicated-event-hubs--an-overview"></a>Event Hubs Dedicated : vue d’ensemble

La capacité des Event Hubs Dedicated offre des déploiements à locataire unique pour les clients les plus exigeants. Lorsqu’ils sont complètement déployés, les Azure Event Hubs peuvent entrer plus de deux millions d’événements par seconde ou jusqu’à 2 Go par seconde de télémétrie avec un stockage durable et une latence inférieure à une seconde. Cela permet également des solutions intégrées grâce au traitement en temps réel et par lot sur le même système. Avec la fonctionnalité Event Hubs Archive incluse dans l’offre, un seul flux peut prendre en charge des pipelines en temps réel et par lot, réduisant ainsi la complexité de votre solution.

Le tableau ci-dessous compare les niveaux de service Event Hubs disponibles. L’offre Event Hubs Dedicated consiste en un tarif mensuel fixe par rapport à la tarification à l’utilisation pour la plupart des fonctionnalités De base et Standard. Le niveau Dedicated offre les fonctionnalités du plan Standard, mais avec la capacité de mise à l’échelle de classe entreprise pour les clients avec des charges de travail exigeantes.

|  | De base | Standard | Dédié |
| --- |:---:|:---:|:---:|
| Événements d’entrée | Paiement par million d’événements | Paiement par million d’événements | Inclus |
| Unité de débit (entrée de 1 Mo/s, sortie de 2 Mo/s) | Paiement par heure | Paiement par heure | Inclus |
| Taille des messages | 256 Ko | 256 Ko | 1 Mo |
| Stratégies d’éditeur | N/A | ✓ | ✓ |     
| Groupes de consommateurs | 1 - Par défaut | 20 | 20 |
| Relecture des messages | ✓ | ✓  | ✓ |
| Unités de débit maximales | 20 | 20 (flexible jusqu’à 100)  | 1 unité de capacité≈200 |
| Connexions réparties | 100 inclus | 1 000 inclus | 100 000 inclus |
| Connexions négociées supplémentaires | N/A | ✓ | ✓ |
| Rétention des messages | 1 jour inclus | 1 jour inclus | Jusqu’à 7 jours inclus |
| Archiver <sup>Aperçu</sup> | N/A  | Paiement par heure | Inclus |

## <a name="benefits-of-event-hubs-at-dedicated-capacity-include"></a>Avantages de la capacité des Event Hubs Dedicated :

* Hébergement à locataire unique ne subissant pas le bruit d’autres locataires
* Augmentation de la taille des messages jusqu’à 1 Mo contre 256 Ko pour les plans Standard et De base
* Performances reproductibles chaque fois
* Garantie de la capacité à répondre à vos besoins en rafale
* Évolutivité de 1 à 8 unités de capacité : assure jusqu’à 2 millions d’événements d’entrée par seconde
  * Les unités de capacité gèrent la mise à l’échelle des Dedicated Event Hubs, où chaque unité de capacité peut fournir approximativement l’équivalent de 200 unités de débit.
* Aucune maintenance : nous gérons l’équilibrage de la charge, les mises à jour du système d’exploitation, les correctifs de sécurité et le partitionnement
* Tarification mensuelle fixe

Les Dedicated Event Hubs suppriment également certaines des limites de débit de l’offre Standard. Les unités de débit des niveaux De base et Standard autorisent 1 000 événements par seconde ou 1 Mbit/s en entrée par unité de débit, et deux fois cette quantité en sortie. L’offre de mise à l’échelle Dedicated n’impose aucune restriction sur le nombre d’événements d’entrée et de sortie. Ces limites sont uniquement régies par la capacité de traitement Dedicated Event Hubs achetés.

Ce service est destiné aux grands utilisateurs de télémétrie et est disponible pour les clients disposant d’un contrat d’entreprise.

## <a name="how-to-onboard"></a>Intégration : mode d’emploi

La plateforme Event Hubs Dedicated est proposée au public via un contrat d’entreprise dans différentes tailles d’unités de capacité. Chaque unité de capacité fournit environ l’équivalent de 200 unités de débit et est facturée 31 $/h. Vous pouvez faire évoluer votre capacité à la hausse ou à la baisse au cours du mois pour répondre à vos besoins en ajoutant ou en supprimant des unités de capacité. Le plan Dedicated est unique dans la mesure où vous bénéficierez d’une intégration plus pratique de la part de l’équipe produit Event Hubs pour obtenir le déploiement flexible qui vous convient. 


## <a name="next-steps"></a>Étapes suivantes

[Tarification Dedicated Event Hubs](https://azure.microsoft.com/en-us/pricing/details/event-hubs/)

Contactez votre représentant commercial Microsoft ou le support technique Microsoft pour obtenir des informations supplémentaires sur la capacité des Event Hubs Dedicated.


<!--HONumber=Jan17_HO4-->


