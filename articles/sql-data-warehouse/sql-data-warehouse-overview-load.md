   <properties
   pageTitle="Charger des données dans SQL Data Warehouse | Microsoft Azure"
   description="Découvrez les scénarios courants de chargement des données dans SQL Data Warehouse"
   services="sql-data-warehouse"
   documentationCenter="NA"
   authors="lodipalm"
   manager="barbkess"
   editor=""/>


<tags
   ms.service="sql-data-warehouse"
   ms.devlang="NA"
   ms.topic="article"
   ms.tgt_pltfrm="NA"
   ms.workload="data-services"
   ms.date="06/21/2015"
   ms.author="lodipalm;barbkess"/>


# Charger des données dans SQL Data Warehouse
SQL Data Warehouse comporte de nombreuses options permettant de charger des données, notamment :

- Azure Data Factory
- Utilitaire de ligne de commande BCP
- PolyBase
- SQL Server Integration Services (SSIS)
- Outils de chargement de données tiers

Notez que toutes les méthodes ci-dessus peuvent être utilisées avec SQL Data Warehouse. Pour de nombreux utilisateurs, les chargements initiaux vont de plusieurs centaines de gigaoctets à plusieurs dizaines de téraoctets. Les sections ci-après fournissent des conseils sur le chargement de données initial.

## Chargement initial dans SQL Data Warehouse à partir de SQL Server 
Pour effectuer un chargement dans SQL Data Warehouse à partir d’une instance de SQL Server locale, privilégiez la procédure suivante :

1. À l’aide de l’utilitaire **BCP**, exporter les données SQL Server dans des fichiers plats 
2. Utiliser **AZCopy** ou **Import/Export** (pour les jeux de données volumineux) pour déplacer vos fichiers dans Azure
3. Configurer PolyBase pour lire vos fichiers à partir de votre compte de stockage
4. Créer des tables et charger les données avec **PolyBase**

Dans les sections suivantes, nous examinons chaque étape et fournissons des exemples du processus.

> [AZURE.NOTE]Avant de déplacer les données d’un système tel que SQL Server, nous vous suggérons de consulter les articles [Migration de votre schéma][] et [Migration de votre code][] de notre documentation.

## Exportation de fichiers avec BCP

En vue de déplacer vos fichiers vers Azure, vous devez les exporter dans des fichiers plats. Pour ce faire, privilégiez l’utilitaire de ligne de commande BCP. Si vous ne possédez pas l’utilitaire, vous pouvez le télécharger avec les [utilitaires de ligne de commande Microsoft pour SQL Server][]. Un exemple de commande BCP peut se présenter comme suit :

	bcp "<Directory><File>" -c -T -S <Server Name> -d <Database Name>

Cette commande récupère les résultats d’une requête et les exporte vers un fichier dans le répertoire de votre choix. Vous pouvez paralléliser le processus en exécutant plusieurs commandes BCP pour des tables distinctes simultanément. Cela vous permet d’exécuter jusqu’à un processus BCP par cœur de votre serveur. Vous pouvez commencer par effectuer quelques opérations de moindre envergure sur différentes configurations pour voir ce qui convient le mieux à votre environnement.

En outre, comme nous chargerons les données à l’aide de PolyBase, tous les fichiers doivent être au format UTF-8, car PolyBase ne prend pas encore en charge le format UTF-16. Vous pouvez aisément effectuer cette opération en incluant l’indicateur « -c » dans votre commande BCP ou vous pouvez également convertir les fichiers plats UTF-16 au format UTF-8 avec le code ci-dessous :

		Get-Content <input_file_name> -Encoding Unicode | Set-Content <output_file_name> -Encoding utf8
 
Une fois que vous avez exporté vos données dans des fichiers, il est temps de les déplacer vers Azure. Pour ce faire, vous pouvez utiliser AZCopy ou le service Import/Export, comme décrit dans la section suivante.


## Chargement dans Azure avec AZCopy ou Import/Export
Si le volume de données à déplacer est compris entre 5 et 10 téraoctets, voire plus, nous vous recommandons d’utiliser notre service d’expédition de disque [Import/Export][] pour effectuer le déplacement. Toutefois, nos études ont révélé qu’il est possible de déplacer aisément quelques téraoctets de données au moyen d’une connexion Internet publique avec AZCopy. Ce processus peut également être accéléré ou étendu avec ExpressRoute.

La procédure suivante décrit comment déplacer des données locales vers un compte Azure Storage avec AZCopy. Si vous n’avez pas un compte Azure Storage dans la même région, vous pouvez en créer un en suivant la [documentation Azure Storage][]. Vous pouvez également charger les données à partir d’un compte de stockage dans une autre région, mais les performances dans ce cas ne sont pas optimales.

> [AZURE.NOTE]Cette documentation suppose que vous avez installé l’utilitaire de ligne de commande AZCopy et que vous pouvez l’exécuter avec PowerShell. Si ce n’est pas le cas, exécutez les [instructions d’installation d’AZCopy][].

Une fois créé un ensemble de fichiers à l’aide de BCP, AzCopy peut simplement être exécuté à partir d’Azure PowerShell ou à l’aide d’un script PowerShell. De façon générale, l’invite de commandes nécessaire à l’exécution d’AZCopy se présente comme suit :

	 AZCopy /Source:<File Location> /Dest:<Storage Container Location> /destkey:<Storage Key> /Pattern:<File Name> /NC:256

En outre, nous recommandons les meilleures pratiques suivantes pour le chargement avec AZCopy :


+ **Connexions simultanées** : en plus d’augmenter le nombre d’opérations d’AZCopy qui s’exécutent en même temps, vous pouvez paralléliser davantage l’opération AZCopy elle-même en définissant le paramètre /NC, qui ouvre une série de connexions simultanées vers la destination. Bien que le paramètre puisse être défini sur 512, nous avons constaté que le transfert de données est optimal avec la valeur 256 et vous recommandons de tester différentes valeurs pour déterminer celle qui convient le mieux pour votre configuration.

+ **ExpressRoute** : comme indiqué ci-dessus, ce processus peut être accéléré si ExpressRoute est activé. Vous trouverez une vue d’ensemble d’ExpressRoute et des étapes à configurer dans la [documentation sur ExpressRoute][].

+ **Structure des dossiers** : pour faciliter le transfert avec PolyBase, assurez-vous que chaque table est mappée à son propre dossier. Cela réduit et simplifie les étapes de chargement ultérieures avec PolyBase. Toutefois, l’impact est nul si une table est subdivisée en plusieurs fichiers ou sous-répertoires au sein du dossier.
	 

## Configuration de PolyBase 

Les données se trouvant désormais dans des objets blob Azure Storage, nous allons les importer dans votre instance SQL Data Warehouse à l’aide de PolyBase. Les étapes ci-dessous ne concernent que la configuration et bon nombre d’entre elles ne doivent être effectuées qu’une fois par compte de stockage, utilisateur ou instance SQL Data Warehouse. Ces étapes sont également présentées plus en détail dans notre documentation [Charger des données avec PolyBase][].

1. **Créez une clé principale de base de données.** Cette opération ne doit être effectuée qu’une fois par base de données. 

2. **Créez un fichier d’informations d’identification de niveau base de données.** Cette opération ne doit être créée que si vous cherchez à créer des informations d’identification ou un utilisateur ; sinon vous pouvez utiliser des informations d’identification existantes.

3. **Créez un format de fichier externe.** Les formats de fichiers externes étant également réutilisables, vous devez uniquement en créer un si vous chargez un nouveau type de fichier.

4. **Créez une source de données externe.** Quand l’opération pointe vers un compte de stockage, une source de données externe peut être utilisée si le chargement s’effectue à partir du même conteneur. Pour le paramètre « LOCATION », utilisez un emplacement qui présente le format suivant : « wasbs://mycontainer@ test.blob.core.windows.net/path ».

		-- Creating master key
		CREATE MASTER KEY;

		-- Creating a database scoped credential
		CREATE DATABASE SCOPED CREDENTIAL <Credential Name> WITH IDENTITY = '<User Name>', 
    	Secret = '<Azure Storage Key>';

		-- Creating external file format (delimited text file)
		CREATE EXTERNAL FILE FORMAT text_file_format 
		WITH (
		    FORMAT_TYPE = DELIMITEDTEXT, 
		    FORMAT_OPTIONS (
		        FIELD_TERMINATOR ='|', 
		        USE_TYPE_DEFAULT = TRUE
		    )
		);

		--Creating an external data source
		CREATE EXTERNAL DATA SOURCE azure_storage 
		WITH (
	    	TYPE = HADOOP, 
	        LOCATION ='wasbs://<Container>@<Blob Path>’,
	        CREDENTIAL = <Credential Name>
		;


Votre compte de stockage étant correctement configuré, vous pouvez procéder au chargement de vos données dans SQL Data Warehouse.

## Chargement des données avec PolyBase 
Après avoir configuré PolyBase, vous pouvez charger les données directement dans SQL Data Warehouse en créant simplement une table externe qui pointe vers vos données dans le stockage, puis en mappant ces données à une nouvelle table dans SQL Data Warehouse. Vous pouvez accomplir cette opération à l’aide des deux commandes simples ci-dessous.

1. Utilisez la commande « CREATE EXTERNAL TABLE » pour définir la structure de vos données. Pour capturer l’état de vos données rapidement et efficacement, nous vous recommandons de générer la table SQL Server avec un script dans SSMS, puis d’effectuer des ajustements manuels en fonction de la table externe. Une fois une table externe créée dans Azure, elle continue de pointer vers le même emplacement, même si les données sont mises à jour ou que des données sont ajoutées.  

		-- Creating external table pointing to file stored in Azure Storage
		CREATE EXTERNAL TABLE <External Table Name> (
		    <Column name>, <Column type>, <NULL/NOT NULL>
		)
		WITH (LOCATION='<Folder Path>',
		      DATA_SOURCE = <Data Source>,
		      FILE_FORMAT = <File Format>,      
		);

2. Chargez les données avec une instruction « CREATE TABLE...AS SELECT ».

		CREATE TABLE <Table Name> 
		WITH (
    		CLUSTERED COLUMNSTORE INDEX
    		)
		AS SELECT * from <External Table Name>;

	Notez que vous pouvez également charger une partie des lignes d’une table à l’aide d’une instruction SELECT plus détaillée. Toutefois, comme PolyBase n’envoie pas de calcul supplémentaire aux comptes de stockage à ce stade, si vous chargez une partie des lignes avec une instruction SELECT, cette opération n’est pas plus rapide que le chargement de la totalité du jeu de données.

En plus de l’instruction « CREATE TABLE...AS SELECT », vous pouvez également charger des données à partir de tables externes dans des tables préexistantes avec une instruction « INSERT...INTO ».

## Étapes suivantes
Pour obtenir des conseils supplémentaires sur le développement, consultez la [vue d’ensemble sur le développement][].

<!--Image references-->

<!--Article references-->
[Load data with bcp]: sql-data-warehouse-load-with-bcp.md
[Charger des données avec PolyBase]: sql-data-warehouse-load-with-polybase.md
[solution partners]: sql-data-warehouse-solution-partners.md
[vue d’ensemble sur le développement]: sql-data-warehouse-overview-develop.md
[Migration de votre schéma]: sql-data-warehouse-migrate-schema.md
[Migration de votre code]: sql-data-warehouse-migrate-code.md

<!--MSDN references-->
[supported source/sink]: https://msdn.microsoft.com/library/dn894007.aspx
[copy activity]: https://msdn.microsoft.com/library/dn835035.aspx
[SQL Server destination adapter]: https://msdn.microsoft.com/library/ms141237.aspx
[SSIS]: https://msdn.microsoft.com/library/ms141026.aspx

<!--Other Web references-->
[instructions d’installation d’AZCopy]: https://azure.microsoft.com/fr-fr/documentation/articles/storage-use-azcopy/
[utilitaires de ligne de commande Microsoft pour SQL Server]: http://www.microsoft.com/fr-fr/download/details.aspx?id=36433
[Import/Export]: https://azure.microsoft.com/fr-fr/documentation/articles/storage-import-export-service/
[documentation Azure Storage]: https://azure.microsoft.com/fr-fr/documentation/articles/storage-create-storage-account/
[documentation sur ExpressRoute]: http://azure.microsoft.com/documentation/services/expressroute/

<!---HONumber=August15_HO6-->