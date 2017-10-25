---
title: "Limitations dans une base de données Azure pour MySQL | Microsoft Docs"
description: "Décrit les limitations de la préversion des bases de données Azure pour MySQL."
services: mysql
author: jasonh
ms.author: kamathsun
manager: jhubbard
editor: jasonwhowell
ms.service: mysql-database
ms.topic: article
ms.date: 10/8/2017
ms.openlocfilehash: dc5ad012398b7d07886cd22c20975e61f820d7e0
ms.sourcegitcommit: 54fd091c82a71fbc663b2220b27bc0b691a39b5b
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 10/12/2017
---
# <a name="limitations-in-azure-database-for-mysql-preview"></a>Limitations dans une base de données Azure pour MySQL (préversion)
Le service de base de données Azure pour MySQL est en préversion publique. Les sections suivantes décrivent les limites fonctionnelles et les limites de capacités du service de base de données. Vous pouvez aussi consulter les [limitations générales](https://dev.mysql.com/doc/mysql-reslimits-excerpt/5.6/en/limits.html) qui sont applicables au moteur de base de données MySQL.

## <a name="service-tier-maximums"></a>Valeurs maximales des niveaux de service
Azure Database pour MySQL vous permet de choisir entre plusieurs niveaux de service lorsque vous créez un serveur. Pour plus d’informations, consultez la page [Comprendre les éléments disponibles dans chaque niveau de service](concepts-service-tiers.md).  

Chaque niveau de service dans la préversion comporte un nombre maximal de connexions et d’unités de calcul, ainsi qu’un espace maximal de stockage, comme suit : 

|                            |                   |
| :------------------------- | :---------------- |
| **Nombre maximal de connexions**        |                   |
| 50 unités de calcul de base     | 50 connexions    |
| 100 unités de calcul de base    | 100 connexions   |
| 100 unités de calcul standard | 200 connexions   |
| 200 unités de calcul standard | 400 connexions   |
| 400 unités de calcul standard | 400 connexions   |
| 800 unités de calcul standard | 1 600 connexions  |
| **Nombre maximal d’unités de calcul**      |                   |
| Niveau de service De base         | 100 unités de calcul |
| Niveau de service Standard      | 800 unités de calcul |
| **Volume maximal de stockage**            |                   |
| Niveau de service De base         | 1 To              |
| Niveau de service Standard      | 1 To              |

Au-delà du nombre maximal de connexions, vous risquez de recevoir l’erreur suivante :
> ERROR 1040 (08004): Too many connections

## <a name="preview-functional-limitations"></a>Limitations fonctionnelles de la préversion

### <a name="scale-operations"></a>Opérations de mise à l’échelle
- La mise à l’échelle dynamique de serveurs sur différents niveaux de service n’est pas prise en charge pour le moment. Autrement dit, il n’est pas possible de basculer entre les niveaux de service De base et Standard.
- L’augmentation dynamique de la demande de stockage sur un serveur créé au préalable n’est pas prise en charge pour le moment.
- La diminution de la taille de stockage du serveur n’est pas prise en charge.

### <a name="server-version-upgrades"></a>Mises à niveau de la version du serveur
- La migration automatique entre les versions principales du moteur de base de données n’est pas prise en charge pour le moment.

### <a name="subscription-management"></a>Gestion des abonnements
- Le déplacement dynamique de serveurs créés au préalable entre les groupes de ressources et d’abonnements n’est pas pris en charge pour le moment.

### <a name="point-in-time-restore"></a>Restauration dans le temps
- La restauration à un autre niveau de service et/ou à une autre taille d’unités de calcul et de stockage n’est pas autorisée.
- La restauration d’un serveur supprimé n’est pas prise en charge.

## <a name="next-steps"></a>Étapes suivantes
- [Éléments disponibles dans chaque niveau de service](concepts-service-tiers.md)
- [Versions de base de données MySQL prises en charge](concepts-supported-versions.md)
