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
ms.sourcegitcommit: 0d8472cb3b0d891d2b184621d62830d1ccd5e2e7
ms.openlocfilehash: 8a06e4b3f482943f55e998c3c68857d3530ff98a
ms.lasthandoff: 03/21/2017

---

# <a name="change-blob-path-from-the-default-private-preview"></a>Modifier le chemin d’accès d’objet blob par défaut (version préliminaire privée)

Cet article décrit comment configurer une fonction Azure pour renommer un chemin d’accès de fichier d’objet blob par défaut. 

## <a name="prerequisites"></a>Composants requis

Avant de commencer, assurez-vous de satisfaire les exigences suivantes :
* Une définition de travail qui a été configurée correctement dans une ressource de données hybride dans un groupe de ressources.

## <a name="create-an-azure-function"></a>Création d’une fonction Azure

Pour créer une fonction Azure, procédez comme suit.

#### <a name="to-create-an-azure-function"></a>Pour créer une fonction Azure

1. Accédez au [portail Azure](http://portal.azure.com/).

2. Cliquez sur **+ Nouveau** dans l’angle supérieur gauche. Tapez « Function App » dans la zone de **recherche** et appuyez sur **Entrée**.

    ![Accéder à la ressource Function App](./media/storsimple-data-manager-change-default-blob-path/goto-function-app-resource.png)

3. Cliquez sur **Function App** dans les résultats.

    ![Sélectionner la ressource Function App](./media/storsimple-data-manager-change-default-blob-path/select-function-app-resource.png)

4. Ouvrez la fenêtre **Function App** et cliquez sur **Créer**.

    ![Créer une Function App](./media/storsimple-data-manager-change-default-blob-path/create-new-function-app.png)

5. Dans le panneau **Configuration**, saisissez toutes les entrées et cliquez sur **Créer**.

    1. Nom de l’application
    2. Abonnement
    3. Groupe de ressources
    4. Plan d’hébergement - **Plan de consommation**
    5. Emplacement
    6. Compte de stockage : utilisez un compte de stockage existant ou créez un nouveau compte de stockage. Un compte de stockage est utilisé en interne pour la fonction.

        ![Entrer les données de configuration de la nouvelle application Function App](./media/storsimple-data-manager-change-default-blob-path/enter-new-funcion-app-data.png)

6. Une fois l’application Function App créée, accédez à **Plus de services >** en bas à gauche. Tapez « App Services » dans la zone de texte **Filtre** et cliquez sur **App Services**.

    ![Plus de services >](./media/storsimple-data-manager-change-default-blob-path/more-services.png)

7. Cliquez sur le **nom d’application de fonctions** dans la liste App Services.

8. Cliquez sur **+ Nouvelle fonction**. Sélectionnez **C#** dans la liste déroulante **Langage**. Sélectionnez l’option **QueueTrigger-CSharp** dans la liste des modèles. Saisissez toutes les entrées.

   1. Nom : entrez un nom pour votre fonction.
   2. Nom de file d’attente : entrez le **nom de la définition du travail de transformation des données**.
   3. Connexion au compte de stockage : cliquez sur l’option **nouveau**. Sélectionnez le compte correspondant au travail Transformation des données.
      
      Notez la valeur de `Connection name`. Ce nom est requis plus loin dans la fonction Azure.

   4. Cliquez sur le bouton **Créer**.

       ![Créer une nouvelle fonction C Sharp >](./media/storsimple-data-manager-change-default-blob-path/create-new-csharp-function.png)

9. Dans la fenêtre **Fonction**, exécutez le fichier _.csx_. Copiez et collez le code suivant :

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
            log.Info($"Blob Uri: {myQueueItem.TargetLocation}");
            CloudStorageAccount storageAccount = CloudStorageAccount.Parse(ConfigurationManager.AppSettings["STORAGE_CONNECTIONNAME"]);

            // Create the blob client.
            CloudBlobClient blobClient = storageAccount.CreateCloudBlobClient();

            string uriString = "windows.net/";
            string containerName = myQueueItem.TargetLocation;
            containerName = containerName.Substring(containerName.IndexOf(uriString) + uriString.Length);
            containerName = containerName.Substring(0, containerName.IndexOf("/"));
            //log.Info($"Container name: {containerName}");

            CloudBlobContainer container = blobClient.GetContainerReference(containerName);

            if(!container.Exists())
            {
                log.Info($"Container - {containerName} not exists");
                return;
            }

            string containerUri = string.Format("{0}/", container.Uri.ToString());  // Reading container Uri
            string blobUri =  myQueueItem.TargetLocation;
            string blobName = blobUri.Replace(containerUri, string.Empty).Replace("%20", " "); // Reading existing file path after container name
            string newBlobName = blobName.Substring(blobName.IndexOf(string.Format("{0}/", container.Name)) + container.Name.Length + 1); // Reading actual File path after container name

            log.Info($"Blob name: {blobName}");
            log.Info($"New blob name: {newBlobName}");

            CloudBlockBlob blobCopy = container.GetBlockBlobReference(newBlobName);
            CloudBlockBlob blob = container.GetBlockBlobReference(blobName);
            if (!blobCopy.Exists())
            {
                blobCopy.StartCopy(blob);
                // Delete old blob, after copy the blob
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

   1. Remplacez la valeur **STORAGE_CONNECTIONNAME** exacte de la ligne 12 par la connexion de votre compte de stockage (point de référence 8c).
   2. Cliquez sur le bouton **Enregistrer** en haut à gauche.

       ![Enregistrer la fonction >](./media/storsimple-data-manager-change-default-blob-path/save-function.png)

10.  Cliquez sur **Afficher les fichiers** dans l’angle supérieur droit.

    ![Afficher les fichiers](./media/storsimple-data-manager-change-default-blob-path/view-files.png)

   1. Cliquez sur **+ Ajouter**. Tapez **project.json** et appuyez sur **Entrée**.
   2. Copiez et collez le code suivant dans le fichier **project.json**.

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

   2. Cliquez sur **Save**.

Vous avez créé une fonction Azure. Cette fonction est déclenchée à chaque fois qu’un nouvel objet blob est généré par le travail Transformation des données.

## <a name="next-steps"></a>Étapes suivantes

[Utilisez l’interface utilisateur de StorSimple Data Manager pour transformer vos données](storsimple-data-manager-ui.md).

