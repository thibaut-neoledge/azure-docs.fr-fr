<properties
	pageTitle="Utilisation des activités personnalisées dans un pipeline Azure Data Factory"
	description="Découvrez comment créer des activités personnalisées et les utiliser dans un pipeline Azure Data Factory."
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
	ms.date="12/15/2015"
	ms.author="spelluru"/>

# Utilisation des activités personnalisées dans un pipeline Azure Data Factory
Azure Data Factory prend en charge l’utilisation d’activités intégrées telles que **Copier l’activité** et **Activité HDInsight** dans les pipelines dans le but de déplacer et de traiter des données. Vous pouvez également créer une activité personnalisée .NET avec votre propre logique de transformation/de traitement et utiliser l’activité dans un pipeline. Vous pouvez configurer l’activité à exécuter en utilisant un cluster **Azure HDInsight** ou un service **Azure Batch**.

Cet article décrit comment créer une activité personnalisée et l'utiliser dans un pipeline Azure Data Factory. Il fournit également une procédure pas à pas pour créer et utiliser une activité personnalisée. La procédure pas à pas utilise le service HDInsight lié. Pour utiliser le service lié Azure Batch à la place, vous créez un service lié de type **AzureBatch** et l’utilisez dans la section d’activité du pipeline JSON (**linkedServiceName**). Consultez la section [Service lié Azure Batch](#AzureBatch) pour plus d’informations sur l’utilisation d’Azure Batch avec l’activité personnalisée.


## <a name="walkthrough" /> Procédure pas à pas
Cette procédure pas à pas vous fournit des instructions détaillées sur la création d’une activité personnalisée et l’utilisation de l’activité dans un pipeline Microsoft Azure Data Factory. Cette procédure pas à pas développe le didacticiel de la rubrique [Prise en main d’Azure Data Factory][adfgetstarted]. Si vous souhaitez voir fonctionner l'activité personnalisée, vous devez commencer par le didacticiel de prise en main, puis effectuer cette procédure pas à pas.

### Composants requis


- Didacticiel de la rubrique [Prise en main d’Azure Data Factory][adfgetstarted]. Vous devez suivre le didacticiel de cet article avant d’effectuer cette procédure pas à pas.
- Visual Studio 2012 ou 2013
- Téléchargez et installez le [Kit de développement logiciel (SDK) Azure .NET][azure-developer-center].
- Téléchargez la dernière version du [package NuGet pour Microsoft Azure Data Factory](https://www.nuget.org/packages/Microsoft.Azure.Management.DataFactories/) et installez-la. Les instructions sont fournies dans la procédure pas à pas.
- Téléchargez et installez le package NuGet pour Azure Storage. Les instructions sont fournies dans la procédure pas à pas, vous pouvez donc ignorer cette étape.

### Procédure générale 
1.	**Créez une activité personnalisée** à utiliser dans la solution Data Factory. L’activité personnalisée contient la logique de traitement des données. 
	1.	Dans Visual Studio (ou un éditeur de code de votre choix), créez un projet de bibliothèque de classes .NET, ajoutez le code de traitement des données d’entrée et compilez le projet.	
	2.	Compressez les fichiers binaires et le fichier (facultatif) PDB dans le dossier de sortie.	
	3.	Téléchargez le fichier compressé dans un stockage d’objets blob Azure. La procédure détaillée est décrite dans la section Création de l’activité personnalisée. 
2. **Créez une fabrique de données Azure qui utilise l’activité personnalisée** :
	1. Créer une fabrique de données Azure.
	2. créez des services liés.
		1. StorageLinkedService : fournit les informations d’identification du stockage permettant d’accéder aux objets blob.
		2. HDInsightLinkedService : spécifie Azure HDInsight en tant qu’entité de calcul.
	3. Créez les jeux de données.
		1. InputDataset : spécifie le conteneur de stockage et le dossier contenant les objets blob d’entrée.
		1. OutputDataset : spécifie le conteneur de stockage et le dossier contenant les objets blob de sortie.
	2. Créez un pipeline qui utilise l’activité personnalisée.
	3. Exécutez et testez le pipeline.
	4. Déboguez le pipeline.

## Création de l’activité personnalisée
Pour créer une activité personnalisée .NET utilisable dans un pipeline Azure Data Factory, vous devez créer un projet de **bibliothèque de classes .NET** contenant une classe qui implémente cette interface **IDotNetActivity**. Cette interface possède une seule méthode : Execute. Voici la signature de la méthode :

	public IDictionary<string, string> Execute(
            IEnumerable<LinkedService> linkedServices, 
            IEnumerable<Dataset> datasets, 
            Activity activity, 
            IActivityLogger logger)
        
La méthode comporte quelques composants clés qu’il est important d’assimiler.

- La méthode accepte quatre paramètres :
	- **linkedServices** : liste énumérable de services liés relie les sources de données d’entrée/sortie (par exemple, Azure Blob Storage) à la fabrique de données. Dans cet exemple, il s’agit du seul service lié de type Azure Storage utilisé à la fois pour les données d’entrée et de sortie. 
	- **datasets** : liste énumérable de jeux de données. Vous pouvez utiliser ce paramètre pour obtenir les emplacements et les schémas définis par les jeux de données d’entrée et de sortie.
	- **activity** : ce paramètre représente l’entité de calcul actuelle (dans ce cas, une instance Azure HDInsight).
	- **logger** : permet d’écrire des commentaires de débogage qui apparaîtront en tant que journal « utilisateur » pour le pipeline. 

- La méthode renvoie un dictionnaire qui peut être utilisé pour enchaîner les activités personnalisées. Nous n’utiliserons pas cette fonctionnalité dans cet exemple de solution.

### Procédure : 
1.	Créez un projet de bibliothèque de classes .NET.
	<ol type="a">
	<li>Lancez <b>Visual Studio&#160;2012</b> ou <b>Visual Studio&#160;2013</b>.</li>
	<li>Cliquez sur <b>Fichier</b>, pointez le curseur de la souris sur <b>Nouveau</b>, puis cliquez sur <b>Projet</b>.</li>
	<li>Développez <b>Modèles</b>, puis sélectionnez <b>Visual&#160;C#</b>. Dans cette procédure pas à pas, vous utilisez&#160;C#, mais vous pouvez utiliser un autre langage&#160;.NET pour développer l'activité personnalisée.</li>
	<li>Sélectionnez <b>Bibliothèque de classes</b> dans la liste des types de projet, sur la droite.</li>
	<li>Entrez <b>MyDotNetActivity</b> dans le champ <b>Nom</b>.</li>
	<li>Sélectionnez <b>C:\ADFGetStarted</b> dans le champ <b>Emplacement</b>.</li>
	<li>Cliquez sur <b>OK</b> pour créer le projet.</li>
</ol>
2.  Cliquez sur <b>Outils</b>, pointez le curseur de la souris sur <b>Gestionnaire de package NuGet</b>, puis cliquez sur <b>Console du gestionnaire de package</b>.
3.	Dans la <b>Console du gestionnaire de package</b>, exécutez la commande suivante pour importer l’élément <b>Microsoft.Azure.Management.DataFactories</b>.

		Install-Package Microsoft.Azure.Management.DataFactories

4. Importez le package NuGet Azure Storage dans le projet.

		Install-Package Azure.Storage

5. Ajoutez les instructions **using** ci-après dans le fichier source du projet.

		using System.IO;
		using System.Globalization;
		using System.Diagnostics;
		using System.Linq;

		using Microsoft.Azure.Management.DataFactories.Models;
		using Microsoft.Azure.Management.DataFactories.Runtime;

		using Microsoft.WindowsAzure.Storage;
		using Microsoft.WindowsAzure.Storage.Blob;

6. Remplacez le nom de l’**espace de noms** par **MyDotNetActivityNS**.

		namespace MyDotNetActivityNS

7. Remplacez le nom de la classe par **MyDotNetActivity** et dérivez-le de l’interface **IDotNetActivity**, comme indiqué ci-dessous.

		public class MyDotNetActivity : IDotNetActivity

8. Implémentez (ajoutez) la méthode **Execute** de l’interface **IDotNetActivity** dans la classe **MyDotNetActivity** et copiez l’exemple de code suivant dans la méthode.

	L’exemple de code suivant compte le nombre de lignes de l’objet blob en entrée et produit le contenu suivant dans l’objet blob de sortie : chemin de l’objet blob, nombre de lignes qu’il inclut, machine sur laquelle l’activité s’est exécutée et horodatage actuel.

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

            // declare dataset types
            CustomDataset inputLocation;
            AzureBlobDataset outputLocation;

            Dataset inputDataset = datasets.Single(dataset => dataset.Name == activity.Inputs.Single().Name);
            inputLocation = inputDataset.Properties.TypeProperties as CustomDataset;

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
            // convert to blob location object.
            outputLocation = outputDataset.Properties.TypeProperties as AzureBlobDataset;

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

            // return a new Dictionary object (unused in this code).
            return new Dictionary<string, string>();
        }

9. Ajoutez les méthodes d'assistance suivantes. La méthode **Execute** appelle ces méthodes d’assistance. La méthode **GetConnectionString** récupère la chaîne de connexion Microsoft Azure Storage et la méthode **GetFolderPath**, l’emplacement de l’objet blob. Plus important encore, la méthode **Calculate** isole le code qui effectue une itération dans chaque objet blob.

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

	La méthode GetFolderPath renvoie le chemin d’accès au dossier vers lequel pointe le jeu de données et la méthode GetFileName renvoie le nom de l’objet blob/fichier vers lequel pointe le jeu de données.
	
		    "name": "InputDataset",
		    "properties": {
		        "type": "AzureBlob",
		        "linkedServiceName": "StorageLinkedService",
		        "typeProperties": {
		            "fileName": "file.txt",
		            "folderPath": "mycontainer/inputfolder/",
	
	La méthode Calculate calcule le nombre d’instances du mot-clé Microsoft dans les fichiers d’entrée (objets blob du dossier). Le terme de recherche (« Microsoft ») est codé en dur dans le code.

10. Compilez le projet. Cliquez sur l’option **Générer** du menu, puis sur **Générer la solution**.
11. Lancez l’**Explorateur Windows** et accédez au dossier **bin\\debug** ou **bin\\release** (selon le type de build).
12. Créez un fichier **MyDotNetActivity.zip** contenant tous les binaires dans le dossier <project folder>\\bin\\Debug. Si vous le souhaitez, vous pouvez inclure le fichier **MyDotNetActivity.pdb** afin d’obtenir des détails supplémentaires tels que le numéro de ligne du code source à l’origine du problème en cas de défaillance.

	![Fichiers de sortie binaires](./media/data-factory-use-custom-activities/Binaries.png)
13. Chargez le fichier **MyDotNetActivity.zip** en tant qu’objet blob dans le conteneur d’objets blob **customactvitycontainer** du stockage d’objets blob Azure utilisé par le service lié **StorageLinkedService** dans **ADFTutorialDataFactory**. Créez le conteneur d’objets blob **customactivitycontainer**, le cas échéant.

> [AZURE.NOTE]Si vous ajoutez ce projet d'activité .NET à une solution dans Visual Studio qui contient un projet Data Factory, il est inutile d'effectuer les deux dernières étapes de création du fichier zip et de le télécharger manuellement dans le stockage blob Azure. Lorsque vous publiez des entités Data Factory à l'aide de Visual Studio, ces étapes sont effectuées automatiquement par le processus de publication. Consultez les articles [Concevez votre premier pipeline à l’aide de Visual Studio](data-factory-build-your-first-pipeline-using-vs.md) et [Copie de données à partir d’un objet Blob Azure vers SQL Azure](data-factory-get-started-using-vs.md) pour en savoir plus sur la création et la publication des entités Data Factory à l’aide de Visual Studio.

### Méthode Execute

Cette section fournit des informations et remarques supplémentaires concernant le code contenu dans la méthode **Execute**.
 
1. Les membres de l’itération dans la collection d’entrée se trouvent dans l’espace de noms [Microsoft.WindowsAzure.Storage.Blob](https://msdn.microsoft.com/library/azure/microsoft.windowsazure.storage.blob.aspx). Pour parcourir la collection d’objets blob, vous devez utiliser la classe **BlobContinuationToken**. Vous devez utiliser une boucle do-while en utilisant le jeton pour sortir de la boucle. Pour plus d’informations, consultez la page [Utilisation du stockage d’objets blob à partir de .NET](../storage/storage-dotnet-how-to-use-blobs.md). Une boucle de base est illustrée ici :

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

	Consultez la documentation de la méthode [ListBlobsSegmented](https://msdn.microsoft.com/library/jj717596.aspx) pour plus d’informations.

2.	Le code permettant de parcourir l’ensemble d’objets blob s’inscrit logiquement dans la boucle do-while. Dans la méthode **Execute**, la boucle do-while transmet la liste d’objets blob à une méthode nommée **Calculate**. La méthode renvoie une variable de chaîne nommée **output**, qui correspond au résultat de l’itération dans tous les objets blob du segment.

	Elle retourne le nombre d’occurrences du terme de recherche (**Microsoft**) dans l’objet blob transmis à la méthode **Calculate**.

			output += string.Format("{0} occurrences of the search term "{1}" were found in the file {2}.\r\n", wordCount, searchTerm, inputBlob.Name);

3.	À la fin de l’exécution de la méthode **Calculate**, vous devez l’écrire dans un nouvel objet blob. Par conséquent, pour chaque ensemble d’objets blob traité, un nouvel objet blob peut être écrit avec les résultats correspondants. Pour écrire dans un nouvel objet blob, commencez par rechercher le jeu de données de sortie.

			// Get the output dataset using the name of the dataset matched to a name in the Activity output collection.
			Dataset outputDataset = datasets.Single(dataset => dataset.Name == activity.Outputs.Single().Name);

			// Convert to blob location object.
			outputLocation = outputDataset.Properties.TypeProperties as AzureBlobDataset;

4.	Le code appelle également une méthode d’assistance **GetFolderPath** pour récupérer le chemin d’accès au dossier (le nom du conteneur de stockage).
 
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

7.	Le nom du fichier a été écrit et vous pouvez maintenant écrire la chaîne de sortie dans un nouvel objet blob à partir de la méthode de calcul :

			// Create a new blob and upload the output text.
			CloudBlockBlob outputBlob = new CloudBlockBlob(outputBlobUri, outputStorageAccount.Credentials);
			logger.Write("Writing {0} to the output blob", output);
			outputBlob.UploadText(output);

## Création de la fabrique de données

Dans la section **Création de l’activité personnalisée**, vous avez créé une activité personnalisée et téléchargé le fichier zip contenant les fichiers binaires et le fichier PDB dans un conteneur d’objets blob Azure. Dans cette section, vous allez maintenant créer une **fabrique de données** Azure avec un **pipeline** qui utilise l’**activité personnalisée**.
 
Le jeu de données d’entrée de l’activité personnalisée représente les objets blob (fichiers) contenus dans le dossier d’entrée (mycontainer\\inputfolder) du stockage d’objets blob. Le jeu de données de sortie de l’activité personnalisée représente les objets blob de sortie contenus dans le dossier de sortie (mycontainer\\outputfolder) du stockage d’objets blob.

Créez un fichier nommé file.txt avec le contenu suivant et téléchargez-le dans mycontainer\\inputfolder (mycontainer est le nom du conteneur d’objets blob Azure et inputfolder est le nom du dossier se trouvant dans ce conteneur).

	test custom activity Microsoft test custom activity Microsoft

Le dossier d’entrée correspond à une tranche dans Azure Data Factory, même si le dossier contient au moins 2 fichiers. Lorsque chaque tranche est traitée par le pipeline, l’activité personnalisée effectue une itération dans tous les objets blob du dossier d’entrée pour la tranche en question.

Un fichier de sortie à 1 ou plusieurs lignes (le même nombre de lignes que le nombre d’objets blob contenus dans le dossier d’entrée) apparaîtra dans le dossier mycontainer\\output :
 
	2 occurrences(s) of the search term "Microsoft" were found in the file inputfolder/2015-11-16-00/file.txt.


Voici les étapes que vous allez effectuer lors de cette opération :

1. Création d'une **fabrique de données**.
2. Créez des **services liés** pour le cluster HDInsight sur lequel l’activité personnalisée sera exécutée comme tâche de mappage et pour le stockage Azure contenant les objets blob d’entrée/sortie. 
2. Créer des **jeux de données** d’entrée et de sortie qui représentent les entrées / sorties de l’activité personnalisée. 
3. Créez et exécutez un **pipeline** qui utilise l’activité personnalisée

### Étape 1 : Créer la fabrique de données

1.	Une fois connecté au portail Azure, procédez comme suit :
	1.	Cliquez sur **NOUVEAU** dans le menu de gauche.
	2.	Cliquez sur **Données + Analyse** dans le panneau **Nouveau**.
	3.	Cliquez sur **Data Factory** dans le panneau **Analyse des données**.
2.	Dans le panneau **Nouvelle fabrique de données**, spécifiez le nom **CustomActivityFactory**. Le nom de la fabrique de données Azure doit être un nom global unique. Si l’erreur suivante s’affiche : **Le nom de la fabrique de données « CustomActivityFactory » n’est pas disponible**, changez le nom de la fabrique de données (par exemple, **votrenomCustomActivityFactory**), puis tentez de la recréer.
3.	Cliquez sur **NOM DU GROUPE DE RESSOURCES** pour sélectionner un groupe de ressources existant, ou créez un nouveau groupe de ressources. 
4.	Vérifiez que vous utilisez l’**abonnement** et la **région** correspondant à ceux dans lesquels vous souhaitez créer la fabrique de données. 
5.	Cliquez sur **Créer** dans le panneau **Nouvelle fabrique de données**.
6.	La fabrique de données apparaît comme étant en cours de création dans le **Tableau de bord** du portail Azure.
7.	Une fois la fabrique de données créée, son contenu apparaître dans le panneau correspondant indiquant.

### Étape 2 : Créer des services liés

Les services liés se chargent de lier des magasins de données ou des services de calcul à une fabrique de données Azure. Dans cette étape, vous allez lier votre compte Azure Storage et un cluster Azure HDInsight à votre fabrique de données.

#### Créer le service lié Azure Storage

1.	Cliquez sur la vignette **Créer et déployer** dans le panneau **DATA FACTORY** de **CustomActivityFactory**. Cette action lance l'éditeur Data Factory Editor.
2.	Cliquez sur **Nouvelle banque de données** dans la barre de commandes et choisissez **Azure Storage**. Le script JSON de création d’un service lié Microsoft Azure Storage doit apparaître dans l’éditeur.
3.	Remplacez **account name** par le nom de votre compte de stockage Azure et **account key** par sa clé d’accès. Pour découvrir comment obtenir votre clé d’accès de stockage, consultez la rubrique [Affichage, copie et régénération de clés d’accès de stockage](../storage/storage-create-storage-account.md#view-copy-and-regenerate-storage-access-keys).
4.	Cliquez sur l’option **Déployer** de la barre de commandes pour déployer le service lié.


#### Créer le service lié Azure HDInsight 
Le service Azure Data Factory prend en charge la création d’un cluster à la demande et l’utilise pour traiter des données d’entrée afin de produire des données de sortie. Vous pouvez également utiliser votre propre cluster pour effectuer cette opération. Lorsque vous utilisez un cluster HDInsight à la demande, un cluster est créé pour chaque tranche. Par contre, si vous utilisez votre propre cluster HDInsight, le cluster est prêt à traiter la tranche immédiatement. Par conséquent, lorsque vous utilisez un cluster à la demande, vous ne voyez pas les données de sortie aussi rapidement que lorsque vous utilisez votre propre cluster.

> [AZURE.NOTE]Lors de l’exécution, une instance d’activité .NET s’exécute uniquement sur un nœud de travail du cluster HDInsight ; elle ne peut pas être mise à l’échelle pour s’exécuter sur plusieurs nœuds. Cependant, plusieurs instances d’activité .NET peuvent s’exécuter en parallèle sur différents nœuds du cluster HDInsight.

Si, après avoir exécuté le didacticiel [Prise en main d’Azure Data Factory][adfgetstarted], vous avez effectué la procédure pas à pas de la section [Utilisation de Pig et Hive avec Azure Data Factory][hivewalkthrough], vous pouvez ignorer la création de ce service lié et utiliser celui de l’élément ADFTutorialDataFactory.


##### Pour utiliser un cluster HDInsight à la demande

1. Dans le **portail Microsoft Azure**, cliquez sur l’option **Créer et déployer** de la page d’accueil du logiciel Data Factory.
2. Dans Data Factory Editor, cliquez sur **Nouveau calcul** dans la barre de commandes et sélectionnez l’option **Cluster HDInsight à la demande** dans le menu.
2. Procédez comme suit dans le script JSON :
	1. Pour la propriété **clusterSize**, spécifiez la taille du cluster HDInsight.
	3. Pour la propriété **timeToLive**, spécifiez la durée pendant laquelle le client peut être inactif avant d’être supprimé.
	4. Pour la propriété **version**, spécifiez la version de Microsoft Azure HDInsight que vous souhaitez utiliser. Si vous excluez cette propriété, la version utilisée sera la plus récente.  
	5. Pour **linkedServiceName**, spécifiez le service lié **StorageLinkedService** que vous avez créé dans le didacticiel de prise en main.

			{
			  "name": "HDInsightOnDemandLinkedService",
			  "properties": {
			    "type": "HDInsightOnDemand",
			    "typeProperties": {
			      "clusterSize": "1",
			      "timeToLive": "00:05:00",
			      "version": "3.2",
			      "linkedServiceName": "StorageLinkedService"
			    }
			  }
			}

2. Cliquez sur l’option **Déployer** de la barre de commandes pour déployer le service lié.

##### Pour utiliser votre propre cluster HDInsight :

1. Dans le **portail Microsoft Azure**, cliquez sur l’option **Créer et déployer** de la page d’accueil du logiciel Data Factory.
2. Dans **Data Factory Editor**, cliquez sur **Nouveau calcul** dans la barre de commandes et sélectionnez **Cluster HDInsight** dans le menu.
2. Procédez comme suit dans le script JSON :
	1. Pour la propriété **clusterUri**, saisissez l’URL de votre cluster HDInsight. Par exemple : https://<clustername>.azurehdinsight.net/.     
	2. Pour la propriété **UserName**, saisissez le nom d’utilisateur ayant accès au cluster HDInsight.
	3. Pour la propriété **Password**, spécifiez le mot de passe de l’utilisateur.
	4. Pour la propriété **LinkedServiceName**, saisissez **StorageLinkedService**. Désigne le service lié que vous avez créé dans le didacticiel de prise en main.

2. Cliquez sur l’option **Déployer** de la barre de commandes pour déployer le service lié.

### Étape 3 : Créer les jeux de données
Dans cette étape, vous allez créer des jeux de données pour représenter les données d’entrée et de sortie.

#### Créer le jeu de données d’entrée
1.	Dans l’**éditeur** de Data Factory, cliquez sur le bouton **Nouveau jeu de données** de la barre d’outils et sélectionnez **Stockage d’objets blob Azure** dans le menu déroulant.
2.	Remplacez le script JSON affiché dans le volet droit par l’extrait de code JSON suivant :

			{
			    "name": "InputDataset",
			    "properties": {
			        "type": "AzureBlob",
			        "linkedServiceName": "StorageLinkedService",
			        "typeProperties": {
			            "folderPath": "adftutorial/customactivityinput/",
			            "format": {
			                "type": "TextFormat"
			            }
			        },
			        "availability": {
			            "frequency": "Hour",
			            "interval": 1
			        },
			        "external": true,
			        "policy": {}
			    }
			}

	Plus loin dans cette procédure pas à pas, vous allez créer un pipeline associé à l’heure de début 2015-11-16T00:00:00Z et à l’heure de fin 2015-11-16T05:00:00Z. Ce pipeline est planifié pour produire des données toutes les heures, ce qui signifie que l’on obtiendra 5 tranches d’entrée/sortie (comprises entre **00**: 00:00 et **05**: 00:00).

	Les paramètres **frequency** et **interval** du jeu de données d’entrée sont respectivement définis sur **Hour** et sur **1**, ce qui signifie que la tranche d’entrée est disponible toutes les heures. Dans cet exemple, il s’agit du même fichier (file.txt) que celui contenu dans le dossier d’entrée.

	Voici les heures de début de chaque tranche, chacune étant représentée par la variable système SliceStart dans l’extrait de code JSON ci-dessus.

	
3.	Cliquez sur **Déployer** sur la barre d’outils pour créer et déployer le **jeu de données d’entrée**. Vérifiez que le message **TABLE CORRECTEMENT CRÉÉE** s'affiche dans la barre de titre de l'éditeur.


#### Créer une table de sortie

1. Dans **Data Factory Editor**, cliquez sur **Nouveau jeu de données**, puis sur **Stockage d’objets blob Azure** dans la barre de commandes.
2. Remplacez le script JSON affiché dans le volet droit par le script JSON suivant :

		{
		    "name": "OutputDataset",
		    "properties": {
		        "type": "AzureBlob",
		        "linkedServiceName": "StorageLinkedService",
		        "typeProperties": {
		            "fileName": "{slice}.txt",
		            "folderPath": "adftutorial/customactivityoutput",
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

 	L’emplacement de sortie est le suivant : **adftutorial/customactivityoutput/AAAAMMJJHH/**, où « AAAAMMJJHH » correspond à l’année, au mois, au jour et à l’heure de la tranche produite. Pour en savoir plus, consultez la page [Référence du développeur][adf-developer-reference].

	Un objet blob/fichier de sortie est généré pour chaque tranche d’entrée. Voici la procédure de nommage des fichiers de sortie pour chaque tranche. Tous les fichiers de sortie sont générés dans un seul dossier de sortie : **adftutorial\\customactivityoutput**.

	| Tranche | Heure de début | Fichier de sortie |
	| :---- | :--------- | :---------- | 
	| 1 | 2015-11-16T00:00:00 | 2015-11-16-00.txt |
	| 2 | 2015-11-16T01:00:00 | 2015-11-16-01.txt |
	| 3 | 2015-11-16T02:00:00 | 2015-11-16-02.txt |
	| 4 | 2015-11-16T03:00:00 | 2015-11-16-03.txt |
	| 5 | 2015-11-16T04:00:00 | 2015-11-16-04.txt |

	N’oubliez pas que tous les fichiers d’un dossier d’entrée font partie d’une tranche associée aux heures de début indiquées ci-dessus. Lorsque cette tranche est traitée, l’activité personnalisée parcourt chaque fichier et génère une ligne dans le fichier de sortie avec le nombre d’occurrences du terme de recherche (« Microsoft »). Si le dossier d’entrée comporte trois fichiers, trois lignes apparaîtront dans le fichier de sortie pour chaque tranche horaire : 2015-11-16-00.txt, 2015-11-16:01:00:00.txt, etc.


2. Cliquez sur **Déployer** dans la barre de commandes pour déployer le **jeu de données de sortie**.


### Créer et exécuter un pipeline qui utilise l’activité personnalisée

1. Dans Data Factory Editor, cliquez sur **Nouveau pipeline** dans la barre de commandes. Si vous ne voyez pas apparaître la commande, cliquez sur **... (points de suspension)** pour l’afficher.
2. Remplacez le script JSON affiché dans le volet droit par le script JSON suivant. Si vous souhaitez utiliser votre propre cluster et si vous avez suivi les étapes permettant de créer le service lié **HDInsightLinkedService**, remplacez **HDInsightOnDemandLinkedService** par **HDInsightLinkedService** dans le JSON suivant.

		{
		  "name": "ADFTutorialPipelineCustom",
		  "properties": {
		    "description": "Use custom activity",
		    "activities": [
		      {
		        "Name": "MyDotNetActivity",
		        "Type": "DotNetActivity",
		        "Inputs": [
		          {
		            "Name": "InputDataset"
		          }
		        ],
		        "Outputs": [
		          {
		            "Name": "OutputDataset"
		          }
		        ],
		        "LinkedServiceName": "HDInsightOnDemandLinkedService",
		        "typeProperties": {
		          "AssemblyName": "MyDotNetActivity.dll",
		          "EntryPoint": "MyDotNetActivityNS.MyDotNetActivity",
		          "PackageLinkedService": "StorageLinkedService",
		          "PackageFile": "customactivitycontainer/MyDotNetActivity.zip",
		          "extendedProperties": {
		            "SliceStart": "$$Text.Format('{0:yyyyMMddHH-mm}', Time.AddMinutes(SliceStart, 0))"
		          }
		        },
		        "Policy": {
		          "Concurrency": 1,
		          "ExecutionPriorityOrder": "OldestFirst",
		          "Retry": 3,
		          "Timeout": "00:30:00",
		          "Delay": "00:00:00"
		        }
		      }
		    ],
    		"start": "2015-11-16T00:00:00Z",
			"end": "2015-11-16T05:00:00Z",
		    "isPaused": false
		  }
		}

	Remplacez la valeur **StartDateTime** par une date antérieure de trois jours à la date actuelle, et remplacez la valeur **EndDateTime** par la date actuelle. Les valeurs StartDateTime et EndDateTime doivent être au [format ISO](http://en.wikipedia.org/wiki/ISO_8601). Par exemple : 2014-10-14T16:32:41Z. La table de sortie est planifiée pour être produite tous les jours, trois tranches seront donc produites.

	Notez les points suivants :

	- Il existe une activité dans la section des activités ; elle présente le type suivant : **DotNetActivity**.
	- Utilisez la table d’entrée **EmpTableFromBlob** que vous avez exploitée dans le didacticiel de prise en main.
	- Utilisez une nouvelle table de sortie **OutputTableForCustom**, que vous allez créer à l’étape suivante.
	- Le paramètre **AssemblyName** est défini sur le nom de la DLL : **MyActivities.dll**.
	- Le paramètre **EntryPoint** est défini sur **MyDotNetActivityNS.MyDotNetActivity**.
	- **PackageLinkedService** est défini sur **StorageLinkedService**, qui pointe vers le stockage d’objets blob contenant le fichier .zip de l’activité personnalisée. Si vous utilisez des comptes de stockage différents pour les fichiers d’entrée/sortie et le fichier zip de l’activité personnalisée, vous devez créer un autre service lié Azure Storage. Cet article suppose d’utiliser le même compte de stockage Azure.
	- Le paramètre **PackageFile** est défini sur **customactivitycontainer/MyDotNetActivity.zip**. Il est au format : <containerforthezip>/<nameofthezip.zip>.
	- L’activité personnalisée utilise **InputDataset** comme entrée et **OutputDataset** comme sortie.
	- La propriété linkedServiceName de l’activité personnalisée pointe vers **HDInsightLinkedService**, ce qui indique à Azure Data Factory que l’activité personnalisée doit s’exécuter sur un cluster Azure HDInsight.
	- La propriété **isPaused** est définie par défaut sur **false**. Le pipeline s’exécute immédiatement dans cet exemple car les tranches débutent à une date antérieure. Vous pouvez définir cette propriété sur true pour suspendre le pipeline et lui réaffecter la valeur false pour reprendre l’exécution. 
	- **5** heures séparent les heures de **début** et de **fin**, et les tranches sont produites toutes les heures, ce qui signifie que 5 tranches seront produites par le pipeline. 


4. Cliquez sur **Déployer** dans la barre de commandes pour déployer le pipeline.

### Surveiller le pipeline
 
8. Dans le panneau Data Factory du portail Azure, cliquez sur **Diagramme**.
	
	![Vignette schématique](./media/data-factory-use-custom-activities/DataFactoryBlade.png)
 
9. Dans la vue de diagramme, cliquez sur OutputDataset.
 
	![Vue schématique](./media/data-factory-use-custom-activities/diagram.png)

10. Si elles ont déjà été produites, les 5 tranches de sortie doivent être à l’état « prêt ».

	![Tranches de sortie](./media/data-factory-use-custom-activities/OutputSlices.png)
	
12. Vérifiez que les fichiers de sortie sont générés dans le stockage d’objets blob, dans le conteneur **adftutorial**.

	![Sortie de l’activité personnalisée][image-data-factory-ouput-from-custom-activity]

9. Si vous ouvrez le fichier de sortie, la sortie doit avoir l'aspect suivant :

	2 occurrences (s) du terme de recherche « Microsoft » ont été trouvées dans le fichier inputfolder/2015-11-16-00/file.txt.

10.	Utilisez le [portail Azure][azure-preview-portal] ou les applets de commande Azure PowerShell pour surveiller votre fabrique de données, pipelines et jeux de données. Vous pouvez voir les messages de l’outil **ActivityLogger** dans le code concernant l’activité personnalisée dans les journaux (plus précisément, user-0.log) que vous pouvez télécharger à partir du portail ou à l’aide d’applets de commande.

	![Téléchargement des journaux d’activités personnalisées][image-data-factory-download-logs-from-custom-activity]


Pour découvrir la procédure détaillée de surveillance des jeux de données et des pipelines, consultez l’article [Surveiller et gérer les pipelines](data-factory-monitor-manage-pipelines.md).

### Déboguer le pipeline
Le débogage consiste à utiliser quelques techniques de base :

1.	Si la tranche d’entrée n’est pas définie sur **Prêt**, vérifiez que la structure de dossiers d’entrée est correcte et que le fichier file.txt est présent dans les dossiers d’entrée.
2.	Dans la méthode **Execute** de votre activité personnalisée, utilisez l’objet **IActivityLogger** pour consigner les informations qui vous aideront à résoudre les problèmes. Les messages consignés s’afficheront dans le fichier user\_0.log. 

	Dans le panneau **OutputDataset**, cliquez sur la tranche pour afficher le panneau **TRANCHE DE DONNÉES** correspondant à cette tranche. Les **activités exécutées** s’afficheront pour cette tranche. Vous devez normalement voir une exécution d’activité pour la tranche. Si vous cliquez sur Exécuter dans la barre de commandes, vous pouvez démarrer une autre exécution d’activité pour la même tranche.

	Lorsque vous cliquez sur l’exécution d’activité, le panneau **DÉTAILS DE L’EXÉCUTION D’ACTIVITÉ** s’affiche avec une liste de fichiers journaux. Les messages consignés s’afficheront dans le fichier user\_0.log. Lorsqu’une erreur se produit, vous verrez trois exécutions d’activité car le nombre de tentatives est défini sur 3 dans le script JSON du pipeline et de l’activité. Lorsque vous cliquez sur l’exécution de l’activité, vous accédez aux fichiers journaux qui vous permettront de résoudre l’erreur.

	Dans la liste des fichiers journaux, cliquez sur le fichier **user-0.log**. Dans le volet droit s’affichent les résultats de l’utilisation de la méthode **IActivityLogger.Write**.

	Vous devez également vérifier le fichier **system-0.log** pour vérifier les exceptions et messages d’erreur système éventuels.

3.	Incluez le fichier **PDB** dans le fichier zip afin que les détails de l’erreur reflètent certaines informations, telles que la **pile des appels**, lorsqu’une erreur se produit.
4.	Tous les fichiers contenus dans le fichier zip de l’activité personnalisée doivent se trouver au **niveau supérieur** et ne contenir aucun sous-dossier.
5.	Assurez-vous que les paramètres **assemblyName** (MyDotNetActivity.dll), **entryPoint**(MyDotNetActivityNS.MyDotNetActivity), **packageFile** (customactivitycontainer/Mydotnetactivity.zip) et **packageLinkedService** (qui doit pointer vers le stockage d’objets blob Azure contenant le fichier .zip) sont définis sur les valeurs correctes. 
6.	Si vous avez corrigé une erreur et que vous souhaitez relancer le traitement de la tranche, cliquez avec le bouton droit sur la tranche dans le panneau **OutputDataset** puis cliquez sur **Exécuter**. 


## Mettre à jour l’activité personnalisée
Si vous mettez à jour le code de l’activité personnalisée, créez et téléchargez le fichier .zip qui contient les nouveaux fichiers binaires pour le stockage d’objets blob.

## <a name="AzureBatch"></a> Utilisation du service lié Azure Batch
> [AZURE.NOTE]Consultez la rubrique concernant les [concepts de base d’Azure Batch][batch-technical-overview] pour obtenir un aperçu du service Microsoft Azure Batch, et la page [Prise en main de la bibliothèque Azure Batch pour .NET][batch-get-started] pour mettre rapidement en route ce service.

Vous pouvez exécuter vos activités personnalisées .NET à l'aide d’Azure Batch comme ressource de calcul. Vous devez créer vos propres pools Azure Batch et spécifier le nombre d’ordinateurs virtuels, ainsi que d'autres configurations. Les pools Azure Batch fournissent aux clients les fonctionnalités suivantes :

1. Création de pools contenant entre un et des milliers de cœurs.
2. Nombre d'ordinateurs virtuels dimensionnés automatiquement suivant une formule
3. Prise en charge d’ordinateurs virtuels de toute taille
4. Nombre configurable de tâches par ordinateur virtuel
5. Mise en file d’attente d’un nombre illimité de tâches


Voici les étapes générales d’utilisation du service lié Microsoft Azure Batch dans la procédure pas à pas décrite à la section précédente :

1. Créez un compte Azure Batch à l’aide du [portail Azure](http://manage.windowsazure.com). Consultez l’article [Créer et gérer un compte Azure Batch][batch-create-account] pour obtenir des instructions. Notez la clé et le nom du compte Microsoft Azure Batch.

	Vous pouvez également utiliser l’applet de commande [New-AzureBatchAccount][new-azure-batch-account] pour créer un compte Microsoft Azure Batch. Pour obtenir des instructions détaillées sur l’utilisation de cette applet de commande, voir le billet de blog [Using Azure PowerShell to Manage Azure Batch Account][azure-batch-blog].
2. Créez un pool Microsoft Azure Batch. Vous pouvez télécharger le code source de l’outil [Azure Batch Explorer][batch-explorer], compiler, l’utiliser ou utiliser la [bibliothèque Azure Batch pour .NET][batch-net-library] pour créer un pool Azure Batch. Consultez le billet de blog [Azure Batch Explorer Sample Walkthrough][batch-explorer-walkthrough] pour obtenir des instructions détaillées sur l’utilisation d’Azure Batch Explorer.

	Vous pouvez également utiliser l’applet de commande [New-AzureRmBatchPool](https://msdn.microsoft.com/library/mt628690.aspx) pour créer un pool Microsoft Azure Batch.

	Il peut être judicieux de créer le pool Azure Batch avec au moins 2 nœuds de calcul afin que les tranches puissent être traitées en parallèle. Si vous utilisez l’Explorateur Batch :

	- Entrez un ID pour le pool (**ID de pool**). Notez l’**ID du pool**, car vous en aurez besoin lors de la création de la solution Data Factory. 
	- Spécifiez **Windows Server 2012 R2** pour le paramètre de famille du système d’exploitation.
	- Spécifiez **2** comme valeur pour le paramètre **Nombre maximal de tâches par nœud de calcul**.
	- Spécifiez **2** comme valeur pour le paramètre **Nombre de cibles dédiées**. 

	Le service Data Factory crée une tâche dans Azure sous le nom : adf-<pool name>:job-xxx. Une tâche est créée pour chaque exécution d’activité d’une tranche. Si 10 tranches sont prêtes à être traitées, 10 tâches seront créées. Plusieurs tranches peuvent être exécutées en parallèle si vous disposez de plusieurs nœuds de calcul dans le pool. Vous pouvez également avoir plusieurs tranches exécutées sur le même nœud de calcul si le nombre maximum de tâches par nœud de calcul est défini sur une valeur supérieure à 1.
	
	![Tâches de l’Explorateur Batch](./media/data-factory-use-custom-activities/BatchExplorerTasks.png)

	![Data Factory et Batch](./media/data-factory-use-custom-activities/DataFactoryAndBatch.png)

2. Créez un service lié Microsoft Azure Batch à l’aide du modèle JSON suivant. Data Factory Editor affiche un modèle similaire, que vous pouvez utiliser pour commencer. Spécifiez le nom de compte Microsoft Azure Batch, la clé de compte et le nom du pool dans l’extrait de code JSON.

		{
		  "name": "AzureBatchLinkedService",
		  "properties": {
		    "type": "AzureBatch",
		    "typeProperties": {
		      "accountName": "<Azure Batch account name>",
			  "batchUri": "https://<region>.batch.azure.com",
		      "accessKey": "<Azure Batch account key>",
		      "poolName": "<Azure Batch pool name>",
		      "linkedServiceName": "<Specify associated storage linked service reference here>"
		    }
		  }
		}

	> [AZURE.IMPORTANT]L’**URL** du **panneau de compte Azure Batch** est au format suivant : nomducompte.région.batch.azure.com. Pour la propriété **batchUri** dans le fichier JSON, vous devez **supprimer le paramètre « accountname. »** de l’URL et utiliser **accountname** pour la propriété JSON **accountname**.

	Pour la propriété **poolName**, vous pouvez également spécifier l’ID du pool au lieu du nom du pool.

	Consultez la rubrique [Service Microsoft Azure Batch lié](https://msdn.microsoft.com/library/mt163609.aspx) de MSDN pour obtenir une description de ces propriétés.

2.  Dans Data Factory Editor, ouvrez la définition JSON pour le pipeline que vous avez créé dans la procédure pas à pas et remplacez **HDInsightLinkedService** par **AzureBatchLinkedService**.
3.  Vous pouvez modifier les heures de début et de fin du pipeline pour pouvoir tester le scénario avec le service Microsoft Azure Batch.
4.  Vous pouvez voir les tâches Microsoft Azure Batch associées au traitement des tranches dans Azure Batch Explorer, comme illustré dans le schéma suivant.

	![Tâches Microsoft Azure Batch][image-data-factory-azure-batch-tasks]

> [AZURE.NOTE]Le service Data Factory ne prend pas en charge l’option à la demande pour Azure Batch contrairement à HDInsight. Vous pouvez uniquement utiliser votre propre pool Azure Batch dans une fabrique de données Azure.

## Voir aussi

[Mises à jour Azure Data Factory : exécution d’activités personnalisées Azure Data Factory .NET au moyen d’Azure Batch](http://azure.microsoft.com/blog/2015/05/01/azure-data-factory-updates-execute-adf-custom-net-activities-using-azure-batch/).

[batch-net-library]: ../batch/batch-dotnet-get-started.md
[batch-explorer]: https://github.com/Azure/azure-batch-samples/tree/master/CSharp/BatchExplorer
[batch-explorer-walkthrough]: http://blogs.technet.com/b/windowshpc/archive/2015/01/20/azure-batch-explorer-sample-walkthrough.aspx
[batch-create-account]: ../batch/batch-account-create-portal.md
[batch-technical-overview]: ../batch/batch-technical-overview.md
[batch-get-started]: ../batch/batch-dotnet-get-started.md
[monitor-manage-using-powershell]: data-factory-monitor-manage-using-powershell.md
[adf-tutorial]: data-factory-tutorial.md
[use-custom-activities]: data-factory-use-custom-activities.md
[troubleshoot]: data-factory-troubleshoot.md
[data-factory-introduction]: data-factory-introduction.md
[azure-powershell-install]: https://github.com/Azure/azure-sdk-tools/releases


[developer-reference]: http://go.microsoft.com/fwlink/?LinkId=516908
[cmdlet-reference]: http://go.microsoft.com/fwlink/?LinkId=517456

[new-azure-batch-account]: https://msdn.microsoft.com/library/mt125880.aspx
[new-azure-batch-pool]: https://msdn.microsoft.com/library/mt125936.aspx
[azure-batch-blog]: http://blogs.technet.com/b/windowshpc/archive/2014/10/28/using-azure-powershell-to-manage-azure-batch-account.aspx

[nuget-package]: http://go.microsoft.com/fwlink/?LinkId=517478
[azure-developer-center]: http://azure.microsoft.com/develop/net/
[adf-developer-reference]: http://go.microsoft.com/fwlink/?LinkId=516908
[azure-preview-portal]: https://portal.azure.com/

[adfgetstarted]: data-factory-get-started.md
[hivewalkthrough]: data-factory-data-transformation-activities.md

[image-data-factory-ouput-from-custom-activity]: ./media/data-factory-use-custom-activities/OutputFilesFromCustomActivity.png

[image-data-factory-download-logs-from-custom-activity]: ./media/data-factory-use-custom-activities/DownloadLogsFromCustomActivity.png

[image-data-factory-azure-batch-tasks]: ./media/data-factory-use-custom-activities/AzureBatchTasks.png

<!---HONumber=AcomDC_1217_2015-->