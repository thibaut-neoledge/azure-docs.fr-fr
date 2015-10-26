<properties 
   pageTitle="Exercices de récupération d'urgence de bases de données SQL" 
   description="Découvrez des conseils et des meilleures pratiques en matière d'utilisation de la base de données Azure SQL pour effectuer des exercices de récupération d'urgence qui vous aideront à maintenir la résistance aux pannes de vos applications métier stratégiques en cas de défaillances." 
   services="sql-database" 
   documentationCenter="" 
   authors="mihaelablendea" 
   manager="jeffreyg" 
   editor="monicar"/>

<tags
   ms.service="sql-database"
   ms.devlang="NA"
   ms.topic="article"
   ms.tgt_pltfrm="NA"
   ms.workload="data-management" 
   ms.date="07/15/2015"
   ms.author="mihaelab"/>

#Exécution d'un exercice de récupération d'urgence

Nous recommandons de valider régulièrement la préparation des applications à la récupération. La vérification du comportement de l'application et des implications en matière de pertes de données et/ou d'interruptions en cas basculement constitue une bonne pratique. Il s'agit également d'une exigence figurant dans la plupart des normes industrielles dans le cadre d'une certification de la continuité des activités.

L'exécution d'un exercice de récupération d'urgence comprend :

- la simulation d'une défaillance des couches de données
- la récupération 
- la validation de l'intégrité des applications après la récupération

Le flux de travail à exécuter peut varier en fonction de votre [conception pour la continuité des activités](sql-database-business-continuity.md). Ci-dessous, vous trouverez une description des meilleures pratiques en matière d'exécution d'un exercice de récupération d'urgence dans le contexte de bases de données Azure SQL.

##Restauration géographique

Pour éviter une perte de données potentielle lors d'un exercice de récupération d'urgence, nous vous recommandons d'effectuer l'exercice à dans un environnement de test en créant une copie de l'environnement de production et en l'utilisant pour vérifier le flux de travail de basculement de l'application.
 
####Simulation d'une défaillance

- Simulez la défaillance en supprimant ou en renommant la base de données source pour provoquer l'échec de la connectivité des applications. De cette manière, vous pouvez valider la détection / l'alerte de défaillances et mesurer le RTO pour la durée de la récupération.

####Récupération

- Effectuez la restauration géographique de la base de données dans un autre serveur comme décrit [ici](sql-database-disaster-recovery.md). 
- Modifiez la configuration de l'application pour établir une connexion aux bases de données récupérées, puis suivez le guide [Finaliser une base de données récupérée](sql-database-recovered-finalize.md) (en anglais) pour terminer la récupération.

####Validation

- Terminez l'exercice de récupération en vérifiant l'intégrité des applications après la récupération (chaînes de connexion, connexions, test des fonctionnalités de base ou autres validations faisant partie de procédures d'approbations d'applications standard).

##Géo-réplication standard

Une base de données protégée à l'aide de la géo-réplication Standard ne peut avoir qu'une seule base de données secondaire non lisible. L'exercice implique l'arrêt forcé de la liaison, après quoi la base de données ne sera pas protégée. En outre, une perte de données n'étant pas à exclure, nous déconseillons aux clients d'effectuer ce test sur des bases de données de production. Au lieu de cela, nous recommandons la création d'une copie de l'environnement de production et son utilisation pour vérifier le flux de travail de basculement de l'application.

####Simulation d'une défaillance

- Simulez des charges de travail sur la base de données primaire. Si la base de données primaire est active au moment de l'arrêt, une perte de données peut se produire, ce qui rend l'exercice plus réaliste.
- Supprimez la base de données primaire ou [forcez l'arrêt](sql-database-disaster-recovery.md) de la liaison dans la base de données secondaire.

####Récupération

- Modifiez la configuration de l'application pour établir une connexion à l'ancienne base de données secondaire en lecture seule qui sera alors accessible pour que l'application puisse l'utiliser en tant que nouvelle base de données primaire. 
- Suivez le guide [Finaliser une base de données récupérée](sql-database-recovered-finalize.md) (en anglais) pour terminer la récupération.

####Validation

- Terminez l'exercice de récupération en vérifiant l'intégrité des applications après la récupération (chaînes de connexion, connexions, test des fonctionnalités de base ou autres validations faisant partie de procédures d'approbations d'applications standard).

##Géo-réplication active

L'exercice de récupération d'urgence est effectué en utilisant un serveur cible parallèle et en y créant un autre ensemble de bases de données secondaires en lecture seule. Une version test de la couche d'application doit être utilisée pour vérifier l'intégrité de l'opération et des données grâce à des tests sur ce serveur après l'arrêt forcé.

####Simulation d'une défaillance

- [Créez une liaison de géo-réplication active](sql-database-business-continuity-design.md) depuis la base de données primaire vers un serveur test secondaire. Si la base de données primaire est active au moment de l'arrêt, une perte de données peut se produire, ce qui rend l'exercice plus réaliste.
- [Exécutez un arrêt forcé](sql-database-disaster-recovery.md) de la liaison dans la base de données secondaire qui se trouve dans le serveur test.

####Récupération

- Modifiez la configuration de l'application pour établir une connexion à l'ancienne base de données en lecture seule qui sera disponible pour des opérations d'écritures après l'arrêt.
- Suivez le guide [Finaliser une base de données récupérée](sql-database-recovered-finalize.md) (en anglais) pour terminer la récupération.

####Validation

- Terminez l'exercice de récupération en vérifiant l'intégrité des applications après la récupération (chaînes de connexion, connexions, test des fonctionnalités de base ou autres validations faisant partie de procédures d'approbations d'applications standard).

<!---HONumber=Oct15_HO3-->