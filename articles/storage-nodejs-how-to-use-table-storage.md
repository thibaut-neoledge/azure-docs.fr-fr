<properties linkid="dev-nodejs-how-to-table-services" urlDisplayName="Table Service" pageTitle="How to use table storage (Node.js) | Microsoft Azure" metaKeywords="Azure table storage service, Azure table service Node.js, table storage Node.js" description="Learn how to use the table storage service in Azure. Code samples are written using the Node.js API." metaCanonical="" services="storage" documentationCenter="Node.js" title="How to Use the Table Service from Node.js" authors="" solutions="" manager="" editor="" />

Utilisation du service de Table à partir de Node.js
===================================================

Ce guide décrit le déroulement de scénarios courants dans le cadre de l'utilisation du service de Table Azure. Les exemples sont écrits en utilisant l'API Node.js. Les scénarios traités incluent la **création et la suppression d'une table, l'insertion et l'interrogation d'entités dans une table**. Pour plus d'informations sur les tables, consultez la section [Étapes suivantes](#next-steps).

Sommaire
--------

-   [Présentation du service de Table](#what-is)
-   [Concepts](#concepts)
-   [Création d'un compte Azure Storage](#create-account)
-   [Création d'une application Node.js](#create-app)
-   [Configuration de votre application pour accéder au stockage](#configure-access)
-   [Configuration d'une connexion Azure Storage](#setup-connection-string)
-   [Création d'une table](#create-table)
-   [Ajout d'une entité à une table](#add-entity)
-   [Mise à jour d'une entité](#update-entity)
-   [Utilisation des groupes d'entités](#change-entities)
-   [Interrogation d'une entité](#query-for-entity)
-   [Interrogation d'un ensemble d'entités](#query-set-entities)
-   [Interrogation d'un sous-ensemble de propriétés d'entité](#query-entity-properties)
-   [Suppression d'une entité](#delete-entity)
-   [Suppression d'une table](#delete-table)
-   [Étapes suivantes](#next-steps)

Présentation du service de Table
--------------------------------

Le service de Table Azure stocke de grandes quantités de données structurées. Il accepte les appels authentifiés provenant de l'intérieur et de l'extérieur du cloud Azure. Les tables Azure sont idéales pour le stockage des données structurées non relationnelles. Voici quelques utilisations courantes des services de Table :

-   Stockage de grands volumes de données (plusieurs téraoctets) qui sont automatiquement modulés pour répondre aux demandes de débit
-   Stockage des jeux de données ne nécessitant pas de jonctions complexes, de clés étrangères ni de procédures stockées, et pouvant être dénormalisés pour un accès rapide
-   Interrogation rapide des données, par exemple des profils utilisateur, à l'aide d'un index cluster

Vous pouvez utiliser le service de Table pour stocker et interroger de grands ensembles de données non relationnelles structurées. Vos tables évoluent en même temps que le volume.

Concepts
--------

Le service de table contient les composants suivants :

![Table1](./media/storage-nodejs-how-to-use-table-storage/table1.png)

-   **Format d'URL :** le code traite les tables dans un compte à l'aide de ce format d'adresse :

        http://storageaccount.table.core.windows.net/table  

    Vous pouvez traiter les tables Azure directement à l'aide de cette adresse avec le protocole OData. Pour plus d'informations, consultez [OData.org](http://www.odata.org/)

-   **Compte de stockage :** tous les accès à Azure Storage passent par un compte de stockage. Dans un compte de stockage, la taille totale du contenu des objets blob, des tables et des files d'attente ne doit pas dépasser 100 To.

-   **Table** : une table est une collection illimitée d'entités. Les tables n'appliquent pas de schéma sur les entités, ce qui signifie qu'une seule table peut contenir des entités ayant différents ensembles de propriétés. Un compte peut contenir plusieurs tables.

-   **Entité** : une entité est un ensemble de propriétés similaire à une ligne de base de données. Une entité peut avoir une taille maximale de 1 Mo.

-   **Propriétés** : une propriété est une paire nom-valeur. Chaque entité peut inclure jusqu'à 252 propriétés pour stocker des données. Chaque entité a également 3 propriétés système qui spécifient une clé de partition, une clé de ligne et un horodatage. Les entités ayant la même clé de partition peuvent être interrogées plus rapidement et insérées ou mises à jour dans des opérations atomiques. La clé de ligne d'une entité est son identificateur unique à l'intérieur d'une partition.

Création d'un compte Azure Storage
----------------------------------

Pour utiliser les opérations de stockage, vous avez besoin d'un compte Azure Storage. Pour en créer un, procédez comme suit : (Vous pouvez également créer un compte de stockage [à l'aide de l'API REST](http://msdn.microsoft.com/en-us/library/windowsazure/hh264518.aspx).)

1.  Connectez-vous au [portail de gestion Azure](http://manage.windowsazure.com).

2.  En bas du volet de navigation, cliquez sur **+NEW**.

    ![+nouveau](./media/storage-nodejs-how-to-use-table-storage/plus-new.png)

3.  Cliquez sur **Storage Account**, puis sur **Quick Create**.

    ![Boîte de dialogue Création rapide](./media/storage-nodejs-how-to-use-table-storage/quick-storage.png)

4.  Dans URL, tapez un nom de sous-domaine à utiliser dans l'URI du compte de stockage. L'entrée peut être composée de 3 à 24 lettres minuscules et chiffres. Cette valeur devient le nom d'hôte contenu dans l'URI utilisé pour adresser les ressources d'objets blob, de files d'attente et de tables pour l'abonnement.

5.  Choisissez la région ou le groupe d'affinités dans lequel localiser le stockage. Si vous utilisez le stockage à partir de votre application Azure, sélectionnez la même région que celle du déploiement de votre application.

6.  Cliquez sur **Create Storage Account**.

Création d'une application Node.js
----------------------------------

Créez une application Node.js vide. Pour obtenir les instructions permettant de créer une application Node.js, consultez les pages [Création et déploiement d'une application Node.js dans un site Web Azure](/en-us/develop/nodejs/tutorials/create-a-website-(mac)/), [Service cloud Node.js]({localLink:2221} "Application Web avec Express") (avec Windows PowerShell) ou [Site Web avec WebMatrix](/en-us/develop/nodejs/tutorials/web-site-with-webmatrix/).

Configuration de votre application pour accéder au stockage
-----------------------------------------------------------

Pour utiliser Azure Storage, vous devez télécharger et utiliser le package Azure Node.js, qui inclut des bibliothèques permettant de communiquer avec les services de stockage REST.

### Utilisation de Node Package Manager (NPM) pour obtenir le package

1.  Utilisez une interface de ligne de commande telle que **PowerShell** (Windows), **Terminal** (Mac) ou **Bash** (Unix) pour accéder au dossier dans lequel vous avez créé votre exemple d'application.

2.  Tapez **npm install azure** dans la fenêtre de commande, ce qui génère la sortie suivante :

        azure@0.7.5 node_modules\azure
        ├── dateformat@1.0.2-1.2.3
        ├── xmlbuilder@0.4.2
        ├── node-uuid@1.2.0
        ├── mime@1.2.9
        ├── underscore@1.4.4
        ├── validator@1.1.1
        ├── tunnel@0.0.2
        ├── wns@0.5.3
        ├── xml2js@0.2.7 (sax@0.5.2)
        └── request@2.21.0 (json-stringify-safe@4.0.0, forever-agent@0.5.0, aws-sign@0.3.0, tunnel-agent@0.3.0, oauth-sign@0.3.0, qs@0.6.5, cookie-jar@0.3.0, node-uuid@1.4.0, http-signature@0.9.11, form-data@0.0.8, hawk@0.13.1)

3.  Vous pouvez exécuter manuellement la commande **ls** pour vérifier que le dossier **node\_modules** a été créé. Dans ce dossier, vous trouverez le package **azure**, qui contient les bibliothèques nécessaires pour accéder au stockage.

### Importation du package

À l'aide d'un éditeur de texte, comme le Bloc-notes, ajoutez la commande suivante au début du fichier **server.js** de l'application dans laquelle vous souhaitez utiliser le stockage :

    var azure = require('azure');

Configuration d'une connexion Azure Storage
-------------------------------------------

Le module Azure lit les variables d'environnement AZURE\_STORAGE\_ACCOUNT et AZURE\_STORAGE\_ACCESS\_KEY pour obtenir les informations nécessaires à la connexion à votre compte Azure Storage. Si ces variables d'environnement ne sont pas définies, vous devez spécifier les informations de compte lors de l'appel de **TableService**.

Pour consulter un exemple de paramétrage de variables d'environnement dans un fichier de configuration pour un service cloud Azure, consultez la page [Service cloud Node.js avec stockage](/en-us/develop/nodejs/tutorials/web-app-with-storage/).

Pour consulter un exemple de paramétrage de variables d'environnement dans le portail de gestion d'un site Web Azure, consultez la page [Application Web Node.js avec stockage](/en-us/develop/nodejs/tutorials/web-site-with-storage/)

Création d'une table
--------------------

Le code suivant crée un objet **TableService** et l'utilise pour créer une table. Ajoutez le code suivant vers le début du fichier **server.js** :

    var tableService = azure.createTableService();

L'appel de **createTableIfNotExists** renvoie la table spécifiée si elle existe ou crée une table avec le nom spécifié si elle n'existe pas déjà. Dans l'exemple suivant, la table 'mytable' est créée, si elle n'existe pas déjà :

    tableService.createTableIfNotExists('mytable', function(error){
        if (!error) {
            // La table existe ou est créée
        }
    });

### Filtres

Des opérations facultatives de filtrage peuvent être appliquées aux opérations exécutées via **TableService**. Il peut s'agir d'opérations de journalisation, de relance automatique, etc. Les filtres sont des objets qui implémentent une méthode avec la signature :

     function handle (requestOptions, next)

Après le prétraitement des options de la requête, la méthode doit appeler « next » en passant un rappel avec la signature suivante :

     function (returnObject, finalCallback, next)

Dans ce rappel, et après le traitement de returnObject (la réponse de la requête au serveur), le rappel doit appeler la fonction next, si elle existe, pour continuer à traiter d'autres filtres ou simplement appeler finalCallback pour terminer l'utilisation du service.

Deux filtres qui implémentent la logique de relance sont inclus dans le Kit de développement logiciel (SDK) Azure pour Node.js : **ExponentialRetryPolicyFilter** et **LinearRetryPolicyFilter**. Le code suivant crée un objet **TableService** qui utilise le filtre **ExponentialRetryPolicyFilter** :

    var retryOperations = new azure.ExponentialRetryPolicyFilter();
    var tableService = azure.createTableService().withFilter(retryOperations);

Ajout d'une entité à une table
------------------------------

Pour ajouter une entité, commencez par créer un objet définissant les propriétés de vos entités et leurs types de données. Notez que pour chaque entité, vous devez spécifier les clés **PartitionKey** et **RowKey**. Il s'agit d'identificateurs uniques de vos entités, dont les valeurs peuvent être interrogées bien plus rapidement que d'autres propriétés. Le système utilise **PartitionKey** pour distribuer automatiquement les entités de la table sur plusieurs nœuds de stockage. Les entités partageant la même clé **PartitionKey** sont stockées sur le même nœud. **RowKey** est l'identifiant unique de l'entité dans sa partition. Pour ajouter une entité à votre table, transmettez l'objet d'entité à la méthode **insertEntity**.

    var task = {
        PartitionKey : 'hometasks'
        , RowKey : '1'
        , Description : 'Take out the trash'
        , DueDate: new Date(2012, 6, 20)
    };
    tableService.insertEntity('mytable', task, function(error){
        if (!error) {
            // Entité insérée
        }
    });

Mise à jour d'une entité
------------------------

Plusieurs méthodes permettent de mettre à jour une entité existante :

-   **updateEntity** : met à jour une entité existante en la remplaçant.

-   **mergeEntity** : met à jour une entité existante en fusionnant les nouvelles valeurs des propriétés avec l'entité.

-   **insertOrReplaceEntity** : met à jour une entité existante en la remplaçant. En l'absence d'entité, une nouvelle entité est insérée.

-   **insertOrMergeEntity** : met à jour une entité existante en fusionnant les nouvelles valeurs des propriétés avec l'entité. En l'absence d'entité, une nouvelle entité est insérée.

L'exemple suivant illustre la mise à jour d'une entité avec **updateEntity** :

    var task = {
        PartitionKey : 'hometasks'
        , RowKey : '1'
        , Description : 'Wash Dishes'
    }
    tableService.updateEntity('mytable', task, function(error){
        if (!error) {
            // L'entité a été mise à jour
        }
    });

Avec **updateEntity** et **mergeEntity**, si l'entité mise à jour n'existe pas, l'opération échoue. Si vous voulez stocker une entité, qu'elle existe déjà ou non, utilisez plutôt **insertOrReplaceEntity** ou **insertOrMergeEntity**.

Utilisation de groupes d'entités
--------------------------------

Il est parfois intéressant de soumettre un lot d'opérations simultanément pour assurer un traitement atomique par le serveur. Pour cela, vous devez utiliser la méthode **beginBatch** sur **TableService**, puis appeler les habituelles séries d'opérations. La différence est que les fonctions de rappel de ces opérateurs indiquent qu'il s'agit d'une opération par lot, non soumise au serveur. Lorsque vous devez soumettre le lot, appelez **commitBatch**. Le rappel fourni pour cette méthode indique l'aboutissement correct de l'opération pour tout le lot. L'exemple suivant illustre la soumission par lot de deux entités :

    var tasks=[
        {
            PartitionKey : 'hometasks'
            , RowKey : '1'
            , Description : 'Take out the trash.'
            , DueDate: new Date(2012, 6, 20)
        }
        , {
            PartitionKey : 'hometasks'
            , RowKey : '2'
            , Description : 'Wash the dishes.'
            , DueDate: new Date(2012, 6, 20)
        }
    ]
    tableService.beginBatch();
    var async=require('async');

    async.forEach(tasks
        , function taskIterator(task, callback){
            tableService.insertEntity('mytable', task, function(error){
                if (!error) {
                    // Entité insérée
                    callback(null);
                } else {
                    callback(error);
                }
            });
        }
        , function(error){
            if (!error) {
                // Toutes les insertions sont terminées
                tableService.commitBatch(function(error){
                    if (!error) {
                        // Le lot est correctement validé
                    }
                });
            }
        });

**Remarque**

Cet exemple utilise le module 'async' pour que toutes les entités soient correctement soumises avant d'appeler **commitBatch**.

Interrogation d'une entité
--------------------------

Pour interroger une entité dans une table, utilisez la méthode **queryEntity** en transmettant les clés **PartitionKey** et **RowKey**.

    tableService.queryEntity('mytable'
        , 'hometasks'
        , '1'
        , function(error, entity){
            if (!error) {
                // L'entité contient l'entité renvoyée
            }
        });

Interrogation d'un ensemble d'entités
-------------------------------------

Pour interroger une table, utilisez l'objet **TableQuery** pour créer une expression de requête en utilisant des clauses telles que **select**, **from**, **where** (y compris des clauses pratiques telles que **wherePartitionKey**, **whereRowKey**, **whereNextPartitionKey** et **whereNextRowKey**), **and**, **or** et **top**. Transmettez ensuite l'expression de requête à la méthode **queryEntities**. Vous pouvez utiliser les résultats dans une boucle **for** dans le rappel.

Cet exemple recherche toutes les tâches dans Seattle avec la clé **PartitionKey**.

    var query = azure.TableQuery
        .select()
        .from('mytable')
        .where('PartitionKey eq 
        ', 'hometasks');
    tableService.queryEntities(query, function(error, entities){
        if (!error) {
            //Les entités contiennent un tableau d'entités
        }
    });

Interrogation d'un sous-ensemble de propriétés d'entité
-------------------------------------------------------

Vous pouvez utiliser une requête de table pour récupérer uniquement quelques propriétés d'une entité. Cette technique, nommée « projection », réduit la consommation de bande passante et peut améliorer les performances des requêtes, notamment pour les entités volumineuses. Utilisez la clause **select** et transmettez le nom des propriétés à soumettre au client.

Dans le code suivant, la requête renvoie uniquement les **Descriptions** des entités de la table. Dans la sortie du programme, **DueDate** est marquée comme **undefined**, car non envoyée par le serveur.

**Remarque**

L'extrait suivant fonctionne uniquement dans le service cloud de stockage, le mot clé **select** n'étant pas pris en charge par l'émulateur de stockage.

    var query = azure.TableQuery
        .select('Description')
        .from('mytable')
        .where('PartitionKey eq 
        ', 'hometasks');
    tableService.queryEntities(query, function(error, entities){
        if (!error) {
            //Les entités contiennent un tableau d'entités
        }
    });

Suppression d'une entité
------------------------

Vous pouvez supprimer une entité en utilisant ses clés de partition et de ligne. Dans cet exemple, l'objet **task1** contient les valeurs **RowKey** et **PartitionKey** de l'entité à supprimer. L'objet est transmis à la méthode **deleteEntity**.

    tableService.deleteEntity('mytable'
        , {
            PartitionKey : 'hometasks'
            , RowKey : '1'
        }
        , function(error){
            if (!error) {
                // Entité supprimée
            }
        });

Suppression d'une table
-----------------------

Le code suivant supprime une table d'un compte de stockage.

    tableService.deleteTable('mytable', function(error){
        if (!error) {
            // Table supprimée
        }
    });

Étapes suivantes
----------------

Maintenant que vous avez appris les bases du stockage de tables, suivez ces liens pour apprendre des tâches de stockage plus complexes.

-   Consultez la référence MSDN suivante : [Stockage et accessibilité des données dans Azure](http://msdn.microsoft.com/en-us/library/windowsazure/gg433040.aspx).
-   [Consultez le blog de l'équipe Azure Storage](http://blogs.msdn.com/b/windowsazurestorage/).
-   Accédez au référentiel du [Kit de développement logiciel (SDK) Azure pour Node](https://github.com/WindowsAzure/azure-sdk-for-node) sur GitHub.

