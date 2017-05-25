---
title: "Niveaux de service dans une base de données Azure pour PostgreSQL | Microsoft Docs"
description: "Niveaux de service dans la base de données Azure pour PostgreSQL"
services: postgresql
author: kamathsun
ms.author: sukamat
manager: jhubbard
editor: jasonh
ms.assetid: 
ms.service: postgresql-database
ms.tgt_pltfrm: portal
ms.topic: article
ms.date: 05/10/2017
ms.translationtype: Human Translation
ms.sourcegitcommit: 71fea4a41b2e3a60f2f610609a14372e678b7ec4
ms.openlocfilehash: 23a93060697bc48084658bbbd895d13fd393b07b
ms.contentlocale: fr-fr
ms.lasthandoff: 05/10/2017

---
# <a name="azure-database-for-postgresql-options-and-performance-understand-whats-available-in-each-service-tier"></a>Options et performances de la base de données Azure pour PostgreSQL : comprendre les éléments disponibles dans chaque niveau de service

En préversion, la base de données Azure pour PostgreSQL propose uniquement les niveaux de service De base et Standard. Le niveau Premium n’est pas encore disponible.

Chaque niveau de service est associé à plusieurs niveaux de performances pour gérer différents types de besoins en matière de charge de travail. Plus un niveau de performances est élevé, plus le nombre de ressources dont vous disposez est étendu afin de générer un meilleur débit. Vous pouvez modifier les niveaux de performances au sein d’un niveau de service de façon dynamique, sans temps d’arrêt des applications.

À l’avenir, il sera possible de passer d’un niveau de service à l’autre.

> [!IMPORTANT]
> Le service est actuellement en préversion publique ; par conséquent, il ne fournit pas encore de Contrat de niveau de Service (SLA).

Vous pouvez créer un serveur PostgreSQL unique avec des ressources dédiées au sein d’un niveau de service avec un niveau de performances spécifique. Vous pouvez alors créer une ou plusieurs bases de données sur le serveur dans lequel les ressources sont partagées entre plusieurs bases de données. Les ressources disponibles pour un serveur PostgreSQL unique sont exprimées en unités de calcul et en unités de stockage. Pour plus d’informations sur les unités de calcul et de stockage, consultez la page [Explication des unités de calcul et de stockage](concepts-compute-unit-and-storage.md).

## <a name="choosing-a-service-tier"></a>Choix d’un niveau de service

Le tableau suivant fournit des exemples de niveaux adaptés à différentes charges de travail d'application.

| Niveau de service | Charges de travail cibles |
| :----------- | :----------------|
| De base | Idéal pour les petites charges de travail qui requièrent une capacité de calcul et de stockage évolutive sans garantie d’E/S par seconde. Exemple : serveurs utilisés pour le développement ou le test ou pour des applications à petite échelle rarement utilisées. |
| Standard | L’option incontournable pour les applications cloud qui ont besoin d’une garantie d’E/S par seconde avec la possibilité d’évoluer indépendamment vers une capacité de calcul et de stockage supérieure pour un débit élevé. Exemples : applications web ou applications d’analyse. |
| Premium | Idéal pour les charges de travail nécessitant de très courtes latences pour les transactions et les E/S, avec un débit élevé d’E/S et de charges de travail. Assure la meilleure prise en charge possible d’un grand nombre d’utilisateurs simultanés. S’applique aux bases de données qui prennent en charge les applications critiques.<br />Le niveau de service Premium n’est pas disponible en préversion. |
| &nbsp; | &nbsp; |

Pour choisir un niveau de service, commencez par déterminer si votre charge de travail a besoin d’une garantie d’E/S par seconde. Ensuite, déterminez les fonctionnalités dont vous avez besoin au minimum :

| **Fonctionnalités de niveau de service** | **De base** | **Standard** | **Premium** * |
| :------------------------ | :-------- | :----------- | :------------ |
| Nombre maximal d’unités de calcul | 100 | 2000 | Non disponible en préversion |
| Volume total de stockage maximal | 1 050 Go | 10 000 Go | Non disponible en préversion |
| Garantie d’E/S par seconde de stockage | N/A  | Oui | Non disponible en préversion |
| Quantité maximale d’E/S par seconde de stockage | N/A  | 30 000 | Non disponible en préversion |
| Période de rétention de sauvegarde de bases de données | 7 jours | 35 jours | 35 jours |
| &nbsp; | &nbsp; | &nbsp; | &nbsp; |

> [!NOTE]
> Le niveau de service Standard en préversion prend actuellement en charge jusqu’à 800 unités de calcul et 1 000 Go de stockage au maximum.

Une fois que vous avez déterminé le niveau de service minimal, vous êtes prêt à déterminer le niveau de performances du serveur PostgreSQL (les unités de calcul). Les 200 et 400 unités de calcul standard constituent souvent un bon point de départ pour les applications qui ont besoin d’un accès utilisateur concurrentiel supérieur pour leurs charges de travail d’analyse ou web. 

Toutefois, vous pouvez augmenter ou réduire le nombre d’unités de calcul indépendamment des unités de stockage, en fonction des exigences de la charge de travail. Si la charge de travail a besoin d’un ajustement des ressources de calcul, vous pouvez augmenter ou diminuer dynamiquement le nombre d’unités de calcul. Si votre charge de travail a besoin de plus d’E/S par seconde ou de stockage, vous pouvez également mettre à l’échelle le stockage.

> [!NOTE]
> En préversion, les niveaux De base et Standard ne prennent pas en charge la mise à l’échelle dynamique du stockage pour le moment. Nous prévoyons d’ajouter cette fonctionnalité à l’avenir.

> [!NOTE]
> Dans le niveau de service Standard, les E/S par seconde s’adaptent proportionnellement à la taille de stockage approvisionnée selon un rapport fixe de trois contre un. Le stockage de 125 Go inclus garantit 375 E/S par seconde approvisionnées, chaque E/S pouvant atteindre 256 Ko. Si vous approvisionnez 1 000 Go, vous obtiendrez 3 000 E/S par seconde approvisionnées. Vous devez surveiller la consommation d’unités de calcul du serveur et monter en puissance pour utiliser pleinement les E/S par seconde approvisionnées disponibles.

## <a name="service-tiers-and-performance-levels"></a>Niveaux de service et niveaux de performances

La base de données Azure pour PostgreSQL offre plusieurs niveaux de performances au sein de chaque niveau de service. Vous avez la possibilité de choisir le niveau qui répond le mieux aux besoins de votre charge de travail, en utilisant l’un des outils suivants :

- [Portail Azure](/azure/azure-portal-overview), à l’adresse [http://portal.azure.com](http://portal.azure.com)
- [Interface de ligne de commande Azure](quickstart-create-server-database-azure-cli.md)

Quel que soit le nombre de bases de données hébergées dans chaque serveur PostgreSQL, votre base de données obtient toujours un ensemble de ressources garanti, et les caractéristiques de performance attendues du serveur ne sont pas affectées.

Niveau de service De base :

| **Niveau de performances** | **50** | **100** |
| :-------------------- | :----- | :------ |
| Nombre maximal d’unités de calcul | 50 | 100 |
| Taille du stockage inclus | 50 Go | 50 Go |
| Taille maximale de stockage du serveur\* | 1 050 Go | 1 050 Go |
| Nombre maximal de connexions simultanées | &nbsp; | &nbsp; |
| Nombre maximal de connexions | &nbsp; | &nbsp; |
| &nbsp; | &nbsp; | &nbsp; |

Niveau de service Standard :

| **Niveau de performances** | **100** | **200** | **400** | **800** |
| :-------------------- | :------ | :------ | :------ | :------ |
| Nombre maximal d’unités de calcul | 100 | 200 | 400 | 800 |
| Taille du stockage inclus et E/S par seconde approvisionnées | 125 Go, 375 E/S par seconde | &nbsp; | &nbsp; | &nbsp; |
| Taille maximale de stockage du serveur\* | 1 To | &nbsp; | &nbsp; | &nbsp; |
| Nombre maximal d’E/S par seconde du serveur approvisionnées | 3 000 E/S par seconde | &nbsp; | &nbsp; | &nbsp; |
| Nombre maximal d’E/S par seconde du serveur approvisionnées par Go | 3 E/S par seconde fixes par Go | &nbsp; | &nbsp; | &nbsp; |
| Nombre maximal de connexions simultanées | &nbsp; | &nbsp; | &nbsp; | &nbsp; |
| Nombre maximal de connexions | &nbsp; | &nbsp; | &nbsp; | &nbsp; |
| &nbsp; | &nbsp; | &nbsp; | &nbsp; | &nbsp; |

\*La taille maximale de stockage du serveur fait référence à la taille maximale de stockage approvisionnée pour votre serveur.

## <a name="scaling-up-or-down-a-single-server"></a>Montée ou descente en puissance d’un serveur unique

Après avoir choisi un niveau de service et un niveau de performances initiaux, vous pouvez monter ou descendre en puissance le serveur de façon dynamique en fonction des besoins de votre charge de travail. Pour cela, vous pouvez très simplement modifier le niveau de votre base de données sur le Portail Azure ou avec Azure CLI.

La modification du niveau de service et/ou de performances d’une base de données crée un réplica de la base de données d’origine au nouveau niveau de performances, puis bascule les connexions vers ce réplica. Aucune donnée n’est perdue lors de ce processus, mais pendant le bref instant où nous basculons vers le réplica, les connexions à la base de données sont désactivées, de sorte que certaines transactions en cours sont susceptibles d’être restaurées. Cette fenêtre de désactivation varie, mais dure moins de 4 secondes en moyenne, et ne dépasse pas 30 secondes dans plus de 99 % des cas. Cette durée peut se révéler supérieure, en particulier s’il existe un très grand nombre de transactions en cours au moment où les connexions sont désactivées.

La durée de la totalité du processus de montée en puissance dépend de la taille et du niveau de service du serveur avant et après la modification. Par exemple, la modification des unités de calcul d’un serveur vers ou à partir d’un niveau de service Standard ne demande que quelques minutes. Les nouvelles propriétés du serveur ne sont appliquées qu’une fois les modifications terminées.

### <a name="documentation-about-the-steps-for-scaling-up-or-down"></a>Documentation sur les étapes de montée et descente en puissance

- [Gérer un serveur unique sur le Portail Azure](quickstart-create-server-database-portal.md)
- [Gérer une base de données unique avec Azure CLI](quickstart-create-server-database-azure-cli.md)

### <a name="details-about-scaling-up-or-down"></a>Autres informations sur la montée et la descente en puissance

- Pour descendre en puissance un serveur, ses unités de stockage doivent être inférieures à la taille maximale autorisée par le niveau de service voulu.
- Les offres de service de restauration sont différentes selon les niveaux de service. Si vous passez à une version antérieure, vous risquez de ne plus pouvoir effectuer de restauration à un moment donné, ou de bénéficier d’une période de rétention des sauvegardes moins étendue. Pour plus d’informations, consultez la page [Guide pratique pour sauvegarder et restaurer un serveur de base de données Azure pour PostgreSQL à l’aide du Portail Azure](howto-restore-server-portal.md).
- Les nouvelles propriétés du serveur ne sont appliquées qu’une fois les modifications terminées.

