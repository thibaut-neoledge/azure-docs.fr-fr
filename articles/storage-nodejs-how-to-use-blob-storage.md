<properties linkid="dev-nodejs-how-to-blob-storage" urlDisplayName="Blob Service" pageTitle="How to use blob storage (Node.js) | Microsoft Azure" metaKeywords="Get started Azure blob, Azure unstructured data, Azure unstructured storage, Azure blob, Azure blob storage, Azure blob Node.js" description="Learn how to use the Azure blob service to upload, download, list, and delete blob content. Samples written in Node.js." metaCanonical="" services="storage" documentationCenter="Node.js" title="How to Use the Blob Service from Node.js" authors="" solutions="" manager="" editor="" />

Utilisation du service BLOB à partir de Node.js
===============================================

Ce guide décrit le déroulement de scénarios courants dans le cadre de l'utilisation du service Blob Azure. Les exemples sont écrits en utilisant l'API Node.js. Les scénarios traités incluent le **téléchargement (vers une cible)**, la **création de listes**, le **téléchargement (à partir d'une source)** et la **suppression** d'objets blob. Pour plus d'informations sur les objets blob, consultez la section [Étapes suivantes](#next-steps).

Sommaire
--------

-   [Présentation du service BLOB](#what-is)
-   [Concepts](#concepts)
-   [Création d'un compte Azure Storage](#create-account)
-   [Création d'une application Node.js](#create-app)
-   [Configuration de votre application pour accéder au stockage](#configure-access)
-   [Configuration d'une chaîne de connexion Azure Storage](#setup-connection-string)
-   [Création d'un conteneur](#create-container)
-   [Téléchargement d'un objet blob dans un conteneur](#upload-blob)
-   [Création d'une liste d'objets blob dans un conteneur](#list-blob)
-   [Téléchargement d'objets blob](#download-blobs)
-   [Suppression d'un objet blob](#delete-blobs)
-   [Étapes suivantes](#next-steps)

Présentation du service BLOB
----------------------------

Le service BLOB Azure permet de stocker de grandes quantités de données non structurées, accessibles depuis n'importe où dans le monde via HTTP ou HTTPS. Un seul objet blob peut avoir une taille de plusieurs centaines de gigaoctets et un seul compte de stockage peut contenir jusqu'à 100 To d'objets blob. Voici quelques utilisations courantes des objets blob :

-   Mise à disposition d'images ou de documents directement dans un navigateur
-   Stockage de fichiers pour un accès distribué
-   Diffusion en continu de vidéo et d'audio
-   Sauvegarde sécurisée et récupération d'urgence
-   Stockage des données pour l'analyse par un service local ou hébergé par Azure

Vous pouvez utiliser les objets blob pour exposer les données publiquement ou en privé pour le stockage d'applications internes.

Concepts
--------

Le service BLOB contient les composants suivants :

![Blob1](./media/storage-nodejs-how-to-use-blob-storage/blob1.jpg)

-   **Format d'URL :** les objets blob sont adressables à l'aide du format d'URL suivant :

        http://storageaccount.blob.core.windows.net/container/blob  

    L'URL suivante désigne l'un des objets blob du diagramme :

        http://sally.blob.core.windows.net/movies/MOV1.AVI

-   **Compte de stockage :** tous les accès à Azure Storage passent par un compte de stockage. Il s'agit du plus haut niveau d'espace de noms permettant d'accéder aux objets blob. Un compte peut contenir un nombre illimité de conteneurs, tant que sa taille totale ne dépasse pas 100 To.

-   **Conteneur :** un conteneur regroupe un ensemble d'objets blob. Tous les objets blob doivent figurer dans un conteneur. Un compte peut contenir un nombre illimité de conteneurs. Un conteneur peut stocker un nombre illimité d'objets blob.

-   **Blob :** fichier de n'importe quel type et de n'importe quelle taille. Il existe deux types d'objets blob : les objets blob de blocs et les objets blob de pages. La plupart des fichiers sont des objets blob de blocs. Un seul objet blob de blocs peut avoir une taille maximale de 200 Go. Ce didacticiel utilise des objets blob de blocs. Les objets blob de pages, autre type d'objets blob, peuvent avoir une taille de 1 To et sont plus efficaces lorsque des plages d'octets dans un fichier sont modifiées fréquemment.

Création d'un compte Azure Storage
----------------------------------

Pour utiliser les opérations de stockage, vous avez besoin d'un compte Azure Storage. Pour en créer un, procédez comme suit : (Vous pouvez également créer un compte de stockage [à l'aide de l'API REST](http://msdn.microsoft.com/en-us/library/windowsazure/hh264518.aspx).)

1.  Connectez-vous au [portail de gestion Azure](http://manage.windowsazure.com).

2.  En bas du volet de navigation, cliquez sur **+NEW**.

    ![+nouveau](./media/storage-nodejs-how-to-use-blob-storage/plus-new.png)

3.  Cliquez sur **Storage Account**, puis sur **Quick Create**.

    ![Boîte de dialogue Création rapide](./media/storage-nodejs-how-to-use-blob-storage/quick-storage.png)

4.  Dans URL, tapez un nom de sous-domaine à utiliser dans l'URI du compte de stockage. L'entrée peut être composée de 3 à 24 lettres minuscules et chiffres. Cette valeur devient le nom d'hôte contenu dans l'URI utilisé pour adresser les ressources d'objets blob, de files d'attente et de tables pour l'abonnement.

5.  Choisissez la région ou le groupe d'affinités dans lequel localiser le stockage. Si vous utilisez le stockage à partir de votre application Azure, sélectionnez la même région que celle du déploiement de votre application.

6.  Cliquez sur **Create Storage Account**.

Création d'une application Node.js
----------------------------------

Créez une application Node.js vide. Pour obtenir les instructions permettant de créer une application Node.js, consultez les pages [Création et déploiement d'une application Node.js dans un site Web Azure](/en-us/develop/nodejs/tutorials/create-a-website-(mac)/), [Service cloud Node.js]({localLink:2221} "Application Web Node.js") (avec Windows PowerShell) ou [Site Web avec WebMatrix](/en-us/develop/nodejs/tutorials/web-site-with-webmatrix/).

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

3.  Vous pouvez exécuter manuellement la commande **ls** pour vérifier que le dossier **node\_modules** a été créé. Dans ce dossier, recherchez le package **azure**, qui contient les bibliothèques nécessaires pour accéder au stockage.

### Importation du package

À l'aide d'un éditeur de texte, comme le Bloc-notes, ajoutez la commande suivante au début du fichier **server.js** de l'application dans laquelle vous souhaitez utiliser le stockage :

    var azure = require('azure');

Configuration d'une connexion Azure Storage
-------------------------------------------

Le module Azure lit les variables d'environnement AZURE\_STORAGE\_ACCOUNT et AZURE\_STORAGE\_ACCESS\_KEY pour obtenir les informations nécessaires à la connexion à votre compte Azure Storage. Si ces variables d'environnement ne sont pas définies, vous devez spécifier les informations de compte lors de l'appel de **createBlobService**.

Pour consulter un exemple de paramétrage de variables d'environnement dans un fichier de configuration pour un service cloud Azure, consultez la page [Service cloud Node.js avec stockage](/en-us/develop/nodejs/tutorials/web-app-with-storage/).

Pour consulter un exemple de paramétrage de variables d'environnement dans le portail de gestion d'un site Web Azure, consultez la page [Application Web Node.js avec stockage](/en-us/develop/nodejs/tutorials/web-site-with-storage/)

Création d'un conteneur
-----------------------

L'objet **BlobService** permet d'utiliser des conteneurs et des objets blob. Le code suivant crée un objet **BlobService**. Ajoutez le code suivant vers le début du fichier **server.js** :

    var blobService = azure.createBlobService();

Tous les objets blob résident dans un conteneur. L'appel de **createContainerIfNotExists** sur l'objet **BlobService** renvoie le conteneur spécifié s'il existe ou en crée un avec le nom spécifié s'il n'existe pas déjà. Le nouveau conteneur est privé par défaut et vous devez indiquer votre clé d'accès pour télécharger des objets blob depuis ce conteneur.

    blobService.createContainerIfNotExists(containerName, function(error){
        if (!error) {
            // Le conteneur existe et est privé
        }
    });

Si vous souhaitez que les fichiers du conteneur soient publics et accessibles sans clé d'accès, vous pouvez définir le niveau d'accès du conteneur sur **blob** ou **container**. Définir le niveau d'accès sur **blob** autorise un accès en lecture anonyme au contenu et aux métadonnées des objets blob du conteneur, mais pas aux métadonnées du conteneur telles que la liste de tous les objets blob d'un conteneur. Définir le niveau d'accès sur **container** autorise un accès en lecture anonyme au contenu et aux métadonnées des objets blob ainsi qu'aux métadonnées du conteneur. L'exemple suivant illustre le niveau d'accès défini sur **blob** :

    blobService.createContainerIfNotExists(containerName
        , {publicAccessLevel : 'blob'}
        , function(error){
            if (!error) {
                // Le conteneur existe et est public
            }
        });

Vous pouvez également modifier le niveau d'accès d'un conteneur en le spécifiant via **setContainerAcl**. Dans l'exemple suivant, le niveau d'accès du conteneur est modifié :

    blobService.setContainerAcl(containerName
        , 'container'
        , function(error){
            if (!error) {
                // Niveau d'accès du conteneur défini sur 'container'
            }
        });

### Filtres

Des opérations facultatives de filtrage peuvent être appliquées aux opérations exécutées via **BlobService**. Il peut s'agir d'opérations de journalisation, de relance automatique, etc. Les filtres sont des objets qui implémentent une méthode avec la signature :

     function handle (requestOptions, next)

Après le prétraitement des options de la requête, la méthode doit appeler « next » en passant un rappel avec la signature suivante :

     function (returnObject, finalCallback, next)

Dans ce rappel, et après le traitement de returnObject (la réponse de la requête au serveur), le rappel doit appeler la fonction next, si elle existe, pour continuer à traiter d'autres filtres ou simplement appeler finalCallback pour terminer l'utilisation du service.

Deux filtres qui implémentent la logique de relance sont inclus dans le Kit de développement logiciel (SDK) Azure pour Node.js : **ExponentialRetryPolicyFilter** et **LinearRetryPolicyFilter**. Le code suivant crée un objet **BlobService** qui utilise le filtre **ExponentialRetryPolicyFilter** :

    var retryOperations = new azure.ExponentialRetryPolicyFilter();
    var blobService = azure.createBlobService().withFilter(retryOperations);

Téléchargement d'un objet blob dans un conteneur
------------------------------------------------

Pour télécharger des données dans un objet blob, utilisez les méthodes **createBlockBlobFromFile**, **createBlockBlobFromStream** ou **createBlockBlobFromText**. **createBlockBlobFromFile** télécharge le contenu d'un fichier, alors que **createBlockBlobFromStream** télécharge le contenu d'un flux. **createBlockBlobFromText** télécharge la valeur de texte spécifiée.

Dans l'exemple suivant, le contenu du fichier **test1.txt** est téléchargé dans l'objet blob 'test1'.

    blobService.createBlockBlobFromFile(containerName
        , 'test1'
        , 'test1.txt'
        , function(error){
            if (!error) {
                // Le fichier a été téléchargé
            }
        });

Création d'une liste d'objets blob dans un conteneur
----------------------------------------------------

Pour créer la liste des objets blob d'un conteneur, utilisez la méthode **listBlobs** avec une boucle **for** pour afficher le nom de chaque objet blob du conteneur. Le code suivant sort le **nom** de chaque objet blob d'un conteneur sur la console.

    blobService.listBlobs(containerName, function(error, blobs){
        if (!error) {
            for(var index in blobs){
                console.log(blobs[index].name);
            }
        }
    });

Téléchargement d'objets blob
----------------------------

Pour télécharger les données d'un objet blob, utilisez **getBlobToFile**, **getBlobToStream** ou **getBlobToText**. L'exemple suivant illustre l'utilisation de **getBlobToStream** pour télécharger le contenu de l'objet blob **test1** et le stocker sur le fichier **output.txt** via un flux :

    var fs=require('fs');
    blobService.getBlobToStream(containerName
        , 'test1'
        , fs.createWriteStream('output.txt')
        , function(error){
            if (!error) {
                // Écrivez l'objet blob sur le flux
            }
        });

Suppression d'un objet blob
---------------------------

Pour supprimer un objet blob, appelez **deleteBlob**. Dans l'exemple suivant, l'objet blob 'blob1' est supprimé.

    blobService.deleteBlob(containerName
        , 'blob1'
        , function(error){
            if (!error) {
                // L'objet blob a été supprimé
            }
        });

Étapes suivantes
----------------

Maintenant que vous avez appris les bases du stockage d'objets blob, suivez ces liens pour apprendre des tâches de stockage plus complexes.

-   Consultez la référence MSDN suivante : [Stockage et accessibilité des données dans Azure](http://msdn.microsoft.com/en-us/library/windowsazure/gg433040.aspx).
-   Consultez le [Blog de l'équipe Azure Storage](http://blogs.msdn.com/b/windowsazurestorage/).
-   Accédez au référentiel du [Kit de développement logiciel (SDK) Azure pour Node](https://github.com/WindowsAzure/azure-sdk-for-node) sur GitHub.

