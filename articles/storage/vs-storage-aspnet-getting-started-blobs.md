---
title: "Prise en main du stockage d’objets blob Azure et des services connectés de Visual Studio (ASP.NET) | Microsoft Docs"
description: "Comment prendre en main le stockage d’objets blob Azure dans le cadre d’un projet ASP.NET Visual Studio après s’être connecté à un compte de stockage à l’aide des services connectés de Visual Studio"
services: storage
documentationcenter: 
author: TomArcher
manager: douge
editor: 
ms.assetid: b3497055-bef8-4c95-8567-181556b50d95
ms.service: storage
ms.workload: web
ms.tgt_pltfrm: vs-getting-started
ms.devlang: na
ms.topic: article
ms.date: 12/21/2016
ms.author: tarcher
ms.translationtype: HT
ms.sourcegitcommit: 8021f8641ff3f009104082093143ec8eb087279e
ms.openlocfilehash: 8fd13efdbdd98c6d7dff1b88a6b232a08aa5a13d
ms.contentlocale: fr-fr
ms.lasthandoff: 07/21/2017

---
# <a name="get-started-with-azure-blob-storage-and-visual-studio-connected-services-aspnet"></a>Prise en main du stockage d’objets blob Azure et des services connectés de Visual Studio (ASP.NET)
[!INCLUDE [storage-try-azure-tools-blobs](../../includes/storage-try-azure-tools-blobs.md)]

## <a name="overview"></a>Vue d'ensemble

Le stockage d’objets blob Azure est un service qui stocke des données non structurées dans le cloud en tant qu’objets/blobs. Ce service peut stocker tout type de données texte ou binaires, par exemple, un document, un fichier multimédia ou un programme d’installation d’application. Le stockage d’objets blob est également appelé Blob Storage.

Ce didacticiel montre comment écrire du code ASP.NET pour des scénarios courants d’utilisation du stockage d’objets blob Azure. Ces scénarios incluent la création d’un conteneur d’objets blob, ainsi que le chargement, la création d’une liste, le téléchargement et la suppression d’objets blob.

##<a name="prerequisites"></a>Composants requis

* [Microsoft Visual Studio](https://www.visualstudio.com/downloads/)
* [Compte Stockage Azure](storage-create-storage-account.md#create-a-storage-account)

[!INCLUDE [storage-blob-concepts-include](../../includes/storage-blob-concepts-include.md)]

[!INCLUDE [storage-create-account-include](../../includes/vs-storage-aspnet-getting-started-create-azure-account.md)]

[!INCLUDE [storage-development-environment-include](../../includes/vs-storage-aspnet-getting-started-setup-dev-env.md)]

### <a name="create-an-mvc-controller"></a>Créer un contrôleur MVC 

1. Dans l’**Explorateur de solutions**, cliquez avec le bouton droit sur **Contrôleurs**, puis sélectionnez **Ajouter -> Contrôleur** dans le menu contextuel.

    ![Ajouter un contrôleur à une application ASP.NET MVC](./media/vs-storage-aspnet-getting-started-blobs/add-controller-menu.png)

1. Dans la boîte de dialogue **Ajouter la structure**, cliquez sur **Contrôleur MVC 5 - vide**, puis sélectionnez **Ajouter**.

    ![Spécifier le type de contrôleur MVC](./media/vs-storage-aspnet-getting-started-blobs/add-controller.png)

1. Dans la boîte de dialogue **Ajouter un contrôleur**, nommez le contrôleur *BlobsController*, puis sélectionnez **Ajouter**.

    ![Nommer le contrôleur MVC](./media/vs-storage-aspnet-getting-started-blobs/add-controller-name.png)

1. Ajoutez les directives *using* suivantes au fichier `BlobsController.cs` :

    ```csharp
    using Microsoft.Azure;
    using Microsoft.WindowsAzure.Storage;
    using Microsoft.WindowsAzure.Storage.Auth;
    using Microsoft.WindowsAzure.Storage.Blob;
    ```

## <a name="create-a-blob-container"></a>Création d’un conteneur d’objets blob

Un conteneur d’objets blob est une hiérarchie imbriquée d’objets blobs et de dossiers. Les étapes suivantes montrent comment créer un conteneur d’objets blob :

> [!NOTE]
> 
> Le code de cette section part du principe que vous avez terminé les étapes décrites dans la section [Configuration de l’environnement de développement](#set-up-the-development-environment). 

1. Ouvrez le fichier `BlobsController.cs` .

1. Ajoutez une méthode appelée **CreateBlobContainer** qui retourne un objet **ActionResult**.

    ```csharp
    public ActionResult CreateBlobContainer()
    {
        // The code in this section goes here.

        return View();
    }
    ```
 
1. Dans la méthode **CreateBlobContainer**, ajoutez un objet **CloudStorageAccount** qui représente les informations de votre compte de stockage. Le code suivant permet d'obtenir la chaîne de connexion de stockage et les informations de compte de stockage à partir de la configuration du service Azure. (Remplacez *&lt;storage-account-name>* par le nom du compte de stockage Azure auquel accéder.)
   
    ```csharp
    CloudStorageAccount storageAccount = CloudStorageAccount.Parse(
       CloudConfigurationManager.GetSetting("<storage-account-name>_AzureStorageConnectionString"));
    ```
   
1. Ajoutez un objet **CloudBlobClient** représentant un client du service BLOB.
   
    ```csharp
    CloudBlobClient blobClient = storageAccount.CreateCloudBlobClient();
    ```

1. Ajoutez un objet **CloudBlobContainer** représentant une référence au nom souhaité pour le conteneur d’objets blob. La méthode **CloudBlobClient.GetContainerReference** n’effectue pas de demande auprès du stockage d’objets blob. La référence est renvoyée que le conteneur d’objets blob existe ou non. 
   
    ```csharp
    CloudBlobContainer container = blobClient.GetContainerReference("test-blob-container");
    ```

1. Appelez la méthode **CloudBlobContainer.CreateIfNotExists** pour créer le conteneur d’objets blob si celui-ci n’existe pas encore. La méthode **CloudBlobContainer.CreateIfNotExists** renvoie **true** si le conteneur n’existe pas et est créé avec succès. Sinon, la valeur **false** est retournée.    

    ```csharp
    ViewBag.Success = container.CreateIfNotExists();
    ```

1. Mettez à jour l’objet **ViewBag** avec le nom du conteneur d’objets blob.

    ```csharp
    ViewBag.BlobContainerName = container.Name;
    ```

1. Dans l’**Explorateur de solutions** de Visual Studio, développez le dossier **Vues**, cliquez avec le bouton droit sur **Objets blob**, puis sélectionnez **Ajouter -> Vue** dans le menu contextuel.

1. Dans la boîte de dialogue **Ajouter une vue**, entrez **CreateBlobContainer** pour le nom de la vue, puis sélectionnez **Ajouter**.

1. Ouvrez le fichier `CreateBlobContainer.cshtml` et modifiez-le pour qu’il se présente comme l'extrait de code suivant :

    ```csharp
    @{
        ViewBag.Title = "Create Blob Container";
    }
    
    <h2>Create Blob Container results</h2>

    Creation of @ViewBag.BlobContainerName @(ViewBag.Success == true ? "succeeded" : "failed")
    ```

1. Dans l’**Explorateur de solutions**, développez le dossier **Vues -> Partagé** et ouvrez le fichier `_Layout.cshtml`.

1. Après le dernier élément **Html.ActionLink**, ajoutez l’élément **Html.ActionLink** suivant :

    ```html
    <li>@Html.ActionLink("Create blob container", "CreateBlobContainer", "Blobs")</li>
    ```

1. Exécutez l’application, puis sélectionnez **Créer un conteneur d’objets blob** pour afficher des résultats similaires à la capture d’écran suivante :
  
    ![Création du conteneur d’objets blob](./media/vs-storage-aspnet-getting-started-blobs/create-blob-container-results.png)

    Comme mentionné précédemment, la méthode **CloudBlobContainer.CreateIfNotExists** renvoie **true** uniquement lorsque le conteneur n’existe pas et est créé. Par conséquent, si vous exécutez l’application alors que le conteneur existe, la méthode renvoit la valeur **false**. Pour exécuter l’application plusieurs fois, vous devez supprimer le conteneur avant d’exécuter à nouveau l’application. Le conteneur peut être supprimé à l’aide de la méthode **CloudBlobContainer.Delete**. Vous pouvez également le faire à partir du [portail Azure](http://go.microsoft.com/fwlink/p/?LinkID=525040) ou de l’[Explorateur de stockage Microsoft Azure](../vs-azure-tools-storage-manage-with-storage-explorer.md).  

## <a name="upload-a-blob-into-a-blob-container"></a>Charger un objet blob dans un conteneur d’objets blob

Une fois que vous avez [créé un conteneur d’objets blob](#create-a-blob-container), vous pouvez charger des fichiers dans ce conteneur. Cette section présente la procédure à suivre pour charger un fichier local dans un conteneur d’objets blob. Dans ces étapes, le conteneur d’objets blob créé porte le nom *test-blob-container*. 

> [!NOTE]
> 
> Le code de cette section part du principe que vous avez terminé les étapes décrites dans la section [Configuration de l’environnement de développement](#set-up-the-development-environment). 

1. Ouvrez le fichier `BlobsController.cs` .

1. Ajoutez une méthode appelée **UploadBlob** qui renvoie un objet **EmptyResult**.

    ```csharp
    public EmptyResult UploadBlob()
    {
        // The code in this section goes here.

        return new EmptyResult();
    }
    ```
 
1. Dans la méthode **UploadBlob**, ajoutez un objet **CloudStorageAccount** qui représente les informations de votre compte de stockage. Le code suivant permet d'obtenir la chaîne de connexion de stockage et les informations de compte de stockage à partir de la configuration du service Azure : (Remplacez *&lt;storage-account-name>* par le nom du compte de stockage Azure auquel accéder.)
   
    ```csharp
    CloudStorageAccount storageAccount = CloudStorageAccount.Parse(
       CloudConfigurationManager.GetSetting("<storage-account-name>_AzureStorageConnectionString"));
    ```
   
1. Ajoutez un objet **CloudBlobClient** représentant un client du service BLOB.
   
    ```csharp
    CloudBlobClient blobClient = storageAccount.CreateCloudBlobClient();
    ```

1. Ajoutez un objet **CloudBlobContainer** représentant une référence au nom du conteneur d’objets blob. 
   
    ```csharp
    CloudBlobContainer container = blobClient.GetContainerReference("test-blob-container");
    ```

1. Comme nous l’avons expliqué précédemment, le stockage Azure prend en charge différents types d’objets blob. Pour récupérer une référence à un objet blob de pages, appelez la méthode **CloudBlobContainer.GetPageBlobReference**. Pour récupérer une référence à un objet blob de blocs, appelez la méthode **CloudBlobContainer.GetBlockBlobReference**. En règle générale, il est recommandé d’utiliser le type d’objet blob de blocs. (Remplacez <blob-name>* par le nom que vous souhaitez donner à l’objet blob une fois ce dernier chargé.)

    ```csharp
    CloudBlockBlob blob = container.GetBlockBlobReference(<blob-name>);
    ```

1. Une fois que vous disposez d’une référence d’objet blob, vous pouvez charger n’importe quel flux de données vers cet objet en appelant la méthode **UploadFromStream** pour l’objet de cette référence. La méthode **UploadFromStream** crée l’objet blob s’il n’existe pas. S’il existe, elle le remplace. (Remplacez *&lt;file-to-upload>* par le chemin d’accès complet au fichier à charger.)

    ```csharp
    using (var fileStream = System.IO.File.OpenRead(<file-to-upload>))
    {
        blob.UploadFromStream(fileStream);
    }
    ```

1. Dans l’**Explorateur de solutions** de Visual Studio, développez le dossier **Vues**, cliquez avec le bouton droit sur **Objets blob**, puis sélectionnez **Ajouter -> Vue** dans le menu contextuel.

1. Dans l’**Explorateur de solutions**, développez le dossier **Vues -> Partagé** et ouvrez le fichier `_Layout.cshtml`.

1. Après le dernier élément **Html.ActionLink**, ajoutez l’élément **Html.ActionLink** suivant :

    ```html
    <li>@Html.ActionLink("Upload blob", "UploadBlob", "Blobs")</li>
    ```

1. Exécutez l’application, puis sélectionnez **Upload blob** (Charger l’objet blob).  
  
La section [Répertorier les objets blob d’un conteneur d’objets blob](#list-the-blobs-in-a-blob-container) explique comment répertorier les objets blob d’un conteneur d’objets blob.    

## <a name="list-the-blobs-in-a-blob-container"></a>Répertorier les objets blob d’un conteneur d’objets blob

Cette section explique comment répertorier les objets blob d’un conteneur d’objets blob. Les exemples de code font référence au conteneur *test-blob-container* créé dans la section [Création d’un conteneur d’objets blob](#create-a-blob-container).

> [!NOTE]
> 
> Le code de cette section part du principe que vous avez terminé les étapes décrites dans la section [Configuration de l’environnement de développement](#set-up-the-development-environment). 

1. Ouvrez le fichier `BlobsController.cs` .

1. Ajoutez une méthode appelée **ListBlobs** qui renvoie un objet **ActionResult**.

    ```csharp
    public ActionResult ListBlobs()
    {
        // The code in this section goes here.

        return View();
    }
    ```
 
1. Dans la méthode **ListBlobs**, ajoutez un objet **CloudStorageAccount** qui représente les informations de votre compte de stockage. Le code suivant permet d'obtenir la chaîne de connexion de stockage et les informations de compte de stockage à partir de la configuration du service Azure : (Remplacez *&lt;storage-account-name>* par le nom du compte de stockage Azure auquel accéder.)
   
    ```csharp
    CloudStorageAccount storageAccount = CloudStorageAccount.Parse(
       CloudConfigurationManager.GetSetting("<storage-account-name>_AzureStorageConnectionString"));
    ```
   
1. Ajoutez un objet **CloudBlobClient** représentant un client du service BLOB.
   
    ```csharp
    CloudBlobClient blobClient = storageAccount.CreateCloudBlobClient();
    ```

1. Ajoutez un objet **CloudBlobContainer** représentant une référence au nom du conteneur d’objets blob. 
   
    ```csharp
    CloudBlobContainer container = blobClient.GetContainerReference("test-blob-container");
    ```

1. Pour répertorier les objets blob d’un conteneur d’objets blob, utilisez la méthode **CloudBlobContainer.ListBlobs**. La méthode **CloudBlobContainer.ListBlobs** renvoie un objet **IListBlobItem** que vous convertissez en objet **CloudBlockBlob**, **CloudPageBlob** ou **CloudBlobDirectory**. L’extrait de code suivant énumère tous les objets blob dans un conteneur d’objets blob. Chaque objet blob est converti en objet approprié en fonction de son type, puis son nom (ou son URI, dans le cas d’un **CloudBlobDirectory**) est ajouté à une liste.

    ```csharp
    List<string> blobs = new List<string>();

    foreach (IListBlobItem item in container.ListBlobs(null, false))
    {
        if (item.GetType() == typeof(CloudBlockBlob))
        {
            CloudBlockBlob blob = (CloudBlockBlob)item;
            blobs.Add(blob.Name);
        }
        else if (item.GetType() == typeof(CloudPageBlob))
        {
            CloudPageBlob blob = (CloudPageBlob)item;
            blobs.Add(blob.Name);
        }
        else if (item.GetType() == typeof(CloudBlobDirectory))
        {
            CloudBlobDirectory dir = (CloudBlobDirectory)item;
            blobs.Add(dir.Uri.ToString());
        }
    }

    return View(blobs);
    ```

    Outre des objets blobs, les conteneurs d’objets blob peuvent contenir des répertoires. Supposons que vous disposiez d’un conteneur d’objets blob appelé *test-blob-container* et qui présente la hiérarchie suivante :

        foo.png
        dir1/bar.png
        dir2/baz.png

    Avec l’exemple de code précédent, la liste de la chaîne **blobs** contient des valeurs similaires à ce qui suit :

        foo.png
        <storage-account-url>/test-blob-container/dir1
        <storage-account-url>/test-blob-container/dir2

    Comme vous pouvez le voir, la liste inclut uniquement les entités de niveau supérieur ; elle n’inclut pas celles qui sont imbriquées (*bar.png* et *baz.png*). Pour répertorier toutes les entités d’un conteneur d’objets blob, vous devez appeler la méthode **CloudBlobContainer.ListBlobs** et transmettre **true** pour le paramètre **useFlatBlobListing**.    

    ```csharp
    ...
    foreach (IListBlobItem item in container.ListBlobs(useFlatBlobListing:true))
    ...
    ```

    La définition du paramètre **useFlatBlobListing** sur **true** renvoie une liste plate de toutes les entités du conteneur d’objets blob et produit le résultat suivant :

        foo.png
        dir1/bar.png
        dir2/baz.png

1. Dans l’**Explorateur de solutions** de Visual Studio, développez le dossier **Vues**, cliquez avec le bouton droit sur **Objets blob**, puis sélectionnez **Ajouter -> Vue** dans le menu contextuel.

1. Dans la boîte de dialogue **Ajouter une vue**, entrez **ListBlobs** pour le nom de la vue, puis sélectionnez **Ajouter**.

1. Ouvrez le fichier `ListBlobs.cshtml` et modifiez-le pour qu’il se présente comme l'extrait de code suivant :

    ```html
    @model List<string>
    @{
        ViewBag.Title = "List blobs";
    }
    
    <h2>List blobs</h2>
    
    <ul>
        @foreach (var item in Model)
        {
        <li>@item</li>
        }
    </ul>
    ```

1. Dans l’**Explorateur de solutions**, développez le dossier **Vues -> Partagé** et ouvrez le fichier `_Layout.cshtml`.

1. Après le dernier élément **Html.ActionLink**, ajoutez l’élément **Html.ActionLink** suivant :

    ```html
    <li>@Html.ActionLink("List blobs", "ListBlobs", "Blobs")</li>
    ```

1. Exécutez l’application, puis sélectionnez **List blobs** (Créer une liste d’objets blob) pour afficher des résultats similaires à la capture d’écran suivante :
  
    ![Création de la liste d’objets blob](./media/vs-storage-aspnet-getting-started-blobs/listblobs.png)

## <a name="download-blobs"></a>Télécharger des objets blob

Cette section montre comment télécharger un objet blob et le conserver dans le stockage local ou lire le contenu dans une chaîne. Les exemples de code font référence au conteneur *test-blob-container* créé dans la section [Création d’un conteneur d’objets blob](#create-a-blob-container).

1. Ouvrez le fichier `BlobsController.cs` .

1. Ajoutez une méthode appelée **DownloadBlob** qui renvoie un objet **ActionResult**.

    ```csharp
    public EmptyResult DownloadBlob()
    {
        // The code in this section goes here.

        return new EmptyResult();
    }
    ```
 
1. Dans la méthode **DownloadBlob**, ajoutez un objet **CloudStorageAccount** qui représente les informations de votre compte de stockage. Le code suivant permet d'obtenir la chaîne de connexion de stockage et les informations de compte de stockage à partir de la configuration du service Azure : (Remplacez *&lt;storage-account-name>* par le nom du compte de stockage Azure auquel accéder.)
   
    ```csharp
    CloudStorageAccount storageAccount = CloudStorageAccount.Parse(
       CloudConfigurationManager.GetSetting("<storage-account-name>_AzureStorageConnectionString"));
    ```
   
1. Ajoutez un objet **CloudBlobClient** représentant un client du service BLOB.
   
    ```csharp
    CloudBlobClient blobClient = storageAccount.CreateCloudBlobClient();
    ```

1. Ajoutez un objet **CloudBlobContainer** représentant une référence au nom du conteneur d’objets blob. 
   
    ```csharp
    CloudBlobContainer container = blobClient.GetContainerReference("test-blob-container");
    ```

1. Ajoutez un objet de référence d’objet blob en appelant la méthode **CloudBlobContainer.GetBlockBlobReference** ou **CloudBlobContainer.GetPageBlobReference**. (Remplacez *&lt;blob-name>* par le nom de l’objet blob à télécharger.)

    ```csharp
    CloudBlockBlob blob = container.GetBlockBlobReference(<blob-name>);
    ```

1. Pour télécharger un objet blob, utilisez la méthode **CloudBlockBlob.DownloadToStream** ou **CloudPageBlob.DownloadToStream**, selon le type d’objet blob. L’exemple de code suivant utilise la méthode **CloudBlockBlob.DownloadToStream** pour transférer le contenu d’un objet blob vers un objet de flux qui est ensuite conservé dans un fichier local. (Remplacez *&lt;local-file-name>* par le nom de fichier complet représentant l’emplacement à utiliser pour le téléchargement de l’objet blob.) 

    ```csharp
    using (var fileStream = System.IO.File.OpenWrite(<local-file-name>))
    {
        blob.DownloadToStream(fileStream);
    }
    ```

1. Dans l’**Explorateur de solutions**, développez le dossier **Vues -> Partagé** et ouvrez le fichier `_Layout.cshtml`.

1. Après le dernier élément **Html.ActionLink**, ajoutez l’élément **Html.ActionLink** suivant :

    ```html
    <li>@Html.ActionLink("Download blob", "DownloadBlob", "Blobs")</li>
    ```

1. Exécutez l’application, puis sélectionnez **Download blob** (Télécharger l’objet blob) pour télécharger l’objet blob. L’objet blob spécifié dans la méthode **CloudBlobContainer.GetBlockBlobReference** est téléchargé à l’emplacement indiqué dans l’appel de méthode **File.OpenWrite**. 

## <a name="delete-blobs"></a>Suppression d’objets blob

Les étapes suivantes montrent comment supprimer un objet blob :

> [!NOTE]
> 
> Le code de cette section part du principe que vous avez terminé les étapes décrites dans la section [Configuration de l’environnement de développement](#set-up-the-development-environment). 

1. Ouvrez le fichier `BlobsController.cs` .

1. Ajoutez une méthode appelée **DeleteBlob** qui renvoie un objet **ActionResult**.

    ```csharp
    public EmptyResult DeleteBlob()
    {
        // The code in this section goes here.

        return new EmptyResult();
    }
    ```

1. Obtenez un objet **CloudStorageAccount** qui représente les informations de votre compte de stockage. Le code suivant permet d'obtenir la chaîne de connexion de stockage et les informations de compte de stockage à partir de la configuration du service Azure : (Remplacez *&lt;storage-account-name>* par le nom du compte de stockage Azure auquel accéder.)
   
    ```csharp
    CloudStorageAccount storageAccount = CloudStorageAccount.Parse(
       CloudConfigurationManager.GetSetting("<storage-account-name>_AzureStorageConnectionString"));
    ```
   
1. Ajoutez un objet **CloudBlobClient** représentant un client du service BLOB.
   
    ```csharp
    CloudBlobClient blobClient = storageAccount.CreateCloudBlobClient();
    ```

1. Ajoutez un objet **CloudBlobContainer** représentant une référence au nom du conteneur d’objets blob. 
   
    ```csharp
    CloudBlobContainer container = blobClient.GetContainerReference("test-blob-container");
    ```

1. Ajoutez un objet de référence d’objet blob en appelant la méthode **CloudBlobContainer.GetBlockBlobReference** ou **CloudBlobContainer.GetPageBlobReference**. (Remplacez *&lt;blob-name>* par le nom de l’objet blob à supprimer.)

    ```csharp
    CloudBlockBlob blob = container.GetBlockBlobReference(<blob-name>);
        ```

1. To delete a blob, use the **Delete** method.

    ```csharp
    blob.Delete();
    ```

1. Dans l’**Explorateur de solutions**, développez le dossier **Vues -> Partagé** et ouvrez le fichier `_Layout.cshtml`.

1. Après le dernier élément **Html.ActionLink**, ajoutez l’élément **Html.ActionLink** suivant :

    ```html
    <li>@Html.ActionLink("Delete blob", "DeleteBlob", "Blobs")</li>
    ```

1. Exécutez l’application, puis sélectionnez **Delete blob** (Supprimer l’objet blob) pour supprimer l’objet blob spécifié dans l’appel de méthode **CloudBlobContainer.GetBlockBlobReference**. 

## <a name="next-steps"></a>Étapes suivantes
Pour plus d’informations sur les autres options de stockage de données dans Azure, consultez d’autres guides de fonctionnalités.

  * [Prise en main du stockage de tables Azure et des services connectés de Visual Studio (ASP.NET)](./vs-storage-aspnet-getting-started-tables.md)
  * [Prise en main du stockage de files d’attente Azure et des services connectés de Visual Studio (ASP.NET)](./vs-storage-aspnet-getting-started-queues.md)

