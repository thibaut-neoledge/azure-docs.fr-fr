---
title: "Explication des unités de calcul et unités de stockage pour Azure Database pour MySQL | Documents Microsoft"
description: "Explique les concepts d’unité de calcul et d’unité de stockage, et ce qu’il se passe lorsque vous atteignez la capacité maximale en unités de calcul ou de stockage."
services: mysql
author: v-chenyh
ms.author: v-chenyh
manager: jhubbard
editor: jasonh
ms.assetid: 
ms.service: mysql - database
ms.tgt_pltfrm: portal
ms.topic: article
ms.date: 05/10/2017
ms.translationtype: Human Translation
ms.sourcegitcommit: 71fea4a41b2e3a60f2f610609a14372e678b7ec4
ms.openlocfilehash: 2c10253698864b5e1964fbf15b3484752a551c3b
ms.contentlocale: fr-fr
ms.lasthandoff: 05/10/2017

---
# <a name="explaining-compute-unit-and-storage-unit"></a>Explication des unités de calcul et de stockage
Cet article explique les concepts d’unité de calcul et d’unité de stockage, et ce qu’il se passe lorsque vous atteignez la capacité maximale en unités de calcul ou de stockage.

## <a name="what-are-compute-units"></a>Que sont les unités de calcul ?
Les unités de calcul sont une mesure de débit de traitement processeur garanti disponible pour un serveur Azure Database pour MySQL unique. Une unité de calcul est une mesure mélangée de ressources processeur et mémoire. En règle générale, 50 unités de calcul sont équivalentes à un demi-cœur 100 unités de calcul sont équivalentes à un cœur et 2000 unités de calcul sont équivalentes à vingt cœurs de débit de traitement garanti disponible pour votre serveur.

La quantité de mémoire par unité de calcul est optimisée pour les niveaux de service De base, Standard et Premium. Le fait de doubler les unités de calcul en augmentant le niveau de performances revient à doubler l’ensemble des ressources disponibles pour cette instance d’Azure Database pour MySQL particulière

Par exemple, une 2000 unités de calcul Standard fournissent 20x plus de débit processeur et de mémoire que 100 unités de calcul Standard. Toutefois, là où 100 unités de calcul Standard fournissent le même débit processeur que 100 unités de calcul De base, la quantité de mémoire qui est préconfigurée dans le niveau Standard est le double de la quantité de mémoire configurée pour le niveau De base et offre donc une meilleure latence de transaction et de meilleures performances de charge de travail.

>[!IMPORTANT]
>Pour des performances de débit de charge de travail prévisibles et une simultanéité d’utilisateurs importante, il est fortement recommandé de choisir le niveau de service Standard.

Vous pouvez modifier les [niveaux de service](./concepts-service-tiers.md) à tout moment pratiquement sans interruption d’application. Pour de nombreuses entreprises et applications, la possibilité de créer une à plusieurs bases de données dans chaque serveur Azure Database pour MySQL unique et l’augmentation ou la réduction des performances à la demande offre la flexibilité nécessaire pour gérer les coûts.

>[!IMPORTANT]
>Actuellement, nous prenons en charge la montée/diminution en puissance des niveaux de performances au sein d’un niveau de service. Par exemple, vous pouvez faire monter en puissance 100 unités de calcul Standard en 400 unités de calcul Standard. De même, vous pouvez faire diminuer en puissance 400 unités de calcul Standard en 100 unités de calcul Standard. La fonctionnalité permettant de monter/diminuer en puissance à travers les niveaux de service, par exemple entre les niveaux De base et Standard, sera disponible à l’avenir.

## <a name="what-are-storage-units"></a>Que sont les unités de stockage ?
Les unités de stockage sont une mesure de capacité de stockage configurée et garantie disponible pour un serveur Azure Database pour MySQL unique. Chaque niveau de service dispose d’une quantité fixe de stockage configurée, qui est incluse dans le prix du niveau de service sélectionné.

Éventuellement, les unités de stockage peuvent évoluer indépendamment des unités de calcul, par incréments de 125 Go, jusqu'à la limite maximale autorisée de stockage comme indiqué dans le tableau ci-dessous.

| **Fonctionnalités de niveau de service** | **De base** | **Standard** | **Premium\*** |
|---------------------------|-----------|--------------|---------------|
| Unités de stockage incluses | 50 Go | 125 Go | 250 Go |
| Stockage total maximal | 1050 Go | 10000 Go | 4000 Go |
| Garantie d’E/S par seconde de stockage | N/A  | Oui | Oui |
| E/S par seconde de stockage maximales | N/A  | 30 000 | 40 000 |

Les niveaux de service Standard et Premium fournissent également une garantie d’E/S par seconde configurées. La quantité d’E/S par seconde configurées disponibles dépend du niveau de service et de la capacité de stockage configurée. Pour les serveurs déployés dans le niveau Standard, les E/S par seconde sont fixées à 3 fois le stockage configuré. 

Par exemple, si vous avez 125 Go de stockage configuré, 375 E/S par seconde configurées sont disponibles pour votre serveur. Le niveau Premium offre une très faible latence et un stockage d’E/S par seconde élevée. Pour les serveurs déployés dans le niveau Premium, les E/S par seconde faible latence configurées évoluer entre cinq et dix fois le stockage configuré.

>[!IMPORTANT]
>Si votre charge de travail est limitée par les unités de calcul configurées, vous ne serez peut-être pas en mesure d’exploiter pleinement les E/S par seconde configurées disponibles. Il est recommandé de surveiller les unités de calcul et d’envisager la mise à l’échelle des unités de calcul si vous n’êtes pas en mesure d’exploiter pleinement les E/S par seconde configurées disponibles.

La mise à l’échelle des unités de stockage en incrémentant le stockage approvisionné équivaut à augmenter proportionnellement les E/S par seconde configurées pour les niveaux Standard et Premium.

>[!IMPORTANT]
>Le niveau de base n’offre pas de garantie d’E/S.

## <a name="how-can-i-determine-the-number-of-compute-units-needed-for-my-workload"></a>Comment puis-je déterminer le nombre d’unités de calcul requises pour ma charge de travail ?
Si vous cherchez à migrer un serveur existant local ou MySQL s’exécutant sur une machine virtuelle, vous pouvez déterminer le nombre d’unités de calcul en estimant le nombre de noyaux de débit de traitement dont votre charge de travail a besoin. 

Si le serveur local ou sur machine virtuelle utilise actuellement 4 cœurs (sans compter l’hyperthread processeur), vous pouvez commencer par configurer 400 unités de calcul pour Azure Database pour MySQL. Les unités de calcul peuvent monter ou diminuer en puissance en fonction des besoins de votre charge de travail dynamiquement, et pratiquement sans interruption des applications. Vous pouvez également surveiller la consommation d’unités de calcul via le portail ou la CLI pour dimensionner les ressources à la bonne taille pour le serveur MySQL.

## <a name="how-can-i-determine-the-number-of-storage-units-needed-for-my-workload"></a>Comment puis-je déterminer le nombre d’unités de stockage requises pour ma charge de travail ?
Vous pouvez estimer la quantité d’unités de stockage requises en déterminant d’abord la quantité de capacité de stockage nécessaire. Les niveaux De base, Standard et Premium niveau sont fournis avec du stockage inclus, intégré à la référence.

Le second facteur important consiste à déterminer les E/S par seconde requises. Le niveau de base fournit des E/S par seconde variables sans garantie. Le niveau standard s’adapte à un taux fixe de trois E/S par seconde par Go de stockage et garantit les E/S par seconde. Vous pouvez également surveiller la consommation d’E/S par seconde configurées via le portail ou la CLI pour déterminer l’utilisation.

>[!IMPORTANT]
>Les unités de stockage, une fois provisionnées, ne peuvent pas descendre en puissance dynamiquement.

## <a name="what-happens-when-i-hit-my-maximum-compute-units-andor-storage-units"></a>Que se passe-t-il lorsque j’atteins le maximum d’unités de calcul et/ou d’unités de stockage ?
Les niveaux de performances sont étalonnés et régis pour fournir les ressources nécessaires permettant d’exécuter la charge de travail de votre base de données dans les limites maximales autorisées pour le niveau de service/niveau de performances sélectionné.

Si votre charge de travail atteint les limites d’unités de calcul/d’E/S par seconde provisionnées, vous continuez à recevoir les ressources au niveau maximum autorisé, mais la latence de vos requêtes sera augmentée. Ces limites ne génèrent pas d’erreur, plutôt un ralentissement de la charge de travail, sauf si le ralentissement s’accentue au point que les requêtes arrivent à expiration.

Si vous atteignez la limite de nombre de connexions autorisé, vous observez des erreurs explicites. Consultez [Limites de ressources de base données Azure pour MySQL](https://docs.microsoft.com/azure/sql-database/sql-database-resource-limits) pour plus d’informations sur les limites de ressources. <Need to write about the behavior if a user reaches the storage capacity limits>

## <a name="next-steps"></a>Étapes suivantes
[Niveaux de service Azure Database pour MySQL](./concepts-service-tiers.md) 

