<properties 
	pageTitle="Prise en main des outils de base de données élastiques" 
	description="Explication de base de la fonctionnalité des outils de bases de données élastiques d’Azure SQL Database, y compris un exemple d’application simple à exécuter." 
	services="sql-database" 
	documentationCenter="" 
	manager="jeffreyg" 
	authors="sidneyh" 
	editor=""/>

<tags 
	ms.service="sql-database" 
	ms.workload="sql-database" 
	ms.tgt_pltfrm="na" 
	ms.devlang="na" 
	ms.topic="article" 
	ms.date="04/17/2015" 
	ms.author="sidneyh@microsoft.com"/>

#Prise en main des outils de base de données élastiques

La capacité d’augmentation et de réduction à la demande est l’une des principales promesses du cloud computing. Pour les applications de base de données, une technique importante de création de ces solutions extensibles est le modèle appelé partitionnement, où les données sont partitionnées physiquement sur un certain nombre de bases de données structurées de façon identique. Jusqu’ici, la création et la gestion des applications qui utilisent le partitionnement ont nécessité un codage important en dehors de la logique métier de l’application.

Les outils de base de données élastiques simplifient la création et la gestion des applications à l’aide du partitionnement de base de données dans Azure SQL DB. Les outils incluent la bibliothèque cliente de la base de données élastique et l’outil de fusion et de fractionnement. Ensemble, ils implémente les aspects du partitionnement ayant trait à l’infrastructure, ce qui vous permet de vous concentrer sur la logique métier de votre application.

Ce document présente l’aspect développement à l’aide de la bibliothèque cliente de la base de données élastique.

Pour plus d’informations sur le fonctionnement des outils de base de données élastique, consultez [Présentation des outils de bases de données élastiques](sql-database-elastic-scale-introduction.md).

Pour obtenir la liste de toutes les rubriques sur les outils de bases de données élastiques, consultez la [documentation correspondante](sql-database-elastic-scale-documentation-map.md)

## Exemple d’application de base de données élastique

L’exemple crée une application partitionnée simple et explore les fonctionnalités clés des outils de bases de données élastiques. Pour télécharger et exécuter l’application, suivez les étapes affichées ci-dessous ou dans la vidéo intitulée [Infrastructure élastique - Prise en main](http://channel9.msdn.com/Blogs/Windows-Azure/Elastic-Scale-with-Azure-SQL-Database-Getting-Started).

### Configuration requise
Pour exécuter l'exemple d'application, vous devez utiliser Visual Studio et accéder à une base de données SQL Azure exécutée sur Azure. Si vous n'êtes pas encore abonné à Azure, inscrivez-vous pour vous abonner à une [version d'évaluation](http://azure.microsoft.com/pricing/free-trial/).
#### Visual Studio et Nuget

1. Visual Studio 2012 ou ultérieur et C# sont requis. Téléchargez une version gratuite à la page [Téléchargements Visual Studio](http://www.visualstudio.com/downloads/download-visual-studio-vs.aspx).
2. Nuget 2.7 ou ultérieur. Pour obtenir la toute dernière version, consultez la page [Installation de NuGet](http://docs.nuget.org/docs/start-here/installing-nuget)
#### Création d'une base de données SQL Azure

* Consultez [Prise en main de Microsoft Azure SQL Database](sql-database-get-started.md).

## Télécharger et exécuter l’exemple d’application

L’exemple **Base de données élastique avec Azure SQL - Prise en main** présente les aspects les plus importants du développement d’applications partitionnées à l’aide des outils de bases de données élastiques SQL Azure. Il s’intéresse aux principaux cas d’utilisation pour la [gestion des cartes de partition](sql-database-elastic-scale-shard-map-management.md), le [routage dépendant des données](sql-database-elastic-scale-data-dependent-routing.md) et l’[interrogation de plusieurs partitions](sql-database-elastic-scale-multishard-querying.md). Pour télécharger et exécuter les exemples, procédez comme suit :

1. Ouvrez Visual Studio et sélectionnez **Fichier -> Nouveau -> Projet**.
2. Dans la boîte de dialogue, cliquez sur **En ligne**.

    ![New Project>Online][2]
3. Cliquez ensuite sur **Visual C#** sous **Exemples**.

    ![Click Visual C#][3]
4. Dans la zone de recherche, tapez **base de données élastique** pour trouver l’exemple. Le titre **Outils de bases de données élastiques pour Azure SQL - Prise en main** s’affiche.

    ![Search Box][1]
 
5. Sélectionnez l’exemple, choisissez un nom et un emplacement pour le nouveau projet et appuyez sur **OK** pour créer le projet.
6. Dans la solution, ouvrez le fichier **app.config** de l'exemple de projet et suivez les instructions du fichier pour ajouter le nom du serveur de base de données SQL Azure et vos informations de connexion (nom d'utilisateur et mot de passe).
7. Générez et exécutez l'application. Lorsque cela vous est demandé, autorisez Visual Studio à restaurer les packages NuGet de la solution. Cela va vous permettre de télécharger la version la plus récente de la bibliothèque cliente de la base de données élastique à partir de NuGet.
8. Consultez les différentes options pour en savoir plus sur les fonctionnalités de la bibliothèque cliente. Notez les étapes suivies par l’application dans la sortie de la console. N’hésitez pas à explorer le code en arrière-plan.

    ![progress][4]

Félicitations. Vous avez correctement conçu et exécuté votre première application partitionnée à l’aide des outils de bases de données élastiques sur Azure SQL Database. Passez rapidement en revue les partitions créées par l’exemple en vous connectant avec Visual Studio ou SQL Server Management Studio à votre serveur de base de données Azure. Vous remarquerez de nouveaux exemples de bases de données de partitions, ainsi que la base de données de gestionnaire de carte de partitions créée par l’exemple.

**Remarque** Si vous n’avez pas SQL Server Management Studio, consultez [Gestion d'Azure SQL Database à l'aide de SQL Server Management Studio](sql-database-manage-azure-ssms.md), où vous trouverez des instructions sur l’obtention de l’outil.

### Éléments clés de l’exemple de code

1. **Gestion des partitions et des cartes de partitions** : le code illustre le fonctionnement des partitions, des plages et des mappages dans le fichier **ShardMapManagerSample.cs**. Vous trouverez des informations supplémentaires à ce sujet ici : [Gestion des mappages de partition](http://go.microsoft.com/?linkid=9862595).  
2. **Routage dépendant des données** : le routage des transactions vers la partition appropriée est indiqué dans **DataDependentRoutingSample.cs**. Pour plus d’informations, consultez la page [Routage dépendant des données](http://go.microsoft.com/?linkid=9862596). 
3. **Interrogation sur plusieurs partitions ** : l’interrogation sur plusieurs partitions est illustrée dans le fichier **MultiShardQuerySample.cs**. Pour plus d’informations, consultez la page [Interrogation de plusieurs partitions](http://go.microsoft.com/?linkid=9862597).
4. **Ajout de partitions vides** : l’ajout itératif de nouvelles partitions vides est effectué par le code dans le fichier **AddNewShardsSample.cs**. Des informations détaillées à ce sujet sont disponibles ici : [Gestion des mappages de partition](http://go.microsoft.com/?linkid=9862595).

### Autres opérations de mise à l’échelle élastique

1. **Fractionnement d’une partition existante** : la fonctionnalité de fractionnement des partitions est fournie via l’**outil de fractionnement/fusion**. Vous trouverez plus d’informations sur cet outil ici : [Présentation de l’outil de fusion et de fractionnement](sql-database-elastic-scale-overview-split-and-merge.md).
2. **Fusion des partitions existantes** : les fusions de partitions sont également effectuées à l’aide de l’**outil de fractionnement/fusion**. Pour plus d’informations, reportez-vous à: [Présentation de l’outil de fusion et fractionnement](sql-database-elastic-scale-overview-split-and-merge).   


## Coût

Les outils de base de données élastiques sont gratuits. Les outils de base de données élastiques n’induisent aucun coût supplémentaire en plus de votre utilisation d’Azure.

Par exemple, l’exemple d’application crée des bases de données. Le coût dépend de l'édition d'Azure SQL DB choisie et de l'utilisation Azure de votre application.

Pour plus d’informations sur la tarification, consultez la page [Tarification - Base de données SQL](http://azure.microsoft.com/pricing/details/sql-database/).

## Étapes suivantes
Pour plus d’informations sur les outils de bases de données élastiques, consultez :

* [Table de documentation des outils des bases de données élastiques](sql-database-elastic-scale-documentation-map.md) 
-    Exemples de code : 
    -    [Base de données élastique avec Azure SQL - Prise en main](http://code.msdn.microsoft.com/Elastic-Scale-with-Azure-a80d8dc6?SRC=VSIDE)
    -    [Base de données élastique avec Azure SQL - Intégration avec Entity Framework](http://code.msdn.microsoft.com/Elastic-Scale-with-Azure-bae904ba?SRC=VSIDE)
    -    [Partitionner l’élasticité sur le centre de scripts](https://gallery.technet.microsoft.com/scriptcenter/Elastic-Scale-Shard-c9530cbe)
-    Blog : [Annonce de la mise à l’échelle élastique](http://azure.microsoft.com/blog/2014/10/02/introducing-elastic-scale-preview-for-azure-sql-database/)
-    Channel 9 : [Vidéo de présentation de la mise à l’échelle élastique](http://channel9.msdn.com/Shows/Data-Exposed/Azure-SQL-Database-Elastic-Scale)
-    Forum de discussion : [Forum sur Azure SQL Database](http://social.msdn.microsoft.com/forums/azure/home?forum=ssdsgetstarted)


<!--Anchors-->
[The Elastic Scale Sample Application]: #The-Elastic-Scale-Sample-Application
[Download and Run the Sample App]: #Download-and-Run-the-Sample-App
[Cost]: #Cost
[Next steps]: #next-steps

<!--Image references-->
[1]: ./media/sql-database-elastic-scale-get-started/newProject.png
[2]: ./media/sql-database-elastic-scale-get-started/click-online.png
[3]: ./media/sql-database-elastic-scale-get-started/click-CSharp.png
[4]: ./media/sql-database-elastic-scale-get-started/output2.png
 

<!---HONumber=62-->