---
title: "Sauvegardes SQL Database automatiques ou géoredondantes | Microsoft Docs"
description: "SQL Database crée automatiquement une sauvegarde de base de données locale toutes les cinq minutes et utilise le stockage géoredondant avec accès en lecture pour fournir la géoredondance."
services: sql-database
documentationcenter: 
author: anosov1960
manager: jhubbard
editor: 
ms.assetid: 3ee3d49d-16fa-47cf-a3ab-7b22aa491a8d
ms.service: sql-database
ms.custom: business continuity
ms.devlang: NA
ms.topic: article
ms.tgt_pltfrm: NA
ms.workload: NA
ms.date: 11/02/2016
ms.author: sashan;carlrab;barbkess
translationtype: Human Translation
ms.sourcegitcommit: a5b69625272b96fc9e42b9b7e57b5401351d8d6e
ms.openlocfilehash: 9e58a144be039db609ad1400246ef2de05c1b84d


---
# <a name="learn-about-sql-database-backups"></a>Découvrir les sauvegardes SQL Database
<!------------------
This topic is annotated with TEMPLATE guidelines for FEATURE TOPICS.

Metadata guidelines

pageTitle
    60 characters or less. Includes name of the feature - primary benefit. Not the same as H1. Its 60 characters or fewer including all characters between the quotes and the Microsoft Azure site identifier.

description
    115-145 characters. Duplicate of the first sentence in the introduction. This is the abstract of the article that displays under the title when searching in Bing or Google. 

    Example: "SQL Database automatically creates a local database backup every few minutes and uses Azure read-access geo-redundant storage for geo-redundancy."

TEMPLATE GUIDELINES for feature topics

The Feature Topic is a one-pager (ok, sometimes longer) that explains a capability of the product or service. It explains what the capability is and characteristics of the capability.  

It is a "learning" topic, not an action topic.

DO explain this:
    • Definition of the feature terminology.  i.e., What is a database backup?
    • Characteristics and capabilities of the feature. (How the feature works)
    • Common uses with links to overview topics that recommend when to use the feature.
    • Reference specifications (Limitations and Restrictions, Permissions, General Remarks, etc.)
    • Next Steps with links to related overviews, features, and tasks.

DON'T explain this:
    • How to steps for using the feature (Tasks)
    • How to solve business problems that incorporate the feature (Overviews)

GUIDELINES for the H1 

    The H1 should answer the question "What is in this topic?" Write the H1 heading in conversational language and use search key words as much as possible. Since this is a learning topic, make sure the title indicates that and doesn't mislead people to think this will tell them how to do tasks.  

    To help people understand this is a learning topic and not an action topic, start the title with "Learn about ... "

    Heading must use an industry standard term. If your feature is a proprietary name like "Elastic database pools", use a synonym. For example:    "Learn about elastic database pools for multi-tenant databases". In this case multi-tenant database is the industry-standard term that will be an anchor for finding the topic.

GUIDELINES for introduction

    The introduction is 1-2 sentences.  It is optimized for search and sets proper expectations about what to expect in the article. It should contain the top key words that you are using throughout the article.The introduction should be brief and to the point of what the feature is, what it is used for, and what's in the article. 

    If the introduction is short enough, your article can pop to the top in Google Instant Answers.

    In this example:

Sentence #1 Explains what the article will cover, which is what the feature is or does. This is also the metadata description. 
    SQL Database automatically creates a database backup every five minutes and uses Azure read-access geo-redundant storage (RA-GRS) to provide geo-redundancy. 

Sentence #2 Explains why I should care about this.  
    Database backups are an essential part of any business continuity and disaster recovery strategy because they protect your data from accidental corruption or deletion.

-------------------->

SQL Database crée automatiquement des sauvegardes de base de données et utilise le stockage géoredondant avec accès en lecture (RA-GRS) d’Azure pour fournir la géoredondance. Ces sauvegardes sont créées automatiquement et sans frais supplémentaires. Vous n’avez pas besoin de faire quoi que ce soit pour qu’elles se produisent. Les sauvegardes de base de données sont une partie essentielle de toute stratégie de continuité d’activité ou de récupération d’urgence, dans la mesure où elles protègent vos données des corruptions et des suppressions accidentelles. Si vous souhaitez conserver les sauvegardes dans votre propre conteneur de stockage, vous pouvez configurer une stratégie de rétention de sauvegarde à long terme. Pour plus d’informations, consultez [Rétention à long terme](sql-database-long-term-retention.md).

<!-- This image needs work, so not putting it in right now.

This diagram shows SQL Database running in the US East region. It creates a database backup every five minutes, which it stores locally to Azure Read Access Geo-redundant Storage (RA-GRS). Azure uses geo-replication to copy the database backups to a paired data center in the US West region.

![geo-restore](./media/sql-database-geo-restore/geo-restore-1.png)

-->

<!---------------
GUIDELINES for the first ## H2.

    The first ## describes what the feature encompasses and how it is used. It points to related task articles.

    For consistency, being the heading with "What is ... "
----------------->

## <a name="what-is-a-sql-database-backup"></a>Qu’est-ce qu’une sauvegarde SQL Database ?
<!-- 
    Explains what a SQL Database backup is and answers an important question that people want to know.
-->


<!----------------- 
    Explains first component of the backup feature
------------------>

SQL Database utilise la technologie SQL Server pour créer des sauvegardes [complètes](https://msdn.microsoft.com/library/ms186289.aspx), [différentielles](https://msdn.microsoft.com/library/ms175526.aspx) et du [journal des transactions](https://msdn.microsoft.com/library/ms191429.aspx). Les sauvegardes du journal des transactions se produisent, en général, toutes les 5 à 10 minutes. La fréquence varie selon le niveau de performance et l’activité de la base de données. Les sauvegardes du journal des transactions, avec les sauvegardes complètes et différentielles, vous permettent de restaurer une base de données à un point spécifique sur le même serveur qui héberge la base de données. Quand vous restaurez une base de données, le service identifie les sauvegardes nécessitant une restauration (complète, différentielle ou journal des transactions).

<!--------------- 
    Explicit list of what to do with a local backup. "Use a ..." helps people to scan the topic and find the uses quickly.
---------------->

Vous pouvez utiliser ces sauvegardes aux fins suivantes :

* Restaure une base de données à un point dans le temps dans la période de rétention. Cette opération crée une base de données dans le même serveur que la base de données d’origine.
* Restaurer une base de données supprimée sur le moment où elle été supprimée ou tout moment pendant la période de rétention. La base de données supprimée ne peut être restaurée que sur le même serveur où la base de données d’origine a été créée.
* Restaurer une base de données dans une autre région géographique. Ceci vous permet de procéder à la récupération après un sinistre géographique lorsque vous ne pouvez pas accéder à votre serveur, ni à la base de données. Cette opération crée une base de données sur n’importe quel serveur existant dans le monde entier. 
* Restaurer une base de données à partir d’une sauvegarde spécifique stockée dans le coffre Azure Recovery Services. Ceci vous permet de restaurer une ancienne version de la base de données pour répondre à une demande de conformité ou exécuter une ancienne version de l’application. Consultez [Rétention à long terme](sql-database-long-term-retention.md).
* Pour effectuer une restauration, consultez [Restauration de la base de données à partir de la sauvegarde](sql-database-recovery-using-backups.md).

<!----------------- 
    Explains first component of the backup feature
------------------>

<!--------------- 
    Explicit list of what to do with a geo-redundant backup. "Use a ..." helps people to scan the topic and find the uses quickly.
---------------->

> [!NOTE]
> Dans le stockage Azure, le terme *réplication* fait référence à la copie de fichier d’un emplacement à un autre. La *réplication de base de données* de SQL fait référence à la gestion de la synchronisation de plusieurs bases de données secondaires avec une base de données primaire. 
> 
> 

<!----------------
    The next ## H2's discuss key characteristics of how the feature works. The title is in conversational language and asks the question that will be answered.
------------------->
## <a name="how-much-backup-storage-is-included-at-no-cost"></a>Quelle est la quantité de stockage de sauvegarde incluse sans frais ?
SQL Database fournit jusqu’à 200 % du stockage de base de données maximal configuré pour le stockage de sauvegarde sans coût supplémentaire. Par exemple, si vous avez une instance de base de données Standard configurée à une taille de 250 Go, vous bénéficiez de 500 Go d’espace de stockage de sauvegarde sans coût supplémentaire. Si votre base de données dépassé la capacité fournie de stockage de sauvegarde, vous pouvez décider de réduire la période de rétention, en contactant le support Azure. Vous pouvez également décider de payer le stockage de sauvegarde supplémentaire, qui est facturé au taux standard de stockage redondant avec accès en lecture. 

## <a name="how-often-do-backups-happen"></a>À quelle fréquence les sauvegardes se produisent-elles ?
Les sauvegardes complètes de base de données sont effectuées chaque semaine, les sauvegardes différentielles de base de données sont, en général, effectuées à quelques heures d’intervalle et les sauvegardes du journal des transactions sont effectuées toutes les 5 à 10 minutes. La première sauvegarde complète est planifiée immédiatement après la création d’une base de données. Elle s’exécute généralement en 30 minutes, mais elle peut nécessiter davantage de temps s’il s’agit d’une base de données de taille considérable. Par exemple, la sauvegarde initiale peut prendre davantage de temps sur une base de données restaurée ou une copie de base de donnée. Après la première sauvegarde complète, toutes les sauvegardes sont planifiées automatiquement et gérées en mode silencieux en arrière-plan. Le moment exact de toutes les sauvegardes de base de données est déterminé par le service SQL Database en fonction de l’équilibrage de la charge de travail globale du système. 

La géoréplication du stockage de sauvegarde se produit en fonction de la planification de réplication de Stockage Azure.

## <a name="how-long-do-you-keep-my-backups"></a>Combien de temps conservez-vous mes sauvegardes ?
Chaque sauvegarde SQL Database a une période de rétention qui est basée sur le [-niveau de service](sql-database-service-tiers.md) de la base de données. Les périodes de rétention pour une base de données sont les suivantes :

<!------------------

    Using a list so the information is easy to find when scanning.
------------------->

* Niveau de service De base : 7 jours.
* Niveau de service Standard : 35 jours.
* Niveau de service Premium : 35 jours.

Si vous rétrogradez votre base de données des niveaux de service Standard ou Premium au niveau De base, les sauvegardes sont conservées sept jours. Toutes les sauvegardes existantes d’une ancienneté supérieure à sept jours ne sont plus disponibles. 

Si vous mettez à niveau votre base de données du niveau De base vers le niveau Standard ou Premium, SQL Database conserve les sauvegardes existantes jusqu’à ce que leur ancienneté soit de 35 jours. Il conserve les nouvelles sauvegardes pendant 35 jours.

Si vous supprimez une base de données, SQL Database conserve les sauvegardes de la même façon que s’il s’agissait d’une base de données en ligne. Par exemple, supposons que vous supprimez une base de données qui a une période de rétention de sept jours. Une sauvegarde dont l’ancienneté est de quatre jours est conservée trois jours de plus.

> [!IMPORTANT]
> Si vous supprimez le serveur Azure SQL Server, toutes les bases de données qui appartiennent au serveur sont également supprimées, sans pouvoir être restaurées. Vous ne pouvez pas restaurer un serveur supprimé.
> 
> 

## <a name="how-to-extend-the-backup-retention-period"></a>Comment étendre la période de rétention de la sauvegarde ?
Si votre application nécessite que les sauvegardes soient disponibles pendant une période de temps plus longue, vous pouvez étendre la période de rétention intégrée en configurant la stratégie de rétention de sauvegarde à long terme pour les bases de données individuelles (stratégie LTR). Cela vous permet d’étendre la période de rétention intégrée de 35 jours à 10 ans. Pour plus d’informations, consultez [Rétention à long terme](sql-database-long-term-retention.md).

Une fois que vous ajoutez la stratégie LTR à une base de données à l’aide de l’API ou du portail Azure, les sauvegardes de base de données complètes hebdomadaires sont copiées automatiquement dans votre propre coffre de service Sauvegarde Azure. Si votre base de données est chiffrée à l’aide de TDE, les sauvegardes sont automatiquement chiffrées au repos.  Le coffre des services supprime automatiquement vos sauvegardes ayant expiré en fonction de leur horodatage et de la stratégie LTR.  Vous n’avez pas besoin de gérer la planification des sauvegardes, ni de vous préoccuper du nettoyage des anciens fichiers. L’API de restauration prend en charge les sauvegardes stockées dans le coffre tant que celui-ci se trouve dans le même abonnement que votre base de données SQL. Vous pouvez utiliser le portail ou PowerShell pour accéder à ces sauvegardes.

<!-------------------
OPTIONAL section
## Best practices 
--------------------->

<!-------------------
OPTIONAL section
## General remarks
--------------------->

<!-------------------
OPTIONAL section
## Limitations and restrictions
--------------------->

<!-------------------
OPTIONAL section
## Metadata
--------------------->

<!-------------------
OPTIONAL section
## Performance
--------------------->

<!-------------------
OPTIONAL section
## Permissions
--------------------->

<!-------------------
OPTIONAL section
## Security
--------------------->

<!-------------------
GUIDELINES for Next Steps

    The last section is Next Steps. Give a next step that would be relevant to the customer after they have learned about the feature and the tasks associated with it.  Perhaps point them to one or two key scenarios that use this feature.

    You don't need to repeat links you have already given them.
--------------------->

## <a name="next-steps"></a>Étapes suivantes
Les sauvegardes de base de données sont une partie essentielle de toute stratégie de continuité d’activité ou de récupération d’urgence, dans la mesure où elles protègent vos données des corruptions et des suppressions accidentelles. Pour en savoir plus sur les autres solutions de continuité des activités Azure SQL Database, consultez [Vue d’ensemble de la continuité des activités](sql-database-business-continuity.md).




<!--HONumber=Nov16_HO5-->


