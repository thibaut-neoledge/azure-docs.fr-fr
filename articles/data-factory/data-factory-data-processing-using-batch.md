<properties
    pageTitle="Calcul haute performance et orchestration de données à l’aide des services Azure Batch et Data Factory"
    description="Décrit comment traiter de grandes quantités de données dans un pipeline Azure Data Factory en utilisant une capacité de traitement parallèle d’Azure Batch."
    services="data-factory"
    documentationCenter=""
    authors="spelluru"
    manager="jhubbard"
    editor="monicar"/>

<tags
    ms.service="data-factory"
    ms.workload="data-services"
    ms.tgt_pltfrm="na"
    ms.devlang="na"
    ms.topic="article"
    ms.date="04/26/2016"
    ms.author="spelluru"/>
# Calcul haute performance et orchestration de données à l’aide des services Azure Batch et Data Factory

Voici un exemple de solution qui déplace et traite automatiquement des jeux de données à grande échelle. La solution est de bout en bout et inclut l’architecture et le code. Elle est basés sur deux services Azure. Azure Batch fournit HPC en tant que service pour configurer les ordinateurs nécessaires, et pour planifier et coordonner le travail. Azure Data Factory complète Batch en simplifiant l’orchestration du déplacement des données. Vous pouvez spécifier des mouvements réguliers de données dans le cadre du processus ETL, traiter les données, puis transférer les résultats dans un stockage permanent.

L’architecture s’applique à de nombreux scénarios, tels que la modélisation des risques par les services financiers, le traitement et la restitution d’images, ou encore l’analyse génomique.

Avant de suivre l’exemple de solution, consultez la documentation [Azure Batch](../batch/batch-api-basics.md) et [Data Factory](data-factory-introduction.md) si vous n’êtes pas familiarisé avec ces services.

## Diagramme de l’architecture

Le diagramme illustre 1) la manière dont Data Factory orchestre le déplacement et le traitement des données, et (2) la manière dont Azure Batch traite les données en parallèle. Pour une meilleure visibilité, téléchargez et imprimez le diagramme (au format A3) : [Calcul haute performance et orchestration des données à l’aide des services Azure Batch et Data Factory](http://go.microsoft.com/fwlink/?LinkId=717686).

![Diagramme du HPC en tant que service](./media/data-factory-data-processing-using-batch/image1.png)

Les étapes de base du processus sont énoncées ci-dessous. La solution inclut du code et des explications relatives à la génération de la solution de bout en bout.

1.  Configurez Azure Batch avec un pool de nœuds de calcul (machines virtuelles). Vous pouvez spécifier le nombre de nœuds et la taille de chacun d’eux.

2.  Créez une instance Azure Data Factory configurée avec des entités qui représentent respectivement le stockage d’objets blob Azure, le service de calcul Azure Batch, les données en entrée et sortie, ainsi qu’un flux de travail, ou pipeline, d’activités qui déplacent et transforment des données.

3.  Le pipeline Data Factory comprend une activité .NET personnalisée, qui est configurée pour s’exécuter sur le pool de nœuds Azure Batch.

4.  Stockez de grandes quantités de données d’entrée en tant qu’objets blob dans Azure Storage. Les données sont divisées en tranches logiques (généralement basées sur l’heure).

5.  Data Factory copie vers l’emplacement secondaire les données qui seront traitées en parallèle.

6.  Data Factory exécute l’activité personnalisée à l’aide du pool alloué par Batch. Data Factory peut exécuter plusieurs activités simultanément. Chaque activité traite une tranche de données. Les résultats sont stockés dans Azure Storage.

7.  Une fois tous les résultats obtenus, Data Factory les déplace vers un troisième emplacement, soit pour les distribuer via une application, soit pour les traiter avec d’autres outils.

## Solution d’architecture

La solution compte le nombre d’occurrences d’un terme de recherche (« Microsoft ») dans les fichiers d’entrée organisés en série chronologique. Il renvoie le nombre de fichiers de sortie.

**Temps** : si vous maîtrisez Azure, Data Factory et Batch et disposez des composants requis, nous estimons que cette solution vous prendra entre 1 et 2 heures.

## Composants requis

1.  **Abonnement Azure**. Si vous n’êtes pas abonné, vous pouvez créer un compte d’essai gratuit en quelques minutes. Voir [essai gratuit](https://azure.microsoft.com/pricing/free-trial/).

2.  **Compte de stockage Azure** Dans ce didacticiel, vous allez utiliser un compte de stockage Azure pour stocker des données. Si vous ne possédez pas de compte de stockage Azure, voir [Création d’un compte de stockage](../storage/storage-create-storage-account.md#create-a-storage-account). L’exemple de solution utilise un stockage d’objets blob.

3.  Créez un compte [Azure Batch](http://manage.windowsazure.com/) via le **portail Azure**. Voir [Créer et gérer un compte Azure Batch](../batch/batch-account-create-portal.md). Notez la clé et le nom du compte Azure Batch. Vous pouvez également créer un compte Azure Batch à l’aide de l’applet de commande [New-AzureRmBatchAccount](https://msdn.microsoft.com/library/mt603749.aspx). Pour obtenir des instructions détaillées sur l’utilisation de cette applet de commande, voir [Prise en main des applets de commande Azure Batch PowerShell](../batch/batch-powershell-cmdlets-get-started.md).

    L’exemple de solution utilise Azure Batch (indirectement via un pipeline Azure Data Factory) pour traiter des données en parallèle sur un pool de nœuds de calcul, c’est-à-dire une collection gérée de machines virtuelles.

4.  Créez un **pool Azure Batch** comprenant au moins 2 nœuds de calcul.
	1.  Dans le [portail Azure](https://portal.azure.com), cliquez sur **Parcourir** dans le menu de gauche, puis cliquez sur **Comptes Batch**. 
	2. Sélectionnez votre compte Azure Batch pour ouvrir le panneau **Compte Batch**. 
	3. Cliquez sur la vignette **Pools**.
	4. Dans le panneau **Pools**, cliquez sur le bouton Ajouter de la barre d’outils pour ajouter un pool.
		1. Entrez un ID pour le pool (**ID du pool**). Notez l’**ID du pool**, car vous en aurez besoin lors de la création de la solution Data Factory. 
		2. Spécifiez **Windows Server 2012 R2** pour le paramètre de famille du système d’exploitation.
		3. Sélectionnez le **niveau tarifaire du nœud**. 
		3. Entrez **2** comme valeur du paramètre **Target Dedicated** (Cibles dédiées).
		4. Entrez **2** comme valeur du paramètre **Max tasks per node** (Nombre maximal de tâches par nœud).
	5. Cliquez sur **OK** pour créer le pool. 
 	 
5.  [Azure Storage Explorer 6 (outil)](https://azurestorageexplorer.codeplex.com/) ou [CloudXplorer](http://clumsyleaf.com/products/cloudxplorer) (à partir de logiciels ClumsyLeaf). Il s’agit d’outils d’interface graphique utilisateur permettant de consulter et de modifier les données de vos projets Azure Storage, notamment les journaux de vos applications hébergées dans le cloud.

    1.  Créez un conteneur nommé **mycontainer** avec un accès privé (par d’accès anonyme).

    2.  Si vous utilisez **CloudXplorer**, créez des dossiers et sous-dossiers avec la structure suivante :

 		![](./media/data-factory-data-processing-using-batch/image3.png)

		 Les éléments **inputfolder** et **outputfolder** sont des dossiers de niveau supérieur dans **mycontainer,** et **inputfolder** comprend des sous-dossiers horodatés (AAAA-MM-JJ-HH).

		 Si vous utilisez **Azure Storage Explorer**, à l’étape suivante, vous devez charger les fichiers nommés inputfolder/2015-11-16-00/file.txt, inputfolder/2015-11-16-01/file.txt, et ainsi de suite. Cette opération crée automatiquement les dossiers.

	3.  Créez sur votre ordinateur un fichier texte **file.txt** contenant le mot clé **Microsoft**. Par exemple, « activité de test personnalisé Microsoft ».

	4.  Chargez le fichier dans les dossiers d’entrée suivants du stockage d’objets blob Azure.

		![](./media/data-factory-data-processing-using-batch/image4.png)

	 	Si vous utilisez **Azure Storage Explorer**, chargez le fichier **file.txt** dans **mycontainer**. Cliquez sur **Copy** (Copier) dans la barre d’outils pour créer une copie de l’objet blob. Dans la boîte de dialogue **Copy Blob** (Copier l’objet blob), remplacez le **destination blob name** (nom d’objet blob de destination) par **inputfolder/2015-11-16-00/file.txt.** Répétez l’opération pour créer inputfolder/2015-11-16-01/file.txt, inputfolder/2015-11-16-02/file.txt, inputfolder/2015-11-16-03/file.txt, inputfolder/2015-11-16-04/file.txt, et ainsi de suite. Cette opération crée automatiquement les dossiers.

	3.  Créez un autre conteneur nommé **customactivitycontainer**. Vous allez charger le fichier zip d’activité personnalisée dans ce conteneur.

6.  **Microsoft Visual Studio 2012 ou version ultérieure** (pour créer l’activité Batch personnalisée à utiliser dans la solution Data Factory).

## Principales étapes pour créer la solution

1.  Créez une activité personnalisée à utiliser dans la solution Data Factory. L’activité personnalisée contient la logique de traitement des données.

    1.  Dans Visual Studio (ou un éditeur de code de votre choix), créez un projet de bibliothèque de classes .NET, ajoutez le code de traitement des données d’entrée et compilez le projet.

    2.  Compressez les fichiers binaires et le fichier (facultatif) PDB dans le dossier de sortie.

    3.  Téléchargez le fichier compressé dans un stockage d’objets blob Azure.

	La procédure détaillée est décrite dans la section [Création de l’activité personnalisée](#_Coding_the_custom).

2.  Créez une fabrique de données Azure qui utilise l’activité personnalisée :

    1.  Créer une fabrique de données Azure.

    2.  créez des services liés.

        1.  StorageLinkedService : fournit les informations d’identification du stockage permettant d’accéder aux objets blob.

        2.  AzureBatchLinkedService : Spécifie Azure Batch comme calcul.

    3.  Créez les jeux de données.

        1.  InputDataset : spécifie le conteneur de stockage et le dossier contenant les objets blob d’entrée.

        2.  OutputDataset : spécifie le conteneur de stockage et le dossier contenant les objets blob de sortie.

    4.  Créez un pipeline qui utilise l’activité personnalisée.

    5.  Exécutez et testez le pipeline.

    6.  Déboguez le pipeline.

 	Les étapes détaillées sont décrites dans la section [Création de la fabrique de données](#create-the-data-factory).

## Création de l’activité personnalisée

L’activité personnalisée de Data Factory est au cœur de cet exemple de solution. L’exemple de solution utilise Azure Batch pour exécuter l’activité personnalisée. Pour les informations de base relatives au développement d’activités personnalisées et leur utilisation dans des pipelines Azure Data Factory, voir [Utilisation des activités personnalisées dans un pipeline Azure Data Factory](data-factory-use-custom-activities.md).

Pour créer une activité personnalisée .NET utilisable dans un pipeline Azure Data Factory, vous devez créer un projet de **bibliothèque de classes .NET** contenant une classe qui implémente cette interface **IDotNetActivity**. Cette interface possède une seule méthode : **Execute**. Voici la signature de la méthode :

	public IDictionary<string, string> Execute(
	            IEnumerable<LinkedService> linkedServices,
	            IEnumerable<Dataset> datasets,
	            Activity activity,
	            IActivityLogger logger)

La méthode comporte quelques composants clés qu’il est important d’assimiler.

-   La méthode accepte quatre paramètres :

    1.  **linkedServices** : liste énumérable de services liés relie les sources de données d’entrée/sortie (par exemple, Azure Blob Storage) à la fabrique de données. Dans cet exemple, il s’agit du seul service lié de type Azure Storage utilisé à la fois pour les données d’entrée et de sortie.

    2.  **datasets** : liste énumérable de jeux de données. Vous pouvez utiliser ce paramètre pour obtenir les emplacements et les schémas définis par les jeux de données d’entrée et de sortie.

    3.  **activity** : ce paramètre représente l’entité de calcul actuelle (dans ce cas, un service Azure Batch).

    4.  **logger** : permet d’écrire des commentaires de débogage qui apparaîtront en tant que journal « utilisateur » pour le pipeline.

-   La méthode retourne un dictionnaire qui peut être utilisé pour enchaîner ultérieurement des activités personnalisées. Cette fonctionnalité n’étant pas encore implémentée, seule un dictionnaire vide est retourné par la méthode.

### Procédure : Création de l’activité personnalisée

1.  Créez un projet de bibliothèque de classes .NET dans Visual Studio 2013.

    1.  Lancez **Visual Studio 2012**/**2013/2015**.

    2.  Cliquez sur **Fichier**, pointez le curseur de la souris sur **Nouveau**, puis cliquez sur **Projet**.

    3.  Développez **Modèles**, puis sélectionnez **Visual C#**. Dans cette procédure pas à pas, vous utilisez C#, mais vous pouvez utiliser un autre langage .NET pour développer l’activité personnalisée.

    4.  Sélectionnez **Bibliothèque de classes** dans la liste des types de projet, sur la droite.

    5.  Entrez **MyDotNetActivity** dans le champ **Nom**.

    6.  Sélectionnez **C:\\ADF** comme **emplacement**. Créez le dossier **ADF** s’il n’existe pas.

    7.  Cliquez sur **OK** pour créer le projet.

2.  Cliquez sur **Outils**, pointez le curseur de la souris sur **Gestionnaire de package NuGet**, puis cliquez sur **Console du Gestionnaire de package**.

3.  Dans la **Console du gestionnaire de package**, exécutez la commande suivante pour importer l’élément **Microsoft.Azure.Management.DataFactories**.

			Install-Package Microsoft.Azure.Management.DataFactories

4.  Importez le package NuGet **Azure Storage** dans le projet. Vous en avez besoin, car vous allez utiliser l’API de stockage d’objets Blob.

		Install-Package Azure.Storage

5.  Ajoutez les instructions **using** suivantes au fichier source du projet.

		using System.IO;
		using System.Globalization;
		using System.Diagnostics;
		using System.Linq;

		using Microsoft.Azure.Management.DataFactories.Models;
		using Microsoft.Azure.Management.DataFactories.Runtime;

		using Microsoft.WindowsAzure.Storage;
		using Microsoft.WindowsAzure.Storage.Blob;

6.  Remplacez le nom de l’**espace de noms** par **MyDotNetActivityNS**.

		namespace MyDotNetActivityNS

7.  Remplacez le nom de la classe par **MyDotNetActivity** et dérivez-le de l’interface **IDotNetActivity**, comme indiqué ci-dessous.

		public class MyDotNetActivity : IDotNetActivity

8.  Implémentez (ajoutez) la méthode **Execute** de l’interface **IDotNetActivity** dans la classe **MyDotNetActivity** et copiez l’exemple de code suivant dans la méthode. Pour une explication de la logique utilisée dans cette méthode, voir la section [Méthode Execute](#execute-method).

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
            // create a new blob and upload the output text.
            CloudBlockBlob outputBlob = new CloudBlockBlob(outputBlobUri, outputStorageAccount.Credentials);
            logger.Write("Writing {0} to the output blob", output);
            outputBlob.UploadText(output);

			// The dictionary can be used to chain custom activities together in the future.
			// This feature is not implemented yet, so just return an empty dictionary.
            return new Dictionary<string, string>();
        }


9.  Ajoutez les méthodes d’assistance suivantes à la classe. Ces méthodes sont appelées par la méthode **Execute**. Plus important encore, la méthode **Calculate** isole le code qui effectue une itération dans chaque objet blob.

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
		/// and prepares the output text that will be written to the output blob.
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
		            output += string.Format("{0} occurrences(s) of the search term "{1}" were found in the file {2}.\r\n", wordCount, searchTerm, inputBlob.Name);
		        }
		    }
		    return output;
		}


	La méthode **GetFolderPath** renvoie le chemin d’accès au dossier vers lequel pointe le jeu de données, et la méthode **GetFileName** renvoie le nom de l’objet blob/fichier vers lequel pointe le jeu de données.

	    "name": "InputDataset",
	    "properties": {
	        "type": "AzureBlob",
	        "linkedServiceName": "StorageLinkedService",
	        "typeProperties": {
	            "fileName": "file.txt",
	            "folderPath": "mycontainer/inputfolder/{Year}-{Month}-{Day}-{Hour}",


	La méthode **Calculate** calcule le nombre d’instances du mot clé **Microsoft** dans les fichiers d’entrée (objets blob du dossier). Le terme de recherche (« Microsoft ») est codé en dur dans le code.

10.  Compilez le projet. Cliquez sur l’option **Générer** du menu, puis sur **Générer la solution**.

11.  Lancez l’**Explorateur Windows** et accédez au dossier **bin\\debug** ou **bin\\release** (selon le type de build).

12.  Créez un fichier zip **MyDotNetActivity.zip** contenant tous les fichiers binaires dans le dossier **\\bin\\Debug**. Vous pouvez également inclure le fichier **MyDotNetActivity.pdb** afin d’obtenir des détails supplémentaires, tels que le numéro de ligne du code source à l’origine du problème en cas de défaillance.

	![](./media/data-factory-data-processing-using-batch/image5.png)

13.  Chargez le fichier **MyDotNetActivity.zip** en tant qu’objet blob dans le conteneur d’objets blob **customactvitycontainer** du stockage d’objets blob Azure qu’utilise le service lié **StorageLinkedService** dans **ADFTutorialDataFactory**. Si nécessaire, créez le conteneur d’objets blob **customactivitycontainer**.

### Méthode Execute

Cette section fournit des informations et remarques supplémentaires concernant le code contenu dans la méthode Execute.

1.	Les membres nécessaires pour l’itération dans la collection d’entrée se trouvent dans l’espace de noms [Microsoft.WindowsAzure.Storage.Blob](https://msdn.microsoft.com/library/azure/microsoft.windowsazure.storage.blob.aspx). L’itération dans la collection d’objets blob requiert d’utiliser la classe **BlobContinuationToken**. Vous devez utiliser une boucle do-while en utilisant le jeton pour sortir de la boucle. Pour plus d’informations, voir [Utilisation du stockage d’objets blob à partir de .NET](../storage/storage-dotnet-how-to-use-blobs.md). Une boucle de base est illustrée ici :

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

	Pour plus de détails, voir la documentation sur la méthode [ListBlobsSegmented](https://msdn.microsoft.com/library/jj717596.aspx).

2.	Le code permettant de parcourir l’ensemble d’objets blob s’inscrit logiquement dans la boucle do-while. Dans la méthode **Execute**, la boucle do-while transmet la liste d’objets blob à une méthode nommée **Calculate**. La méthode renvoie une variable de chaîne nommée **output**, qui correspond au résultat de l’itération dans tous les objets blob du segment.

	Elle retourne le nombre d’occurrences du terme de recherche (**Microsoft**) dans l’objet blob transmis à la méthode **Calculate**.

		output += string.Format("{0} occurrences of the search term "{1}" were found in the file {2}.\r\n", wordCount, searchTerm, inputBlob.Name);

3.	Une fois l’exécution de la méthode **Calculate** terminée, vous devez écrire le résultat dans un nouvel objet blob. Par conséquent, pour chaque ensemble d’objets blob traité, un nouvel objet blob peut être écrit avec les résultats correspondants. Pour écrire dans un nouvel objet blob, commencez par rechercher le jeu de données de sortie.

		// Get the output dataset using the name of the dataset matched to a name in the Activity output collection.
		Dataset outputDataset = datasets.Single(dataset => dataset.Name == activity.Outputs.Single().Name);

4.	Le code appelle également une méthode d’assistance, **GetFolderPath**, pour récupérer le chemin d’accès au dossier (nom du conteneur de stockage).

		folderPath = GetFolderPath(outputDataset);

	La méthode **GetFolderPath** effectue un cast de l’objet DataSet dans un AzureBlobDataSet, qui comporte une propriété nommée FolderPath.

		AzureBlobDataset blobDataset = dataArtifact.Properties.TypeProperties as AzureBlobDataset;

		return blobDataset.FolderPath;

5.	Le code appelle la méthode **GetFileName** pour récupérer le nom du fichier (nom de l’objet blob). Le code est similaire au code présenté ci-dessus pour obtenir le chemin d’accès au dossier.

		AzureBlobDataset blobDataset = dataArtifact.Properties.TypeProperties as AzureBlobDataset;

		return blobDataset.FileName;

6.	Le nom du fichier est écrit grâce à la création d’un nouvel objet URI. Le constructeur d’URI utilise la propriété **BlobEndpoint** pour renvoyer le nom du conteneur. Le nom de fichier et le chemin du dossier sont ajoutés pour construire l’URI de l’objet blob de sortie.

		// Write the name of the file.
		Uri outputBlobUri = new Uri(outputStorageAccount.BlobEndpoint, folderPath + "/" + GetFileName(outputDataset));

7.	Le nom du fichier ayant été écrit, vous pouvez à présent écrire la chaîne de sortie de la méthode **Calculate** dans un nouvel objet blob :

		// Create a new blob and upload the output text.
		CloudBlockBlob outputBlob = new CloudBlockBlob(outputBlobUri, outputStorageAccount.Credentials);
		logger.Write("Writing {0} to the output blob", output);
		outputBlob.UploadText(output);


## Création de la fabrique de données

Dans la section [Création de l’activité personnalisée](#create-the-custom-activity), vous avez créé une activité personnalisée et chargé le fichier zip contenant les fichiers binaires et le fichier PDB dans un conteneur d’objets blob Azure. Dans cette section, vous allez créer une **fabrique de données** Azure avec un **pipeline** qui utilise l’**activité personnalisée**.

Le jeu de données d’entrée de l’activité personnalisée représente les objets blob (fichiers) contenus dans le dossier d’entrée (mycontainer\\inputfolder) du stockage d’objets blob. Le jeu de données de sortie de l’activité personnalisée représente les objets blob de sortie contenus dans le dossier de sortie (mycontainer\\outputfolder) du stockage d’objets blob.

Vous allez déposer un ou plusieurs fichiers dans les dossiers d’entrée :

	mycontainer -> inputfolder
		2015-11-16-00
		2015-11-16-01
		2015-11-16-02
		2015-11-16-03
		2015-11-16-04

Par exemple, déposez un fichier (file.txt) avec le contenu suivant dans chacun des dossiers.

	test custom activity Microsoft test custom activity Microsoft

Le dossier d’entrée correspond à une tranche dans Azure Data Factory, même s’il contient plusieurs fichiers. Lorsque chaque tranche est traitée par le pipeline, l’activité personnalisée effectue une itération dans tous les objets blob du dossier d’entrée pour la tranche en question.

Vous allez voir cinq fichiers de sortie avec le même contenu. Par exemple, le fichier de sortie résultant du traitement du fichier figurant dans le dossier 2015-11-16-00 a le contenu suivant :

	2 occurrences(s) of the search term "Microsoft" were found in the file inputfolder/2015-11-16-00/file.txt.

Si vous déposez plusieurs fichiers (file.txt, file2.txt, file3.txt) ayant le même contenu dans le dossier d’entrée, le contenu suivant apparaît dans le fichier de sortie. Dans cet exemple, chaque dossier (2015-11-16-00, etc.) correspond à une tranche, même si le dossier contient plusieurs fichiers d’entrée.

	2 occurrences(s) of the search term "Microsoft" were found in the file inputfolder/2015-11-16-00/file.txt.
	2 occurrences(s) of the search term "Microsoft" were found in the file inputfolder/2015-11-16-00/file2.txt.
	2 occurrences(s) of the search term "Microsoft" were found in the file inputfolder/2015-11-16-00/file3.txt.


Notez que le fichier de sortie comprend désormais trois lignes, une par fichier d’entrée (objet blob) dans le dossier associé à la tranche (2015-11-16-00).

Une tâche est créée pour chaque exécution d’activité. Dans cet exemple, le pipeline ne contient qu’une seule activité. Quand le pipeline traite une tranche, l’activité personnalisée s’exécute sur Azure Batch pour traiter la tranche. Étant donné qu’il y a 5 tranches (chacune pouvant comporter plusieurs objets blob ou fichiers), 5 tâches sont créées dans Azure Batch. Quand une tâche s’exécute sur Batch, c’est en fait l’activité personnalisée qui s’exécute.

La procédure pas à pas suivante fournit des détails supplémentaires.

### Étape 1 : Créer la fabrique de données

1.  Une fois connecté au [portail Azure](https://portal.azure.com/), procédez comme suit :

    1.  Cliquez sur **NOUVEAU** dans le menu de gauche.

    2.  Dans le panneau **Nouveau**, cliquez sur **Données et analyse**.

    3.  Cliquez sur **Data Factory** dans le panneau **Analyse des données**.

2.  Dans le panneau **Nouvelle fabrique de données**, spécifiez le nom **CustomActivityFactory**. Le nom de la fabrique de données Azure doit être un nom global unique. Si l’erreur **Data factory name “CustomActivityFactory” is not available** (Le nom de la fabrique de données « CustomActivityFactory » n’est pas disponible) s’affiche, changez le nom de la fabrique de données (par exemple, **votrenomCustomActivityFactory**), puis tentez de la recréer.

3.  Cliquez sur **NOM DU GROUPE DE RESSOURCES**, puis sélectionnez un groupe de ressources ou créez-en un.

4.  Veillez à utiliser l’abonnement et la région correspondant à ceux dans lesquels vous voulez créer la fabrique de données.

5.  Dans le panneau **Nouvelle fabrique de données**, cliquez sur **Créer**.

6.  La fabrique de données en cours de création apparaît dans le **Tableau de bord** du portail Azure.

7.  Une fois la fabrique de données créée, vous verrez la page correspondante indiquant son contenu.

 ![](./media/data-factory-data-processing-using-batch/image6.png)

### Étape 2 : Créer des services liés

Les services liés se chargent de lier des magasins de données ou des services de calcul à une fabrique de données Azure. Au cours de cette étape, vous allez lier vos comptes **Azure Storage** et **Azure Batch** à votre fabrique de données.

#### Créer le service lié Azure Storage

1.  Cliquez sur la vignette **Créer et déployer** dans le panneau **DATA FACTORY** de **CustomActivityFactory**. Cette action lance l’éditeur Data Factory Editor.

2.  Dans la barre de commandes, cliquez sur **Nouvelle banque de données**, puis choisissez **Azure Storage**. Le script JSON de création d’un service lié Microsoft Azure Storage doit apparaître dans l’éditeur.

    ![](./media/data-factory-data-processing-using-batch/image7.png)

3.  Remplacez **account name** par le nom de votre compte de stockage Azure, et **account key** par sa clé d’accès. Pour savoir comment obtenir votre clé d’accès de stockage, voir [Affichage, copie et régénération de clés d’accès de stockage](../storage/storage-create-storage-account.md#view-copy-and-regenerate-storage-access-keys).

4.  Cliquez sur l’option **Déployer** de la barre de commandes pour déployer le service lié.

    ![](./media/data-factory-data-processing-using-batch/image8.png)

#### Créer un service lié Azure Batch

Au cours de cette étape, vous allez créer un service lié pour votre compte **Azure Batch**, qui sera utilisé pour exécuter l’activité personnalisée Data Factory.

1.  Dans la barre de commandes, cliquez sur **Nouveau calcul**, puis choisissez **SQL Azure**. Le script JSON pour la création d’un service lié Azure Batch doit apparaître dans l’éditeur.

2.  Dans le script JSON :

    1.  Remplacez **nom de compte** par le nom de votre compte Azure Batch.

    2.  Remplacez **clé d’accès** avec la clé d’accès du compte Azure Batch.

    3.  Entrez l’ID du pool pour la propriété **poolName**.** pour cette propriété, vous pouvez spécifier un nom de pool ou un ID de pool

    4.  Entrez l’URI du lot pour la propriété JSON **batchUri**.
    
		> [AZURE.IMPORTANT] L’**URL** figurant dans le **panneau du compte Azure Batch** est au format suivant : <nomducompte>.<région>.batch.azure.com. Pour la propriété **batchUri** dans le fichier JSON, vous devez **supprimer « nomducompte ».** de l’URL. Par exemple : "batchUri": "https://eastus.batch.azure.com".

        ![](./media/data-factory-data-processing-using-batch/image9.png)

		Pour la propriété **poolName**, vous pouvez également spécifier l’ID du pool au lieu du nom du pool.

		> [AZURE.NOTE] Le service Data Factory ne prend pas en charge l’option à la demande pour Azure Batch contrairement à HDInsight. Vous pouvez uniquement utiliser votre propre pool Azure Batch dans une fabrique de données Azure.

    5.  Pour la propriété **LinkedServiceName**, spécifiez **StorageLinkedService**. Vous avez créé ce service lié à l’étape précédente. Ce stockage est utilisé en tant que zone de transit pour les fichiers et les journaux.

3.  Cliquez sur l’option **Déployer** de la barre de commandes pour déployer le service lié.

### Étape 3 : Créer les jeux de données

Dans cette étape, vous allez créer des jeux de données pour représenter les données d’entrée et de sortie.

#### Créer le jeu de données d’entrée

1.  Dans l’**éditeur** de Data Factory, cliquez sur le bouton **Nouveau jeu de données** de la barre d’outils et sélectionnez **Stockage d’objets blob Azure** dans le menu déroulant.

2.  Remplacez le script JSON affiché dans le volet droit par l’extrait de code JSON suivant :

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


	 Plus loin dans cette procédure pas à pas, vous allez créer un pipeline associé à l’heure de début 2015-11-16T00:00:00Z et à l’heure de fin 2015-11-16T05:00:00Z. Ce pipeline est planifié pour produire des données **toutes les heures**, ce qui signifie qu’il y aura 5 tranches d’entrée/sortie (comprises entre **00**: 00:00 et **05**: 00:00).

	 Les paramètres **frequency** et **interval** pour le jeu de données d’entrée sont respectivement définis sur **Hour** et sur **1**, ce qui signifie que la tranche d’entrée est disponible toutes les heures.

	 Voici les heures de début de chaque tranche, représentées par la variable système **SliceStart** dans l’extrait de code JSON ci-dessus.

	| **Tranche** | **Heure de début** |
	|-----------|-------------------------|
	| 1 | 2015-11-16T**00**:00:00 |
	| 2 | 2015-11-16T**01**:00:00 |
	| 3 | 2015-11-16T**02**:00:00 |
	| 4 | 2015-11-16T**03**:00:00 |
	| 5 | 2015-11-16T**04**:00:00 |

	 La valeur **folderPath** est calculée à l’aide de la partie année, mois, jour et heure de l’heure de début de la tranche (**SliceStart**). Par conséquent, voici comment un dossier d’entrée est mappé à une tranche.

	| **Tranche** | **Heure de début** | **Dossier d’entrée** |
	|-----------|-------------------------|-------------------|
	| 1 | 2015-11-16T**00**:00:00 | 2015-11-16-**00** |
	| 2 | 2015-11-16T**01**:00:00 | 2015-11-16-**01** |
	| 3 | 2015-11-16T**02**:00:00 | 2015-11-16-**02** |
	| 4 | 2015-11-16T**03**:00:00 | 2015-11-16-**03** |
	| 5 | 2015-11-16T**04**:00:00 | 2015-11-16-**04** |

3.  Dans la barre d’outils, cliquez sur **Déployer** pour créer et déployer la table **InputDataset**.

#### Créer un jeu de données de sortie

Au cours de cette étape, vous allez créer un autre jeu de données de type AzureBlob pour représenter les données de sortie.

1.  Dans l’**éditeur** de Data Factory, cliquez sur le bouton **Nouveau jeu de données** de la barre d’outils et sélectionnez **Stockage d’objets blob Azure** dans le menu déroulant.

2.  Remplacez le script JSON affiché dans le volet droit par l’extrait de code JSON suivant :

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


 	Un objet blob/fichier de sortie est généré pour chaque tranche d’entrée. Voici la procédure de nommage des fichiers de sortie pour chaque tranche. Tous les fichiers de sortie sont générés dans un seul dossier de sortie : **mycontainer\\outputfolder**.


	| **Tranche** | **Heure de début** | **Fichier de sortie** |
	|-----------|-------------------------|-----------------------|
	| 1 | 2015-11-16T**00**:00:00 | 2015-11-16-**00.txt** |
	| 2 | 2015-11-16T**01**:00:00 | 2015-11-16-**01.txt** |
	| 3 | 2015-11-16T**02**:00:00 | 2015-11-16-**02.txt** |
	| 4 | 2015-11-16T**03**:00:00 | 2015-11-16-**03.txt** |
	| 5 | 2015-11-16T**04**:00:00 | 2015-11-16-**04.txt** |

	 N’oubliez pas que tous les fichiers figurant dans un dossier d’entrée (par exemple, 2015-11-16-00) font partie d’une tranche associée à l’heure de début (2015-11-16-00). Lorsque cette tranche est traitée, l’activité personnalisée parcourt chaque fichier et génère une ligne dans le fichier de sortie avec le nombre d’occurrences du terme de recherche (« Microsoft »). Si le dossier 2015-11-16-00 contient trois fichiers, le fichier de sortie 2015-11-16-00.txt comprend trois lignes.

3.  Dans la barre d’outils, cliquez sur **Déployer** pour créer et déployer la table **OutputDataset**.

### Étape 4 : Créer et exécuter le pipeline avec une activité personnalisée

Au cours de cette étape, vous allez créer un pipeline comprenant une seule activité, l’activité personnalisée que vous avez créé précédemment.

> [AZURE.IMPORTANT] Si vous n’avez pas chargé le fichier **file.txt** dans les dossiers d’entrée du conteneur d’objets blob, veuillez le faire avant de créer le pipeline. La propriété **isPaused** étant définie sur false dans le fichier JSON du pipeline, le pipeline s’exécute immédiatement, car la date de **début** se situe dans le passé.

1.  Dans Data Factory Editor, cliquez sur **Nouveau pipeline** dans la barre de commandes. Si vous ne voyez pas apparaître la commande, cliquez sur **... (points de suspension)** pour l’afficher.

2.  Remplacez le script JSON affiché dans le volet droit par le script JSON suivant.

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

	Notez les points suivants :

	-   Le pipeline ne comprend qu’une seule activité du type **DotNetActivity**.

	-   Le paramètre **AssemblyName** est défini sur le nom de la DLL **MyDotNetActivity.dll**.

	-   Le paramètre **EntryPoint** est défini sur **MyDotNetActivityNS.MyDotNetActivity**. Il s’agit essentiellement de \<namespace\>.\<classname\> dans votre code.

	-   **PackageLinkedService** est défini sur **StorageLinkedService**, qui pointe vers le stockage d’objets blob contenant le fichier .zip de l’activité personnalisée. Si vous utilisez des comptes de stockage différents pour les fichiers d’entrée/sortie et le fichier zip de l’activité personnalisée, vous devez créer un autre service lié Azure Storage. Cet article suppose que vous utilisez le même compte Azure Storage.

	-   Le paramètre **PackageFile** est défini sur **customactivitycontainer/MyDotNetActivity.zip**. Il est au format \<containerforthezip\>/\<nameofthezip.zip\>.

	-   L’activité personnalisée utilise **InputDataset** comme entrée et **OutputDataset** comme sortie.

	-   La propriété **linkedServiceName** de l’activité personnalisée pointe vers **AzureBatchLinkedService**, ce qui indique à Azure Data Factory que l’activité personnalisée doit s’exécuter sur Azure Batch.

	-   Le paramètre **concurrency** est important. Si vous utilisez la valeur par défaut, 1, même si vous avez plusieurs nœuds de calcul dans le pool Azure Batch, les tranches sont traitées successivement. Par conséquent, vous ne profitez pas de la capacité de traitement en parallèle d’Azure Batch. Si vous définissez **concurrency** sur une valeur plus élevée, par exemple 2, deux tranches (correspondant à deux tâches dans Azure Batch) peuvent être traitées simultanément, auquel cas les deux machines virtuelles du pool Azure Batch sont utilisées. Vous devez par conséquent définir la propriété concurrency de façon appropriée.

	-   Par défaut, une seule tâche (tranche) est exécutée sur une machine virtuelle à un moment donné. C’est pourquoi, par défaut, le paramètre **Maximum tasks per VM** (nombre maximal de tâches par machine virtuelle) est défini sur 1 pour un pool Azure Batch. En rapport avec les conditions préalables, vous avez créé un pool pour lequel cette propriété est définie sur 2, de sorte que deux tranches Data Factory peuvent s’exécuter simultanément sur une machine virtuelle.


	-   Par défaut, la propriété **isPaused** est définie sur false. Le pipeline s’exécute immédiatement dans cet exemple car les tranches débutent à une date antérieure. Vous pouvez définir cette propriété sur true pour suspendre le pipeline et lui réaffecter la valeur false pour reprendre l’exécution.

	-   L’écart entre le **début** et la **fin** du pipeline étant de cinq heures, et une tranche étant produite toutes les heures, le pipeline produit cinq tranches.

3.  Cliquez sur **Déployer** dans la barre de commandes pour déployer le pipeline.

### Étape 5 : Tester le pipeline

Au cours de cette étape, vous allez tester le pipeline en déposant des fichiers dans les dossiers d’entrée. Commençons par tester le pipeline avec un fichier par dossier d’entrée.

1.  Dans le panneau Data Factory du portail Azure, cliquez sur **Diagramme**.

    ![](./media/data-factory-data-processing-using-batch/image10.png)

2.  Dans la vue de diagramme, double-cliquez sur le jeu de données d’entrée **InputDataset**.

    ![](./media/data-factory-data-processing-using-batch/image11.png)

3.  Vous devriez voir le panneau **InputDataset** avec les cinq tranches prêtes. Notez que l’**HEURE DE DÉBUT DE LA TRANCHE** et l’**HEURE DE FIN DE LA TRANCHE** pour chaque tranche.

    ![](./media/data-factory-data-processing-using-batch/image12.png)

4.  Dans la **vue de diagramme**, cliquez sur **OutputDataset**.

5.  Si elles ont déjà été produites, les 5 tranches de sortie doivent être à l’état « prêt ».

    ![](./media/data-factory-data-processing-using-batch/image13.png)

6.  Pour afficher les **tâches** associées aux **tranches** et voir la machine virtuelle sur laquelle chaque tranche a été exécutée, utilisez [Azure Batch Explorer](http://blogs.technet.com/b/windowshpc/archive/2015/01/20/azure-batch-explorer-sample-walkthrough.aspx). Vous pouvez voir qu’un travail est créé avec le nom **adf-<poolname>**. Ce travail comprend une tâche pour chaque tranche. Dans cet exemple, il y aura cinq tranches, donc cinq tâches dans Azure Batch. Avec la propriété **concurrency** définie sur **5** dans le fichier JSON du pipeline dans Azure Data Factory, et le paramètre **Maximum tasks per VM** (Nombre maximal de tâches par machine virtuelle) défini sur **2** dans le pool Azure Batch avec **2** machines virtuelles, les tâches ont été exécutées très rapidement (voir l’heure **Créé**).

    ![](./media/data-factory-data-processing-using-batch/image14.png)

	> [AZURE.NOTE] Téléchargez le code source de l’[outil Explorateur Azure Batch][batch-explorer], compilez-le et servez-vous en pour créer et surveiller les pools Batch. Consultez le billet de blog [Azure Batch Explorer Sample Walkthrough][batch-explorer-walkthrough] pour obtenir des instructions détaillées sur l’utilisation d’Azure Batch Explorer.

7.  Les fichiers de sortie devraient apparaître dans le dossier **outputfolder** de **mycontainer** à l’intérieur de votre stockage d’objets blob.

    ![](./media/data-factory-data-processing-using-batch/image15.png)

    Vous devriez voir cinq fichiers de sortie, un par tranche d’entrée. Chaque fichier de sortie doit avoir contenu similaire à ce qui suit :

    	2 occurrences(s) of the search term "Microsoft" were found in the file inputfolder/2015-11-16-00/file.txt.

    Le diagramme suivant illustre la manière dont les tranches Data Factory sont mappées à des tâches dans Azure Batch. Dans cet exemple, une tranche n’est exécutée qu’une seule fois.

    ![](./media/data-factory-data-processing-using-batch/image16.png)

8.  À présent, essayons avec plusieurs fichiers dans un dossier. Créez des fichiers **file2.txt**, **file3.txt**, **file4.txt** et **file5.txt** ayant le même contenu que le fichier file.txt dans le dossier **2015-11-06-01**.

9.  Dans le dossier de sortie **supprimez** le fichier de sortie **2015-11-16-01.txt**.

10. À présent, dans le panneau **OutputDataset**, cliquez avec le bouton droit sur la tranche dont l’**HEURE DE DÉBUT DE LA TRANCHE** a la valeur **16/11/2015 01:00:00 AM**, puis cliquez sur **Exécuter** pour réexécuter/re-traiter la tranche. Maintenant, la tranche comprend cinq fichiers au lieu d’un seul.

    ![](./media/data-factory-data-processing-using-batch/image17.png)

11. Une fois la tranche exécutée, quand son état est **Prêt**, vérifiez le contenu de son fichier de sortie (**2015-11-16-01.txt**) dans le dossier **outputfolder** de **mycontainer** à l’intérieur votre stockage d’objets blob. Il doit y avoir une ligne pour chaque fichier de la tranche.

		2 occurrences(s) of the search term "Microsoft" were found in the file inputfolder/2015-11-16-01/file.txt.
		2 occurrences(s) of the search term "Microsoft" were found in the file inputfolder/2015-11-16-01/file2.txt.
		2 occurrences(s) of the search term "Microsoft" were found in the file inputfolder/2015-11-16-01/file3.txt.
		2 occurrences(s) of the search term "Microsoft" were found in the file inputfolder/2015-11-16-01/file4.txt.
		2 occurrences(s) of the search term "Microsoft" were found in the file inputfolder/2015-11-16-01/file5.txt.


    **Remarque :** si vous n’avez pas supprimé le fichier de sortie 2015-11-16-01.txt avant d’essayer avec cinq fichiers d’entrée, vous devez voir une ligne pour l’exécution de la tranche précédente, et cinq lignes pour l’exécution de la tranche actuelle. Par défaut, le contenu est ajouté au fichier de sortie s’il existe.

## Déboguer le pipeline

Le débogage consiste à utiliser quelques techniques de base :

1.  Si la tranche d’entrée n’est pas définie sur **Prêt**, vérifiez que la structure du dossier d’entrée est correcte et que le fichier file.txt est présent dans les dossiers d’entrée.

    ![](./media/data-factory-data-processing-using-batch/image3.png)

2.  Dans la méthode **Execute** de votre activité personnalisée, utilisez l’objet **IActivityLogger** pour journaliser les informations qui vous aideront à résoudre d’éventuels problèmes. Les messages consignés s’afficheront dans le fichier user\_0.log.

    Dans le panneau **OutputDataset**, cliquez sur la tranche pour afficher le panneau **TRANCHE DE DONNÉES** correspondant à cette tranche. Les **activités exécutées** pour cette tranche s’affichent. Vous devez normalement voir une exécution d’activité pour la tranche. Si vous cliquez sur **Exécuter** dans la barre de commandes, vous pouvez démarrer une autre exécution d’activité pour la même tranche.

    Lorsque vous cliquez sur l’exécution d’activité, le panneau **DÉTAILS DE L’EXÉCUTION D’ACTIVITÉ** s’affiche avec une liste de fichiers journaux. Les messages journalisés figurent dans le fichier **user\_0.log**. Lorsqu’une erreur se produit, vous verrez trois exécutions d’activité car le nombre de tentatives est défini sur 3 dans le script JSON du pipeline et de l’activité. Lorsque vous cliquez sur l’exécution de l’activité, vous accédez aux fichiers journaux qui vous permettront de résoudre l’erreur.

    ![](./media/data-factory-data-processing-using-batch/image18.png)

    Dans la liste des fichiers journaux, cliquez sur le fichier **user-0.log**. Le volet droit affiche les résultats de l’utilisation de la méthode **IActivityLogger.Write**.

    ![](./media/data-factory-data-processing-using-batch/image19.png)

    Vous devez également examiner le fichier system-0.log pour voir s’il contient des exceptions et messages d’erreur système.

		Trace\_T\_D\_12/6/2015 1:43:35 AM\_T\_D\_\_T\_D\_Verbose\_T\_D\_0\_T\_D\_Loading assembly file MyDotNetActivity...

		Trace\_T\_D\_12/6/2015 1:43:35 AM\_T\_D\_\_T\_D\_Verbose\_T\_D\_0\_T\_D\_Creating an instance of MyDotNetActivityNS.MyDotNetActivity from assembly file MyDotNetActivity...

		Trace\_T\_D\_12/6/2015 1:43:35 AM\_T\_D\_\_T\_D\_Verbose\_T\_D\_0\_T\_D\_Executing Module

		Trace\_T\_D\_12/6/2015 1:43:38 AM\_T\_D\_\_T\_D\_Information\_T\_D\_0\_T\_D\_Activity e3817da0-d843-4c5c-85c6-40ba7424dce2 finished successfully

3.  Incluez le fichier **PDB** dans le fichier zip afin que les détails de l’erreur incluent des informations telles que la **pile des appels** quand une erreur se produit.

4.  Tous les fichiers contenus dans le fichier zip de l’activité personnalisée doivent se trouver au **niveau supérieur** et ne contenir aucun sous-dossier.

    ![](./media/data-factory-data-processing-using-batch/image20.png)

5.  Assurez-vous que les paramètres **assemblyName** (MyDotNetActivity.dll), **entryPoint**(MyDotNetActivityNS.MyDotNetActivity), **packageFile** (customactivitycontainer/Mydotnetactivity.zip) et **packageLinkedService** (qui doit pointer vers le stockage d’objets blob Azure contenant le fichier .zip) sont définis sur les valeurs correctes.

6.  Si vous avez corrigé une erreur et souhaitez relancer le traitement de la tranche, cliquez avec le bouton droit sur la tranche dans le panneau **OutputDataset** puis cliquez sur **Exécuter**.

    ![](./media/data-factory-data-processing-using-batch/image21.png)

    **Remarque :** vous verrez un **conteneur** dans votre stockage d’objets Blob Azure nommé **adfjobs**. Ce conteneur n’est pas automatiquement supprimé, mais vous pouvez le supprimer en toute sécurité après avoir testé la solution. De même, la solution Data Factory crée un **travail** Azure Batch nommé **adf-<pool ID/name>:job-0000000001**. Si vous le souhaitez, vous pouvez supprimer ce travail après avoir testé la solution.
7. L’activité personnalisée n’utilise pas le fichier **app.config** de votre package. Par conséquent, si votre code lit les chaînes de connexion du fichier de configuration, il ne fonctionnera pas au moment de l’exécution. Quand vous utilisez Azure Batch, la meilleure pratique consiste à stocker les clés secrètes dans un coffre de clés **Azure KeyVault**, à utiliser un principal de service basé sur certificat pour protéger le coffre de clés et à distribuer le certificat à un pool Azure Batch. L’activité personnalisée .NET peut alors accéder aux secrets du coffre de clés au moment de l’exécution. Cette solution est générique et peut s’adapter à n’importe quel type de clé secrète, et pas uniquement aux chaînes de connexion.

	Il existe une solution plus simple (mais non recommandée) : vous pouvez créer un nouveau **service lié SQL Azure** avec des paramètres de chaîne de connexion, puis créer un jeu de données qui utilise le service lié et chaîner le jeu de données à l’activité .NET personnalisée en tant que jeu de données d’entrée factice. Vous pouvez accéder ensuite à la chaîne de connexion du service lié dans le code de l’activité personnalisée. Cette fois-ci, le code devrait fonctionner sans problème lors de l’exécution.

### Étendre l’exemple

Vous pouvez étendre cet exemple pour en savoir plus sur les fonctionnalités d’Azure Data Factory et d’Azure Batch. Par exemple, pour traiter des tranches d’une autre plage de temps, procédez comme suit :

1.  Ajoutez au dossier **inputfolder** les sous-dossiers 2015-11-16-05, 2015-11-16-06, 201-11-16-07, 2011-11-16-08, 2015-11-16-09, et placez les fichiers d’entrée dans ces dossiers. Modifiez l’heure de fin pour le pipeline de 2015-11-16T05:00:00Z en 2015-11-16T10:00:00Z. Dans la **vue de diagramme**, double-cliquez sur **InputDataset**, et vérifiez que les tranches d’entrée sont prêtes. Double-cliquez sur **OuptutDataset** pour vérifier l’état des tranches de sortie. Si leur état est Prêt, vérifiez les fichiers de sortie dans le dossier outputfolder.

2.  Augmentez ou réduisez la valeur du paramètre **concurrency** pour comprendre comment il affecte les performances de votre solution, en particulier le traitement qui se produit sur Azure Batch. (Pour plus d’informations sur le paramètre **concurrency**, voir l’étape 4 : Créer et exécuter le pipeline.)

3.  Créez un pool avec une valeur **Maximum tasks per VM** (Nombre maximal de tâches par machine virtuelle) supérieure/inférieure. Mettez à jour le service lié Azure Batch dans la solution Data Factory pour utiliser le nouveau pool créé. (Pour plus d’informations sur le paramètre **Maximum tasks per VM**, voir l’étape 4 : Créer et exécuter le pipeline.)

4.  Créez un pool Azure Batch avec la fonctionnalité **autoscale**. La mise à l’échelle automatique des nœuds de calcul dans un pool Azure Batch est en fait un ajustement dynamique de la puissance de traitement utilisée par votre application. Par exemple, vous pouvez créer un pool Azure Batch avec 0 machine virtuelle dédiée et une formule de mise à l’échelle automatique en fonction du nombre de tâches en attente :
 
		pendingTaskSampleVector=$PendingTasks.GetSample(600 * TimeInterval_Second);$TargetDedicated = max(pendingTaskSampleVector);

	Pour plus d’informations, consultez [Mettre automatiquement à l’échelle les nœuds de calcul dans un pool Azure Batch](../batch/batch-automatic-scaling.md).

	Si le pool utilise la valeur par défaut du paramètre [autoScaleEvaluationInterval](https://msdn.microsoft.com/library/azure/dn820173.aspx), le service Batch peut mettre entre 15 et 30 minutes à préparer la machine virtuelle avant d’exécuter l’activité personnalisée. Si le pool utilise une autre valeur pour autoScaleEvaluationInterval, le service Batch peut prendre la durée d’autoScaleEvaluationInterval + 10 minutes.
	 
5. Dans l’exemple de solution, la méthode **Execute** appelle la méthode **Calculate** qui traite une tranche de données d’entrée pour produire une tranche de données de sortie. Vous pouvez écrire votre propre méthode pour traiter les données d’entrée, et remplacer l’appel de la méthode Calculate dans la méthode Execute par un appel à votre méthode.

 


## Étapes suivantes : Consommer les données

Après avoir traité des données, vous pouvez les employer avec des outils en ligne tels que **Microsoft Power BI**. Voici des liens pour vous aider à comprendre Power BI et comment l’utiliser dans Azure :

-   [Explorer un jeu de données dans Power BI](https://powerbi.microsoft.com/fr-FR/documentation/powerbi-service-get-data/)

-   [Prise en main de Power BI Desktop](https://powerbi.microsoft.com/fr-FR/documentation/powerbi-desktop-getting-started/)

-   [Actualisation des données dans Power BI](https://powerbi.microsoft.com/fr-FR/documentation/powerbi-refresh-data/)

-   [Azure et Power BI](https://powerbi.microsoft.com/fr-FR/documentation/powerbi-azure-and-power-bi/)

## Références

-   [Azure Data Factory](https://azure.microsoft.com/documentation/services/data-factory/)

    -   [Présentation du service Azure Data Factory](data-factory-introduction.md)

    -   [Prise en main d’Azure Data Factory](data-factory-build-your-first-pipeline.md)

    -   [Utilisation des activités personnalisées dans un pipeline Azure Data Factory](data-factory-use-custom-activities.md)

-   [Azure Batch](https://azure.microsoft.com/documentation/services/batch/)

    -   [Notions de base d’Azure Batch](../batch/batch-technical-overview.md)

    -   [Vue d’ensemble des fonctionnalités d’Azure Batch](../batch/batch-api-basics.md)

    -   [Création et gestion d’un compte Azure Batch dans le portail Azure](../batch/batch-account-create-portal.md)

    -   [Get started with the .NET Azure Batch Library .NET](../batch/batch-dotnet-get-started.md)


[batch-explorer]: https://github.com/Azure/azure-batch-samples/tree/master/CSharp/BatchExplorer
[batch-explorer-walkthrough]: http://blogs.technet.com/b/windowshpc/archive/2015/01/20/azure-batch-explorer-sample-walkthrough.aspx

<!---HONumber=AcomDC_0525_2016-->