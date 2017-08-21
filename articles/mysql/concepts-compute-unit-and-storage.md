---
title: "Présentation des unités de calcul dans Azure Database pour MySQL | Microsoft Docs"
description: "Azure Database pour MySQL : Cet article explique les concepts des unités de calcul et ce qui se passe quand votre charge de travail atteint le maximum d’unités de calcul."
services: mysql
author: jasonwhowell
ms.author: jasonh
manager: jhubbard
editor: jasonwhowell
ms.service: mysql-database
ms.topic: article
ms.date: 05/23/2017
ms.translationtype: HT
ms.sourcegitcommit: f76de4efe3d4328a37f86f986287092c808ea537
ms.openlocfilehash: a82c283df688d36cd284973312e276f30ed893c0
ms.contentlocale: fr-fr
ms.lasthandoff: 07/11/2017

---
# <a name="explaining-compute-units-in-azure-database-for-mysql"></a>Présentation des unités de calcul dans Azure Database pour MySQL
Cet article explique les concepts des unités de calcul et ce qui se passe quand votre charge de travail atteint le maximum d’unités de calcul.

## <a name="what-are-compute-units"></a>Que sont les unités de calcul ?
Les unités de calcul sont une mesure du débit de traitement du processeur, dont la disponibilité est garantie pour un serveur Azure Database pour MySQL. Une unité de calcul est une mesure mélangée de ressources processeur et mémoire. Globalement, 50 unités de calcul sont équivalentes à la moitié d’un cœur. 100 unités de calcul sont équivalentes à un cœur. 2 000 unités de calcul sont équivalentes à vingt cœurs de débit de traitement garanti disponible pour votre serveur.

La quantité de mémoire par unité de calcul est optimisée pour les niveaux tarifaires De base et Standard. Le fait de doubler les unités de calcul en augmentant le niveau de performances revient à doubler l’ensemble des ressources disponibles pour cette instance d’Azure Database pour MySQL.

Par exemple, 800 unités de calcul Standard fournissent 8 fois plus de débit d’UC et de mémoire qu’une configuration de 100 unités de calcul Standard. Cependant, là où 100 unités de calcul Standard fournissent le même débit d’UC que 100 unités de calcul De base, la quantité de mémoire qui est préconfigurée dans le niveau tarifaire Standard est le double de la quantité de mémoire configurée pour le niveau tarifaire De base. Par conséquent, le niveau tarifaire Standard offre de meilleures performances de charge de travail et une latence inférieure des transactions par rapport au niveau tarifaire De base avec le même nombre d’unités de calcul sélectionné.

## <a name="how-can-i-determine-the-number-of-compute-units-needed-for-my-workload"></a>Comment puis-je déterminer le nombre d’unités de calcul requises pour ma charge de travail ?
Si vous cherchez à migrer un serveur MySQL existant s’exécutant localement ou sur une machine virtuelle, vous pouvez déterminer le nombre d’unités de calcul en estimant le nombre de cœurs de débit de traitement dont votre charge de travail a besoin. 

Si votre serveur actuel local ou sur machine virtuelle utilise actuellement 4 cœurs (sans compter l’hyperthread d’UC), commencez par configurer 400 unités de calcul pour votre serveur Azure Database pour serveur MySQL. Les unités de calcul peuvent être augmentées ou diminuées dynamiquement en fonction des besoins de votre charge de travail, pratiquement sans interruption des applications. 

Surveillez le graphe des métriques dans le portail Azure ou lancez des commandes Azure CLI pour mesurer les unités de calcul. Les métriques pertinentes à surveiller sont Pourcentage d’unités de calcul et Limite d’unités de calcul.

>[!IMPORTANT]
> Si vous trouvez que les E/S par seconde du stockage ne sont pas entièrement utilisées jusqu’à leur valeur maximale, vous pouvez aussi surveiller l’utilisation des unités de calcul. L’augmentation des unités de calcul peut permettre un débit d’E/S plus élevé, en réduisant le goulot d’étranglement des performances dû à une quantité d’UC ou de mémoire limitée.

## <a name="what-happens-when-i-hit-my-maximum-compute-units"></a>Que se passe-t-il quand j’atteins le maximum d’unités de calculs ?
Les niveaux de performances sont étalonnés et prévus pour fournir les ressources nécessaires pour exécuter la charge de travail de votre base de données jusqu’aux limites maximales pour le niveau de tarifaire et le niveau de performances sélectionnés. 

Si votre charge de travail atteint les limites maximales pour les unités de calcul ou pour les E/S par seconde approvisionnées, vous continuez à utiliser les ressources au niveau maximal autorisé, mais la latence de vos requêtes risque d’augmenter. Ces limites ne génèrent pas d’erreur mais plutôt un ralentissement de la charge de travail, sauf si le ralentissement s’accentue au point que les requêtes dépassent le délai d’expiration. 

Si votre charge de travail atteint les limites maximales du nombre de connexions, des erreurs explicites sont déclenchées. Pour plus d’informations sur les limites des ressources, consultez [Limitations dans Azure Database pour MySQL](concepts-limits.md).

## <a name="next-steps"></a>Étapes suivantes
Pour plus d’informations sur les niveaux tarifaires, consultez [Niveaux tarifaires d’Azure Database pour MySQL](./concepts-service-tiers.md).

