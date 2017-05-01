---
title: "Modifier le chemin d’accès d’objet blob par défaut | Microsoft Docs"
description: "Découvrez comment configurer une fonction Azure pour renommer un chemin d’accès de fichier d’objet blob (version préliminaire privée)"
services: storsimple
documentationcenter: NA
author: vidarmsft
manager: syadav
editor: 
ms.assetid: 
ms.service: storsimple
ms.devlang: NA
ms.topic: article
ms.tgt_pltfrm: NA
ms.workload: TBD
ms.date: 03/16/2017
ms.author: vidarmsft
translationtype: Human Translation
ms.sourcegitcommit: 785d3a8920d48e11e80048665e9866f16c514cf7
ms.openlocfilehash: 057d4d7370207859617eb63238bf425bfa6d3e16
ms.lasthandoff: 04/12/2017

---

# <a name="change-a-blob-path-from-the-default-path-private-preview"></a>Modifier le chemin d’accès d’objet blob par défaut (version préliminaire privée)

Cet article décrit comment configurer une fonction Azure pour renommer un chemin d’accès de fichier d’objet blob par défaut. 

## <a name="prerequisites"></a>Composants requis

Vérifiez que vous disposez d’une définition de travail qui a été configurée correctement dans une ressource de données hybride dans un groupe de ressources.

## <a name="create-an-azure-function"></a>Création d’une fonction Azure

Pour créer une fonction Azure, procédez comme suit :

1. Accédez au [portail Azure](http://portal.azure.com/).

2. En haut du panneau gauche, cliquez sur **Nouveau**. 

3. Dans la zone de **recherche**, saisissez **Function App**, puis appuyez sur Entrée.

    ![Saisissez « Function App » dans la zone de recherche](./media/storsimple-data-manager-change-default-blob-path/goto-function-app-resource.png)

4. Dans la liste **Résultats**, cliquez sur **Function App**.

    ![Sélectionnez la ressource Function App dans la liste des résultats](./media/storsimple-data-manager-change-default-blob-path/select-function-app-resource.png)

    La fenêtre **Function App** s’ouvre.

5. Cliquez sur **Create**.

    ![Bouton « Créer » de la fenêtre Function App](./media/storsimple-data-manager-change-default-blob-path/create-new-function-app.png)

6. Dans le panneau de configuration **Function App**, procédez comme suit :

    a. Dans la zone **Nom de l’application**, saisissez le nom de l’application.
    
    b. Dans la zone **Abonnement**, saisissez le nom de l’abonnement.

    c. Sous **Groupe de ressources**, cliquez sur **Créer un nouveau** et saisissez le nom du groupe de ressources.

    d. Dans la zone **Plan d’hébergement**, saisissez **Plan de consommation**.

    e. Dans la zone **Emplacement**, saisissez l’emplacement.

    f. Sous **Compte de stockage**, sélectionnez un compte de stockage existant ou créez un nouveau compte de stockage. Un compte de stockage est utilisé en interne pour la fonction.

    ![Entrer les données de configuration de la nouvelle application Function App](./media/storsimple-data-manager-change-default-blob-path/enter-new-funcion-app-data.png)

7. Cliquez sur **Create**.  
    L’application Function App est créée.

8. Dans le volet gauche, cliquez sur **Plus de services**, puis procédez comme suit :
    
    a. Dans la zone **Filtre**, saisissez **App Services**.
    
    b. Cliquez sur **App Services**. 

    ![Lien « Plus de services » dans le volet gauche](./media/storsimple-data-manager-change-default-blob-path/more-services.png)

9. Dans la liste des services d’application, cliquez sur le nom de l’application Function App.  
    La page de l’application Function App s’ouvre.

10. Dans le volet gauche, cliquez sur **Nouvelle fonction**, puis procédez comme suit : 

    a. Dans la liste **Langage**, sélectionnez **C#**.
    
    b. Dans le tableau de vignettes de modèle, sélectionnez **QueueTrigger-CSharp**.

    c. Dans la zone **Nommez votre fonction**, saisissez un nom pour la fonction.

    d. Dans la zone **Nom de file d’attente**, saisissez le nom de la définition du travail de transformation des données.

    e. Sous **Connexion au compte de stockage**, cliquez sur **nouveau**, puis sélectionnez le compte correspondant au travail de transformation des données.  
        Notez le nom de la connexion. Ce nom est requis plus loin dans la fonction Azure.

       ![Créer une fonction C#](./media/storsimple-data-manager-change-default-blob-path/create-new-csharp-function.png)

    f. Cliquez sur **Create**.  
    La fenêtre **Function** s’ouvre.

11. Dans la fenêtre **Function**, exécutez le fichier _.csx_, puis procédez comme suit :

    a. Collez le code suivant :

    ```
    using System;
    using System.Configuration;
    using Microsoft.WindowsAzure.Storage.Blob;
    using Microsoft.WindowsAzure.Storage.Queue;
    using Microsoft.WindowsAzure.Storage;
    using System.Collections.Generic;
    using System.Linq;

    public static void Run(QueueItem myQueueItem, TraceWriter log)
    {
        CloudStorageAccount storageAccount = CloudStorageAccount.Parse(ConfigurationManager.AppSettings["STORAGE_CONNECTIONNAME"]);

        string storageAccUriEndswith = "windows.net/";
        string uri = myQueueItem.TargetLocation.Replace("%20", " ");
        log.Info($"Blob Uri: {uri}");

        // Remove storage account uri string
        uri = uri.Substring(uri.IndexOf(storageAccUriEndswith) + storageAccUriEndswith.Length);

        string containerName = uri.Substring(0, uri.IndexOf("/")); 

        // Remove container name string
        uri = uri.Substring(containerName.Length + 1);

        // Current blob path
        string blobName = uri; 

        string volumeName = uri.Substring(containerName.Length + 1);
        volumeName = uri.Substring(0, uri.IndexOf("/"));

        // Remove volume name string
        uri = uri.Substring(volumeName.Length + 1);

        string newContainerName = uri.Substring(0, uri.IndexOf("/")).ToLower();
        string newBlobName = uri.Substring(newContainerName.Length + 1);

        log.Info($"Container name: {containerName}");
        log.Info($"Volume name: {volumeName}");
        log.Info($"New container name: {newContainerName}");

        log.Info($"Blob name: {blobName}");
        log.Info($"New blob name: {newBlobName}");

        // Create the blob client.
        CloudBlobClient blobClient = storageAccount.CreateCloudBlobClient();

        // Container reference
        CloudBlobContainer container = blobClient.GetContainerReference(containerName);
        CloudBlobContainer newContainer = blobClient.GetContainerReference(newContainerName);
        newContainer.CreateIfNotExists();

        if(!container.Exists())
        {
            log.Info($"Container - {containerName} not exists");
            return;
        }

        if(!newContainer.Exists())
        {
            log.Info($"Container - {newContainerName} not exists");
            return;
        }

        CloudBlockBlob blob = container.GetBlockBlobReference(blobName);
        if (!blob.Exists())
        {
            // Skip to copy the blob to new container, if source blob doesn't exist
            log.Info($"The specified blob does not exist.");
            log.Info($"Blob Uri: {blob.Uri}");
            return;
        }

        CloudBlockBlob blobCopy = newContainer.GetBlockBlobReference(newBlobName);
        if (!blobCopy.Exists())
        {
            blobCopy.StartCopy(blob);
            // Delete old blob, after copy to new container
            blob.DeleteIfExists();
            log.Info($"Blob file path renamed completed successfully");
        }
        else
        {
            log.Info($"Blob file path renamed already done");
            // Delete old blob, if already exists.
            blob.DeleteIfExists();
        }
    }

    public class QueueItem
    {
        public string SourceLocation {get;set;}
        public long SizeInBytes {get;set;}
        public string Status {get;set;}
        public string JobID {get;set;}
        public string TargetLocation {get; set;}
    }

    ```

    b. Remplacez la valeur **STORAGE_CONNECTIONNAME** de la ligne 11 par la connexion à votre compte de stockage (point de référence 8c).

    c. Cliquez sur **Enregistrer** en haut à gauche.

    ![Enregistrer la fonction](./media/storsimple-data-manager-change-default-blob-path/save-function.png)

12. Pour terminer la fonction, ajoutez un fichier de plus en procédant comme suit :

    a. Cliquez sur **Afficher les fichiers**.

       ![Lien « Afficher les fichiers »](./media/storsimple-data-manager-change-default-blob-path/view-files.png)

    b. Cliquez sur **Add**.
    
    c. Saisissez **project.json** et appuyez sur Entrée.
    
    d. Collez le code suivant dans le fichier **project.json** :

    ```
    {
    "frameworks": {
        "net46":{
        "dependencies": {
            "windowsazure.storage": "8.1.1"
        }
        }
    }
    }

    ```

    e. Cliquez sur **Enregistrer**.

Vous avez créé une fonction Azure. Cette fonction est déclenchée à chaque fois qu’un nouvel objet blob est généré par le travail de transformation des données.

## <a name="next-steps"></a>Étapes suivantes

[Utilisez l’interface utilisateur de StorSimple Data Manager pour transformer vos données](storsimple-data-manager-ui.md)

