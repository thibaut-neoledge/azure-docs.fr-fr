<properties  linkid="dev-net-2-how-to-table-services" urlDisplayName="Table Service" pageTitle="How to use table storage  from .NET | Microsoft Azure" metaKeywords="Get started Azure table   Azure nosql   Azure large structured data store   Azure table   Azure table storage   Azure table .NET   Azure table storage .NET   Azure table C#   Azure table storage C#" description="Learn how to use table storage to create and delete tables and insert and query entities in a table." services="storage" documentationCenter=".NET" metaCanonical="" disqusComments="1" umbracoNaviHide="1" title="How to use the Table Storage Service" authors="tamram" />
# Utilisation du service de stockage de table à partir de .NET

Ce guide décrit le déroulement de scénarios courants dans le cadre de
l'utilisation du service de stockage de table Azure. Les exemples ont
été écrits en code C# et utilisent la bibliothèque du client de stockage
Azure pour .NET. Les scénarios traités incluent la **création et
suppression d'une table**, ainsi que l'**utilisation d'entités de
table**. Pour plus d'informations sur les tables, consultez la section
[Étapes suivantes](#next-steps).
## Sommaire

* [Présentation du service de table](#what-is)
* [Concepts](#concepts)
* [Création d'un compte de stockage Azure](#create-account)
* [Configuration d'une chaîne de connexion de
  stockage](#setup-connection-string)
* [Accès au stockage de table par programme](#configure-access)
* [Création d'une table](#create-table)
* [Ajout d'une entité à une table](#add-entity)
* [Insertion d'un lot d'entités](#insert-batch)
* [Récupération de toutes les entités dans une
  partition](#retrieve-all-entities)
* [Récupération d'un ensemble d'entités dans une
  partition](#retrieve-range-entities)
* [Récupération d'une seule entité](#retrieve-single-entity)
* [Remplacement d'une entité](#replace-entity)
* [Insertion ou remplacement d'une entité](#insert-or-replace-entity)
* [Envoi d'une requête de sous-ensemble de propriétés
  d'entité](#query-entity-properties)
* [Suppression d'une entité](#delete-entity)
* [Suppression d'une table](#delete-table)
* [Étapes suivantes](#next-steps)

[WACOM.INCLUDE [howto-table-storage](../includes/howto-table-storage.md)]

<h2><a name="create-account"></a><span  class="short-header">Création d'un compte</span>Création d'un compte de stockage Azure</h2>


[WACOM.INCLUDE [create-storage-account](../includes/create-storage-account.md)]

<h2><a name="setup-connection-string"></a><span  class="short-header">Configuration d'une chaîne de connexion</span>Configuration d'une chaîne de connexion de stockage</h2>


La bibliothèque du client de stockage Azure pour .NET prend en charge
l'utilisation d'une chaîne de connexion de stockage pour la
configuration de points de terminaison et d'informations
d'identification permettant d'accéder aux services de stockage. Vous
pouvez placer votre chaîne de connexion de stockage dans un fichier de
configuration, au lieu de la coder en dur dans le code :

* Lorsque vous utilisez des services cloud Azure, il est recommandé de
  stocker votre chaîne de connexion en utilisant le système de
  configuration de service Azure (fichiers `*.csdef` et `*.cscfg`).
* Lorsque vous utilisez des sites Web Azure, des machines virtuelles
  Azure ou que vous développez des applications .NET conçues pour être
  exécutées en dehors d'Azure, il est recommandé de stocker votre chaîne
  de connexion en utilisant le système de configuration .NET (par
  exemple, le fichier `web.config` ou `app.config`).

Dans les deux cas, vous pouvez récupérer votre chaîne de connexion en
utilisant la méthode `CloudConfigurationManager.GetSetting`, comme cela
est présenté plus loin dans ce guide.
### Configuration de votre chaîne de connexion lors de l'utilisation des services cloud

Le mécanisme de configuration de service est propre aux projets de
services cloud Azure et vous permet de modifier les paramètres de
configuration de façon dynamique à partir du portail de gestion Azure
sans avoir à redéployer votre application.

Pour configurer votre chaîne de connexion dans la configuration de
service Azure :

1.  Dans l'Explorateur de solutions de Visual Studio, dans le dossier
    **Roles** de votre projet de déploiement Azure, cliquez avec le
    bouton droit sur votre rôle Web ou votre rôle de travail, puis
    cliquez sur **Properties**.  
     ![Blob5](./media/storage-dotnet-how-to-use-table-storage-20/blob5.png)

2.  Cliquez sur l'onglet **Settings**, puis sur le bouton **Add
    Setting**.  
     ![Blob6](./media/storage-dotnet-how-to-use-table-storage-20/blob6.png)
    
    Une nouvelle entrée nommée **Setting1** est ensuite affichée dans la
    grille des paramètres.

3.  Dans le menu déroulant **Type** de l'entrée **Setting1**,
    sélectionnez **Connection String**.  
     ![Blob7](./media/storage-dotnet-how-to-use-table-storage-20/blob7.png)

4.  Cliquez sur le bouton **...** situé à l'extrême droite de l'entrée
    **Setting1**. La boîte de dialogue **Storage Account Connection
    String** s'ouvre.

5.  Indiquez si vous voulez cibler l'émulateur de stockage (simulation
    de stockage Windows Azure sur votre machine locale) ou un compte de
    stockage existant dans le cloud. Le code de ce guide fonctionne dans
    les deux cas. Si vous voulez stocker des données d'objet blob dans
    le compte de stockage créé précédemment dans Azure, entrez la valeur
    **Primary Access Key** copiée lors de l'étape précédente de ce
    didacticiel.   
     ![Blob8](./media/storage-dotnet-how-to-use-table-storage-20/blob8.png)

6.  Remplacez la valeur **Setting1** de l'entrée **Name** par un nom
    plus convivial, tel que **ChaineConnexionStockage**. Le
    référencement de la chaîne de connexion dans le code sera effectué
    plus loin dans ce guide.  
     ![Blob9](./media/storage-dotnet-how-to-use-table-storage-20/blob9.png)
### Configuration de votre chaîne de connexion en utilisant la configuration .NET

Si vous développez une application qui n'est pas un service cloud Azure
(voir la section précédente), il est recommandé d'utiliser le système de
configuration .NET (par exemple, `web.config` ou `app.config`). Ceci
inclut les sites Web Azure ou les machines virtuelles Azure, ainsi que
les applications conçues pour être exécutées hors de Azure. Stockez la
chaîne de connexion en utilisant l'élément `<appSettings>` comme suit :

   	<configuration>
  		<appSettings>
    		<add key="ChaineDeConnexionDeStockage" value="DefaultEndpointsProtocol=https;AccountName=[AccountName];AccountKey=[AccountKey]" />
  		</appSettings>
	</configuration>

 Pour plus d'informations sur les chaînes de connexion de stockage,
consultez la page [Configuration des chaînes de connexion][1].

 Vous pouvez à présent effectuer les tâches présentées dans ce guide.

 <h2> <a name="configure-access"> </a><span  class="short-header">Accès par programme</span>Accès au stockage de table par programme</h2>


 <h3>Obtention de l'assembly</h3>


 Vous pouvez utiliser NuGet pour obtenir l'assembly
`Microsoft.WindowsAzure.Storage.dll`. Cliquez avec le bouton droit sur
votre projet dans l'**Explorateur de solutions**, puis sélectionnez
**Manage NuGet Packages**. Effectuez une recherche en ligne sur
<< WindowsAzure.Storage >>, puis cliquez sur **Install** pour installer
le package de stockage Azure et ses dépendances.

 `Microsoft.WindowsAzure.Storage.dll` est également inclus dans le Kit de
développement logiciel (SDK) Azure pour .NET, téléchargeable dans le
[Centre de développement .NET][2]. L'assembly est
installé dans le répertoire `%Program Files%\Microsoft SDKs\Windows
Azure\.NET SDK\<sdk -version>\ref\`.

 <h3>Déclarations d'espace de noms</h3>


 Ajoutez les déclarations d'espace de noms suivantes en haut de chaque
fichier C# dans lequel vous souhaitez accéder au stockage Azure par
programme :

    using Microsoft.WindowsAzure.Storage;
    using Microsoft.WindowsAzure.Storage.Auth;
    using Microsoft.WindowsAzure.Storage.Table;

 Assurez-vous de bien référencer l'assembly
`Microsoft.WindowsAzure.Storage.dll`.

 <h3>Récupération de votre chaîne de connexion</h3>


 Vous pouvez utiliser le type **CloudStorageAccount** pour représenter
vos informations de compte de stockage. Si vous utilisez un modèle de
projet Azure et/ou qu'une référence pointe vers
Microsoft.WindowsAzure.CloudConfigurationManager, vous pouvez utiliser
le type **CloudConfigurationManager** pour récupérer votre chaîne de
connexion de stockage, ainsi que vos informations de compte de stockage
depuis la configuration de service Azure :

    CloudStorageAccount storageAccount = CloudStorageAccount.Parse(
        CloudConfigurationManager.GetSetting("ChaineConnexionStockage"));

 Si vous créez une application sans référence pointant vers
Microsoft.WindowsAzure.CloudConfigurationManager et que votre chaîne de
connexion est située dans le fichier `web.config` ou `app.config` comme
indiqué précédemment, vous pouvez utiliser **ConfigurationManager** pour
récupérer la chaîne de connexion. Vous devez ajouter une référence
pointant vers System.Configuration.dll à votre projet, ainsi qu'une
autre déclaration d'espace de noms :

    using System.Configuration;
    ...
    CloudStorageAccount storageAccount = CloudStorageAccount.Parse(
    	ConfigurationManager.ConnectionStrings["ChaineConnexionStockage"].ConnectionString);

 <h3>Dépendances ODataLib</h3>


 Les dépendances ODataLib de la bibliothèque de client de stockage pour
.NET sont résolues via les packages ODataLib (version 5.0.2) disponibles
avec NuGet et non pas avec les services de données WCF. Vous pouvez
télécharger directement les bibliothèques ODataLib ou les référencer
avec votre projet de code via NuGet. Les packages ODataLib sont
[OData][3], [Edm][4] et [Spatial][5].

 <h2><a name="create-table"></a><span  class="short-header">Création d'une table</span>Création d'une table</h2>


 Un objet **CloudTableClient** vous permet d'obtenir les objets de
référence pour les tables et entités. Le code suivant crée un objet
**CloudTableClient** et l'utilise pour créer une table. Tous les codes
de ce guide partent du principe que l'application développée est un
projet de service cloud Azure et utilise une chaîne de connexion de
stockage stockée dans la configuration de service de l'application
Azure.

    // Récupérez le compte de stockage de la chaîne de connexion.
    CloudStorageAccount storageAccount = CloudStorageAccount.Parse(
        CloudConfigurationManager.GetSetting("ChaineConnexionStockage"));
    
    // Créez le client de la table.
    CloudTableClient tableClient = storageAccount.CreateCloudTableClient();
    
    // Si la table n’existe pas, créez-la.
    CloudTable table = tableClient.GetTableReference("people");
    table.CreateIfNotExists();

 <h2><a name="add-entity"></a><span  class="short-header">Ajout d'une entité à une table</span>Ajout d'une entité à une table</h2>


 Les entités mappent vers les objets C# en utilisant une classe
personnalisée dérivée d'une **TableEntity**. Pour ajouter une entité à
une table, créez une classe définissant les propriétés de votre entité.
Le code suivant définit une classe d'entité utilisant le prénom du
client en tant que clé de ligne et son nom de famille en tant que clé de
partition. Ensemble, les clés de partition et de ligne d'une entité
identifient l'entité de façon unique dans la table. Les requêtes
d'entités dont les clés de partition sont identiques sont plus rapides
que celles d'entités dont les clés de partition sont différentes, mais
le fait d'utiliser différentes clés de partition améliore
l'extensibilité des opérations parallèles. Si une propriété doit être
stockée dans le service de table, il doit s'agir d'une propriété
publique d'un type pris en charge qui expose `get` et `set`. De plus,
votre type d'entité *doit* exposer un constructeur sans paramètre.

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

 Les opérations de table impliquant des entités sont effectuées en
utilisant l'objet **CloudTable** créé dans la section Création d'une
table. L'opération à effectuer est représentée par un objet
**TableOperation**. L'exemple de code suivant illustre la création des
objets **CloudTable** et **CustomerEntity**. Pour préparer l'opération,
un objet **TableOperation** est créé pour insérer l'entité du client
dans la table. Finalement, l'opération est exécutée en appelant
**CloudTable.Execute**.

    // Récupérez le compte de stockage de la chaîne de connexion.
    CloudStorageAccount storageAccount = CloudStorageAccount.Parse(
       CloudConfigurationManager.GetSetting("ChaineConnexionStockage"));
    
    // Créez le client de la table.
    CloudTableClient tableClient = storageAccount.CreateCloudTableClient();
    
    // Créez l’objet CloudTable représentant la table "people".
    CloudTable table = tableClient.GetTableReference("people");
    
    // Créez une entité de client.
    CustomerEntity customer1 = new CustomerEntity("Harp", "Walter");
    customer1.Email = "Walter@contoso.com";
    customer1.PhoneNumber = "425-555-0101";
    
    // Créez l’objet TableOperation insérant l’identité du client.
    TableOperation insertOperation = TableOperation.Insert(customer1);
    
    // Exécutez l’opération d’insertion.
    table.Execute(insertOperation);

 <h2><a name="insert-batch"></a><span  class="short-header">Insertion d'un lot d'entités</span>Insertion d'un lot d'entités</h2>


 Vous pouvez insérer un lot d'entités dans une table en une seule
opération d'écriture. Autres remarques sur les opérations par lot :

 1.  Vous pouvez effectuer des mises à jour, des suppressions et des
    insertions dans la même opération par lot.
2.  Une seule opération par lot peut inclure jusqu'à 100 entités.
3.  Toutes les entités d'une opération par lot doivent avoir la même clé
    de partition.
4.  Même s'il est possible d'exécuter une requête en tant qu'opération
    par lot, il doit s'agir de la seule opération du lot.

 <!-- -->
 L'exemple de code suivant crée deux objets d'entité et ajoute chacun
d'eux à une **TableBatchOperation** en utilisant la méthode **Insert**.
La méthode **CloudTable.Execute** est ensuite appelée pour exécuter
l'opération.

    // Récupérez le compte de stockage de la chaîne de connexion.
    CloudStorageAccount storageAccount = CloudStorageAccount.Parse(
        CloudConfigurationManager.GetSetting("ChaineConnexionStockage"));
    
    // Créez le client de la table.
    CloudTableClient tableClient = storageAccount.CreateCloudTableClient();
    
    // Créez l’objet CloudTable représentant la table "people".
    CloudTable table = tableClient.GetTableReference("people");
    
    // Créez l’opération par lot.
    TableBatchOperation batchOperation = new TableBatchOperation();
    
    // Créez une entité de client, puis ajoutez-la à la table.
    CustomerEntity customer1 = new CustomerEntity("Smith", "Jeff");
    customer1.Email = "Jeff@contoso.com";
    customer1.PhoneNumber = "425-555-0104";
            
    // Créez une autre entité de client, puis ajoutez-la à la table.
    CustomerEntity customer2 = new CustomerEntity("Smith", "Ben");
    customer2.Email = "Ben@contoso.com";
    customer2.PhoneNumber = "425-555-0102";
            
    // Ajoutez les deux entités de client à l’opération d’insertion par lot.
    batchOperation.Insert(customer1);
    batchOperation.Insert(customer2);
    
    // Exécutez l’opération par lot.
    table.ExecuteBatch(batchOperation);

 <h2><a name="retrieve-all-entities"></a><span  class="short-header">Récupération de toutes les entités</span>Récupération de toutes les entités dans une partition</h2>


 Pour exécuter une requête de table pour toutes les entités d'une
partition, utilisez un objet **TableQuery**. L'exemple de code suivant
indique un filtre pour les entités où 'Smith' est la clé de partition.
Il imprime les champs de chaque entité dans les résultats de requête
vers la console.

    // Récupérez le compte de stockage de la chaîne de connexion.
    CloudStorageAccount storageAccount = CloudStorageAccount.Parse(
        CloudConfigurationManager.GetSetting("ChaineConnexionStockage"));
    
    // Créez le client de la table.
    CloudTableClient tableClient = storageAccount.CreateCloudTableClient();
    
    // Créez l’objet CloudTable représentant la table "people".
    CloudTable table = tableClient.GetTableReference("people");
    
    // Construisez l’opération de requête pour toutes les entités de client où PartitionKey="Smith".
    TableQuery<CustomerEntity> query = new TableQuery<CustomerEntity>().Where(TableQuery.GenerateFilterCondition("PartitionKey", QueryComparisons.Equal, "Smith"));
    
    // Imprimez les champs pour chaque client.
    foreach (CustomerEntity entity in table.ExecuteQuery(query))
    {
        Console.WriteLine("{0}, {1}\t{2}\t{3}", entity.PartitionKey, entity.RowKey,
            entity.Email, entity.PhoneNumber);
    }

 <h2><a name="retrieve-range-entities"></a><span  class="short-header">Récupération d'un ensemble d'entités</span>Récupération d'un ensemble d'entités dans une partition</h2>


 Si vous ne voulez pas exécuter une requête pour toutes les entités d'une
partition, vous pouvez spécifier un ensemble en combinant le filtre de
clé de partition avec un filtre de clé de ligne. L'exemple de code
suivant utilise deux filtres pour obtenir toutes les entités dans la
partition 'Smith' où la clé de ligne (prénom) commence par une lettre
située avant la lettre << E >> dans l'ordre alphabétique, puis imprime
les résultats de la requête.

    // Récupérez le compte de stockage de la chaîne de connexion.
    CloudStorageAccount storageAccount = CloudStorageAccount.Parse(
        CloudConfigurationManager.GetSetting("ChaineConnexionStockage"));
    
    // Créez le client de la table.
    CloudTableClient tableClient = storageAccount.CreateCloudTableClient();
    
    // Créez l’objet CloudTable représentant la table "people".
    CloudTable table = tableClient.GetTableReference("people");
    
    // Créez la requête de table.
    TableQuery<CustomerEntity> rangeQuery = new TableQuery<CustomerEntity>().Where(
        TableQuery.CombineFilters(
            TableQuery.GenerateFilterCondition("PartitionKey", QueryComparisons.Equal, "Smith"),
            TableOperators.And,
            TableQuery.GenerateFilterCondition("RowKey", QueryComparisons.LessThan, "E")));
    
    // Créez une boucle entre les résultats, qui affiche les informations sur l’entité.
    foreach (CustomerEntity entity in table.ExecuteQuery(rangeQuery))
    {
        Console.WriteLine("{0}, {1}\t{2}\t{3}", entity.PartitionKey, entity.RowKey,
            entity.Email, entity.PhoneNumber);
    }

 <h2><a name="retrieve-single-entity"></a><span  class="short-header">Récupération d'une seule entité</span>Récupération d'une seule entité</h2>


 Vous pouvez écrire une requête pour récupérer une seule entité. Le code
suivant utilise une **TableOperation** pour spécifier le client 'Ben
Smith'. Cette méthode renvoie une seule entité, au lieu d'une
collection. De plus, la valeur renvoyée dans **TableResult.Result** est
un objet **CustomerEntity**. La méthode la plus rapide pour récupérer
une seule entité dans le service de table consiste à spécifier une clé
de partition et une clé de ligne.

    // Récupérez le compte de stockage de la chaîne de connexion.
    CloudStorageAccount storageAccount = CloudStorageAccount.Parse(
        CloudConfigurationManager.GetSetting("ChaineConnexionStockage"));
    
    // Créez le client de la table.
    CloudTableClient tableClient = storageAccount.CreateCloudTableClient();
    
    // Créez l’objet CloudTable représentant la table "people".
    CloudTable table = tableClient.GetTableReference("people");
    
    // Créez une opération de récupération prenant l’entité d’un client.
    TableOperation retrieveOperation = TableOperation.Retrieve<CustomerEntity>("Smith", "Ben");
    
    // Exécutez l’opération de récupération.
    TableResult retrievedResult = table.Execute(retrieveOperation);
    
    // Imprimez le numéro de téléphone du résultat.
    if (retrievedResult.Result != null)
       Console.WriteLine(((CustomerEntity)retrievedResult.Result).PhoneNumber);
    else
       Console.WriteLine("Impossible de récupérer le numéro de téléphone.");

 <h2><a name="replace-entity"></a><span  class="short-header">Remplacement d'une entité</span>Remplacement d'une entité</h2>


 Pour mettre à jour une entité, récupérez-la dans le service de table,
modifiez l'objet d'entité, puis enregistrez les modifications dans la
table de service. Le code suivant modifie le numéro de téléphone d'un
client existant. Au lieu d'appeler la méthode **Insert**, ce code
utilise la méthode **Replace**. Ceci entraîne le remplacement complet de
l'entité sur le serveur, sauf si cette dernière a été modifiée depuis sa
récupération, auquel cas l'opération échoue. Cet échec survient pour
empêcher votre application de remplacer par erreur une modification
apportée entre la récupération et la mise à jour par un autre composant
de votre application. Pour gérer correctement cet échec, vous devez
récupérer de nouveau l'entité, apporter vos modifications (si elles sont
toujours valides), puis effectuer une autre opération **Replace**. La
prochaine section vous apprendra à remplacer ce comportement.

    // Récupérez le compte de stockage de la chaîne de connexion.
    CloudStorageAccount storageAccount = CloudStorageAccount.Parse(
        CloudConfigurationManager.GetSetting("ChaineConnexionStockage"));
    
    // Créez le client de la table.
    CloudTableClient tableClient = storageAccount.CreateCloudTableClient();
    
    // Créez l’objet CloudTable représentant la table "people".
    CloudTable table = tableClient.GetTableReference("people");
    
    // Créez une opération de récupération prenant l’entité d’un client.
    TableOperation retrieveOperation = TableOperation.Retrieve<CustomerEntity>("Smith", "Ben");
    
    // Exécutez l’opération.
    TableResult retrievedResult = table.Execute(retrieveOperation);
    
    // Attribuez le résultat à un objet CustomerEntity.
    CustomerEntity updateEntity = (CustomerEntity)retrievedResult.Result;
    
    if (updateEntity != null)
    {
       // Modifiez le numéro de téléphone.
       updateEntity.PhoneNumber = "425-555-0105";
    
       // Créez la TableOperation InsertOrReplace
       TableOperation updateOperation = TableOperation.Replace(updateEntity);
    
       // Exécutez l’opération.
       table.Execute(updateOperation);
    
       Console.WriteLine("Entité mise à jour.");
    }
    
    else
       Console.WriteLine("Impossible de récupérer l’entité.");

 <h2><a name="insert-or-replace-entity"></a><span  class="short-header">Insertion ou remplacement d'une entité</span>Insertion ou remplacement d'une entité</h2>


 Les opérations **Replace** échouent si l'entité est modifiée depuis sa
récupération à partir du serveur. De plus, vous devez d'abord récupérer
l'entité à partir du serveur pour que l'opération **Replace** réussisse.
Cependant, il se peut parfois que vous ne sachiez pas si l'entité existe
sur le serveur et si les valeurs stockées sont inadaptées. Votre mise à
jour doit donc toutes les remplacer. Pour cela, vous devez utiliser une
opération **InsertOrReplace**. Cette opération insère l'entité (s'il n'y
en a pas déjà une) ou la remplace (s'il y en a une), indépendamment du
moment de la dernière mise à jour. Dans l'exemple de code suivant,
l'entité de client pour Ben Smith est toujours récupérée, mais elle est
ensuite enregistrée sur le serveur en utilisant **InsertOrReplace**. Les
mises à jour apportées à l'entité entre les opérations de récupération
et de mise à jour sont remplacées.

    // Récupérez le compte de stockage de la chaîne de connexion.
    CloudStorageAccount storageAccount = CloudStorageAccount.Parse(
        CloudConfigurationManager.GetSetting("ChaineConnexionStockage"));
    
    // Créez le client de la table.
    CloudTableClient tableClient = storageAccount.CreateCloudTableClient();
    
    // Créez l’objet CloudTable représentant la table "people".
    CloudTable table = tableClient.GetTableReference("people");
    
    // Créez une opération de récupération prenant l’entité d’un client.
    TableOperation retrieveOperation = TableOperation.Retrieve<CustomerEntity>("Smith", "Ben");
    
    // Exécutez l’opération.
    TableResult retrievedResult = table.Execute(retrieveOperation);
    
    // Attribuez le résultat à un objet CustomerEntity.
    CustomerEntity updateEntity = (CustomerEntity)retrievedResult.Result;
    
    if (updateEntity != null)
    {
       // Modifiez le numéro de téléphone.
       updateEntity.PhoneNumber = "425-555-1234";
    
       // Créez la TableOperation InsertOrReplace
       TableOperation insertOrReplaceOperation = TableOperation.InsertOrReplace(updateEntity);
    
       // Exécutez l’opération.
       table.Execute(insertOrReplaceOperation);
    
       Console.WriteLine("L’entité a été mise à jour.");
    }
    
    else
       Console.WriteLine("Impossible de récupérer l’entité.");

 <h2><a name="query-entity-properties"></a><span  class="short-header">Envoi d'une requête de sous-ensemble de propriétés</span>Envoi d'une requête de sous-ensemble de propriétés d'entité</h2>


 Vous pouvez utiliser une requête de table pour récupérer uniquement
quelques propriétés au lieu de l'intégralité des propriétés de l'entité.
Cette technique, nommée << projection >>, réduit la consommation de
bande passante et peut améliorer les performances des requêtes,
notamment pour les entités volumineuses. La requête contenue dans le
code suivant renvoie uniquement les adresses de messagerie électronique
des entités dans la table. Pour ce faire, nous utilisons une requête
**DynamicTableEntity**, ainsi qu'une requête **EntityResolver**. Pour
plus d'informations sur la projection, consultez ce [billet de blog][6].
Notez que la projection n'est pas prise en charge sur l'émulateur de
stockage local : ce code s'exécute donc uniquement lors de
l'utilisation d'un compte sur le service de table.

    // Récupérez le compte de stockage à partir de la chaîne de connexion.
    CloudStorageAccount storageAccount = CloudStorageAccount.Parse(
        CloudConfigurationManager.GetSetting("ChaineConnexionStockage"));
    
    // Créez le client de la table.
    CloudTableClient tableClient = storageAccount.CreateCloudTableClient();
    
    //Créez la CloudTable représentant la table "people".
    CloudTable table = tableClient.GetTableReference("people");
    
    // Définissez la requête, puis sélectionnez uniquement la propriété Email
    TableQuery<DynamicTableEntity> projectionQuery = new TableQuery<DynamicTableEntity>().Select(new string[] { "Email" });
    
    // Définissez un programme de résolution d’entité pour pouvoir utiliser l’entité après la récupération.
    EntityResolver<string> resolver = (pk, rk, ts, props, etag) => props.ContainsKey("Email") ? props["Email"].StringValue : null;
    
    foreach (string projectedEmail in table.ExecuteQuery(projectionQuery, resolver, null, null))
    {
        Console.WriteLine(projectedEmail);
    }

 <h2><a name="delete-entity"></a><span  class="short-header">Suppression d'une entité</span>Suppression d'une entité</h2>


 Il est facile de supprimer une entité après l'avoir récupérée. Il suffit
pour cela d'utiliser la procédure suivie pour la mise à jour d'une
entité. Le code suivant récupère et supprime une entité de client.

    // Récupérez le compte de stockage à partir de la chaîne de connexion.
    CloudStorageAccount storageAccount = CloudStorageAccount.Parse(
        CloudConfigurationManager.GetSetting("ChaineConnexionStockage"));
    
    // Créez le client de la table.
    CloudTableClient tableClient = storageAccount.CreateCloudTableClient();
    
    //Créez la CloudTable représentant la table "people".
    CloudTable table = tableClient.GetTableReference("people");
    
    // Créez une opération de récupération qui attend une entité de client.
    TableOperation retrieveOperation = TableOperation.Retrieve<CustomerEntity>("Smith", "Ben");
    
    // Exécutez l’opération.
    TableResult retrievedResult = table.Execute(retrieveOperation);
    
    // Attribuez le résultat à une CustomerEntity.
    CustomerEntity deleteEntity = (CustomerEntity)retrievedResult.Result;
    
    // Créez une TableOperation Delete.
    if (deleteEntity != null)
    {
       TableOperation deleteOperation = TableOperation.Delete(deleteEntity);
    
       // Exécutez l’opération.
       table.Execute(deleteOperation);
    
       Console.WriteLine("Entité supprimée.");
    }
    
    else
       Console.WriteLine("Impossible de récupérer l’entité.");

 <h2><a name="delete-table"></a><span  class="short-header">Suppression d'une table</span>Suppression d'une table</h2>


 Pour finir, l'exemple de code suivant supprime une table d'un compte de
stockage. Une table supprimée ne pourra plus être recréée pendant un
certain temps.

    // Récupérez le compte de stockage de la chaîne de connexion.
    CloudStorageAccount storageAccount = CloudStorageAccount.Parse(
        CloudConfigurationManager.GetSetting("ChaineConnexionStockage"));
    
    // Créez le client de la table.
    CloudTableClient tableClient = storageAccount.CreateCloudTableClient();
    
    //Créez la CloudTable représentant la table "people".
    CloudTable table = tableClient.GetTableReference("people");
    
    // Supprimez la table, si elle existe.
    table.DeleteIfExists();

 <h2><a name="next-steps"></a><span  class="short-header">Étapes suivantes</span>Étapes suivantes</h2>


 Maintenant que vous avez appris les bases du stockage de tables, suivez
ces liens pour apprendre des tâches de stockage plus complexes.

 *  Pour plus d'informations sur les API disponibles, consultez la
  documentation de référence des services d'objets blob :
  *  [Référence de la bibliothèque de client de stockage pour .NET][7]
  *  [Référence d'API REST][8]
  

*  Pour plus d'informations sur les tâches avancées que vous pouvez
  effectuer avec le stockage Azure, consultez la page [Stockage et
  accessibilité des données dans Windows Azure][9].
*  Pour plus d'informations sur les autres options de stockage de données
  dans Azure, consultez d'autres guides de fonctionnalités.
  *  Utilisez le [stockage d'objets
    blob](/en-us/develop/net/how-to-guides/blob-storage/) pour stocker des données non structurées.
  *  Utilisez une [base de données
    SQL](/en-us/develop/net/how-to-guides/sql-database/) pour stocker des données relationnelles.
  


 </appsettings>



[1]: http://msdn.microsoft.com/en-us/library/windowsazure/ee758697.aspx
[2]: http://www.windowsazure.com/en-us/develop/net/#
[3]: http://nuget.org/packages/Microsoft.Data.OData/5.0.2
[4]: http://nuget.org/packages/Microsoft.Data.Edm/5.0.2
[5]: http://nuget.org/packages/System.Spatial/5.0.2
[6]: http://blogs.msdn.com/b/windowsazurestorage/archive/2011/09/15/windows-azure-tables-introducing-upsert-and-query-projection.aspx
[7]: http://msdn.microsoft.com/en-us/library/windowsazure/dn495001(v=azure.10).aspx
[8]: http://msdn.microsoft.com/en-us/library/windowsazure/dd179355
[9]: http://msdn.microsoft.com/en-us/library/windowsazure/gg433040.aspx
