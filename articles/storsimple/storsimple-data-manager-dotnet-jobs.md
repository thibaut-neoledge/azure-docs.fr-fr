---
title: "Utiliser le kit SDK .NET pour les travaux Microsoft Azure StorSimple Data Manager | Microsoft Docs"
description: "Découvrez comment utiliser le Kit SDK .NET pour lancer les travaux StorSimple Data Manager (version préliminaire privée)"
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
ms.date: 11/22/2016
ms.author: vidarmsft
ms.openlocfilehash: 44d243a034b20b99faf284c8615e470bc6f9d020
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 10/11/2017
---
# <a name="use-the-net-sdk-to-initiate-data-transformation-private-preview"></a>Utiliser le kit SDK .NET pour initier la transformation des données (préversion privée)

## <a name="overview"></a>Vue d'ensemble

Cet article vous explique comment utiliser la fonctionnalité de transformation des données au sein du service StorSimple Data Manager afin de transformer les données de l’appareil StorSimple. Les données transformées sont ensuite utilisées par d’autres services Azure dans le cloud. L’article comporte également une procédure pas à pas pour créer un exemple d’application console .NET pour initier un travail de transformation de données et effectuer son suivi.

## <a name="prerequisites"></a>Prérequis

Avant de commencer, assurez-vous de satisfaire les exigences suivantes :
*   Un système avec Visual Studio 2012, 2013, 2015 ou 2017 installé.
*   Azure Powershell installé. [Téléchargez Azure Powershell](https://azure.microsoft.com/documentation/articles/powershell-install-configure/).
*   Les paramètres de configuration pour initialiser le travail de transformation des données (les instructions pour obtenir ces paramètres sont disponibles ici).
*   Une définition de travail qui a été configurée correctement dans une ressource de données hybride dans un groupe de ressources.
*   Toutes les DLL requises. Téléchargez ces DLL à partir du [dépôt GitHub](https://github.com/Azure-Samples/storsimple-dotnet-data-manager-get-started/tree/master/Data_Manager_Job_Run/dlls).
*   `Get-ConfigurationParams.ps1` Le [script](https://github.com/Azure-Samples/storsimple-dotnet-data-manager-get-started/blob/master/Data_Manager_Job_Run/Get-ConfigurationParams.ps1) du dépôt GitHub.

## <a name="step-by-step"></a>Procédure pas à pas

Procédez comme suit pour utiliser .NET pour lancer un travail de transformation de données.

1. Pour récupérer les paramètres de configuration, procédez comme suit :
    1. Téléchargez l’élément `Get-ConfigurationParams.ps1` à partir du script du dépôt GitHub, dans l’emplacement `C:\DataTransformation`.
    1. Exécutez le script `Get-ConfigurationParams.ps1` à partir du dépôt GitHub. Tapez la commande suivante :

        ```
        C:\DataTransformation\Get-ConfigurationParams.ps1 -SubscriptionName "AzureSubscriptionName" -ActiveDirectoryKey "AnyRandomPassword" -AppName "ApplicationName"
         ```
        Vous pouvez saisir les valeurs de votre choix pour les éléments ActiveDirectoryKey et AppName.


2. Ce script génère les valeurs suivantes :
    * ID client
    * ID client
    * Clé Active Directory (identique à celle entrée ci-dessus)
    * Identifiant d’abonnement

3. À l'aide de Visual Studio 2012, 2013 ou 2015, créez une application console Visual C# .NET.

    1. Lancez **Visual Studio 2012/2013/2015**.
    1. Cliquez sur **Fichier**, pointez le curseur de la souris sur **Nouveau**, puis cliquez sur **Projet**.
    2. Développez **Modèles**, puis sélectionnez **Visual C#**.
    3. Sélectionnez **Application console** dans la liste des types de projet située sur la droite.
    4. Saisissez **DataTransformationApp** pour le **Nom**.
    5. Sélectionnez **C:\DataTransformation** pour l’**Emplacement**.
    6. Cliquez sur **OK** pour créer le projet.

4.  Maintenant, ajoutez l’ensemble des DLL présentes dans le dossier [dédié](https://github.com/Azure-Samples/storsimple-dotnet-data-manager-get-started/tree/master/Data_Manager_Job_Run/dlls) en tant que **Références**dans le projet que vous avez créé. Pour télécharger les fichiers DLL, procédez comme suit :

    1. Dans Visual Studio, accédez à **Affichage > Explorateur de solutions**.
    1. Cliquez sur la flèche à gauche du projet de l’application de transformation des données. Cliquez sur **Références**, puis cliquez avec le bouton droit sur **Ajouter une référence**.
    2. Accédez à l’emplacement du dossier des packages, sélectionnez l’ensemble des DLL, puis cliquez sur **Ajouter** et sur **OK**.

5. Ajoutez les instructions **using** suivantes au fichier source (Program.cs) dans le projet.

    ```
    using System;
    using System.Collections.Generic;
    using System.Threading;
    using Microsoft.Azure.Management.HybridData.Models;
    using Microsoft.Internal.Dms.DmsWebJob;
    using Microsoft.Internal.Dms.DmsWebJob.Contracts;
    ```


6. Le code suivant initialise l’instance du travail de transformation de données. Add this in the **Méthode Main**. Remplacez les valeurs des paramètres de configuration obtenues précédemment. Insérez les valeurs de **Nom du groupe de ressources** et **Nom de la ressource de données hybride**. Le **Nom du groupe de ressources**est celui qui héberge la ressource de données hybride sur laquelle a été configurée la définition de travail.

    ```
    // Setup the configuration parameters.
    var configParams = new ConfigurationParams
    {
        ClientId = "client-id",
        TenantId = "tenant-id",
        ActiveDirectoryKey = "active-directory-key",
        SubscriptionId = "subscription-id",
        ResourceGroupName = "resource-group-name",
        ResourceName = "resource-name"
    };

    // Initialize the Data Transformation Job instance.
    DataTransformationJob dataTransformationJob = new DataTransformationJob(configParams);

    ```

7. Spécifiez les paramètres avec lesquels exécuter la définition de travail

    ```
    string jobDefinitionName = "job-definition-name";

    DataTransformationInput dataTransformationInput = dataTransformationJob.GetJobDefinitionParameters(jobDefinitionName);

    ```

    (OU)

    Si vous souhaitez modifier les paramètres de définition de travail durant l’exécution, ajoutez le code suivant :

    ```
    string jobDefinitionName = "job-definition-name";
    // Must start with a '\'
    var rootDirectories = new List<string> {@"\root"};

    // Name of the volume on the StorSimple device.
    var volumeNames = new List<string> {"volume-name"};

    var dataTransformationInput = new DataTransformationInput
    {
        // If you require the latest existing backup to be picked else use TakeNow to trigger a new backup.
        BackupChoice = BackupChoice.UseExistingLatest.ToString(),
        // Name of the StorSimple device.
        DeviceName = "device-name",
        // Name of the container in Azure storage where the files will be placed after execution.
        ContainerName = "container-name",
        // File name filter (search pattern) to be applied on files under the root directory. * - Match all files.
        FileNameFilter = "*",
        // List of root directories.
        RootDirectories = rootDirectories,
        // Name of the volume on StorSimple device on which the relevant data is present. 
        VolumeNames = volumeNames
    };
    
    ```

8. Après l’initialisation, ajoutez le code suivant pour déclencher un travail de transformation de données sur la définition de travail. Insérez le **Job Definition Name** (Nom de définition de travail).

    ```
    // Trigger a job, retrieve the jobId and the retry interval for polling.
    int retryAfter;
    string jobId = dataTransformationJob.RunJobAsync(jobDefinitionName, 
    dataTransformationInput, out retryAfter);

    ```

9. Ce travail charge les fichiers correspondants présents sous le répertoire racine du volume StorSimple sur le conteneur spécifié. Lorsqu’un fichier est chargé, un message est déposé dans la file d’attente (dans le compte de stockage du conteneur), avec le nom de la définition de travail. Ce message peut être utilisé comme un déclencheur pour initier le traitement ultérieur du fichier.

10. Une fois le travail déclenché, ajoutez le code suivant afin de suivre son exécution.

    ```
    Job jobDetails = null;

    // Poll the job.
    do
    {
        jobDetails = dataTransformationJob.GetJob(jobDefinitionName, jobId);

        // Wait before polling for the status again.
        Thread.Sleep(TimeSpan.FromSeconds(retryAfter));

    } while (jobDetails.Status == JobStatus.InProgress);

    // Completion status of the job.
    Console.WriteLine("JobStatus: {0}", jobDetails.Status);
    
    // To hold the console before exiting.
    Console.Read();

    ```


## <a name="next-steps"></a>Étapes suivantes

[Utilisez l’interface utilisateur de StorSimple Data Manager pour transformer vos données](storsimple-data-manager-ui.md).
