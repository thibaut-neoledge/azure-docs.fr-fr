---
title: "Prise en main des outils de base de données élastiques"
description: "Explication de base de la fonctionnalité des outils de bases de données élastiques d’Azure SQL Database, y compris un exemple d’application simple à exécuter."
services: sql-database
documentationcenter: 
manager: jhubbard
author: ddove
editor: CarlRabeler
ms.assetid: b6911f8d-2bae-4d04-9fa8-f79a3db7129d
ms.service: sql-database
ms.workload: sql-database
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 10/24/2016
ms.author: ddove
translationtype: Human Translation
ms.sourcegitcommit: 219dcbfdca145bedb570eb9ef747ee00cc0342eb
ms.openlocfilehash: a42f6ed3bce42cbd5841f241c2f735b87252cb3f


---
# <a name="get-started-with-elastic-database-tools"></a>Prise en main des outils de base de données élastiques
Ce document présente l'aspect développement avec l'exécution de l'exemple d'application. L’exemple crée une application partitionnée simple et explore les fonctionnalités clés des outils de bases de données élastiques. L'exemple illustre des fonctions de la [bibliothèque de client de bases de données élastiques](sql-database-elastic-database-client-library.md)

Pour installer la bibliothèque, accédez à [Microsoft.Azure.SqlDatabase.ElasticScale.Client](https://www.nuget.org/packages/Microsoft.Azure.SqlDatabase.ElasticScale.Client/). La bibliothèque est installée avec l’exemple d’application décrit ci-dessous.

## <a name="prerequisites"></a>Composants requis
1. Visual Studio 2012 ou ultérieur et C# sont requis. Téléchargez une version gratuite à la page [Téléchargements Visual Studio](http://www.visualstudio.com/downloads/download-visual-studio-vs.aspx).
2. NuGet 2.7 ou version supérieure. Pour obtenir la toute dernière version, consultez la page [Installation de NuGet](http://docs.nuget.org/docs/start-here/installing-nuget)

## <a name="download-and-run-the-sample-app"></a>Télécharger et exécuter l’exemple d’application
L'exemple **Base de données élastique avec Azure SQL - Prise en main** présente les aspects les plus importants du développement d'applications partitionnées à l'aide des outils de bases de données élastiques SQL Azure. Il s’intéresse aux principaux cas d’utilisation pour la [gestion des cartes de partition](sql-database-elastic-scale-shard-map-management.md), le [routage dépendant des données](sql-database-elastic-scale-data-dependent-routing.md) et [l’interrogation de plusieurs partitions](sql-database-elastic-scale-multishard-querying.md). Pour télécharger et exécuter les exemples, procédez comme suit : 

1. Ouvrez Visual Studio et sélectionnez **Fichier -> Nouveau -> Projet**.
2. Dans la boîte de dialogue, cliquez sur **En ligne**.
   
    ![New Project>Online][2]
3. Cliquez ensuite sur **Visual C#** sous **Exemples**.
   
    ![Cliquez sur Visual C#][3]
4. Dans la zone de recherche, tapez **base de données élastique** pour rechercher l’exemple. Le titre **Outils de base de données pour base de données SQL Azure - Bien démarrer** s’affiche.
   
    ![Search Box][1]
5. Sélectionnez l’exemple, choisissez un nom et un emplacement pour le nouveau projet et appuyez sur **OK** pour créer le projet.
6. Dans la solution, ouvrez le fichier **app.config** de l'exemple de projet et suivez les instructions du fichier pour ajouter le nom du serveur de base de données SQL Azure et vos informations de connexion (nom d'utilisateur et mot de passe).
7. Générez et exécutez l'application. Lorsque cela vous est demandé, autorisez Visual Studio à restaurer les packages NuGet de la solution. Cela va vous permettre de télécharger la version la plus récente de la bibliothèque cliente de la base de données élastique à partir de NuGet.
8. Consultez les différentes options pour en savoir plus sur les fonctionnalités de la bibliothèque cliente. Notez les étapes suivies par l’application dans la sortie de la console. N’hésitez pas à explorer le code en arrière-plan.
   
    ![progress][4]

Félicitations. Vous avez correctement conçu et exécuté votre première application partitionnée à l’aide des outils de bases de données élastiques sur Azure SQL Database. Passez rapidement en revue les partitions créées par l’exemple en vous connectant avec Visual Studio ou SQL Server Management Studio à votre serveur de base de données Azure. Vous remarquerez de nouveaux exemples de bases de données de partitions, ainsi que la base de données de gestionnaire de carte de partitions créée par l’exemple.

> [!IMPORTANT]
> Nous vous recommandons d’utiliser systématiquement la dernière version de Management Studio afin de rester en cohérence avec les mises à jour de Microsoft Azure et Base de données SQL. [Mettre à jour SQL Server Management Studio](https://msdn.microsoft.com/library/mt238290.aspx).
> 
> 

### <a name="key-pieces-of-the-code-sample"></a>Éléments clés de l’exemple de code
1. **Gestion des partitions et des cartes de partitions** : le code illustre le fonctionnement des partitions, des plages et des mappages dans le fichier **ShardMapManagerSample.cs**. Vous trouverez des informations supplémentaires à ce sujet ici : [Gestion des mappages de partition](http://go.microsoft.com/?linkid=9862595).  
2. **Routage dépendant des données** : le routage des transactions vers la partition appropriée est indiqué dans **DataDependentRoutingSample.cs**. Pour plus d’informations, consultez la page [Routage dépendant des données](http://go.microsoft.com/?linkid=9862596). 
3. **Interrogation sur plusieurs partitions** : l’interrogation sur plusieurs partitions est illustrée dans le fichier **MultiShardQuerySample.cs**. Pour plus d’informations, consultez la page [Interrogation de plusieurs partitions](http://go.microsoft.com/?linkid=9862597).
4. **Ajout de partitions vides** : l’ajout itératif de nouvelles partitions vides est effectué par le code dans le fichier **AddNewShardsSample.cs**. Des informations détaillées à ce sujet sont disponibles ici : [Gestion des mappages de partition](http://go.microsoft.com/?linkid=9862595).

### <a name="other-elastic-scale-operations"></a>Autres opérations de mise à l’échelle élastique
1. **Fractionnement d’une partition existante** : la fonctionnalité de fractionnement des partitions est fournie via **l’outil de fractionnement et de fusion**. Vous trouverez plus d’informations sur cet outil ici : [Présentation de l’outil de fusion et de fractionnement](sql-database-elastic-scale-overview-split-and-merge.md).
2. **Fusion des partitions existantes** : les fusions de partitions sont également effectuées à l’aide de **l’outil de fractionnement et de fusion**. Pour plus d’informations, reportez-vous à: [Présentation de l’outil de fusion et fractionnement](sql-database-elastic-scale-overview-split-and-merge.md).   

## <a name="cost"></a>Coût
Les outils de base de données élastiques sont gratuits. Les outils de base de données élastiques n’induisent aucun coût supplémentaire en plus de votre utilisation d’Azure. 

Par exemple, l’exemple d’application crée des bases de données. Le coût dépend de l'édition d'Azure SQL DB choisie et de l'utilisation Azure de votre application.

Pour plus d’informations sur la tarification, consultez la page [Tarification - Base de données SQL](https://azure.microsoft.com/pricing/details/sql-database/).

## <a name="next-steps"></a>Étapes suivantes
Pour plus d’informations sur les outils de bases de données élastiques, consultez :

* [Table de documentation des outils des bases de données élastiques](https://azure.microsoft.com/documentation/learning-paths/sql-database-elastic-scale/) 
* Exemples de code : 
  * [Base de données élastique avec Azure SQL - Prise en main](http://code.msdn.microsoft.com/Elastic-Scale-with-Azure-a80d8dc6?SRC=VSIDE)
  * [Base de données élastique avec Azure SQL - Intégration avec Entity Framework](http://code.msdn.microsoft.com/Elastic-Scale-with-Azure-bae904ba?SRC=VSIDE)
  * [Partitionner l’élasticité sur le centre de scripts](https://gallery.technet.microsoft.com/scriptcenter/Elastic-Scale-Shard-c9530cbe)
* Blog : [Annonce de la mise à l’échelle élastique](https://azure.microsoft.com/blog/2014/10/02/introducing-elastic-scale-preview-for-azure-sql-database/)
* Microsoft Virtual Academy : [Vidéo d’implémentation de la montée en charge à l’aide du partitionnement avec la bibliothèque cliente de bases de données élastiques](https://mva.microsoft.com/en-US/training-courses/elastic-database-capabilities-with-azure-sql-db-16554?l=lWyQhF1fC_6306218965) 
* Channel 9 : [Vidéo de présentation de la mise à l’échelle élastique](http://channel9.msdn.com/Shows/Data-Exposed/Azure-SQL-Database-Elastic-Scale)
* Forum de discussion : [Forum sur Azure SQL Database](http://social.msdn.microsoft.com/forums/azure/home?forum=ssdsgetstarted)
* Pour mesurer les performances : [Compteurs de performances pour le Gestionnaire de cartes de partitions](sql-database-elastic-database-client-library.md)

<!--Anchors-->
[Exemple d’application de mise à l’échelle élastique]: #The-Elastic-Scale-Sample-Application
[Download and Run the Sample App]: #Download-and-Run-the-Sample-App
[Coût]: #Cost
[Étapes suivantes]: #next-steps

<!--Image references-->
[1]: ./media/sql-database-elastic-scale-get-started/newProject.png
[2]: ./media/sql-database-elastic-scale-get-started/click-online.png
[3]: ./media/sql-database-elastic-scale-get-started/click-CSharp.png
[4]: ./media/sql-database-elastic-scale-get-started/output2.png




<!--HONumber=Nov16_HO3-->


