---
title: "Exemple d’application mutualisée Azure SQL Database - SaaS Wingtip | Microsoft Docs"
description: "Apprendre à l’aide d’un exemple d’application mutualisée utilisant l’exemple Azure SQL Database - SaaS Wingtip"
keywords: "didacticiel sur les bases de données SQL"
services: sql-database
author: stevestein
manager: craigg
ms.service: sql-database
ms.custom: scale out apps
ms.workload: data-management
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 06/09/2017
ms.author: sstein
ms.translationtype: HT
ms.sourcegitcommit: 2c6cf0eff812b12ad852e1434e7adf42c5eb7422
ms.openlocfilehash: 68a9d97a881f3a7628a08b66091c3feb4c4dbbfe
ms.contentlocale: fr-fr
ms.lasthandoff: 09/13/2017

---
# <a name="introduction-to-a-sql-database-multi-tenant-saas-app-example"></a>Présentation d’un exemple d’application SaaS mutualisée SQL Database

L’application *SaaS Wingtip* est un exemple d’application mutualisée qui présente les avantages uniques de SQL Database. L’application utilise une base de donnée par locataire, un modèle d’application SaaS, pour traiter plusieurs locataires. L’application est conçue pour présenter les fonctionnalités d’Azure SQL Database qui activent des scénarios Saas, dont quelques modèles de gestion et de conception SaaS. Pour que vous soyez rapidement opérationnel, l’application SaaS Wingtip se déploie en moins de cinq minutes.

Le code source de l’application et les scripts de gestion sont disponibles dans le référentiel GitHub [WingtipSaaS](https://github.com/Microsoft/WingtipSaaS). Pour exécuter les scripts, [téléchargez le dossier Learning Modules](#download-and-unblock-the-wingtip-saas-scripts) sur votre ordinateur local.

## <a name="sql-database-wingtip-saas-tutorials"></a>Didacticiels de l’application SaaS Wingtip de SQL Database

Après avoir déployé l’application, explorez les didacticiels suivants qui se basent sur le déploiement initial. Ces didacticiels explorent des modèles SaaS courants qui tirent parti des fonctionnalités intégrées de SQL Database, de SQL Data Warehouse et d’autres services Azure. Les didacticiels incluent des scripts PowerShell, avec des explications détaillées qui simplifient considérablement la compréhension et l’implémentation des mêmes modèles de gestion SaaS dans vos applications.


| Didacticiel | Description |
|:--|:--|
|[Déployer et explorer l’application SaaS Wingtip](sql-database-saas-tutorial.md)| **COMMENCEZ ICI** Déployez et explorez l’application SaaS Wingtip dans votre abonnement Azure. |
|[Approvisionner des clients et les inscrire dans le catalogue](sql-database-saas-tutorial-provision-and-catalog.md)| Découvrez comment l’application se connecte aux clients à l’aide d’une base de données catalogue, et comment le catalogue mappe les clients à leurs données. |
|[Surveiller et gérer les performances](sql-database-saas-tutorial-performance-monitoring.md)| Découvrez comment utiliser les fonctionnalités de surveillance de SQL Database, et définir des alertes qui se déclenchent en cas de dépassement des seuils de performances. |
|[Surveiller avec Log Analytics (OMS)](sql-database-saas-tutorial-log-analytics.md) | Apprenez à utiliser [Log Analytics](../log-analytics/log-analytics-overview.md) pour surveiller de grandes quantités de ressources dans plusieurs pools. |
|[Restaurer un client unique](sql-database-saas-tutorial-restore-single-tenant.md)| Découvrez comment restaurer une base de données client à un point antérieur dans le temps. Les étapes permettant de restaurer une base de données parallèle, en laissant la base de données client en ligne, sont également incluses. |
|[Gérer les schémas de client](sql-database-saas-tutorial-schema-management.md)| Découvrez comment mettre à jour un schéma et des données de référence sur tous les clients SaaS Wingtip. |
|[Exécuter une analyse ad hoc](sql-database-saas-tutorial-adhoc-analytics.md) | Créez une base de données analytique ad hoc, puis exécutez des requêtes distribuées en temps réel sur tous les clients.  |
|[Exécuter une analyse des clients](sql-database-saas-tutorial-tenant-analytics.md) | Extrayez les données client dans une base de données analytique ou un entrepôt de données pour l’exécution de requêtes analytiques en mode hors connexion. |



## <a name="application-architecture"></a>Architecture de l'application

L’application SaaS Wingtip utilise le modèle de base de données par client, ainsi que des pools élastiques SQL pour optimiser l’efficacité. Pour l’approvisionnement et le mappage des clients à leurs données, une base de données catalogue est utilisée. L’application SaaS Wingtip centrale utilise un pool avec trois exemples de clients, plus la base de données catalogue. Suivre un grand nombre de didacticiels SaaS Wingtip entraîne l’ajout de modules complémentaires au déploiement initial, en raison de l’introduction de bases de données analytiques, de la gestion des schémas des bases de données, etc.


![Architecture de SaaS Wingtip](media/sql-database-wtp-overview/app-architecture.png)


Lorsque vous suivez les didacticiels et utilisez l’application, il est important que vous vous concentriez sur les modèles SaaS liés à la couche données. En d’autres termes, concentrez-vous sur le niveau de données et n’analysez pas trop l’application en elle-même. La compréhension de l’implémentation de ces modèles SaaS est essentielle pour implémenter ces modèles dans vos applications, tout en prenant en compte toutes les modifications nécessaires pour vos besoins métier spécifiques.

## <a name="download-and-unblock-the-wingtip-saas-scripts"></a>Télécharger et débloquer les scripts Wingtip SaaS

Le contenu exécutable (scripts, DLL) peut être bloqué par Windows lorsque des fichiers zip sont téléchargés à partir d’une source externe puis extraits. Lorsque vous extrayez les scripts d’un fichier zip, ***suivez les étapes ci-dessous pour débloquer le fichier .zip avant l’extraction***. Cela garantit que les scripts sont autorisés à s’exécuter.

1. Accédez au [référentiel github Wingtip SaaS](https://github.com/Microsoft/WingtipSaaS).
1. Cliquez sur **Cloner ou télécharger**.
1. Cliquez sur **Télécharger ZIP** et enregistrez le fichier.
1. Cliquez avec le bouton droit sur le fichier **WingtipSaaS-master.zip**, puis sélectionnez **Propriétés**.
1. Sous l’onglet **Général**, sélectionnez **Débloquer**.
1. Cliquez sur **OK**.
1. Procédez à l’extraction des fichiers.

Les scripts se trouvent dans le dossier *..\\WingtipSaaS-master\\Learning Modules*.


## <a name="working-with-the-wingtip-saas-powershell-scripts"></a>Utilisation des scripts PowerShell de SaaS Wingtip

Pour tirer le meilleur parti de l’exemple, vous devez approfondir les scripts fournis. Utilisez des points d’arrêt et parcourez les scripts en examinant les détails de l’implémentation des différents motifs SaaS. Pour parcourir facilement les scripts et les modules fournis afin d’acquérir une compréhension optimale de ceux-ci, nous vous recommandons d’utiliser [PowerShell ISE](https://msdn.microsoft.com/powershell/scripting/core-powershell/ise/introducing-the-windows-powershell-ise).

### <a name="update-the-configuration-file-for-your-deployment"></a>Mettre à jour le fichier de configuration pour votre déploiement

Modifiez le fichier **UserConfig.psm1** avec le groupe de ressources et la valeur utilisateur que vous avez définis durant le déploiement :

1. Ouvrez le *PowerShell ISE* puis chargez...\\Learning Modules\\*UserConfig.psm1*. 
1. Mettez à jour *ResourceGroupName* et *Name* avec les valeurs spécifique de votre déploiement (sur les lignes 10 et 11 uniquement).
1. N’oubliez pas d’enregistrer les modifications.

La définition de ces valeurs ici vous évite simplement d’avoir à mettre à jour ces valeurs spécifiques du déploiement dans chaque script.

### <a name="execute-scripts-by-pressing-f5"></a>Exécuter des scripts en appuyant sur F5

Plusieurs scripts utilisent *$PSScriptRoot* pour parcourir les dossiers, et *$PSScriptRoot* est évalué uniquement lors de l’exécution de scripts en appuyant sur **F5**.  La mise en surbrillance et l’exécution d’une sélection (**F8**) pouvant entraîner des erreurs, nous vous conseillons d’appuyer sur **F5** lors de l’exécution des scripts.

### <a name="step-through-the-scripts-to-examine-the-implementation"></a>Parcourir les scripts pour examiner l’implémentation

La meilleure façon de comprendre les scripts consiste à les parcourir pour voir ce qu’ils font. Extrayez les scripts **Demo-** inclus, qui présentent un flux de travail de haut niveau facile pour suivre. Les scripts **Demo-** présentent les étapes nécessaires à l’accomplissement de chaque tâche. Par conséquent, définissez des points d’arrêt, puis approfondissez les appels individuels pour voir les détails d’implémentation des différents modèles SaaS.

Conseils pour l’exploration et le parcours des scripts PowerShell :

* Ouvrez les scripts **Demo-** dans le PowerShell ISE.
* Exécutez ou continuez à exécuter le script à l’aide de la touche **F5** (l’utilisation de la touche **F8** n’est pas conseillée, car *$PSScriptRoot* n’est pas évalué lors de l’exécution des sélections d’un script).
* Placez des points d’arrêt en cliquant ou en sélectionnant une ligne et en appuyant sur **F9**.
* Survolez l’appel d’une fonction ou d’un script à l’aide de la touche **F10**.
* Parcourez l’appel d’une fonction ou d’un script à l’aide de la touche **F11**.
* Sortez de l’appel actuel d’une fonction ou d’un script en appuyant sur **MAJ + F11**.


## <a name="explore-database-schema-and-execute-sql-queries-using-ssms"></a>Explorez le schéma de base de données et exécutez des requêtes SQL à l’aide de SSMS

Utilisez [SQL Server Management Studio (SSMS)](https://docs.microsoft.com/sql/ssms/download-sql-server-management-studio-ssms) pour vous connecter et parcourir les bases de données et les serveurs de l’application.

Le déploiement comprend initialement deux serveurs de base de données SQL auxquels se connecter : le serveur *tenants1-&lt;Utilisateur&gt;* et le serveur *catalog-&lt;Utilisateur&gt;*. Pour garantir une connexion de démonstration réussie, les deux serveurs ont un [règle de pare-feu](sql-database-firewall-configure.md) autorisant toutes les adresses IP.


1. Ouvrez *SSMS* et connectez-vous au serveur *tenants1-&lt;User&gt;.database.windows.net*.
1. Cliquez sur **Connexion** > **Moteur de base de données...** :

   ![catalog server](media/sql-database-wtp-overview/connect.png)

1. Les informations d’identification de la démonstration sont : nom d’utilisateur = *developer*, mot de passe = *P@ssword1*.

   ![connection](media\sql-database-wtp-overview\tenants1-connect.png)

1. Répétez les étapes 2 et 3 et connectez-vous au serveur *catalog-&lt;User&gt;.database.windows.net*.

Une fois la connexion établie, vous devez voir les deux serveurs. Votre liste des bases de données peut être différente, selon les clients que vous avez approvisionnés :

![object explorer](media/sql-database-wtp-overview/object-explorer.png)



## <a name="next-steps"></a>Étapes suivantes

[Déployer l’application SaaS Wingtip](sql-database-saas-tutorial.md)
