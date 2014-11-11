<properties linkid="dev-nodejs-how-to-table-services" urlDisplayName="Table Service" pageTitle="How to use table storage (Node.js) | Microsoft Azure" metaKeywords="Azure table storage service, Azure table service Node.js, table storage Node.js" description="Learn how to use the table storage service in Azure. Code samples are written using the Node.js API." metaCanonical="" services="storage" documentationCenter="Node.js" title="How to Use the Table Service from Node.js" authors="larryfr" solutions="" manager="" editor="" />

<tags ms.service="storage" ms.workload="storage" ms.tgt_pltfrm="na" ms.devlang="nodejs" ms.topic="article" ms.date="01/01/1900" ms.author="larryfr" />

# Utilisation du service de Table à partir de Node.js

Ce guide décrit le déroulement de scénarios courants dans le cadre de l'utilisation du service de
Table Windows Azure. Les exemples sont écrits en utilisant
l'API Node.js. Les scénarios traités incluent la **création et la suppression d'une
table, l'insertion et l'interrogation d'entités dans une table**. Pour plus
d'informations sur les tables, consultez la section [Étapes suivantes][Étapes suivantes].

## Sommaire

-   [Présentation du service de Table][Présentation du service de Table]
-   [Concepts][Concepts]
-   [Création d'un compte de stockage Azure][Création d'un compte de stockage Azure]
-   [Création d'une application Node.js][Création d'une application Node.js]
-   [Configuration de votre application pour accéder au stockage][Configuration de votre application pour accéder au stockage]
-   [Configuration d'une connexion Azure Storage][Configuration d'une connexion Azure Storage]
-   [Création d'une table][Création d'une table]
-   [Ajout d'une entité à une table][Ajout d'une entité à une table]
-   [Mise à jour d'une entité][Mise à jour d'une entité]
-   [Utilisation des groupes d'entités][Utilisation des groupes d'entités]
-   [Extraction d'une entité][Extraction d'une entité]
-   [Interrogation d'un ensemble d'entités][Interrogation d'un ensemble d'entités]
-   [Suppression d'une entité][Suppression d'une entité]
-   [Suppression d'une table][Suppression d'une table]
-   [Utilisation des signatures d'accès partagé][Utilisation des signatures d'accès partagé]
-   [Étapes suivantes][Étapes suivantes]

[WACOM.INCLUDE [howto-table-storage](../includes/howto-table-storage.md)]

## <a name="create-account"></a>Création d'un compte de stockage Azure

[WACOM.INCLUDE [create-storage-account](../includes/create-storage-account.md)]

## <a name="create-app"> </a>Création d'une application Node.js

Créez une application Node.js vide. Pour obtenir les instructions permettant de créer une application Node.js, consultez les pages [Création et déploiement d'une application Node.js dans un site Web Azure][Création et déploiement d'une application Node.js dans un site Web Azure], [Service cloud Node.js][Service cloud Node.js] (avec Windows PowerShell) ou [Site Web avec WebMatrix][Site Web avec WebMatrix].

## <a name="configure-access"> </a>Configuration de votre application pour accéder au stockage

Pour utiliser le stockage Azure, vous avez besoin du Kit de développement logiciel (SDK) Azure Storage pour Node.js, qui inclut un ensemble de bibliothèques pratiques qui
communiquent avec les services REST de stockage.

### Utilisation de Node Package Manager (NPM) pour obtenir le package

1.  Utilisez une interface de ligne de commande telle que **PowerShell** (Windows), **Terminal** (Mac) ou **Bash** (Unix) pour accéder au dossier dans lequel vous avez créé votre exemple d'application.

2.  Tapez **npm install azure-storage** dans la fenêtre de commande, ce qui doit aboutir à la sortie suivante :

        azure-storage@0.1.0 node_modules\azure-storage
        ├── extend@1.2.1
        ├── xmlbuilder@0.4.3
        ├── mime@1.2.11
        ├── underscore@1.4.4
        ├── validator@3.1.0
        ├── node-uuid@1.4.1
        ├── xml2js@0.2.7 (sax@0.5.2)
        └── request@2.27.0 (json-stringify-safe@5.0.0, tunnel-agent@0.3.0, aws-sign@0.3.0, forever-agent@0.5.2, qs@0.6.6, oauth-sign@0.3.0, cookie-jar@0.3.0, hawk@1.0.0, form-data@0.1.3, http-signature@0.10.0)

3.  Vous pouvez exécuter manuellement la commande **ls** pour vérifier que le dossier
    **node\_modules** a été créé. Dans ce dossier,
    recherchez le package **azure-storage** qui contient les bibliothèques dont vous avez besoin
    pour accéder au stockage.

### Importation du package

À l'aide d'un éditeur de texte, comme le Bloc-notes, ajoutez la commande suivante au début du fichier
**server.js** de l'application dans laquelle vous souhaitez utiliser le stockage :

    var azure = require('azure-storage');

## <a name="setup-connection-string"> </a>Configuration d'une connexion Azure Storage

Le module Azure lit les variables d'environnement AZURE\_STORAGE\_ACCOUNT et AZURE\_STORAGE\_ACCESS\_KEY, ou AZURE\_STORAGE\_CONNECTION\_STRING pour obtenir les informations obligatoires pour se connecter à votre compte de stockage Azure. Si ces variables d'environnement ne sont pas définies, vous devez spécifier les informations de compte lors de l'appel de **TableService**.

Pour obtenir un exemple de configuration des variables d'environnement dans le portail de gestion pour un site web Azure, consultez la rubrique [Application web Node.js avec stockage][Application web Node.js avec stockage]

## <a name="create-table"> </a>Création d'une table

Le code suivant crée un objet **TableService** et l'utilise pour
créer une table. Ajoutez le code suivant vers le début du fichier **server.js** :

    var tableSvc = azure.createTableService();

L'appel de **createTableIfNotExists** crée une table avec le nom spécifié si elle
n'existe pas déjà. Dans l'exemple suivant, la table 'mytable' est créée, si elle n'existe pas déjà :

    tableSvc.createTableIfNotExists('mytable', function(error, result, response){
        if(!error){
            // Table exists or created
        }
    });

Le `result` a la valeur `true` si une table est créée, et la valeur `false` si la table existe déjà. `response` contient des informations sur la requête.

### Filtres

Des opérations facultatives de filtrage peuvent être appliquées aux opérations exécutées via **TableService**. Il peut s'agir d'opérations de journalisation, de relance automatique, etc. Les filtres sont des objets qui implémentent une méthode avec la signature :

        function handle (requestOptions, next)

Après le prétraitement des options de la requête, la méthode doit appeler « next » en passant un rappel avec la signature suivante :

        function (returnObject, finalCallback, next)

Dans ce rappel, et après le traitement de returnObject (la réponse de la requête au serveur), le rappel doit appeler la fonction next, si elle existe, pour continuer à traiter d'autres filtres ou simplement appeler finalCallback pour terminer l'utilisation du service.

Deux filtres qui implémentent la logique de relance sont inclus dans le Kit de développement logiciel (SDK) Azure pour Node.js : **ExponentialRetryPolicyFilter** et **LinearRetryPolicyFilter**. Le code suivant crée un objet **TableService** qui utilise le filtre **ExponentialRetryPolicyFilter** :

    var retryOperations = new azure.ExponentialRetryPolicyFilter();
    var tableSvc = azure.createTableService().withFilter(retryOperations);

## <a name="add-entity"> </a>Ajout d'une entité à une table

Pour ajouter une entité, commencez par créer un objet qui définit les
propriétés de l'entité. Toutes les entités doivent contenir une propriété **PartitionKey** et **RowKey**, qui sont des identificateurs uniques de l'entité.

-   **PartitionKey** : détermine la partition dans laquelle l'entité est stockée.

-   **RowKey** : identifie de façon unique l'entité dans la partition.

**PartitionKey** et **RowKey** doivent être des valeurs de chaîne. Pour plus d'informations, consultez la rubrique [Présentation du modèle de données du service de Table][Présentation du modèle de données du service de Table].

Voici un exemple de définition d'une entité. Notez que **dueDate** est définie comme un type de **Edm.DateTime**. L'indication du type est facultative et s'ils ne sont pas spécifiés, les types sont déduits.

    var task = { 
      PartitionKey: {'_':'hometasks'},
      RowKey: {'_': '1'},
      description: {'_':'take out the trash'},
      dueDate: {'_':new Date(2015, 6, 20), '$':'Edm.DateTime'}
    };

> [WACOM.NOTE] Il existe également un champ **Timestamp** pour chaque enregistrement, qui est défini par Azure lorsqu'une entité est ajoutée ou mise à jour.

Vous pouvez également utiliser **entityGenerator** pour créer des entités. L'exemple suivant crée la même entité de tâche en utilisant **entityGenerator**.

    var entGen = azure.TableUtilities.entityGenerator;
    var task = {
      PartitionKey: entGen.String('hometasks'),
      RowKey: entGen.String('1'),
      description: entGen.String('take out the trash'),
      dueDate: entGen.DateTime(new Date(Date.UTC(2015, 6, 20))),
    };

Pour ajouter une entité à votre table, transmettez l'objet entité dans
la méthode **insertEntity**.

    tableSvc.insertEntity('mytable',task, function (error, result, response) {
        if(!error){
            // Entity inserted
        }
    });

Si l'opération aboutit, `result` contient l'élément [ETag][ETag] de l'enregistrement inséré et `response` contient des informations sur l'opération.

> [WACOM.NOTE] Par défaut, **insertEntity** ne renvoie pas l'entité insérée comme information `response`. Si vous prévoyez d'exécuter d'autres opérations sur cette entité, ou si vous voulez mettre en cache les informations, il peut être utile de la faire renvoyer dans `result`. Pour ce faire, activez **echoContent** comme suit :
>
> `tableSvc.insertEntity('mytable', task, {echoContent: true}, function (error, result, response) {...}`

## <a name="update-entity"> </a>Mise à jour d'une entité

Plusieurs méthodes permettent de mettre à jour une entité existante :

-   **updateEntity** : met à jour une entité existante en la remplaçant.

-   **mergeEntity** : met à jour une entité existante en fusionnant les nouvelles valeurs des propriétés avec l'entité.

-   **insertOrReplaceEntity** : met à jour une entité existante en la remplaçant. En l'absence d'entité, une nouvelle entité est insérée.

-   **insertOrMergeEntity** : met à jour une entité existante en fusionnant les nouvelles valeurs des propriétés avec l'entité. En l'absence d'entité, une nouvelle entité est insérée.

L'exemple suivant illustre la mise à jour d'une entité avec **updateEntity** :

    tableSvc.updateEntity('mytable', updatedTask, function(error, result, response){
      if(!error) {
        // Entity updated
      }
    });

> [WACOM.NOTE] Par défaut, la mise à jour d'une entité ne vérifie pas si les données en cours de mise à jour ont déjà été modifiées par un autre processus. Pour activer la prise en charge de mises à jour simultanées :
>
> 1.  Obtenez l'ETag de l'objet mis à jour. Il est renvoyé dans la `response` d'une opération sur une entité et peut être extrait dans `response['.metadata'].etag`.
>
> 2.  Lors d'une opération de mise à jour sur une entité, ajoutez les informations ETag précédemment extraites dans la nouvelle entité. Par exemple :
>
>     `entity2['.metadata'].etag = currentEtag;`
>
> 3.  Effectuez l'opération de mise à jour. Si l'entité a été modifiée depuis que vous avez extrait la valeur ETag, par exemple avec une autre instance de votre application, une `error` est renvoyée, indiquant que la condition de mise à jour spécifiée dans la requête n'est pas remplie.
>
Avec **updateEntity** et **mergeEntity**, si l'entité mise à jour n'existe pas, l'opération échoue. Si vous voulez stocker une entité, qu'elle existe déjà ou non, utilisez plutôt **insertOrReplaceEntity** ou **insertOrMergeEntity**.

Le `result` des opérations de mise à jour réussies contient l'**Etag** de l'entité mise à jour.

## <a name="change-entities"> </a>Utilisation de groupes d'entités

Il est parfois intéressant de soumettre un lot d'opérations
simultanément pour assurer un traitement atomique par le serveur. Pour ce faire,
utilisez la classe **TableBatch** pour créer un traitement par lots, puis la méthode **executeBatch** de **TableService** pour exécuter les opérations du traitement par lots.

L'exemple suivant illustre la soumission par lot de deux entités :

    var task1 = { 
      PartitionKey: {'_':'hometasks'},
      RowKey: {'_': '1'},
      description: {'_':'Take out the trash'},
      dueDate: {'_':new Date(2015, 6, 20)}
    };
    var task2 = { 
      PartitionKey: {'_':'hometasks'},
      RowKey: {'_': '2'},
      description: {'_':'Wash the dishes'},
      dueDate: {'_':new Date(2015, 6, 20)}
    };

    var batch = new azure.TableBatch();

    batch.insertEntity(task1, {echoContent: true});
    batch.insertEntity(task2, {echoContent: true});

    tableSvc.executeBatch('mytable', batch, function (error, result, response) {
      if(!error) {
        // Batch completed
      }
    });

Pour les opérations de traitement par lots réussies, `result` contient les informations de chaque opération du lot.

### Utilisation des opérations de traitement par lots

Les opérations ajoutées à un traitement par lots peuvent être inspectées en affichant la propriété `operations`. Vous pouvez également utiliser les méthodes suivantes avec les opérations.

-   **clear** : permet de supprimer toutes les opérations d'un traitement par lots.

-   **getOperations** : permet d'obtenir une opération du traitement par lots.

-   **hasOperations** : permet de renvoyer true si le traitement par lots contient des opérations.

-   **removeOperations** : permet de supprimer une opération.

-   **size** : permet de renvoyer le nombre d'opérations du traitement par lots.

## <a name="query-for-entity"> </a>Extraction d'une entité

Si vous voulez renvoyer une entité donnée d'après **PartitionKey** et **RowKey**, utilisez la méthode **retrieveEntity**.

    tableSvc.retrieveEntity('mytable', 'hometasks', '1', function(error, result, response){
      if(!error){
        // result contains the entity
      }
    });

À la fin de cette opération, `result` contient l'entité.

## <a name="query-set-entities"> </a>Interrogation d'un ensemble d'entités

Pour interroger une table, utilisez l'objet **TableQuery** pour générer une
expression de requête en utilisant les clauses suivantes :

-   **select** : champs à renvoyer par la requête.

-   **where** : clause where.

    -   **and** : condition where `and`.

    -   **or** : condition where `or`.

-   **top** : nombre d'éléments à extraire.

L'exemple suivant crée une requête qui renvoie les 5 premiers éléments avec une PartitionKey « hometasks ».

    var query = new azure.TableQuery()
      .top(5)
      .where('PartitionKey eq ?', 'hometasks');

Comme **select** n'est pas utilisé, tous les champs sont renvoyés. Pour exécuter la requête dans une table, utilisez **queryEntities**. L'exemple suivant utilise cette requête pour renvoyer des entités de « mytable ».

    tableSvc.queryEntities('mytable',query, null, function(error, result, response) {
      if(!error) {
        // query was successful
      }
    });

En cas de réussite, `result.entries` contient un tableau d'entités qui correspondent à la requête. Si la requête n'a pas pu renvoyer toutes les entités, `result.continuationToken` peut servir de troisième paramètre de **queryEntities** pour extraire plus de résultats. Pour la requête initiale, le deuxième paramètre doit être *null*.

### Interrogation d'un sous-ensemble de propriétés d'entité

L'interrogation d'une table peut permettre d'extraire uniquement quelques champs d'une entité.
Cela permet de réduire la bande passante et d'optimiser les performances de requête, notamment pour les entités volumineuses. Utilisez la clause **select** et transmettez les noms des champs à renvoyer. Par exemple, la requête suivante renvoie uniquement les champs **description** et **dueDate**.

    var query = new azure.TableQuery()
      .select(['description', 'dueDate'])
      .top(5)
      .where('PartitionKey eq ?', 'hometasks');

## <a name="delete-entity"> </a>Suppression d'une entité

Vous pouvez supprimer une entité en utilisant ses clés de partition et de ligne. Dans cet
exemple, l'objet **task1** contient les valeurs **RowKey** et
**PartitionKey** de l'entité à supprimer. L'objet est
transmis à la méthode **deleteEntity**.

    var task = { 
      PartitionKey: {'_':'hometasks'},
      RowKey: {'_': '1'}
    };

    tableSvc.deleteEntity('mytable', task, function(error, response){
      if(!error) {
        // Entity deleted
      }
    });

> [WACOM.NOTE] Vous avez intérêt à utiliser les ETag pour supprimer des éléments afin de vous assurer que les éléments n'ont pas été modifiés par un autre processus. Consultez la rubrique [Mise à jour d'une entité][Mise à jour d'une entité] pour plus d'informations sur l'utilisation des ETags.

## <a name="delete-table"> </a>Suppression d'une table

Le code suivant permet de supprimer une table d'un compte de stockage.

    tableSvc.deleteTable('mytable', function(error, response){
        if(!error){
            // Table deleted
        }
    });

Si vous ne savez pas si la table existe, utilisez **deleteTableIfExists**.

## <a name="sas"></a> Utilisation des signatures d'accès partagé

Les signatures d'accès partagé sont un moyen sécurisé de fournir un accès précis aux tables sans fournir le nom ni les clés de votre compte de stockage. Elles servent souvent à fournir un accès limité à vos données, par exemple pour autoriser une application mobile à interroger des enregistrements.

Une application approuvée, comme un service cloud, génère une signature d'accès partagé à l'aide de **generateSharedAccessSignature** de **TableService**, et la fournit à une application non approuvée ou à moitié approuvée. Par exemple, une application mobile. La signature d'accès partagé est générée à l'aide d'une stratégie, qui décrit les dates de début et de fin de validité de la signature, et le niveau d'accès accordé au détenteur de la signature.

L'exemple suivant génère une nouvelle stratégie d'accès partagé qui autorise le détenteur de la signature d'accès partagé à interroger (« r ») la table et expire 100 minutes après son heure de création.

    var startDate = new Date();
    var expiryDate = new Date(startDate);
    expiryDate.setMinutes(startDate.getMinutes() + 100);
    startDate.setMinutes(startDate.getMinutes() - 100);
        
    var sharedAccessPolicy = {
      AccessPolicy: {
        Permissions: azure.TableUtilities.SharedAccessPermissions.QUERY,
        Start: startDate,
        Expiry: expiryDate
      },
    };

    var tableSAS = tableSvc.generateSharedAccessSignature('mytable', sharedAccessPolicy);
    var host = tableSvc.host;

Notez que les informations sur l'hôte doivent également être fournies, car elles sont obligatoires lorsque le détenteur de la signature d'accès partagé tente d'accéder à la table.

L'application cliente utilise les signatures d'accès partagé avec **TableServiceWithSAS** pour effectuer les opérations sur la table. L'exemple suivant se connecte à la table et exécute une requête.

    var sharedTableService = azure.createTableServiceWithSas(host, tableSAS);
    var query = azure.TableQuery()
      .where('PartitionKey eq ?', 'hometasks');
        
    sharedTableService.queryEntities(query, null, function(error, result, response) {
      if(!error) {
        // result contains the entities
      }
    });

Comme la signature d'accès partagé a été générée seulement avec un accès en requête, une erreur sera renvoyée en cas de tentative d'ajout, de mise à jour ou de suppression des entités.

### Listes de contrôle d'accès

Vous pouvez également utiliser une liste de contrôle d'accès (ACL) pour définir la stratégie d'accès pour une signature d'accès partagé. Cela est utile si vous voulez autoriser plusieurs clients à accéder à la table, mais fournir des stratégies d'accès différentes à chaque client.

Une liste de contrôle d'accès est implémentée à l'aide d'un tableau de stratégies d'accès, dans lequel un ID est associé à chaque stratégie. L'exemple suivant définit deux stratégies ; une pour « user1 » et une pour « user2 » :

    var sharedAccessPolicy = [
      {
        AccessPolicy: {
          Permissions: azure.TableUtilities.SharedAccessPermissions.QUERY,
          Start: startDate,
          Expiry: expiryDate
        },
        Id: 'user1'
      },
      {
        AccessPolicy: {
          Permissions: azure.TableUtilities.SharedAccessPermissions.ADD,
          Start: startDate,
          Expiry: expiryDate
        },
        Id: 'user2'
      }
    ];

L'exemple suivant obtient la liste de contrôle d'accès active pour la table **hometasks**, puis ajoute les nouvelles stratégies à l'aide de **setTableAcl**. Cette approche permet :

    tableSvc.getTableAcl('hometasks', function(error, result, response) {
      if(!error){
        //push the new policy into signedIdentifiers
        result.signedIdentifiers.push(sharedAccessPolicy);
        tableSvc.setTableAcl('hometasks', result, function(error, result, response){
          if(!error){
            // ACL set
          }
        });
      }
    });

Lorsque la liste de contrôle d'accès est définie, vous pouvez créer une signature d'accès partagé basée sur l'ID pour une stratégie. L'exemple suivant crée une signature d'accès partagé pour « user2 » :

    tableSAS = tableSvc.generateSharedAccessSignature('hometasks', { Id: 'user2' });

## <a name="next-steps"> </a>Étapes suivantes

Maintenant que vous connaissez les bases du stockage de tables, consultez les liens suivants
pour apprendre à effectuer des tâches de stockage plus complexes.

-   Consultez la référence MSDN suivante : [Stockage et accessibilité des données dans Azure][Stockage et accessibilité des données dans Azure].
-   [Consultez le blog de l'équipe Azure Storage][Consultez le blog de l'équipe Azure Storage].
-   Consultez le référentiel [Kit de développement logiciel (SDK) Azure Storage pour Node][Kit de développement logiciel (SDK) Azure Storage pour Node] sur GitHub.

  [Étapes suivantes]: #next-steps
  [Présentation du service de Table]: #what-is
  [Concepts]: #concepts
  [Création d'un compte de stockage Azure]: #create-account
  [Création d'une application Node.js]: #create-app
  [Configuration de votre application pour accéder au stockage]: #configure-access
  [Configuration d'une connexion Azure Storage]: #setup-connection-string
  [Création d'une table]: #create-table
  [Ajout d'une entité à une table]: #add-entity
  [Mise à jour d'une entité]: #update-entity
  [Utilisation des groupes d'entités]: #change-entities
  [Extraction d'une entité]: #query-for-entity
  [Interrogation d'un ensemble d'entités]: #query-set-entities
  [Suppression d'une entité]: #delete-entity
  [Suppression d'une table]: #delete-table
  [Utilisation des signatures d'accès partagé]: #sas
  [howto-table-storage]: ../includes/howto-table-storage.md
  [create-storage-account]: ../includes/create-storage-account.md
  [Création et déploiement d'une application Node.js dans un site Web Azure]: /fr-fr/documentation/articles/web-sites-nodejs-develop-deploy-mac/
  [Service cloud Node.js]: /fr-fr/documentation/articles/cloud-services-nodejs-develop-deploy-app/
  [Site Web avec WebMatrix]: /fr-fr/documentation/articles/web-sites-nodejs-use-webmatrix/
  [Application web Node.js avec stockage]: /fr-fr/documentation/articles/storage-nodejs-use-table-storage-web-site/
  [Présentation du modèle de données du service de Table]: http://msdn.microsoft.com/library/azure/dd179338.aspx
  [ETag]: http://en.wikipedia.org/wiki/HTTP_ETag
  [Stockage et accessibilité des données dans Azure]: http://msdn.microsoft.com/fr-fr/library/windowsazure/gg433040.aspx
  [Consultez le blog de l'équipe Azure Storage]: http://blogs.msdn.com/b/windowsazurestorage/
