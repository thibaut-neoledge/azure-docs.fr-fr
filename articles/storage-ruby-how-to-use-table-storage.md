<properties urlDisplayName="Table Service" pageTitle="Utilisation du stockage de tables (Ruby) | Microsoft&nbsp;Azure" metaKeywords="Azure table storage service, Azure table service Ruby, table storage Ruby" description="D&eacute;couvrez comment utiliser le service de stockage de tables dans Azure. Les exemples de code sont &eacute;crits &agrave; l'aide de l'API&nbsp;Ruby." metaCanonical="" services="storage" documentationCenter="Ruby" title="Utilisation du service de Table &agrave; partir de Ruby" authors="guayan" solutions="" manager="wpickett" editor="" />

<tags ms.service="storage" ms.workload="storage" ms.tgt_pltfrm="na" ms.devlang="ruby" ms.topic="article" ms.date="01/01/1900" ms.author="guayan" />

# Utilisation du service de Table à partir de Ruby

Ce guide décrit le déroulement de scénarios courants dans le cadre de l'utilisation du service de
Table Windows Azure. Les exemples sont écrits à l'aide de l'API
Ruby. Les scénarios traités incluent la **création et la suppression d'une
table, l'insertion et l'interrogation d'entités dans une table**. Pour plus
d'informations sur les tables, consultez la section [Étapes suivantes][Étapes suivantes].

## Sommaire

-   [Présentation du service de Table][Présentation du service de Table]
-   [Concepts][Concepts]
-   [Création d'un compte de stockage Azure][Création d'un compte de stockage Azure]
-   [Création d'une application Ruby][Création d'une application Ruby]
-   [Configuration de votre application pour accéder au stockage][Configuration de votre application pour accéder au stockage]
-   [Configuration d'une connexion Azure Storage][Configuration d'une connexion Azure Storage]
-   [Création d'une table][Création d'une table]
-   [Ajout d'une entité à une table][Ajout d'une entité à une table]
-   [Mise à jour d'une entité][Mise à jour d'une entité]
-   [Utilisation des groupes d'entités][Utilisation des groupes d'entités]
-   [Interrogation d'une entité][Interrogation d'une entité]
-   [Interrogation d'un ensemble d'entités][Interrogation d'un ensemble d'entités]
-   [Interrogation d'un sous-ensemble de propriétés d'entité][Interrogation d'un sous-ensemble de propriétés d'entité]
-   [Suppression d'une entité][Suppression d'une entité]
-   [Suppression d'une table][Suppression d'une table]
-   [Étapes suivantes][Étapes suivantes]

[WACOM.INCLUDE [howto-table-storage](../includes/howto-table-storage.md)]

## <span id="create-a-windows-azure-storage-account"></span></a>Création d'un compte Azure Storage

[WACOM.INCLUDE [create-storage-account](../includes/create-storage-account.md)]

## <span id="create-a-ruby-application"></span></a>Création d'une application Ruby

Créez une application Ruby. Pour obtenir des
instructions, consultez le guide [Création d'une application Ruby sur Azure][Création d'une application Ruby sur Azure].

## <span id="configure-your-application-to-access-storage"></span></a>Configuration de votre application pour accéder au stockage

Pour utiliser Azure Storage, vous devez télécharger et utiliser le package Azure Ruby,
qui inclut un ensemble de bibliothèques permettant de communiquer avec les services de stockage REST.

### Utilisation de RubyGems pour obtenir le package

1.  Ouvrez une interface de ligne de commande, telle que **PowerShell** (Windows), **Terminal** (Mac) ou **Bash** (Unix).

2.  Tapez **gem install azure** dans la fenêtre de commande pour installer gem et les dépendances.

### Importation du package

À l'aide de votre éditeur de texte, ajoutez la commande suivante au début du fichier Ruby où vous comptez utiliser le stockage :

    require "azure"

## <span id="setup-a-windows-azure-storage-connection"></span></a>Configuration d'une connexion Azure Storage

Le module Azure lit les variables d'environnement **AZURE\_STORAGE\_ACCOUNT** et **AZURE\_STORAGE\_ACCESS\_KEY**
 pour obtenir les informations nécessaires à la connexion à votre compte Azure Storage. Si ces variables d'environnement ne sont pas définies, vous devez spécifier les informations de compte avant d'utiliser **Azure::TableService** grâce au code suivant :

    Azure.config.storage_account_name = "<your azure storage account>"
    Azure.config.storage_access_key = "<your azure storage access key>"

Pour obtenir ces valeurs :

1.  Connectez-vous au [portail de gestion Azure][portail de gestion Azure].

2.  Accédez au compte de stockage que vous souhaitez utiliser.

3.  Cliquez sur **GÉRER LES CLÉS** au bas du volet de navigation.

4.  Dans la boîte de dialogue contextuelle, vous voyez le nom du compte de stockage et la clé d'accès primaire ou secondaire. Vous pouvez utiliser soit la clé d'accès primaire, soit la clé d'accès secondaire.

## <span id="how-to-create-a-table"></span></a>Création d'une table

L'objet **Azure::TableService** permet d'utiliser des tables et des entités. Pour créer une table, utilisez la méthode **create\_table()**. L'exemple suivant crée une table ou imprime l'erreur le cas échéant.

    azure_table_service = Azure::TableService.new
    begin
      azure_table_service.create_table("testtable")
    rescue
      puts $!
    end

## <span id="how-to-add-an-entity-to-a-table"></span></a>Ajout d'une entité à une table

Pour ajouter une entité, créez tout d'abord un objet de hachage qui définit les propriétés de votre entité. Notez que pour chaque entité, vous devez spécifier les clés **PartitionKey** et **RowKey**. Il s'agit d'identificateurs uniques de vos entités, dont les valeurs peuvent être interrogées bien plus rapidement que d'autres propriétés. Le service de stockage Azure utilise **PartitionKey** pour distribuer automatiquement les entités de la table sur plusieurs nœuds de stockage. Les entités partageant la même clé **PartitionKey** sont stockées sur le même nœud. **RowKey** est l'identifiant unique de l'entité dans sa partition.

    entity = { "content" => "test entity", 
      :PartitionKey => "test-partition-key", :RowKey => "1" }
    azure_table_service.insert_entity("testtable", entity)

## <span id="how-to-update-an-entity"></span></a> Mise à jour d'une entité

Plusieurs méthodes permettent de mettre à jour une entité existante :

-   **update\_entity() :** met à jour une entité existante en la remplaçant.
-   **merge\_entity() :** met à jour une entité existante en fusionnant les nouvelles valeurs des propriétés avec l'entité.
-   **insert\_or\_merge\_entity() :** met à jour une entité existante en la remplaçant. En l'absence d'entité, une nouvelle entité est insérée.
-   **insert\_or\_replace\_entity() :** met à jour une entité existante en fusionnant les nouvelles valeurs des propriétés avec l'entité. En l'absence d'entité, une nouvelle entité est insérée.

L'exemple suivant illustre la mise à jour d'une entité avec **update\_entity()** :

    entity = { "content" => "test entity with updated content", 
      :PartitionKey => "test-partition-key", :RowKey => "1" }
    azure_table_service.update_entity("testtable", entity)

Avec **update\_entity()** et **merge\_entity()**, si l'entité mise à jour n'existe pas, l'opération échoue. Si vous voulez stocker une entité, qu'elle existe déjà ou non, utilisez plutôt **insert\_or\_replace\_entity()** ou **insert\_or\_merge\_entity()**.

## <span id="how-to-work-with-groups-of-entities"></span></a> Utilisation des groupes d'entités

Il est parfois intéressant de soumettre un lot d'opérations simultanément pour assurer un traitement atomique par le serveur. Pour cela, vous devez d'abord créer un objet **Lot**, puis utiliser la méthode **execute\_batch()** sur **TableService**. L'exemple ci-dessous présente l'envoi de deux entités avec RowKey 2 et 3 dans un lot. Notez qu'il ne s'applique qu'aux entités possédant le même élément PartitionKey.

    azure_table_service = Azure::TableService.new
    batch = Azure::Storage::Table::Batch.new("testtable", 
      "test-partition-key") do
      insert "2", { "content" => "new content 2" }
      insert "3", { "content" => "new content 3" }
    end
    results = azure_table_service.execute_batch(batch)

## <span id="how-to-query-for-an-entity"></span></a> Interrogation d'une entité

Pour interroger une entité dans une table, utilisez la méthode **get\_entity()** en transmettant le nom de table, **PartitionKey** et **RowKey**.

    result = azure_table_service.get_entity("testtable", "test-partition-key", 
      "1")

## <span id="how-to-query-a-set-of-entities"></span></a> Interrogation d'un ensemble d'entités

Pour interroger un ensemble d'entités dans une table, créez un objet de hachage de requête et utilisez la méthode **query\_entities()**. L'exemple ci-dessous présente l'obtention de toutes les identités avec le même élément **PartitionKey** :

    query = { :filter => "PartitionKey eq 'test-partition-key'" }
    result, token = azure_table_service.query_entities("testtable", query)

**Notez** que si l'ensemble de résultats est trop grand pour être renvoyé par une seule requête, un jeton de liaison sera renvoyé. Vous pourrez l'utiliser pour récupérer les pages suivantes.

## <span id="how-to-query-a-subset-of-entity-properties"></span></a> Interrogation d'un sous-ensemble de propriétés d'entité

Vous pouvez utiliser une requête de table pour extraire uniquement quelques propriétés d'une entité. Cette technique, nommée « projection », réduit la consommation de bande passante et peut améliorer les performances des requêtes, notamment pour les entités volumineuses. Utilisez la clause select et transmettez le nom des propriétés à soumettre au client.

    query = { :filter => "PartitionKey eq 'test-partition-key'", 
      :select => ["content"] }
    result, token = azure_table_service.query_entities("testtable", query)

## <span id="how-to-delete-an-entity"></span></a> Suppression d'une entité

Pour supprimer une entité, utilisez la méthode **delete\_entity()**. Vous devez transmettre le nom de la table qui contient l'entité, ainsi que les éléments PartitionKey et RowKey de l'entité.

        azure_table_service.delete_entity("testtable", "test-partition-key", "1")

## <span id="how-to-delete-a-table"></span></a> Suppression d'une table

Pour supprimer une table, utilisez la méthode **delete\_table()** et transmettez le nom de la table que vous souhaitez supprimer.

        azure_table_service.delete_table("testtable")

## <span id="next-steps"></span></a>Étapes suivantes

Maintenant que vous avez appris les bases du stockage de tables, suivez ces liens pour apprendre des tâches de stockage plus complexes.

-   Consultez la référence MSDN suivante : [Stockage et accessibilité des données dans Azure][Stockage et accessibilité des données dans Azure]
-   Consultez le [blog de l'équipe Azure Storage][blog de l'équipe Azure Storage]
-   Accédez au référentiel du [Kit de développement logiciel (SDK) Azure pour Ruby][Kit de développement logiciel (SDK) Azure pour Ruby] sur GitHub.

  [Étapes suivantes]: #next-steps
  [Présentation du service de Table]: #what-is
  [Concepts]: #concepts
  [Création d'un compte de stockage Azure]: #create-a-windows-azure-storage-account
  [Création d'une application Ruby]: #create-a-ruby-application
  [Configuration de votre application pour accéder au stockage]: #configure-your-application-to-access-storage
  [Configuration d'une connexion Azure Storage]: #setup-a-windows-azure-storage-connection
  [Création d'une table]: #how-to-create-a-table
  [Ajout d'une entité à une table]: #how-to-add-an-entity-to-a-table
  [Mise à jour d'une entité]: #how-to-update-an-entity
  [Utilisation des groupes d'entités]: #how-to-work-with-groups-of-entities
  [Interrogation d'une entité]: #how-to-query-for-an-entity
  [Interrogation d'un ensemble d'entités]: #how-to-query-a-set-of-entities
  [Interrogation d'un sous-ensemble de propriétés d'entité]: #how-to-query-a-subset-of-entity-properties
  [Suppression d'une entité]: #how-to-delete-an-entity
  [Suppression d'une table]: #how-to-delete-a-table
  [Création d'une application Ruby sur Azure]: /fr-fr/develop/ruby/tutorials/web-app-with-linux-vm/
  [portail de gestion Azure]: https://manage.windowsazure.com/
  [Stockage et accessibilité des données dans Azure]: http://msdn.microsoft.com/fr-fr/library/windowsazure/gg433040.aspx
  [blog de l'équipe Azure Storage]: http://blogs.msdn.com/b/windowsazurestorage/
  [Kit de développement logiciel (SDK) Azure pour Ruby]: http://github.com/WindowsAzure/azure-sdk-for-ruby
