---
title: "Exercices de récupération d’urgence de SQL Database | Microsoft Docs"
description: "Découvrez les conseils et meilleures pratiques pour l’utilisation d’Azure SQL Database pour effectuer des exercices de récupération d’urgence."
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
ms.workload: Inactive
ms.date: 07/31/2016
ms.author: sashan
ms.openlocfilehash: 8e395153fc9907107156c3412e5e0de554c83750
ms.sourcegitcommit: e5355615d11d69fc8d3101ca97067b3ebb3a45ef
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 10/31/2017
---
# <a name="performing-disaster-recovery-drill"></a>Exécution d'un exercice de récupération d'urgence
Nous recommandons de valider régulièrement la préparation des applications à la récupération. La vérification du comportement de l'application et des implications en matière de pertes de données et/ou d'interruptions en cas basculement constitue une bonne pratique. Il s'agit également d'une exigence figurant dans la plupart des normes industrielles dans le cadre d'une certification de la continuité des activités.

L'exécution d'un exercice de récupération d'urgence comprend :

* la simulation d'une défaillance des couches de données
* la récupération
* la validation de l'intégrité des applications après la récupération

Le flux de travail à exécuter peut varier en fonction de la [conception de votre application pour la continuité des activités](sql-database-business-continuity.md). Ci-dessous, vous trouverez une description des meilleures pratiques en matière d'exécution d'un exercice de récupération d'urgence dans le contexte de bases de données Azure SQL.

## <a name="geo-restore"></a>Géo-restauration
Pour éviter une perte de données potentielle lors d'un exercice de récupération d'urgence, nous vous recommandons d'effectuer l'exercice à dans un environnement de test en créant une copie de l'environnement de production et en l'utilisant pour vérifier le flux de travail de basculement de l'application.

#### <a name="outage-simulation"></a>Simulation d'une défaillance
Pour simuler la défaillance, vous pouvez supprimer ou renommer la base de données source. Cela entraîne des échecs de connexion de l’application.

#### <a name="recovery"></a>Récupérer
* Effectuez la géorestauration de la base de données sur un autre serveur, comme décrit [ici](sql-database-disaster-recovery.md).
* Modifiez la configuration de l’application pour établir une connexion aux bases de données récupérées, puis suivez le guide [Configurer une base de données après récupération](sql-database-disaster-recovery.md) pour achever la récupération.

#### <a name="validation"></a>Validation
* Achevez l’exercice de récupération en vérifiant l’intégrité des applications après la récupération (notamment, chaînes de connexion, connexions, test des fonctionnalités de base ou autres validations faisant partie de procédures d’approbations d’applications standard).

## <a name="geo-replication"></a>Géoréplication
Pour une base de données protégée à l’aide de la géoréplication, l’exercice implique un basculement planifié vers la base de données secondaire. Le basculement planifié garantit que les bases de données principale et secondaire restent synchronisées lorsque les rôles sont permutés. À la différence du basculement non planifié, cette opération n’entraîne pas de perte de données. L’exercice peut donc être exécuté dans l’environnement de production.

#### <a name="outage-simulation"></a>Simulation d'une défaillance
Pour simuler la défaillance, vous pouvez désactiver l’application web ou une machine virtuelle connectée à la base de données. Cela entraîne des échecs de connectivité des clients web.

#### <a name="recovery"></a>Récupérer
* Vérifiez que la configuration de l’application dans la région de récupération d’urgence pointe vers l’ancienne base de données secondaire qui devient la nouvelle base de données primaire entièrement accessible.
* Exécutez un [basculement planifié](scripts/sql-database-setup-geodr-and-failover-database-powershell.md) pour que la base de données secondaire devienne la nouvelle base de données primaire.
* Suivez le guide [Configure a database after recovery](sql-database-disaster-recovery.md) pour effectuer la restauration.

#### <a name="validation"></a>Validation
* Achevez l’exercice de récupération en vérifiant l’intégrité des applications après la récupération (notamment, chaînes de connexion, connexions, test des fonctionnalités de base ou autres validations faisant partie de procédures d’approbations d’applications standard).

## <a name="next-steps"></a>Étapes suivantes
* Pour en savoir plus sur les scénarios de continuité d’activités, consultez [Scénarios de continuité des activités](sql-database-business-continuity.md)
* Pour en savoir plus sur les sauvegardes automatisées d’une base de données SQL Azure, consultez [Sauvegardes automatisées d’une base de données SQL](sql-database-automated-backups.md)
* Pour en savoir plus sur l’utilisation des sauvegardes automatisées pour la récupération, consultez [Restaurer une base de données à partir des sauvegardes initiées par le service](sql-database-recovery-using-backups.md)
* Pour découvrir des options de récupération plus rapides, voir [Géoréplication active](sql-database-geo-replication-overview.md).  
