<properties linkid="dev-net-how-to-table-services" urlDisplayName="Table Service" pageTitle="How to use table storage  from .NET | Microsoft Azure" metaKeywords="Get started Azure table   Azure nosql   Azure large structured data store   Azure table   Azure table storage   Azure table .NET   Azure table storage .NET   Azure table C#   Azure table storage C#" description="Learn how to use Microsoft Azure Table storage to create and delete tables and insert and query entities in a table." services="storage" documentationCenter=".NET" metaCanonical="" disqusComments="1" umbracoNaviHide="1" title="How to use Microsoft Azure Table storage" authors="tamram" />

<tags ms.service="storage" ms.workload="storage" ms.tgt_pltfrm="na" ms.devlang="dotnet" ms.topic="article" ms.date="01/01/1900" ms.author="tamram" />

# Utilisation du stockage de tables à partir de .NET

Ce guide décrit le déroulement de scénarios courants dans le cadre de l'utilisation du
service de stockage de tables Azure. Les exemples ont été écrits en code C# et utilisent la bibliothèque cliente de stockage Azure pour .NET. Les scénarios traités incluent la **création et
suppression d'une table**, ainsi que l'**utilisation d'entités de table**. Pour plus d'informations sur les tables, consultez la section [Étapes suivantes][Étapes suivantes].

> [WACOM.NOTE] Ce guide cible la bibliothèque cliente de stockage Azure .NET 2.x et les versions ultérieures. Nous vous recommandons d'utiliser la version 4.x disponible via [NuGet][NuGet] ou dans le [Kit de développement logiciel (SDK) Azure pour .NET][Kit de développement logiciel (SDK) Azure pour .NET]. Pour plus d'informations sur l'obtention de la bibliothèque cliente de stockage, consultez la rubrique [Accès au stockage de tables par programme][Accès au stockage de tables par programme] ci-dessous.

## Sommaire

-   [Présentation du service de Table][Présentation du service de Table]
-   [Concepts][Concepts]
-   [Création d’un compte de stockage Azure][Création d’un compte de stockage Azure]
-   [Configuration d’une chaîne de connexion de stockage][Configuration d’une chaîne de connexion de stockage]
-   [Accès au stockage de tables par programme][Accès au stockage de tables par programme]
-   [Création d'une table][Création d'une table]
-   [Ajout d'une entité à une table][Ajout d'une entité à une table]
-   [Insertion d'un lot d'entités][Insertion d'un lot d'entités]
-   [Extraction de toutes les entités d'une partition][Extraction de toutes les entités d'une partition]
-   [Extraction d'un ensemble d'entités dans une partition][Extraction d'un ensemble d'entités dans une partition]
-   [Extraction d'une seule entité][Extraction d'une seule entité]
-   [Remplacement d'une entité][Remplacement d'une entité]
-   [Insertion ou remplacement d'une entité][Insertion ou remplacement d'une entité]
-   [Interrogation d'un sous-ensemble de propriétés d'entité][Interrogation d'un sous-ensemble de propriétés d'entité]
-   [Suppression d'une entité][Suppression d'une entité]
-   [Suppression d'une table][Suppression d'une table]
-   [Étapes suivantes][Étapes suivantes]

[WACOM.INCLUDE [howto-table-storage](../includes/howto-table-storage.md)]

## 

## <a name="create-account"></a><span class="short-header">Création d’un compte</span>Création d’un compte de stockage Azure

</h2>
[WACOM.INCLUDE [create-storage-account](../includes/create-storage-account.md)]

## 

## <a name="setup-connection-string"></a><span class="short-header">Configuration d’une chaîne de connexion</span>Configuration d’une chaîne de connexion de stockage

</h2>
[WACOM.INCLUDE [storage-configure-connection-string](../includes/storage-configure-connection-string.md)]

## 

## <a name="configure-access"> </a><span class="short-header">Accès par programme</span> Accès au stockage de tables par programme

</h2>
### Obtention de l’assembly

Vous pouvez utiliser NuGet pour obtenir l’assembly `Microsoft.WindowsAzure.Storage.dll`. Cliquez avec le bouton droit sur votre projet dans l’**Explorateur de solutions**, puis sélectionnez **Manage NuGet Packages**. Effectuez une recherche en ligne sur « WindowsAzure.Storage », puis cliquez sur **Install** pour installer le package de stockage Azure et ses dépendances.

`Microsoft.WindowsAzure.Storage.dll` est également inclus dans le Kit de développement logiciel (SDK) Azure pour .NET, téléchargeable à partir du [Centre de développement .NET][Centre de développement .NET]. L'assembly est installé dans le répertoire `%Program Files%\Microsoft SDKs\Windows Azure\.NET SDK%Program Files%\Microsoft SDKs\Windows Azure\.NET SDK\<sdk-version>\ref\`lt;sdk-version\>\\ref\\</code>.

### Déclarations d’espace de noms

Ajoutez les déclarations d'espace de noms suivantes au début de chaque fichier C#
dans lequel vous souhaitez accéder au stockage Azure par programme :

    using Microsoft.WindowsAzure.Storage;
    using Microsoft.WindowsAzure.Storage.Auth;
    using Microsoft.WindowsAzure.Storage.Table;

Assurez-vous de bien référencer l’assembly `Microsoft.WindowsAzure.Storage.dll`.

### Récupération de votre chaîne de connexion

Vous pouvez utiliser le type **CloudStorageAccount** pour représenter
les informations de votre compte de stockage. Si vous utilisez un
modèle de projet Azure et/ou qu'une référence pointe vers l'espace de noms
Microsoft.WindowsAzure.CloudConfigurationManager, vous
pouvez utiliser le type **CloudConfigurationManager** pour
extraire votre chaîne de connexion de stockage, ainsi que les informations de votre compte de stockage
depuis la configuration de service Azure :

    CloudStorageAccount storageAccount = CloudStorageAccount.Parse(
        CloudConfigurationManager.GetSetting("StorageConnectionString"));

Si vous créez une application sans référence pointant vers Microsoft.WindowsAzure.CloudConfigurationManager et que votre chaîne de connexion est située dans le fichier `web.config` ou `app.config` comme indiqué précédemment, vous pouvez utiliser **ConfigurationManager** pour récupérer la chaîne de connexion. Vous devez ajouter une référence pointant vers System.Configuration.dll à votre projet, ainsi qu’une autre déclaration d’espace de noms :

    using System.Configuration;
    ...
    CloudStorageAccount storageAccount = CloudStorageAccount.Parse(
        ConfigurationManager.ConnectionStrings["StorageConnectionString"].ConnectionString);

### Dépendances ODataLib

Les dépendances ODataLib de la bibliothèque de client de stockage pour .NET sont résolues via les packages ODataLib (version 5.0.2) disponibles avec NuGet et non pas avec les services de données WCF. Vous pouvez télécharger directement les bibliothèques ODataLib ou les référencer avec votre projet de code via NuGet. Les packages ODataLib sont [OData][OData], [Edm][Edm] et [Spatial][Spatial].

## <a name="create-table"></a><span class="short-header">Création d'une table</span> Création d'une table

Un objet **CloudTableClient** vous permet d'obtenir les objets de référence pour les tables et entités. Le code suivant crée un objet **CloudTableClient** et l'utilise pour créer une table. Tous les codes de ce guide partent du principe que l'application développée est un projet de service cloud Azure et utilise une chaîne de connexion de stockage stockée dans la configuration de service de l'application Azure.

    // Retrieve the storage account from the connection string.
    CloudStorageAccount storageAccount = CloudStorageAccount.Parse(
        CloudConfigurationManager.GetSetting("StorageConnectionString"));

    // Create the table client.
    CloudTableClient tableClient = storageAccount.CreateCloudTableClient();

    // Create the table if it doesn't exist.
    CloudTable table = tableClient.GetTableReference("people");
    table.CreateIfNotExists();

## <a name="add-entity"></a><span class="short-header">Ajout d'une entité à une table</span> Ajout d'une entité à une table

Les entités mappent vers les objets C# en utilisant une classe personnalisée dérivée d’une
**TableEntity**. Pour ajouter une entité à une table, créez une classe définissant les propriétés de votre entité. Le code suivant définit une classe d'entité utilisant le prénom du client en tant que clé de ligne et son nom de famille en tant que clé de partition. Ensemble, les clés de partition et de ligne d'une entité identifient l'entité de façon unique dans la table. Les requêtes des entités dont les clés de partition sont identiques sont plus rapides que celles des entités dont les clés de partition sont différentes, mais le fait d'utiliser différentes clés de partition améliore l'extensibilité des opérations parallèles. Si une propriété doit être stockée dans le service de Table,
il doit s’agir d’une propriété publique d’un type pris en charge qui expose `get` et `set`.
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

Les opérations de table impliquant des entités sont effectuées en utilisant l’objet **CloudTable**
 créé dans la section Création d’une table. L'opération à effectuer est représentée par un objet **TableOperation**. L’exemple de code suivant illustre la création des objets **CloudTable** et **CustomerEntity**. Pour préparer l’opération, un objet **TableOperation** est créé pour insérer l’entité du client dans la table. Finalement, l’opération est exécutée en appelant **CloudTable.Execute**.

    // Retrieve the storage account from the connection string.
    CloudStorageAccount storageAccount = CloudStorageAccount.Parse(
       CloudConfigurationManager.GetSetting("StorageConnectionString"));

    // Create the table client.
    CloudTableClient tableClient = storageAccount.CreateCloudTableClient();

    // Create the CloudTable object that represents the "people" table.
    CloudTable table = tableClient.GetTableReference("people");

    // Create a new customer entity.
    CustomerEntity customer1 = new CustomerEntity("Harp", "Walter");
    customer1.Email = "Walter@contoso.com";
    customer1.PhoneNumber = "425-555-0101";

    // Create the TableOperation that inserts the customer entity.
    TableOperation insertOperation = TableOperation.Insert(customer1);

    // Execute the insert operation.
    table.Execute(insertOperation);

## <a name="insert-batch"></a><span class="short-header">Insertion d'un lot d'entités</span> Insertion d'un lot d'entités

Vous pouvez insérer un lot d'entités dans une table en une seule opération d'écriture. Autres remarques sur les opérations par lot :

1.  Vous pouvez effectuer des mises à jour, des suppressions et des insertions dans la même opération par lot.
2.  Une seule opération par lot peut inclure jusqu’à 100 entités.
3.  Toutes les entités d'une opération par lot doivent avoir la même clé de partition.
4.  Même s’il est possible d’exécuter une requête en tant qu’opération par lot, il doit s’agir de la seule opération du lot.

<!-- -->

L'exemple de code suivant crée deux objets d'entité et ajoute chacun d'eux à une opération **TableBatchOperation** en utilisant la méthode **Insert**. La méthode **CloudTable.Execute** est ensuite appelée pour exécuter l’opération.

    // Retrieve the storage account from the connection string.
    CloudStorageAccount storageAccount = CloudStorageAccount.Parse(
        CloudConfigurationManager.GetSetting("StorageConnectionString"));

    // Create the table client.
    CloudTableClient tableClient = storageAccount.CreateCloudTableClient();

    // Create the CloudTable object that represents the "people" table.
    CloudTable table = tableClient.GetTableReference("people");

    // Create the batch operation.
    TableBatchOperation batchOperation = new TableBatchOperation();

    // Create a customer entity and add it to the table.
    CustomerEntity customer1 = new CustomerEntity("Smith", "Jeff");
    customer1.Email = "Jeff@contoso.com";
    customer1.PhoneNumber = "425-555-0104";
            
    // Create another customer entity and add it to the table.
    CustomerEntity customer2 = new CustomerEntity("Smith", "Ben");
    customer2.Email = "Ben@contoso.com";
    customer2.PhoneNumber = "425-555-0102";
            
    // Add both customer entities to the batch insert operation.
    batchOperation.Insert(customer1);
    batchOperation.Insert(customer2);

    // Execute the batch operation.
    table.ExecuteBatch(batchOperation);

## <a name="retrieve-all-entities"></a><span class="short-header">Récupération de toutes les entités</span> Extraction de toutes les entités d'une partition

Pour exécuter une requête de table applicable à toutes les entités d'une partition, utilisez un objet **TableQuery**. L'exemple de code suivant indique un filtre pour les entités où « Smith » correspond à la clé de partition. Il imprime les champs de chaque entité dans les résultats de requête vers la console.

    // Retrieve the storage account from the connection string.
    CloudStorageAccount storageAccount = CloudStorageAccount.Parse(
        CloudConfigurationManager.GetSetting("StorageConnectionString"));

    // Create the table client.
    CloudTableClient tableClient = storageAccount.CreateCloudTableClient();

    // Create the CloudTable object that represents the "people" table.
    CloudTable table = tableClient.GetTableReference("people");

    // Construct the query operation for all customer entities where PartitionKey="Smith".
    TableQuery<CustomerEntity> query = new TableQuery<CustomerEntity>().Where(TableQuery.GenerateFilterCondition("PartitionKey", QueryComparisons.Equal, "Smith"));

    // Print the fields for each customer.
    foreach (CustomerEntity entity in table.ExecuteQuery(query))
    {
        Console.WriteLine("{0}, {1}\t{2}\t{3}", entity.PartitionKey, entity.RowKey,
            entity.Email, entity.PhoneNumber);
    }

## <a name="retrieve-range-entities"></a><span class="short-header">Récupération d'un ensemble d'entités</span> Extraction d'un ensemble d'entités dans une partition

Si vous ne souhaitez pas exécuter une requête applicable à toutes les entités d'une partition, vous pouvez spécifier une plage en combinant le filtre de clé de partition avec un filtre de clé de ligne. L'exemple de code suivant utilise deux filtres pour obtenir toutes les entités de la partition « Smith » où la clé de ligne (prénom) commence par une lettre située avant la lettre « E » dans l'ordre alphabétique, puis imprime les résultats de la requête.

    // Retrieve the storage account from the connection string.
    CloudStorageAccount storageAccount = CloudStorageAccount.Parse(
        CloudConfigurationManager.GetSetting("StorageConnectionString"));

    // Create the table client.
    CloudTableClient tableClient = storageAccount.CreateCloudTableClient();

    //Create the CloudTable object that represents the "people" table.
    CloudTable table = tableClient.GetTableReference("people");

    // Create the table query.
    TableQuery<CustomerEntity> rangeQuery = new TableQuery<CustomerEntity>().Where(
        TableQuery.CombineFilters(
            TableQuery.GenerateFilterCondition("PartitionKey", QueryComparisons.Equal, "Smith"),
            TableOperators.And,
            TableQuery.GenerateFilterCondition("RowKey", QueryComparisons.LessThan, "E")));

    // Loop through the results, displaying information about the entity.
    foreach (CustomerEntity entity in table.ExecuteQuery(rangeQuery))
    {
        Console.WriteLine("{0}, {1}\t{2}\t{3}", entity.PartitionKey, entity.RowKey,
            entity.Email, entity.PhoneNumber);
    }

## <a name="retrieve-single-entity"></a><span class="short-header">Récupération d'une seule entité</span> Extraction d'une seule entité

Vous pouvez écrire une requête pour extraire une seule entité. Le code suivant utilise une opération **TableOperation** pour spécifier le client « Ben Smith ». Cette méthode renvoie une seule entité, au lieu d'une collection. De plus, la valeur renvoyée dans **TableResult.Result** est un objet **CustomerEntity**. La méthode la plus rapide pour extraire une seule entité dans le service de Table consiste à spécifier une clé de partition et une clé de ligne.

    // Retrieve the storage account from the connection string.
    CloudStorageAccount storageAccount = CloudStorageAccount.Parse(
        CloudConfigurationManager.GetSetting("StorageConnectionString"));

    // Create the table client.
    CloudTableClient tableClient = storageAccount.CreateCloudTableClient();

    // Create the CloudTable object that represents the "people" table.
    CloudTable table = tableClient.GetTableReference("people");

    // Create a retrieve operation that takes a customer entity.
    TableOperation retrieveOperation = TableOperation.Retrieve<CustomerEntity>("Smith", "Ben");

    // Execute the retrieve operation.
    TableResult retrievedResult = table.Execute(retrieveOperation);

    // Print the phone number of the result.
    if (retrievedResult.Result != null)
       Console.WriteLine(((CustomerEntity)retrievedResult.Result).PhoneNumber);
    else
       Console.WriteLine("The phone number could not be retrieved.");

## <a name="replace-entity"></a><span class="short-header">Remplacement d'une entité</span> Remplacement d'une entité

Pour mettre à jour une entité, extrayez-la du service de Table, modifiez l'objet d'entité, puis enregistrez les modifications dans le service de Table. Le code suivant modifie le numéro de téléphone d'un client existant. Au lieu d'appeler la méthode **Insert**, ce code utilise la méthode **Replace**. Ceci entraîne le remplacement complet de l'entité sur le serveur, sauf si cette dernière a été modifiée depuis son extraction, auquel cas l'opération échoue. Cet échec survient pour empêcher votre application de remplacer par erreur une modification apportée entre l'extraction et la mise à jour par un autre composant de votre application. Pour gérer correctement cet échec, vous devez de nouveau extraire l'entité, apporter vos modifications (si elles sont toujours valides), puis effectuer une autre opération **Replace**. La prochaine section vous apprendra à remplacer ce comportement.

    // Retrieve the storage account from the connection string.
    CloudStorageAccount storageAccount = CloudStorageAccount.Parse(
        CloudConfigurationManager.GetSetting("StorageConnectionString"));

    // Create the table client
    CloudTableClient tableClient = storageAccount.CreateCloudTableClient();

    // Create the CloudTable object that represents the "people" table.
    CloudTable table = tableClient.GetTableReference("people");

    // Create a retrieve operation that takes a customer entity.
    TableOperation retrieveOperation = TableOperation.Retrieve<CustomerEntity>("Smith", "Ben");

    // Execute the operation.
    TableResult retrievedResult = table.Execute(retrieveOperation);

    // Assign the result to a CustomerEntity object.
    CustomerEntity updateEntity = (CustomerEntity)retrievedResult.Result;

    if (updateEntity != null)
    {
       // Change the phone number.
       updateEntity.PhoneNumber = "425-555-0105";

       // Create the InsertOrReplace TableOperation
       TableOperation updateOperation = TableOperation.Replace(updateEntity);

       // Execute the operation.
       table.Execute(updateOperation);

       Console.WriteLine("Entity updated.");
    }

    else
       Console.WriteLine("Entity could not be retrieved.");

## <a name="insert-or-replace-entity"></a><span class="short-header">Insertion ou remplacement d'une entité</span> Insertion ou remplacement d'une entité

Les opérations **Replace** échouent si l'entité a été modifiée depuis son extraction à partir du serveur. De plus, vous devez d'abord extraire l'entité à partir du serveur pour que l'opération **Replace** réussisse. Cependant, il peut arriver que vous ne sachiez pas si l'entité existe sur le serveur et si les valeurs stockées sont inadaptées. Votre mise à jour doit donc toutes les remplacer. Pour cela, vous devez utiliser une opération **InsertOrReplace**
. Cette opération insère l'entité (s'il n'y en a pas déjà une) ou la remplace (s’il y en a une), indépendamment du moment où la dernière mise à jour est intervenue. Dans l'exemple de code suivant, l'entité du client Ben Smith est toujours extraite, mais elle est ensuite enregistrée sur le serveur en utilisant **InsertOrReplace**. Les mises à jour apportées à l'entité entre les opérations d'extraction et de mise à jour sont remplacées.

    // Retrieve the storage account from the connection string.
    CloudStorageAccount storageAccount = CloudStorageAccount.Parse(
        CloudConfigurationManager.GetSetting("StorageConnectionString"));

    // Create the table client.
    CloudTableClient tableClient = storageAccount.CreateCloudTableClient();

    // Create the CloudTable object that represents the "people" table.
    CloudTable table = tableClient.GetTableReference("people");

    // Create a retrieve operation that takes a customer entity.
    TableOperation retrieveOperation = TableOperation.Retrieve<CustomerEntity>("Smith", "Ben");

    // Execute the operation.
    TableResult retrievedResult = table.Execute(retrieveOperation);

    // Assign the result to a CustomerEntity object.
    CustomerEntity updateEntity = (CustomerEntity)retrievedResult.Result;

    if (updateEntity != null)
    {
       // Change the phone number.
       updateEntity.PhoneNumber = "425-555-1234";

       // Create the InsertOrReplace TableOperation
       TableOperation insertOrReplaceOperation = TableOperation.InsertOrReplace(updateEntity);

       // Execute the operation.
       table.Execute(insertOrReplaceOperation);

       Console.WriteLine("Entity was updated.");
    }

    else
       Console.WriteLine("Entity could not be retrieved.");

## <a name="query-entity-properties"></a><span class="short-header">Interrogation d'un sous-ensemble de propriétés</span> Interrogation d'un sous-ensemble de propriétés d'entité

Vous pouvez utiliser une requête de table pour récupérer uniquement quelques propriétés au lieu de l’intégralité des propriétés de l’entité. Cette technique, nommée « projection », réduit la consommation de bande passante et peut améliorer les performances des requêtes, notamment pour les entités volumineuses. La requête contenue dans le code suivant renvoie uniquement les adresses de messagerie des entités présentes dans la table. Pour ce faire, nous utilisons une requête **DynamicTableEntity** ainsi qu'une requête **EntityResolver**. Pour plus d'informations sur la projection, consultez ce [billet de blog][billet de blog]. Notez que la projection n'est pas prise en charge sur l'émulateur de stockage local : ce code s'exécute donc uniquement lors de l'utilisation d'un compte sur le service de table.

    // Retrieve storage account from connection string
    CloudStorageAccount storageAccount = CloudStorageAccount.Parse(
        CloudConfigurationManager.GetSetting("StorageConnectionString"));

    // Create the table client
    CloudTableClient tableClient = storageAccount.CreateCloudTableClient();

    //Create the CloudTable that represents the "people" table.
    CloudTable table = tableClient.GetTableReference("people");

    // Define the query, and only select the Email property
    TableQuery<DynamicTableEntity> projectionQuery = new TableQuery<DynamicTableEntity>().Select(new string[] { "Email" });

    // Define an entity resolver to work with the entity after retrieval.
    EntityResolver<string> resolver = (pk, rk, ts, props, etag) => props.ContainsKey("Email") ? props["Email"].StringValue : null;

    foreach (string projectedEmail in table.ExecuteQuery(projectionQuery, resolver, null, null))
    {
        Console.WriteLine(projectedEmail);
    }

## <a name="delete-entity"></a><span class="short-header">Suppression d'une entité</span> Suppression d'une entité

Vous pouvez facilement supprimer une entité après l'avoir extraite. Il vous suffit pour cela d'utiliser la procédure de mise à jour d'une entité. Le code suivant extrait et supprime une entité de client.

    // Retrieve storage account from connection string
    CloudStorageAccount storageAccount = CloudStorageAccount.Parse(
        CloudConfigurationManager.GetSetting("StorageConnectionString"));

    // Create the table client
    CloudTableClient tableClient = storageAccount.CreateCloudTableClient();

    //Create the CloudTable that represents the "people" table.
    CloudTable table = tableClient.GetTableReference("people");

    // Create a retrieve operation that expects a customer entity.
    TableOperation retrieveOperation = TableOperation.Retrieve<CustomerEntity>("Smith", "Ben");

    // Execute the operation.
    TableResult retrievedResult = table.Execute(retrieveOperation);

    // Assign the result to a CustomerEntity.
    CustomerEntity deleteEntity = (CustomerEntity)retrievedResult.Result;

    // Create the Delete TableOperation.
    if (deleteEntity != null)
    {
       TableOperation deleteOperation = TableOperation.Delete(deleteEntity);

       // Execute the operation.
       table.Execute(deleteOperation);

       Console.WriteLine("Entity deleted.");
    }

    else
       Console.WriteLine("Could not retrieve the entity.");

## <a name="delete-table"></a><span class="short-header">Suppression d'une table</span> Suppression d'une table

Pour finir, l’exemple de code suivant supprime une table d’un compte de stockage. Une table supprimée ne pourra plus être recréée pendant un certain temps.

    // Retrieve the storage account from the connection string.
    CloudStorageAccount storageAccount = CloudStorageAccount.Parse(
        CloudConfigurationManager.GetSetting("StorageConnectionString"));

    // Create the table client.
    CloudTableClient tableClient = storageAccount.CreateCloudTableClient();

    //Create the CloudTable that represents the "people" table.
    CloudTable table = tableClient.GetTableReference("people");

    // Delete the table it if exists.
    table.DeleteIfExists();

## <a name="next-steps"></a><span class="short-header">Étapes suivantes</span>Étapes suivantes

Maintenant que vous connaissez les bases du stockage de tables, consultez les liens suivants pour apprendre à réaliser des tâches de stockage plus complexes.

-   Pour plus d'informations sur les API disponibles, consultez la documentation de référence du service de Table :
    -   [Référence de la bibliothèque de client de stockage pour .NET][Référence de la bibliothèque de client de stockage pour .NET]
    -   [Référence d’API REST][Référence d’API REST]
-   Pour plus d’informations sur les tâches avancées que vous pouvez effectuer avec le stockage Azure, consultez la page [Stockage et accessibilité des données dans Windows Azure][Stockage et accessibilité des données dans Windows Azure].
-   Pour apprendre à utiliser Azure Storage dans les processus principaux relatifs à Sites Web Azure, consultez la page [Prise en main du Kit de développement logiciel (SDK) Tâches web Azure][Prise en main du Kit de développement logiciel (SDK) Tâches web Azure].
-   Pour plus d’informations sur les autres options de stockage de données dans Azure, consultez d’autres guides de fonctionnalités.
    -   Utilisez le [stockage d’objets blob][stockage d’objets blob] pour stocker des données non structurées.
    -   Utilisez le [stockage des files d'attente][stockage des files d'attente] pour stocker des données structurées.
    -   Utilisez une [base de données SQL][base de données SQL] pour stocker des données relationnelles.

  [Étapes suivantes]: #next-steps
  [NuGet]: https://www.nuget.org/packages/WindowsAzure.Storage/
  [Accès au stockage de tables par programme]: #configure-access
  [Présentation du service de Table]: #what-is
  [Concepts]: #concepts
  [Création d’un compte de stockage Azure]: #create-account
  [Configuration d’une chaîne de connexion de stockage]: #setup-connection-string
  [Création d'une table]: #create-table
  [Ajout d'une entité à une table]: #add-entity
  [Insertion d'un lot d'entités]: #insert-batch
  [Extraction de toutes les entités d'une partition]: #retrieve-all-entities
  [Extraction d'un ensemble d'entités dans une partition]: #retrieve-range-entities
  [Extraction d'une seule entité]: #retrieve-single-entity
  [Remplacement d'une entité]: #replace-entity
  [Insertion ou remplacement d'une entité]: #insert-or-replace-entity
  [Interrogation d'un sous-ensemble de propriétés d'entité]: #query-entity-properties
  [Suppression d'une entité]: #delete-entity
  [Suppression d'une table]: #delete-table
  [howto-table-storage]: ../includes/howto-table-storage.md
  [create-storage-account]: ../includes/create-storage-account.md
  [storage-configure-connection-string]: ../includes/storage-configure-connection-string.md
  [Centre de développement .NET]: http://www.windowsazure.com/fr-fr/develop/net/#
  [OData]: http://nuget.org/packages/Microsoft.Data.OData/5.0.2
  [Edm]: http://nuget.org/packages/Microsoft.Data.Edm/5.0.2
  [Spatial]: http://nuget.org/packages/System.Spatial/5.0.2
  [billet de blog]: http://blogs.msdn.com/b/windowsazurestorage/archive/2011/09/15/windows-azure-tables-introducing-upsert-and-query-projection.aspx
  [Référence de la bibliothèque de client de stockage pour .NET]: http://go.microsoft.com/fwlink/?LinkID=390731&clcid=0x409
  [Référence d’API REST]: http://msdn.microsoft.com/fr-fr/library/windowsazure/dd179355
  [Stockage et accessibilité des données dans Windows Azure]: http://msdn.microsoft.com/fr-fr/library/windowsazure/gg433040.aspx
  [stockage d’objets blob]: /fr-fr/documentation/articles/storage-dotnet-how-to-use-blobs/
  [stockage des files d'attente]: /fr-fr/documentation/articles/storage-dotnet-how-to-use-queues/
  [base de données SQL]: /fr-fr/documentation/articles/sql-database-dotnet-how-to-use/
