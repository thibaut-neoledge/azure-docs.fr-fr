<properties
	pageTitle="Prise en main des requêtes de base de données élastique"
	description="comment utiliser la requête de base de données élastique"
	services="sql-database"
	documentationCenter=""  
	manager="jeffreyg"
	authors="sidneyh"/>

<tags
	ms.service="sql-database"
	ms.workload="sql-database"
	ms.tgt_pltfrm="na"
	ms.devlang="na"
	ms.topic="article"
	ms.date="06/23/2015"
	ms.author="sidneyh" />

# Prise en main des requêtes de base de données élastique

Les requêtes de bases de données élastiques (en version préliminaire) pour Base de données SQL Azure vous permettent d’exécuter des requêtes T-SQL qui s’étendent sur plusieurs bases de données via un seul point de connexion. Pour plus d’informations sur la fonctionnalité de requête de base de données flexible, veuillez consulter la [page de vue d’ensemble des fonctionnalités](sql-database-elastic-query-overview.md).

Cette rubrique étend l’exemple de la rubrique [Prise en main des outils de base de données élastique](sql-database-elastic-scale-get-started.md). A l’issue du processus, vous serez capable de configurer et d’utiliser une base de données SQL Azure pour exécuter des requêtes qui s’étendent sur plusieurs bases de données associées.
## Conditions préalables

Téléchargez et exécutez l’exemple de la rubrique [Prise en main des outils de base de données élastique](sql-database-elastic-scale-get-started.md).

## Créez un gestionnaire des cartes de partitions à l’aide de l’exemple d’application

Ici vous allez créer un gestionnaire des cartes de partitions avec plusieurs partitions, puis insérer des données dans les partitions. Si vos partitions comportent déjà des données partitionnées, vous pouvez ignorer ces étapes et passer à la section suivante.

1. Créez et exécutez l’exemple d’application de la rubrique **Prise en main des outils de base de données élastique**. Suivez la procédure jusqu’à l’étape 7 dans la section [Télécharger et exécuter l’exemple d’application](sql-database-elastic-scale-get-started.md#Getting-started-with-elastic-database-tools). À la fin de l’étape 7, vous verrez l’invite de commande suivante :

	![invite de commande][1]

2.  Dans la fenêtre de commande, entrez « 1 » et appuyez sur **Entrée**. Cela crée le gestionnaire des cartes de partitions et ajoute deux partitions sur le serveur. Entrez « 3 », puis appuyez sur **Entrée**. Répétez l’action quatre fois. Cela permet d’insérer des lignes d’exemples de données dans vos partitions.
3.  Le [portail Azure en version préliminaire](https://portal.azure.com) doit afficher trois nouvelles bases de données dans votre serveur v12 :

	![Confirmation Visual Studio][2]

	À ce stade, les requêtes de bases de données croisées sont prises en charge via les bibliothèques clientes de bases de données élastiques. Par exemple, utilisez l’option 4 dans la fenêtre de commande. Les résultats d’une requête de plusieurs partitions sont toujours un élément **UNION ALL** des résultats de toutes les partitions.

	Dans la section suivante, nous créons un exemple de point de terminaison de base de données qui prend en charge une interrogation plus riche des données entre les partitions.

## Créez une base de données de requête élastique

1. Ouvrez la [version préliminaire du portail Azure](https://portal.azure.com) et connectez-vous.
2. Créez une nouvelle base de données SQL Azure dans le même serveur que votre partition configurée. Nommez la base de données « ElasticDBQuery ». Pour le niveau de tarification, vous devez sélectionner l’une des offres premium. La requête de base de données élastique n’est actuellement disponible qu’au niveau premium.

	![Portail Azure et tarification][3]

	Remarque : vous pouvez utiliser une base de données premium existante. Si c’est le cas, il ne doit pas s’agir de l’une des partitions sur laquelle vous souhaitez exécuter les requêtes. Cette base de données sera utilisée pour la création d’objets de métadonnées pour une requête de base de données élastique.


## Créez des objets de base de données

### Clé principale et informations d’identification de la base de données

Celles-ci sont utilisées pour se connecter au gestionnaire des cartes de partition et aux partitions :

1. Ouvrez SQL Server Management Studio ou SQL Server Data Tools dans Visual Studio.
2. Connectez-vous à la base de données ElasticDBQuery et exécutez les commandes T-SQL suivantes :

		CREATE MASTER KEY ENCRYPTION BY PASSWORD = '<password>';

		CREATE CREDENTIAL ElasticDBQueryCred ON DATABASE
		WITH IDENTITY = '<username>',
		SECRET = '<password>';

	le nom d’utilisateur et le mot de passe doivent être les mêmes que les informations de connexion utilisées à l’étape 6 de la rubrique [Télécharger et exécuter l’exemple d’application](sql-database-elastic-scale-get-started.md#Getting-started-with-elastic-database-tools) dans [Prise en main des outils de base de données élastique](sql-database-elastic-scale-get-started.md).


### Sources de données externes

Pour créer une source de données externe, exécutez la commande suivante sur la base de données ElasticDBQuery :

	CREATE EXTERNAL DATA SOURCE MyElasticDBQueryDataSrc WITH
      (TYPE = SHARD_MAP_MANAGER,
      LOCATION = '<server_name>.database.windows.net',
      DATABASE_NAME = 'ElasticScaleStarterKit_ShardMapManagerDb',
	  CREDENTIAL = ElasticDBQueryCred,
 	  SHARD_MAP_NAME = 'CustomerIDShardMap'
    ) ;

 « CustomerIDShardMap » est le nom de la carte de partition, si vous avez créé la carte de partition et le gestionnaire des cartes de partition à l’aide de l’exemple d’outils souples de la base de données. Toutefois, si vous avez utilisé l’installation personnalisée pour cet exemple, le nom de carte de partition doit correspondre au nom choisi dans l’application.

### Tables externes

Créez une table externe qui correspond à la table des clients sur les partitions en exécutant la commande suivante sur la base de données ElasticDBQuery :

	CREATE EXTERNAL TABLE [dbo].[Customers]
	( [CustomerId] [int] NOT NULL,
	  [Name] [nvarchar](256) NOT NULL,
	  [RegionId] [int] NOT NULL)
	WITH
	( DATA_SOURCE = MyElasticDBQueryDataSrc,
      DISTRIBUTION = SHARDED([CustomerId])
	) ;

## Exécutez un exemple de requête T-SQL de base de données élastique

Après avoir défini vos tables externes et votre source de données externe, vous pouvez utiliser l’ensemble T-SQL sur vos tables externes.

Exécutez cette requête sur la base de données ElasticDBQuery :

	select count(CustomerId) from [dbo].[Customers]

Vous remarquerez que la requête regroupe les résultats de toutes les partitions et donne le résultat suivant :

![Détails des résultats][4]

## Importez les résultats de la requête de base de données élastique dans Excel

 Vous pouvez importer les résultats d’une requête vers un fichier Excel.

1. Lancez Excel 2013.
2. 	Accédez au ruban **Données**.
3. 	Cliquez sur **À partir d’autres sources** et sur **À partir de SQL Server**.

	![Importation au format Excel à partir d’autres sources][5]
4. 	Dans l’**Assistant de connexion de données** saisissez le nom du serveur et les informations de connexion. Cliquez ensuite sur **Suivant**.
5. 	Dans la boîte de dialogue **Sélectionner la base de données qui contient les données que vous souhaitez**, sélectionnez la base de données **ElasticDBQuery**.
6. 	Sélectionnez la table **Clients** dans la liste et cliquez sur **Suivant**. Puis, cliquez sur **Terminer**.
7. 	Dans le formulaire **Importer des données**, sous **Sélectionner le mode d’affichage des données dans votre classeur**, sélectionnez **Table** et cliquez sur **OK**.

Toutes les lignes de la table **Clients**, stockées dans des partitions différentes, remplissent la feuille Excel.

## Étapes suivantes
Vous pouvez maintenant utiliser les fonctions de données puissantes d’Excel. Vous pouvez utiliser la chaîne de connexion avec votre nom de serveur, votre nom de base de données et les informations d’identification pour vous connecter vos outils d’intégration BI et de données dans la base de données de requête élastique. Assurez-vous que SQL Server est pris en charge comme source de données pour votre outil. Vous pouvez vous traiter la base de données de requête élastique et les tables externes comme n’importe quelles bases de données SQL Server et tables SQL Server auxquelles vous vous connectez avec votre outil.

### Coût
La fonction de requête de base de données élastique n’entraîne aucuns frais supplémentaires. Toutefois, pour l’instant, cette fonctionnalité n’est disponible que sur les bases de données premium comme point de terminaison, mais les partitions peuvent provenir de n’importe quel niveau de service.

Pour plus d’informations sur la tarification, consultez la page [Tarification - Base de données SQL](http://azure.microsoft.com/pricing/details/sql-database/).


[AZURE.INCLUDE [elastic-scale-include](../../includes/elastic-scale-include.md)]

<!--Image references-->
[1]: ./media/sql-database-elastic-query-getting-started/cmd-prompt.png
[2]: ./media/sql-database-elastic-query-getting-started/portal.png
[3]: ./media/sql-database-elastic-query-getting-started/tiers.png
[4]: ./media/sql-database-elastic-query-getting-started/details.png
[5]: ./media/sql-database-elastic-query-getting-started/exel-sources.png
<!--anchors-->

<!---HONumber=July15_HO2-->