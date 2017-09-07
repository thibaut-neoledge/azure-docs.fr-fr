---
title: "Traiter des jeux de données volumineux à l’aide de Data Factory et Batch | Microsoft Docs"
description: "Décrit comment traiter de grandes quantités de données dans un pipeline Azure Data Factory en utilisant une capacité de traitement parallèle d’Azure Batch."
services: data-factory
documentationcenter: 
author: spelluru
manager: jhubbard
editor: monicar
ms.assetid: 688b964b-51d0-4faa-91a7-26c7e3150868
ms.service: data-factory
ms.workload: data-services
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 06/19/2017
ms.author: spelluru
ms.translationtype: HT
ms.sourcegitcommit: 83f19cfdff37ce4bb03eae4d8d69ba3cbcdc42f3
ms.openlocfilehash: 9defbf7a6a515740fa3b3cb1c67a2f5f9d9baa01
ms.contentlocale: fr-fr
ms.lasthandoff: 08/21/2017

---
# <a name="process-large-scale-datasets-using-data-factory-and-batch"></a>Traiter des jeux de données volumineux à l’aide de Data Factory et Batch
Cet article décrit une architecture d’un exemple de solution qui déplace et traite des jeux de données volumineux de manière automatique et planifiée. Il fournit également une procédure de bout en bout pour implémenter la solution à l’aide d’Azure Data Factory et d’Azure Batch.

Cet article est plus long que nos articles habituels car il contient une procédure pas à pas d’un exemple de solution complète. Si vous débutez avec Batch et Data Factory, vous découvrirez différentes informations sur ces services et comment ils fonctionnent ensemble. Si vous connaissez déjà ces services et que vous concevez/élaborez une solution, vous pouvez vous concentrer uniquement sur la [section architecture](#architecture-of-sample-solution) de l’article. Si vous développez un prototype ou une solution, vous pouvez également suivre nos instructions détaillées dans la [procédure pas à pas](#implementation-of-sample-solution). N’hésitez pas à nous faire part de vos commentaires sur ce contenu et son utilisation.

Tout d’abord, découvrons de quelle manière les services Data Factory et Batch permettent de traiter des jeux de données volumineux dans le cloud.     

## <a name="why-azure-batch"></a>Pourquoi Azure Batch ?
Azure Batch vous permet d’exécuter efficacement dans le cloud des applications de calcul haute performance (HPC) en parallèle et à grande échelle. Ce service de plateforme planifie les travaux nécessitant une grande quantité de ressources système à exécuter sur une collection gérée de machines virtuelles. Il peut mettre automatiquement à l’échelle les ressources de calcul pour répondre aux besoins du travail.

Avec le service Batch, vous définissez des ressources de calcul Azure pour exécuter vos applications en parallèle et à grande échelle. Vous pouvez exécuter des travaux à la demande ou selon un calendrier précis, sans avoir à créer, configurer et gérer manuellement un cluster HPC, des machines virtuelles individuelles, des réseaux virtuels ou une infrastructure complexe de planification des tâches et des travaux.

Si vous ne connaissez pas Azure Batch, consultez les articles suivants qui vous aideront à comprendre l’architecture/l’implémentation de la solution décrite dans cet article.   

* [Notions de base d’Azure Batch](../batch/batch-technical-overview.md)
* [Aperçu des fonctionnalités d’Azure Batch](../batch/batch-api-basics.md)

(facultatif) Pour en savoir plus sur Azure Batch, voir [Parcours d’apprentissage pour Azure Batch](https://azure.microsoft.com/documentation/learning-paths/batch/).

## <a name="why-azure-data-factory"></a>Pourquoi Azure Data Factory ?
Data Factory est un service d’intégration de données dans le cloud qui gère et automatise le déplacement et la transformation des données. Grâce au service Data Factory, vous pouvez créer des pipelines de données gérés pour déplacer les données des magasins de données locaux et cloud vers un magasin de données centralisé (par exemple : le stockage d’objets blob Azure) et traiter/transformer les données à l’aide de services tels que Azure HDInsight et Azure Machine Learning. Vous pouvez également configurer les pipelines de données pour qu’ils s’exécutent de manière programmée (toutes les heures, tous les jours, toutes les semaines, etc.), mais aussi les surveiller et les gérer en un coup d’œil afin d’identifier les problèmes et de prendre les mesures adéquates.

Si vous ne connaissez pas Azure Data Factory, consultez les articles suivants qui vous aideront à comprendre l’architecture/l’implémentation de la solution décrite dans cet article.  

* [Présentation d’Azure Data Factory](data-factory-introduction.md)
* [Générer votre premier pipeline de données](data-factory-build-your-first-pipeline.md)   

(facultatif) Pour en savoir plus sur Azure Data Factory, voir [Parcours d’apprentissage Azure Data Factory](https://azure.microsoft.com/documentation/learning-paths/data-factory/).

## <a name="data-factory-and-batch-together"></a>Intégration de Data Factory et Batch
Data Factory comprend des activités intégrées telles que l’activité de copie pour copier/déplacer des données à partir d’un magasin de données source vers un magasin de données de destination et l’activité Hive pour traiter les données à l’aide de clusters Hadoop (HDInsight) dans Azure. Consultez l’article [Activités de transformation des données](data-factory-data-transformation-activities.md) pour obtenir la liste des activités de transformation prises en charge.

Il vous permet également de créer des activités .NET personnalisées pour déplacer ou traiter les données selon votre propre logique et d’exécuter ces activités dans un cluster Azure HDInsight ou dans un pool de machines virtuelles Azure Batch. Lorsque vous utilisez Azure Batch, vous pouvez configurer la mise à l’échelle automatique du pool (ajouter ou supprimer des machines virtuelles en fonction de la charge de travail) selon une formule que vous définissez.     

## <a name="architecture-of-sample-solution"></a>Architecture de l’exemple de solution
Même si l’architecture décrite dans cet article est associée à une solution simple, elle s’applique à des scénarios complexes, tels que la modélisation des risques par les services financiers, le traitement et la restitution d’images, ou encore l’analyse génomique.

Le diagramme illustre 1) la manière dont Data Factory orchestre le déplacement et le traitement des données, et (2) la manière dont Azure Batch traite les données en parallèle. Téléchargez et imprimez le diagramme pour le consulter facilement (11 x 17 pouces ou format A3) : [Calcul haute performance et orchestration de données à l’aide des services Azure Batch et Data Factory](http://go.microsoft.com/fwlink/?LinkId=717686).

[![Diagramme de traitement des données à grande échelle](./media/data-factory-data-processing-using-batch/image1.png)](http://go.microsoft.com/fwlink/?LinkId=717686)

La liste suivante fournit les étapes de base du processus. La solution inclut du code et des explications relatives à la génération de la solution de bout en bout.

1. **Configurez Azure Batch avec un pool de nœuds de calcul (machines virtuelles)**. Vous pouvez spécifier le nombre de nœuds et la taille de chacun d’eux.
2. **Créez une instance Azure Data Factory** configurée avec des entités qui représentent respectivement le stockage d’objets blob Azure, le service de calcul Azure Batch, les données en entrée et sortie, ainsi qu’un flux de travail, ou pipeline, d’activités qui déplacent et transforment des données.
3. **Créez une activité .NET personnalisée dans le pipeline Data Factory**. L’activité est votre code utilisateur qui s’exécute sur le pool Azure Batch.
4. **Stockez de grandes quantités de données d’entrée en tant qu’objets blob dans Azure Storage**. Les données sont divisées en tranches logiques (généralement basées sur l’heure).
5. **Data Factory copie vers l’emplacement secondaire les données qui sont traitées en parallèle**.
6. **Data Factory exécute l’activité personnalisée à l’aide du pool alloué par Batch**. Data Factory peut exécuter plusieurs activités simultanément. Chaque activité traite une tranche de données. Les résultats sont stockés dans Azure Storage.
7. **Data Factory déplace les résultats finaux vers un troisième emplacement**, soit pour les distribuer via une application, soit pour les traiter avec d’autres outils.

## <a name="implementation-of-sample-solution"></a>Implémentation de l’exemple de solution
L’exemple de solution est volontairement simple et a pour objectif de vous montrer comment utiliser conjointement les services Data Factory et Batch pour traiter des jeux de données. La solution compte le nombre d’occurrences d’un terme de recherche (« Microsoft ») dans les fichiers d’entrée organisés en série chronologique. Il renvoie le nombre de fichiers de sortie.

**Temps** : si vous maîtrisez Azure, Data Factory et Batch et que vous disposez des composants requis listés ci-dessous, cette solution devrait vous prendre entre 1 et 2 heures.

### <a name="prerequisites"></a>Composants requis
#### <a name="azure-subscription"></a>Abonnement Azure
Si vous n’êtes pas abonné, vous pouvez créer un compte d’essai gratuit en quelques minutes. Voir [essai gratuit](https://azure.microsoft.com/pricing/free-trial/).

#### <a name="azure-storage-account"></a>Compte Azure Storage
Dans ce didacticiel, vous utilisez un compte de stockage Azure pour stocker des données. Si vous ne possédez pas de compte de stockage Azure, voir [Création d’un compte de stockage](../storage/common/storage-create-storage-account.md#create-a-storage-account). L’exemple de solution utilise un stockage d’objets blob.

#### <a name="azure-batch-account"></a>Compte Azure Batch
Créez un compte Azure Batch via le [portail Azure](http://manage.windowsazure.com/). Voir [Créer et gérer un compte Azure Batch](../batch/batch-account-create-portal.md). Notez la clé et le nom du compte Azure Batch. Vous pouvez également créer un compte Azure Batch à l’aide de l’applet de commande [New-AzureRmBatchAccount](https://msdn.microsoft.com/library/mt603749.aspx) . Pour obtenir des instructions détaillées sur l’utilisation de cette applet de commande, voir [Prise en main des applets de commande Azure Batch PowerShell](../batch/batch-powershell-cmdlets-get-started.md) .

L’exemple de solution utilise Azure Batch (indirectement via un pipeline Azure Data Factory) pour traiter des données en parallèle sur un pool de nœuds de calcul (une collection gérée de machines virtuelles).

#### <a name="azure-batch-pool-of-virtual-machines-vms"></a>Pool de machines virtuelles Azure Batch
Créez un **pool Azure Batch** comprenant au moins 2 nœuds de calcul.

1. Dans le [portail Azure](https://portal.azure.com), cliquez sur **Parcourir** dans le menu de gauche, puis cliquez sur **Comptes Batch**.
2. Sélectionnez votre compte Azure Batch pour ouvrir le panneau **Compte Batch** .
3. Cliquez sur la vignette **Pools** .
4. Dans le panneau **Pools** , cliquez sur le bouton Ajouter de la barre d’outils pour ajouter un pool.
   1. Entrez un ID pour le pool (**ID du pool**). Notez **l’ID du pool**, car vous en aurez besoin lors de la création de la solution Data Factory.
   2. Spécifiez **Windows Server 2012 R2** pour le paramètre de famille du système d’exploitation.
   3. Sélectionnez le **niveau tarifaire du nœud**.
   4. Entrez **2** comme valeur du paramètre **Quantité dédiée cible**.
   5. Entrez **2** comme valeur du paramètre **Nombre maximal de tâches par nœud**.
   6. Cliquez sur **OK** pour créer le pool.

#### <a name="azure-storage-explorer"></a>Azure Storage Explorer
[Azure Storage Explorer 6 (outil)](https://azurestorageexplorer.codeplex.com/) ou [CloudXplorer](http://clumsyleaf.com/products/cloudxplorer) (à partir du logiciel ClumsyLeaf). Vous utilisez ces outils pour consulter et modifier les données de vos projets Stockage Azure, notamment les journaux de vos applications hébergées dans le cloud.

1. Créez un conteneur nommé **mycontainer** avec un accès privé (par d’accès anonyme).
2. Si vous utilisez **CloudXplorer**, créez des dossiers et sous-dossiers avec la structure suivante :

   ![](./media/data-factory-data-processing-using-batch/image3.png)

   `Inputfolder` et `outputfolder` sont des dossiers de niveau supérieur de `mycontainer`. Le dossier `inputfolder` contient des sous-dossiers horodatés (AAAA-MM-JJ-HH).

   Si vous utilisez **l’Explorateur de stockage Azure**, à l’étape suivante, vous devez charger les fichiers nommés `inputfolder/2015-11-16-00/file.txt`, `inputfolder/2015-11-16-01/file.txt` et ainsi de suite. Cette étape crée automatiquement les dossiers.
3. Créez sur votre ordinateur un fichier texte **file.txt** contenant le mot clé **Microsoft**. Par exemple, « activité de test personnalisé Microsoft ».
4. Chargez le fichier dans les dossiers d’entrée suivants du stockage d’objets blob Azure.

   ![](./media/data-factory-data-processing-using-batch/image4.png)

   Si vous utilisez **l’Explorateur de stockage Azure**, chargez le fichier **file.txt** dans **mycontainer**. Cliquez sur **Copy** (Copier) dans la barre d’outils pour créer une copie de l’objet blob. Dans la boîte de dialogue **Copy Blob** (Copie de l’objet blob), remplacez le **nom d’objet blob de destination** par `inputfolder/2015-11-16-00/file.txt`. Répétez cette étape pour créer `inputfolder/2015-11-16-01/file.txt`, `inputfolder/2015-11-16-02/file.txt`, `inputfolder/2015-11-16-03/file.txt`, `inputfolder/2015-11-16-04/file.txt` et ainsi de suite. Cette action crée automatiquement les dossiers.
5. Créez un autre conteneur nommé : `customactivitycontainer`. Vous chargez le fichier zip d’activité personnalisée dans ce conteneur.

#### <a name="visual-studio"></a>Visual Studio
Installez Microsoft Visual Studio 2012 ou version ultérieure pour créer l’activité Batch personnalisée à utiliser dans la solution Data Factory.

### <a name="high-level-steps-to-create-the-solution"></a>Principales étapes pour créer la solution
1. Créez une activité personnalisée contenant la logique de traitement des données.
2. Créez une fabrique de données Azure qui utilise l’activité personnalisée :

### <a name="create-the-custom-activity"></a>Création de l’activité personnalisée
L’activité personnalisée de Data Factory est au cœur de cet exemple de solution. L’exemple de solution utilise Azure Batch pour exécuter l’activité personnalisée. Pour les informations de base relatives au développement d’activités personnalisées et leur utilisation dans des pipelines Azure Data Factory, voir [Utilisation des activités personnalisées dans un pipeline Azure Data Factory](data-factory-use-custom-activities.md) .

Pour créer une activité personnalisée .NET utilisable dans un pipeline Azure Data Factory, vous devez créer un projet de **bibliothèque de classes .NET** contenant une classe qui implémente cette interface **IDotNetActivity**. Cette interface possède une seule méthode : **Execute**. Voici la signature de la méthode :

```csharp
public IDictionary<string, string> Execute(
            IEnumerable<LinkedService> linkedServices,
            IEnumerable<Dataset> datasets,
            Activity activity,
            IActivityLogger logger)
```

La méthode comporte quelques composants clés qu’il est important d’assimiler.

* La méthode accepte quatre paramètres :

  1. **linkedServices**. Liste énumérable de services liés qui relie les sources de données d’entrée/sortie (par exemple, Stockage Blob Azure) à la fabrique de données. Dans cet exemple, il s’agit du seul service lié de type Azure Storage utilisé à la fois pour les données d’entrée et de sortie.
  2. **jeux de données**. liste énumérable de jeux de données. Vous pouvez utiliser ce paramètre pour obtenir les emplacements et les schémas définis par les jeux de données d’entrée et de sortie.
  3. **activity**. ce paramètre représente l’entité de calcul actuelle (dans ce cas, un service Azure Batch).
  4. **logger**. L’enregistreur vous permet d’écrire des commentaires de débogage qui apparaîtront en tant que journal « utilisateur » pour le pipeline.
* La méthode retourne un dictionnaire qui peut être utilisé pour enchaîner ultérieurement des activités personnalisées. Cette fonctionnalité n’étant pas encore implémentée, seul un dictionnaire vide est retourné par la méthode.

#### <a name="procedure-create-the-custom-activity"></a>Procédure : Création de l’activité personnalisée
1. Créez un projet de bibliothèque de classes .NET dans Visual Studio 2013.

   1. Lancez **Visual Studio 2012**/**2013/2015**.
   2. Cliquez sur **Fichier**, pointez le curseur de la souris sur **Nouveau**, puis cliquez sur **Projet**.
   3. Développez **Modèles**, puis sélectionnez **Visual C\#**. Dans cette procédure pas à pas, vous utilisez C\#, mais vous pouvez utiliser un autre langage .NET pour développer l’activité personnalisée.
   4. Sélectionnez **Bibliothèque de classes** dans la liste des types de projet, sur la droite.
   5. Entrez **MyDotNetActivity** for the **Nom**.
   6. Sélectionnez **C:\\ADF** comme **emplacement**. Créez le dossier **ADF** s’il n’existe pas.
   7. Cliquez sur **OK** pour créer le projet.
2. Cliquez sur **Outils**, pointez le curseur de la souris sur **Gestionnaire de package NuGet**, puis cliquez sur **Console du gestionnaire de package**.
3. Dans la **Console du gestionnaire de package**, exécutez la commande suivante pour importer l’élément **Microsoft.Azure.Management.DataFactories**.

    ```powershell
    Install-Package Microsoft.Azure.Management.DataFactories
    ```
4. Importez le package NuGet **Azure Storage** dans le projet. Vous en avez besoin, car vous utilisez l’API de stockage d’objets Blob dans cet exemple.

    ```powershell
    Install-Package Azure.Storage
    ```
5. Ajoutez les instructions **using** suivantes au fichier source du projet.

    ```csharp
    using System.IO;
    using System.Globalization;
    using System.Diagnostics;
    using System.Linq;
    
    using Microsoft.Azure.Management.DataFactories.Models;
    using Microsoft.Azure.Management.DataFactories.Runtime;
    
    using Microsoft.WindowsAzure.Storage;
    using Microsoft.WindowsAzure.Storage.Blob;
    ```
6. Remplacez le nom de **l’espace de noms** par **MyDotNetActivityNS**.

    ```csharp
    namespace MyDotNetActivityNS
    ```
7. Remplacez le nom de la classe par **MyDotNetActivity** et dérivez-le de l’interface **IDotNetActivity**, comme indiqué ci-dessous.

    ```csharp
    public class MyDotNetActivity : IDotNetActivity
    ```
8. Implémentez (ajoutez) la méthode **Execute** de l’interface **IDotNetActivity** dans la classe **MyDotNetActivity** et copiez l’exemple de code suivant dans la méthode. Pour une explication de la logique utilisée dans cette méthode, voir la section [Méthode Execute](#execute-method) .

    ```csharp
    /// <summary>
    /// Execute method is the only method of IDotNetActivity interface you must implement.
    /// In this sample, the method invokes the Calculate method to perform the core logic.  
    /// </summary>
    public IDictionary<string, string> Execute(
       IEnumerable<LinkedService> linkedServices,
       IEnumerable<Dataset> datasets,
       Activity activity,
       IActivityLogger logger)
    {
    
       // declare types for input and output data stores
       AzureStorageLinkedService inputLinkedService;
    
       Dataset inputDataset = datasets.Single(dataset => dataset.Name == activity.Inputs.Single().Name);
    
       foreach (LinkedService ls in linkedServices)
           logger.Write("linkedService.Name {0}", ls.Name);
    
       // using First method instead of Single since we are using the same
       // Azure Storage linked service for input and output.
       inputLinkedService = linkedServices.First(
           linkedService =>
           linkedService.Name ==
           inputDataset.Properties.LinkedServiceName).Properties.TypeProperties
           as AzureStorageLinkedService;
    
       string connectionString = inputLinkedService.ConnectionString; // To create an input storage client.
       string folderPath = GetFolderPath(inputDataset);
       string output = string.Empty; // for use later.
    
       // create storage client for input. Pass the connection string.
       CloudStorageAccount inputStorageAccount = CloudStorageAccount.Parse(connectionString);
       CloudBlobClient inputClient = inputStorageAccount.CreateCloudBlobClient();
    
       // initialize the continuation token before using it in the do-while loop.
       BlobContinuationToken continuationToken = null;
       do
       {   // get the list of input blobs from the input storage client object.
           BlobResultSegment blobList = inputClient.ListBlobsSegmented(folderPath,
                                    true,
                                    BlobListingDetails.Metadata,
                                    null,
                                    continuationToken,
                                    null,
                                    null);
    
           // Calculate method returns the number of occurrences of
           // the search term (“Microsoft”) in each blob associated
           // with the data slice.
           //
           // definition of the method is shown in the next step.
           output = Calculate(blobList, logger, folderPath, ref continuationToken, "Microsoft");
    
       } while (continuationToken != null);
    
       // get the output dataset using the name of the dataset matched to a name in the Activity output collection.
       Dataset outputDataset = datasets.Single(dataset => dataset.Name == activity.Outputs.Single().Name);
    
       folderPath = GetFolderPath(outputDataset);
    
       logger.Write("Writing blob to the folder: {0}", folderPath);
    
       // create a storage object for the output blob.
       CloudStorageAccount outputStorageAccount = CloudStorageAccount.Parse(connectionString);
       // write the name of the file.
       Uri outputBlobUri = new Uri(outputStorageAccount.BlobEndpoint, folderPath + "/" + GetFileName(outputDataset));
    
       logger.Write("output blob URI: {0}", outputBlobUri.ToString());
       // create a blob and upload the output text.
       CloudBlockBlob outputBlob = new CloudBlockBlob(outputBlobUri, outputStorageAccount.Credentials);
       logger.Write("Writing {0} to the output blob", output);
       outputBlob.UploadText(output);
    
       // The dictionary can be used to chain custom activities together in the future.
       // This feature is not implemented yet, so just return an empty dictionary.
       return new Dictionary<string, string>();
    }
    ```
9. Ajoutez les méthodes d’assistance suivantes à la classe. Ces méthodes sont appelées par la méthode **Execute** . Plus important encore, la méthode **Calculate** isole le code qui effectue une itération dans chaque objet blob.

    ```csharp
    /// <summary>
    /// Gets the folderPath value from the input/output dataset.
    /// </summary>
    private static string GetFolderPath(Dataset dataArtifact)
    {
       if (dataArtifact == null || dataArtifact.Properties == null)
       {
           return null;
       }
    
       AzureBlobDataset blobDataset = dataArtifact.Properties.TypeProperties as AzureBlobDataset;
       if (blobDataset == null)
       {
           return null;
       }
    
       return blobDataset.FolderPath;
    }
    
    /// <summary>
    /// Gets the fileName value from the input/output dataset.
    /// </summary>
    
    private static string GetFileName(Dataset dataArtifact)
    {
       if (dataArtifact == null || dataArtifact.Properties == null)
       {
           return null;
       }
    
       AzureBlobDataset blobDataset = dataArtifact.Properties.TypeProperties as AzureBlobDataset;
       if (blobDataset == null)
       {
           return null;
       }
    
       return blobDataset.FileName;
    }
    
    /// <summary>
    /// Iterates through each blob (file) in the folder, counts the number of instances of search term in the file,
    /// and prepares the output text that is written to the output blob.
    /// </summary>
    
    public static string Calculate(BlobResultSegment Bresult, IActivityLogger logger, string folderPath, ref BlobContinuationToken token, string searchTerm)
    {
       string output = string.Empty;
       logger.Write("number of blobs found: {0}", Bresult.Results.Count<IListBlobItem>());
       foreach (IListBlobItem listBlobItem in Bresult.Results)
       {
           CloudBlockBlob inputBlob = listBlobItem as CloudBlockBlob;
           if ((inputBlob != null) && (inputBlob.Name.IndexOf("$$$.$$$") == -1))
           {
               string blobText = inputBlob.DownloadText(Encoding.ASCII, null, null, null);
               logger.Write("input blob text: {0}", blobText);
               string[] source = blobText.Split(new char[] { '.', '?', '!', ' ', ';', ':', ',' }, StringSplitOptions.RemoveEmptyEntries);
               var matchQuery = from word in source
                                where word.ToLowerInvariant() == searchTerm.ToLowerInvariant()
                                select word;
               int wordCount = matchQuery.Count();
               output += string.Format("{0} occurrences(s) of the search term \"{1}\" were found in the file {2}.\r\n", wordCount, searchTerm, inputBlob.Name);
           }
       }
       return output;
    }
    ```
    La méthode **GetFolderPath** renvoie le chemin d’accès au dossier vers lequel pointe le jeu de données et la méthode **GetFileName** renvoie le nom de l’objet blob/fichier vers lequel pointe le jeu de données.

    ```csharp

    "name": "InputDataset",
    "properties": {
        "type": "AzureBlob",
        "linkedServiceName": "StorageLinkedService",
        "typeProperties": {
            "fileName": "file.txt",
            "folderPath": "mycontainer/inputfolder/{Year}-{Month}-{Day}-{Hour}",
    ```

    La méthode **Calculate** calcule le nombre d’instances du mot-clé **Microsoft** dans les fichiers d’entrée (objets blob du dossier). Le terme de recherche (« Microsoft ») est codé en dur dans le code.

1. Compilez le projet. Cliquez sur l’option **Générer** du menu, puis sur **Générer la solution**.
2. Lancez **l’Explorateur Windows** et accédez au dossier **bin\\debug** ou **bin\\release** (selon le type de build).
3. Créez un fichier zip **MyDotNetActivity.zip** contenant tous les fichiers binaires dans le dossier **\\bin\\Debug**. Vous pouvez également inclure le fichier MyDotNetActivity.**pdb** afin d’obtenir des détails supplémentaires, tels que le numéro de ligne du code source à l’origine du problème en cas de défaillance.

   ![](./media/data-factory-data-processing-using-batch/image5.png)
4. Chargez le fichier **MyDotNetActivity.zip** en tant qu’objet blob dans le conteneur d’objets blob `customactivitycontainer` du Stockage Blob Azure qu’utilise le service lié **StorageLinkedService** dans **ADFTutorialDataFactory**. S’il n’existe pas déjà, créez le conteneur d’objets blob `customactivitycontainer`.

#### <a name="execute-method"></a>Méthode Execute
Cette section fournit des informations et remarques supplémentaires concernant le code contenu dans la méthode Execute.

1. Les membres nécessaires pour l’itération dans la collection d’entrée se trouvent dans l’espace de noms [Microsoft.WindowsAzure.Storage.Blob](https://msdn.microsoft.com/library/azure/microsoft.windowsazure.storage.blob.aspx) . L’itération dans la collection d’objets blob requiert d’utiliser la classe **BlobContinuationToken** . Vous devez utiliser une boucle do-while en utilisant le jeton pour sortir de la boucle. Pour plus d’informations, voir [Utilisation du stockage d’objets blob à partir de .NET](../storage/blobs/storage-dotnet-how-to-use-blobs.md). Une boucle de base est illustrée ici :

    ```csharp
    // Initialize the continuation token.
    BlobContinuationToken continuationToken = null;
    do
    {
    // Get the list of input blobs from the input storage client object.
    BlobResultSegment blobList = inputClient.ListBlobsSegmented(folderPath,
    
                         true,
                                   BlobListingDetails.Metadata,
                                   null,
                                   continuationToken,
                                   null,
                                   null);
    // Return a string derived from parsing each blob.
    
     output = Calculate(blobList, logger, folderPath, ref continuationToken, "Microsoft");
    
    } while (continuationToken != null);

    ```
   Pour plus de détails, voir la documentation sur la méthode [ListBlobsSegmented](https://msdn.microsoft.com/library/jj717596.aspx) .
2. Le code permettant de parcourir l’ensemble d’objets blob s’inscrit logiquement dans la boucle do-while. Dans la méthode **Execute**, la boucle do-while transmet la liste d’objets blob à une méthode nommée **Calculate**. La méthode renvoie une variable de chaîne nommée **output** , qui correspond au résultat de l’itération dans tous les objets blob du segment.

   Elle retourne le nombre d’occurrences du terme de recherche (**Microsoft**) dans l’objet blob transmis à la méthode **Calculate**.

    ```csharp
    output += string.Format("{0} occurrences of the search term \"{1}\" were found in the file {2}.\r\n", wordCount, searchTerm, inputBlob.Name);
    ```
3. Une fois l’exécution de la méthode **Calculate** terminée, vous devez écrire le résultat dans un nouvel objet blob. Par conséquent, pour chaque ensemble d’objets blob traité, un nouvel objet blob peut être écrit avec les résultats correspondants. Pour écrire dans un nouvel objet blob, commencez par rechercher le jeu de données de sortie.

    ```csharp
    // Get the output dataset using the name of the dataset matched to a name in the Activity output collection.
    Dataset outputDataset = datasets.Single(dataset => dataset.Name == activity.Outputs.Single().Name);
    ```
4. Le code appelle également une méthode d’assistance, **GetFolderPath** , pour récupérer le chemin d’accès au dossier (nom du conteneur de stockage).

    ```csharp
    folderPath = GetFolderPath(outputDataset);
    ```
   La méthode **GetFolderPath** effectue un cast de l’objet DataSet dans un AzureBlobDataSet, qui comporte une propriété nommée FolderPath.

    ```csharp
    AzureBlobDataset blobDataset = dataArtifact.Properties.TypeProperties as AzureBlobDataset;
    
    return blobDataset.FolderPath;
    ```
5. Le code appelle la méthode **GetFileName** pour récupérer le nom du fichier (nom de l’objet blob). Le code est similaire au code présenté ci-dessus pour obtenir le chemin d’accès au dossier.

    ```csharp
    AzureBlobDataset blobDataset = dataArtifact.Properties.TypeProperties as AzureBlobDataset;
    
    return blobDataset.FileName;
    ```
6. Le nom du fichier est écrit grâce à la création d’un objet URI. Le constructeur d’URI utilise la propriété **BlobEndpoint** pour renvoyer le nom du conteneur. Le nom de fichier et le chemin du dossier sont ajoutés pour construire l’URI de l’objet blob de sortie.  

    ```csharp
    // Write the name of the file.
    Uri outputBlobUri = new Uri(outputStorageAccount.BlobEndpoint, folderPath + "/" + GetFileName(outputDataset));
    ```
7. Le nom du fichier ayant été écrit, vous pouvez à présent écrire la chaîne de sortie de la méthode **Calculate** dans un nouvel objet blob :

    ```csharp
    // Create a blob and upload the output text.
    CloudBlockBlob outputBlob = new CloudBlockBlob(outputBlobUri, outputStorageAccount.Credentials);
    logger.Write("Writing {0} to the output blob", output);
    outputBlob.UploadText(output);
    ```

### <a name="create-the-data-factory"></a>Création de la fabrique de données
Dans la section [Création de l’activité personnalisée](#create-the-custom-activity) , vous avez créé une activité personnalisée et chargé le fichier zip contenant les fichiers binaires et le fichier PDB dans un conteneur d’objets blob Azure. Dans cette section, vous allez créer une **fabrique de données** Azure avec un **pipeline** qui utilise **l’activité personnalisée**.

Le jeu de données d’entrée de l’activité personnalisée représente les objets blob (fichiers) contenus dans le dossier d’entrée (`mycontainer\\inputfolder`) du stockage d’objets blob. Le jeu de données de sortie de l’activité représente les objets blob de sortie contenus dans le dossier de sortie (`mycontainer\\outputfolder`) du stockage d’objets blob.

Déposez un ou plusieurs fichiers dans les dossiers d’entrée :

```
mycontainer -\> inputfolder
    2015-11-16-00
    2015-11-16-01
    2015-11-16-02
    2015-11-16-03
    2015-11-16-04
```

Par exemple, déposez un fichier (file.txt) avec le contenu suivant dans chacun des dossiers.

```
test custom activity Microsoft test custom activity Microsoft
```

Le dossier d’entrée correspond à une tranche dans Azure Data Factory, même s’il contient plusieurs fichiers. Lorsque chaque tranche est traitée par le pipeline, l’activité personnalisée effectue une itération dans tous les objets blob du dossier d’entrée pour la tranche en question.

Vous voyez cinq fichiers de sortie avec le même contenu. Par exemple, le fichier de sortie résultant du traitement du fichier figurant dans le dossier 2015-11-16-00 a le contenu suivant :

```
2 occurrences(s) of the search term "Microsoft" were found in the file inputfolder/2015-11-16-00/file.txt.
```

Si vous déposez plusieurs fichiers (file.txt, file2.txt, file3.txt) ayant le même contenu dans le dossier d’entrée, le contenu suivant apparaît dans le fichier de sortie. Dans cet exemple, chaque dossier (2015-11-16-00, etc.) correspond à une tranche, même si le dossier contient plusieurs fichiers d’entrée.

```csharp
2 occurrences(s) of the search term "Microsoft" were found in the file inputfolder/2015-11-16-00/file.txt.
2 occurrences(s) of the search term "Microsoft" were found in the file inputfolder/2015-11-16-00/file2.txt.
2 occurrences(s) of the search term "Microsoft" were found in the file inputfolder/2015-11-16-00/file3.txt.
```

Le fichier de sortie comprend désormais trois lignes, une par fichier d’entrée (objet blob) dans le dossier associé à la tranche (2015-11-16-00).

Une tâche est créée pour chaque exécution d’activité. Dans cet exemple, le pipeline ne contient qu’une seule activité. Quand le pipeline traite une tranche, l’activité personnalisée s’exécute sur Azure Batch pour traiter la tranche. Étant donné qu’il y a cinq tranches (chacune pouvant comporter plusieurs objets blob ou fichiers), cinq tâches sont créées dans Azure Batch. Quand une tâche s’exécute sur Batch, c’est en fait l’activité personnalisée qui s’exécute.

La procédure pas à pas suivante fournit des détails supplémentaires.

#### <a name="step-1-create-the-data-factory"></a>Étape 1 : Créer la fabrique de données
1. Une fois connecté au [portail Azure](https://portal.azure.com/), procédez comme suit :

   1. Cliquez sur **NOUVEAU** dans le menu de gauche.
   2. Dans le panneau **Nouveau**, cliquez sur **Données et analyses**.
   3. Cliquez sur **Data Factory** dans le panneau **Analyse des données**.
2. Dans le panneau **Nouvelle fabrique de données**, spécifiez le nom **CustomActivityFactory**. Le nom de la fabrique de données Azure doit être un nom global unique. Si l’erreur **Data factory name “CustomActivityFactory” is not available** (Le nom de la fabrique de données « CustomActivityFactory » n’est pas disponible) s’affiche, changez le nom de la fabrique de données (par exemple, **votrenomCustomActivityFactory**), puis tentez de la recréer.
3. Cliquez sur **NOM DU GROUPE DE RESSOURCES**pour sélectionner un groupe de ressources existant, ou créez un groupe de ressources.
4. Veillez à utiliser l’abonnement et la région correspondant à ceux dans lesquels vous voulez créer la fabrique de données.
5. Cliquez sur **Créer** dans le panneau **Nouvelle fabrique de données**.
6. La fabrique de données apparaît comme étant en cours de création dans le **Tableau de bord** du portail Azure.
7. Une fois la fabrique de données créée, la page correspondante s’affiche et indique son contenu.

   ![](./media/data-factory-data-processing-using-batch/image6.png)

#### <a name="step-2-create-linked-services"></a>Étape 2 : Créer des services liés
Les services liés se chargent de lier des magasins de données ou des services de calcul à une fabrique de données Azure. Dans cette étape, vous allez lier vos comptes **Stockage Azure** et **Azure Batch** à votre fabrique de données.

#### <a name="create-azure-storage-linked-service"></a>Créer le service lié Azure Storage
1. Cliquez sur la vignette **Créer et déployer** dans le panneau **DATA FACTORY** de **CustomActivityFactory**. Data Factory Editor s’affiche.
2. Cliquez sur **Nouvelle banque de données** dans la barre de commandes et choisissez **Stockage Azure**. Le script JSON de création d’un service lié Microsoft Azure Storage doit apparaître dans l’éditeur.

   ![](./media/data-factory-data-processing-using-batch/image7.png)

3. Remplacez **account name** par le nom de votre compte de stockage Azure et **account key** par sa clé d’accès. Pour savoir comment obtenir votre clé d’accès de stockage, voir [Affichage, copie et régénération de clés d’accès de stockage](../storage/common/storage-create-storage-account.md#manage-your-storage-account).

4. Cliquez sur l’option **Déployer** de la barre de commandes pour déployer le service lié.

   ![](./media/data-factory-data-processing-using-batch/image8.png)

#### <a name="create-azure-batch-linked-service"></a>Créer un service lié Azure Batch
Au cours de cette étape, vous créez un service lié pour votre compte **Azure Batch**, qui est utilisé pour exécuter l’activité personnalisée Data Factory.

1. Cliquez sur **Nouveau calcul** dans la barre de commandes et choisissez **Azure Batch**. Le script JSON pour la création d’un service lié Azure Batch doit apparaître dans l’éditeur.
2. Dans le script JSON :

   1. Remplacez **nom de compte** par le nom de votre compte Azure Batch.
   2. Remplacez **clé d’accès** avec la clé d’accès du compte Azure Batch.
   3. Entrez l’ID du pool pour la propriété **poolName****.** Pour cette propriété, vous pouvez spécifier le nom de pool ou l’ID de pool.
   4. Entrez l’URI du lot pour la propriété JSON **batchUri** .

      > [!IMPORTANT]
      > **L’URL** figurant dans le **panneau du compte Azure Batch** est au format suivant : \<nomducompte\>.\<région\>.batch.azure.com. Pour la propriété **batchUri** dans le fichier JSON, vous devez **supprimer « nomducompte ».** de l’URL. Exemple : `"batchUri": "https://eastus.batch.azure.com"`.
      >
      >

      ![](./media/data-factory-data-processing-using-batch/image9.png)

      Pour la propriété **poolName** , vous pouvez également spécifier l’ID du pool au lieu du nom du pool.

      > [!NOTE]
      > Le service Data Factory ne prend pas en charge l’option à la demande pour Azure Batch contrairement à HDInsight. Vous pouvez uniquement utiliser votre propre pool Azure Batch dans une fabrique de données Azure.
      >
      >
   5. Spécifiez **StorageLinkedService** for the **StorageLinkedService** . Vous avez créé ce service lié à l’étape précédente. Ce stockage est utilisé en tant que zone de transit pour les fichiers et les journaux.
3. Cliquez sur l’option **Déployer** de la barre de commandes pour déployer le service lié.

#### <a name="step-3-create-datasets"></a>Étape 3 : Créer les jeux de données
Dans cette étape, vous allez créer des jeux de données pour représenter les données d’entrée et de sortie.

#### <a name="create-input-dataset"></a>Créer le jeu de données d’entrée
1. Dans **l’éditeur** de Data Factory, cliquez sur le bouton **Nouveau jeu de données** de la barre d’outils et sélectionnez **Stockage Blob Azure** dans le menu déroulant.
2. Remplacez le script JSON affiché dans le volet droit par l’extrait de code JSON suivant :

    ```json
    {
       "name": "InputDataset",
       "properties": {
           "type": "AzureBlob",
           "linkedServiceName": "AzureStorageLinkedService",
           "typeProperties": {
               "folderPath": "mycontainer/inputfolder/{Year}-{Month}-{Day}-{Hour}",
               "format": {
                   "type": "TextFormat"
               },
               "partitionedBy": [
                   {
                       "name": "Year",
                       "value": {
                           "type": "DateTime",
                           "date": "SliceStart",
                           "format": "yyyy"
                       }
                   },
                   {
                       "name": "Month",
                       "value": {
                           "type": "DateTime",
                           "date": "SliceStart",
                           "format": "MM"
                       }
                   },
                   {
                       "name": "Day",
                       "value": {
                           "type": "DateTime",
                           "date": "SliceStart",
                           "format": "dd"
                       }
                   },
                   {
                       "name": "Hour",
                       "value": {
                           "type": "DateTime",
                           "date": "SliceStart",
                           "format": "HH"
                       }
                   }
               ]
           },
           "availability": {
               "frequency": "Hour",
               "interval": 1
           },
           "external": true,
           "policy": {}
       }
    }
    ```

    Plus loin dans cette procédure pas à pas, vous allez créer un pipeline associé à l’heure de début 2015-11-16T00:00:00Z et à l’heure de fin 2015-11-16T05:00:00Z. Ce pipeline est planifié pour produire des données **toutes les heures**, ce qui signifie que l’on obtient 5 tranches d’entrée/sortie (comprises entre **00**: 00:00 et \>**05**: 00:00).

    Les paramètres **frequency** et **interval** du jeu de données d’entrée sont respectivement définis sur **Hour** et sur **1**, ce qui signifie que la tranche d’entrée est disponible toutes les heures.

    Voici les heures de début de chaque tranche, représentées par la variable système **SliceStart** dans l’extrait de code JSON ci-dessus.

    | **Tranche** | **Heure de début**          |
    |-----------|-------------------------|
    | 1         | 2015-11-16T**00**:00:00 |
    | 2         | 2015-11-16T**01**:00:00 |
    | 3         | 2015-11-16T**02**:00:00 |
    | 4         | 2015-11-16T**03**:00:00 |
    | 5         | 2015-11-16T**04**:00:00 |

    La valeur **folderPath** est calculée à l’aide de la partie année, mois, jour et heure de l’heure de début de la tranche (**SliceStart**). Par conséquent, voici comment un dossier d’entrée est mappé à une tranche.

    | **Tranche** | **Heure de début**          | **Dossier d’entrée**  |
    |-----------|-------------------------|-------------------|
    | 1         | 2015-11-16T**00**:00:00 | 2015-11-16-**00** |
    | 2         | 2015-11-16T**01**:00:00 | 2015-11-16-**01** |
    | 3         | 2015-11-16T**02**:00:00 | 2015-11-16-**02** |
    | 4         | 2015-11-16T**03**:00:00 | 2015-11-16-**03** |
    | 5         | 2015-11-16T**04**:00:00 | 2015-11-16-**04** |

1. Dans la barre d’outils, cliquez sur **Déployer** pour créer et déployer la table **InputDataset**.

#### <a name="create-output-dataset"></a>Créer un jeu de données de sortie
Au cours de cette étape, vous créez un autre jeu de données de type AzureBlob pour représenter les données de sortie.

1. Dans **l’éditeur** de Data Factory, cliquez sur le bouton **Nouveau jeu de données** de la barre d’outils et sélectionnez **Stockage Blob Azure** dans le menu déroulant.
2. Remplacez le script JSON affiché dans le volet droit par l’extrait de code JSON suivant :

    ```json
    {
       "name": "OutputDataset",
       "properties": {
           "type": "AzureBlob",
           "linkedServiceName": "AzureStorageLinkedService",
           "typeProperties": {
               "fileName": "{slice}.txt",
               "folderPath": "mycontainer/outputfolder",
               "partitionedBy": [
                   {
                       "name": "slice",
                       "value": {
                           "type": "DateTime",
                           "date": "SliceStart",
                           "format": "yyyy-MM-dd-HH"
                       }
                   }
               ]
           },
           "availability": {
               "frequency": "Hour",
               "interval": 1
           }
       }
    }
    ```

    Un objet blob/fichier de sortie est généré pour chaque tranche d’entrée. Voici la procédure de nommage des fichiers de sortie pour chaque tranche. Tous les fichiers de sortie sont générés dans un seul dossier de sortie : `mycontainer\\outputfolder`.

    | **Tranche** | **Heure de début**          | **Fichier de sortie**       |
    |-----------|-------------------------|-----------------------|
    | 1         | 2015-11-16T**00**:00:00 | 2015-11-16-**00.txt** |
    | 2         | 2015-11-16T**01**:00:00 | 2015-11-16-**01.txt** |
    | 3         | 2015-11-16T**02**:00:00 | 2015-11-16-**02.txt** |
    | 4         | 2015-11-16T**03**:00:00 | 2015-11-16-**03.txt** |
    | 5         | 2015-11-16T**04**:00:00 | 2015-11-16-**04.txt** |

    N’oubliez pas que tous les fichiers figurant dans un dossier d’entrée (par exemple, 2015-11-16-00) font partie d’une tranche associée à l’heure de début (2015-11-16-00). Lorsque cette tranche est traitée, l’activité personnalisée parcourt chaque fichier et génère une ligne dans le fichier de sortie avec le nombre d’occurrences du terme de recherche (« Microsoft »). Si le dossier 2015-11-16-00 contient trois fichiers, le fichier de sortie 2015-11-16-00.txt comprend trois lignes.

1. Dans la barre d’outils, cliquez sur **Déployer** pour créer et déployer la table **OutputDataset**.

#### <a name="step-4-create-and-run-the-pipeline-with-custom-activity"></a>Étape 4 : Créer et exécuter le pipeline avec une activité personnalisée
Au cours de cette étape, vous créez un pipeline comprenant une seule activité, l’activité personnalisée que vous avez créée précédemment.

> [!IMPORTANT]
> Si vous n’avez pas chargé le fichier **file.txt** dans les dossiers d’entrée du conteneur d’objets blob, veuillez le faire avant de créer le pipeline. La propriété **isPaused** étant définie sur false dans le fichier JSON du pipeline, le pipeline s’exécute immédiatement, car la date de **début** se situe dans le passé.
>
>

1. Dans Data Factory Editor, cliquez sur **Nouveau pipeline** dans la barre de commandes. Si vous ne voyez pas apparaître la commande, cliquez sur **... (points de suspension)** pour l’afficher.
2. Remplacez le script JSON affiché dans le volet droit par le script JSON suivant :

    ```json
    {
       "name": "PipelineCustom",
       "properties": {
           "description": "Use custom activity",
           "activities": [
               {
                   "type": "DotNetActivity",
                   "typeProperties": {
                       "assemblyName": "MyDotNetActivity.dll",
                       "entryPoint": "MyDotNetActivityNS.MyDotNetActivity",
                       "packageLinkedService": "AzureStorageLinkedService",
                       "packageFile": "customactivitycontainer/MyDotNetActivity.zip"
                   },
                   "inputs": [
                       {
                           "name": "InputDataset"
                       }
                   ],
                   "outputs": [
                       {
                           "name": "OutputDataset"
                       }
                   ],
                   "policy": {
                       "timeout": "00:30:00",
                       "concurrency": 5,
                       "retry": 3
                   },
                   "scheduler": {
                       "frequency": "Hour",
                       "interval": 1
                   },
                   "name": "MyDotNetActivity",
                   "linkedServiceName": "AzureBatchLinkedService"
               }
           ],
           "start": "2015-11-16T00:00:00Z",
           "end": "2015-11-16T05:00:00Z",
           "isPaused": false
      }
    }
    ```
   Notez les points suivants :

   * Le pipeline ne comprend qu’une seule activité du type **DotNetActivity**.
   * Le paramètre **AssemblyName** est défini sur le nom de la DLL **MyDotNetActivity.dll**.
   * Le paramètre **EntryPoint** est défini sur **MyDotNetActivityNS.MyDotNetActivity**. Il s’agit essentiellement de \<namespace\>.\<classname\> dans votre code.
   * **PackageLinkedService** est défini sur **StorageLinkedService**, qui pointe vers le stockage d’objets blob contenant le fichier .zip de l’activité personnalisée. Si vous utilisez des comptes de stockage différents pour les fichiers d’entrée/sortie et le fichier zip de l’activité personnalisée, vous devez créer un autre service lié Stockage Azure. Cet article suppose que vous utilisez le même compte Azure Storage.
   * Le paramètre **PackageFile** est défini sur **customactivitycontainer/MyDotNetActivity.zip**. Il est au format : \<conteneur_du_zip\>/\<nom_du_zip.zip\>.
   * L’activité personnalisée utilise **InputDataset** comme entrée et **OutputDataset** comme sortie.
   * La propriété **linkedServiceName** de l’activité personnalisée pointe vers **AzureBatchLinkedService**, ce qui indique à Azure Data Factory que l’activité personnalisée doit s’exécuter sur Azure Batch.
   * Le paramètre **concurrency** est important. Si vous utilisez la valeur par défaut, 1, même si vous avez plusieurs nœuds de calcul dans le pool Azure Batch, les tranches sont traitées successivement. Par conséquent, vous ne profitez pas de la capacité de traitement en parallèle d’Azure Batch. Si vous définissez **concurrency** sur une valeur plus élevée, par exemple 2, deux tranches (correspondant à deux tâches dans Azure Batch) peuvent être traitées simultanément, auquel cas les deux machines virtuelles du pool Azure Batch sont utilisées. Vous devez par conséquent définir la propriété concurrency de façon appropriée.
   * Par défaut, une seule tâche (tranche) est exécutée sur une machine virtuelle à un moment donné. La raison est que, par défaut, le paramètre **Maximum tasks per VM** (Nombre maximal de tâches par machine virtuelle) est défini sur 1 pour un pool Azure Batch. En rapport avec les conditions préalables, vous avez créé un pool pour lequel cette propriété est définie sur 2, de sorte que deux tranches Data Factory peuvent s’exécuter simultanément sur une machine virtuelle.

    -   **isPaused** est définie sur false. Le pipeline s’exécute immédiatement dans cet exemple car les tranches débutent à une date antérieure. Vous pouvez définir cette propriété sur true pour suspendre le pipeline et lui réaffecter la valeur false pour reprendre l’exécution.

    -   Cinq heures séparent les heures de **début** et de **fin**, et les tranches sont produites toutes les heures, ce qui signifie que cinq tranches sont produites par le pipeline.

1. Cliquez sur **Déployer** dans la barre de commandes pour déployer le pipeline.

#### <a name="step-5-test-the-pipeline"></a>Étape 5 : Tester le pipeline
Au cours de cette étape, vous testez le pipeline en déposant des fichiers dans les dossiers d’entrée. Commençons par tester le pipeline avec un fichier par dossier d’entrée.

1. Dans le panneau Data Factory du portail Azure, cliquez sur **Diagramme**.

   ![](./media/data-factory-data-processing-using-batch/image10.png)
2. Dans la vue de diagramme, double-cliquez sur le jeu de données d’entrée **InputDataset**.

   ![](./media/data-factory-data-processing-using-batch/image11.png)
3. Vous devriez voir le panneau **InputDataset** avec les cinq tranches prêtes. Notez **l’HEURE DE DÉBUT DE LA TRANCHE** et **l’HEURE DE FIN DE LA TRANCHE** pour chaque tranche.

   ![](./media/data-factory-data-processing-using-batch/image12.png)
4. Dans la **vue de diagramme**, cliquez sur **OutputDataset**.
5. Si elles ont déjà été produites, les cinq tranches de sortie doivent être à l’état Prêt.

   ![](./media/data-factory-data-processing-using-batch/image13.png)
6. Pour afficher les **tâches** associées aux **tranches** et voir la machine virtuelle sur laquelle chaque tranche a été exécutée, utilisez le portail Azure. Consultez la section [Intégration de Data Factory et Batch](#data-factory-and-batch-integration) pour plus d’informations.
7. Les fichiers de sortie devraient apparaître dans le dossier `outputfolder` de `mycontainer` dans votre Stockage Blob Azure.

   ![](./media/data-factory-data-processing-using-batch/image15.png)

   Vous devriez voir cinq fichiers de sortie, un par tranche d’entrée. Chaque fichier de sortie doit avoir contenu similaire à ce qui suit :

    ```
    2 occurrences(s) of the search term "Microsoft" were found in the file inputfolder/2015-11-16-00/file.txt.
    ```
   Le diagramme suivant illustre la manière dont les tranches Data Factory sont mappées à des tâches dans Azure Batch. Dans cet exemple, une tranche n’est exécutée qu’une seule fois.

   ![](./media/data-factory-data-processing-using-batch/image16.png)
8. À présent, essayons avec plusieurs fichiers dans un dossier. Créez des fichiers **file2.txt**, **file3.txt**, **file4.txt** et **file5.txt** ayant le même contenu que le fichier file.txt dans le dossier **2015-11-06-01**.
9. Dans le dossier de sortie, **supprimez** le fichier de sortie **2015-11-16-01.txt**.
10. À présent, dans le panneau **OutputDataset**, cliquez avec le bouton droit sur la tranche dont **l’HEURE DE DÉBUT DE LA TRANCHE** a la valeur **16/11/2015 01:00:00 AM**, puis cliquez sur **Exécuter** pour réexécuter/re-traiter la tranche. À présent, la tranche contient cinq fichiers au lieu d’un seul.

    ![](./media/data-factory-data-processing-using-batch/image17.png)
11. Une fois la tranche exécutée, quand son état est **Prêt**, vérifiez le contenu de son fichier de sortie (**2015-11-16-01.txt**) dans le dossier `outputfolder` de `mycontainer` dans votre stockage d’objets blob. Il doit y avoir une ligne pour chaque fichier de la tranche.

    ```
    2 occurrences(s) of the search term "Microsoft" were found in the file inputfolder/2015-11-16-01/file.txt.
    2 occurrences(s) of the search term "Microsoft" were found in the file inputfolder/2015-11-16-01/file2.txt.
    2 occurrences(s) of the search term "Microsoft" were found in the file inputfolder/2015-11-16-01/file3.txt.
    2 occurrences(s) of the search term "Microsoft" were found in the file inputfolder/2015-11-16-01/file4.txt.
    2 occurrences(s) of the search term "Microsoft" were found in the file inputfolder/2015-11-16-01/file5.txt.
    ```

> [!NOTE]
> Si vous n’avez pas supprimé le fichier de sortie 2015-11-16-01.txt avant d’essayer avec cinq fichiers d’entrée, vous devez voir une ligne pour l’exécution de la tranche précédente, et cinq lignes pour l’exécution de la tranche actuelle. Par défaut, le contenu est ajouté au fichier de sortie s’il existe.
>
>

#### <a name="data-factory-and-batch-integration"></a>Intégration de Data Factory et Batch
Le service Data Factory crée un travail dans Azure Batch sous le nom `adf-poolname:job-xxx`.

![Azure Data Factory - Travaux Batch](media/data-factory-data-processing-using-batch/data-factory-batch-jobs.png)

Une tâche dans le travail est créée pour chaque exécution d’activité d’une tranche. Si 10 tranches sont prêtes à être traitées, 10 tâches seront créées dans le travail. Plusieurs tranches peuvent être exécutées en parallèle si vous disposez de plusieurs nœuds de calcul dans le pool. Vous pouvez également avoir plusieurs tranches exécutées sur le même nœud de calcul si le nombre maximum de tâches par nœud de calcul est défini sur une valeur supérieure à 1.

Dans cet exemple, il y a cinq tranches, donc cinq tâches dans Azure Batch. Avec la propriété **concurrency** définie sur **5** dans le fichier JSON du pipeline dans Azure Data Factory, et le paramètre **Nombre maximal de tâches par machine virtuelle** défini sur **2** dans le pool Azure Batch avec **2** machines virtuelles, les tâches s’exécutent très rapidement (vérifiez les heures de début et de fin des tâches).

Utilisez le portail pour afficher le travail Batch et ses tâches associées avec des **tranches** et voir sur quelle machine virtuelle chaque tranche s’est exécutée.

![Azure Data Factory - Tâches de travaux Batch](media/data-factory-data-processing-using-batch/data-factory-batch-job-tasks.png)

### <a name="debug-the-pipeline"></a>Déboguer le pipeline
Le débogage consiste à utiliser quelques techniques de base :

1. Si la tranche d’entrée n’est pas définie sur **Prêt**, vérifiez que la structure du dossier d’entrée est correcte et que le fichier file.txt est présent dans les dossiers d’entrée.

   ![](./media/data-factory-data-processing-using-batch/image3.png)
2. Dans la méthode **Execute** de votre activité personnalisée, utilisez l’objet **IActivityLogger** pour journaliser les informations qui vous aident à résoudre d’éventuels problèmes. Les messages consignés s’affichent dans le fichier user\_0.log.

   Dans le panneau **OutputDataset**, cliquez sur la tranche pour afficher le panneau **TRANCHE DE DONNÉES** correspondant à cette tranche. Les **activités exécutées** pour cette tranche s’affichent. Vous devez normalement voir une exécution d’activité pour la tranche. Si vous cliquez sur **Exécuter** dans la barre de commandes, vous pouvez démarrer une autre exécution d’activité pour la même tranche.

   Lorsque vous cliquez sur l’exécution d’activité, le panneau **DÉTAILS DE L’EXÉCUTION D’ACTIVITÉ** s’affiche avec une liste de fichiers journaux. Les messages consignés s’affichent dans le fichier **user\_0.log**. Lorsqu’une erreur se produit, vous verrez trois exécutions d’activité car le nombre de tentatives est défini sur 3 dans le script JSON du pipeline et de l’activité. Lorsque vous cliquez sur l’exécution de l’activité, vous accédez aux fichiers journaux qui vous permettront de résoudre l’erreur.

   ![](./media/data-factory-data-processing-using-batch/image18.png)

   Dans la liste des fichiers journaux, cliquez sur le fichier **user-0.log**. Le volet droit affiche les résultats de l’utilisation de la méthode **IActivityLogger.Write** .

   ![](./media/data-factory-data-processing-using-batch/image19.png)

   Consultez le fichier system-0.log pour vérifier les exceptions et messages d’erreur système éventuels.

    ```
    Trace\_T\_D\_12/6/2015 1:43:35 AM\_T\_D\_\_T\_D\_Verbose\_T\_D\_0\_T\_D\_Loading assembly file MyDotNetActivity...
    
    Trace\_T\_D\_12/6/2015 1:43:35 AM\_T\_D\_\_T\_D\_Verbose\_T\_D\_0\_T\_D\_Creating an instance of MyDotNetActivityNS.MyDotNetActivity from assembly file MyDotNetActivity...
    
    Trace\_T\_D\_12/6/2015 1:43:35 AM\_T\_D\_\_T\_D\_Verbose\_T\_D\_0\_T\_D\_Executing Module
    
    Trace\_T\_D\_12/6/2015 1:43:38 AM\_T\_D\_\_T\_D\_Information\_T\_D\_0\_T\_D\_Activity e3817da0-d843-4c5c-85c6-40ba7424dce2 finished successfully
    ```
3. Incluez le fichier **PDB** dans le fichier zip afin que les détails de l’erreur incluent des informations telles que la **pile des appels** quand une erreur se produit.
4. Tous les fichiers contenus dans le fichier zip de l’activité personnalisée doivent se trouver au **niveau supérieur** et ne contenir aucun sous-dossier.

   ![](./media/data-factory-data-processing-using-batch/image20.png)
5. Assurez-vous que les paramètres **assemblyName** (MyDotNetActivity.dll), **entryPoint** (MyDotNetActivityNS.MyDotNetActivity), **packageFile** (customactivitycontainer/Mydotnetactivity.zip) et **packageLinkedService** (qui doit pointer vers le Stockage Blob Azure contenant le fichier .zip) sont définis sur des valeurs correctes.
6. Si vous avez corrigé une erreur et souhaitez relancer le traitement de la tranche, cliquez avec le bouton droit sur la tranche dans le panneau **OutputDataset** puis cliquez sur **Exécuter**.

   ![](./media/data-factory-data-processing-using-batch/image21.png)

   > [!NOTE]
   > Vous voyez un **conteneur** dans votre Stockage Blob Azure nommé `adfjobs`. Ce conteneur n’est pas automatiquement supprimé, mais vous pouvez le supprimer en toute sécurité après avoir testé la solution. De même, la solution Data Factory crée un **travail** Azure Batch nommé `adf-\<pool ID/name\>:job-0000000001`. Si vous le souhaitez, vous pouvez supprimer ce travail après avoir testé la solution.
   >
   >
7. L’activité personnalisée n’utilise pas le ficher **app.config** de votre package. Par conséquent, si votre code lit des chaînes de connexion dans le fichier de configuration, il ne fonctionne pas lors de l’exécution. Quand vous utilisez Azure Batch, la meilleure pratique consiste à stocker les clés secrètes dans un coffre de clés **Azure KeyVault**, à utiliser un principal de service basé sur certificat pour protéger le coffre de clés et à distribuer le certificat à un pool Azure Batch. L’activité personnalisée .NET peut alors accéder aux secrets du coffre de clés au moment de l’exécution. Cette solution est générique et peut s’adapter à n’importe quel type de clé secrète, et pas uniquement aux chaînes de connexion.

    Il existe une solution plus simple (mais non recommandée) : vous pouvez créer un **service lié SQL Azure** avec des paramètres de chaîne de connexion, puis créer un jeu de données qui utilise le service lié et chaîner le jeu de données à l’activité .NET personnalisée en tant que jeu de données d’entrée factice. Vous pouvez accéder ensuite à la chaîne de connexion du service lié dans le code de l’activité personnalisée. Cette fois-ci, le code devrait fonctionner sans problème lors de l’exécution.  

#### <a name="extend-the-sample"></a>Étendre l’exemple
Vous pouvez étendre cet exemple pour en savoir plus sur les fonctionnalités d’Azure Data Factory et d’Azure Batch. Par exemple, pour traiter des tranches d’une autre plage de temps, procédez comme suit :

1. Ajoutez au dossier `inputfolder` les sous-dossiers 2015-11-16-05, 2015-11-16-06, 201-11-16-07, 2011-11-16-08 et 2015-11-16-09, et placez les fichiers d’entrée dans ces dossiers. Modifiez l’heure de fin pour le pipeline de `2015-11-16T05:00:00Z` à `2015-11-16T10:00:00Z`. Dans la **vue de diagramme**, double-cliquez sur **InputDataset** et vérifiez que les tranches d’entrée sont prêtes. Double-cliquez sur **OuptutDataset** pour vérifier l’état des tranches de sortie. Si leur état est Prêt, vérifiez les fichiers de sortie dans le dossier de sortie.
2. Augmentez ou réduisez la valeur du paramètre **concurrency** pour comprendre comment il affecte les performances de votre solution, en particulier le traitement qui se produit sur Azure Batch. (Pour plus d’informations sur le paramètre **concurrency** , voir l’étape 4 : Créer et exécuter le pipeline.)
3. Créez un pool avec une valeur **Maximum tasks per VM**(Nombre maximal de tâches par machine virtuelle) supérieure/inférieure. Mettez à jour le service lié Azure Batch dans la solution Data Factory pour utiliser le nouveau pool créé. (Pour plus d’informations sur le paramètre **Maximum tasks per VM** , voir l’étape 4 : Créer et exécuter le pipeline.)
4. Créez un pool Azure Batch avec la fonctionnalité **autoscale** . La mise à l’échelle automatique des nœuds de calcul dans un pool Azure Batch est en fait un ajustement dynamique de la puissance de traitement utilisée par votre application. 

    Cet exemple de formule permet d’obtenir le comportement suivant : quand le pool est créé, il commence avec 1 machine virtuelle. La métrique $PendingTasks définit le nombre de tâches dans l’état En cours d’exécution + Actif (en file d’attente).  Cette formule recherche le nombre moyen de tâches en attente au cours des 180 dernières secondes et définit TargetDedicated en conséquence. Elle garantit que TargetDedicated ne va jamais au-delà de 25 machines virtuelles. Par conséquent, à mesure que de nouvelles tâches sont envoyées, le pool s’accroît automatiquement et, au fil de la réalisation des tâches, les machines virtuelles se libèrent une à une et la mise à l’échelle automatique réduit ces machines virtuelles. Vous pouvez ajuster startingNumberOfVMs et maxNumberofVMs selon vos besoins.
 
    Formule de mise à l’échelle automatique :

    ``` 
    startingNumberOfVMs = 1;
    maxNumberofVMs = 25;
    pendingTaskSamplePercent = $PendingTasks.GetSamplePercent(180 * TimeInterval_Second);
    pendingTaskSamples = pendingTaskSamplePercent < 70 ? startingNumberOfVMs : avg($PendingTasks.GetSample(180 * TimeInterval_Second));
    $TargetDedicated=min(maxNumberofVMs,pendingTaskSamples);
    ```

   Pour plus d’informations, consultez [Mettre automatiquement à l’échelle les nœuds de calcul dans un pool Azure Batch](../batch/batch-automatic-scaling.md) .

   Si le pool utilise la valeur par défaut du paramètre [autoScaleEvaluationInterval](https://msdn.microsoft.com/library/azure/dn820173.aspx), le service Batch peut mettre 15 à 30 minutes à préparer la machine virtuelle avant d’exécuter l’activité personnalisée.  Si le pool utilise une autre valeur pour autoScaleEvaluationInterval, le service Batch peut prendre la durée d’autoScaleEvaluationInterval + 10 minutes.
5. Dans l’exemple de solution, la méthode **Execute** appelle la méthode **Calculate** qui traite une tranche de données d’entrée pour produire une tranche de données de sortie. Vous pouvez écrire votre propre méthode pour traiter les données d’entrée, et remplacer l’appel de la méthode Calculate dans la méthode Execute par un appel à votre méthode.

### <a name="next-steps-consume-the-data"></a>Étapes suivantes : Consommer les données
Après avoir traité des données, vous pouvez les employer avec des outils en ligne tels que **Microsoft Power BI**. Voici des liens pour vous aider à comprendre Power BI et comment l’utiliser dans Azure :

* [Explorer un jeu de données dans Power BI](https://powerbi.microsoft.com/documentation/powerbi-service-get-data/)
* [Prise en main de Power BI Desktop](https://powerbi.microsoft.com/documentation/powerbi-desktop-getting-started/)
* [Actualisation des données dans Power BI](https://powerbi.microsoft.com/documentation/powerbi-refresh-data/)
* [Azure et Power BI](https://powerbi.microsoft.com/documentation/powerbi-azure-and-power-bi/)

## <a name="references"></a>Références
* [Azure Data Factory](https://azure.microsoft.com/documentation/services/data-factory/)

  * [Présentation du service Azure Data Factory](data-factory-introduction.md)
  * [Prise en main d’Azure Data Factory](data-factory-build-your-first-pipeline.md)
  * [Utilisation des activités personnalisées dans un pipeline Azure Data Factory](data-factory-use-custom-activities.md)
* [Azure Batch](https://azure.microsoft.com/documentation/services/batch/)

  * [Notions de base d’Azure Batch](../batch/batch-technical-overview.md)
  * [Vue d’ensemble des fonctionnalités d’Azure Batch](../batch/batch-api-basics.md)
  * [Création et gestion d’un compte Azure Batch dans le portail Azure](../batch/batch-account-create-portal.md)
  * [Get started with the .NET Azure Batch Library .NET](../batch/batch-dotnet-get-started.md)

[batch-explorer]: https://github.com/Azure/azure-batch-samples/tree/master/CSharp/BatchExplorer
[batch-explorer-walkthrough]: http://blogs.technet.com/b/windowshpc/archive/2015/01/20/azure-batch-explorer-sample-walkthrough.aspx

