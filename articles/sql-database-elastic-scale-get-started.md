<properties 
	pageTitle="Prise en main de l'infrastructure élastique de Base de données SQL Azure" 
	description="Explication de base de la fonctionnalité d'infrastructure élastique de Base de données SQL Azure, y compris l'exécution d'un exemple d'application." 
	services="sql-database" 
	documentationCenter="" 
	manager="jhubbard" 
	authors="sidneyh" 
	editor=""/>

<tags 
	ms.service="sql-database" 
	ms.workload="sql-database" 
	ms.tgt_pltfrm="na" 
	ms.devlang="na" 
	ms.topic="article" 
	ms.date="02/03/2015" 
	ms.author="sidneyh@microsoft.com"/>

# Prise en main de l'infrastructure élastique de Base de données SQL Azure (version préliminaire)

La capacité d'augmentation et de réduction à la demande est l'une des principales promesses du cloud computing. Répondre à cette promesse peut s'avérer fastidieux et complexe pour le niveau Base de données des applications cloud. Ces dernières années, le secteur s'est mis d'accord sur un modèle de conception bien établi connu sous le nom de partitionnement. Alors que le modèle de partitionnement global répond à ce défi, la conception et la gestion d'applications utilisant le partitionnement nécessitent des investissements significatifs en termes d'infrastructure, qui sont indépendants de la logique métier de l'application. 

L'infrastructure élastique de base de données SQL Azure (version préliminaire) permet à la couche de données d'une application de faire l'objet d'une mise à l'échelle via les pratiques de partitionnement standard du secteur, tout en rationalisant de manière significative le développement et la gestion de vos applications cloud partitionnées. L'infrastructure élastique propose des fonctionnalités de développement et de gestion via un ensemble de bibliothèques .net et de modèles de service Azure que vous pouvez héberger dans votre propre abonnement Azure pour gérer vos applications hautement évolutives. L'infrastructure élastique de Base de données SQL Azure implémente les aspects du partitionnement ayant trait à l'infrastructure, ce qui vous permet de vous concentrer sur la logique métier de votre application. 

Ce document présente l'aspect développement de l'infrastructure élastique de Base de données SQL Azure. 

Pour plus d'informations sur le fonctionnement de l'infrastructure élastique, consultez la page [Vue d'ensemble de l'infrastructure élastique](http://go.microsoft.com/?linkid=9862592).

Pour obtenir la liste de toutes les rubriques sur l'infrastructure élastique, consultez la rubrique [Documentation sur l'infrastructure élastique](./sql-database-elastic-scale-documentation-map.md)

## Exemple d'application d'infrastructure élastique

L'exemple crée une application partitionnée et explore les fonctionnalités clés de l'infrastructure élastique. Pour télécharger et exécuter l'application, suivez les étapes affichées ci-dessous ou dans la vidéo intitulée [Infrastructure élastique - Prise en main](http://go.microsoft.com/?linkid=9862983). 

### Conditions préalables
Pour exécuter l'exemple d'application, vous devez utiliser Visual Studio et accéder à une base de données SQL Azure exécutée sur Azure Si vous n'êtes pas encore abonné à Azure, inscrivez-vous pour vous abonner à une [version d'évaluation](http://azure.microsoft.com/pricing/free-trial/).
#### Visual Studio et Nuget

1. Visual Studio 2012 ou ultérieur et C# sont requis. Téléchargez une version gratuite à la page [Téléchargements Visual Studio](http://www.visualstudio.com/downloads/download-visual-studio-vs.aspx).
2. Nuget 2.7 ou ultérieur. Pour obtenir la toute dernière version, consultez la page [Installation de NuGet](http://docs.nuget.org/docs/start-here/installing-nuget)
#### Création d'une base de données SQL Azure

* Consultez la page [Prise en main de Base de données SQL Microsoft SQL](http://azure.microsoft.com/documentation/articles/sql-database-get-started/).

## Téléchargement et exécution de l'exemple d'application
L'exemple d'application **Infrastructure élastique avec Base de données SQL Azure - Prise en main** présente les aspects les plus importants du développement d'applications partitionnées à l'aide de l'infrastructure élastique Base de données SQL Azure. Il s'intéresse aux principaux cas d'utilisation pour la [gestion des cartes de partition](http://go.microsoft.com/?linkid=9862595), le [routage dépendant des données](http://go.microsoft.com/?linkid=9862596) et l'[interrogation de plusieurs partitions](http://go.microsoft.com/?linkid=9862597). Pour télécharger et exécuter les exemples, procédez comme suit : 

1. Ouvrez Visual Studio et sélectionnez **Fichier -> Nouveau -> Projet**.
2. Dans la boîte de dialogue, cliquez sur **En ligne**.

    ![New Project>Online][2]
3. Cliquez ensuite sur **Visual C#** sous **Exemples**.

    ![Click Visual C#][3]
4. Dans la zone de recherche, tapez **Elastic Scale** pour trouver l'exemple. Le titre **Infrastructure élastique avec Base de données SQL Azure - Prise en main** s'affiche.

    ![Search Box][1]
 
5. Sélectionnez l'exemple, choisissez un nom et un emplacement pour le nouveau projet et appuyez sur **OK** pour créer le projet.
6. Dans la solution, ouvrez le fichier **app.config** de l'exemple de projet et suivez les instructions du fichier pour ajouter le nom du serveur de base de données SQL Azure et vos informations de connexion (nom d'utilisateur et mot de passe).
7. Générez et exécutez l'application. Lorsque cela vous est demandé, autorisez Visual Studio à restaurer les packages NuGet de la solution. Cela va vous permettre de télécharger la version la plus récente des bibliothèques clientes de l'infrastructure élastique à partir de NuGet.
8. Consultez les différentes options pour en savoir plus sur les fonctionnalités proposées par l'infrastructure élastique. Notez les étapes suivies par l'application dans la sortie de la console. N'hésitez pas à explorer le code en arrière-plan.

    ![progress][4]

Félicitations. Vous avez correctement conçu et exécuté votre première application Infrastructure élastique sur la base de données SQL Azure. Passez rapidement en revue les partitions créées par l'exemple en connectant SQL Server Management Studio à votre serveur de base de données Azure. Vous remarquerez de nouveaux exemples de bases de données de partitions, ainsi que la base de données de gestionnaire de carte de partitions créée par l'exemple.

**Remarque**   Si vous ne possédez pas SQL Server Management Studio, consultez la page [Gestion de Base de données SQL Azure à l'aide de SQL Server Management Studio](http://azure.microsoft.com/documentation/articles/sql-database-manage-azure-ssms/), où vous trouverez des instructions pour obtenir cet outil.  

### Éléments clés de l'exemple de code

1. **Gestion des partitions et des cartes de partitions** : le code illustre le fonctionnement des partitions, des plages et des mappages dans le fichier **ShardMapManagerSample.cs**. Vous trouverez des informations supplémentaires à ce sujet ici : [Gestion des cartes de partitions](http://go.microsoft.com/?linkid=9862595).  
2. **Routage dépendant des données** : le routage des transactions vers la partition appropriée est indiqué dans **DataDependentRoutingSample.cs**. Pour plus d'informations, consultez la rubrique [Routage dépendant des données](http://go.microsoft.com/?linkid=9862596). 
3. **Interrogation de plusieurs partitions** : l'interrogation de partitions est illustrée dans le fichier **MultiShardQuerySample.cs**. Pour plus d'informations, consultez la page [Interrogation de plusieurs partitions](http://go.microsoft.com/?linkid=9862597).
4. **Ajout de partitions vides** : l'ajout itératif de nouvelles partitions vides est effectué par le code du
fichier **AddNewShardsSample.cs**. Des informations détaillées à ce sujet sont disponibles ici : [Gestion des cartes de partitions](http://go.microsoft.com/?linkid=9862595).

### Autres opérations d'infrastructure élastique

1. **Fractionnement d'une partition existante** : la fonctionnalité de fractionnement des partitions est fournie via le **service de fractionnement/fusion**. Vous trouverez des informations supplémentaires sur ce service ici : [Service de fractionnement/fusion](http://go.microsoft.com/?linkid=9862795).
2. **Fusion de partitions existantes** : les fusions de partitions sont également effectuées à l'aide du **Service de fractionnement/fusion**. Pour plus d'informations, consultez la page suivante : [Service de fractionnement/fusion](http://go.microsoft.com/?linkid=9862795).   


## Coût

Les bibliothèques et modèles de service Infrastructure élastique sont gratuits. L'infrastructure élastique n'induit aucun coût supplémentaire en plus de votre utilisation d'Azure. 

Par exemple, l'exemple d'application crée des bases de données. Le coût dépend de l'édition de base de données SQL Azure choisie et de l'utilisation Azure de votre application.

Pour plus d'informations sur la tarification, consultez la page [Tarification - Base de données SQL](http://azure.microsoft.com/pricing/details/sql-database/).

## Étapes suivantes
Pour plus d'informations sur la fonctionnalité d'infrastructure élastique, consultez les pages suivantes :

* [Page de formation sur l'infrastructure élastique](./sql-database-elastic-scale-documentation-map.md) 
-    Exemples de code : 
    -    [Infrastructure élastique avec Base de données SQL Azure - Prise en main](http://code.msdn.microsoft.com/Elastic-Scale-with-Azure-a80d8dc6?SRC=VSIDE)
    -    [Infrastructure élastique avec Base de données SQL Azure - Intégration avec Entity Framework](http://code.msdn.microsoft.com/Elastic-Scale-with-Azure-bae904ba?SRC=VSIDE)
    -    [Élasticité des partitions dans le Centre de scripts](http://go.microsoft.com/?linkid=9862617)
-    Blog : [annonce concernant l'infrastructure élastique](http://go.microsoft.com/?linkid=9862608)
-    Channel 9 : [vidéo de présentation de l'infrastructure élastique](http://go.microsoft.com/?linkid=9862609)
-    Forum de discussion : [Forum Base de données SQL Azure](http://social.msdn.microsoft.com/forums/azure/home?forum=ssdsgetstarted)


<!--Anchors-->
[Exemple d'application d'infrastructure élastique]: #The-Elastic-Scale-Sample-Application
[Téléchargement et exécution de l'exemple d'application]: #Download-and-Run-the-Sample-App
[Coût]: #Cost
[Étapes suivantes]: #next-steps

<!--Image references-->
[1]: ./media/sql-database-elastic-scale-get-started/newProject.png
[2]: ./media/sql-database-elastic-scale-get-started/click-online.png
[3]: ./media/sql-database-elastic-scale-get-started/click-CSharp.png
[4]: ./media/sql-database-elastic-scale-get-started/output2.png

<!--HONumber=47-->
