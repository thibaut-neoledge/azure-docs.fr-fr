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
	ms.date="06/02/2015" 
	ms.author="spelluru"/>

# Utilisation des activités personnalisées dans un pipeline Azure Data Factory
Fabrique de données Azure prend en charge les activités intégrées telles que **activité copie** et **HDInsight activité** à utiliser dans les pipelines pour déplacer et traiter des données. Vous pouvez également créer une activité personnalisée de .NET avec votre propre logique de transformation/de traitement et utiliser l'activité dans un pipeline. Vous pouvez configurer l'activité à exécuter en utilisant un **Azure HDInsight** cluster ou un **lot Azure** service.

Cet article décrit comment créer une activité personnalisée et l'utiliser dans un pipeline Azure Data Factory. Il fournit également une procédure pas à pas pour créer et utiliser une activité personnalisée. La procédure pas à pas utilise le service HDInsight lié. Pour utiliser le traitement par lots Azure lié service au lieu de cela, vous créez un service de type lié **AzureBatchLinkedService** et son utilisation dans la section de l'activité du pipeline JSON (** linkedServiceName **). Consultez le [Azure lot lié Service](#AzureBatch) section pour plus d'informations sur l'utilisation de commandes Azure avec l'activité personnalisée.

## Composants requis
Téléchargez la dernière version [package NuGet pour la fabrique de données Azure][nuget-package] et l'installer. Instructions se trouvent dans le [procédure pas à pas](#SupportedSourcesAndSinks) dans cet article.

## Création d'une activité personnalisée

Pour créer une activité personnalisée :
 
1.	Créer un **bibliothèque de classes** projet dans Visual Studio 2013.
3. À l'aide des instructions, ajoutez le code suivant en haut du fichier source dans la bibliothèque de classes.
	
		using Microsoft.Azure.Management.DataFactories.Models;
		using Microsoft.DataFactories.Runtime; 

4. Mettre à jour la classe pour implémenter le **IDotNetActivity** interface.
	<ol type='a'>
	<li>
		Dérivez la classe de <b>IDotNetActivity</b>.
		<br/>
		Exemple&#160;: <br/>
		classe publique <b>MyDotNetActivity : IDotNetActivity</b>
	</li>

	<li>
		Implémentez le <b>Execute</b> méthode <b>IDotNetActivity</b> interface
	</li>

</ol>
5. Compilez le projet.


## À l'aide de l'activité personnalisée dans un pipeline
Pour utiliser l'activité personnalisée dans un pipeline :

1.	**Compresser** tous les fichiers binaires à partir de la **bin\\debug** ou **bin\\release** sortie de dossiers pour le projet. 
2.	**Télécharger le code postal** fichier comme un objet blob à votre **stockage d'objets blob Azure**. 
3.	Mise à jour de la **JSON de pipeline** fichier pour faire référence à un fichier zip, activité personnalisée DLL, la classe d'activité et l'objet blob qui contient le fichier zip dans le pipeline de JSON. Dans le fichier JSON :
	<ol type ="a">
	<li><b>Type d'activité</b> doit être définie sur <b>DotNetActivity</b>.</li>
	<li><b>AssemblyName</b> est le nom de la DLL de sortie du projet Visual Studio.</li>
	<li><b>EntryPoint</b> Spécifie le <b>espace de noms</b> et <b>nom</b> de la <b>classe</b> qui implémente la <b>IDotNetActivity</b> interface.</li>
	<li><b>PackageLinkedService</b> est le service lié qui fait référence à l'objet blob qui contient le fichier zip. </li>
	<li><b>PackageFile</b> Spécifie l'emplacement et le nom du fichier zip téléchargé vers le stockage d'objets blob Azure.</li>
	<li><b>LinkedServiceName</b> est le nom du service lié qui lie un cluster HDInsight (à la demande ou votre propre) à une fabrique de données. L'activité personnalisée s'exécute comme une tâche de mappage seul du cluster HDInsight spécifié.</li>
</ol>**Exemple JSON partiel**

		"Name": "MyDotNetActivity",
    	"Type": "DotNetActivity",
    	"Inputs": [{"Name": "EmpTableFromBlob"}],
    	"Outputs": [{"Name": "OutputTableForCustom"}],
		"LinkedServiceName": "myhdinsightcluster",
    	"Transformation":
    	{
	    	"AssemblyName": "MyDotNetActivity.dll",
    	    "EntryPoint": "MyDotNetActivityNS.MyDotNetActivity",
    	    "PackageLinkedService": "MyBlobStore",
    	    "PackageFile": "customactivitycontainer/MyDotNetActivity.zip",

## Mise à jour d'une activité personnalisée
Si vous mettez à jour le code de l'activité personnalisée, créer et télécharger le fichier zip qui contient les nouveaux binaires pour le stockage d'objets blob.

## <a name="walkthrough" /> Procédure pas à pas
Cette procédure pas à pas vous fournit des instructions détaillées sur la création d'une activité personnalisée et l'utilisation de l'activité dans un pipeline de fabrique de données Azure. Cette procédure pas à pas étend le didacticiel à partir de la [prise en main Azure Data Factory][adfgetstarted]. Si vous souhaitez voir fonctionner l'activité personnalisée, vous devez commencer par le didacticiel de prise en main, puis effectuer cette procédure pas à pas.

**Configuration requise :**


- Didacticiel de [prise en main Azure Data Factory][adfgetstarted]. Vous devez suivre le didacticiel à partir de cet article avant d'effectuer cette procédure pas à pas.
- Visual Studio 2012 ou 2013
- Téléchargez et installez [Azure .NET SDK][azure-developer-center]
- Téléchargez la dernière version [package NuGet pour la fabrique de données Azure][nuget-package] et l'installer. Les instructions sont fournies dans la procédure pas à pas.
- Téléchargez et installez le package NuGet pour Azure Storage. Les instructions sont fournies dans la procédure pas à pas, vous pouvez donc ignorer cette étape.

## Étape 1: Créer une activité personnalisée

1.	Créez un projet de bibliothèque de classes .NET.
	<ol type="a">
	<li>Lancez <b>Visual Studio 2012</b> ou <b>Visual Studio 2013</b>.</li>
	<li>Cliquez sur <b>fichier</b>, pointez sur <b>nouveau</b>, puis cliquez sur <b>projet</b>.</li> 
	<li>Développez <b>modèles</b>, puis sélectionnez <b>Visual C#</b>. Dans cette procédure pas à pas, vous utilisez&#160;C#, mais vous pouvez utiliser un autre langage&#160;.NET pour développer l'activité personnalisée.</li> 
	<li>Sélectionnez <b>bibliothèque de classes</b> dans la liste des types de projet à droite.</li>
	<li>Entrez <b>MyDotNetActivity</b> pour le <b>nom</b>.</li> 
	<li>Sélectionnez <b>C:\ADFGetStarted</b> pour le <b>emplacement</b>.</li>
	<li>Cliquez sur <b>OK</b> pour créer le projet.</li>
</ol>
2.  Cliquez sur <b>outils</b>, pointez sur <b>Gestionnaire de Package NuGet</b>, puis cliquez sur <b>Package Manager Console</b>.
3.	Dans la <b>Package Manager Console</b>, exécutez la commande suivante pour importer <b>Microsoft.Azure.Management.DataFactories</b>. 

		Install-Package Microsoft.Azure.Management.DataFactories –Pre

3.	Dans la <b>Package Manager Console</b>, exécutez la commande suivante pour importer <b>Microsoft.DataFactories.Runtime</b>. Remplacez le dossier par l'emplacement qui contient le package NuGet Data Factory téléchargé.

		Install-Package Microsoft.DataFactories.Runtime –Pre

4. Importer le package NuGet de stockage Azure dans le projet.

		Install-Package Azure.Storage

5. Ajoutez le code suivant **à l'aide de** instructions dans le fichier source dans le projet.

		using System.IO;
		using System.Globalization;
		using System.Diagnostics;
	
		using Microsoft.Azure.Management.DataFactories.Models;
		using Microsoft.DataFactories.Runtime; 
	
		using Microsoft.WindowsAzure.Storage;
		using Microsoft.WindowsAzure.Storage.Blob;
  
6. Modifier le nom de la **espace de noms** à **MyDotNetActivityNS**.

		namespace MyDotNetActivityNS

7. Modifier le nom de la classe **MyDotNetActivity** et dériver de la **IDotNetActivity** interface comme indiqué ci-dessous.

		public class MyDotNetActivity : IDotNetActivity

8. Implémenter (Ajouter) le **Execute** procédé de la **IDotNetActivity** interface à la **MyDotNetActivity** classe et copiez l'exemple de code suivant à la méthode.

	Le **inputTables** et **outputTables** paramètres représentent des tables d'entrée et de sortie de l'activité comme leur nom le suggère. Vous pouvez voir les messages que vous ouvrez une session à l'aide de la **Enregistreur** objet dans le fichier journal que vous pouvez télécharger à partir du portail Azure ou à l'aide des applets de commande. Le **extendedProperties** dictionnaire contient la liste des propriétés étendues que vous spécifiez dans le fichier JSON pour l'activité et leurs valeurs.

	L'exemple de code suivant compte le nombre de lignes dans l'objet blob en entrée et génère le contenu suivant dans l'objet blob de sortie : chemin d'accès à l'objet blob, nombre de lignes dans l'objet blob, l'ordinateur sur lequel l'exécution de l'activité, date-heure actuelle.

        public IDictionary<string, string> Execute(
                    IEnumerable<ResolvedTable> inputTables, 
                    IEnumerable<ResolvedTable> outputTables, 
                    IDictionary<string, string> extendedProperties, 
                    IActivityLogger logger)
        {
            string output = string.Empty;

            logger.Write(TraceEventType.Information, "Before anything...");

            logger.Write(TraceEventType.Information, "Printing dictionary entities if any...");
            foreach (KeyValuePair<string, string> entry in extendedProperties)
            {
                logger.Write(TraceEventType.Information, "<key:{0}> <value:{1}>", entry.Key, entry.Value);
            }

            foreach (ResolvedTable inputTable in inputTables)
            {
                string connectionString = GetConnectionString(inputTable.LinkedService);
                string folderPath = GetFolderPath(inputTable.Table);

                if (String.IsNullOrEmpty(connectionString) ||
                    String.IsNullOrEmpty(folderPath))
                {
                    continue;
                }

                logger.Write(TraceEventType.Information, "Reading blob from: {0}", folderPath);

                CloudStorageAccount inputStorageAccount = CloudStorageAccount.Parse(connectionString);
                CloudBlobClient inputClient = inputStorageAccount.CreateCloudBlobClient();

                BlobContinuationToken continuationToken = null;

                do
                {
                    BlobResultSegment result = inputClient.ListBlobsSegmented(folderPath, 
												true, 
												BlobListingDetails.Metadata, 
												null, 
												continuationToken, 
												null, 
												null);
                    foreach (IListBlobItem listBlobItem in result.Results)
                    {
                        CloudBlockBlob inputBlob = listBlobItem as CloudBlockBlob;
                        int count = 0;
                        if (inputBlob != null)
                        {
                            using (StreamReader sr = new StreamReader(inputBlob.OpenRead()))
                            {
                                while (!sr.EndOfStream)
                                {
                                    string line = sr.ReadLine();
                                    if (count == 0)
                                    {
                                        logger.Write(TraceEventType.Information, "First line: [{0}]", line);
                                    }
                                    count++;
                                }

                            }

                        }
                        output += string.Format(CultureInfo.InvariantCulture,
                                        "{0},{1},{2},{3},{4}\n",
                                        folderPath,
                                        inputBlob.Name,
                                        count,
                                        Environment.MachineName,
                                        DateTime.UtcNow);

                    }
                    continuationToken = result.ContinuationToken;

                } while (continuationToken != null);
            }

            foreach (ResolvedTable outputTable in outputTables)
            {
                string connectionString = GetConnectionString(outputTable.LinkedService);
                string folderPath = GetFolderPath(outputTable.Table);

                if (String.IsNullOrEmpty(connectionString) ||
                    String.IsNullOrEmpty(folderPath))
                {
                    continue;
                }

                logger.Write(TraceEventType.Information, "Writing blob to: {0}", folderPath);

                CloudStorageAccount outputStorageAccount = CloudStorageAccount.Parse(connectionString);
                Uri outputBlobUri = new Uri(outputStorageAccount.BlobEndpoint, folderPath + "/" + Guid.NewGuid() + ".csv");

                CloudBlockBlob outputBlob = new CloudBlockBlob(outputBlobUri, outputStorageAccount.Credentials);
                outputBlob.UploadText(output);

            }
            return new Dictionary<string, string>();

        }

9. Ajoutez les méthodes d'assistance suivantes. Le **Execute** méthode appelle ces méthodes d'assistance. Le **GetConnectionString** méthode récupère la chaîne de connexion de stockage Azure et **GetFolderPath** méthode récupère l'emplacement de l'objet blob.


        private static string GetConnectionString(LinkedService asset)
        {
            AzureStorageLinkedService storageAsset;
            if (asset == null)
            {
                return null;
            }

            storageAsset = asset.Properties as AzureStorageLinkedService;
            if (storageAsset == null)
            {
                return null;
            }

            return storageAsset.ConnectionString;
        }

        private static string GetFolderPath(Table dataArtifact)
        {
            AzureBlobLocation blobLocation;
            if (dataArtifact == null || dataArtifact.Properties == null)
            {
                return null;
            }

            blobLocation = dataArtifact.Properties.Location as AzureBlobLocation;
            if (blobLocation == null)
            {
                return null;
            }

            return blobLocation.FolderPath;
        }
   


10. Compilez le projet. Cliquez sur **Build** dans le menu et cliquez sur **Générer la Solution**.
11. Lancez **l'Explorateur Windows**, et accédez à **bin\\debug** ou **bin\\release** type en fonction du build.
12. Créer un fichier zip **MyDotNetActivity.zip** qui contiennent tous les fichiers binaires dans le <project folder>dossier \\bin\\Debug.
13. Télécharger **MyDotNetActivity.zip** comme un objet blob dans le conteneur d'objets blob : **customactvitycontainer** dans Azure stockage d'objets blob qui le **MyBlobStore** liés de service dans le **ADFTutorialDataFactory** utilise. Créez le conteneur d'objets blob **blobcustomactivitycontainer** si elle n'existe pas déjà. 


## Étape 2: Utiliser l'activité personnalisée dans un pipeline
Voici les étapes que vous allez effectuer cette étape :

1. Créer un service lié pour le cluster HDInsight sur lequel l'activité personnalisée s'exécutera comme une tâche de mappage. 
2. Créer une table de sortie produit par le pipeline dans cet exemple.
3. Créer et exécuter un pipeline qui utilise l'activité personnalisée que vous avez créé à l'étape 1. 
 
### Créer un service de cluster HDInsight qui sera utilisé pour exécuter l'activité personnalisée lié
Le service Azure Data Factory prend en charge la création d'un cluster à la demande et l'utilise pour traiter des données d'entrée afin de produire des données de sortie. Vous pouvez également utiliser votre propre cluster pour effectuer cette opération. Lorsque vous utilisez un cluster HDInsight à la demande, un cluster est créé pour chaque tranche. Par contre, si vous utilisez votre propre cluster HDInsight, le cluster est prêt à traiter la tranche immédiatement. Par conséquent, lorsque vous utilisez un cluster à la demande, vous ne voyez pas les données de sortie aussi rapidement que lorsque vous utilisez votre propre cluster.

> [AZURE.NOTE]Lors de l'exécution, une instance d'une activité .NET s'exécute uniquement sur un nœud de travail du cluster HDInsight ; Il ne peut pas être mis à l'échelle pour s'exécuter sur plusieurs nœuds. Plusieurs instances d'activité .NET peuvent s'exécuter en parallèle sur différents nœuds du cluster HDInsight.

Si vous avez étendu le [prise en main Azure Data Factory][adfgetstarted] didacticiel avec la procédure pas à pas [utilisation de Pig et Hive avec Azure Data Factory][hivewalkthrough], vous pouvez ignorer la création de ce service lié et utiliser le service lié vous disposez déjà dans le ADFTutorialDataFactory.


#### Pour utiliser un cluster HDInsight à la demande

1. Dans le **Azure Portal**, cliquez sur **auteur et le déploiement** dans la page d'accueil de fabrique de données.
2. Dans l'éditeur de fabrique de données, cliquez sur **Nouveau calcul** à partir de la barre de commandes et sélectionnez **cluster HDInsight sur demande** dans le menu.
2. Procédez comme suit dans le script JSON : 
	1. Pour le **clusterSize** propriété, spécifiez la taille du cluster HDInsight.
	2. Pour le **jobsContainer** propriété, spécifiez le nom du conteneur par défaut où les journaux de cluster seront stockés. Pour les besoins de ce didacticiel, spécifiez **adfjobscontainer**.
	3. Pour le **timeToLive** propriété, spécifiez la durée pendant laquelle le client peut être inactif avant d'être supprimé. 
	4. Pour le **version** propriété, spécifiez la version de HDInsight que vous souhaitez utiliser. Si vous excluez cette propriété, la dernière version est utilisée.  
	5. Pour le **linkedServiceName**, spécifiez **StorageLinkedService** que vous avez créé à la méthode Get de démarrer le didacticiel. 

			{
		    	"name": "HDInsightOnDemandLinkedService",
				    "properties": {
		    	    "type": "HDInsightOnDemandLinkedService",
		    	    "clusterSize": "4",
		    	    "jobsContainer": "adfjobscontainer",
		    	    "timeToLive": "00:05:00",
		    	    "version": "3.1",
		    	    "linkedServiceName": "StorageLinkedService"
		    	}
			}

2. Cliquez sur **déployer** sur la barre de commandes pour déployer le service lié.
   
#### Pour utiliser votre propre cluster HDInsight : 

1. Dans le **Azure Portal**, cliquez sur **auteur et le déploiement** dans la page d'accueil de fabrique de données.
2. Dans le **données fabrique éditeur**, cliquez sur **Nouveau calcul** à partir de la barre de commandes et sélectionnez **cluster HDInsight** à partir du menu.
2. Procédez comme suit dans le script JSON : 
	1. Pour le **clusterUri** propriété, entrez l'URL de votre HDInsight. Par exemple : https://<clustername>.azurehdinsight.net/     
	2. Pour le **nom d'utilisateur** propriété, entrez le nom d'utilisateur qui a accès au cluster HDInsight.
	3. Pour le **mot de passe** propriété, entrez le mot de passe de l'utilisateur. 
	4. Pour le **LinkedServiceName** propriété, entrez **StorageLinkedService**. Désigne le service lié que vous avez créé dans le didacticiel démarré Get. 

2. Cliquez sur **déployer** sur la barre de commandes pour déployer le service lié.

### Créer une table de sortie

1. Dans le **éditeur Data Factory**, cliquez sur **nouveau dataset**, puis cliquez sur **stockage d'objets Blob Azure** à partir de la barre de commandes.
2. Remplacez le script JSON dans le volet droit par le script JSON suivant :

		{
    		"name": "OutputTableForCustom",
    		"properties":
    		{
        		"location": 
        		{
					"type": "AzureBlobLocation",
					"folderPath": "adftutorial/customactivityoutput/{Slice}",
					"partitionedBy": [ { "name": "Slice", "value": { "type": "DateTime", "date": "SliceStart", "format": "yyyyMMddHH" } }],

					"linkedServiceName": "MyBlobStore"
        		},
        		"availability": 
        		{
            		"frequency": "hour",
            		"interval": 1
        		}   
    		}
		}


 	Emplacement de sortie est **customactivityoutput/adftutorial/AAAAMMJJHH/** où AAAAMMJJHH est l'année mois, date et heure de la section en cours de production. Voir [référence du développeur][adf-developer-reference] pour plus d'informations.

2. Cliquez sur **déployer** sur la barre de commandes pour déployer le tableau.


### Créer et exécuter un pipeline qui utilise l'activité personnalisée
   
1. Dans l'éditeur de fabrique de données, cliquez sur **nouveau pipeline** sur la barre de commandes. Si vous ne voyez pas la commande, cliquez sur **... (Sélection)** pour l'afficher. 
2. Remplacez le JSON dans le volet droit par le script JSON suivant. Si vous souhaitez utiliser votre propre cluster et suivi les étapes pour créer le **HDInsightLinkedService** lié service, remplacez **HDInsightOnDemandLinkedService** avec **HDInsightLinkedService** dans le JSON suivant. 

		{
    		"name": "ADFTutorialPipelineCustom",
    		"properties":
    		{
        		"description" : "Use custom activity",
        		"activities":
        		[
					{
                		"Name": "MyDotNetActivity",
                     	"Type": "DotNetActivity",
                     	"Inputs": [{"Name": "EmpTableFromBlob"}],
                     	"Outputs": [{"Name": "OutputTableForCustom"}],
						"LinkedServiceName": "HDInsightLinkedService",
                     	"Transformation":
                     	{
                        	"AssemblyName": "MyDotNetActivity.dll",
                            "EntryPoint": "MyDotNetActivityNS.MyDotNetActivity",
                            "PackageLinkedService": "MyBlobStore",
                            "PackageFile": "customactivitycontainer/MyDotNetActivity.zip",
                            "ExtendedProperties":
							{
								"SliceStart": "$$Text.Format('{0:yyyyMMddHH-mm}', Time.AddMinutes(SliceStart, 0))"
							}
                      	},
                        "Policy":
                        {
                        	"Concurrency": 1,
                            "ExecutionPriorityOrder": "OldestFirst",
                            "Retry": 3,
                            "Timeout": "00:30:00",
                            "Delay": "00:00:00"		
						}
					}
        		],
				"start": "2015-02-13T00:00:00Z",
        		"end": "2015-02-14T00:00:00Z",
        		"isPaused": false
			}
		}

	> [AZURE.NOTE]Remplacez **StartDateTime** valeur avec les trois jours précédant la date du jour et **EndDateTime** valeur avec la date du jour. StartDateTime et EndDateTime doivent être dans [au format ISO](http://en.wikipedia.org/wiki/ISO_8601). Par exemple : 2014-10-14T16:32:41Z. La table de sortie est planifiée pour être produite tous les jours, trois tranches seront donc produites.

	Notez les points suivants :

	- Il existe une activité dans la section des activités et il est de type : **DotNetActivity**.
	- Utilisez la même table d'entrée **EmpTableFromBlob** que vous avez utilisé dans la méthode Get de démarrer le didacticiel.
	- Utiliser une nouvelle table de sortie **OutputTableForCustom** que vous allez créer à l'étape suivante.
	- **AssemblyName** est définie sur le nom de la DLL : **MyActivities.dll**.
	- **EntryPoint** a **MyDotNetActivityNS.MyDotNetActivity**.
	- **PackageLinkedService** a **MyBlobStore** qui a été créé dans le cadre du didacticiel de [prise en main Azure Data Factory][adfgetstarted]. Ce magasin d'objets blob contient le fichier .zip d'activités personnalisées.
	- **PackageFile** a **customactivitycontainer/MyDotNetActivity.zip**.
     
4. Cliquez sur **déployer** sur la barre de commandes pour déployer le pipeline.
8. Vérifiez que les fichiers de sortie sont générées dans le stockage blob dans le **adftutorial** conteneur.

	![la sortie à partir de l'activité personnalisée][image-data-factory-ouput-from-custom-activity]

9. Si vous ouvrez le fichier de sortie, la sortie doit avoir l'aspect suivant :
	
	adftutorial/,emp.txt,2,WORKERNODE0,03/27/2015 19:23:28

	(objets blob emplacement), (nom de l'objet blob) (nombre de lignes dans l'objet blob), (nœud sur lequel l'activité a été exécutée), (horodatage)

10.	Utilisez le [Azure Portal][azure-preview-portal] ou applets de commande PowerShell Azure pour surveiller votre fabrique de données, les pipelines et les jeux de données. Vous pouvez voir les messages à partir de la **ActivityLogger** dans le code de l'activité personnalisée dans les journaux que vous pouvez télécharger à partir du portail ou à l'aide des applets de commande.

	![Télécharger les journaux d'activité personnalisée][image-data-factory-download-logs-from-custom-activity]
   
Voir [prise en main Azure Data Factory][adfgetstarted] pour obtenir la procédure détaillée pour la surveillance des datasets et des pipelines.
    
## <a name="AzureBatch"></a> À l'aide de commandes Azure lié service 
> [AZURE.NOTE]Voir [présentation technique du lot Azure][batch-technical-overview] pour une vue d'ensemble du lot Azure service et consultez [mise en route avec la bibliothèque de commandes Azure pour .NET][batch-get-started] pour commencer rapidement avec le service de traitement par lots Azure.

Voici les étapes générales pour l'utilisation de Service Azure lot lié à la procédure décrite dans la section précédente :

1. Créez un compte Azure lot à l'aide du portail de gestion Azure. Voir [présentation technique du lot Azure][batch-create-account] article pour obtenir des instructions. Notez que la clé de compte et de nom de compte Azure lot. 

	Vous pouvez également utiliser [New-AzureBatchAccount][new-azure-batch-account] applet de commande pour créer un compte Azure lot. Voir [à l'aide de Azure PowerShell pour gérer le compte Azure lot][azure-batch-blog] pour obtenir des instructions détaillées sur l'utilisation de cette applet de commande. 
2. Créer un pool de traitement par lots Azure. Vous pouvez télécharger et utiliser le [outil Explorateur de lot Azure][batch-explorer] (ou) utiliser [bibliothèque de lot Azure pour .NET][batch-net-library] pour créer un pool de traitement par lots Azure. Voir [procédure d'exemple Azure lot Explorer][batch-explorer-walkthrough] pour obtenir des instructions détaillées pour l'utilisation de l'Explorateur de lot de Windows Azure.
	
	Vous pouvez également utiliser [New-AzureBatchPool][new-azure-batch-pool] applet de commande pour créer un pool de traitement par lots Azure.

2. Créer un Service lié Azure lot à l'aide du modèle JSON suivant. L'éditeur de la fabrique de données affiche un modèle similaire pour vous pour commencer. Spécifiez le nom du compte Azure lot, le nom de clé et le pool de compte dans l'extrait de JSON.

		{
		    "name": "AzureBatchLinkedService",
		    "properties": {
		        "type": "AzureBatchLinkedService",
		        "accountName": "<Azure Batch account name>",
		        "accessKey": "<Azure Batch account key>",
		        "poolName": "<Azure Batch pool name>",
		        "linkedServiceName": "<Specify associated storage linked service reference here>"
		  }
		}

	Voir [rubrique Azure lot lié Service MSDN](https://msdn.microsoft.com/library/mt163609.aspx) pour obtenir une description de ces propriétés.

2.  Dans l'éditeur de fabrique de données, ouvrez la définition de JSON pour le pipeline que vous avez créé dans la procédure pas à pas et remplacez **HDInsightLinkedService** avec **AzureBatchLinkedService**.
3.  Voulez-vous modifier les heures de début et de fin pour le pipeline afin que vous pouvez tester le scénario avec le service de traitement par lots Azure. 
4.  Vous pouvez voir les tâches de traitement par lots Azure associés au traitement des secteurs de l'Explorateur de lot Azure comme indiqué dans le diagramme suivant.

	![Tâches de traitement par lots Azure][image-data-factory-azure-batch-tasks]

## Voir aussi

[Mises à jour de fabrique de données azure : les activités de Execute ADF personnalisée .NET à l'aide de commandes Azure](http://azure.microsoft.com/blog/2015/05/01/azure-data-factory-updates-execute-adf-custom-net-activities-using-azure-batch/).

[batch-net-library]: ../batch/batch-dotnet-get-started.md
[batch-explorer]: https://github.com/Azure/azure-batch-samples/tree/master/CSharp/BatchExplorer
[batch-explorer-walkthrough]: http://blogs.technet.com/b/windowshpc/archive/2015/01/20/azure-batch-explorer-sample-walkthrough.aspx
[batch-create-account]: ../batch/batch-technical-overview.md/#batch-concepts
[batch-technical-overview]: ../batch/batch-technical-overview.md
[batch-get-started]: ../batch/batch-dotnet-get-started.md
[monitor-manage-using-powershell]: data-factory-monitor-manage-using-powershell.md
[use-onpremises-datasources]: data-factory-use-onpremises-datasources.md
[adf-tutorial]: data-factory-tutorial.md
[use-custom-activities]: data-factory-use-custom-activities.md
[use-pig-and-hive-with-data-factory]: data-factory-pig-hive-activities.md
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
[hivewalkthrough]: data-factory-pig-hive-activities.md

[image-data-factory-ouput-from-custom-activity]: ./media/data-factory-use-custom-activities/OutputFilesFromCustomActivity.png

[image-data-factory-download-logs-from-custom-activity]: ./media/data-factory-use-custom-activities/DownloadLogsFromCustomActivity.png

[image-data-factory-azure-batch-tasks]: ./media/data-factory-use-custom-activities/AzureBatchTasks.png

<!---HONumber=GIT-SubDir--> 