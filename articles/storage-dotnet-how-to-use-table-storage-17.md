<properties linkid="dev-net-2-how-to-table-services" urlDisplayName="Table Service (2.0)" pageTitle="How to use table storage | Microsoft Azure" metaKeywords="Get started Azure table, Azure nosql, Azure large structured data store, Azure table, Azure table storage, Azure table .NET, Azure table storage .NET, Azure table C#, Azure table storage C#" description="Learn how to use table storage to create and delete tables and insert and query entities in a table." metaCanonical="" services="storage" documentationCenter=".NET" title="How to use the Table Storage Service" authors="" solutions="" manager="paulettm" editor="cgronlun" />

Utilisation du service de stockage de table
===========================================

[version 1.7](/en-us/develop/net/how-to-guides/table-services-v17/ "version 1.7") [version 2.0](/en-us/develop/net/how-to-guides/table-services/ "version 2.0")

Ce guide décrit le déroulement de scénarios courants dans le cadre de l'utilisation du service de stockage de table Azure. Les exemples sont écrits en C\# et utilisent l'API .NET. Les scénarios traités incluent la **création et la suppression d'une table, l'insertion et l'interrogation d'entités dans une table**. Pour plus d'informations sur les tables, consultez la section [Étapes suivantes](#next-steps).

Sommaire
--------

-   [Présentation du service de table](#what-is)
-   [Concepts](#concepts)
-   [Création d'un compte Azure Storage](#create-account)
-   [Configuration d'une chaîne de connexion de stockage](#setup-connection-string)
-   [Accès au stockage de tables par programme](#configure-access)
-   [Création d'une table](#create-table)
-   [Ajout d'une entité à une table](#add-entity)
-   [Insertion d'un lot d'entités](#insert-batch)
-   [Récupération de toutes les entités dans une partition](#retrieve-all-entities)
-   [Récupération d'un ensemble d'entités dans une partition](#retrieve-range-entities)
-   [Récupération d'une seule entité](#retrieve-single-entity)
-   [Mise à jour d'une entité](#update-entity)
-   [Interrogation d'un sous-ensemble de propriétés d'entité](#query-entity-properties)
-   [Insertion ou remplacement d'une entité](#insert-entity)
-   [Suppression d'une entité](#delete-entity)
-   [Suppression d'une table](#delete-table)
-   [Étapes suivantes](#next-steps)

[WACOM.INCLUDE [howto-table-storage](../includes/howto-table-storage.md)]

Création d'un compteCréation d'un compte de stockage Azure
----------------------------------------------------------

[WACOM.INCLUDE [create-storage-account](../includes/create-storage-account.md)]

Configuration d'une chaîne de connexionConfiguration d'une chaîne de connexion de stockage
------------------------------------------------------------------------------------------

L'API de stockage Azure .NET prend en charge l'utilisation d'une chaîne de connexion de stockage pour la configuration de points de terminaison et d'informations d'identification permettant d'accéder aux services de stockage. Vous pouvez placer votre chaîne de connexion de stockage dans un fichier de configuration, au lieu de la coder en dur dans le code :

-   Lorsque vous utilisez Azure Cloud Services, il est recommandé de stocker votre chaîne de connexion en utilisant le système de configuration de service Azure (fichiers `*.csdef` et `*.cscfg`).
-   Lorsque vous utilisez Sites Web Azure ou Azure Virtual Machines, il est recommandé de stocker votre chaîne de connexion en utilisant le système de configuration .NET (par exemple, un fichier `web.config`).

Dans les deux cas, vous pouvez récupérer votre chaîne de connexion en utilisant la méthode `CloudConfigurationManager.GetSetting`, tel qu'indiqué plus loin dans ce guide.

### Configuration de votre chaîne de connexion lors de l'utilisation de Cloud Services

Le mécanisme de configuration de service est propre aux projets Azure Cloud Services et vous permet de modifier les paramètres de configuration de façon dynamique à partir du portail de gestion Azure sans avoir à redéployer votre application.

Pour configurer votre chaîne de connexion dans la configuration de service Azure :

1.  Dans l'Explorateur de solutions de Visual Studio, dans le dossier **Roles** de votre projet de déploiement Azure, cliquez avec le bouton droit sur votre rôle Web ou votre rôle de travail, puis cliquez sur **Properties**.
     ![Blob5](./media/storage-dotnet-how-to-use-table-storage-17/blob5.png)

2.  Cliquez sur l'onglet **Settings**, puis sur le bouton **Add Setting**.
     ![Blob6](./media/storage-dotnet-how-to-use-table-storage-17/blob6.png)

    Une nouvelle entrée nommée **Setting1** est ensuite affichée dans la grille des paramètres.

3.  Dans le menu déroulant **Type** de l'entrée **Setting1**, sélectionnez **Connection String**.
     ![Blob7](./media/storage-dotnet-how-to-use-table-storage-17/blob7.png)

4.  Cliquez sur le bouton **...** situé à l'extrême droite de l'entrée **Setting1**. La boîte de dialogue **Storage Account Connection String** s'ouvre.

5.  Indiquez si vous voulez cibler l'émulateur de stockage (simulation de stockage Windows Azure sur votre machine locale) ou un compte de stockage existant dans le cloud. Le code de ce guide fonctionne dans les deux cas. Si vous voulez stocker des données d'objet blob dans le compte de stockage créé précédemment dans Azure, entrez la valeur **Primary Access Key** copiée lors de l'étape précédente de ce didacticiel.
     ![Blob8](./media/storage-dotnet-how-to-use-table-storage-17/blob8.png)

6.  Remplacez la valeur **Setting1** de l'entrée **Name** par un nom plus convivial, tel que **StorageConnectionString**. Le référencement de la chaîne de connexion dans le code sera effectué plus loin dans ce guide.
     ![Blob9](./media/storage-dotnet-how-to-use-table-storage-17/blob9.png)

### Configuration de votre chaîne de connexion dans le cadre de l'utilisation de Sites Web Azure ou Azure Virtual Machines

Lorsque vous utilisez Sites Web Azure ou Azure Virtual Machines, il est recommandé d'utiliser le système de configuration .NET (par exemple, `web.config`). Stockez la chaîne de connexion en utilisant l'élément `<appSettings>` :

    <configuration>
        <appSettings>
            <add key="StorageConnectionString"
                 value="DefaultEndpointsProtocol=https;AccountName=[AccountName];AccountKey=[AccountKey]" />
        </appSettings>
    </configuration>

Pour plus d'informations sur les chaînes de connexion de stockage, consultez la page [Configuration des chaînes de connexion](http://msdn.microsoft.com/en-us/library/windowsazure/ee758697.aspx).

Vous pouvez à présent effectuer les tâches présentées dans ce guide.

Accès par programme Accès au stockage de tables par programme
-------------------------------------------------------------

Ajoutez les déclarations d'espace de noms suivantes en haut de chaque fichier C\# dans lequel vous souhaitez accéder au stockage Azure par programme :

    using Microsoft.WindowsAzure;
    using Microsoft.WindowsAzure.StorageClient;

Vous pouvez utiliser les types **CloudStorageAccount** et **CloudConfigurationManager** pour récupérer votre chaîne de connexion de stockage, ainsi que vos informations de compte de stockage depuis la configuration de service Azure :

    CloudStorageAccount storageAccount = CloudStorageAccount.Parse(
        CloudConfigurationManager.GetSetting("ChaineConnexionStockage"));

Création d'une table Création d'une table
-----------------------------------------

Un objet **CloudTableClient** vous permet d'obtenir les objets de référence pour les tables et entités. Le code suivant crée un objet **CloudTableClient** et l'utilise pour créer une table. Tous les codes de ce guide utilisent une chaîne de connexion de stockage stockée dans la configuration de service de l'application Azure. Plusieurs méthodes permettent de créer un objet **CloudStorageAccount**.

    // Récupérez le compte de stockage à partir de la chaîne de connexion
    CloudStorageAccount storageAccount = CloudStorageAccount.Parse(
        CloudConfigurationManager.GetSetting("ChaineConnexionStockage"));

    // Créez le client de la table.
    CloudTableClient tableClient = storageAccount.CreateCloudTableClient();

    // Si la table n'existe pas, créez-la
    string tableName = "people";
    tableClient.CreateTableIfNotExist(tableName);

Ajout d'une entité à une table Ajout d'une entité à une table
-------------------------------------------------------------

Les entités mappent vers les objets C\# en utilisant une classe personnalisée dérivée d'une **TableServiceEntity**. Pour ajouter une entité à une table, commencez par créer une classe définissant les propriétés de votre entité. Le code suivant définit une classe d'entité utilisant le prénom du client en tant que clé de ligne et son nom de famille en tant que clé de partition. Ensemble, les clés de partition et de ligne d'une entité identifient l'entité de façon unique dans la table. Les requêtes d'entités dont les clés de partition sont identiques sont plus rapides que celles d'entités dont les clés de partition sont différentes.

    public class CustomerEntity : TableServiceEntity
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

Les opérations de table impliquant des entités ont besoin d'un objet **TableServiceContext**. Cet objet effectue le suivi de l'état côté client de toutes les entités de table créées et utilisées dans le code client. Conserver un objet côté client représentant chaque entité facilite les opérations de lecture, car seuls les objets comprenant des modifications sont mis à jour sur le service de Table lors des opérations d'enregistrement. Le code suivant crée un objet **TableServiceContext** en appelant la méthode **GetDataServiceContext**. Le code crée ensuite une instance de la classe **CustomerEntity**. Il appelle **serviceContext.AddObject** pour insérer la nouvelle entité dans la table. L'objet d'entité est ajouté à **serviceContext**, mais aucune opération de service n'a lieu. Le code envoie la nouvelle entité vers le service de Table lorsque la méthode **SaveChangesWithRetries** est appelée.

    // Récupérez le compte de stockage à partir de la chaîne de connexion
    CloudStorageAccount storageAccount = CloudStorageAccount.Parse(
        CloudConfigurationManager.GetSetting("ChaineConnexionStockage"));

    // Créez le client de la table.
    CloudTableClient tableClient = storageAccount.CreateCloudTableClient();

    // Obtenez le contexte du service de données
    TableServiceContext serviceContext = tableClient.GetDataServiceContext();

    // Créez une entité de client
    CustomerEntity customer1 = new CustomerEntity("Harp", "Walter");
    customer1.Email = "Walter@contoso.com";
    customer1.PhoneNumber = "425-555-0101";

    // Ajoutez le nouveau client à la table people
    serviceContext.AddObject("people", customer1);

    // Soumettez l'opération au service de Table
    serviceContext.SaveChangesWithRetries();

Insertion d'un lot d'entités Insertion d'un lot d'entités
---------------------------------------------------------

Vous pouvez insérer un lot d'entités dans le service de Table en une seule opération d'écriture. Le code suivant crée trois objets d'entité et les ajoute au contexte du service avec la méthode **AddObject**. Il appelle ensuite **SaveChangesWithRetries** avec le paramètre **SaveChangesOptions.Batch**. Si vous omettez **SaveChangesOptions.Batch**, trois appels seront passés séparément au service de Table. Autres remarques sur les opérations par lot :

1.  Vous pouvez effectuer des mises à jour, des suppressions et des insertions.
2.  Une seule opération par lot peut inclure jusqu'à 100 entités.
3.  Toutes les entités d'une opération par lot doivent avoir la même clé de partition.

<!-- -->

    // Récupérez le compte de stockage à partir de la chaîne de connexion
    CloudStorageAccount storageAccount = CloudStorageAccount.Parse(
        CloudConfigurationManager.GetSetting("ChaineConnexionStockage"));

    // Créez le client de la table.
    CloudTableClient tableClient = storageAccount.CreateCloudTableClient();
    string tableName = "people";

    // Obtenez le contexte du service de données
    TableServiceContext serviceContext = tableClient.GetDataServiceContext();

    // Créez une entité de client et ajoutez-la à la table
    CustomerEntity customer = new CustomerEntity("Smith", "Jeff");
    customer.Email = "Jeff@contoso.com";
    customer.PhoneNumber = "425-555-0104";
    serviceContext.AddObject(tableName, customer);

    // Créez un autre entité de client et ajoutez-la à la table
    CustomerEntity customer2 = new CustomerEntity("Smith", "Ben");
    customer2.Email = "Ben@contoso.com";
    customer2.PhoneNumber = "425-555-0102";
    serviceContext.AddObject(tableName, customer2);

    // Créez une entité de client et ajoutez-la à la table
    CustomerEntity customer3 = new CustomerEntity("Smith", "Denise");
    customer3.Email = "Denise@contoso.com";
    customer3.PhoneNumber = "425-555-0103";
    serviceContext.AddObject(tableName, customer3);

    // Soumettez l'opération au service de Table
    serviceContext.SaveChangesWithRetries(SaveChangesOptions.Batch);

Récupération de toutes les entités Récupération de toutes les entités dans une partition
----------------------------------------------------------------------------------------

Pour exécuter une requête de table pour les entités d'une partition, utilisez une requête LINQ. Appelez **serviceContext.CreateQuery** pour créer une requête à partir de votre source de données. Le code suivant indique un filtre pour les entités où 'Smith' est la clé de partition. Appelez **AsTableServiceQuery&lt;CustomerEntity\>** sur le résultat de la requête LINQ pour terminer la création de l'objet **CloudTableQuery**. Vous pouvez utiliser l'objet **partitionQuery** que vous avez créé dans une boucle **foreach** pour traiter les résultats. Ce code imprime les champs de chaque entité dans les résultats de requête vers la console.

    // Récupérez le compte de stockage à partir de la chaîne de connexion
    CloudStorageAccount storageAccount = CloudStorageAccount.Parse(
        CloudConfigurationManager.GetSetting("ChaineConnexionStockage"));

    // Créez le client de la table.
    CloudTableClient tableClient = storageAccount.CreateCloudTableClient();

    // Obtenez le contexte du service de données
    TableServiceContext serviceContext = tableClient.GetDataServiceContext();

    // Spécifiez une requête de partition, en utilisant "Smith" comme clé de partition
    CloudTableQuery<CustomerEntity> partitionQuery =
        (from e in serviceContext.CreateQuery<CustomerEntity>("people")
         where e.PartitionKey == "Smith"
         select e).AsTableServiceQuery<CustomerEntity>();

    // Créez une boucle sur les résultats, pour afficher les informations sur l'entité
    foreach (CustomerEntity entity in partitionQuery)
    {
        Console.WriteLine("{0}, {1}\t{2}\t{3}", entity.PartitionKey, entity.RowKey,
            entity.Email, entity.PhoneNumber);
    }

Récupération d'un ensemble d'entités Récupération d'un ensemble d'entités dans une partition
--------------------------------------------------------------------------------------------

Si vous ne voulez pas exécuter une requête pour toutes les entités d'une partition, vous pouvez spécifier une plage en utilisant la méthode **CompareTo** au lieu d'utiliser les opérateurs habituels supérieur à (\>) et inférieur à (&lt;). En effet, ces derniers génèrent une construction de requête incorrecte. Le code suivant utilise deux filtres pour obtenir toutes les entités dans la partition 'Smith' où la clé de ligne (prénom) commence par une lettre allant jusqu'à « E », puis imprime les résultats de la requête. Si vous utilisez les entités ajoutées à la table de la section de ce guide consacrée à l'insertion de lot, seulement deux entités sont renvoyées (Ben et Denise Smith). Jeff Smith n'est pas inclus.

    // Récupérez le compte de stockage à partir de la chaîne de connexion
    CloudStorageAccount storageAccount = CloudStorageAccount.Parse(
        CloudConfigurationManager.GetSetting("ChaineConnexionStockage"));

    // Créez le client de la table.
    CloudTableClient tableClient = storageAccount.CreateCloudTableClient();

    // Obtenez le contexte du service de données
    TableServiceContext serviceContext = tableClient.GetDataServiceContext();

    // Spécifiez une requête de partition, en utilisant "Smith" comme clé de partition
    // avec la clé de ligne commençant par une lettre allant jusqu'à "E"
    CloudTableQuery<CustomerEntity> entityRangeQuery =
        (from e in serviceContext.CreateQuery<CustomerEntity>("people")
         where e.PartitionKey == "Smith" && e.RowKey.CompareTo("E") < 0
         select e).AsTableServiceQuery<CustomerEntity>();

    // Créez une boucle sur les résultats, pour afficher les informations sur l'entité
    foreach (CustomerEntity entity in entityRangeQuery)
    {
        Console.WriteLine("{0}, {1}\t{2}\t{3}", entity.PartitionKey, entity.RowKey,
            entity.Email, entity.PhoneNumber);
    }

Récupération d'une seule entité Récupération d'une seule entité
---------------------------------------------------------------

Vous pouvez écrire une requête pour récupérer une seule entité. Le code suivant utilise deux filtres pour spécifier le client 'Jeff Smith'. Au lieu d'appeler la méthode **AsTableServiceQuery**, ce code appelle la méthode **FirstOrDefault**. Celle-ci renvoie une seule entité et non une collection, et le code attribue la valeur renvoyée directement à un objet **CustomerEntity**. La valeur null est renvoyée si aucune entité n'a de correspondance exacte avec la clé de partition et de ligne. La méthode la plus rapide pour récupérer une seule entité dans le service de table consiste à spécifier une clé de partition et une clé de ligne.

    // Récupérez le compte de stockage à partir de la chaîne de connexion
    CloudStorageAccount storageAccount = CloudStorageAccount.Parse(
        CloudConfigurationManager.GetSetting("ChaineConnexionStockage"));

    // Créez le client de la table.
    CloudTableClient tableClient = storageAccount.CreateCloudTableClient();

    // Obtenez le contexte du service de données
    TableServiceContext serviceContext = tableClient.GetDataServiceContext();

    // Renvoyez l'entité avec la clé de partition "Smith" et la clé de ligne "Jeff"
    CustomerEntity specificEntity =
        (from e in serviceContext.CreateQuery<CustomerEntity>("people")
         where e.PartitionKey == "Smith" && e.RowKey == "Jeff"
         select e).FirstOrDefault();

Mise à jour d'une entité Mise à jour d'une entité
-------------------------------------------------

Pour mettre à jour une entité, récupérez-la dans le service de Table, modifiez l'objet d'entité, puis enregistrez les modifications dans le service de Table. Le code suivant modifie le numéro de téléphone d'un client existant. Au lieu d'appeler **AddObject** comme pour l'insertion, ce code appelle **UpdateObject**. La méthode **SaveChangesWithRetries** appelle le service de Table et l'entité est mise à jour, sauf si une autre application l'a changée après son extraction par cette application. Lorsque c'est le cas, une exception est levée et l'entité doit être récupérée, modifiée, puis de nouveau enregistrée. Ce modèle de nouvelle tentative est courant dans un système de stockage distribué.

    // Récupérez le compte de stockage à partir de la chaîne de connexion
    CloudStorageAccount storageAccount = CloudStorageAccount.Parse(
        CloudConfigurationManager.GetSetting("ChaineConnexionStockage"));

    // Créez le client de la table.
    CloudTableClient tableClient = storageAccount.CreateCloudTableClient();

    // Obtenez le contexte du service de données
    TableServiceContext serviceContext = tableClient.GetDataServiceContext();

    // Renvoyez l'entité avec la clé de partition "Smith" et la clé de ligne "Jeff"
    CustomerEntity specificEntity =
        (from e in serviceContext.CreateQuery<CustomerEntity>("people")
         where e.PartitionKey == "Smith" && e.RowKey == "Jeff"
         select e).FirstOrDefault();

    // Spécifiez un nouveau numéro de téléphone
    specificEntity.PhoneNumber = "425-555-0105";

    // Mettez à jour l'entité
    serviceContext.UpdateObject(specificEntity);

    // Soumettez l'opération au service de Table
    serviceContext.SaveChangesWithRetries();

Interrogation d'un sous-ensemble de propriétés Interrogation d'un sous-ensemble de propriétés d'entité
------------------------------------------------------------------------------------------------------

Vous pouvez utiliser une requête de table pour récupérer uniquement quelques propriétés d'une entité. Cette technique, nommée « projection », réduit la consommation de bande passante et peut améliorer les performances des requêtes, notamment pour les entités volumineuses. La requête contenue dans le code suivant renvoie uniquement les adresses de messagerie électronique des entités dans la table. Pour plus d'informations sur la projection, consultez ce [billet de blog](http://blogs.msdn.com/b/windowsazurestorage/archive/2011/09/15/windows-azure-tables-introducing-upsert-and-query-projection.aspx). Notez que la projection n'est pas prise en charge sur l'émulateur de stockage local : ce code s'exécute donc uniquement lors de l'utilisation d'un compte sur le service de table.

    // Récupérez le compte de stockage à partir de la chaîne de connexion
    CloudStorageAccount storageAccount = CloudStorageAccount.Parse(
        CloudConfigurationManager.GetSetting("ChaineConnexionStockage"));

    // Créez le client de la table.
    CloudTableClient tableClient = storageAccount.CreateCloudTableClient();

    // Obtenez le contexte du service de données
    TableServiceContext serviceContext = tableClient.GetDataServiceContext();

    // Définissez une requête de type projection pour récupérer uniquement la propriété Email
    var projectionQuery = 
        from e in serviceContext.CreateQuery<CustomerEntity>("people")
        select new
        {
            Email = e.Email
            // Vous pouvez spécifier d'autres champs ici
        };

    // Créez une boucle sur les résultats, pour afficher la valeur Email
    foreach (var person in projectionQuery)
    {
        Console.WriteLine(person.Email);
    }

Insertion ou remplacement d'une entité Insertion ou remplacement d'une entité
-----------------------------------------------------------------------------

Il arrive souvent de vouloir ajouter une entité à une table sans savoir si elle existe dans la table. Une opération d'insertion ou de remplacement permet d'envoyer une seule requête pour insérer l'entité si elle n'existe pas ou la remplacer si elle existe. À partir des exemples précédents, le code suivant insère ou remplace l'entité 'Walter Harp'. Après la création d'une entité, ce code appelle la méthode **serviceContext.AttachTo**. Il appelle ensuite **UpdateObject**, puis **SaveChangesWithRetries** avec le paramètre **SaveChangesOptions.ReplaceOnUpdate**. L'omission du paramètre **SaveChangesOptions.ReplaceOnUpdate** génère une opération d'insertion ou de fusion. Notez que l'opération d'insertion ou de remplacement n'est pas prise en charge sur l'émulateur de stockage local : ce code s'exécute donc uniquement lors de l'utilisation d'un compte sur le service de Table. Pour plus d'informations sur les opérations d'insertion ou de remplacement et d'insertion ou de fusion, consultez ce [billet de blog](http://blogs.msdn.com/b/windowsazurestorage/archive/2011/09/15/windows-azure-tables-introducing-upsert-and-query-projection.aspx).

    // Récupérez le compte de stockage à partir de la chaîne de connexion
    CloudStorageAccount storageAccount = CloudStorageAccount.Parse(
        CloudConfigurationManager.GetSetting("ChaineConnexionStockage"));

    // Créez le client de la table.
    CloudTableClient tableClient = storageAccount.CreateCloudTableClient();

    // Obtenez le contexte du service de données
    TableServiceContext serviceContext = tableClient.GetDataServiceContext();

    // Créez une entité de client
    CustomerEntity customer5 = new CustomerEntity("Harp", "Walter");
    customer5.Email = "Walter@contoso.com";
    customer5.PhoneNumber = "425-555-0106";

    // Associez ce client à la table people
    serviceContext.AttachTo("people", customer5);

    // Insérez ce client s'il est nouveau, ou remplacez-le s'il existe déjà
    serviceContext.UpdateObject(customer5);

    // Soumettez l'opération au service de Table en utilisant l'option ReplaceOnUpdate
    serviceContext.SaveChangesWithRetries(SaveChangesOptions.ReplaceOnUpdate);

Suppression d'une entité Suppression d'une entité
-------------------------------------------------

Il est facile de supprimer une entité après l'avoir récupérée. Vous pouvez également utiliser la méthode **AttachTo** pour commencer à la suivre sans la récupérer sur le serveur (voir les opérations d'insertion ou de remplacement ci-dessus). Lorsque l'entité est suivie avec **serviceContext**, appelez **DeleteObject** avec l'entité à supprimer. Appelez ensuite **SaveChangesWithRetries**. Le code suivant récupère et supprime une entité de client.

    // Récupérez le compte de stockage à partir de la chaîne de connexion
    CloudStorageAccount storageAccount = CloudStorageAccount.Parse(
        CloudConfigurationManager.GetSetting("ChaineConnexionStockage"));

    // Créez le client de la table.
    CloudTableClient tableClient = storageAccount.CreateCloudTableClient();

    // Obtenez le contexte du service de données
    TableServiceContext serviceContext = tableClient.GetDataServiceContext();

    CustomerEntity specificEntity =
        (from e in serviceContext.CreateQuery<CustomerEntity>("people")
         where e.PartitionKey == "Smith" && e.RowKey == "Jeff"
         select e).FirstOrDefault();

    // Supprimez l'entité
    serviceContext.DeleteObject(specificEntity);

    // Soumettez l'opération au service de Table
    serviceContext.SaveChangesWithRetries();

Suppression d'une table Suppression d'une table
-----------------------------------------------

Pour finir, le code suivant supprime une table d'un compte de stockage. Une table supprimée ne pourra plus être recréée pendant un certain temps.

    // Récupérez le compte de stockage à partir de la chaîne de connexion
    CloudStorageAccount storageAccount = CloudStorageAccount.Parse(
        CloudConfigurationManager.GetSetting("ChaineConnexionStockage"));

    // Créez le client de la table.
    CloudTableClient tableClient = storageAccount.CreateCloudTableClient();

    // Supprimez la table, si elle existe
    tableClient.DeleteTableIfExist("people");

Étapes suivantesÉtapes suivantes
--------------------------------

Maintenant que vous avez appris les bases du stockage de tables, suivez ces liens pour apprendre des tâches de stockage plus complexes.

-   Pour plus d'informations sur les API disponibles, consultez la documentation de référence des services d'objets blob :
    -   [Référence de bibliothèque cliente .NET](http://msdn.microsoft.com/en-us/library/windowsazure/wl_svchosting_mref_reference_home)
    -   [Référence d'API REST](http://msdn.microsoft.com/en-us/library/windowsazure/dd179355)
-   Pour plus d'informations sur les tâches avancées que vous pouvez effectuer avec le stockage Azure, consultez la page [Stockage et accessibilité des données dans Azure](http://msdn.microsoft.com/en-us/library/windowsazure/gg433040.aspx).
-   Pour plus d'informations sur les autres options de stockage de données dans Azure, consultez d'autres guides de fonctionnalités.
    -   Utilisez le [stockage d'objets blob](/en-us/develop/net/how-to-guides/blob-storage/) pour stocker des données non structurées.
    -   Utilisez une [base de données SQL](/en-us/develop/net/how-to-guides/sql-database/) pour stocker des données relationnelles.

