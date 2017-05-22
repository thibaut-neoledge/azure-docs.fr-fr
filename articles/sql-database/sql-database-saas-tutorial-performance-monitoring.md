---
title: "Surveiller les performances d’une application SaaS SQL Database | Microsoft Docs"
description: "Surveiller et gérer les performances de l’exemple d’application WTP Azure SQL Database"
keywords: "didacticiel sur les bases de données SQL"
services: sql-database
documentationcenter: 
author: stevestein
manager: jhubbard
editor: 
ms.assetid: 
ms.service: sql-database
ms.custom: tutorial
ms.workload: data-management
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: hero-article
ms.date: 05/10/2017
ms.author: billgib; sstein
ms.translationtype: Human Translation
ms.sourcegitcommit: fc4172b27b93a49c613eb915252895e845b96892
ms.openlocfilehash: af9511978718af10c97bee6af3a2835c9d2c1ff4
ms.contentlocale: fr-fr
ms.lasthandoff: 05/12/2017


---
# <a name="monitor-performance-of-the-wtp-sample-saas-application"></a>Surveiller les performances de l’exemple d’application SaaS WTP

Ce tutoriel illustre les fonctionnalités intégrées de surveillance et de gestion d’alertes de SQL Database et des pools élastiques. Il présente ensuite plusieurs scénarios clés de gestion des performances utilisés dans les applications SaaS.

L’application Wingtip Tickets utilise un modèle de données par locataire, où chaque lieu (locataire) possède sa propre base de données. Comme de nombreuses applications SaaS, le modèle de charge de travail de locataire anticipé est imprévisible et sporadique. En d’autres termes, les ventes de tickets peuvent se produire à tout moment. Pour tirer parti de ce modèle d’utilisation typique, les bases de données de locataire sont déployées dans des pools de bases de données élastiques. Les pools élastiques optimisent le coût d’une solution en partageant des ressources entre de nombreuses bases de données. Avec ce type de modèle, il est important de surveiller l’utilisation des ressources des bases de données et des pools pour veiller à ce que les charges soient raisonnablement équilibrées entre les pools. Vous devez également vous assurer que les bases de données ont des ressources appropriées et que les pools n’atteignent pas les limites d’[eDTU](sql-database-what-is-a-dtu.md). Ce tutoriel explore plusieurs moyens de surveiller et de gérer des bases de données et des pools et montre comment prendre des mesures correctives en réponse aux variations de la charge de travail.

Ce tutoriel vous montre comment effectuer les opérations suivantes :

> [!div class="checklist"]

> * Simuler l’utilisation sur les bases de données de locataire en exécutant un générateur de charge fourni
> * Surveiller les bases de données de locataire à mesure qu’elles répondent à l’augmentation de la charge
> * Augmenter la taille du pool élastique en réponse à la charge accrue sur les bases de données
> * Approvisionner un deuxième pool élastique pour équilibrer la charge de l’activité des bases de données


Pour suivre ce tutoriel, vérifiez que les conditions préalables suivantes sont bien satisfaites :

* L’application WTP est déployée. Pour la déployer en moins de cinq minutes, consultez [Déployer et découvrir l’application SaaS WTP](sql-database-saas-tutorial.md).
* Azure PowerShell est installé. Pour plus d’informations, consultez [Bien démarrer avec Azure PowerShell](https://docs.microsoft.com/powershell/azure/get-started-azureps).

## <a name="introduction-to-saas-performance-management-patterns"></a>Introduction aux modèles de gestion des performances SaaS

La gestion des performances des bases de données se compose des opérations suivantes : compilation et analyse des données de performances, puis réaction à ces données en ajustant les paramètres afin de conserver un temps de réponse acceptable pour votre application. Lorsque vous hébergez plusieurs locataires, les pools de bases de données élastiques sont un moyen économique pour fournir et gérer des ressources d’un groupe de bases de données avec des charges de travail imprévisibles. Avec certains modèles de charge de travail, il peut être avantageux de gérer ne serait-ce que 2 bases de données S3 dans un pool. Un pool partage le coût des ressources, mais il peut également éliminer la nécessité de constamment surveiller et suivre les bases de données individuelles.

![Médias](./media/sql-database-saas-tutorial-performance-monitoring/app-diagram.png)

Les pools et les bases de données qu’ils incluent doivent toujours être surveillés pour vérifier qu’ils restent dans les limites de performances acceptables. Paramétrez la configuration du pool pour répondre aux besoins de la charge de travail globale, en faisant en sorte que les eDTU du pool soient adaptés à la charge de travail globale. Ajustez les valeurs d’eDTU minimales et maximales par base de données en fonction des besoins spécifiques de votre application.

### <a name="performance-management-strategies"></a>Stratégies de gestion des performances

* Pour éviter de devoir analyser manuellement les performances, il est plus efficace de **définir des alertes qui se déclenchent si les bases de données ou les pools s’éloignent des limites normales**.
* Pour répondre aux fluctuations à court terme du niveau de performances agrégé d’un pool, le **niveau eDTU du pool peut augmenté ou diminué**. Si cette fluctuation est régulière ou prévisible, la **mise à l’échelle du pool peut être planifiée automatiquement**. Par exemple, diminuez la taille du pool lorsque vous savez que votre charge de travail est faible, disons la nuit ou le week-end.
* Pour répondre aux fluctuations à plus long terme ou à la modification du nombre de bases de données, **des bases de données spécifiques peuvent être déplacées dans d’autres pools**.
* Pour répondre aux augmentations à court terme de la charge de bases de données *spécifiques*, **vous pouvez sortir celles-ci du pool et leur attribuer un niveau de performances spécifique** pendant une période donnée. Une fois que la charge est réduite, la base de données peut être remise dans le pool. Si cela est connu à l’avance, la base de données peut être déplacée préalablement pour vérifier qu’elle a toujours les ressources nécessaires et pour éviter l’impact sur les autres bases de données du pool. Si ce besoin est prévisible, par exemple un lieu accueillant un événement populaire avec une vente de tickets à grande échelle, ce comportement de gestion peut être intégré à l’application.

Le [portail Azure](https://portal.azure.com) offre des fonctionnalités intégrées de surveillance et d’alerte sur la plupart des ressources. Pour SQL Database, la surveillance et les alertes sont disponibles pour les bases de données et les pools. Ces fonctionnalités de surveillance et d’alertes intégrées sont propres à la ressource. Par conséquent, il est pratique de les utiliser pour un petit nombre de ressources, mais pas pour de nombreuses ressources.

Pour les scénarios impliquant de grands volumes, il est possible d’utiliser Log Analytics (également appelé OMS). Il s’agit d’un service Azure distinct offrant l’analytique des journaux de diagnostic et des données de télémétrie rassemblés dans un espace de travail Log Analytics qui permet de collecter de telles données de télémétrie provenant de nombreux services, d’exécuter des requêtes et de définir des alertes.

## <a name="get-the-wingtip-application-scripts"></a>Obtenir les scripts d’application Wingtip

Les scripts et le code source de l’application Wingtip Tickets sont disponibles dans le référentiel GitHub [WingtipSaaS](https://github.com/Microsoft/WingtipSaaS). Les fichiers de script se trouvent dans le [dossier Learning Modules](https://github.com/Microsoft/WingtipSaaS/tree/master/Learning%20Modules). Téléchargez le dossier **Learning Modules** en local sur votre ordinateur, tout en conservant l’arborescence.

## <a name="provision-additional-tenants"></a>Approvisionner des locataires supplémentaires

Bien que les pools puissent être économiques avec seulement deux bases de données S3, plus le nombre de bases de données du pool est élevé, plus les économies sont notables. Pour une bonne compréhension du fonctionnement de la gestion et de la surveillance des performances à grande échelle, ce tutoriel requiert le déploiement d’au moins 20 bases de données.

Si vous avez déjà configuré un lot de locataires dans un tutoriel précédent, vous pouvez passer à la section [Simuler l’utilisation sur toutes les bases de données de locataire](#simulate-usage-on-all-tenant-databases).

1. Ouvrez le fichier …\\Learning Modules\\Provision and Catalog\\*Demo-PerformanceMonitoringAndManagement.ps1* dans l’**ISE PowerShell**. Gardez ce script ouvert, car vous exécuterez plusieurs scénarios au cours de ce tutoriel.
1. Définissez **$DemoScenario** = **1**, **Approvisionner un lot de locataires**
1. Appuyez sur **F5** pour exécuter le script.

Le script déploie 17 locataires en moins de cinq minutes.

Le script *New-TenantBatch* utilise un ensemble imbriqué ou lié de modèles [Resource Manager](../azure-resource-manager/index.md) qui créent un lot de locataires, ce qui copie par défaut la base de données **baseTenantDb** sur le serveur de catalogue pour créer de nouvelles bases de données de locataire, puis inscrit celles-ci dans le catalogue, avant de les initialiser avec le type de lieu et le nom du locataire. Ce comportement est cohérent avec la manière dont l’application WTP approvisionne un nouveau locataire. Les modifications apportées à *baseTenantDB* sont appliquées aux nouveaux locataires approvisionnés par la suite. Consultez le [tutoriel de gestion de schéma](sql-database-saas-tutorial-schema-management.md) pour voir comment apporter des modifications de schéma à des bases de données de locataire *existantes*, y compris la base de données *or* (golden).

## <a name="simulate-different-usage-patterns-by-generating-different-load-types"></a>Simuler différents modèles d’utilisation en générant différents types de charge

Le script *Demo-PerformanceMonitoringAndManagement.ps1* démarre le générateur de charge à l’aide d’un des *types de charge* disponibles :

| Démonstration | Scénario |
|:--|:--|
| 2 | Générer une charge d’intensité normale (environ 40 DTU) |
| 3 | Générer une charge avec des pics plus longs et plus fréquents par base de données|
| 4 | Générer une charge avec des pics de DTU supérieurs par base de données (environ 80 DTU)|
| 5 | Générer une charge normale et une charge élevée sur un seul locataire (environ 95 DTU)|
| 6 | Générer une charge déséquilibrée entre plusieurs pools|

## <a name="simulate-usage-on-all-tenant-databases"></a>Simuler l’utilisation sur toutes les bases de données de locataire

Le générateur de charge applique une charge CPU *synthétique* à chaque base de données de locataire. Le générateur démarre un travail pour chaque base de données de locataire, qui appelle périodiquement une procédure stockée qui génère la charge. Les niveaux de charge (exprimés en eDTU), la durée et les intervalles varient selon les bases de données afin de simuler l’activité d’un locataire imprévisible.

1. Définissez **$DemoScenario** = **2**, *Générer une charge d’intensité normale*.
1. Appuyez sur **F5** pour appliquer une charge à toutes vos bases de données de locataire.

En raison de la nature sporadique de la charge, laissez le générateur s’exécuter pendant 10 à 20 minutes afin que l’activité puisse atteindre un état stable et proposer un modèle intéressant.

> [!IMPORTANT]
> Le générateur de charge exécute une série de travaux dans votre session PowerShell locale. Laissez le fichier *Demo-PerformanceMonitoringAndManagement.ps1* ouvert. Si vous fermez l’onglet ou que vous interrompez votre ordinateur, le générateur de charge s’arrête.

## <a name="monitor-resource-usage-using-the-portal"></a>Surveiller l’utilisation des ressources dans le portail

Pour surveiller l’utilisation de ressources qui résulte de la charge appliquée, dans le portail, ouvrez le pool contenant les bases de données de locataire.

1. Ouvrez le [portail Azure](https://portal.azure.com) et accédez au serveur tenants1-&lt;USER&gt;.
1. Vous devez voir la liste des bases de données de locataire, y compris le nouveau lot de bases de données.
1. Faites défiler vers le bas, recherchez les pools élastiques, puis cliquez sur **Pool1**. Ce pool contient toutes les bases de données de locataire créées jusqu’à présent.
1. Développez le panneau du pool qui s’ouvre et observez le graphique d’utilisation du pool et le graphique d’utilisation des principales bases de données.

L’utilisation du pool est essentiellement l’agrégat de l’utilisation de toutes les bases de données du pool. Le graphique d’utilisation des bases de données montre les 5 principales bases de données :

![](./media/sql-database-saas-tutorial-performance-monitoring/pool1.png)

Comme il existe d’autres bases de données dans le pool en plus des 5 principales, l’utilisation du pool affiche une activité qui n’est pas reflétée dans le graphique des cinq bases de données principales. Pour afficher des informations supplémentaires, cliquez sur **Utilisation des ressources de base de données** :

![](./media/sql-database-saas-tutorial-performance-monitoring/database-utilization.png)


## <a name="set-performance-alerts-on-the-pool"></a>Définir les alertes de performance sur le pool

Définissez une alerte sur le pool qui se déclenche lors d’une \>utilisation à 75 % maintenue pendant 5 minutes, comme suit :

1. Ouvrez *Pool1* (sur le serveur *tenants1-\<user\>*) dans le [portail Azure](https://portal.azure.com).
1. Cliquez sur **Règles d’alerte**, puis sur **+ Ajouter une alerte**.

   ![ajouter une alerte](media/sql-database-saas-tutorial-performance-monitoring/add-alert.png)

1. Entrez un nom, par exemple **Nombre élevé de DTU**, 
1. Définissez les valeurs suivantes :
   * **Métrique = pourcentage eDTU**
   * **Condition = supérieur à**.
   * **Seuil = 75**.
   * **Période = Au cours des 30 dernières minutes**.

   ![définir une alerte](media/sql-database-saas-tutorial-performance-monitoring/alert-rule.png)

Vous pouvez recevoir des notifications sur la messagerie de votre compte Azure et éventuellement d’autres courriers (cette configuration est déconseillée, sauf si vous êtes le propriétaire de l’abonnement utilisé).

> [!NOTE]
> Comme l’alerte se déclenche uniquement si le seuil est dépassé au cours des 30 dernières minutes, le générateur de charge doit être en cours d’exécution pendant plus de 30 minutes pour tester l’alerte.


## <a name="scale-up-a-busy-pool"></a>Augmenter la taille d’un pool occupé

Si le niveau de charge global d’un pool augmente jusqu’à atteindre la limite maximale du pool et l’utilisation de 100 % des eDTU, les performances de chaque base de données sont affectées, ce qui peut ralentir les temps de réponse pour l’ensemble des bases de données du pool.

À court terme, envisagez d’augmenter la taille du pool pour fournir des ressources supplémentaires ou de retirer des bases de données du pool (en les déplaçant vers d’autres pools ou hors du pool vers un niveau de service autonome).

À long terme, envisagez d’optimiser les requêtes ou l’utilisation de l’index pour améliorer les performances des bases de données. En fonction de la sensibilité de l’application aux problèmes de performances, il est recommandé d’augmenter la taille d’un pool avant que celui-ci atteigne l’utilisation d’eDTU maximale. Utilisez une alerte pour vous avertir à l’avance.

Vous pouvez simuler un pool occupé en augmentant la charge produite par le générateur. Demander aux bases de données de fonctionner au maximum plus fréquemment et plus longtemps augmente la charge globale sur le pool sans modifier les besoins des bases de données individuelles. Vous pouvez facilement augmenter la taille du pool dans le portail ou à partir de PowerShell. Cet exercice utilise le portail.

1. Définissez *$DemoScenario* = **3**, _Générer une charge avec des pics plus longs et plus fréquents par base de données_ pour augmenter l’intensité de la charge globale sur le pool sans modifier le pic de charge requis par chaque base de données.
1. Appuyez sur **F5** pour appliquer une charge à toutes vos bases de données de locataire.


1. **Ouvrez le panneau du pool** **tenants1/Pool1**.


1. Surveillez l’augmentation de l’utilisation de DTU du pool sur le graphique du haut. L’affichage de la nouvelle charge supérieure peut prendre quelques minutes, mais vous devez rapidement voir que le pool commence à atteindre une utilisation à 100 % et qu’à mesure que la charge se stabilise dans le nouveau modèle, le pool est rapidement surchargé.


1. Pour augmenter la taille du pool, cliquez sur **Configurer le pool**
1. Réglez le curseur **eDTU du pool** sur 100 (il est recommandé de ne pas choisir une valeur plus élevée pour limiter les coûts). Notez la façon dont le stockage agrégé disponible pour toutes les bases de données du pool, indiqué par **Go du pool**, est lié au paramètre eDTU et augmente également. La modification du nombre d’eDTU du pool ne modifie pas les paramètres de chaque base de données (qui est toujours de 50 eDTU par base de données). Vous pouvez voir les paramètres de chaque base de données sur le côté droit du panneau **Configurer le pool**.
1. Cliquez sur **Enregistrer** pour envoyer la demande. La modification prend généralement de 3 à 5 minutes pour un pool Standard.

Revenez à **Pool1** > **Vue d’ensemble** pour afficher les graphiques de surveillance. Surveillez l’effet produit par la fourniture de davantage de ressources au pool (bien qu’avec quelques bases de données et une charge aléatoire, il n’est pas toujours facile de voir l’effet de façon concluante). Lorsque vous examinez les graphiques, gardez à l’esprit le fait que 100 % sur le graphique du haut représente maintenant 100 eDTU, alors que sur le graphique du bas, 100 % correspond toujours à 50 eDTU, car la valeur maximale par base de données est toujours de 50 eDTU.

La base de données reste en ligne et entièrement disponible tout au long du processus. Au dernier moment, lorsque chaque base de données est prête à être activée avec le nouveau nombre d’eDTU du pool, toutes les connexions actives sont interrompues. Le code d’application doit toujours être écrit de façon à retenter les connexions ignorées et à ce que la reconnexion à la base de données ait lieu dans le pool mis à l’échelle.

## <a name="create-a-second-pool-and-load-balance-databases-to-handle-increased-aggregate-load"></a>Créer un deuxième pool et équilibrer la charge des bases de données pour gérer l’augmentation de la charge agrégée

Comme alternative à la mise à l’échelle du pool, créez un deuxième pool et déplacez-y des bases de données pour équilibrer la charge entre les deux pools. Pour ce faire, le nouveau pool doit être créé sur le même serveur que le premier.

1. Ouvrez le **panneau du serveur customers1-&lt;USER&gt;**. Si vous êtes dans le panneau d’une base de données ou d’un pool, vous pouvez descendre au contrôle Bases et sélectionner le nom du serveur comme raccourci.
1. Cliquez sur **+ Nouveau pool** pour créer un pool sur le serveur actuel.
1. Sur le nouveau modèle de pool de bases de données élastique :

    1. Définissez **Nom = Pool2**.
    1. Laissez le niveau tarifaire **Pool Standard**.
    1. Cliquez sur **Configurer le pool**.
    1. Dans le panneau Configurer le pool qui s’ouvre, définissez **eDTU du pool = 50 DTU**.
    1. Cliquez sur la commande **Ajouter des bases de données** pour afficher la liste des bases de données sur ce serveur qui ne sont pas dans le pool actuel.
    1. Dans la liste, **sélectionnez** la moitié des bases de données (10 sur 20) pour les déplacer vers le nouveau pool, puis cliquez sur **Sélectionner**.
    1. Cliquez à nouveau sur **Sélectionner** pour accepter les modifications de la configuration. Notez l’estimation des coûts pour un mois d’utilisation avec les options sélectionnées.
    1. Sélectionnez **OK** pour créer le nouveau pool avec la nouvelle configuration et pour déplacer les bases de données.

La création du pool et le déplacement des bases de données dans celui-ci prend quelques minutes. Chacune des bases de données déplacées reste en ligne et accessible jusqu’au dernier moment, où toutes les connexions ouvertes sont fermées. Lorsqu’un client retente la connexion, il se connecte à la base de données dans le nouveau pool.

Une fois que le pool a été créé, il apparaît dans le panneau du serveur customers1. Cliquez sur le nom du pool pour ouvrir le panneau associé et surveiller ses performances.

Vous devez voir que l’utilisation des ressources sur Pool1 a diminué et que Pool2 est chargé de la même façon.

## <a name="manage-an-increased-load-on-a-single-database"></a>Gérer une augmentation de charge sur une seule base de données

Si une base de données d’un pool connaît une charge élevée soutenue, selon la configuration du pool, elle peut dominer les ressources du pool et avoir un impact sur d’autres bases de données. Si l’activité est susceptible de continuer pendant un certain temps, la base de données peut être déplacée temporairement hors du pool. Cela permet d’attribuer à la base de données davantage de ressources que les autres bases de données du pool et de l’isoler de ces dernières. Cet exercice simule l’effet de la salle de concert Contoso qui subit une charge élevée quand les tickets sont mis en vente pour un concert populaire.

1. Dans le script …\\**Demo-PerformanceManagementAndMonitoring**.ps1
1. Définissez **$DemoScenario = 5, Générer une charge normale et une charge élevée sur un seul locataire (environ 95 DTU).**
1. Définissez **$SingleTenantDatabaseName = contosoconcerthall**
1. Exécutez le script en appuyant sur **F5**.
1. **Ouvrez le panneau du pool** **Customers1/Pool1**.
1. Examinez l’affichage **Surveillance du pool élastique** en haut du panneau et recherchez l’augmentation de l’utilisation du nombre de DTU du pool. Après une ou deux minutes, la charge plus élevée doit commencer à apparaître et vous devez rapidement voir que le pool a atteint 100 % d’utilisation.
1. Surveillez également l’affichage **Surveillance de la base de données élastique** qui illustre les bases de données les plus actives au cours de la dernière heure. La base de données contosoconcerthall doit apparaître rapidement parmi les 5 bases de données principales.
1. **Cliquez sur le**  **graphique** Surveillance de la base de données élastique pour ouvrir le panneau **Utilisation des ressources de base de données** dans lequel vous pouvez surveiller des bases de données de façon sélective. Cela vous permet d’isoler l’affichage de la base de données contosoconcerthall.
1. Dans la liste des bases de données, **cliquez sur contosoconcerthall** pour ouvrir le panneau associé.
1. Cliquez sur **Niveau tarifaire (mise à l’échelle de DTU)** dans le menu contextuel pour ouvrir le panneau **Configurer les performances** dans lequel vous pouvez définir un niveau de performances isolé pour la base de données.
1. Cliquez sur l’onglet **Standard** pour ouvrir les options de mise à l’échelle du niveau Standard.
1. Faites glisser le **curseur DTU** à droite pour sélectionner 100 DTU. Notez que cela correspond à l’objectif de service, **S3,** indiqué entre crochets entre les compteurs Taille du stockage et DTU.
1. Cliquez sur **Appliquer** pour déplacer la base de données hors du pool et en faire une base de données S3 Standard.
1. Une fois le déploiement terminé, contrôlez son effet sur la base de données contosoconcerthall et le pool dont elle a été supprimée en consultant les panneaux de la base de données et du pool élastique.

Une fois que la charge supérieure à la normale sur la base de données contosoconcerthall diminue, vous devez rapidement ramener celle-ci dans le pool pour réduire les coûts associés. S’il est difficile de savoir quand cela se produit, vous pouvez définir une alerte sur la base de données qui se déclenche lorsque son utilisation en DTU descend en dessous du maximum par base de données sur le pool. Le déplacement d’une base de données dans un pool est décrite dans l’exercice 5.

## <a name="other-performance-management-patterns"></a>Autres modèles de gestion des performances

**Mise à l’échelle préemptive** : dans l’exercice 6 où vous avez exploré la mise à l’échelle d’une base de données isolée, vous saviez quelle base de données rechercher. Si la direction de la salle de concert Contoso avait informé WTP de la vente de tickets imminente, la base de données aurait pu être déplacée hors du pool préalablement. Dans le cas contraire, il aurait probablement été nécessaire de configurer une alerte sur le pool ou la base de données pour détecter ce qui s’est produit. Vous ne voulez pas prendre connaissance de ce type de problème par d’autres locataires du pool qui se plaignent d’une dégradation des performances. Et si le locataire peut prédire la durée pendant laquelle il va avoir besoin de ressources supplémentaires, vous pouvez configurer un runbook Azure Automation pour déplacer la base de données hors du pool puis la réimporter selon une planification définie.

**Mise à l’échelle libre-service par le locataire** : comme la mise à l’échelle est une tâche facilement appelée via l’API de gestion, vous pouvez aisément configurer la possibilité de mettre à l’échelle des bases de données de locataire dans votre application côté locataire et l’offrir en tant que fonctionnalité de votre service SaaS. Par exemple, laissez les locataires administrer la mise à l’échelle en liant ces opérations à leur facturation.

### <a name="scaling-a-pool-up-and-down-on-a-schedule-to-match-usage-patterns"></a>Mise à l’échelle d’un pool selon une planification pour une correspondance avec les modèles d’utilisation

Lorsque l’utilisation globale du locataire suit des modèles d’utilisation prévisibles, vous pouvez utiliser Azure Automation pour mettre à l’échelle un pool selon une planification. Par exemple, diminuez la taille d’un pool après 18 h et avant 6 h en semaine lorsque vous savez que les besoins en ressources baissent.



## <a name="next-steps"></a>Étapes suivantes

Ce tutoriel vous montre comment effectuer les opérations suivantes :

> [!div class="checklist"]
> * Simuler l’utilisation sur les bases de données de locataire en exécutant un générateur de charge fourni
> * Surveiller les bases de données de locataire à mesure qu’elles répondent à l’augmentation de la charge
> * Augmenter la taille du pool élastique en réponse à la charge accrue sur les bases de données
> * Approvisionner un deuxième pool élastique pour équilibrer la charge de l’activité de la base de données

[Tutoriel Restaurer un locataire unique](sql-database-saas-tutorial-restore-single-tenant.md)


## <a name="additional-resources"></a>Ressources supplémentaires

* [Autres tutoriels reposant sur le déploiement initial d’applications Wingtip Tickets Platform (WTP)](sql-database-wtp-overview.md#sql-database-wtp-saas-tutorials)
* [Pools élastiques SQL](sql-database-elastic-pool.md)
* [Azure Automation](../automation/automation-intro.md)
* [Log Analytics](sql-database-saas-tutorial-log-analytics.md) - Tutoriel Configuration et utilisation de Log Analytics
