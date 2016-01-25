<properties 
	pageTitle="Gestion de l’accès en lecture anonyme aux conteneurs et aux objets blob | Microsoft Azure" 
	description="Découvrez comment autoriser l’accès anonyme aux conteneurs et aux objets Blob et comment utiliser un programme pour y accéder." 
	services="storage" 
	documentationCenter="" 
	authors="tamram" 
	manager="carmonm" 
	editor="tysonn"/>

<tags 
	ms.service="storage" 
	ms.workload="storage" 
	ms.tgt_pltfrm="na" 
	ms.devlang="na" 
	ms.topic="article" 
	ms.date="01/05/2016" 
	ms.author="tamram"/>

# Gestion de l’accès en lecture anonyme aux conteneurs et aux objets blob

## Vue d'ensemble

Par défaut, seul le propriétaire du compte de stockage peut accéder aux ressources de stockage de ce compte. Pour le stockage d’objets Blob uniquement, vous pouvez définir les autorisations d’un conteneur pour autoriser l’accès en lecture anonyme au conteneur et à ses objets Blob, afin que vous puissiez accorder l’accès à ces ressources sans avoir à partager votre clé de compte.

L’accès anonyme est idéal dans les situations où vous souhaitez permettre l’accès en lecture anonyme à certains objets Blob de façon permanente. Pour un contrôle plus fin, vous pouvez créer une signature d’accès partagé de manière à déléguer un accès restreint en utilisant des autorisations différentes sur un intervalle de temps spécifié. Pour plus d’informations sur les signatures d’accès partagé, consultez la page [Signatures d’accès partagé : présentation du modèle SAP](storage-dotnet-shared-access-signature-part-1.md)

## Accorder à des utilisateurs anonymes des autorisations d’accès aux conteneurs et objets blob

Par défaut, un conteneur et tous les objets blob qu'il contient sont accessibles uniquement par le propriétaire du compte de stockage. Pour fournir aux utilisateurs anonymes des autorisations de lecture sur un conteneur et ses objets blob, vous pouvez configurer les autorisations du conteneur afin d’autoriser l’accès public. Les utilisateurs anonymes peuvent lire les objets blob d’un conteneur accessible publiquement sans avoir à authentifier la demande.

Les conteneurs fournissent les options suivantes pour gérer leur accès :

- **Accès public en lecture** : les données de conteneur et d’objet blob peuvent être lues via une demande anonyme. Les clients peuvent énumérer les objets blob à l’intérieur du conteneur via une demande anonyme, mais ne peuvent pas énumérer les conteneurs dans le compte de stockage.

- **Accès public en lecture pour les objets blob uniquement :** les données d’objets blob à l’intérieur de ce conteneur peuvent être lues via une demande anonyme, mais les données du conteneur ne sont pas disponibles. Les clients ne peuvent pas énumérer les objets blob à l’intérieur du conteneur via une demande anonyme.

- **Pas d’accès public en lecture :** les données de conteneur et d’objet blob ne peuvent être lues que par le propriétaire du compte.

Vous pouvez définir les autorisations du conteneur de différentes manières :

- À partir du [portail Azure](portal.azure.com).
- Dans un programme, en utilisant la bibliothèque cliente de stockage ou l’API REST.
- En utilisant PowerShell. Pour en savoir plus sur la définition des autorisations du conteneur à partir d’Azure PowerShell, consultez [Utilisation d’Azure PowerShell avec Azure Storage](storage-powershell-guide-full#how-to-manage-azure-blobs).

### Définition des autorisations du conteneur à partir du portail Azure

Pour définir les autorisations du conteneur à partir du [portail Azure](portal.azure.com), procédez comme suit :

1. Accédez au tableau de bord de votre compte de stockage.
2. Dans la liste, sélectionnez le nom du conteneur. Notez que vous devez cliquer à droite de la colonne Nom pour sélectionner le nom du conteneur. Lorsque vous cliquez sur le nom, vous accédez aux objets Blob du conteneur.
3. Sélectionnez **Modifier** dans la barre d’outils.
4. Dans la boîte de dialogue **Modifier les métadonnées du conteneur**, sélectionnez le niveau d’autorisations dans le champ **Accès**, comme illustré dans la capture d’écran ci-dessous.

	![Boîte de dialogue Modifier les métadonnées du conteneur](./media/storage-manage-access-to-resources/storage-manage-access-to-resources-1.png)

### Définition des autorisations du conteneur par un programme à l’aide de .NET

Pour définir des autorisations pour un conteneur à l’aide de la bibliothèque cliente .NET, vous devez tout d’abord récupérer les autorisations du conteneur en appelant la méthode **GetPermissions**. Définissez ensuite la propriété **PublicAccess** de l’objet **BlobContainerPermissions** qui est retourné par la méthode **GetPermissions**. Pour finir, appelez la méthode **SetPermissions** avec les autorisations mises à jour.

Dans l’exemple suivant, nous définissons les autorisations du conteneur pour permettre un accès en lecture public complet. Pour autoriser un accès en lecture public pour les objets Blob uniquement, définissez la propriété **PublicAccess** sur **BlobContainerPublicAccessType.Blob**. Pour supprimer toutes les autorisations pour les utilisateurs anonymes, définissez la propriété sur **BlobContainerPublicAccessType.Off**.

    public static void SetPublicContainerPermissions(CloudBlobContainer container)
    {
        BlobContainerPermissions permissions = container.GetPermissions();
        permissions.PublicAccess = BlobContainerPublicAccessType.Container;
        container.SetPermissions(permissions);
    }

## Accéder anonymement aux conteneurs et aux objets Blob

Un client ayant un accès anonyme aux conteneurs et aux objets Blob peut utiliser des constructeurs qui ne nécessitent pas d’informations d’identification. Les exemples suivants illustrent différentes manières de référencer des ressources de service Blob de façon anonyme.

### Créer un objet de client anonyme

Vous pouvez créer un nouvel objet de client de service pour permettre un accès anonyme en spécifiant le point de terminaison du service Blob associé au compte. Toutefois, vous devez également connaître le nom d’un conteneur attaché à ce compte qui est disponible pour un accès anonyme.

    public static void CreateAnonymousBlobClient()
    {
        // Create the client object using the Blob service endpoint.
        CloudBlobClient blobClient = new CloudBlobClient(new Uri(@"https://storagesample.blob.core.windows.net"));

        // Get a reference to a container that's available for anonymous access.
        CloudBlobContainer container = blobClient.GetContainerReference("sample-container");

        // Read the container's properties. Note this is only possible when the container supports full public read access.
        container.FetchAttributes();
        Console.WriteLine(container.Properties.LastModified);
        Console.WriteLine(container.Properties.ETag);
    }

### Référencer un conteneur de manière anonyme

Si vous disposez de l’URL permettant d’accéder à un conteneur accessible de manière anonyme, vous pouvez l’utiliser pour référencer directement le conteneur.

    public static void ListBlobsAnonymously()
    {
        // Get a reference to a container that's available for anonymous access.
        CloudBlobContainer container = new CloudBlobContainer(new Uri(@"https://storagesample.blob.core.windows.net/sample-container"));

        // List blobs in the container.
        foreach (IListBlobItem blobItem in container.ListBlobs())
        {
            Console.WriteLine(blobItem.Uri);
        }
    }


### Référencer un objet Blob de façon anonyme

Si vous disposez de l’URL permettant d’accéder à un objet Blob accessible de manière anonyme, vous pouvez l’utiliser pour référencer directement l’objet Blob :

    public static void DownloadBlobAnonymously()
    {
        CloudBlockBlob blob = new CloudBlockBlob(new Uri(@"https://storagesample.blob.core.windows.net/sample-container/logfile.txt"));
        blob.DownloadToFile(@"C:\Temp\logfile.txt", System.IO.FileMode.Create);
    }

## Fonctionnalités accessibles aux utilisateurs anonymes

Le tableau suivant indique les opérations pouvant être appelées par les utilisateurs anonymes quand la liste de contrôle d’accès (ACL, Access Control List) est définie pour autoriser l’accès public.

| Opération REST | Autorisation avec accès en lecture public complet | Autorisation avec accès en lecture public pour les objets blob uniquement |
|--------------------------------------------------------|-----------------------------------------|---------------------------------------------------|
| List Containers | Propriétaire uniquement | Propriétaire uniquement |
| Create Container | Propriétaire uniquement | Propriétaire uniquement |
| Get Container Properties | Tout | Propriétaire uniquement |
| Get Container Metadata | Tout | Propriétaire uniquement |
| Set Container Metadata | Propriétaire uniquement | Propriétaire uniquement |
| Get Container ACL | Propriétaire uniquement | Propriétaire uniquement |
| Set Container ACL | Propriétaire uniquement | Propriétaire uniquement |
| Delete Container | Propriétaire uniquement | Propriétaire uniquement |
| List Blobs | Tout | Propriétaire uniquement |
| Put Blob | Propriétaire uniquement | Propriétaire uniquement |
| Get Blob | Tout | Tout |
| Get Blob Properties | Tout | Tout |
| Set Blob Properties | Propriétaire uniquement | Propriétaire uniquement |
| Get Blob Metadata | Tout | Tout |
| Set Blob Metadata | Propriétaire uniquement | Propriétaire uniquement |
| Put Block | Propriétaire uniquement | Propriétaire uniquement |
| Get Block List (blocs validés uniquement) | Tout | Tout |
| Get Block List (blocs non validés uniquement ou tous les blocs) | Propriétaire uniquement | Propriétaire uniquement |
| Put Block List | Propriétaire uniquement | Propriétaire uniquement |
| Delete Blob | Propriétaire uniquement | Propriétaire uniquement |
| Copie d'un objet blob | Propriétaire uniquement | Propriétaire uniquement |
| Snapshot Blob | Propriétaire uniquement | Propriétaire uniquement |
| Lease Blob | Propriétaire uniquement | Propriétaire uniquement |
| Put Page | Propriétaire uniquement | Propriétaire uniquement |
| Get Page Ranges | Tout | Tout |
| Append Blob | Propriétaire uniquement | Propriétaire uniquement |


## Voir aussi

- [Authentification pour les services de stockage Azure](https://msdn.microsoft.com/library/azure/dd179428.aspx)
- [Signatures d'accès partagé : présentation du modèle SAP](storage-dotnet-shared-access-signature-part-1.md)
- [Délégation de l'accès avec une signature d'accès partagé](https://msdn.microsoft.com/library/azure/ee395415.aspx) 

<!---HONumber=AcomDC_0114_2016-->