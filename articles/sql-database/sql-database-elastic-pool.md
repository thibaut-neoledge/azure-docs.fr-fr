---
title: "Qu’est-ce qu’un pool élastique ? Gérer plusieurs bases de données SQL - Azure | Microsoft Docs"
description: "Gérez et mettez à l’échelle plusieurs bases de données SQL (des centaines et des milliers) avec des pools élastiques. Un prix unique pour des ressources que vous pouvez distribuer là où vous en avez besoin."
keywords: "plusieurs bases de données, ressources des bases de données, performances des bases de données"
services: sql-database
documentationcenter: 
author: ddove
manager: jhubbard
editor: 
ms.assetid: b46e7fdc-2238-4b3b-a944-8ab36c5bdb8e
ms.service: sql-database
ms.custom: multiple databases
ms.devlang: NA
ms.date: 05/31/2017
ms.author: ddove
ms.workload: data-management
ms.topic: article
ms.tgt_pltfrm: NA
ms.translationtype: Human Translation
ms.sourcegitcommit: 95b8c100246815f72570d898b4a5555e6196a1a0
ms.openlocfilehash: de0c9969ec28e7b6e57bc384ac3ec47191096a9b
ms.contentlocale: fr-fr
ms.lasthandoff: 05/18/2017


---

# <a name="elastic-pools-help-you-manage-and-scale-multiple-sql-databases"></a>Les pools élastiques vous aident à gérer et à mettre à l’échelle plusieurs bases de données SQL

Les pools élastiques SQL Database représentent une solution simple et rentable de gestion et de mise à l’échelle de plusieurs bases de données qui ont des demandes d’utilisation variables et imprévisibles. Les bases de données d’un pool élastique se trouvent sur un seul serveur Azure SQL Database et partagent un nombre défini de ressources ([DTU élastiques](sql-database-what-is-a-dtu.md) (eDTU)) à prix fixe. Les pools élastiques dans Azure SQL Database permettent aux développeurs SaaS d’optimiser le rapport performance/prix pour un groupe de bases de données dans un budget prescrit tout en offrant une élasticité des performances pour chaque base de données.   

> [!NOTE]
> Les pools élastiques sont mis à la disposition générale dans toutes les régions Azure, à l’exception de l’Inde de l’Ouest, où ils sont actuellement en version préliminaire.  Les pools élastiques seront en disposition générale dès que possible dans cette région.
>

## <a name="overview-of-elastic-pools"></a>Vue d’ensemble des pools élastiques 

Les développeurs SaaS créent des applications qui reposent sur des couches de données à grande échelle composées de plusieurs bases de données. Un modèle d’application courant consiste à approvisionner une base de données pour chaque client. Toutefois, les modèles d’utilisation sont souvent imprévisibles et varient d’un client à l’autre, et il est difficile de prévoir les besoins en ressources de chaque utilisateur de base de données spécifique. Vous disposez généralement de deux options : 

- Sur-approvisionner les ressources en fonction de l’utilisation maximale et payer trop, ou
- Sous-approvisionner pour réduire les coûts, au détriment des performances et de la satisfaction des clients au moment des pics d’utilisation. 

Les pools élastiques résolvent ce problème en vous assurant que les bases de données disposent des ressources de performances nécessaires au moment où elles en ont besoin. Ils représentent un mécanisme simple d’allocation de ressources avec un budget prévisible. Pour en savoir plus sur les modèles de conception pour les applications SaaS avec des pools élastiques, voir [Design Patterns for Multi-tenant SaaS Applications with Azure SQL Database](sql-database-design-patterns-multi-tenancy-saas-applications.md)(Modèles de conception pour les applications SaaS mutualisées avec la base de données SQL Azure).

> [!VIDEO https://channel9.msdn.com/Blogs/Azure/Elastic-databases-helps-SaaS-developers-tame-explosive-growth/player]
>

Les pools élastiques permettent au développeur d’acheter des [unités de transaction de base de données élastiques](sql-database-what-is-a-dtu.md) (eDTU) pour un pool partagé par plusieurs bases de données afin de s’adapter aux périodes imprévisibles d’utilisation par les bases de données. Le besoin en eDTU d’un pool est déterminé par l'utilisation globale de ses bases de données. Le nombre d’eDTU disponible pour le pool est contrôlé par le budget du développeur. Le développeur ajoute simplement des bases de données au pool, définit le nombre minimal et maximal d’eDTU pour les bases de données, puis définit l’eDTU du pool en fonction de son budget. Un développeur peut utiliser des pools pour faire évoluer en toute transparence son service en passant d’une lean startup à une entreprise mature à une vitesse sans cesse croissante.

Au sein du pool, les différentes bases de données peuvent en toute souplesse s’adapter automatiquement en fonction des paramètres définis. Si la charge est élevée, une base de données peut consommer plus d’eDTU pour répondre à la demande. Les bases de données soumises à des charges légères en consomment moins, tandis que celles qui ne sont soumises à aucune charge n’en consomment pas du tout. L’approvisionnement des ressources pour l’ensemble du pool plutôt que pour des bases de données uniques simplifie vos tâches de gestion. En outre, vous disposez d’un budget prévisible pour le pool. Des eDTU peuvent être ajoutées à un pool existant sans temps d’arrêt de la base de données, à ceci près que les bases de données peuvent être déplacées pour fournir les ressources de calcul supplémentaires à la nouvelle réservation d’eDTU. De même, si les eDTU supplémentaires ne sont plus nécessaires, ils peuvent être supprimés à partir d’un pool existant à tout moment. De plus, vous pouvez ajouter des bases de données au pool ou en retirer. Si une base de données finit par sous-utiliser les ressources, retirez-la.

Vous pouvez créer et gérer un pool élastique en utilisant le [portail Azure](sql-database-elastic-pool-manage-portal.md), [PowerShell](sql-database-elastic-pool-manage-powershell.md), [Transact-SQL](sql-database-elastic-pool-manage-tsql.md), [C#](sql-database-elastic-pool-manage-csharp.md) et l’API REST. 

## <a name="when-to-consider-a-pool"></a>Quand envisager d’utiliser un pool
Les pools sont idéaux dans le cas de nombreuses bases de données avec des modèles d’utilisation spécifiques. Pour une base de données indiquée, ce modèle se caractérise par une faible utilisation moyenne avec des pics d'utilisation relativement rares.

Plus vous ajoutez de bases de données à un pool, plus vous faites d'économies. En fonction de votre modèle d’utilisation de l’application, il est possible de faire des économies avec seulement deux bases de données S3.  

Les sections suivantes vous aident à comprendre comment évaluer si votre collection de bases de données spécifique peut tirer profit de l’utilisation d’un pool. Les exemples utilisent les pools Standard, mais ces principes s’appliquent également aux pools De base et Premium.

### <a name="assessing-database-utilization-patterns"></a>Évaluation des modèles d'utilisation de base de données
La figure suivante montre l’exemple d'une base de données qui est très souvent inactive, mais qui connaît de temps en temps des pics d’activité. Il s’agit d’un modèle d’utilisation particulièrement adapté à un pool :

   ![une base de données unique adaptée à un pool](./media/sql-database-elastic-pool/one-database.png)

Pour la période de cinq minutes illustrée, DB1 culmine à 90 DTU, mais son utilisation moyenne totale est inférieure à cinq DTU. Un niveau de performance S3 est requis pour exécuter cette charge de travail dans une base de données unique. Cependant, cela laisse la plupart des ressources inutilisées pendant les périodes de faible activité.

Un pool permet de partager ces DTU inutilisées entre plusieurs bases de données, ce qui réduit la quantité totale de DTU nécessaires et le coût global.

En s'appuyant sur l'exemple précédent, supposons qu'il y ait des bases de données supplémentaires avec des modèles d'utilisation similaires comme DB1. Dans les deux figures ci-dessous, l’utilisation de quatre bases de données et celle de 20 bases de données sont représentées sur le même graphique pour montrer que leur utilisation ne se chevauche pas dans le temps :

   ![quatre bases de données avec un modèle d’utilisation adapté à un pool](./media/sql-database-elastic-pool/four-databases.png)

  ![vingt bases de données avec un modèle d’utilisation adapté à un pool](./media/sql-database-elastic-pool/twenty-databases.png)

L’utilisation globale des DTU pour les 20 bases de données est illustrée par la ligne noire dans la figure ci-dessus. Cela montre que l'utilisation globale des DTU ne dépasse jamais 100 DTU et que les 20 bases de données peuvent partager 100 eDTU sur cette période. Le nombre de DTU est diminué par 20 et le prix par 13 par rapport au placement de chacune des bases de données dans des niveaux de performances S3 pour les bases de données uniques.

Cet exemple est idéal pour les raisons suivantes :

* Il existe de grandes différences entre les pics d’utilisation et l'utilisation moyenne par base de données.  
* Les pics d’utilisation de chaque base de données se produisent à différents moments dans le temps.
* Les eDTU sont partagées entre plusieurs bases de données.

Le prix d’un pool dépend des eDTU du pool. Alors que le prix unitaire d’une eDTU pour un pool est 1,5 fois supérieur au prix unitaire d’une DTU pour une base de données unique, les **eDTU de pool peuvent être partagées avec de nombreuses bases de données ; un nombre moins important d’eDTU est donc requis au total**. Ces différences en matière de prix et de partage des eDTU constituent la base du potentiel d'économies que les pools peuvent présenter.  

Les règles élémentaires suivantes relatives au nombre de bases de données et à l’utilisation des bases de données permettent de s’assurer qu’un pool coûte moins cher que l’utilisation de niveaux de performances pour des bases de données uniques.


### <a name="minimum-number-of-databases"></a>Nombre minimal de bases de données
Si la somme des DTU des niveaux de performances pour une base de données unique est égale à plus de 1,5 x les eDTU nécessaires pour le pool, un pool élastique est plus rentable. Pour plus d’informations sur les tailles disponibles, consultez l’article [Limites relatives aux eDTU et au stockage pour les pools et bases de données élastiques](sql-database-elastic-pool.md#edtu-and-storage-limits-for-elastic-pools).

***Exemple***<br>
Au moins deux bases de données S3 ou au moins 15 bases de données S0 sont nécessaires pour qu’un pool de 100 eDTU soit plus rentable que l’utilisation de niveaux de performances pour des bases de données uniques.

### <a name="maximum-number-of-concurrently-peaking-databases"></a>Nombre maximal de bases de données connaissant un pic simultané
En partageant les eDTU, toutes les bases de données d’un pool ne peuvent pas utiliser simultanément les eDTU jusqu'à la limite disponible lors de l'utilisation de niveaux de performances pour les bases de données uniques. Plus le nombre de bases de données connaissant un pic simultané est faible, plus le nombre d’eDTU du pool peut être revu à la baisse et plus le pool devient rentable. En général, pas plus de 2/3 (ou 67 %) des bases de données du pool doivent connaître un pic simultané à leur limite d’eDTU.

***Exemple***<br>
Pour réduire les coûts pour trois bases de données S3 dans un pool de 200 eDTU, au moins deux de ces bases de données peuvent connaître un pic simultané au niveau de leur utilisation. Sinon, si plus de deux de ces quatre bases de données S3 connaissent un pic simultané, le pool devra être redimensionné à plus de 200 eDTU. Si le pool est redimensionné à plus de 200 eDTU, vous devez ajouter plusieurs bases de données S3 au pool pour maintenir des coûts inférieurs aux niveaux de performances pour les bases de données uniques.

Notez que cet exemple ne tient pas compte de l'utilisation des autres bases de données dans le pool. Si toutes les bases de données connaissent une utilisation à un moment donné, moins de 2/3 (ou 67 %) des bases de données peuvent connaître un pic simultané.

### <a name="dtu-utilization-per-database"></a>Utilisation de DTU par base de données
Une différence importante entre le pic d’utilisation et l'utilisation moyenne d’une base de données indique de longues périodes de faible utilisation et de courtes périodes d'utilisation intensive. Ce modèle d'utilisation est idéal pour partager des ressources entre les bases de données. Une base de données doit être envisagée pour un pool quand son pic d’utilisation est environ 1,5 fois supérieur à son utilisation moyenne.

***Exemple***<br>
Une base de données S3 qui culmine à 100 DTU et qui utilise en moyenne 67 DTU ou moins est un bon candidat pour le partage des eDTU dans un pool. Sinon, une base de données S1 qui culmine à 20 DTU et qui utilise en moyenne 13 DTU ou moins est un bon candidat à un pool.

## <a name="sizing-an-elastic-pool"></a>Dimensionnement d’un pool élastique
La taille optimale pour un pool dépend du nombre global d’eDTU et des ressources de stockage nécessaires pour toutes les bases de données du pool. Cela implique de déterminer la plus grande des deux quantités suivantes :

* Nombre maximal de DTU que se partagent toutes les bases de données du pool.
* Nombre maximal d’octets de stockage que se partagent toutes les bases de données du pool.

Pour plus d’informations sur les tailles disponibles, consultez l’article [Limites relatives aux eDTU et au stockage pour les pools et bases de données élastiques](sql-database-elastic-pool.md#edtu-and-storage-limits-for-elastic-pools).

La base de données SQL évalue automatiquement l’historique d’utilisation en ressources des bases de données dans un serveur de base de données SQL existant et recommande la configuration de pool appropriée dans le portail Azure. En plus des recommandations, une expérience intégrée estime l’utilisation d’eDTU pour un groupe personnalisé de bases de données sur le serveur. Cela vous permet de faire une analyse de scénarios en ajoutant de façon interactive des bases de données au pool et en les supprimant pour obtenir l’analyse de l’utilisation des ressources et des conseils de dimensionnement avant de valider vos modifications. Pour afficher une procédure, consultez [Surveiller, gérer et dimensionner un pool élastique](sql-database-elastic-pool-manage-portal.md).

Dans les cas où vous ne pouvez pas utiliser les outils, la procédure pas à pas suivante peut vous aider à estimer si un pool est plus économique que les bases de données uniques :

1. Estimez les eDTU nécessaires pour le pool comme suit :

   MAX(<*nombre total de bases de données* X *utilisation DTU moyenne par base de données*>,<br>
   <*Nombre de bases de données connaissant un pic simultané* X *utilisation DTU maximale par base de données*)
2. Estimez l’espace de stockage nécessaire pour le pool en ajoutant le nombre d’octets nécessaires pour toutes les bases de données du pool. Déterminez ensuite la taille du pool d’eDTU qui fournit cette quantité de stockage. Pour connaître les limites de stockage du pool en fonction de la taille du pool d’eDTU, consultez l’article [Limites relatives aux eDTU et au stockage pour les pools et bases de données élastiques](sql-database-elastic-pool.md#edtu-and-storage-limits-for-elastic-pools).
3. Prenez la plus grande des estimations d’eDTU de l’étape 1 et de l’étape 2.
4. Consultez la [page de tarification de la base de données SQL](https://azure.microsoft.com/pricing/details/sql-database/) et recherchez la plus petite taille du pool d’eDTU qui est supérieure à l’estimation de l’étape 3.
5. Comparez le prix du pool trouvé à l’étape 5 à celui de l’utilisation des niveaux de performances appropriés pour les bases de données uniques.

## <a name="edtu-and-storage-limits-for-elastic-pools"></a>Limites relatives aux eDTU et au stockage pour les pools élastiques

Les tableaux suivants décrivent les limites de ressources imposées aux pools élastiques.  Notez que les limites de ressources des bases de données individuelles dans les pools élastiques sont généralement identiques à celles des bases de données uniques situées hors des pools. Elles dépendent du nombre de DTU et du niveau de service.  Par exemple, le nombre maximal d’ouvriers simultanés dans une base de données S2 est de 120.  Par conséquent, le nombre maximal d’ouvriers simultanés d’une base de données dans un pool Standard est également de 120 si le nombre maximal de DTU par base de données dans le pool est de 50 (soit l’équivalent de S2).

[!INCLUDE [SQL DB service tiers table for elastic pools](../../includes/sql-database-service-tiers-table-elastic-pools.md)]

Si toutes les DTU d’un pool élastique sont utilisées, chaque base de données du pool reçoit une quantité égale de ressources pour traiter les requêtes.  Le service de base de données SQL offre un partage équitable des ressources entre les bases de données, garantissant des tranches de temps de calcul égales. Le partage équitable des ressources du pool élastique s’ajoute à n’importe quelle quantité de ressources garantie pour chaque base de données lorsque le nombre minimal de DTU par base de données est défini sur une valeur différente de zéro.

## <a name="database-properties-for-pooled-databases"></a>Propriétés de base de données pour les bases de données mises en pool

Le tableau suivant décrit les propriétés des bases de données mises en pool.

| Propriété | Description |
|:--- |:--- |
| Nombre maximal d’eDTU par base de données |Nombre maximal d’eDTU pouvant être utilisées par une des bases de données du pool en fonction du nombre d’eDTU utilisées par les autres bases de données du pool.  Le nombre maximal d’eDTU par base de données n’est pas une garantie concernant l’octroi des ressources pour une base de données.  Il s’agit d’un paramètre global qui s’applique à toutes les bases de données du pool. Définissez un nombre maximal d’eDTU par base de données suffisamment élevé pour gérer les pics d’utilisation des bases de données. Une certaine allocation excessive est attendue dans la mesure où le pool prend généralement en compte des modèles de creux et de pics d’utilisation des bases de données dans lesquels toutes les bases de données ne connaissent pas simultanément des pics d’utilisation. Par exemple, supposons que le pic d’utilisation par base de données est de 20 eDTU et que seules 20 % des 100 bases de données du pool connaissent simultanément un pic d’utilisation.  Si le nombre maximal d’eDTU par base de données est défini sur 20 eDTU, vous pouvez envisager une allocation 5 fois plus élevée du pool et définir le nombre d’eDTU par pool sur 400. |
| Nombre minimal d’eDTU par base de données |Nombre minimal d’eDTU garanti pour chaque base de données du pool.  Il s’agit d’un paramètre global qui s’applique à toutes les bases de données du pool. Le nombre minimal d’eDTU par base de données peut être défini sur 0, qui est également la valeur par défaut. Cette propriété est définie sur une valeur comprise entre 0 et le nombre moyen d’eDTU utilisées par base de données. Le produit du nombre de bases de données du pool et du nombre minimal d’eDTU par base de données ne peut pas dépasser le nombre d’eDTU par pool.  Par exemple, si un pool comporte 20 bases de données et que le nombre minimal d’eDTU par base de données est défini sur 10 eDTU, le nombre d’eDTU par pool doit être d’au moins 200 eDTU. |
| Espace de stockage de données maximal par base de données |Espace de stockage maximal pour une base de données du pool. Les bases de données regroupées se partagent l’espace de stockage du pool. Par conséquent, le stockage de base de données est limité au stockage de pool minimal restant, dans la limite du stockage maximal par base de données. Le stockage maximal par base de données fait référence à la taille maximale des fichiers de données et n’inclut pas l’espace utilisé par les fichiers journaux. |
|||

## <a name="elastic-jobs"></a>Tâches élastiques
Un pool simplifie les tâches de gestion grâce à l’exécution des scripts dans des **[tâches élastiques](sql-database-elastic-jobs-overview.md)**. Un travail élastique élimine pratiquement le caractère fastidieux d’un nombre élevé de bases de données. Pour commencer, consultez l’article [Prise en main de Tâches de bases de données élastiques](sql-database-elastic-jobs-getting-started.md).

Pour en savoir plus sur les autres outils de base de données permettant d’utiliser plusieurs bases de données, consultez l’article [Montée en charge avec la base de données SQL Azure](sql-database-elastic-scale-introduction.md).

## <a name="business-continuity-features-for-databases-in-a-pool"></a>Fonctionnalités de continuité des activités pour les bases de données d’un pool
Les bases de données regroupées prennent généralement en charge les mêmes [fonctionnalités de continuité d’activité](sql-database-business-continuity.md) que celles disponibles pour les bases de données uniques.

### <a name="point-in-time-restore"></a>Restauration dans le temps
La restauration dans le temps utilise les sauvegardes automatiques de base de données pour récupérer une base de données d’un pool à un moment précis dans le temps. Voir [Limite de restauration dans le temps](sql-database-recovery-using-backups.md#point-in-time-restore)

### <a name="geo-restore"></a>Géo-restauration
La géorestauration constitue l’option de récupération par défaut lorsque la base de données est indisponible en raison d’un incident survenu dans la région où elle est hébergée. Voir [Restaurer une base de données SQL Azure ou basculer vers une base de données secondaire](sql-database-disaster-recovery.md)

### <a name="active-geo-replication"></a>Géoréplication active
Pour les applications qui ont des exigences de récupération plus agressives que ce qu’offre la géorestauration, configurez la [géoréplication active](sql-database-geo-replication-overview.md).

## <a name="next-steps"></a>Étapes suivantes

* Vous pouvez créer et gérer un pool élastique en utilisant le [portail Azure](sql-database-elastic-pool-manage-portal.md), [PowerShell](sql-database-elastic-pool-manage-powershell.md), [Transact-SQL](sql-database-elastic-pool-manage-tsql.md), [C#](sql-database-elastic-pool-manage-csharp.md) et l’API REST.
* Vous pouvez aussi regarder la vidéo [Formation vidéo Microsoft Virtual Academy sur les fonctions de bases de données élastiques dans Azure SQL Database](https://mva.microsoft.com/training-courses/elastic-database-capabilities-with-azure-sql-db-16554)
* Pour en savoir plus sur les modèles de conception pour les applications SaaS avec des pools élastiques, voir [Design Patterns for Multi-tenant SaaS Applications with Azure SQL Database](sql-database-design-patterns-multi-tenancy-saas-applications.md)(Modèles de conception pour les applications SaaS mutualisées avec la base de données SQL Azure).

