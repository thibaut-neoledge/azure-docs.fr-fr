<properties 
	pageTitle="Importation de données vers DocumentDB | Azure" 
	description="Découvrez comment utiliser l'outil de migration de données open source DocumentDB pour importer des données depuis différentes sources, y compris des fichiers JSON, des fichiers CSV, SQL, MongoDB, le stockage de tables Azure, Amazon DynamoDB et des collections DocumentDB." 
	services="documentdb" 
	authors="stephbaron" 
	manager="johnmac" 
	editor="monicar" 
	documentationCenter=""/>

<tags 
	ms.service="documentdb" 
	ms.workload="data-services" 
	ms.tgt_pltfrm="na" 
	ms.devlang="na" 
	ms.topic="article" 
	ms.date="07/10/2015" 
	ms.author="stbaro"/>

# Importation de données vers DocumentDB #

Cet article vous montre comment utiliser l'outil de migration de données open source DocumentDB pour importer des données dans [Microsoft Azure DocumentDB](http://azure.microsoft.com/services/documentdb/) à partir de différentes sources, y compris des fichiers JSON, des fichiers CSV, SQL, MongoDB, le stockage de tables Azure, Amazon DynamoDB et les collections DocumentDB.

Après avoir lu cet article, vous serez en mesure de répondre aux questions suivantes :

-	Comment puis-je importer les données d’un fichier JSON vers DocumentDB ?
-	Comment puis-je importer les données d’un fichier CSV vers DocumentDB ?
-	Comment puis-je importer des données SQL Server vers DocumentDB ?
-	Comment puis-je importer des données MongoDB vers DocumentDB ?
-	Comment puis-je importer des données à partir du stockage de tables Azure vers DocumentDB ?
-	Comment puis-je importer des données à partir d’Amazon DynamoDB vers DocumentDB ?
-	Comment puis-je importer des données à partir de HBase vers DocumentDB ?
-	Comment puis-je migrer des données entre des collections DocumentDB ?

##<a id="Prerequisites"></a>Configuration requise ##

Avant de suivre les instructions de cet article, vérifiez que les éléments suivants sont installés :

- [Microsoft .NET Framework 4.51](http://www.microsoft.com/net/downloads) ou une version ultérieure.

##<a id="Overviewl"></a>Vue d'ensemble de l'outil de migration de données DocumentDB ##

L'outil de migration de données DocumentDB est une solution open source permettant d’importer des données dans DocumentDB à partir de différentes sources, notamment :

- Fichiers JSON
- MongoDB
- SQL Server
- Fichiers CSV
- Stockage de tables Azure
- Amazon DynamoDB
- HBase
- Collections DocumentDB

L'outil d'importation inclut une interface utilisateur graphique (dtui.exe) et peut aussi être piloté à partir de la ligne de commande (dt.exe). En fait, il existe une option pour générer la commande associée après avoir configuré une importation via l'interface utilisateur. Des données sources tabulaires (par exemple, des fichiers SQL Server ou CSV) peuvent être transformées de manière à ce que des relations hiérarchiques (sous-documents) puissent être créées pendant l'importation. Poursuivez votre lecture pour en savoir plus sur les options sources, les exemples de lignes de commande pour l’importation depuis chaque source, les options cibles et l'affichage des résultats d’importation.


##<a id="Install"></a>Installation de l'outil de migration de données DocumentDB ##

Le code source de l'outil de migration est disponible sur GitHub dans [ce référentiel](https://github.com/azure/azure-documentdb-datamigrationtool) et une version compilée est disponible dans le [Centre de téléchargement Microsoft](http://www.microsoft.com/downloads/details.aspx?FamilyID=cda7703a-2774-4c07-adcc-ad02ddc1a44d). Vous pouvez compiler la solution ou simplement télécharger et extraire la version compilée dans un répertoire de votre choix. Exécutez ensuite l’un des fichiers suivants :

- Dtui.exe : version de l'interface graphique de l'outil
- DT.exe : version en ligne de commande de l'outil

##<a id="JSON"></a>Importation de fichiers JSON ##

L'option d'importateur source du fichier JSON vous permet d'importer un ou plusieurs fichiers JSON ou des fichiers JSON qui contiennent chacun un tableau de documents JSON. Quand vous ajoutez des dossiers qui contiennent des fichiers JSON à importer, vous avez la possibilité de rechercher des fichiers de manière récursive dans les sous-dossiers.

![Capture d’écran des options sources du fichier JSON](./media/documentdb-import-data/jsonsource.png)

Voici quelques exemples de lignes de commande pour importer des fichiers JSON :

	#Import a single JSON file	
	dt.exe /s:JsonFile /s.Files:.\Sessions.json /t:DocumentDBBulk /t.ConnectionString:"AccountEndpoint=<DocumentDB Endpoint>;AccountKey=<DocumentDB Key>;Database=<DocumentDB Database>;" /t.Collection:Sessions /t.CollectionTier:S3

	#Import a directory of JSON files
	dt.exe /s:JsonFile /s.Files:C:\TESessions*.json /t:DocumentDBBulk /t.ConnectionString:" AccountEndpoint=<DocumentDB Endpoint>;AccountKey=<DocumentDB Key>;Database=<DocumentDB Database>;" /t.Collection:Sessions /t.CollectionTier:S3

	#Import a directory (including sub-directories) of JSON files
	dt.exe /s:JsonFile /s.Files:C:\LastFMMusic***.json /t:DocumentDBBulk /t.ConnectionString:" AccountEndpoint=<DocumentDB Endpoint>;AccountKey=<DocumentDB Key>;Database=<DocumentDB Database>;" /t.Collection:Music /t.CollectionTier:S3

	#Import a directory (single), directory (recursive), and individual JSON files
	dt.exe /s:JsonFile /s.Files:C:\Tweets*.*;C:\LargeDocs***.*;C:\TESessions\Session48172.json;C:\TESessions\Session48173.json;C:\TESessions\Session48174.json;C:\TESessions\Session48175.json;C:\TESessions\Session48177.json /t:DocumentDBBulk /t.ConnectionString:"AccountEndpoint=<DocumentDB Endpoint>;AccountKey=<DocumentDB Key>;Database=<DocumentDB Database>;" /t.Collection:subs /t.CollectionTier:S3

	#Import a single JSON file and partition the data across 4 collections
	dt.exe /s:JsonFile /s.Files:D:\\CompanyData\\Companies.json /t:DocumentDBBulk /t.ConnectionString:"AccountEndpoint=<DocumentDB Endpoint>;AccountKey=<DocumentDB Key>;Database=<DocumentDB Database>;" /t.Collection:comp[1-4] /t.PartitionKey:name /t.CollectionTier:S3

##<a id="MongoDB"></a>Importation à partir de MongoDB ##

L'option d'importateur source MongoDB vous permet d’importer à partir d'une collection MongoDB individuelle et de filtrer éventuellement les documents à l'aide d'une requête et/ou de modifier la structure du document à l'aide d'une projection.

![Capture d’écran des options sources MongoDB](./media/documentdb-import-data/mongodbsource.png)

La chaîne de connexion est au format MongoDB standard :

	mongodb://<dbuser>:<dbpassword>@<host>:<port>/<database>

> [AZURE.NOTE]Utilisez la commande Verify pour vous assurer que l'instance MongoDB spécifiée dans le champ de la chaîne de connexion est accessible.

Saisissez le nom de la collection depuis laquelle les données seront importées. Vous pouvez éventuellement spécifier ou fournir un fichier pour une requête (par exemple, {pop: {$gt: 5000}}) et/ou une projection (par exemple, {loc:0}) pour filtrer et mettre en forme les données à importer.

Voici quelques exemples de ligne de commande pour l’importation depuis MongoDB :

	#Import all documents from a MongoDB collection
	dt.exe /s:MongoDB /s.ConnectionString:mongodb://<dbuser>:<dbpassword>@<host>:<port>/<database> /s.Collection:zips /t:DocumentDBBulk /t.ConnectionString:"AccountEndpoint=<DocumentDB Endpoint>;AccountKey=<DocumentDB Key>;Database=<DocumentDB Database>;" /t.Collection:BulkZips /t.IdField:_id /t.CollectionTier:S3

	#Import documents from a MongoDB collection which match the query and exclude the loc field
	dt.exe /s:MongoDB /s.ConnectionString:mongodb://<dbuser>:<dbpassword>@<host>:<port>/<database> /s.Collection:zips /s.Query:{pop:{$gt:50000}} /s.Projection:{loc:0} /t:DocumentDBBulk /t.ConnectionString:"AccountEndpoint=<DocumentDB Endpoint>;AccountKey=<DocumentDB Key>;Database=<DocumentDB Database>;" /t.Collection:BulkZipsTransform /t.IdField:_id/t.CollectionTier:S3

##<a id="MongoDBExport"></a>Importation de fichiers d'exportation MongoDB ##

L’option d’importateur source du fichier JSON d’exportation MongoDB vous permet d’importer un ou plusieurs fichiers JSON générés depuis l’utilitaire mongoexport.

![Capture d’écran des options sources d’exportation MongoDB](./media/documentdb-import-data/mongodbexportsource.png)

Lorsque vous ajoutez des dossiers qui contiennent des fichiers JSON d’exportation à importer, vous avez la possibilité de rechercher des fichiers de manière récursive dans les sous-dossiers.

Voici un exemple de ligne de commande pour importer à partir de fichiers JSON d'exportation MongoDB :

	dt.exe /s:MongoDBExport /s.Files:D:\mongoemployees.json /t:DocumentDBBulk /t.ConnectionString:"AccountEndpoint=<DocumentDB Endpoint>;AccountKey=<DocumentDB Key>;Database=<DocumentDB Database>;" /t.Collection:employees /t.IdField:_id /t.Dates:Epoch /t.CollectionTier:S3

##<a id="SQL"></a>Importation depuis SQL Server ##

L’option d’importateur source SQL vous permet d'importer à partir d'une base de données SQL Server individuelle et de filtrer éventuellement les enregistrements à importer à l'aide d'une requête. De plus, vous pouvez modifier la structure du document en spécifiant un séparateur d'imbrication (plus d’informations dans un instant).

![Capture d’écran des options sources SQL](./media/documentdb-import-data/sqlexportsource.png)

Le format de la chaîne de connexion est le format de chaîne de connexion SQL standard.

> [AZURE.NOTE]Utilisez la commande Verify pour vous assurer que l'instance SQL Server spécifiée dans le champ de la chaîne de connexion est accessible.

La propriété du séparateur d'imbrication est utilisée pour créer des relations hiérarchiques (sous-documents) lors de l'importation. Examinez la requête SQL suivante :

*select CAST(BusinessEntityID AS varchar) as Id, Name, AddressType as [Address.AddressType], AddressLine1 as [Address.AddressLine1], City as [Address.Location.City], StateProvinceName as [Address.Location.StateProvinceName], PostalCode as [Address.PostalCode], CountryRegionName as [Address.CountryRegionName] from Sales.vStoreWithAddresses WHERE AddressType='Main Office'*

Cette requête retourne les résultats (partiels) suivants :

![Capture d’écran des résultats de requête SQL](./media/documentdb-import-data/sqlqueryresults.png)

Notez les alias tels que Address.AddressType et Address.Location.StateProvinceName. En spécifiant un séparateur d'imbrication de « . », l'outil d'importation crée les sous-documents Address et Address.Location lors de l'importation. Voici un exemple de document qui en résulte dans DocumentDB :

*{
  « ID » : « 956 »,
  « Nom » : « Service et vente au détail »,
  « Adresse »: {
    « AddressType »: « Siège »,
    « AddressLine1 »: « #500-75 o ' Connor Street »,
    « Lieu »: {
      « Ville »: « Ottawa »,
      « StateProvinceName »: « Ontario »
    },
    « Code postal »: « K4B 1S2 »,
    « CountryRegionName »: « Canada »
  }
}*
 
Voici quelques exemples de lignes de commande pour l’importation depuis SQL Server :

	#Import records from SQL which match a query	
	dt.exe /s:SQL /s.ConnectionString:"Data Source=<server>;Initial Catalog=AdventureWorks;User Id=advworks;Password=<password>;" /s.Query:"select CAST(BusinessEntityID AS varchar) as Id, * from Sales.vStoreWithAddresses WHERE AddressType='Main Office'" /t:DocumentDBBulk /t.ConnectionString:" AccountEndpoint=<DocumentDB Endpoint>;AccountKey=<DocumentDB Key>;Database=<DocumentDB Database>;" /t.Collection:Stores /t.IdField:Id /t.CollectionTier:S3

	#Import records from sql which match a query and create hierarchical relationships
	dt.exe /s:SQL /s.ConnectionString:"Data Source=<server>;Initial Catalog=AdventureWorks;User Id=advworks;Password=<password>;" /s.Query:"select CAST(BusinessEntityID AS varchar) as Id, Name, AddressType as [Address.AddressType], AddressLine1 as [Address.AddressLine1], City as [Address.Location.City], StateProvinceName as [Address.Location.StateProvinceName], PostalCode as [Address.PostalCode], CountryRegionName as [Address.CountryRegionName] from Sales.vStoreWithAddresses WHERE AddressType='Main Office'" /s.NestingSeparator:. /t:DocumentDBBulk /t.ConnectionString:" AccountEndpoint=<DocumentDB Endpoint>;AccountKey=<DocumentDB Key>;Database=<DocumentDB Database>;" /t.Collection:StoresSub /t.IdField:Id /t.CollectionTier:S3

##<a id="CSV"></a>Importation de fichiers CSV ##

L'option d'importateur source du fichier CSV vous permet d'importer un ou plusieurs fichiers CSV. Quand vous ajoutez des dossiers qui contiennent des fichiers CSV à importer, vous avez la possibilité de rechercher des fichiers de manière récursive dans les sous-dossiers.

![Capture d’écran des options sources CSV](media/documentdb-import-data/csvsource.png)

De même que pour la source SQL, la propriété du séparateur d'imbrication peut être utilisée pour créer des relations hiérarchiques (sous-documents) lors de l'importation. Prenez en compte la ligne d'en-tête et les lignes de données du CSV suivant :

![Capture d’écran des exemples d’enregistrement CSV](./media/documentdb-import-data/csvsample.png)

Notez les alias tels que DomainInfo.Domain_Name et RedirectInfo.Redirecting. En spécifiant un séparateur d'imbrication de « . », l'outil d'importation crée les sous-documents DomainInfo et RedirectInfo lors de l'importation. Voici un exemple de document qui en résulte dans DocumentDB :

*{
  « DomainInfo » : {
    « Domain_name » : « ACUS.GOV »,
    « Domain_Name_Address » : « http://www.ACUS.GOV »
  },
  « Agence fédérale » : « Conférence administrative des États-Unis »,
  « RedirectInfo » : {
    « Redirection » : « 0 »,
    « Redirect_Destination » : « »
  },
  « ID » : « 9cc565c5-ebcd-1c03-ebd3-cc3e2ecd814d »
}*

L'outil d'importation va tenter de déduire les informations de type pour les valeurs sans guillemets dans les fichiers CSV (les valeurs entre guillemets sont toujours traitées comme des chaînes). Les types sont identifiés dans l'ordre suivant : nombre, date et heure, valeur booléenne.

Deux autres points sont à prendre en considération concernant l'importation CSV :

1.	Par défaut, les valeurs sans guillemets sont toujours privées de leurs tabulations et espaces, tandis que les valeurs entre guillemets sont conservées telles quelles. Ce comportement peut être remplacé en cochant la case Trim quoted values (Rogner les valeurs entre guillemets) ou à l'aide de l'option de ligne de commande /s.TrimQuoted.

2.	Par défaut, une valeur null sans guillemets est considérée comme une valeur null. Ce comportement peut être remplacé (c'est-à-dire, traiter une valeur null sans guillemets comme une chaîne « null ») en cochant la case Treat unquoted NULL as string (Traiter les valeurs null sans guillemets comme des chaînes) ou à l'aide de l'option de ligne de commande /s.NoUnquotedNulls.


Voici un exemple de ligne de commande pour une importation CSV :

	dt.exe /s:CsvFile /s.Files:.\Employees.csv /t:DocumentDBBulk /t.ConnectionString:"AccountEndpoint=<DocumentDB Endpoint>;AccountKey=<DocumentDB Key>;Database=<DocumentDB Database>;" /t.Collection:Employees /t.IdField:EntityID /t.CollectionTier:S3

##<a id="AzureTableSource"></a>Importation depuis le stockage de tables Azure ##

L'option d’importateur source de stockage de tables Azure vous permet d'importer à partir d'une table de stockage de tables Azure individuelle et de filtrer éventuellement les entités de table à importer.

![Capture d’écran des options sources de stockage de tables Azure](./media/documentdb-import-data/azuretablesource.png)

Le format de la chaîne de connexion de stockage de tables Azure est :

	DefaultEndpointsProtocol=<protocol>;AccountName=<Account Name>;AccountKey=<Account Key>;

> [AZURE.NOTE]Utilisez la commande Verify pour vous assurer que l'instance de stockage de tables Azure spécifiée dans le champ de la chaîne de connexion est accessible.

Saisissez le nom de la table Azure depuis laquelle les données seront importées. Vous pouvez éventuellement spécifier un [filtre](https://msdn.microsoft.com/library/azure/ff683669.aspx).

L'option d’importateur source de stockage de tables Azure dispose des options supplémentaires suivantes :

1. Inclusion des champs internes 
	2. Tous : inclure tous les champs internes (PartitionKey, RowKey et Timestamp)
	3. Aucun : exclure tous les champs internes
	4. RowKey : inclure uniquement le champ RowKey
3. Sélection des colonnes
	1. Les filtres de stockage de tables Azure ne prennent pas en charge les projections. Si vous souhaitez importer uniquement des propriétés d'entité de table Azure spécifiques, ajoutez-les à la liste Sélection des colonnes. Toutes les autres propriétés d'entité seront ignorées.

Voici un exemple de ligne de commande pour importer depuis le stockage de tables Azure :

	dt.exe /s:AzureTable /s.ConnectionString:"DefaultEndpointsProtocol=https;AccountName=<Account Name>;AccountKey=<Account Key>" /s.Table:metrics /s.InternalFields:All /s.Filter:"PartitionKey eq 'Partition1' and RowKey gt '00001'" /s.Projection:ObjectCount;ObjectSize  /t:DocumentDBBulk /t.ConnectionString:" AccountEndpoint=<DocumentDB Endpoint>;AccountKey=<DocumentDB Key>;Database=<DocumentDB Database>;" /t.Collection:metrics /t.CollectionTier:S3

##<a id="DynamoDBSource"></a>Importation à partir d’Amazon DynamoDB ##

L’option d’importateur source d’Amazon DynamoDB vous permet d'importer à partir d'une table d’Amazon DynamoDB et de filtrer éventuellement les entités à importer. Plusieurs modèles sont fournis pour faciliter au maximum la configuration d'une importation.

![Capture d’écran des options sources Amazon DynamoDB](./media/documentdb-import-data/dynamodbsource1.png)

![Capture d’écran des options sources Amazon DynamoDB](./media/documentdb-import-data/dynamodbsource2.png)

Le format de la chaîne de connexion Amazon DynamoDB est :

	ServiceURL=<Service Address>;AccessKey=<Access Key>;SecretKey=<Secret Key>;

> [AZURE.NOTE]Utilisez la commande Verify pour vous assurer que l'instance Amazon DynamoBD spécifiée dans le champ de la chaîne de connexion est accessible.

Voici un exemple de ligne de commande pour importer à partir d'Amazon DynamoDB :

	dt.exe /s:DynamoDB /s.ConnectionString:ServiceURL=https://dynamodb.us-east-1.amazonaws.com;AccessKey=<accessKey>;SecretKey=<secretKey> /s.Request:"{   """TableName""": """ProductCatalog""" }" /t:DocumentDBBulk /t.ConnectionString:"AccountEndpoint=<DocumentDB Endpoint>;AccountKey=<DocumentDB Key>;Database=<DocumentDB Database>;" /t.Collection:catalogCollection /t.CollectionTier:S3

##<a id="BlobImport"></a>Importation des fichiers à partir du stockage d’objets blob Azure##

Les options d’importateur source du fichier JSON, du fichier d'exportation MongoDB et du fichier CSV vous permettent d'importer un ou plusieurs fichiers à partir du stockage d’objets blob Azure. Après avoir spécifié l’URL d’un conteneur d'objets blob et une clé de compte, fournissez simplement une expression régulière pour sélectionner le(s) fichier(s) à importer.

![Capture d’écran des options sources du fichier blob](./media/documentdb-import-data/blobsource.png)

Voici un exemple de ligne de commande pour importer des fichiers JSON à partir du stockage d’objets blob Azure :

	dt.exe /s:JsonFile /s.Files:"blobs://<account key>@account.blob.core.windows.net:443/importcontainer/.*" /t:DocumentDBBulk /t.ConnectionString:"AccountEndpoint=<DocumentDB Endpoint>;AccountKey=<DocumentDB Key>;Database=<DocumentDB Database>;" /t.Collection:doctest

##<a id="DocumentDBSource"></a>Importation à partir de DocumentDB ##

L'option de l'importateur source DocumentDB vous permet d'importer à partir d'une ou plusieurs collections DocumentDB et de filtrer éventuellement des documents à l'aide d'une requête.

![Capture d’écran des options sources DocumentDB](./media/documentdb-import-data/documentdbsource.png)

Le format de la chaîne de connexion DocumentDB est :

	AccountEndpoint=<DocumentDB Endpoint>;AccountKey=<DocumentDB Key>;Database=<DocumentDB Database>;

> [AZURE.NOTE]Utilisez la commande Verify pour vous assurer que l'instance DocumentDB spécifiée dans le champ de la chaîne de connexion est accessible.

Pour importer à partir d'une seule collection DocumentDB, entrez le nom de la collection à partir de laquelle les données seront importées. Pour importer à partir de plusieurs collections DocumentDB, fournissez une expression régulière correspondant à un ou plusieurs noms de collection (par exemple, collection01 | collection02 | collection03). Vous pouvez éventuellement spécifier ou fournir un fichier pour une requête pour filtrer et mettre en forme les données à importer.

> [AZURE.NOTE]Étant donné que le champ de collection accepte les expressions régulières, si vous importez à partir d'une collection unique dont le nom contient des caractères d'expression régulière, ces caractères doivent être placés en conséquence dans une séquence d'échappement.

L'option d'importateur source DocumentDB dispose des options avancées suivantes :

1. Inclusion des champs internes : cette option précise les propriétés système du document DocumentDB à inclure ou non dans l'exportation (par exemple, _rid, _ts).
2. Nombre de nouvelles tentatives en cas de défaillance : cette option précise le nombre de nouvelles tentatives de connexion à DocumentDB en cas de défaillances temporaires (par exemple, une interruption de connectivité du réseau).
3. Intervalle avant nouvelle tentative : cette option indique le temps à attendre entre les nouvelles tentatives de connexion à DocumentDB en cas de défaillances temporaires (par exemple, une interruption de connectivité du réseau).
4. Mode de connexion : cette option indique le mode de connexion à utiliser avec DocumentDB. Les choix disponibles sont DirectTcp, DirectHttps et la passerelle. Les modes de connexion directs sont plus rapides, tandis que le mode passerelle est mieux adapté au pare-feu car il utilise uniquement le port 443.

![Capture d’écran des options sources avancées DocumentDB](./media/documentdb-import-data/documentdbsourceoptions.png)

> [AZURE.TIP]L’outil d’importation utilise le mode de connexion DirectTcp par défaut. Si vous rencontrez des problèmes liés au pare-feu, passer au mode de connexion passerelle qui ne nécessite que le port 443.


Voici quelques exemples de lignes de commande pour l’importation depuis DocumentDB :

	#Migrate data from one DocumentDB collection to another DocumentDB collections
	dt.exe /s:DocumentDB /s.ConnectionString:"AccountEndpoint=<DocumentDB Endpoint>;AccountKey=<DocumentDB Key>;Database=<DocumentDB Database>;" /s.Collection:TEColl /t:DocumentDBBulk /t.ConnectionString:" AccountEndpoint=<DocumentDB Endpoint>;AccountKey=<DocumentDB Key>;Database=<DocumentDB Database>;" /t.Collection:TESessions /t.CollectionTier:S3

	#Migrate data from multiple DocumentDB collections to a single DocumentDB collection
	dt.exe /s:DocumentDB /s.ConnectionString:"AccountEndpoint=<DocumentDB Endpoint>;AccountKey=<DocumentDB Key>;Database=<DocumentDB Database>;" /s.Collection:comp1|comp2|comp3|comp4 /t:DocumentDBBulk /t.ConnectionString:"AccountEndpoint=<DocumentDB Endpoint>;AccountKey=<DocumentDB Key>;Database=<DocumentDB Database>;" /t.Collection:singleCollection /t.CollectionTier:S3

	#Export a DocumentDB collection to a JSON file
	dt.exe /s:DocumentDB /s.ConnectionString:"AccountEndpoint=<DocumentDB Endpoint>;AccountKey=<DocumentDB Key>;Database=<DocumentDB Database>;" /s.Collection:StoresSub /t:JsonFile /t.File:StoresExport.json /t.Overwrite /t.CollectionTier:S3

##<a id="HBaseSource"></a>Importation à partir de HBase ##

L’option d’importateur source HBase vous permet d’importer des données à partir d'une table HBase et de filtrer éventuellement les données. Plusieurs modèles sont fournis pour faciliter au maximum la configuration d'une importation.

![Capture d’écran des options sources HBase](./media/documentdb-import-data/hbasesource1.png)

![Capture d’écran des options sources HBase](./media/documentdb-import-data/hbasesource2.png)

Le format de la chaîne de connexion HBase Stargate est :

	ServiceURL=<server-address>;Username=<username>;Password=<password>

> [AZURE.NOTE]Utilisez la commande Verify pour vous assurer que l'instance HBase spécifiée dans le champ de la chaîne de connexion est accessible.

Voici un exemple de ligne de commande pour importer à partir de HBase :

	dt.exe /s:HBase /s.ConnectionString:ServiceURL=<server-address>;Username=<username>;Password=<password> /s.Table:Contacts /t:DocumentDBBulk /t.ConnectionString:"AccountEndpoint=<DocumentDB Endpoint>;AccountKey=<DocumentDB Key>;Database=<DocumentDB Database>;" /t.Collection:hbaseimport

##<a id="DocumentDBBulkTarget"></a>Importation de DocumentDB (importation en bloc) ##

L'importateur en bloc DocumentDB vous permet d'importer à partir des options sources disponibles, à l'aide d'une procédure stockée DocumentDB pour plus d'efficacité. L'outil prend en charge l'importation dans une seule collection DocumentDB, ainsi que l'importation partitionnée pour laquelle les données sont partitionnées sur plusieurs collections DocumentDB. Cliquez [ici](documentdb-partition-data.md) pour en savoir plus sur le partitionnement des données dans DocumentDB. L'outil va créer, exécuter, puis supprimer la procédure stockée de la ou les collections cibles.

![Capture d’écran des options de bloc DocumentDB](./media/documentdb-import-data/documentdbbulk.png)

Le format de la chaîne de connexion DocumentDB est :

	AccountEndpoint=<DocumentDB Endpoint>;AccountKey=<DocumentDB Key>;Database=<DocumentDB Database>;

> [AZURE.NOTE]Utilisez la commande Verify pour vous assurer que l'instance DocumentDB spécifiée dans le champ de la chaîne de connexion est accessible.

Pour importer dans seule collection, entrez le nom de la collection dans laquelle les données seront importées et cliquez sur le bouton Ajouter. Pour importer dans plusieurs collections, entrez le nom de chaque collection individuellement ou utilisez la syntaxe suivante pour spécifier plusieurs collections : *préfixe_collection*[index de début - index de fin]. Quand vous spécifiez plusieurs collections via la syntaxe ci-dessus, n'oubliez pas les points suivants :

1. Seuls les modèles de nom de plage de nombres entiers sont pris en charge. Par exemple, la spécification de collection[0-3] produit les collections suivantes : collection0, collection1, collection2, collection3.
2. Vous pouvez utiliser une syntaxe abrégée : collection[3], qui émet le même jeu de collections que celui mentionné à l'étape 1.
3. Plusieurs substitutions peuvent être fournies. Par exemple, collection[0-1] [0-9] génère 20 noms de collection avec des zéros non significatifs (collection01, ..02, ..03).

Une fois que les noms de la collection ont été spécifiés, choisissez le niveau de tarification souhaité des collections (S1, S2 ou S3). Pour de meilleures performances d'importation, choisissez S3. Cliquez [ici](documentdb-performance-levels.md) pour en savoir plus sur les niveaux de performances DocumentDB.

> [AZURE.NOTE]Le paramètre de niveau de performance s'applique uniquement à la création de collections. Si la collection spécifiée existe déjà, son niveau de tarification ne sera pas modifié.

Pendant l'importation de plusieurs collections, l'outil d'importation prend en charge le partitionnement basé sur le hachage. Dans ce scénario, spécifiez la propriété de document que vous voulez utiliser comme clé de partition (si la clé de partition est vide, les documents seront partitionnés de manière aléatoire entre les collections cibles).

Vous pouvez éventuellement spécifier quel champ de la source d'importation doit être utilisé en tant que propriété d'ID du document DocumentDB lors de l'importation (Notez que si les documents ne contiennent pas cette propriété, l'outil d'importation génère alors un GUID comme valeur de propriété de l’ID).

De nombreuses options avancées sont disponibles lors de l'importation. Tout d'abord, tandis que l'outil inclut une procédure stockée d’importation en bloc par défaut (BulkInsert.js), vous pouvez choisir d’indiquer votre propre procédure stockée d'importation :

 ![Capture d'écran de l’option sproc d’insertion de bloc DocumentDB](./media/documentdb-import-data/bulkinsertsp.png)

De plus, lorsque vous importez des types de date (par exemple, depuis SQL Server ou MongoDB), vous pouvez choisir entre trois options d'importation :

 ![Capture d’écran des options d’importation de date et d’heure DocumentDB](./media/documentdb-import-data/datetimeoptions.png)

-	Chaîne : conserver en tant que valeur de chaîne
-	Epoch : conserver en tant que valeur numérique Epoch
-	Les deux : conserver la chaîne et les valeurs numériques Epoch Cette option crée un sous-document, par exemple : « date_joined » : {« Valeur »: « 2013-10-21T21:17:25.2410000Z », « Epoch » : 1382390245} 


L'importateur en bloc DocumentDB dispose des options avancées supplémentaires suivantes :

1. Taille du lot : l'outil par défaut avec une taille de lot de 50. Si les documents qui doivent être importés sont volumineux, pensez à réduire la taille du lot. À l’inverse, si les documents qui doivent être importés sont peu volumineux, pensez à augmenter la taille du lot.
2. Taille de script maximale (octets) : l'outil par défaut avec une taille de script maximale de 960 Ko
3. Désactivation de la génération automatique d’ID : si tous les documents à importer contiennent un champ d'ID, la sélection de cette option permettra d’augmenter les performances. Les documents avec un champ d’ID unique manquant ne seront pas importés.
4. Nombre de nouvelles tentatives en cas de défaillance : cette option précise le nombre de nouvelles tentatives de connexion à DocumentDB en cas de défaillances temporaires (par exemple, une interruption de connectivité du réseau).
5. Intervalle avant nouvelle tentative : cette option indique le temps à attendre entre les nouvelles tentatives de connexion à DocumentDB en cas de défaillances temporaires (par exemple, une interruption de connectivité du réseau).
6. Mode de connexion : cette option indique le mode de connexion à utiliser avec DocumentDB. Les choix disponibles sont DirectTcp, DirectHttps et la passerelle. Les modes de connexion directs sont plus rapides, tandis que le mode passerelle est mieux adapté au pare-feu car il utilise uniquement le port 443.

![Capture d’écran des options d’importation en bloc avancées DocumentDB](./media/documentdb-import-data/docdbbulkoptions.png)

> [AZURE.TIP]L’outil d’importation utilise le mode de connexion DirectTcp par défaut. Si vous rencontrez des problèmes liés au pare-feu, passer au mode de connexion passerelle qui ne nécessite que le port 443.

##<a id="DocumentDBSeqTarget"></a>Importation vers DocumentDB (importation d’enregistrement séquentiel) ##

L’importateur d’enregistrement séquentiel DocumentDB vous permet d’importer à partir de n’importe quelle option source disponible sur un enregistrement en fonction des enregistrements. Vous pouvez choisir cette option si vous importez vers une collection existante ayant atteint son quota de procédures stockées. L'outil prend en charge l'importation dans une seule collection DocumentDB, ainsi que l'importation partitionnée pour laquelle les données sont partitionnées sur plusieurs collections DocumentDB. Cliquez [ici](documentdb-partition-data.md) pour en savoir plus sur le partitionnement des données dans DocumentDB.

![Capture d’écran des options d’importation d’enregistrement séquentiel DocumentDB](./media/documentdb-import-data/documentdbsequential.png)

Le format de la chaîne de connexion DocumentDB est :

	AccountEndpoint=<DocumentDB Endpoint>;AccountKey=<DocumentDB Key>;Database=<DocumentDB Database>;

> [AZURE.NOTE]Utilisez la commande Verify pour vous assurer que l'instance DocumentDB spécifiée dans le champ de la chaîne de connexion est accessible.

Pour importer dans seule collection, entrez le nom de la collection dans laquelle les données seront importées et cliquez sur le bouton Ajouter. Pour importer dans plusieurs collections, entrez le nom de chaque collection individuellement ou utilisez la syntaxe suivante pour spécifier plusieurs collections : *préfixe_collection*[index de début - index de fin]. Quand vous spécifiez plusieurs collections via la syntaxe ci-dessus, n'oubliez pas les points suivants :

1. Seuls les modèles de nom de plage de nombres entiers sont pris en charge. Par exemple, la spécification de collection[0-3] produit les collections suivantes : collection0, collection1, collection2, collection3.
2. Vous pouvez utiliser une syntaxe abrégée : collection[3], qui émet le même jeu de collections que celui mentionné à l'étape 1.
3. Plusieurs substitutions peuvent être fournies. Par exemple, collection[0-1] [0-9] génère 20 noms de collection avec des zéros non significatifs (collection01, ..02, ..03).

Une fois que les noms de la collection ont été spécifiés, choisissez le niveau de tarification souhaité des collections (S1, S2 ou S3). Pour de meilleures performances d'importation, choisissez S3. Cliquez [ici](documentdb-performance-levels.md) pour en savoir plus sur les niveaux de performances DocumentDB.

> [AZURE.NOTE]Le paramètre de niveau de performance s'applique uniquement à la création de collections. Si la collection spécifiée existe déjà, son niveau de tarification ne sera pas modifié.

Pendant l'importation de plusieurs collections, l'outil d'importation prend en charge le partitionnement basé sur le hachage. Dans ce scénario, spécifiez la propriété de document que vous voulez utiliser comme clé de partition (si la clé de partition est vide, les documents seront partitionnés de manière aléatoire entre les collections cibles).

Vous pouvez éventuellement spécifier quel champ de la source d'importation doit être utilisé en tant que propriété d'ID du document DocumentDB lors de l'importation (Notez que si les documents ne contiennent pas cette propriété, l'outil d'importation génère alors un GUID comme valeur de propriété de l’ID).

De nombreuses options avancées sont disponibles lors de l'importation. Tout d’abord, lorsque vous importez des types de date (par exemple, depuis SQL Server ou MongoDB), vous pouvez choisir entre trois options d'importation :

 ![Capture d’écran des options d’importation de date et d’heure DocumentDB](./media/documentdb-import-data/datetimeoptions.png)

-	Chaîne : conserver en tant que valeur de chaîne
-	Epoch : conserver en tant que valeur numérique Epoch
-	Les deux : conserver la chaîne et les valeurs numériques Epoch Cette option crée un sous-document, par exemple : « date_joined » : {« Valeur »: « 2013-10-21T21:17:25.2410000Z », « Epoch » : 1382390245} 

L'importateur d’enregistrement séquentiel DocumentDB dispose des options avancées supplémentaires suivantes :

1. Nombre de demandes parallèles : l'outil par défaut avec 2 demandes parallèles. Si les documents qui doivent être importés sont peu volumineux, pensez à augmenter le nombre de demandes parallèles. Notez que si ce nombre est trop élevé, l'importation peut rencontrer une limitation.
2. Désactivation de la génération automatique d’ID : si tous les documents à importer contiennent un champ d'ID, la sélection de cette option permettra d’en augmenter les performances. Les documents avec un champ d’ID unique manquant ne seront pas importés.
3. Nombre de nouvelles tentatives en cas de défaillance : cette option précise le nombre de nouvelles tentatives de connexion à DocumentDB en cas de défaillances temporaires (par exemple, une interruption de connectivité du réseau).
4. Intervalle avant nouvelle tentative : cette option indique le temps à attendre entre les nouvelles tentatives de connexion à DocumentDB en cas de défaillances temporaires (par exemple, une interruption de connectivité du réseau).
5. Mode de connexion : cette option indique le mode de connexion à utiliser avec DocumentDB. Les choix disponibles sont DirectTcp, DirectHttps et la passerelle. Les modes de connexion directs sont plus rapides, tandis que le mode passerelle est mieux adapté au pare-feu car il utilise uniquement le port 443.

![Capture d’écran des options d’importation d’enregistrement séquentiel avancées DocumentDB](./media/documentdb-import-data/documentdbsequentialoptions.png)

> [AZURE.TIP]L’outil d’importation utilise le mode de connexion DirectTcp par défaut. Si vous rencontrez des problèmes liés au pare-feu, passer au mode de connexion passerelle qui ne nécessite que le port 443.

##<a id="IndexingPolicy"></a>Spécification d’une stratégie d'indexation lors de la création de collections DocumentDB ##

Lorsque vous autorisez l'outil de migration à créer des collections pendant l'importation, vous pouvez spécifier la stratégie d'indexation des collections. Dans la section des options d’importation en bloc avancées DocumentDB et des options d'enregistrement séquentiel DocumentDB, accédez à la section de la stratégie de l'indexation.

![Capture d’écran des options de stratégie d’indexation avancées DocumentDB](./media/documentdb-import-data/indexingpolicy1.png)

À l'aide de l’option de stratégie d'indexation avancée, vous pouvez sélectionner un fichier de stratégie d'indexation, saisir manuellement une stratégie d'indexation ou en sélectionner une parmi les différents modèles proposés par défaut (en cliquant avec le bouton droit dans la zone de texte de stratégie d'indexation).

L'outil fournit les modèles de stratégie suivants :

- Par défaut. Cette stratégie est préférable si vous exécutez des requêtes d’efficacité sur des chaînes et des requêtes ORDER BY, de plage et d’efficacité sur des nombres. Cette stratégie dispose d’une surcharge de stockage d'index inférieure à Plage.
- Hachage. Cette stratégie est préférable si vous exécutez des requêtes d'efficacité sur des nombres et des chaînes. Cette stratégie dispose de la plus faible surcharge de stockage d'index.
- Plage. Cette stratégie est préférable si vous exécutez des requêtes ORDER BY, de plage et d'efficacité sur des nombres et des chaînes. Cette stratégie dispose d’une surcharge de stockage d'index supérieure à Par défaut ou Hachage.


![Capture d’écran des options de stratégie d’indexation avancées DocumentDB](./media/documentdb-import-data/indexingpolicy2.png)

> [AZURE.NOTE]Si vous ne spécifiez pas de stratégie d'indexation, la stratégie par défaut sera appliquée. Pour en savoir plus sur les stratégies d'indexation DocumentDB, cliquez [ici](documentdb-indexing-policies.md).


## Exportation vers un fichier JSON

L'exportateur JSON DocumentDB vous permet d’exporter des options sources disponibles vers un fichier JSON qui contient un tableau des documents JSON. L'outil gère l'exportation pour vous. Vous pouvez également choisir d'afficher la commande de migration qui en résulte et d’exécuter la commande vous-même. Le fichier JSON résultant peut être stocké localement ou dans le stockage d’objets blob Azure.

![Capture d’écran des options d'exportation de fichier local JSON DocumentDB](./media/documentdb-import-data/jsontarget.png)

![Capture d’écran des options d'exportation du stockage d’objets blob Azure JSON DocumentDB](./media/documentdb-import-data/jsontarget2.png)

Vous pouvez éventuellement choisir d’agrémenter le JSON qui en résulte, ce qui augmente la taille du document obtenu tout en rendant le contenu plus lisible.

	Standard JSON export
	[{"id":"Sample","Title":"About Paris","Language":{"Name":"English"},"Author":{"Name":"Don","Location":{"City":"Paris","Country":"France"}},"Content":"Don's document in DocumentDB is a valid JSON document as defined by the JSON spec.","PageViews":10000,"Topics":[{"Title":"History of Paris"},{"Title":"Places to see in Paris"}]}]
	
	Prettified JSON export
	[
 	{
    "id": "Sample",
    "Title": "About Paris",
    "Language": {
      "Name": "English"
    },
    "Author": {
      "Name": "Don",
      "Location": {
        "City": "Paris",
        "Country": "France"
      }
    },
    "Content": "Don's document in DocumentDB is a valid JSON document as defined by the JSON spec.",
    "PageViews": 10000,
    "Topics": [
      {
        "Title": "History of Paris"
      },
      {
        "Title": "Places to see in Paris"
      }
    ]
	}]
	
## Configuration avancée

Dans l'écran Configuration avancée, spécifiez l'emplacement du fichier journal dans lequel écrire toutes les erreurs. Les règles suivantes s'appliquent à cette page :

1.	Si un nom de fichier n'est pas fourni, toutes les erreurs sont retournées dans la page de résultats.
2.	Si un nom de fichier est fourni sans répertoire, le fichier est créé (ou remplacé) dans le répertoire de l'environnement actuel.
3.	Si vous sélectionnez un fichier existant, le fichier est remplacé, il n'existe aucune option d'ajout.

	![Capture de l'écran Configuration avancée](./media/documentdb-import-data/AdvancedConfiguration.png)

## Confirmation des paramètres d'importation et affichage de la ligne de commande

1. Après avoir spécifié les informations sources et cibles ainsi que la configuration avancée, vérifiez le résumé de la migration et affichez ou copiez éventuellement la commande de migration qui en résulte (la copie de la commande est utile pour automatiser les opérations d'importation) :

	![Capture d'écran de l'écran de résumé](./media/documentdb-import-data/summary.png)

	![Capture d'écran de l'écran de résumé](./media/documentdb-import-data/summarycommand.png)

2. Une fois que vous êtes satisfait de vos options sources et cibles, cliquez sur **Importer**. Le temps écoulé, le nombre transféré et les informations relatives aux échecs (si vous n'avez pas fourni de nom de fichier dans la Configuration avancée) sont mis à jour pendant le processus d'importation. Une fois cela terminé, vous pouvez exporter les résultats (par exemple, pour gérer les défaillances d’importation).

	![Capture d’écran des options d'exportation JSON DocumentDB](./media/documentdb-import-data/viewresults.png)

3. Vous pouvez également démarrer une nouvelle importation en conservant les paramètres existants (par exemple, les informations de la chaîne de connexion, le choix source et cible, etc.) ou en réinitialisant toutes les valeurs.

	![Capture d’écran des options d'exportation JSON DocumentDB](./media/documentdb-import-data/newimport.png)

## Étapes suivantes

- Pour en savoir plus sur DocumentDB, cliquez [ici](http://azure.com/docdb).


 

<!----HONumber=July15_HO5-->