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
	ms.date="10/06/2015"
	ms.author="spelluru"/>

# Utilisation des activités personnalisées dans un pipeline Azure Data Factory
Azure Data Factory prend en charge l’utilisation d’activités intégrées telles que **Copier l’activité** et **Activité HDInsight** dans les pipelines dans le but de déplacer et de traiter des données. Vous pouvez également créer une activité personnalisée .NET avec votre propre logique de transformation/de traitement et utiliser l’activité dans un pipeline. Vous pouvez configurer l’activité à exécuter en utilisant un cluster **Azure HDInsight** ou un service **Azure Batch**.

Cet article décrit comment créer une activité personnalisée et l'utiliser dans un pipeline Azure Data Factory. Il fournit également une procédure pas à pas pour créer et utiliser une activité personnalisée. La procédure pas à pas utilise le service HDInsight lié. Pour utiliser le service lié Azure Batch à la place, vous créez un service lié de type **AzureBatch** et l’utilisez dans la section d’activité du pipeline JSON (**linkedServiceName**). Consultez la section [Service lié Azure Batch](#AzureBatch) pour plus d’informations sur l’utilisation d’Azure Batch avec l’activité personnalisée.


## <a name="walkthrough" /> Procédure pas à pas
Cette procédure pas à pas vous fournit des instructions détaillées sur la création d’une activité personnalisée et l’utilisation de l’activité dans un pipeline Microsoft Azure Data Factory. Cette procédure pas à pas développe le didacticiel de la rubrique [Prise en main d’Azure Data Factory][adfgetstarted]. Si vous souhaitez voir fonctionner l'activité personnalisée, vous devez commencer par le didacticiel de prise en main, puis effectuer cette procédure pas à pas.

**Configuration requise :**


- Didacticiel de la rubrique [Prise en main d’Azure Data Factory][adfgetstarted]. Vous devez suivre le didacticiel de cet article avant d’effectuer cette procédure pas à pas.
- Visual Studio 2012 ou 2013
- Téléchargez et installez le [Kit de développement logiciel (SDK) Azure .NET][azure-developer-center].
- Téléchargez la dernière version du [package NuGet pour Microsoft Azure Data Factory](https://www.nuget.org/packages/Microsoft.Azure.Management.DataFactories/) et installez-la. Les instructions sont fournies dans la procédure pas à pas.
- Téléchargez et installez le package NuGet pour Azure Storage. Les instructions sont fournies dans la procédure pas à pas, vous pouvez donc ignorer cette étape.

## Étape 1 : création d’une activité personnalisée

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

		public IDictionary<string, string> Execute(IEnumerable<LinkedService> linkedServices, IEnumerable<Dataset> datasets, Activity activity, IActivityLogger logger)
        {
            IDictionary<string, string> extendedProperties = ((DotNetActivity)activity.TypeProperties).ExtendedProperties;

            AzureStorageLinkedService inputLinkedService, outputLinkedService;
            CustomDataset inputLocation;
            AzureBlobDataset outputLocation;

            Dataset inputDataset = datasets.Single(dataset => dataset.Name == activity.Inputs.Single().Name);
            inputLocation = inputDataset.Properties.TypeProperties as CustomDataset;

            // using First method instead of Single since we are using the same
            // Azure Storage linked service for input and output.
            inputLinkedService = linkedServices.First(linkedService => linkedService.Name == inputDataset.Properties.LinkedServiceName).Properties.TypeProperties as AzureStorageLinkedService;

            string output = string.Empty;

            logger.Write("Before anything...");

            logger.Write("Printing dictionary entities if any...");
            foreach (KeyValuePair<string, string> entry in extendedProperties)
            {
                logger.Write("<key:{0}> <value:{1}>", entry.Key, entry.Value);
            }

            string connectionString = GetConnectionString(inputLinkedService);
            string folderPath = GetFolderPath(inputDataset);

            logger.Write("Reading blob from: {0}", folderPath);

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
                                    logger.Write("First line: [{0}]", line);
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

            Dataset outputDataset = datasets.Single(dataset => dataset.Name == activity.Outputs.Single().Name);
            outputLocation = outputDataset.Properties.TypeProperties as AzureBlobDataset;
            outputLinkedService = linkedServices.First(linkedService => linkedService.Name == outputDataset.Properties.LinkedServiceName).Properties.TypeProperties as AzureStorageLinkedService;

            connectionString = GetConnectionString(outputLinkedService);
            folderPath = GetFolderPath(outputDataset);

            logger.Write("Writing blob to: {0}", folderPath);

            CloudStorageAccount outputStorageAccount = CloudStorageAccount.Parse(connectionString);
            Uri outputBlobUri = new Uri(outputStorageAccount.BlobEndpoint, folderPath + "/" + Guid.NewGuid() + ".csv");

            CloudBlockBlob outputBlob = new CloudBlockBlob(outputBlobUri, outputStorageAccount.Credentials);
            outputBlob.UploadText(output);

            return new Dictionary<string, string>();

        }

9. Ajoutez les méthodes d'assistance suivantes. La méthode **Execute** appelle ces méthodes d’assistance. La méthode **GetConnectionString** récupère la chaîne de connexion Microsoft Azure Storage et la méthode **GetFolderPath**, l’emplacement de l’objet blob.


        private static string GetConnectionString(AzureStorageLinkedService asset)
        {

            if (asset == null)
            {
                return null;
            }

            return asset.ConnectionString;
        }

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


10. Compilez le projet. Cliquez sur l’option **Générer** du menu, puis sur **Générer la solution**.
11. Lancez l’**Explorateur Windows** et accédez au dossier **bin\\debug** ou **bin\\release** (selon le type de build).
12. Créez un fichier **MyDotNetActivity.zip** contenant tous les binaires dans le dossier <project folder>\\bin\\Debug. Si vous le souhaitez, vous pouvez inclure le fichier MyDotNetActivity.pdb afin d’obtenir des détails supplémentaires tels que le numéro de ligne du code source ayant causé le problème en cas de défaillance.
13. Chargez le fichier **MyDotNetActivity.zip** en tant qu’objet blob dans le conteneur d’objets blob **customactvitycontainer** du stockage d’objets blob Azure utilisé par le service lié **StorageLinkedService** dans **ADFTutorialDataFactory**. Créez le conteneur d’objets blob **customactivitycontainer**, le cas échéant.

> [AZURE.NOTE]Si vous ajoutez ce projet d'activité .NET à une solution dans Visual Studio qui contient un projet Data Factory, il est inutile d'effectuer les deux dernières étapes de création du fichier zip et de le télécharger manuellement dans le stockage blob Azure. Lorsque vous publiez des entités Data Factory à l'aide de Visual Studio, ces étapes sont effectuées automatiquement par le processus de publication. Consultez les articles [Concevez votre premier pipeline à l'aide de Visual Studio](data-factory-build-your-first-pipeline-using-vs.md) et [Copie de données à partir d'un objet Blob Azure vers SQL Azure](data-factory-get-started-using-vs.md) pour en savoir plus sur la création et la publication des entités Data Factory à l'aide de Visual Studio.


## Étape 2 : Utiliser l’activité personnalisée dans un pipeline
Voici les étapes que vous allez effectuer lors de cette opération :

1. Création d’un service lié pour le cluster HDInsight sur lequel l’activité personnalisée s’exécute en tant que tâche de mappage uniquement.
2. Création d’une table de sortie produite par le pipeline dans cet exemple.
3. Création et exécution d’un pipeline qui utilise l’activité personnalisée que vous avez créée à l’étape 1.

### Création d’un service lié pour le cluster HDInsight qui sera utilisé pour exécuter l’activité personnalisée.
Le service Azure Data Factory prend en charge la création d'un cluster à la demande et l'utilise pour traiter des données d'entrée afin de produire des données de sortie. Vous pouvez également utiliser votre propre cluster pour effectuer cette opération. Lorsque vous utilisez un cluster HDInsight à la demande, un cluster est créé pour chaque tranche. Par contre, si vous utilisez votre propre cluster HDInsight, le cluster est prêt à traiter la tranche immédiatement. Par conséquent, lorsque vous utilisez un cluster à la demande, vous ne voyez pas les données de sortie aussi rapidement que lorsque vous utilisez votre propre cluster.

> [AZURE.NOTE]Lors de l’exécution, une instance d’activité .NET s’exécute uniquement sur un nœud de travail du cluster HDInsight ; elle ne peut pas être mise à l’échelle pour s’exécuter sur plusieurs nœuds. Cependant, plusieurs instances d’activité .NET peuvent s’exécuter en parallèle sur différents nœuds du cluster HDInsight.

Si, après avoir exécuté le didacticiel [Prise en main d’Azure Data Factory][adfgetstarted], vous avez effectué la procédure pas à pas de la section [Utilisation de Pig et Hive avec Azure Data Factory][hivewalkthrough], vous pouvez ignorer la création de ce service lié et utiliser celui de l’élément ADFTutorialDataFactory.


#### Pour utiliser un cluster HDInsight à la demande

1. Dans le **portail Azure Classic**, cliquez sur l’option **Créer et déployer** de la page d’accueil du logiciel Data Factory.
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

#### Pour utiliser votre propre cluster HDInsight :

1. Dans le **portail Microsoft Azure**, cliquez sur l’option **Créer et déployer** de la page d’accueil du logiciel Data Factory.
2. Dans **Data Factory Editor**, cliquez sur **Nouveau calcul** dans la barre de commandes et sélectionnez **Cluster HDInsight** dans le menu.
2. Procédez comme suit dans le script JSON :
	1. Pour la propriété **clusterUri**, saisissez l’URL de votre cluster HDInsight. Par exemple : https://<clustername>.azurehdinsight.net/.     
	2. Pour la propriété **UserName**, saisissez le nom d’utilisateur ayant accès au cluster HDInsight.
	3. Pour la propriété **Password**, spécifiez le mot de passe de l’utilisateur.
	4. Pour la propriété **LinkedServiceName**, saisissez **StorageLinkedService**. Désigne le service lié que vous avez créé dans le didacticiel de prise en main.

2. Cliquez sur l’option **Déployer** de la barre de commandes pour déployer le service lié.

### Créer une table de sortie

1. Dans **Data Factory Editor**, cliquez sur **Nouveau jeu de données**, puis sur **Stockage d’objets blob Azure** dans la barre de commandes.
2. Remplacez le script JSON affiché dans le volet droit par le script JSON suivant :

		{
		  "name": "OutputTableForCustom",
		  "properties": {
		    "type": "AzureBlob",
		    "linkedServiceName": "StorageLinkedService",
		    "typeProperties": {
		      "folderPath": "adftutorial/customactivityoutput/{Slice}",
		      "partitionedBy": [
		        {
		          "name": "Slice",
		          "value": {
		            "type": "DateTime",
		            "date": "SliceStart",
		            "format": "yyyyMMddHH"
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

2. Cliquez sur l’option **Déployer** de la barre de commandes pour déployer la table.


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
		            "Name": "EmpTableFromBlob"
		          }
		        ],
		        "Outputs": [
		          {
		            "Name": "OutputTableForCustom"
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
		    "start": "2015-02-13T00:00:00Z",
		    "end": "2015-02-14T00:00:00Z",
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
	- Le paramètre **PackageLinkedService** est défini sur l’élément **MyBlobStore**, qui a été créé dans le cadre du didacticiel [Prise en main d’Azure Data Factory][adfgetstarted]. Ce magasin d'objets blob contient le fichier .zip d'activités personnalisées.
	- Le paramètre **PackageFile** est défini sur **customactivitycontainer/MyDotNetActivity.zip**.

4. Cliquez sur l’option **Déployer** de la barre de commandes pour déployer le pipeline.
8. Vérifiez que les fichiers de sortie sont générés dans le stockage d’objets blob, dans le conteneur **adftutorial**.

	![Sortie de l’activité personnalisée][image-data-factory-ouput-from-custom-activity]

9. Si vous ouvrez le fichier de sortie, la sortie doit avoir l'aspect suivant :

	adftutorial/,emp.txt,2,WORKERNODE0,03/27/2015 19:23:28

	(emplacement de l’objet blob), (nom de l’objet blob), (nombre de lignes de l’objet blob), (nœud sur lequel l’activité a été exécutée), (horodatage)

10.	Utilisez le [portail Azure Classic][azure-preview-portal] ou les applets de commande Azure PowerShell pour surveiller votre fabrique de données, pipelines et jeux de données. Vous pouvez voir les messages de l'outil **ActivityLogger** dans le code concernant l'activité personnalisée dans les journaux (plus précisément, user-0.log) que vous pouvez télécharger à partir du portail ou à l'aide d'applets de commande.

	![Téléchargement des journaux d’activités personnalisées][image-data-factory-download-logs-from-custom-activity]


Pour découvrir la procédure détaillée de surveillance des jeux de données et des pipelines, consultez l'article [Surveiller et gérer les pipelines](data-factory-monitor-manage-pipelines.md).

## Mise à jour d’une activité personnalisée
Si vous mettez à jour le code de l’activité personnalisée, créez et téléchargez le fichier .zip qui contient les nouveaux fichiers binaires pour le stockage d’objets blob.

## <a name="AzureBatch"></a> Utilisation du service lié Microsoft Azure Batch
> [AZURE.NOTE]Consultez la rubrique concernant les [concepts de base d'Azure Batch][batch-technical-overview] pour obtenir un aperçu du service Microsoft Azure Batch, et la page [Prise en main de la bibliothèque Azure Batch pour .NET][batch-get-started] pour mettre ce service en route rapidement.

Vous pouvez exécuter vos activités personnalisées .NET à l'aide d’Azure Batch comme ressource de calcul. Vous devez créer vos propres pools Azure Batch et spécifier le nombre d’ordinateurs virtuels, ainsi que d'autres configurations. Les pools Azure Batch fournissent aux clients les fonctionnalités suivantes :

1. Création de pools contenant entre un et des milliers de cœurs.
2. Nombre d'ordinateurs virtuels dimensionnés automatiquement suivant une formule
3. Prise en charge d’ordinateurs virtuels de toute taille
4. Nombre configurable de tâches par ordinateur virtuel
5. Mise en file d’attente d’un nombre illimité de tâches


Voici les étapes générales d’utilisation du service lié Microsoft Azure Batch dans la procédure pas à pas décrite à la section précédente :

1. Créez un compte Azure Batch à l’aide du [portail Azure Classic](http://manage.windowsazure.com). Consultez l'article [Créer et gérer un compte Azure Batch][batch-create-account] pour obtenir des instructions. Notez la clé et le nom du compte Microsoft Azure Batch.

	Vous pouvez également utiliser l’applet de commande [New-AzureBatchAccount][new-azure-batch-account] pour créer un compte Microsoft Azure Batch. Pour obtenir des instructions détaillées sur l’utilisation de cette applet de commande, voir le billet de blog [Using Azure PowerShell to Manage Azure Batch Account][azure-batch-blog].
2. Créez un pool Microsoft Azure Batch. Vous pouvez télécharger le code source de l'outil [Azure Batch Explorer][batch-explorer], compiler, l'utiliser ou utiliser la [bibliothèque Azure Batch pour .NET][batch-net-library] pour créer un pool Azure Batch. Consultez le billet de blog [Azure Batch Explorer Sample Walkthrough][batch-explorer-walkthrough] pour obtenir des instructions détaillées sur l’utilisation d’Azure Batch Explorer.

	Vous pouvez également utiliser l’applet de commande [New-AzureBatchPool][new-azure-batch-pool] pour créer un pool Microsoft Azure Batch.

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

	> [AZURE.IMPORTANT]L'**URL** à partir du **panneau de compte Azure Batch** est au format suivant : nomducompte.région.batch.azure.com. Pour la propriété **batchUri** dans le fichier JSON, vous devez **supprimer le « accountname. »** à partir de l'URL et utiliser **accountname** pour la propriété JSON **accountname**.

	Pour la propriété **poolName** (Nom du pool), vous pouvez également spécifier l'ID du pool au lieu du nom du pool.

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

<!---HONumber=AcomDC_1203_2015-->