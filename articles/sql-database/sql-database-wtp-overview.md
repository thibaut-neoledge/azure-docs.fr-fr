---
title: "SaaS in a box (exemple d’application SaaS utilisant Azure SQL Database) | Microsoft Docs"
description: "Créer des applications SaaS à l’aide de SQL Database"
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
ms.sourcegitcommit: 71fea4a41b2e3a60f2f610609a14372e678b7ec4
ms.openlocfilehash: bf5745a788cd9ab6bf2ea8d5d97b8c04f083fc5d
ms.contentlocale: fr-fr
ms.lasthandoff: 05/10/2017


---
# <a name="introduction-to-the-wingtip-tickets-platform-wtp-sample-saas-application"></a>Présentation de l’exemple d’application SaaS Wingtip Tickets Platform (WTP)

L’application SaaS Wingtip Tickets Platform (WTP) est un exemple d’application multi-locataire qui présente les avantages uniques de SQL Database. L’application utilise une base de donnée par locataire, un modèle d’application SaaS, pour traiter plusieurs locataires. L’application WTP est conçue pour présenter les fonctionnalités d’Azure SQL Database qui activent des scénarios Saas, y compris les modèles de gestion et de conception SaaS. Pour que vous soyez rapidement opérationnel, [l’application WTP se déploie en moins de cinq minutes](sql-database-saas-tutorial.md) !

Après avoir déployé l’application WTP, explorez le [collection de didacticiels](#sql-database-saas-tutorials) qui se basent sur le déploiement initial. Chaque didacticiel se concentre sur les tâches standard qui sont implémentées dans les applications SaaS. Les tâches sont implémentées suivant des modèles SaaS qui tirent parti des fonctionnalités intégrées à SQL Database. Les modèles décrits incluent : l’approvisionnement de nouveaux clients, la restauration des bases de données client, l’exécution de requêtes distribuées sur tous les locataires et le déploiement de modifications de schéma sur toutes les bases de données clientes. Chaque didacticiel inclut des scripts réutilisables, avec des explications détaillées qui simplifient considérablement la compréhension, pour l’implémentation des mêmes modèles de gestion SaaS dans vos applications.

Tandis que l’application WTP est relativement complète et attractive en tant qu’exemple d’application, il est important de se concentrer sur les principaux modèles SaaS qui sont liés à le niveau de données. En d’autres termes, concentrez-vous sur le niveau de données et n’analysez pas trop l’application en elle-même. La compréhension de l’implémentation de ces modèles SaaS centraux est essentielle pour implémenter ces modèles dans vos applications, tout en prenant en compte toutes les modifications nécessaires pour vos besoins métier spécifiques.



## <a name="application-architecture"></a>Architecture de l'application

L’application WTP utilise le modèle de base de données par client, ainsi que des pools élastiques SQL pour optimiser l’efficacité.
Utilisation d’un catalogue client pour la configuration de l’approvisionnement et de la connectivité.
Fonctions intégrées : application, pool, surveillance de la base de données et alertes (OMS).
Gestion des données de référence et des schémas entre locataires (tâches de bases de données élastiques).
Requête entre locataires, analytique opérationnelle (requête élastique).
Utilisation de données géo-distribuées pour une couverture étendue.
Continuité de l’activité Récupération à locataire unique (PITR) DR de mise à l’échelle (géo-restauration, géo-réplication, auto-DR) Gestion du libre-service par locataire (via les API de gestion) PITR pour récupérer des erreurs causées soi-même.

L’application Wingtip centrale utilise un pool avec trois exemples de locataires, plus une base de données de catalogue.

![Architecture WTP](media/sql-database-wtp-overview/wtp-architecture.png)


## <a name="sql-database-wtp-saas-tutorials"></a>Didacticiels SaaS WTP SQL Database

Les didacticiels suivants reposent sur le déploiement initial de l’[exemple d’application SaaS Wingtip Tickets Plateform](sql-database-saas-tutorial.md) :

| Domaine | Description | Emplacement du script |
|:--|:--|:--|
|[Didacticiel Approvisionner de nouveaux locataires et les inscrire dans le catalogue](sql-database-saas-tutorial-provision-and-catalog.md)| Approvisionner de nouveaux locataires et les inscriredans le catalogue | [Scripts sur GitHub](https://github.com/Microsoft/WingtipSaaS/tree/master/Learning%20Modules/Provision%20and%20Catalog) |
|[Didacticiel Surveiller et gérer les performances](sql-database-saas-tutorial-performance-monitoring.md)| Surveiller et gérer les performances de la base de données et du pool | [Scripts sur GitHub](https://github.com/Microsoft/WingtipSaaS/tree/master/Learning%20Modules/Performance%20Monitoring%20and%20Management) |
|[Didacticiel Restaurer une base de données à locataire unique](sql-database-saas-tutorial-restore-single-tenant.md)| Restaurer les bases de données des locataires | [Scripts sur GitHub](https://github.com/Microsoft/WingtipSaaS/tree/master/Learning%20Modules/Business%20Continuity%20and%20Disaster%20Recovery/RestoreTenant) |
|[Didacticiel Gérer les schémas de locataire](sql-database-saas-tutorial-schema-management.md)| Exécuter des requêtes sur tous les locataires  | [Scripts sur GitHub](https://github.com/Microsoft/WingtipSaaS/tree/master/Learning%20Modules/Schema%20Management) |
|[Didacticiel Exécuter des analyses ad hoc](sql-database-saas-tutorial-adhoc-analytics.md) | Créer une base de données d’analyse ad hoc et exécuter des requêtes sur tous les locataires  | [Scripts sur GitHub](https://github.com/Microsoft/WingtipSaaS/tree/master/Learning%20Modules/Operational%20Analytics/Adhoc%20Analytics) |
|[Didacticiel Gestion avec Log Analytics (OMS)](sql-database-saas-tutorial-log-analytics.md) | Configurer et explorer Log Analytics | [Scripts sur GitHub](https://github.com/Microsoft/WingtipSaaS/tree/master/Learning%20Modules/Performance%20Monitoring%20and%20Management/LogAnalytics) |
|[Didacticiel Exécuter des analyses sur les locataires](sql-database-saas-tutorial-tenant-analytics.md) | Configurer et exécuter des requêtes d’analyse sur les locataires | [Scripts sur GitHub](https://github.com/Microsoft/WingtipSaaS/tree/master/Learning%20Modules/Operational%20Analytics/Tenant%20Analytics) |

## <a name="get-the-wingtip-application-scripts"></a>Obtenir les scripts d’application Wingtip

Les scripts Wingtip Tickets et le code source de l’application sont disponibles dans le référentiel GitHub [WingtipSaaS](https://github.com/Microsoft/WingtipSaaS). Les fichiers de script se trouvent dans le [dossier Learning Modules](https://github.com/Microsoft/WingtipSaaS/tree/master/Learning%20Modules). Téléchargez le dossier **Learning Modules** en local sur votre ordinateur, tout en conservant l’arborescence.

## <a name="working-with-the-wtp-powershell-scripts"></a>Utilisation des scripts PowerShell WTP

L’utilisation de l’application WTP présente l’avantage de vous plonger dans les scripts fournis et d’examiner la façon dont les différents modèles SaaS sont implémentés.

Pour afficher les modules et les scripts fournis et pour faciliter la navigation pour une meilleure compréhension, utilisez [Windows PowerShell ISE](https://msdn.microsoft.com/powershell/scripting/core-powershell/ise/introducing-the-windows-powershell-ise). Étant donné que la plupart des scripts avec le préfixe *Demo-* contiennent des variables que vous pouvez modifier avant l’exécution, l’utilisation de PowerShell ISE simplifie l’utilisation de ces scripts.

Pour chaque déploiement de l’application WTP, il existe un fichier **UserConfig.psm1** contenant deux paramètres permettant de configurer les valeurs de groupe de ressources et de nom d’utilisateur que vous avez définies au cours du déploiement. Une fois le déploiement terminé, modifiez le paramètre de module **UserConfig.psm1**, le paramètre _ResourceGroupName_ et le paramètre _Name_. Ces valeurs sont utilisées par d’autres scripts pour leur exécution. Il est donc recommandé de les configurer à la fin du déploiement !



### <a name="execute-scripts-by-pressing-f5"></a>Exécuter des scripts en appuyant sur F5

Plusieurs scripts utilisent *$PSScriptRoot* pour permettre la navigation dans les dossiers. Cette variable est évaluée uniquement lorsque le script est exécuté en appuyant sur **F5**.  La mise en surbrillance et l’exécution d’une sélection (**F8**) peut entraîner des erreurs. Nous vous conseillons donc d’appuyer sur **F5** lors de l’exécution des scripts WTP.

### <a name="step-through-the-scripts-to-examine-the-implementation"></a>Parcourir les scripts pour examiner l’implémentation

Le véritable intérêt de l’exploration des scripts est que la navigation permet de voir à quoi ils servent. Extrayez les scripts _Demo-_ du premier niveau, qui fournissent un flux de travail de qualité facile à lire, incluant les étapes nécessaires à la réalisation de chaque tâche. Explorez plus en profondeur les appels individuels pour afficher les détails d’implémentation pour les différents modèles SaaS.

Conseils pour utiliser et [déboguer les scripts PowerShell](https://msdn.microsoft.com/powershell/scripting/core-powershell/ise/how-to-debug-scripts-in-windows-powershell-ise) :

* Ouvrez et configurez les scripts demo- dans PowerShell ISE.
* Exécutez-les ou poursuivez avec **F5**. L’utilisation de la touche **F8** n’est pas conseillée car *$PSScriptRoot* n’est pas évalué lors de l’exécution de sélections d’un script.
* Placez des points d’arrêt en cliquant ou en sélectionnant une ligne et en appuyant sur **F9**.
* Survolez l’appel d’une fonction ou d’un script à l’aide de la touche **F10**.
* Parcourez l’appel d’une fonction ou d’un script à l’aide de la touche **F11**.
* Sortez de l’appel actuel d’une fonction ou d’un script en appuyant sur **MAJ + F11**.




## <a name="explore-database-schema-and-execute-sql-queries-using-ssms"></a>Explorez le schéma de base de données et exécutez des requêtes SQL à l’aide de SSMS

Utilisez [SQL Server Management Studio (SSMS)](https://docs.microsoft.com/sql/ssms/download-sql-server-management-studio-ssms) pour vous connecter et parcourir les bases de données et les serveurs WTP.

L’exemple d’application WTP peut initialement se connecter à deux serveurs SQL Database : le serveur *tenants1* et le serveur *catalog* :


1. Ouvrez *SSMS* et connectez-vous au serveur *tenants1-&lt;User&gt;.database.windows.net*.
2. Cliquez sur **Connexion** > **Moteur de base de données...** :

   ![catalog server](media/sql-database-wtp-overview/connect.png)

1. Les informations d’identification de la démonstration sont : nom d’utilisateur = *developer*, mot de passe = *P@ssword1*.

   ![connection](media\sql-database-wtp-overview\tenants1-connect.png)

1. Répétez les étapes 2 et 3 et connectez-vous au serveur *catalog-&lt;User&gt;.database.windows.net*.

Une fois la connexion établie, vous devez voir les deux serveurs. Vous pouvez voir plus ou moins de bases de données en fonction du nombre de locataires que vous avez configuré :

![object explorer](media/sql-database-wtp-overview/object-explorer.png)



## <a name="next-steps"></a>Étapes suivantes

[Déployer l’exemple d’application SaaS Wingtip Tickets](sql-database-saas-tutorial.md)
