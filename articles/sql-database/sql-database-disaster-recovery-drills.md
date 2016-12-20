---
title: "Exercices de récupération d’urgence de SQL Database | Microsoft Docs"
description: "Découvrez des conseils et des meilleures pratiques en matière d&quot;utilisation de la base de données Azure SQL pour effectuer des exercices de récupération d&quot;urgence qui vous aideront à maintenir la résistance aux pannes de vos applications métier stratégiques en cas de défaillances."
services: sql-database
documentationcenter: 
author: anosov1960
manager: jhubbard
editor: monicar
ms.assetid: b44a269c-fe2a-404f-b013-290030860bd1
ms.service: sql-database
ms.custom: business continuity
ms.devlang: NA
ms.topic: article
ms.tgt_pltfrm: NA
ms.workload: data-management
ms.date: 07/31/2016
ms.author: sstein; sashan
translationtype: Human Translation
ms.sourcegitcommit: d3456b190d78560dc9380d5ecb56e0e82006e2ea
ms.openlocfilehash: 209446ae6b1a4f951bc141d95ff481f2f5ab43b0


---
# <a name="performing-disaster-recovery-drill"></a>Exécution d'un exercice de récupération d'urgence
Nous recommandons de valider régulièrement la préparation des applications à la récupération. La vérification du comportement de l'application et des implications en matière de pertes de données et/ou d'interruptions en cas basculement constitue une bonne pratique. Il s'agit également d'une exigence figurant dans la plupart des normes industrielles dans le cadre d'une certification de la continuité des activités.

L'exécution d'un exercice de récupération d'urgence comprend :

* la simulation d'une défaillance des couches de données
* la récupération
* la validation de l'intégrité des applications après la récupération

Le flux de travail à exécuter peut varier en fonction de la [conception de votre application pour la continuité des activités](sql-database-business-continuity.md). Ci-dessous, vous trouverez une description des meilleures pratiques en matière d'exécution d'un exercice de récupération d'urgence dans le contexte de bases de données Azure SQL.

## <a name="geo-restore"></a>Restauration géographique
Pour éviter une perte de données potentielle lors d'un exercice de récupération d'urgence, nous vous recommandons d'effectuer l'exercice à dans un environnement de test en créant une copie de l'environnement de production et en l'utilisant pour vérifier le flux de travail de basculement de l'application.

#### <a name="outage-simulation"></a>Simulation d'une défaillance
Pour simuler la défaillance, vous pouvez supprimer ou renommer la base de données source. Cela entraîne l'échec de la connexion de l'application.

#### <a name="recovery"></a>Récupérer
* Effectuez la restauration géographique de la base de données dans un autre serveur comme décrit [ici](sql-database-disaster-recovery.md).
* Modifiez la configuration de l'application pour établir une connexion aux bases de données récupérées, puis suivez le guide [Configure a database after recovery](sql-database-disaster-recovery.md) pour terminer la récupération.

#### <a name="validation"></a>Validation
* Terminez l'exercice de récupération en vérifiant l'intégrité des applications après la récupération (chaînes de connexion, connexions, test des fonctionnalités de base ou autres validations faisant partie de procédures d'approbations d'applications standard).

## <a name="geo-replication"></a>Utiliser la géo-réplication
Pour une base de données protégée à l'aide de la géo-réplication, l’exercice implique un basculement planifié vers la base de données secondaire. Le basculement planifié garantit que les bases de données principale et secondaire restent synchronisées lorsque les rôles sont permutés. À la différence du basculement non planifié, cette opération n'implique pas de perte de données, l'exercice peut donc être exécuté dans l'environnement de production.

#### <a name="outage-simulation"></a>Simulation d'une défaillance
Pour simuler la défaillance, vous pouvez désactiver l'application Web ou un ordinateur virtuel connecté à la base de données. Cela entraîne un échec de connectivité pour les clients web.

#### <a name="recovery"></a>Récupérer
* Vérifiez que la configuration de l’application dans la région de récupération d’urgence pointe vers l’ancienne base de données secondaire qui deviendra la nouvelle base de données principale entièrement accessible.
* Exécutez un [basculement planifié](sql-database-geo-replication-failover-powershell.md#initiate-a-planned-failover) pour que la base de données secondaire devienne la nouvelle base de données primaire.
* Suivez le guide [Configure a database after recovery](sql-database-disaster-recovery.md) pour effectuer la restauration.

#### <a name="validation"></a>Validation
* Terminez l'exercice de récupération en vérifiant l'intégrité des applications après la récupération (chaînes de connexion, connexions, test des fonctionnalités de base ou autres validations faisant partie de procédures d'approbations d'applications standard).

## <a name="next-steps"></a>Étapes suivantes
* Pour en savoir plus sur les scénarios de continuité d’activités, consultez [Scénarios de continuité des activités](sql-database-business-continuity.md)
* Pour en savoir plus sur les sauvegardes automatisées d’une base de données SQL Azure, consultez [Sauvegardes automatisées d’une base de données SQL](sql-database-automated-backups.md)
* Pour en savoir plus sur l’utilisation des sauvegardes automatisées pour la récupération, consultez [Restaurer une base de données à partir des sauvegardes initiées par le service](sql-database-recovery-using-backups.md)
* Pour en savoir plus sur les options de récupération plus rapides, consultez [Géo-réplication active](sql-database-geo-replication-overview.md)  



<!--HONumber=Nov16_HO3-->


