<properties linkid="dev-java-how-to-use-table-storage" urlDisplayName="Table Service" pageTitle="How to use table storage (Java) | Microsoft Azure" metaKeywords="Azure table storage service, Azure table service Java, table storage Java" description="Learn how to use the table storage service in Azure. Code samples are written in Java code." metaCanonical="" services="storage" documentationCenter="Java" title="How to use the Table storage service from Java" authors="" solutions="" manager="" editor="" />

Utilisation du service de stockage de table à partir de Java
============================================================

Ce guide décrit le déroulement de scénarios courants dans le cadre de l'utilisation du service de stockage de table Azure. Les exemples sont écrits en code Java. Les scénarios traités incluent la **création et la suppression d'une table, l'insertion et l'interrogation d'entités dans une table**. Pour plus d'informations sur les tables, consultez la section [Étapes suivantes](#NextSteps).

Sommaire
--------

-   [Présentation du stockage de table](#what-is)
-   [Concepts](#Concepts)
-   [Création d'un compte de stockage Azure](#CreateAccount)
-   [Création d'une application Java](#CreateApplication)
-   [Configuration de votre application pour accéder au stockage de table](#ConfigureStorage)
-   [Configuration d'une chaîne de connexion de stockage Azure](#ConnectionString)
-   [Création d'une table](#CreateTable)
-   [Ajout d'une entité à une table](#AddEntity)
-   [Insertion d'un lot d'entités](#InsertBatch)
-   [Extraction de toutes les entités d'une partition](#RetrieveEntities)
-   [Extraction d'un ensemble d'entités dans une partition](#RetrieveRange)
-   [Extraction d'une seule entité](#RetriveSingle)
-   [Modification d'une entité](#ModifyEntity)
-   [Interrogation d'un sous-ensemble de propriétés d'entité](#QueryProperties)
-   [Insertion ou remplacement d'une entité](#InsertOrReplace)
-   [Suppression d'une entité](#DeleteEntity)
-   [Suppression d'une table](#DeleteTable)
-   [Étapes suivantes](#NextSteps)

[WACOM.INCLUDE [howto-table-storage](../includes/howto-table-storage.md)]

Création d'un compte de stockage Azure
--------------------------------------

[WACOM.INCLUDE [create-storage-account](../includes/create-storage-account.md)]

Création d'une application Java
-------------------------------

Dans ce guide, vous allez utiliser des fonctionnalités de stockage qui peuvent être exécutées dans une application Java en local, ou dans le code s'exécutant dans un rôle Web ou un rôle de travail dans Azure. Nous partons du principe que vous avez téléchargé et installé le Kit de développement Java (JDK) et suivi les instructions figurant sur la page [Kit de développement logiciel (SDK) Azure pour Java](http://www.windowsazure.com/en-us/develop/java/) pour installer les bibliothèques Azure pour Java et le Kit de développement logiciel (SDK) Azure, et que vous avez créé un compte de stockage Azure dans votre abonnement Azure.

Vous pouvez utiliser tous les outils de développement pour créer votre application, y compris Bloc-notes. Vous devez seulement être en mesure de compiler un projet Java et de référencer des bibliothèques Azure pour Java.

Configuration de votre application pour accéder au stockage de table
--------------------------------------------------------------------

Ajoutez l'instruction import suivante au début du fichier Java dans lequel vous voulez utiliser des API de stockage Azure pour accéder aux tables :

    // Ajoutez les imports suivants pour utiliser des API de tables
    import com.microsoft.windowsazure.services.core.storage.*;
    import com.microsoft.windowsazure.services.table.client.*;
    import com.microsoft.windowsazure.services.table.client.TableQuery.*;

Configuration d'une chaîne de connexion de stockage Azure
---------------------------------------------------------

Un client de stockage Azure utilise une chaîne de connexion de stockage pour stocker des points de terminaison et des informations d’identification permettant d'accéder aux services de gestion des données. Lors de l'exécution d'une application cliente, vous devez spécifier la chaîne de connexion de stockage au format suivant, en utilisant le nom de votre compte de stockage et la clé d'accès primaire pour le compte de stockage, répertoriés sur le portail de gestion pour les valeurs *AccountName* et *AccountKey*. Cet exemple vous montre comment déclarer un champ statique pour qu'il contienne une chaîne de connexion :

    // Définissez la chaîne de connexion à l'aide de vos valeurs
    public static final String storageConnectionString = 
        "DefaultEndpointsProtocol=http;" + 
        "AccountName=your_storage_account;" + 
        "AccountKey=your_storage_account_key";

Dans une application exécutée au sein d'un rôle dans Azure, cette chaîne peut être stockée dans le fichier de configuration de service ServiceConfiguration.cscfg et elle est accessible en appelant la méthode **RoleEnvironment.getConfigurationSettings**. Voici un exemple de code vous permettant d'extraire la chaîne de connexion à partir d'un élément **Setting** nommé *StorageConnectionString* dans le fichier de configuration de service :

    // Extrayez le compte de stockage à partir de la chaîne de connexion
    String storageConnectionString = 
        RoleEnvironment.getConfigurationSettings().get("StorageConnectionString");

Les exemples ci-dessous partent du principe que vous avez utilisé une de ces deux définitions pour obtenir la chaîne de connexion de stockage.

Création d'une table
--------------------

Un objet **CloudTableClient** vous permet d'obtenir les objets de référence pour les tables et entités. Le code suivant crée un objet **CloudTableClient** et l'utilise pour créer une table. Tous les codes de ce guide utilisent une chaîne de connexion de stockage stockée dans la configuration de service de l'application Azure. Plusieurs méthodes permettent de créer un objet **CloudStorageAccount**.

    // Extrayez le compte de stockage à partir de la chaîne de connexion
    CloudStorageAccount storageAccount =
        CloudStorageAccount.parse(storageConnectionString);
     
    // Créez le client de la table.
    CloudTableClient tableClient = storageAccount.createCloudTableClient();
     
    // Si la table n’existe pas, créez-la.
    String tableName = "people";
    tableClient.createTableIfNotExists(tableName);

Ajout d'une entité à une table
------------------------------

Les entités mappent vers les objets Java en utilisant une implémentation de classe personnalisée **TableEntity**. Par souci pratique, la classe **TableServiceEntity** implémente **TableEntity** et utilise les reflets pour mapper les propriétés vers les méthodes « getter » et « setter » nommées pour les propriétés. Pour ajouter une entité à une table, commencez par créer une classe définissant les propriétés de votre entité. Le code suivant définit une classe d'entité utilisant le prénom du client en tant que clé de ligne et son nom de famille en tant que clé de partition. Ensemble, les clés de partition et de ligne d'une entité identifient l'entité de façon unique dans la table. Les requêtes d'entités dont les clés de partition sont identiques sont plus rapides que celles d'entités dont les clés de partition sont différentes.

    public class CustomerEntity extends TableServiceEntity {
        public CustomerEntity(String lastName, String firstName) {
            this.partitionKey = lastName;
            this.rowKey = firstName;
        }

        public CustomerEntity() { }

        String email;
        String phoneNumber;
        
        public String getEmail() {
            return this.email;
        }
        
        public void setEmail(String email) {
            this.email = email;
        }
        
        public String getPhoneNumber() {
            return this.phoneNumber;
        }
        
        public void setPhoneNumber(String phoneNumber) {
            this.phoneNumber = phoneNumber;
        }
    }

Les opérations de table impliquant des entités ont besoin d'un objet **TableOperation**. Cet objet définit l'opération à effectuer sur une entité, qui peut être exécutée avec un objet **CloudTableClient**. Le code suivant crée une instance de la classe **CustomerEntity** avec des données client à stocker. Le code appelle ensuite **TableOperation.insert** pour créer un objet **TableOperation** pour insérer une entité dans une table et y associer le nouvel élément **CustomerEntity**. Finalement, le code appelle la méthode **execute** sur l'élément **CloudTableClient**, en spécifiant la table « people » et le nouvel élément **TableOperation**, qui envoie ensuite une requête vers le service de stockage pour insérer la nouvelle entité client dans la table « people ».

    // Extrayez le compte de stockage à partir de la chaîne de connexion
    CloudStorageAccount storageAccount =
        CloudStorageAccount.parse(storageConnectionString);
     
    // Créez le client de la table.
    CloudTableClient tableClient = storageAccount.createCloudTableClient();
     
    // Créez une entité de client.
    CustomerEntity customer1 = new CustomerEntity("Harp", "Walter");
    customer1.setEmail("Walter@contoso.com");
    customer1.setPhoneNumber("425-555-0101");

    // Créez une opération pour ajouter le nouveau client à la table people.
    TableOperation insertCustomer1 = TableOperation.insert(customer1);

    // Soumettez l'opération au service de Table.
    tableClient.execute("people", insertCustomer1);

Insertion d'un lot d'entités
----------------------------

Vous pouvez insérer un lot d'entités dans le service de Table en une seule opération d'écriture. Le code suivant crée un objet **TableBatchOperation**, puis y ajoute trois opérations d'insertion. Chaque opération d'insertion est ajoutée en créant un objet d'entité, en définissant ses valeurs, puis en appelant la méthode **insert** sur l'objet **TableBatchOperation** pour associer la nouvelle entité avec une nouvelle opération d'insertion. Le code applique ensuite la commande **execute** sur **CloudTableClient**, en spécifiant la table « people » et l'objet **TableBatchOperation**, qui envoie le lot d'opérations de table vers le service de stockage en une seule requête. Quelques remarques sur les opérations par lots :

1.  Vous pouvez effectuer jusqu'à 100 opérations d'insertion, de suppression, de fusion, de remplacement, d'insertion ou fusion et d'insertion ou de remplacement dans n'importe quelle combinaison en un seul lot.
2.  Une opération par lot peut comporter une opération d'extraction, s'il s'agit de la seule opération du lot.
3.  Toutes les entités d'une opération par lot doivent avoir la même clé de partition.
4.  Une opération par lot est limitée à une charge utile de données de 4 Mo.

<!-- -->

    // Extrayez le compte de stockage à partir de la chaîne de connexion
    CloudStorageAccount storageAccount =
        CloudStorageAccount.parse(storageConnectionString);
     
    // Créez le client de la table.
    CloudTableClient tableClient = storageAccount.createCloudTableClient();
     
    // Définissez l’opération par lot.
    TableBatchOperation batchOperation = new TableBatchOperation();

    // Créez une entité de client à ajouter à la table
    CustomerEntity customer = new CustomerEntity("Smith", "Jeff");
    customer.setEmail("Jeff@contoso.com");
    customer.setPhoneNumber("425-555-0104");
    batchOperation.insert(customer);

    // Créez une autre entité de client à ajouter à la table
    CustomerEntity customer2 = new CustomerEntity("Smith", "Ben");
    customer2.setEmail("Ben@contoso.com");
    customer2.setPhoneNumber("425-555-0102");
    batchOperation.insert(customer2);

    // Créez une troisième entité de client à ajouter à la table
    CustomerEntity customer3 = new CustomerEntity("Smith", "Denise");
    customer3.setEmail("Denise@contoso.com");
    customer3.setPhoneNumber("425-555-0103");
    batchOperation.insert(customer3);

    // Exécutez le lot d'opérations sur la table « people ».
    tableClient.execute("people", batchOperation);

Extraction de toutes les entités d'une partition
------------------------------------------------

Pour exécuter une requête de table pour les entités d'une partition, utilisez une requête **TableQuery**. Appelez **TableQuery.from** pour créer une requête sur une table donnée qui renvoie un type de résultat spécifique. Le code suivant indique un filtre pour les entités où 'Smith' est la clé de partition. **TableQuery.generateFilterCondition** est une méthode d'aide à la création de filtres pour requêtes. Appelez **where** sur la référence renvoyée par la méthode **TableQuery.from** pour appliquer le filtre à la requête. Lorsque la requête est exécutée avec un appel vers **execute** sur l'objet **CloudTableClient**, elle renvoie un élément **Iterator** avec le type de résultat **CustomerEntity** spécifié. Vous pouvez ensuite utiliser l'élément **Iterator** renvoyé dans une boucle foreach pour traiter les résultats. Ce code imprime les champs de chaque entité dans les résultats de requête vers la console.

    // Extrayez le compte de stockage à partir de la chaîne de connexion
    CloudStorageAccount storageAccount =
        CloudStorageAccount.parse(storageConnectionString);
     
    // Créez le client de la table.
    CloudTableClient tableClient = storageAccount.createCloudTableClient();
     
    // Créez une condition de filtre dans laquelle la clé de partition est « Smith ».
    String partitionFilter = TableQuery.generateFilterCondition(
        TableConstants.PARTITION_KEY, 
        QueryComparisons.EQUAL,
        "Smith");

    // Spécifiez une requête de partition, en utilisant "Smith" comme filtre de clé de partition
    TableQuery<CustomerEntity> partitionQuery =
        TableQuery.from("people", CustomerEntity.class)
        .where(partitionFilter);

    // Créez une boucle entre les résultats, qui affiche les informations sur l'entité.
    for (CustomerEntity entity : tableClient.execute(partitionQuery)) {
        System.out.println(entity.getPartitionKey() + " " + entity.getRowKey() + 
            "\t" + entity.getEmail() + "\t" + entity.getPhoneNumber());
    }

Extraction d'un ensemble d'entités dans une partition
-----------------------------------------------------

Si vous ne voulez pas interroger toutes les entités d'une partition, vous pouvez définir une plage en utilisant les opérateurs de comparaison dans un filtre. Le code suivant combine deux filtres pour obtenir toutes les entités dans la partition 'Smith' où la clé de ligne (prénom) commence par une lettre allant jusqu'à « E », puis imprime les résultats de la requête. Si vous utilisez les entités ajoutées à la table de la section de ce guide consacrée à l'insertion de lot, seulement deux entités sont renvoyées (Ben et Denise Smith). Jeff Smith n'est pas inclus.

    // Extrayez le compte de stockage à partir de la chaîne de connexion
    CloudStorageAccount storageAccount =
        CloudStorageAccount.parse(storageConnectionString);
     
    // Créez le client de la table.
    CloudTableClient tableClient = storageAccount.createCloudTableClient();
     
    // Créez une condition de filtre dans laquelle la clé de partition est « Smith ».
    String partitionFilter = TableQuery.generateFilterCondition(
        TableConstants.PARTITION_KEY, 
        QueryComparisons.EQUAL,
        "Smith");

    // Créez une condition de filtre dans laquelle la clé de ligne est inférieure à la lettre « E ».
    String rowFilter = TableQuery.generateFilterCondition(
        TableConstants.ROW_KEY, 
        QueryComparisons.LESS_THAN,
        "E");

    // Combine les deux conditions dans une expression de filtre.
    String combinedFilter = TableQuery.combineFilters(partitionFilter, 
            Operators.AND, rowFilter);

    // Spécifiez une requête de plage, en utilisant "Smith" comme clé de partition
    // avec la clé de ligne commençant par une lettre allant jusqu'à "E".
    TableQuery<CustomerEntity> rangeQuery =
        TableQuery.from("people", CustomerEntity.class)
        .where(combinedFilter);

    // Créez une boucle sur les résultats, pour afficher les informations sur l'entité
    for (CustomerEntity entity : tableClient.execute(rangeQuery)) {
        System.out.println(entity.getPartitionKey() + " " + entity.getRowKey() + 
            "\t" + entity.getEmail() + "\t" + entity.getPhoneNumber());
    }

Extraction d'une seule entité
-----------------------------

Vous pouvez écrire une requête pour extraire une seule entité. Le code suivant appelle **TableOperation.retrieve** avec les clés de partition et de ligne pour spécifier le client « Jeff Smith », ce qui évite de créer une requête **TableQuery** et d'utiliser des filtres pour parvenir au même résultat. Lors de son exécution, l'opération d'extraction renvoie une seule entité, plutôt que plusieurs. La méthode **getResultAsType** cible le résultat selon le type de cible d'attribution, à savoir un objet **CustomerEntity**. Si ce type n'est pas compatible avec celui spécifié pour la requête, une exception est levée. La valeur null est renvoyée si aucune entité n'a de correspondance exacte avec la clé de partition et de ligne. La méthode la plus rapide pour extraire une seule entité dans le service de table consiste à spécifier une clé de partition et une clé de ligne.

    // Extrayez le compte de stockage à partir de la chaîne de connexion
    CloudStorageAccount storageAccount =
        CloudStorageAccount.parse(storageConnectionString);
     
    // Créez le client de la table.
    CloudTableClient tableClient = storageAccount.createCloudTableClient();
     
    // Extrayez l'entité avec la clé de partition "Smith" et la clé de ligne "Jeff"
    TableOperation retrieveSmithJeff = 
        TableOperation.retrieve("Smith", "Jeff", CustomerEntity.class);

    // Envoyez l'opération au service de Table et obtenez l'entité spécifique.
    CustomerEntity specificEntity =
        tableClient.execute("people", retrieveSmithJeff).getResultAsType();

Modification d'une entité
-------------------------

Pour modifier une entité, extrayez-la dans le service de Table, apportez les modifications souhaitées à l'objet d'entité, puis enregistrez les modifications sur le service de Table via une opération de remplacement ou de fusion. Le code suivant modifie le numéro de téléphone d'un client existant. Plutôt que d'appeler **TableOperation.insert** comme nous l'avons fait pour l'opération d'insertion, ce code appelle **TableOperation.replace**. La méthode **CloudTableClient.execute** appelle le service de Table et l'entité est remplacée, sauf si une autre application l'a changée après son extraction par cette application. Lorsque c'est le cas, une exception est levée et l'entité doit être extraite, modifiée, puis de nouveau enregistrée. Ce modèle de nouvelle tentative d'accès concurrentiel optimiste est courant dans un système de stockage distribué.

    // Extrayez le compte de stockage à partir de la chaîne de connexion
    CloudStorageAccount storageAccount =
        CloudStorageAccount.parse(storageConnectionString);
     
    // Créez le client de la table.
    CloudTableClient tableClient = storageAccount.createCloudTableClient();
     
    // Extrayez l'entité avec la clé de partition "Smith" et la clé de ligne "Jeff".
    TableOperation retrieveSmithJeff = 
        TableOperation.retrieve("Smith", "Jeff", CustomerEntity.class);

    // Envoyez l'opération au service de Table et obtenez l'entité spécifique.
    CustomerEntity specificEntity =
        tableClient.execute("people", retrieveSmithJeff).getResultAsType();
        
    // Spécifiez un nouveau numéro de téléphone.
    specificEntity.setPhoneNumber("425-555-0105");

    // Créez une opération pour remplacer l'entité.
    TableOperation replaceEntity = TableOperation.replace(specificEntity);

    // Soumettez l'opération au service de Table.
    tableClient.execute("people", replaceEntity);

Interrogation d'un sous-ensemble de propriétés d'entité
-------------------------------------------------------

Vous pouvez utiliser une requête de table pour extraire uniquement quelques propriétés d'une entité. Cette technique, nommée « projection », réduit la consommation de bande passante et peut améliorer les performances des requêtes, notamment pour les entités volumineuses. La requête contenue dans le code suivant utilise la méthode **select** pour renvoyer uniquement les adresses de messagerie des entités dans la table. Les résultat sont projetés dans un ensemble d'éléments **String** avec l'aide d'un élément **EntityResolver**, qui effectue la conversion de type des entités renvoyées depuis le serveur. Pour plus d'informations sur la projection, consultez ce [billet de blog](http://blogs.msdn.com/b/windowsazurestorage/archive/2011/09/15/windows-azure-tables-introducing-upsert-and-query-projection.aspx). Notez que la projection n'est pas prise en charge sur l'émulateur de stockage local : ce code s'exécute donc uniquement lors de l'utilisation d'un compte sur le service de table.

    // Extrayez le compte de stockage à partir de la chaîne de connexion
    CloudStorageAccount storageAccount =
        CloudStorageAccount.parse(storageConnectionString);
     
    // Créez le client de la table.
    CloudTableClient tableClient = storageAccount.createCloudTableClient();
     
    // Définissez une requête de type projection pour extraire uniquement la propriété Email
    TableQuery<CustomerEntity> projectionQuery = 
        TableQuery.from("people", CustomerEntity.class)
        .select(new String[] {"Email"});

    // Définissez un programme de résolution d’entité pour projeter l'entité dans la valeur Email.
    EntityResolver<String> emailResolver = new EntityResolver<String>() {
        @Override
        public String resolve(String PartitionKey, String RowKey, Date timeStamp,
                HashMap<String, EntityProperty> properties, String etag) {
            return properties.get("Email").getValueAsString();
        }
    };

    // Créez une boucle sur les résultats pour afficher la valeur Email.
    for (String projectedString : 
            tableClient.execute(projectionQuery, emailResolver)) {
        System.out.println(projectedString);
    }

Insertion ou remplacement d'une entité
--------------------------------------

Il arrive souvent de vouloir ajouter une entité à une table sans savoir si elle existe dans la table. Une opération d'insertion ou de remplacement permet d'envoyer une seule requête pour insérer l'entité si elle n'existe pas ou la remplacer si elle existe. À partir des exemples précédents, le code suivant insère ou remplace l'entité 'Walter Harp'. Après la création d'une entité, ce code appelle la méthode **TableOperation.insertOrReplace**. Ce code appelle ensuite la commande **execute** sur l'élément **CloudTableClient** avec la table et l'opération de table « insertion » ou « remplacement » comme paramètre. Pour mettre à jour seulement une partie de l'entité, il est possible d'utiliser la méthode **TableOperation.insertOrMerge** à la place. Notez que l'opération d'insertion ou de remplacement n'est pas prise en charge sur l'émulateur de stockage local : ce code s'exécute donc uniquement lors de l'utilisation d'un compte sur le service de Table. Pour plus d'informations sur les opérations d'insertion ou de remplacement et d'insertion ou de fusion, consultez ce [billet de blog](http://blogs.msdn.com/b/windowsazurestorage/archive/2011/09/15/windows-azure-tables-introducing-upsert-and-query-projection.aspx).

    // Extrayez le compte de stockage à partir de la chaîne de connexion
    CloudStorageAccount storageAccount =
        CloudStorageAccount.parse(storageConnectionString);
     
    // Créez le client de la table.
    CloudTableClient tableClient = storageAccount.createCloudTableClient();
     
    // Créez une entité de client.
    CustomerEntity customer5 = new CustomerEntity("Harp", "Walter");
    customer5.setEmail("Walter@contoso.com");
    customer5.setPhoneNumber("425-555-0106");

    // Créez une opération pour ajouter le nouveau client à la table people.
    TableOperation insertCustomer5 = TableOperation.insertOrReplace(customer5);

    // Soumettez l'opération au service de Table.
    tableClient.execute("people", insertCustomer5);

Suppression d'une entité
------------------------

Il est facile de supprimer une entité après l'avoir extraite. Une fois que l'entité est extraite, appelez **TableOperation.delete** avec l'entité à supprimer. Appelez ensuite la commande **execute** sur l'élément **CloudTableClient**. Le code suivant extrait et supprime une entité de client.

    // Extrayez le compte de stockage à partir de la chaîne de connexion
    CloudStorageAccount storageAccount =
        CloudStorageAccount.parse(storageConnectionString);
     
    // Créez le client de la table.
    CloudTableClient tableClient = storageAccount.createCloudTableClient();
     
    // Créez une opération pour extraire l'entité avec la clé de partition "Smith" et la clé de ligne "Jeff".
    TableOperation retrieveSmithJeff = TableOperation.retrieve("Smith", "Jeff", CustomerEntity.class);

    // Extrayez l'entité avec la clé de partition "Smith" et la clé de ligne "Jeff".
    CustomerEntity entitySmithJeff =
            tableClient.execute("people", retrieveSmithJeff).getResultAsType();

    // Créez une opération pour supprimer l'entité.
    TableOperation deleteSmithJeff = TableOperation.delete(entitySmithJeff);

    // Soumettez l'opération de suppression au service de Table.
    tableClient.execute("people", deleteSmithJeff);

Suppression d'une table
-----------------------

Pour finir, le code suivant supprime une table d'un compte de stockage. Une table supprimée ne pourra plus être recréée pendant un certain temps (moins de quarante secondes le plus souvent).

    // Extrayez le compte de stockage à partir de la chaîne de connexion
    CloudStorageAccount storageAccount =
        CloudStorageAccount.parse(storageConnectionString);
     
    // Créez le client de la table.
    CloudTableClient tableClient = storageAccount.createCloudTableClient();
     
    // Supprimez la table et toutes les données qu'elle contient, le cas échéant.
    tableClient.deleteTableIfExists("people");

Étapes suivantes
----------------

Maintenant que vous avez appris les bases du stockage de tables, suivez ces liens pour apprendre des tâches de stockage plus complexes.

-   Consultez la référence MSDN suivante : [Stockage et accessibilité des données dans Azure]
-   Consultez le [blog de l'équipe Azure Storage](http://blogs.msdn.com/b/windowsazurestorage/)

