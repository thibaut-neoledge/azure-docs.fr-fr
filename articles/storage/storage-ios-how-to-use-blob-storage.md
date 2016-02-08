<properties
    pageTitle="Utilisation du stockage d’objets blob Azure à partir d’iOS | Microsoft Azure"
    description="Découvrez comment utiliser le stockage d’objets blob Azure pour télécharger, charger, répertorier et supprimer le contenu d’objets blob. Exemples écrits en Objective-C."
    services="storage"
    documentationCenter="ios"
    authors="micurd"
    manager="jahogg"/>

<tags
    ms.service="storage"
    ms.workload="storage"
    ms.tgt_pltfrm="na"
    ms.devlang="Objective-C"
    ms.topic="article"
    ms.date="01/05/2016"
    ms.author="micurd"/>

# Utilisation du stockage d’objets blob à partir d’iOS

[AZURE.INCLUDE [storage-selector-blob-include](../../includes/storage-selector-blob-include.md)]

## Vue d’ensemble

Cet article décrit le déroulement de scénarios courants dans le cadre de l’utilisation du service de stockage d’objets blob Microsoft Azure. Les exemples ont été écrits en Objective-C et utilisent la [bibliothèque iOS Azure Storage](https://github.com/Azure/azure-storage-ios). Les scénarios traités incluent le **téléchargement (vers une cible)**, la **création de listes**, le **téléchargement (à partir d’une source)** et la **suppression** d’objets blob. Pour plus d’informations sur les objets blob, voir la section [Étapes suivantes](#next-steps). Vous pouvez également télécharger l’[exemple d’application](https://github.com/Azure/azure-storage-ios/tree/master/BlobSample) pour voir rapidement l’utilisation d’Azure Storage dans une application iOS.

[AZURE.INCLUDE [storage-blob-concepts-include](../../includes/storage-blob-concepts-include.md)]

[AZURE.INCLUDE [storage-create-account-include](../../includes/storage-create-account-include.md)]

## Créer le fichier d’infrastructure
Pour utiliser la bibliothèque iOS Azure Storage, vous devez d’abord créer le fichier d’infrastructure.

1. Tout d’abord, téléchargez ou clonez le [référentiel azure-storage-ios](https://github.com/azure/azure-storage-ios).

2. Accédez à *azure-storage-ios* -> *Bibliothèque* -> *Bibliothèque cliente Azure Storage*, puis ouvrez `Azure Storage Client Library.xcodeproj` dans Xcode.

3. Dans le coin supérieur gauche de Xcode, changez le schéma actif de « Bibliothèque cliente Azure Storage » à « Infrastructure ».

4. Créez le projet (⌘+B). Cela créera un fichier `Azure Storage Client Library.framework` sur votre bureau.

## Importer la bibliothèque iOS Azure Storage dans votre application

Vous pouvez importer la bibliothèque iOS Azure Storage dans votre application en procédant comme suit :

1. Créez un projet ou ouvrez votre projet existant dans Xcode.

2. Cliquez sur votre projet dans la barre de navigation située sur la gauche, puis cliquez sur l’onglet *Général* en haut de l’éditeur de projets.

3. Dans la section *Bibliothèques et infrastructures liées*, cliquez sur le bouton Ajouter (+).

4. Cliquez sur *Ajouter autre…*. Accédez au fichier `Azure Storage Client Library.framework` que vous venez de créer et ajoutez-le.

5. Dans la section *Bibliothèques et infrastructures liées*, cliquez de nouveau sur le bouton Ajouter (+).

6. Dans la liste des bibliothèques déjà fournie, recherchez la bibliothèque `libxml2.2.dylib` et ajoutez-la à votre projet.

##Instruction d’importation
Vous devez inclure l’instruction d’importation suivante dans le fichier où vous souhaitez appeler l’API Azure Storage.

    // Include the following import statement to use blob APIs.
    #import <Azure Storage Client Library/Azure_Storage_Client_Library.h>

## Configurer votre application pour accéder au stockage d’objets blob

Il existe deux manières d’authentifier votre application pour accéder aux services de stockage :

- Clé partagée : utilisez la clé partagée aux fins de test uniquement
- Signature d’accès partagé (SAP) : utilisez la signature d’accès partagé pour les applications de production

###Clé partagée
L’authentification par clé partagée signifie que votre application utilisera votre nom de compte et votre clé de compte pour accéder aux services de stockage. Afin de vous montrer rapidement comment utiliser le stockage d’objets blob à partir d’iOS, nous allons utiliser l’authentification par clé partagée dans cet article de prise en main.

> [AZURE.WARNING (Only use Shared Key authentication for testing purposes!) ] Votre nom de compte et votre clé de compte, qui donnent un accès complet en lecture/écriture au compte de stockage associé, seront distribués à chaque personne qui télécharge votre application. Ce n’est **pas** une bonne pratique car votre clé risque d’être compromise par des clients non approuvés.

Lorsque vous utilisez une authentification par clé partagée, vous créez une chaîne de connexion. La chaîne de connexion comporte les éléments suivants :

- **DefaultEndpointsProtocol** : vous avez le choix entre les protocoles HTTP et HTTPS. Toutefois, l’utilisation du protocole HTTPS est fortement recommandée.
- **Nom du compte** : nom de votre compte de stockage.
- **Clé de compte** : si vous utilisez le [portail Azure](https://portal.azure.com), accédez à votre compte de stockage, puis cliquez sur l’icône **Clés** pour rechercher ces informations. Si vous utilisez le [portail Azure Classic](https://manage.windowsazure.com), accédez à votre compte de stockage dans le portail, puis cliquez sur **Gérer les clés d’accès**. 

Voici l’aspect affiché dans votre application :

    // Create a storage account object from a connection string.
    AZSCloudStorageAccount *account = [AZSCloudStorageAccount accountFromConnectionString:@"DefaultEndpointsProtocol=https;AccountName=account_name;AccountKey=account_key"];

###Signatures d’accès partagé (SAP)
Pour une application iOS, la méthode recommandée pour authentifier une demande d’un client par rapport au stockage d’objets blob consiste à utiliser une signature d’accès partagé (SAP). Une SAP vous permet d’accorder un accès client à une ressource pour une période spécifiée, avec un jeu d’autorisations spécifié. En tant que propriétaire du compte de stockage, vous devez générer une SAP que vos clients iOS pourront consommer. Pour ce faire, vous souhaiterez probablement écrire un service séparé qui génère les SAP à distribuer à vos clients. Aux fins de test, vous pouvez également utiliser l’interface de ligne de commande Azure pour générer une SAP. Notez que vos informations d’identification de clé partagée sont utilisées pour générer une SAP, mais que vos clients peuvent alors utiliser la SAP à l’aide des informations d’authentification encapsulées dans son URL. Lorsque vous créez la SAP, vous pouvez spécifier l’intervalle de temps pendant lequel elle est valide et les autorisations qu’elle accorde au client. Par exemple, pour un conteneur d’objets blob, la SAP peut accorder des autorisations de lecture, d’écriture ou de suppression pour un objet blob dans le conteneur, ainsi que des autorisations de création de listes des objets blob figurant dans le conteneur.

L’exemple suivant montre comment utiliser l’interface de ligne de commande Azure pour générer un jeton SAP qui accorde des autorisations de lecture et d’écriture sur le conteneur *sascontainer* jusqu’à minuit (12:00 AM UTC) le 5 septembre 2015.

1. Tout d’abord, suivez ce [guide](../xplat-cli/#how-to-install-the-azure-cli) pour savoir comment installer l’interface de ligne de commande Azure et vous connecter à votre abonnement Azure.

2. Ensuite, tapez la commande suivante dans l’interface de ligne de commande Azure pour obtenir la chaîne de connexion pour votre compte :

		azure storage account connectionstring show youraccountname

3. Créez une variable d’environnement avec la chaîne de connexion que vous venez de générer :

		export AZURE_STORAGE_CONNECTION_STRING=”your connection string”

4. Générer une URL de SAP :

		azure storage container sas create --container sascontainer --permissions rw --expiry 2015-09-05T00:00:00

5. L’URL de SAP doit se présenter ainsi :

		https://myaccount.blob.core.windows.net/sascontainer/sasblob.txt?sv=2012-02-12&st=2013-04-29T22%3A18%3A26Z&se=2013-04-30T02%3A23%3A26Z&sr=b&sp=rw&sig=Z%2FRHIX5Xcg0Mq2rqI3OlWTjEg2tYkboXr1P9ZUXDtkk%3D

6. Dans votre application iOS, vous pouvez désormais obtenir une référence à votre conteneur à l’aide de l’URL de SAP de la manière suivante :

		// Get a reference to a container in your Storage account
    	AZSCloudBlobContainer *blobContainer = [[AZSCloudBlobContainer alloc] initWithUrl:[NSURL URLWithString:@" your SAS URL"]];

Comme vous pouvez le voir, lorsque vous utilisez un jeton de SAP, vous n’exposez ni votre nom de compte ni votre clé de compte dans votre application iOS. Vous pouvez en savoir plus sur les SAP en consultant le [didacticiel de signature d’accès partagé](../storage-dotnet-shared-access-signature-part-1).

##Opérations asynchrones
> [AZURE.NOTE] Toutes les méthodes qui effectuent une demande auprès du service sont des opérations asynchrones. Dans les exemples de code, vous découvrirez que ces méthodes sont dotées d’un gestionnaire d’achèvement. Le code dans le gestionnaire d’achèvement s’exécutera **après** que la demande sera terminée. Le code suivant le gestionnaire d’achèvement s’exécute **pendant** l’exécution de la demande.

## Création d’un conteneur
Chaque objet blob dans Azure Storage doit résider dans un conteneur. L’exemple suivant montre comment créer un conteneur appelé *newcontainer* dans votre compte de stockage s’il n’existe pas déjà. Lorsque vous choisissez un nom pour votre conteneur, gardez à l’esprit les règles d’affectation de noms mentionnées ci-dessus.

     -(void)createContainer{
        // Create a storage account object from a connection string.
        AZSCloudStorageAccount *account = [AZSCloudStorageAccount accountFromConnectionString:@"DefaultEndpointsProtocol=https;AccountName=your_account_name_here;AccountKey=your_account_key_here"];

        // Create a blob service client object.
        AZSCloudBlobClient *blobClient = [account getBlobClient];

        // Create a local container object.
        AZSCloudBlobContainer *blobContainer = [blobClient containerReferenceFromName:@"newcontainer"];

        // Create container in your Storage account if the container doesn't already exist
        [blobContainer createContainerIfNotExistsWithCompletionHandler:^(NSError *error, BOOL exists) {
            if (error){
                NSLog(@"Error in creating container.");
            }
        }];
    }

Vous pouvez vérifier que cela fonctionne en examinant le [portail Azure](https://portal.azure.com) ou tout [explorateur de stockage](http://blogs.msdn.com/b/windowsazurestorage/archive/2014/03/11/windows-azure-storage-explorers-2014.aspx) et en vérifiant que *newcontainer* figure dans la liste des conteneurs pour votre compte de stockage.

## Définir les autorisations du conteneur
Les autorisations d’un conteneur sont configurées pour l’accès **Privé** par défaut. Toutefois, les conteneurs fournissent d’autres options pour l’accès aux conteneurs :

- **Privé** : seul le propriétaire du compte peut lire les données de conteneur et d’objet blob.

- **BLOB** : les données blob à l’intérieur de ce conteneur sont lisibles au moyen d’une demande anonyme, mais les données du conteneur ne sont pas disponibles. Les clients ne peuvent pas énumérer les objets blob à l’intérieur du conteneur via une demande anonyme.

- **Conteneur** : les données de conteneur et blob sont lisibles au moyen d’une demande anonyme. Les clients peuvent énumérer les objets blob à l’intérieur du conteneur via une demande anonyme, mais ne peuvent pas énumérer les conteneurs dans le compte de stockage.

L’exemple suivant montre comment créer un conteneur avec des autorisations d’accès de type **Conteneur**, qui autorisent un accès public en lecture seule à tous les utilisateurs sur Internet :

     -(void)createContainerWithPublicAccess{
        // Create a storage account object from a connection string.
        AZSCloudStorageAccount *account = [AZSCloudStorageAccount accountFromConnectionString:@"DefaultEndpointsProtocol=https;AccountName=your_account_name_here;AccountKey=your_account_key_here"];

        // Create a blob service client object.
        AZSCloudBlobClient *blobClient = [account getBlobClient];

        // Create a local container object.
        AZSCloudBlobContainer *blobContainer = [blobClient containerReferenceFromName:@"containerpublic"];

        // Create container in your Storage account if the container doesn't already exist
        [blobContainer createContainerIfNotExistsWithAccessType:AZSContainerPublicAccessTypeContainer requestOptions:nil operationContext:nil completionHandler:^(NSError *error, BOOL exists){
            if (error){
                NSLog(@"Error in creating container.");
            }
        }];
    }

## Charger un objet blob dans un conteneur
Comme mentionné dans la section [Concepts du service BLOB](#blob-service-concepts), Blob Storage offre trois types d’objet blob : les objets blob de blocs, les objets blob d’ajouts et les objets blob de pages. À ce stade, la bibliothèque iOS Azure Storage prend uniquement en charge les objets blob de blocs. Dans la plupart des cas, il est recommandé d’utiliser le type d’objet blob de blocs.

L’exemple suivant montre comment charger un objet blob de blocs à partir d’une chaîne NSString. Si un objet blob portant le même nom existe déjà dans ce conteneur, le contenu de cet objet blob est remplacé.

     -(void)uploadBlobToContainer{
        // Create a storage account object from a connection string.
        AZSCloudStorageAccount *account = [AZSCloudStorageAccount accountFromConnectionString:@"DefaultEndpointsProtocol=https;AccountName=your_account_name_here;AccountKey=your_account_key_here"];

        // Create a blob service client object.
        AZSCloudBlobClient *blobClient = [account getBlobClient];

        // Create a local container object.
        AZSCloudBlobContainer *blobContainer = [blobClient containerReferenceFromName:@"containerpublic"];

        [blobContainer createContainerIfNotExistsWithAccessType:AZSContainerPublicAccessTypeContainer requestOptions:nil operationContext:nil completionHandler:^(NSError *error, BOOL exists)
         {
             if (error){
                 NSLog(@"Error in creating container.");
             }
         	 else{
				 // Create a local blob object
             	 AZSCloudBlockBlob *blockBlob = [blobContainer blockBlobReferenceFromName:@"sampleblob"];

             	 // Upload blob to Storage
             	 [blockBlob uploadFromText:@"This text will be uploaded to Blob Storage." completionHandler:^(NSError *error) {
                 	if (error){
						NSLog(@"Error in creating blob.");
                 	}             
             	 }];
			 }
         }];
     }

Vous pouvez vérifier que cela fonctionne en examinant le [portail Azure](https://portal.azure.com) ou tout [explorateur de stockage](http://blogs.msdn.com/b/windowsazurestorage/archive/2014/03/11/windows-azure-storage-explorers-2014.aspx), et en vérifiant que le conteneur *containerpublic* contient l’objet blob *sampleblob*. Dans cet exemple, nous avons utilisé un conteneur public afin que vous puissiez également vérifier que cela a fonctionné en accédant à l’URI des objets blob :

    https://nameofyourstorageaccount.blob.core.windows.net/containerpublic/sampleblob

Outre le chargement d’un objet blob de blocs à partir d’une chaîne NSString, des méthodes similaires existent pour NSData, NSInputStream ou un fichier local.

## Création d’une liste d’objets blob dans un conteneur
L’exemple suivant montre comment répertorier tous les objets blob dans un conteneur. Lorsque vous effectuez cette opération, gardez à l’esprit les paramètres suivants :

- **continuationToken** : le jeton de continuation représente l’emplacement où l’opération de liste doit commencer. Si aucun jeton n’est fourni, il répertorie les objets blob depuis le début. N’importe quel nombre d’objets blob peut être répertorié, à partir de zéro jusqu’à un maximum spécifié. Même si cette méthode ne retourne aucun résultat, si la valeur `results.continuationToken` n’est pas nulle, il peut y avoir davantage d’objets blob sur le service qui n’ont pas été répertoriés.
- **prefix** : vous pouvez spécifier le préfixe à utiliser pour la liste d’objets blob. Seuls les objets blob qui commencent par ce préfixe sont répertoriés.
- **useFlatBlobListing** : comme indiqué dans la section [Désignation et référencement des conteneurs et des objets blob](#naming-and-referencing-containers-and-blobs), bien que le service BLOB soit un schéma de stockage plat, vous pouvez créer une hiérarchie virtuelle en nommant les objets blob avec des informations de chemin d’accès. Toutefois, les listes de stockage non plat ne sont actuellement pas prises en charge ; cette option sera bientôt disponible. Pour le moment, cette valeur doit être `YES`
- **blobListingDetails** : vous pouvez spécifier les éléments à inclure lors de la création de la liste d’objets blob.
	- `AZSBlobListingDetailsNone` : répertorie uniquement les objets blob validés et ne renvoie pas de métadonnées d’objet blob.
	- `AZSBlobListingDetailsSnapshots` : répertorie les objets blob validés et les instantanés d’objet blob.
	- `AZSBlobListingDetailsMetadata` : récupère les métadonnées d’objet blob pour chaque objet blob renvoyé dans la liste.
	- `AZSBlobListingDetailsUncommittedBlobs` : répertorie les objets blob validés et non validés.
	- `AZSBlobListingDetailsCopy` : inclut des propriétés de copie dans la liste.
	- `AZSBlobListingDetailsAll` : répertorie tous les objets blob validés, objets blob non validés et instantanés disponibles, et renvoie l’état de toutes les métadonnées et de la copie pour ces objets blob.
- **maxResults** : nombre maximal de résultats à renvoyer pour cette opération. Utilisez -1 pour ne pas définir une limite.
- **completionHandler** : bloc de code à exécuter avec les résultats de l’opération de génération de liste.

Dans cet exemple, une méthode d’assistance est utilisée pour appeler de manière récursive la méthode de liste d’objets blob à chaque fois qu’un jeton de continuation est renvoyé.

    -(void)listBlobsInContainer{
      // Create a storage account object from a connection string.
      AZSCloudStorageAccount *account = [AZSCloudStorageAccount accountFromConnectionString:@"DefaultEndpointsProtocol=https;AccountName=your_account_name_here;AccountKey=your_account_key_here"];

      // Create a blob service client object.
      AZSCloudBlobClient *blobClient = [account getBlobClient];

      // Create a local container object.
      AZSCloudBlobContainer *blobContainer = [blobClient containerReferenceFromName:@"containerpublic"];

      //List all blobs in container
      [self listBlobsInContainerHelper:blobContainer continuationToken:nil prefix:nil blobListingDetails:AZSBlobListingDetailsAll maxResults:-1 completionHandler:^(NSError *error) {
         if (error != nil){
             NSLog(@"Error in creating container.");
         }
      }];
    }

    //List blobs helper method
    -(void)listBlobsInContainerHelper:(AZSCloudBlobContainer *)container continuationToken:(AZSContinuationToken *)continuationToken prefix:(NSString *)prefix blobListingDetails:(AZSBlobListingDetails)blobListingDetails maxResults:(NSUInteger)maxResults completionHandler:(void (^)(NSError *))completionHandler
    {
      [container listBlobsSegmentedWithContinuationToken:continuationToken prefix:prefix useFlatBlobListing:YES blobListingDetails:blobListingDetails maxResults:maxResults completionHandler:^(NSError *error, AZSBlobResultSegment *results) {
         if (error)
         {
             completionHandler(error);
         }
         else
         {
             for (int i = 0; i < results.blobs.count; i++) {
                 NSLog(@"%@",[(AZSCloudBlockBlob *)results.blobs[i] blobName]);
             }
             if (results.continuationToken)
             {
                 [self listBlobsInContainerHelper:container continuationToken:results.continuationToken prefix:prefix blobListingDetails:blobListingDetails maxResults:maxResults completionHandler:completionHandler];
             }
             else
             {
                 completionHandler(nil);
             }
         }
      }];
    }

## Téléchargement d’un objet blob

L’exemple suivant montre comment télécharger un objet blob vers un objet NSString.

     -(void)downloadBlobToString{
        // Create a storage account object from a connection string.
        AZSCloudStorageAccount *account = [AZSCloudStorageAccount accountFromConnectionString:@"DefaultEndpointsProtocol=https;AccountName=your_account_name_here;AccountKey=your_account_key_here"];

        // Create a blob service client object.
        AZSCloudBlobClient *blobClient = [account getBlobClient];

        // Create a local container object.
        AZSCloudBlobContainer *blobContainer = [blobClient containerReferenceFromName:@"containerpublic"];

        // Create a local blob object
        AZSCloudBlockBlob *blockBlob = [blobContainer blockBlobReferenceFromName:@"sampleblob"];

        // Download blob    
        [blockBlob downloadToTextWithCompletionHandler:^(NSError *error, NSString *text) {
            if (error) {
                NSLog(@"Error in downloading blob");
            }
			else{
            	NSLog(@"%@",text);
			}
        }];
    }

## Supprimer un blob.

L’exemple suivant indique comment supprimer un objet blob.

     -(void)deleteBlob{
        // Create a storage account object from a connection string.
        AZSCloudStorageAccount *account = [AZSCloudStorageAccount accountFromConnectionString:@"DefaultEndpointsProtocol=https;AccountName=your_account_name_here;AccountKey=your_account_key_here"];

        // Create a blob service client object.
        AZSCloudBlobClient *blobClient = [account getBlobClient];

        // Create a local container object.
        AZSCloudBlobContainer *blobContainer = [blobClient containerReferenceFromName:@"containerpublic"];

        // Create a local blob object
        AZSCloudBlockBlob *blockBlob = [blobContainer blockBlobReferenceFromName:@"sampleblob1"];

        // Delete blob
        [blockBlob deleteWithCompletionHandler:^(NSError *error) {
          if (error) {
            NSLog(@"Error in deleting blob.");
          }
        }];
     }

## Suppression d’un conteneur d’objets blob

L’exemple suivant indique comment supprimer un conteneur.

     -(void)deleteContainer{
        // Create a storage account object from a connection string.
        AZSCloudStorageAccount *account = [AZSCloudStorageAccount accountFromConnectionString:@"DefaultEndpointsProtocol=https;AccountName=your_account_name_here;AccountKey=your_account_key_here"];

        // Create a blob service client object.
        AZSCloudBlobClient *blobClient = [account getBlobClient];

        // Create a local container object.
        AZSCloudBlobContainer *blobContainer = [blobClient containerReferenceFromName:@"containerpublic"];

        // Delete container
        [blobContainer deleteContainerIfExistsWithCompletionHandler:^(NSError *error, BOOL success) {
            if(error){
                NSLog(@"Error in deleting container");
            }
        }];
    }

## Étapes suivantes

Maintenant que vous connaissez les bases du stockage d’objets blob, voir les liens suivants pour apprendre à exécuter des tâches de stockage plus complexes.

- [Bibliothèque iOS Azure Storage]
- [API REST d’Azure Storage]
- [Transfert de données avec l’utilitaire de ligne de commande AzCopy](storage-use-azcopy)
- [Blog de l'équipe Azure Storage]

Si vous avez des questions concernant cette bibliothèque, n’hésitez pas à les publier sur notre [forum MSDN Azure](http://social.msdn.microsoft.com/Forums/windowsazure/home?forum=windowsazuredata) ou sur [Stack Overflow](http://stackoverflow.com/questions/tagged/windows-azure-storage+or+windows-azure-storage+or+azure-storage-blobs+or+azure-storage-tables+or+azure-table-storage+or+windows-azure-queues+or+azure-storage-queues+or+azure-storage-emulator+or+azure-storage-files). Si vous avez des suggestions de fonctionnalités pour Azure Storage, envoyez un message sur [Commentaires Azure Storage](https://feedback.azure.com/forums/217298-storage/).

[Bibliothèque iOS Azure Storage]: https://github.com/azure/azure-storage-ios
[API REST d’Azure Storage]: https://msdn.microsoft.com/library/azure/dd179355.aspx
[Blog de l'équipe Azure Storage]: http://blogs.msdn.com/b/windowsazurestorage

<!---HONumber=AcomDC_0128_2016-->