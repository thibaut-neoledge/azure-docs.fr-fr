<properties urlDisplayName="Table Service" pageTitle="Utilisation du stockage de tables à partir de .NET | Microsoft Azure" metaKeywords="Get started Azure table   Azure nosql   Azure large structured data store   Azure table   Azure table storage   Azure table .NET   Azure table storage .NET   Azure table C#   Azure table storage C#" description="Learn how to use Microsoft Azure Table storage to create and delete tables and insert and query entities in a table." services="storage" documentationCenter=".NET" metaCanonical="" disqusComments="1" umbracoNaviHide="1" title="How to use Microsoft Azure Table storage" authors="tamram" manager="adinah" />

<tags ms.service="storage" ms.workload="storage" ms.tgt_pltfrm="na" ms.devlang="dotnet" ms.topic="article" ms.date="01/01/1900" ms.author="tamram" />



# Utilisation du stockage de tables à partir de .NET


Ce guide décrit le déroulement de scénarios courants dans le cadre de l'utilisation 
du service de stockage de tables Azure. Les exemples sont écrits en code C\#
et utilisent la bibliothèque cliente de stockage Azure pour .NET. Les scénarios traités incluent **la création et
la suppression d'une table**, ainsi que l'**utilisation d'entités de table**. Pour plus
d'informations sur les tables, consultez la section [Étapes suivantes][Étapes suivantes].

> [WACOM.NOTE] Ce guide cible la bibliothèque cliente de stockage Azure .NET 2.x et les versions ultérieures. Nous vous recommandons d'utiliser la version 4.x disponible via [NuGet](https://www.nuget.org/packages/WindowsAzure.Storage/) ou dans le [Kit de développement logiciel (SDK) Azure pour .NET](/fr-fr/downloads/). Pour plus d'informations sur l'obtention de la bibliothèque cliente de stockage, consultez la rubrique [ Accès au stockage de tables par programme][ Accès au stockage de tables par programme] ci-dessous.

## Sommaire

-   [Présentation du service de Table][Présentation du service de Table]
-   [Concepts][Concepts]
-   [Création d'un compte Azure Storage][Création d'un compte Azure Storage]
-   [Configuration d'une chaîne de connexion de stockage][Configuration d'une chaîne de connexion de stockage]
-   [ Accès au stockage de tables par programme][ Accès au stockage de tables par programme]
-   [ Création d'une table][ Création d'une table]
-   [ Ajout d'une entité à une table][ Ajout d'une entité à une table]
-   [ Insertion d'un lot d'entités][ Insertion d'un lot d'entités]
-   [ Extraction de toutes les entités d'une partition][ Extraction de toutes les entités d'une partition]
-   [ Extraction d'un ensemble d'entités dans une partition][ Extraction d'un ensemble d'entités dans une partition]
-   [ Extraction d'une seule entité][ Extraction d'une seule entité]
-   [ Remplacement d'une entité][ Remplacement d'une entité]
-   [ Insertion ou remplacement d'une entité][ Insertion ou remplacement d'une entité]
-   [ Interrogation d'un sous-ensemble de propriétés d'entité][ Interrogation d'un sous-ensemble de propriétés d'entité]
-   [ Suppression d'une entité][ Suppression d'une entité]
-   [ Suppression d'une table][ Suppression d'une table]
-   [Étapes suivantes][Étapes suivantes]

[WACOM.INCLUDE [howto-table-storage](../includes/howto-table-storage.md)]

## <h2><a name="create-account"></a><span class="short-header">Création d'un compte</span>Création d'un compte de stockage Azure</h2>

[WACOM.INCLUDE [create-storage-account](../includes/create-storage-account.md)]

## <h2><a name="setup-connection-string"></a><span class="short-header">Configuration d'une chaîne de connexion</span>Configuration d'une chaîne de connexion de stockage</h2>

[WACOM.INCLUDE [storage-configure-connection-string](../includes/storage-configure-connection-string.md)]

## <h2> <a name="configure-access"> </a><span  class="short-header">Accès par programme</span> Accès au stockage de tables par programme</h2>

<h3>Obtention de l'assembly</h3>
Vous pouvez utiliser NuGet pour obtenir l'assembly " Microsoft.WindowsAzure.Storage.dll ". Cliquez avec le bouton droit sur votre projet dans l'**Explorateur de solutions**, puis sélectionnez **Manage NuGet Packages**.  Effectuez une recherche en ligne sur " WindowsAzure.Storage ", puis cliquez sur **Install** pour installer le package de stockage Azure et ses dépendances.

`Microsoft.WindowsAzure.Storage.dll` est également inclus dans le Kit de développement logiciel (SDK) Azure pour .NET, téléchargeable dans le <a href="http://www.windowsazure.com/fr-fr/develop/net/#">Centre de développement .NET</a>. L'assembly est installé dans le répertoire `%Program Files%\Microsoft SDKs\Windows Azure\.NET SDK\<sdk-version>\ref\`.

<h3>Déclarations d'espace de noms</h3>
Ajoutez les déclarations d'espace de noms suivantes au début de chaque fichier C\#
dans lequel vous souhaitez accéder au stockage Azure par programme :

    using Microsoft.WindowsAzure.Storage;
	using Microsoft.WindowsAzure.Storage.Auth;
    using Microsoft.WindowsAzure.Storage.Table;

Assurez-vous de bien référencer l'assembly `Microsoft.WindowsAzure.Storage.dll`.

<h3>Récupération de votre chaîne de connexion</h3>
Vous pouvez utiliser le type **CloudStorageAccount** pour représenter 
les informations de votre compte de stockage. Si vous utilisez un 
modèle de projet Azure et/ou qu'une référence pointe vers l'espace de noms
Microsoft.WindowsAzure.CloudConfigurationManager, vous 
pouvez utiliser le type **CloudConfigurationManager** 
pour extraire votre chaîne de connexion de stockage, ainsi que les informations de
votre compte de stockage depuis la configuration de service Azure :

    CloudStorageAccount storageAccount = CloudStorageAccount.Parse(
        CloudConfigurationManager.GetSetting("ChaineConnexionStockage"));

Si vous créez une application sans référence pointant vers Microsoft.WindowsAzure.CloudConfigurationManager et que votre chaîne de connexion est située dans le fichier `web.config` ou `app.config` comme indiqué précédemment, vous pouvez utiliser **ConfigurationManager** pour récupérer la chaîne de connexion.  Vous devez ajouter une référence pointant vers System.Configuration.dll à votre projet, ainsi qu'une autre déclaration d'espace de noms :

	using System.Configuration;
	...
	CloudStorageAccount storageAccount = CloudStorageAccount.Parse(
		ConfigurationManager.ConnectionStrings["StorageConnectionString"].ConnectionString);

<h3>Dépendances ODataLib</h3>
Les dépendances ODataLib de la bibliothèque de client de stockage pour .NET sont résolues via les packages ODataLib (version 5.0.2) disponibles avec NuGet et non pas avec les services de données WCF.  Vous pouvez télécharger directement les bibliothèques ODataLib ou les référencer avec votre projet de code via NuGet.  Les packages ODataLib sont [OData], [Edm] et [Spatial].

<h2><a name="create-table"></a><span class="short-header">Création d'une table</span> Création d'une table</h2>

Un objet **CloudTableClient** vous permet d'obtenir les objets de référence pour les tables
et entités. Le code suivant crée un objet **CloudTableClient** 
et l'utilise pour créer une table. Tous les codes de ce guide partent du principe que
l'application développée est un projet de service cloud Azure et
utilise une chaîne de connexion de stockage stockée dans la configuration de service de l'application Azure.

    // Récupérez le compte de stockage de la chaîne de connexion.
    CloudStorageAccount storageAccount = CloudStorageAccount.Parse(
        CloudConfigurationManager.GetSetting("ChaineConnexionStockage"));

    // Créez le client de la table.
    CloudTableClient tableClient = storageAccount.CreateCloudTableClient();

    // Si la table n'existe pas, créez-la.
    CloudTable table = tableClient.GetTableReference("people");
    table.CreateIfNotExists();

<h2><a name="add-entity"></a><span class="short-header">Ajout d'une entité à une table</span> Ajout d'une entité à une table</h2>

Les entités mappent vers les objets C\# en utilisant une classe personnalisée dérivée d'une
**TableEntity**. Pour ajouter une entité à une table, créez une
classe définissant les propriétés de votre entité. Le code suivant
définit une classe d'entité utilisant le prénom du client en tant que clé
de ligne et son nom de famille en tant que clé de partition. Ensemble, les clés de partition et
de ligne d'une entité identifient l'entité de façon unique dans la table. Les requêtes des entités dont
les clés de partition sont identiques sont plus rapides que celles des entités dont les clés de partition
sont différentes, mais le fait d'utiliser différentes clés de partition améliore l'extensibilité
des opérations parallèles.  Si une propriété doit être stockée dans le service de table, 
il doit s'agir d'une propriété publique d'un type pris en charge qui expose `get` et `set`.
De plus, votre type d'entité *doit* exposer un constructeur sans paramètre.

    public class CustomerEntity : TableEntity
    {
        public CustomerEntity(string lastName, string firstName)
        {
            this.PartitionKey = lastName;
            this.RowKey = firstName;
        }
		
        public CustomerEntity() { }

        public string Email { get; set; }

        public string PhoneNumber { get; set; }
    }

Les opérations de table impliquant des entités sont effectuées en utilisant l'objet **CloudTable**
créé dans la section Création d'une table.  L'opération à effectuer
est représentée par un objet **TableOperation**.  L'exemple de code suivant illustre la création des objets **CloudTable** et **CustomerEntity**.  Pour préparer l'opération, un objet **TableOperation** est créé pour insérer l'entité du client dans la table.  Pour terminer, l'opération est exécutée en appelant **CloudTable.Execute**.

    // Récupérez le compte de stockage de la chaîne de connexion.
    CloudStorageAccount storageAccount = CloudStorageAccount.Parse(
       CloudConfigurationManager.GetSetting("ChaineConnexionStockage"));

    // Créez le client de la table.
    CloudTableClient tableClient = storageAccount.CreateCloudTableClient();
	
	// Créez l'objet CloudTable représentant la table "people".
	CloudTable table = tableClient.GetTableReference("people");

    // Créez une entité de client.
    CustomerEntity customer1 = new CustomerEntity("Harp", "Walter");
    customer1.Email = "Walter@contoso.com";
    customer1.PhoneNumber = "425-555-0101";

    // Créez l'objet TableOperation insérant l'identité du client.
    TableOperation insertOperation = TableOperation.Insert(customer1);

    // Exécutez l'opération d'insertion.
    table.Execute(insertOperation);

<h2><a name="insert-batch"></a><span class="short-header">Insertion d'un lot d'entités</span> Insertion d'un lot d'entités</h2>

Vous pouvez insérer un lot d'entités dans une table en une seule opération
d'écriture. Autres remarques sur les opérations
par lot :

1.  Vous pouvez effectuer des mises à jour, des suppressions et des insertions dans la même opération par lot.
2.  Une seule opération par lot peut inclure jusqu'à 100 entités.
3.  Toutes les entités d'une opération par lot doivent avoir la même
    clé de partition.
4.  Même s'il est possible d'exécuter une requête en tant qu'opération par lot, il doit s'agir de la seule opération du lot.

<!-- -->
L'exemple de code suivant crée deux objets d'entité et ajoute chacun d'eux
à une opération **TableBatchOperation** en utilisant la méthode **Insert**. La méthode **CloudTable.Execute** est ensuite appelée pour exécuter l'opération.

    // Récupérez le compte de stockage de la chaîne de connexion.
    CloudStorageAccount storageAccount = CloudStorageAccount.Parse(
        CloudConfigurationManager.GetSetting("ChaineConnexionStockage"));

    // Créez le client de la table.
    CloudTableClient tableClient = storageAccount.CreateCloudTableClient();
    
	// Créez l'objet CloudTable représentant la table "people".
    CloudTable table = tableClient.GetTableReference("people");

    // Créez l'opération par lot.
    TableBatchOperation batchOperation = new TableBatchOperation();

    // Créez une entité de client, puis ajoutez-la à la table.
	CustomerEntity customer1 = new CustomerEntity("Smith", "Jeff");
	customer1.Email = "Jeff@contoso.com";
	customer1.PhoneNumber = "425-555-0104";
            
	// Créez une autre entité de client, puis ajoutez-la à la table.
	CustomerEntity customer2 = new CustomerEntity("Smith", "Ben");
	customer2.Email = "Ben@contoso.com";
	customer2.PhoneNumber = "425-555-0102";
            
	// Ajoutez les deux entités de client à l'opération d'insertion par lot.
	batchOperation.Insert(customer1);
	batchOperation.Insert(customer2);

	// Exécutez l'opération par lot.
	table.ExecuteBatch(batchOperation);

<h2><a name="retrieve-all-entities"></a><span class="short-header">Récupération de toutes les entités</span> Extraction de toutes les entités d'une partition</h2>

Pour exécuter une requête de table pour toutes les entités d'une partition, utilisez un objet **TableQuery**.
L'exemple de code suivant indique un filtre pour les entités où 'Smith'
est la clé de partition. Il imprime les champs de
chaque entité dans les résultats de requête vers la console.

    // Récupérez le compte de stockage de la chaîne de connexion.
    CloudStorageAccount storageAccount = CloudStorageAccount.Parse(
        CloudConfigurationManager.GetSetting("ChaineConnexionStockage"));

    // Créez le client de la table.
    CloudTableClient tableClient = storageAccount.CreateCloudTableClient();

    // Créez l'objet CloudTable représentant la table "people".
    CloudTable table = tableClient.GetTableReference("people");

    // Construisez l'opération de requête pour toutes les entités de client où PartitionKey="Smith".
    TableQuery<CustomerEntity> query = new TableQuery<CustomerEntity>().Where(TableQuery.GenerateFilterCondition("PartitionKey", QueryComparisons.Equal, "Smith"));

    // Imprimez les champs pour chaque client.
    foreach (CustomerEntity entity in table.ExecuteQuery(query))
    {
        Console.WriteLine("{0}, {1}\t{2}\t{3}", entity.PartitionKey, entity.RowKey,
            entity.Email, entity.PhoneNumber);
    }

<h2><a name="retrieve-range-entities"></a><span class="short-header">Récupération d'un ensemble d'entités</span> Extraction d'un ensemble d'entités dans une partition</h2>

Si vous ne souhaitez pas exécuter une requête applicable à toutes les entités d'une partition, vous pouvez
spécifier une plage en combinant le filtre de clé de partition avec un filtre de clé de ligne. L'exemple de code suivant
utilise deux filtres pour obtenir toutes les entités de la partition " Smith " où la clé de ligne
(prénom) commence par une lettre située avant la lettre " E " dans l'ordre alphabétique, puis
imprime les résultats de la requête.

    // Récupérez le compte de stockage de la chaîne de connexion.
    CloudStorageAccount storageAccount = CloudStorageAccount.Parse(
        CloudConfigurationManager.GetSetting("ChaineConnexionStockage"));

    // Créez le client de la table.
    CloudTableClient tableClient = storageAccount.CreateCloudTableClient();

    // Créez l'objet CloudTable représentant la table "people".
    CloudTable table = tableClient.GetTableReference("people");

	// Créez la requête de table.
    TableQuery<CustomerEntity> rangeQuery = new TableQuery<CustomerEntity>().Where(
        TableQuery.CombineFilters(
            TableQuery.GenerateFilterCondition("PartitionKey", QueryComparisons.Equal, "Smith"),
            TableOperators.And,
            TableQuery.GenerateFilterCondition("RowKey", QueryComparisons.LessThan, "E")));

    // Créez une boucle entre les résultats, qui affiche les informations sur l'entité.
    foreach (CustomerEntity entity in table.ExecuteQuery(rangeQuery))
    {
        Console.WriteLine("{0}, {1}\t{2}\t{3}", entity.PartitionKey, entity.RowKey,
            entity.Email, entity.PhoneNumber);
    }

<h2><a name="retrieve-single-entity"></a><span class="short-header">Récupération d'une seule entité</span> Extraction d'une seule entité</h2>

Vous pouvez écrire une requête pour extraire une seule entité. Le
code suivant utilise une **TableOperation** pour spécifier le client 'Ben Smith'.
Cette méthode renvoie une seule entité (et non une
collection). De plus, la valeur renvoyée dans **TableResult.Result** est un objet **CustomerEntity**.
La méthode la plus rapide pour extraire une seule entité dans le service de table 
consiste à spécifier une clé de partition et une clé de ligne.

    // Récupérez le compte de stockage de la chaîne de connexion.
    CloudStorageAccount storageAccount = CloudStorageAccount.Parse(
        CloudConfigurationManager.GetSetting("ChaineConnexionStockage"));

    // Créez le client de la table.
    CloudTableClient tableClient = storageAccount.CreateCloudTableClient();

    // Créez l'objet CloudTable représentant la table "people".
    CloudTable table = tableClient.GetTableReference("people");

    // Créez une opération de récupération prenant l'entité d'un client.
    TableOperation retrieveOperation = TableOperation.Retrieve<CustomerEntity>("Smith", "Ben");

    // Exécutez l'opération de récupération.
    TableResult retrievedResult = table.Execute(retrieveOperation);

    // Imprimez le numéro de téléphone du résultat.
	if (retrievedResult.Result != null)
	   Console.WriteLine(((CustomerEntity)retrievedResult.Result).PhoneNumber);
	else
	   Console.WriteLine("Impossible de récupérer le numéro de téléphone.");

<h2><a name="replace-entity"></a><span class="short-header">Remplacement d'une entité</span> Remplacement d'une entité</h2>

Pour mettre à jour une entité, récupérez-la dans le service de table, modifiez
l'objet d'entité, puis enregistrez les modifications dans la table de service. Le
code suivant modifie le numéro de téléphone d'un client existant. Au lieu
d'appeler la méthode **Insert**, ce code utilise la méthode  
**Replace**. Ceci entraîne le remplacement complet de l'entité sur le serveur,
sauf si cette dernière a été modifiée depuis sa récupération,
auquel cas l'opération échoue.  Cet échec survient pour empêcher votre application
de remplacer par erreur une modification apportée entre la récupération et 
la mise à jour par un autre composant de votre application.  Pour gérer correctement cet échec,
vous devez récupérer de nouveau l'entité, apporter vos modifications (si elles sont toujours valides), puis 
effectuer une autre opération **Replace**.  La prochaine section vous apprendra
à remplacer ce comportement.

    // Récupérez le compte de stockage de la chaîne de connexion.
    CloudStorageAccount storageAccount = CloudStorageAccount.Parse(
        CloudConfigurationManager.GetSetting("ChaineConnexionStockage"));

    // Créez le client de la table.
    CloudTableClient tableClient = storageAccount.CreateCloudTableClient();

    // Créez l'objet CloudTable représentant la table "people".
    CloudTable table = tableClient.GetTableReference("people");

    // Créez une opération de récupération prenant l'entité d'un client.
    TableOperation retrieveOperation = TableOperation.Retrieve<CustomerEntity>("Smith", "Ben");

    // Exécutez l'opération.
    TableResult retrievedResult = table.Execute(retrieveOperation);

    // Attribuez le résultat à un objet CustomerEntity.
    CustomerEntity updateEntity = (CustomerEntity)retrievedResult.Result;

    if (updateEntity != null)
	{
	   // Modifiez le numéro de téléphone.
	   updateEntity.PhoneNumber = "425-555-0105";

	   // Créez la TableOperation InsertOrReplace
	   TableOperation updateOperation = TableOperation.Replace(updateEntity);

	   // Exécutez l'opération.
	   table.Execute(updateOperation);

	   Console.WriteLine("Entité mise à jour.");
	}

	else
	   Console.WriteLine("Impossible de récupérer l'entité.");

<h2><a name="insert-or-replace-entity"></a><span class="short-header">Insertion ou remplacement d'une entité</span> Insertion ou remplacement d'une entité</h2>

Les opérations **Replace** échouent si l'entité est modifiée
depuis sa récupération à partir du serveur.  De plus, vous devez d'abord extraire
l'entité à partir du serveur pour que l'opération **Replace** réussisse.
Cependant, il se peut parfois que vous ne sachiez pas si l'entité existe sur le serveur
et si les valeurs stockées sont inadaptées. Votre mise à jour doit
donc toutes les remplacer.  Pour cela, vous devez utiliser une opération **InsertOrReplace**
.  Cette opération insère l'entité (s'il n'y en a pas déjà une) ou
la remplace (s'il y en a une), indépendamment du moment où la dernière mise à jour est intervenue.  Dans la 
Dans l'exemple de code suivant, l'entité du client Ben Smith est toujours extraite, mais elle est ensuite enregistrée sur le serveur en utilisant **InsertOrReplace**.  Les mises à jour
apportées à l'entité entre les opérations de récupération et de mise à jour sont 
remplacées.

    // Récupérez le compte de stockage de la chaîne de connexion.
    CloudStorageAccount storageAccount = CloudStorageAccount.Parse(
        CloudConfigurationManager.GetSetting("ChaineConnexionStockage"));

    // Créez le client de la table.
    CloudTableClient tableClient = storageAccount.CreateCloudTableClient();

    // Créez l'objet CloudTable représentant la table "people".
    CloudTable table = tableClient.GetTableReference("people");

    // Créez une opération de récupération prenant l'entité d'un client.
    TableOperation retrieveOperation = TableOperation.Retrieve<CustomerEntity>("Smith", "Ben");

    // Exécutez l'opération.
    TableResult retrievedResult = table.Execute(retrieveOperation);

    // Attribuez le résultat à un objet CustomerEntity.
    CustomerEntity updateEntity = (CustomerEntity)retrievedResult.Result;

    if (updateEntity != null)
	{
	   // Modifiez le numéro de téléphone.
	   updateEntity.PhoneNumber = "425-555-1234";

	   // Créez la TableOperation InsertOrReplace
	   TableOperation insertOrReplaceOperation = TableOperation.InsertOrReplace(updateEntity);

	   // Exécutez l'opération.
	   table.Execute(insertOrReplaceOperation);

	   Console.WriteLine("L'entité a été mise à jour.");
	}

	else
	   Console.WriteLine("Impossible de récupérer l'entité.");

<h2><a name="query-entity-properties"></a><span class="short-header">Interrogation d'un sous-ensemble de propriétés</span> Interrogation d'un sous-ensemble de propriétés d'entité</h2>

Vous pouvez utiliser une requête de table pour récupérer uniquement quelques propriétés au lieu de l'intégralité des propriétés de l'entité. Cette technique, nommée " projection ", réduit la consommation de bande passante et peut améliorer les performances des requêtes, notamment pour les entités volumineuses. La requête contenue dans le
code suivant renvoie uniquement les adresses de messagerie des entités présentes dans la
table. Pour ce faire, nous utilisons une requête **DynamicTableEntity**, ainsi 
qu'une requête **EntityResolver**. Pour plus d'informations sur la projection, consultez ce [billet de blog][billet de blog]. Notez que la projection n'est pas prise en charge sur l'émulateur de stockage local : ce code s'exécute donc uniquement lors de l'utilisation d'un compte sur le service de table.

    // Récupérez un compte de stockage à partir d'une chaîne de connexion
    CloudStorageAccount storageAccount = CloudStorageAccount.Parse(
        CloudConfigurationManager.GetSetting("ChaineConnexionStockage"));

    // Créez le client de la table.
    CloudTableClient tableClient = storageAccount.CreateCloudTableClient();

    //Créez la CloudTable représentant la table "people".
    CloudTable table = tableClient.GetTableReference("people");

    // Définissez la requête, puis sélectionnez uniquement la propriété Email
    TableQuery<DynamicTableEntity> projectionQuery = new TableQuery<DynamicTableEntity>().Select(new string[] { "Email" });

    // Définissez un programme de résolution d'entité pour pouvoir utiliser l'entité après la récupération.
    EntityResolver<string> resolver = (pk, rk, ts, props, etag) => props.ContainsKey("Email") ? props["Email"].StringValue : null;

    foreach (string projectedEmail in table.ExecuteQuery(projectionQuery, resolver, null, null))
    {
        Console.WriteLine(projectedEmail);
    }

<h2><a name="delete-entity"></a><span class="short-header">Suppression d'une entité</span> Suppression d'une entité</h2>

Vous pouvez facilement supprimer une entité après l'avoir extraite. Il vous suffit pour cela d'utiliser
la procédure de mise à jour d'une entité.  Le code suivant
extrait et supprime une entité de client.

    // Récupérez un compte de stockage à partir d'une chaîne de connexion
    CloudStorageAccount storageAccount = CloudStorageAccount.Parse(
        CloudConfigurationManager.GetSetting("ChaineConnexionStockage"));

    // Créez le client de la table.
    CloudTableClient tableClient = storageAccount.CreateCloudTableClient();

    //Créez la CloudTable représentant la table "people".
    CloudTable table = tableClient.GetTableReference("people");

    // Créez une opération de récupération qui attend une entité de client.
    TableOperation retrieveOperation = TableOperation.Retrieve<CustomerEntity>("Smith", "Ben");

    // Exécutez l'opération.
    TableResult retrievedResult = table.Execute(retrieveOperation);

    // Attribuez le résultat à une CustomerEntity.
    CustomerEntity deleteEntity = (CustomerEntity)retrievedResult.Result;

    // Créez une TableOperation Delete.
	if (deleteEntity != null)
	{
	   TableOperation deleteOperation = TableOperation.Delete(deleteEntity);

	   // Exécutez l'opération.
	   table.Execute(deleteOperation);

	   Console.WriteLine("Entité supprimée.");
	}

	else
	   Console.WriteLine("Impossible de récupérer l'entité.");

<h2><a name="delete-table"></a><span class="short-header">Suppression d'une table</span> Suppression d'une table</h2>

Pour finir, l'exemple de code suivant supprime une table d'un compte de stockage. Une
table supprimée ne pourra plus être recréée
pendant un certain temps.

    // Récupérez le compte de stockage de la chaîne de connexion.
    CloudStorageAccount storageAccount = CloudStorageAccount.Parse(
        CloudConfigurationManager.GetSetting("ChaineConnexionStockage"));

    // Créez le client de la table.
    CloudTableClient tableClient = storageAccount.CreateCloudTableClient();

    //Créez la CloudTable représentant la table "people".
    CloudTable table = tableClient.GetTableReference("people");

    // Supprimez la table, si elle existe.
    table.DeleteIfExists();

<h2><a name="next-steps"></a><span class="short-header">Étapes suivantes</span>Étapes suivantes</h2>

Maintenant que vous connaissez les bases du stockage de tables, consultez les liens suivants
pour apprendre à réaliser des tâches de stockage plus complexes.

<ul>
<li>Pour plus d'informations sur les API disponibles, consultez la documentation de référence du service de Table :
  <ul>
    <li><a href="http://go.microsoft.com/fwlink/?LinkID=390731&clcid=0x409">Référence de la bibliothèque de client de stockage pour .NET</a>
    </li>
    <li><a href="http://msdn.microsoft.com/fr-fr/library/windowsazure/dd179355">Référence d'API REST</a></li>
  </ul>
</li>
<li>Pour plus d'informations sur les tâches avancées que vous pouvez effectuer avec le stockage Azure, consultez la page <a href="http://msdn.microsoft.com/fr-fr/library/windowsazure/gg433040.aspx">Stockage et accessibilité des données dans Azure</a>.</li>
<li>Pour apprendre à utiliser Azure Storage dans les processus principaux relatifs à Sites Web Azure, consultez la page <a href="/fr-fr/documentation/articles/websites-dotnet-webjobs-sdk-get-started/">Prise en main du Kit de développement logiciel (SDK) Tâches web Azure</a>.</li>
<li>Pour plus d'informations sur les autres options de stockage de données dans Azure, consultez d'autres guides de fonctionnalités.
  <ul>
    <li>Utilisez le <a href="/fr-fr/documentation/articles/storage-dotnet-how-to-use-blobs/">stockage d'objets blob</a> pour stocker des données non structurées.</li>
    <li>Utilisez le <a href="/fr-fr/documentation/articles/storage-dotnet-how-to-use-queues/">stockage des files d'attente</a> pour stocker des données structurées.</li>
    <li>Utilisez une <a href="/fr-fr/documentation/articles/sql-database-dotnet-how-to-use/">base de données SQL</a> pour stocker des données relationnelles.</li>
  </ul>
</li>
</ul>

  [Étapes suivantes]: #next-steps
  [Concepts]: #concepts
  [Création d'un compte Azure Storage]: #create-account
  [Configuration d'une chaîne de connexion de stockage]: #setup-connection-string
  [ Accès au stockage de tables par programme]: #configure-access
  [ Création d'une table]: #create-table
  [ Ajout d'une entité à une table]: #add-entity
  [ Insertion d'un lot d'entités]: #insert-batch
  [ Extraction d'un ensemble d'entités dans une partition]: #retrieve-all-entities
  [ Extraction d'une seule entité]: #retrieve-single-entity
  [ Remplacement d'une entité]: #replace-entity
  [ Insertion ou remplacement d'une entité]: #insert-or-replace-entity
  [ Interrogation d'un sous-ensemble de propriétés d'entité]: #query-entity-properties
  [ Suppression d'une entité]: #delete-entity
  [ Suppression d'une table]: #delete-table
  
  
  [billet de blog]: http://blogs.msdn.com/b/windowsazurestorage/archive/2011/09/15/windows-azure-tables-introducing-upsert-and-query-projection.aspx
  [OData]: http://nuget.org/packages/Microsoft.Data.OData/5.0.2
  [Edm]: http://nuget.org/packages/Microsoft.Data.Edm/5.0.2
  [Spatial]: http://nuget.org/packages/System.Spatial/5.0.2
