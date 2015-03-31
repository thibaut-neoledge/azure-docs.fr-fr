<properties 
	pageTitle="Gestion de l'accès aux ressources d'Azure Storage" 
	description="Découvrez les différentes manières de gérer l'accès aux ressources d'Azure Storage." 
	services="storage" 
	documentationCenter="" 
	authors="micurd" 
	manager="jahogg" 
	editor=""/>

<tags 
	ms.service="storage" 
	ms.workload="storage" 
	ms.tgt_pltfrm="na" 
	ms.devlang="na" 
	ms.topic="article" 
	ms.date="02/20/2015" 
	ms.author="micurd"/>

#Gestion de l'accès aux ressources d'Azure Storage
Par défaut, seul le propriétaire du compte de stockage peut accéder aux objets blob, aux tables et aux files d'attentes dans ce compte. Si votre service ou application doit mettre ces ressources à disposition d'autres clients sans partager votre clé d'accès, vous disposez des options suivantes pour autoriser l'accès :

- Vous pouvez définir les autorisations d'un conteneur afin de permettre un accès en lecture anonyme au conteneur et à ses objets blob. Cela n'est pas autorisé pour les tables ou les files d'attente.

- Vous pouvez exposer une ressource via une signature d'accès partagé, ce qui vous permet de déléguer un accès limité à une ressource de conteneur, à un objet blob, à une table ou à une file d'attente en spécifiant l'intervalle pendant lequel les ressources seront disponibles et les autorisations dont bénéficiera le client.

- Vous pouvez utiliser une stratégie d'accès stockée pour gérer les signatures d'accès partagé d'un conteneur ou ses objets blob, d'une file d'attente ou d'une table. La stratégie d'accès stockée vous donne un contrôle supplémentaire sur vos signatures d'accès partagé et permet également de les révoquer simplement.

## Limitation de l'accès aux conteneurs et aux objets blob

Par défaut, un conteneur et tous les objets blob qu'il contient sont accessibles uniquement par le propriétaire du compte de stockage. Si vous souhaitez fournir aux utilisateurs anonymes des autorisations de lecture sur un conteneur et ses objets blob, vous pouvez configurer les autorisations du conteneur afin d'autoriser l'accès public. Les utilisateurs anonymes peuvent lire les objets blob d'un conteneur accessible publiquement sans avoir à authentifier la demande.

Les conteneurs fournissent les options suivantes pour gérer leur accès :

- Accès en lecture public complet : les données de conteneur et d'objet blob peuvent être lues via une demande anonyme. 
 Les clients peuvent énumérer les objets blob à l'intérieur du conteneur via une demande anonyme, mais ne peuvent pas énumérer les conteneurs dans le compte de stockage.

- Accès en lecture public pour les objets blob uniquement : les données d'objets blob à l'intérieur de ce conteneur peuvent être lues via une demande anonyme, mais les données du conteneur ne sont pas disponibles. Les clients ne peuvent pas énumérer les objets blob à l'intérieur du conteneur via une demande anonyme.

- Aucun accès en lecture public : les données de conteneur et d'objet blob ne peuvent être lues que par le propriétaire du compte.

>[AZURE.NOTE]Si votre service requiert un contrôle plus précis sur les ressources d'objet blob ou si vous souhaitez accorder des autorisations pour des opérations autres que les opérations de lecture, vous pouvez utiliser une signature d'accès partagé pour que la ressource soit accessible aux utilisateurs. 

###Fonctionnalités accessibles aux utilisateurs anonymes
Le tableau suivant indique les opérations pouvant être appelées par les utilisateurs anonymes quand la liste de contrôle d'accès (ACL, Access Control List) est définie pour autoriser l'accès public.

| Opération REST                                         | Méthode .NET                                      | Autorisation avec accès en lecture public complet | Autorisation avec accès en lecture public pour les objets blob uniquement |
|--------------------------------------------------------|--------------------------------------------------|-----------------------------------------|---------------------------------------------------|
| List Containers                                        | ListContainers                                   | Propriétaire uniquement                              | Propriétaire uniquement                                        |
| Create Container                                       | Create                                           | Propriétaire uniquement                              | Propriétaire uniquement                                        |
| Get Container Properties                               | Properties                                       | Tout                                     | Propriétaire uniquement                                        |
| Get Container Metadata                                 | Metadata                                         | Tous                                     | Propriétaire uniquement                                        |
| Set Container Metadata                                 | Metadata                                         | Propriétaire uniquement                              | Propriétaire uniquement                                        |
| Get Container ACL                                      | BlobContainerPermissions                         | Propriétaire uniquement                              | Propriétaire uniquement                                        |
| Set Container ACL                                      | BlobContainerPermissions                         | Propriétaire uniquement                              | Propriétaire uniquement                                        |
| Delete Container                                       | Delete                                           | Propriétaire uniquement                              | Propriétaire uniquement                                        |
| List Blobs                                             | ListBlobs                                        | Tous                                     | Propriétaire uniquement                                        |
| Put Blob                                               | Méthode Create de CloudPageBlob ou CloudBlockBlob | Propriétaire uniquement                              | Propriétaire uniquement                                        |
| Get Blob                                               | DownloadByteArray et autres                     | Tous                                     | Tous                                               |
| Get Blob Properties                                    | Properties                                       | Tous                                     | Tous                                               |
| Set Blob Properties                                    | SetProperties                                    | Propriétaire uniquement                              | Propriétaire uniquement                                        |
| Get Blob Metadata                                      | Metadata                                         | Tous                                     | Tous                                               |
| Set Blob Metadata                                      | Metadata                                         | Propriétaire uniquement                              | Propriétaire uniquement                                        |
| Put Block                                              | PutBlock                                         | Propriétaire uniquement                              | Propriétaire uniquement                                        |
| Get Block List (blocs validés uniquement)                 | DownloadBlockList                                | Tous                                     | Tous                                               |
| Get Block List (blocs non validés uniquement ou tous les blocs) | DownloadBlockList                                | Propriétaire uniquement                              | Propriétaire uniquement                                        |
| Put Block List                                         | PutBlockList                                     | Propriétaire uniquement                              | Propriétaire uniquement                                        |
| Delete Blob                                            | Delete                                           | Propriétaire uniquement                              | Propriétaire uniquement                                        |
| Copy Blob                                              | CopyFromBlob                                     | Propriétaire uniquement                              | Propriétaire uniquement                                        |
| Snapshot Blob                                          | CreateSnapshot                                   | Propriétaire uniquement                              | Propriétaire uniquement                                        |
| Lease Blob                                             | Lease                                            | Propriétaire uniquement                              | Propriétaire uniquement                                        |
| Put Page                                               | WritePages                                       | Propriétaire uniquement                              | Propriétaire uniquement                                        |
| Get Page Ranges                                        | GetPageRanges                                    | Tous                                     |                                                   |

##Créer et utiliser une signature d'accès partagé
Une signature d'accès partagé est un URI qui accorde des droits d'accès restreints aux conteneurs, objets blob, files d'attente et tables pour un intervalle de temps spécifique. En fournissant à un client une signature d'accès partagé, vous lui permettez d'accéder aux ressources de votre compte de stockage sans partager la clé de votre compte.

>[AZURE.NOTE]Pour obtenir une vue d'ensemble conceptuelle détaillée et un didacticiel sur les signatures d'accès partagé, consultez [Signatures d'accès partagé](http://azure.microsoft.com/documentation/articles/storage-dotnet-shared-access-signature-part-1/).

Les opérations prises en charge qui utilisent des signatures d'accès partagé sont notamment :

- La lecture et l'écriture de contenu d'objet blob de blocs ou de pages, de listes de blocs, de propriétés et de métadonnées ; 


- la suppression, la location et la création d'un instantané d'un objet blob ;

- la création d'une liste des objets blob contenus dans un conteneur ;

- l'ajout, la suppression, la mise à jour et la suppression de messages de la file d'attente (dans la version 2.0 et ultérieures de la bibliothèque cliente de stockage) ;

- l'obtention de métadonnées de file d'attente, y compris le nombre de messages (dans la version 2.0 et ultérieures de la bibliothèque cliente de stockage) ;

- l'interrogation, l'ajout, la mise à jour, la suppression et l'upsert des entités de table (dans la version 2.0 et ultérieures de la bibliothèque cliente de stockage).

Les paramètres de requête d'URI de signature d'accès partagé incorporent toutes les informations nécessaires pour accorder l'accès contrôlé à une ressource de stockage. Les paramètres de requête d'URI spécifient l'intervalle de temps pendant lequel la signature d'accès partagé est valide, les autorisations qu'elle accorde, la ressource qui doit être rendue disponible et la signature que les services de stockage doivent utiliser pour authentifier la demande.

En outre, l'URI de signature d'accès partagé peut faire référence à une stratégie d'accès stockée qui fournit un niveau de contrôle supplémentaire sur un ensemble de signatures, y compris la capacité à modifier ou à révoquer l'accès à la ressource si nécessaire. 

Pour plus d'informations sur le format d'URI de signature d'accès partagé, consultez [Délégation d'accès avec une signature d'accès partagé](https://msdn.microsoft.com/library/ee395415.aspx).

###Utilisation sécurisée des signatures d'accès partagé
Une signature d'accès partagé accorde l'accès à la ressource spécifiée par le biais des autorisations accordées à l'URI. Vous devez toujours utiliser le protocole HTTPS pour créer un URI de signature d'accès partagé. L'utilisation du protocole HTTP avec des signatures d'accès partagé peut augmenter la vulnérabilité de votre compte de stockage face à un usage malveillant.

Si une signature d'accès partagé accorde un accès qui n'est pas destiné au grand public, elle doit être créée avec le moins d'autorisations possibles. En outre, une signature d'accès partagé doit être distribuée aux clients sur une connexion sécurisée, être associée à une stratégie d'accès stockée pour la révocation et spécifier la durée de vie la plus courte possible de la signature.

>[AZURE.NOTE]L'URI d'une signature d'accès partagé est associé à la clé du compte utilisée pour créer la signature et à la stratégie d'accès stockée correspondante (le cas échéant). Si aucune stratégie d'accès stockée n'est spécifiée, la seule façon de révoquer une signature d'accès partagé consiste à modifier la clé du compte. 

###Création d'une signature d'accès partagé
L'exemple de code suivant crée une stratégie d'accès sur un conteneur puis génère une signature d'accès partagé pour ce conteneur. Cette signature d'accès partagé peut ensuite être communiquée aux clients :

    // The connection string for the storage account.  Modify for your account.
    string storageConnectionString =
       "DefaultEndpointsProtocol=https;" +
       "AccountName=myaccount;" +
       "AccountKey=<account-key>";
    
    // As an alternative, you can retrieve storage account information from an app.config file. 
    // This is one way to store and retrieve a connection string if you are 
    // writing an application that will run locally, rather than in Microsoft Azure.
    
    // string storageConnectionString = ConfigurationManager.AppSettings["StorageAccountConnectionString"];
    
    // Create the storage account with the connection string.
    CloudStorageAccount storageAccount = CloudStorageAccount.Parse(storageConnectionString);
       
    // Create the blob client object.
    CloudBlobClient blobClient = storageAccount.CreateCloudBlobClient();
    
    // Get a reference to the container for which shared access signature will be created.
    CloudBlobContainer container = blobClient.GetContainerReference("mycontainer");
    container.CreateIfNotExists();
    
    // Create blob container permissions, consisting of a shared access policy 
    // and a public access setting. 
    BlobContainerPermissions blobPermissions = new BlobContainerPermissions();
    
    // The shared access policy provides 
    // read/write access to the container for 10 hours.
    blobPermissions.SharedAccessPolicies.Add("mypolicy", new SharedAccessBlobPolicy()
    {
       // To ensure SAS is valid immediately, don't set start time.
       // This way, you can avoid failures caused by small clock differences.
       SharedAccessExpiryTime = DateTime.UtcNow.AddHours(10),
       Permissions = SharedAccessBlobPermissions.Write |
      SharedAccessBlobPermissions.Read
    });
    
    // The public access setting explicitly specifies that 
    // the container is private, so that it can't be accessed anonymously.
    blobPermissions.PublicAccess = BlobContainerPublicAccessType.Off;
    
    // Set the permission policy on the container.
    container.SetPermissions(blobPermissions);
    
    // Get the shared access signature to share with users.
    string sasToken =
       container.GetSharedAccessSignature(new SharedAccessBlobPolicy(), "mypolicy");

###Utilisation d'une signature d'accès partagé
Un client qui reçoit une signature d'accès partagé peut l'utiliser dans son code pour construire un objet de type [StorageCredentials](https://msdn.microsoft.com/library/microsoft.windowsazure.storage.auth.storagecredentials.aspx). Ces informations d'identification peuvent ensuite servir à construire un objet [CloudStorageAccount](https://msdn.microsoft.com/library/microsoft.windowsazure.storage.cloudstorageaccount.aspx) ou [CloudBlobClient](https://msdn.microsoft.com/library/microsoft.windowsazure.storage.blob.cloudblobclient.aspx) pour utiliser la ressource, comme indiqué dans cet exemple :

    Uri blobUri = new Uri("https://myaccount.blob.core.windows.net/mycontainer/myblob.txt");
    
    // Create credentials with the SAS token. The SAS token was created in previous example.
    StorageCredentials credentials = new StorageCredentials(sasToken);
    
    // Create a new blob.
    CloudBlockBlob blob = new CloudBlockBlob(blobUri, credentials);
    
    // Upload the blob. 
    // If the blob does not yet exist, it will be created. 
    // If the blob does exist, its existing content will be overwritten.
    using (var fileStream = System.IO.File.OpenRead(@"c:\Test\myblob.txt"))
    {
    blob.UploadFromStream(fileStream);
    }

##Utiliser une stratégie d'accès stockée
Une stratégie d'accès stockée fournit un niveau de contrôle supplémentaire sur les signatures d'accès partagé côté serveur. L'établissement d'une stratégie d'accès stockée sert à regrouper des signatures d'accès partagé et à fournir des restrictions supplémentaires pour les signatures liées par la stratégie. Vous pouvez utiliser une stratégie d'accès stockée pour modifier l'heure de début, l'heure d'expiration ou les autorisations d'une signature, ou pour la révoquer après sa publication.

Une stratégie d'accès stockée procure un contrôle accru sur les signatures d'accès partagé que vous avez publiées. Au lieu de spécifier la durée de vie et les autorisations de la signature sur l'URL, vous pouvez spécifier ces paramètres dans la stratégie d'accès stockée sur l'objet blob, le conteneur, la file d'attente ou la table partagé(e). Pour modifier ces paramètres pour une ou plusieurs signatures, vous pouvez modifier la stratégie d'accès stockée au lieu de republier les signatures. Vous pouvez aussi rapidement révoquer la signature en modifiant la stratégie d'accès stockée.

Par exemple, imaginez que vous avez publié une signature d'accès partagé associée à une stratégie d'accès stockée. Si vous avez spécifié l'heure d'expiration dans la stratégie d'accès stockée, vous pouvez modifier la stratégie d'accès pour prolonger la durée de vie de la signature sans avoir à publier une nouvelle signature.

Les meilleures pratiques recommandent de spécifier une stratégie d'accès stockée pour toute ressource signée pour laquelle vous publiez une signature d'accès partagé, car la stratégie stockée peut servir à modifier ou à révoquer la signature après sa publication. Si vous ne spécifiez pas de stratégie stockée, nous vous conseillons de limiter la durée de vie de votre signature pour réduire les risques au niveau de vos ressources de compte de stockage. 

###Association d'une signature d'accès partagé à une stratégie d'accès stockée
Une stratégie d'accès stockée inclut un nom composé de 64 caractères maximum et unique dans le conteneur, la file d'attente ou la table. Pour associer une signature d'accès partagé à une stratégie d'accès stockée, vous spécifiez cet identificateur lors de la création de la signature d'accès partagé. Dans l'URI de signature d'accès partagé, le champ  *signedidentifier* spécifie l'identificateur de la stratégie d'accès stockée.

Un conteneur, une file d'attente ou une table peuvent inclure jusqu'à cinq stratégies d'accès stockées. Chaque stratégie peut être utilisée par plusieurs signatures d'accès partagé.

>[AZURE.NOTE]Lorsque vous établissez une stratégie d'accès stockée sur un conteneur, une file d'attente ou une table, son application peut prendre jusqu'à 30 secondes. Au cours de cet intervalle, une signature d'accès partagé associée à la stratégie d'accès stockée échoue avec le code d'état 403 (Interdit), jusqu'à ce que la stratégie d'accès devienne active.

###Spécification des paramètres de stratégie d'accès pour une stratégie d'accès partagé
La stratégie d'accès stockée peut spécifier les paramètres suivants de stratégie d'accès pour les signatures auxquelles elle est associée :

- Heure de début

- Heure d'expiration

- Autorisations

Selon la façon dont vous voulez contrôler l'accès à vos ressources de stockage, vous pouvez spécifier tous ces paramètres dans la stratégie d'accès stockée et les omettre dans l'URL de la signature d'accès partagé. De cette façon, vous pouvez modifier le comportement de la signature associée à tout moment, de même que la révoquer. Vous pouvez aussi spécifier un ou plusieurs paramètres de stratégie d'accès dans la stratégie d'accès stockée et d'autres dans l'URL. Pour finir, vous pouvez spécifier tous les paramètres dans l'URL. Dans ce cas, vous pouvez utiliser la stratégie d'accès stockée pour révoquer la signature, mais pas pour modifier son comportement.

La signature d'accès partagé et la stratégie d'accès stockée doivent inclure tous les champs nécessaires à l'authentification de la signature. Si des champs obligatoires sont manquants, la demande échoue avec le code d'erreur 403 (Interdit). De même, si un champ est spécifié dans l'URL de la signature d'accès partagé et dans la stratégie d'accès stockée, la demande échoue avec le code d'état 403 (Requête incorrecte). Pour plus d'informations sur les champs qui composent la signature, consultez la rubrique Créer et utiliser une signature d'accès partagé.

###Modification ou révocation d'une stratégie d'accès stockée

Pour révoquer l'accès aux signatures d'accès partagé qui utilisent la même stratégie d'accès stockée, supprimez la stratégie stockée de la ressource de stockage en remplaçant la liste des stratégies stockées par une nouvelle liste qui ne contient pas le nom de la stratégie. Pour modifier les paramètres d'accès d'une stratégie d'accès stockée, remplacez la liste de stratégies stockées par une nouvelle liste contenant une stratégie de même nom mais associée à de nouvelles informations de contrôle d'accès.

<!--HONumber=47-->
