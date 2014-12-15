<properties title="Use custom activities in an Azure Data Factory pipeline" pageTitle="Utilisation des activités personnalisées dans un pipeline Azure Data Factory" description="Learn how to create custom activities and use them in an Azure Data Factory pipeline." metaKeywords=""  services="data-factory" solutions=""  documentationCenter="" authors="spelluru" manager="jhubbard" editor="monicar" />

<tags ms.service="data-factory" ms.workload="data-services" ms.tgt_pltfrm="na" ms.devlang="na" ms.topic="article" ms.date="01/01/1900" ms.author="spelluru" />

# Utilisation des activités personnalisées dans un pipeline Azure Data Factory
Azure Data Factory prend en charge des activités intégrées telles que **Activité de copie** et **Activité HDInsight** à utiliser dans les pipelines pour déplacer et traiter les données. Vous pouvez également créer une activité personnalisée avec votre propre logique de transformation/de traitement et utiliser l'activité dans un pipeline. L'activité personnalisée s'exécute comme une tâche de mappage sur un cluster HDInsight, ce qui vous oblige à lier un cluster HDInsight pour l'activité personnalisée dans votre pipeline.
 
Cet article décrit comment créer une activité personnalisée et l'utiliser dans un pipeline Azure Data Factory. Il fournit également une procédure pas à pas pour créer et utiliser une activité personnalisée.

## Création d'une activité personnalisée

Pour créer une activité personnalisée :
 
1.	Créez un projet de **bibliothèque de classes** dans Visual Studio 2013.
2.	Téléchargez le [package NuGet pour Azure Data Factory][nuget-package] et installez-le. Les instructions d'installation sont détaillées dans la procédure pas à pas.
3. À l'aide des instructions, ajoutez le code suivant en haut du fichier source dans la bibliothèque de classes.
	
		using Microsoft.Azure.Management.DataFactories.Models;
		using Microsoft.DataFactories.Runtime; 

4. Mettez à jour la classe pour implémenter l'interface **ICustomActivity**.
	<ol type='a'>
		<li>
			Dérivez la classe depuis <b>ICustomActivity</b>.
			<br/>
			Exemple : <br/>
			public class <b>MyCustomActivity : ICustomActivity</b>
		</li>

		<li>
			Implémentez la méthode  <b>Execute</b> de l'interface <b>ICustomActivity</b> 
		</li>

	</ol>
5. Compilez le projet.


## Utilisation de l'activité personnalisée dans un pipeline
Pour utiliser l'activité personnalisée dans un pipeline :

1.	**Compressez** tous les fichiers binaires des dossiers de sortie **bin\debug** ou **bin\release** pour le projet. 
2.	**Téléchargez le fichier .zip** comme objet blob sur votre **stockage d'objets blob Azure**. 
3.	Mettez à jour le fichier **JSON du pipeline** pour faire référence au fichier .zip, à la DLL d'activité personnalisée, à la classe d'activité et à l'objet blob qui contient le fichier .zip dans le fichier JSON du pipeline. Dans le fichier JSON :
	<ol type ="a">
		<li><b>Le type de l'activité</b> doit être défini sur <b>CustomActivity</b>.</li>
		<li><b>AssemblyName</b> est le nom de la DLL de sortie du projet Visual Studio.</li>
		<li><b>EntryPoint</b> spécifie l'<b>espace de noms</b> et le <b>nom</b> de la <b>classe</b> qui implémente l'interface <b>ICustomActivity</b>.</li>
		<li><b>PackageLinkedService</b> est le service lié qui fait référence à l'objet blob qui contient le fichier .zip. </li>
		<li><b>PackageFile</b> spécifie l'emplacement et le nom du fichier .zip qui a été téléchargé dans le stockage d'objets blob Azure.</li>
		<li><b>LinkedServiceName</b> est le nom du service lié qui lie un cluster HDInsight (à la demande ou à vous) à une fabrique de données. L'activité personnalisée s'exécute comme une tâche de mappage seul du cluster HDInsight spécifié.</li>
	</ol>

	

	**Exemple JSON partiel**

		"Name": "MyCustomActivity",
    	"Type": "CustomActivity",
    	"Inputs": [{"Name": "EmpTableFromBlob"}],
    	"Outputs": [{"Name": "OutputTableForCustom"}],
		"LinkedServiceName": "myhdinsightcluster",
    	"Transformation":
    	{
	    	"AssemblyName": "MyCustomActivity.dll",
    	    "EntryPoint": "MyCustomActivityNS.MyCustomActivity",
    	    "PackageLinkedService": "MyBlobStore",
    	    "PackageFile": "customactivitycontainer/MyCustomActivity.zip",

## Pour mettre à jour l'activité personnalisée
Si vous mettez à jour le code de l'activité personnalisée, que vous le générez et que vous téléchargez le fichier .zip qui contient les nouveaux binaires pour le stockage d'objets blob, vous devez remplacer le pipeline existant qui utilise l'activité personnalisée en exécutant **New-AzureDataFactoryPipeline**. Lorsque vous créez un pipeline qui utilise l'activité personnalisée, le fichier .zip est copié du stockage d'objets blob spécifié vers un conteneur Azure Data Factory sur le stockage d'objets blob associé au cluster HDInsight. Cette opération se produit uniquement à la création du pipeline. Par conséquent, vous devez recréer le pipeline lorsque vous mettez à jour l'activité personnalisée. 

La procédure suivante fournit des instructions pas à pas pour créer une activité personnalisée et utiliser l'activité dans un pipeline Azure Data Factory. Cette procédure pas à pas développe le didacticiel de la rubrique [Prise en main d'Azure Data Factory][adfgetstarted]. Si vous souhaitez voir fonctionner l'activité personnalisée, vous devez commencer par le didacticiel de prise en main, puis effectuer cette procédure pas à pas. 

## Procédure pas à pas
**Configuration requise**


- Didacticiel de la rubrique [Prise en main d'Azure Data Factory][adfgetstarted]. Vous devez commencer par le didacticiel de cet article, puis continuer avec cette procédure pas à pas.
- Visual Studio 2012 ou 2013
- Téléchargez et installez le [Kit de développement logiciel (SDK) Windows Azure .NET][azure-developer-center]
- Téléchargez les [packages NuGet pour Azure Data Factory][nuget-package].
- Téléchargez et installez le package NuGet pour Azure Storage. Les instructions sont fournies dans la procédure pas à pas, vous pouvez donc ignorer cette étape.

### Étape 1 : création d'une activité personnalisée

1.	Créez un projet de bibliothèque de classes .NET.
	<ol type="a">
		<li>Lancez <b>Visual Studio 2012</b> ou <b>Visual Studio 2013</b>.</li>
		<li>Cliquez sur <b>Fichier</b>,pointez sur <b>Nouveau</b>, puis sur <b>Projet</b>.</li> 
		<li>Développez <b>Modèles</b>, puis sélectionnez <b>Visual C#</b>. Dans cette procédure pas à pas, vous utilisez C#, mais vous pouvez utiliser un autre langage .NET pour développer l'activité personnalisée.</li> 
		<li>Sélectionnez la <b>bibliothèque de classes</b> dans la liste des types de projet sur la droite.</li>
		<li>Entrez <b>MyCustomActivity</b> comme <b>Nom</b>.</li> 
		<li>Sélectionnez <b>C:\ADFGetStarted</b> comme <b>Emplacement</b>.</li>
		<li>Cliquez sur <b>OK</b> pour créer le projet.</li>
	</ol>
2.  Cliquez sur <b>Outils</b>, pointez sur <b>Gestionnaire de package NuGet</b>, puis cliquez sur <b>Console du gestionnaire de package</b>.
3.	Dans la <b>console du gestionnaire de package</b>, exécutez la commande suivante pour importer la fabrique <b>Microsoft.Azure.Management.DataFactories</b> que vous avez téléchargée. Remplacez le dossier par l'emplacement qui contient le package NuGet Data Factory téléchargé.

		Install-Package Microsoft.Azure.Management.DataFactories -Source d:\packages -Pre

3.	Dans la <b>console de gestionnaire de package</b>, exécutez la commande suivante pour importer <b>Microsoft.DataFactories.Runtime</b>. Remplacez le dossier par l'emplacement qui contient le package NuGet Data Factory téléchargé.

		Install-Package Microsoft.DataFactories.Runtime -Source d:\packages -Pre

4. Importez le package NuGet Windows Azure Storage dans le projet.

		Install-Package WindowsAzure.Storage

5. Ajoutez les instructions **using** suivantes au fichier source dans le projet.

		using System.IO;
		using System.Globalization;
	
		using Microsoft.Azure.Management.DataFactories.Models;
		using Microsoft.DataFactories.Runtime; 
	
		using Microsoft.WindowsAzure.Storage;
		using Microsoft.WindowsAzure.Storage.Blob;
  
6. Définissez le nom de l'**espace de noms** sur **MyCustomActivityNS**.

		namespace MyCustomActivityNS

7. Remplacez le nom de la classe **MyCustomActivity** et dérivez-la de l'interface **ICustomActivity** comme indiqué ci-dessous.

		public class MyCustomActivity : ICustomActivity

8. Implémentez (ajoutez) la méthode **Execute** de l'interface **ICustomActivity** à la classe **MyCustomActivity** et copiez l'exemple de code suivant dans la méthode. 

	Les paramètres **inputTables** et **outputTables** représentent les tables d'entrée et de sortie de l'activité. Vous pouvez voir les messages que vous consignez à l'aide de l'objet **logger** dans le fichier journal que vous pouvez télécharger à partir du portail Azure ou à l'aide des cmdlets. Le dictionnaire **extendedProperties** contient la liste des propriétés étendues que vous spécifiez dans le fichier JSON pour l'activité et leurs valeurs. 

	L'exemple de code suivant compte le nombre de lignes dans l'objet blob d'entrée et produit le contenu suivant dans l'objet blob de sortie : chemin d'accès à l'objet blob, nombre de lignes dans l'objet blob, ordinateur sur lequel l'activité s'exécute, date-heure actuelles.

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
										"{0},{1},{2},{3}\n", 
										folderPath, 
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

9. Ajoutez les méthodes d'assistance suivantes. La méthode **Execute** appelle ces méthodes d'assistance. La méthode **GetConnectionString** récupère la chaîne de connexion Azure Storage et la méthode **GetFolderPath** récupère l'emplacement de l'objet blob. 


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
   


10. Compilez le projet. Dans le menu, cliquez sur **Générer** et sur **Générer la Solution**.
11. Lancez l'**Explorateur Windows** et accédez au dossier **bin\debug** ou **bin\release** selon le type de version.
12. Créez un fichier .zip **MyCustomActivity.zip** qui contient tous les fichiers binaires du dossier <dossier de projet>\bin\Debug.
	![zip output binaries][image-data-factory-zip-output-binaries]
13. Téléchargez **MyCustomActivity.zip** comme objet blob sur le conteneur d'objets blob : **customactivitycontainer** dans le stockage d'objets blob Azure utilisé par le service lié **MyBlobStore** dans **ADFTutorialDataFactory**.  Créez le conteneur d'objets blob **blobcustomactivitycontainer** s'il n'existe pas déjà. 
    ![upload zip to blob][image-data-factory-upload-zip-to-blob]

### Créez un service lié pour   le cluster HDInsight qui servira pour exécuter l'activité personnalisée
Le service Azure Data Factory prend en charge la création d'un cluster à la demande et l'utilise pour traiter des données d'entrée afin de produire des données de sortie. Vous pouvez également utiliser votre propre cluster pour effectuer cette opération. Lorsque vous utilisez un cluster HDInsight à la demande, un cluster est créé pour chaque tranche. Par contre, si vous utilisez votre propre cluster HDInsight, le cluster est prêt à traiter la tranche immédiatement. Par conséquent, lorsque vous utilisez un cluster à la demande, vous ne voyez pas les données de sortie aussi rapidement que lorsque vous utilisez votre propre cluster. Pour les besoins de l'exemple, nous allons utiliser un cluster à la demande. 

> [WACOM.NOTE] Si vous avez prolongé le didacticiel [Prise en main d'Azure Data Factory][adfgetstarted] avec la procédure pas à pas [Utilisation de Pig et Hive avec Azure Data Factory][hivewalkthrough], vous pouvez ignorer la création de ce service lié et utiliser celui de ADFTutorialDataFactory. 

#### Pour utiliser un cluster HDInsight à la demande

1. Créez un fichier JSON nommé **HDInsightOnDemandCluster.json** avec le contenu suivant et enregistrez-le dans le dossier **C:\ADFGetStarted\Custom**.


		{
    		"name": "HDInsightOnDemandCluster",
    		"properties": 
    		{
        		"type": "HDInsightOnDemandLinkedService",
				"clusterSize": 4,
        		"jobsContainer": "adftutorialjobscontainer",
        		"timeToLive": "00:05:00",
        		"linkedServiceName": "MyBlobStore"
    		}
		}

2. Lancez **Azure PowerShell** et exécutez la commande suivante pour passer au mode **AzureResourceManager**. Les cmdlets Azure Data Factory sont disponibles dans le mode **AzureResourceManager**.

         switch-azuremode AzureResourceManager
		

3. Accédez au dossier **C:\ADFGetstarted\Custom**.
4. Exécutez la commande suivante pour créer le service lié pour le cluster HDInsight à la demande.
 
		New-AzureDataFactoryLinkedService -ResourceGroupName ADFTutorialResourceGroup -DataFactoryName ADFTutorialDataFactory -File .\HDInsightOnDemandCluster.json
  
   
#### Pour utiliser votre propre cluster HDInsight : 

1. Créez un fichier JSON nommé **MyHDInsightCluster.json** avec le contenu suivant et enregistrez-le dans le dossier **C:\ADFGetStarted\Custom**. Avant d'enregistrer le fichier JSON, remplacez clustername, username et password par les valeurs appropriées.  

		{
   			"Name": "MyHDInsightCluster",
    		"Properties": 
			{
        		"Type": "HDInsightBYOCLinkedService",
	        	"ClusterUri": "https://<clustername>.azurehdinsight.net/",
    	    	"UserName": "<username>",
    	    	"Password": "<password>",
    	    	"LinkedServiceName": "MyBlobStore"
    		}
		}

2. Lancez **Azure PowerShell** et exécutez la commande suivante pour passer au mode **AzureResourceManager**. Les cmdlets Azure Data Factory sont disponibles dans le mode **AzureResourceManager**.

         switch-azuremode AzureResourceManager
		

3. Accédez au dossier **C:\ADFGetstarted\Custom**.
4. Exécutez la commande suivante pour créer le service lié pour le cluster HDInsight à la demande.
 
		New-AzureDataFactoryLinkedService -ResourceGroupName ADFTutorialResourceGroup -DataFactoryName ADFTutorialDataFactory -File .\MyHDInsightCluster.json



### Étape 2 : utilisation de l'activité personnalisée dans un pipeline
Étendons le didacticiel [Prise en main d'Azure Data Factory][adfgetstarted] pour créer un autre pipeline afin de tester cette activité personnalisée.

#### Création d'un service lié pour le cluster HDInsight à utiliser pour l'exécution de l'activité personnalisée


1.	Créez un fichier JSON pour le pipeline, comme illustré dans l'exemple suivant, et enregistrez-le sous **ADFTutorialPipelineCustom.json** dans le dossier **C:\ADFGetStarted\Custom**. Remplacez le nom **LinkedServiceName** par le nom du cluster HDInsight (**HDInsightOnDemandCluster** ou **MyHDInsightCluster**)


		{
    		"name": "ADFTutorialPipelineCustom",
    		"properties":
    		{
        		"description" : "Use custom activity",
        		"activities":
        		[
					{
                		"Name": "MyCustomActivity",
                     	"Type": "CustomActivity",
                     	"Inputs": [{"Name": "EmpTableFromBlob"}],
                     	"Outputs": [{"Name": "OutputTableForCustom"}],
						"LinkedServiceName": "MyHDInsightCluster",
                     	"Transformation":
                     	{
                        	"AssemblyName": "MyCustomActivity.dll",
                            "EntryPoint": "MyCustomActivityNS.MyCustomActivity",
                            "PackageLinkedService": "MyBlobStore",
                            "PackageFile": "customactivitycontainer/MyCustomActivity.zip",
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
				]
			}
		}

	Notez les points suivants : 

	- Il existe une activité dans la section des activités, et elle est de type : **CustomActivity**.
	- Utilisez la table d'entrée **EmpTableFromBlob** que vous avez utilisée dans le didacticiel de prise en main.
	- Utilisez une nouvelle table de sortie **OutputTableForCustom** que vous allez créer à l'étape suivante.
	- **AssemblyName** est défini sur le nom de la DLL : **MyActivities.dll**.
	- **EntryPoint** est défini sur **MyCustomActivityNS.MyCustomActivity**.
	- **PackageLinkedService** est défini sur **MyBlobStore**, qui a été créé dans le cadre du didacticiel [Prise en main d'Azure Data Factory][adfgetstarted]. Ce magasin d'objets blob contient le fichier .zip d'activités personnalisées.
	- **PackageFile** est défini sur **customactivitycontainer/MyCustomActivity.zip**.
     

4. Créez un fichier JSON pour la table de sortie (**OutputTableForCustom** à laquelle se réfère le fichier JSON de pipeline) et enregistrez-le sous C:\ADFGetStarted\Custom\OutputTableForCustom.json.

		

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

 	L'emplacement de sortie est **adftutorial/customactivityoutput/AAAAMMJJHH/** où AAAAMMJJHH est l'année, le mois, la date et l'heure de la tranche en cours de production. Pour plus d'informations, consultez la page [Référence du développeur][adf-developer-reference]. 

5. Exécutez la commande suivante pour **créer la table de sortie** dans **ADFTutorialDataFactory**.
		
		

		New-AzureDataFactoryTable  -DataFactoryName ADFTutorialDataFactory -File C:\ADFGetStarted\Custom\OutputTableForCustom.json -ResourceGroupName ADFTutorialResourceGroup


6. Exécutez la commande suivante pour **créer le pipeline**. Vous avez créé le fichier JSON de pipeline dans une étape précédente.

		New-AzureDataFactoryPipeline  -DataFactoryName ADFTutorialDataFactory -File C:\ADFGetStarted\Custom\ADFTutorialPipelineCustom.json -ResourceGroupName ADFTutorialResourceGroup



7. Exécutez la commande PowerShell suivante pour **définir la période active** sur le pipeline que vous avez créé.

		Set-AzureDataFactoryPipelineActivePeriod -ResourceGroupName ADFTutorialResourceGroup -DataFactoryName ADFTutorialDataFactory -StartDateTime 2014-09-29 -EndDateTime 2014-09-30 -Name ADFTutorialPipelineCustom

	> [WACOM.NOTE] Remplacez la valeur **StartDateTime** par le jour en cours et la valeur **EndDateTime** par le jour suivant. Les valeurs StartDateTime et EndDateTime sont exprimées en heure UTC et doivent être au [format ISO](http://en.wikipedia.org/wiki/ISO_8601). Par exemple : 2014-10-14T16:32:41Z. **EndDateTime** est facultatif, mais nous allons l'utiliser dans ce didacticiel. 
	> Si vous ne spécifiez pas la valeur **EndDateTime**, elle est calculée comme suit : **StartDateTime + 48 heures**. Pour exécuter le pipeline indéfiniment, spécifiez **9/9/9999** comme **EndDateTime**.



8. Vérifiez que les fichiers de sortie sont générés dans le stockage d'objets blob dans le conteneur **adftutorial**.

	![output from custom activity][image-data-factory-output-from-custom-activity]

9. Si vous ouvrez le fichier de sortie, la sortie doit avoir l'aspect suivant :
	
	adftutorial/input,2,WORKERNODE0,10/07/2014 18:34:33 

	(emplacement de l'objet blob), (nombre de lignes dans l'objet blob), (nœud sur lequel l'activité a été exécutée), (horodatage)

10.	Utilisez le [portail Azure][azure-preview-portal] ou les cmdlets Azure PowerShell pour surveiller votre fabrique de données, les pipelines et les jeux de données. Vous pouvez voir les messages depuis **ActivityLogger** dans le code de l'activité personnalisée dans les journaux que vous pouvez télécharger depuis le portail ou à l'aide des cmdlets.

	![download logs from custom activity][image-data-factory-download-logs-from-custom-activity]
   
Pour connaître les étapes de la surveillance des jeux de données et des pipelines, consultez la page [Prise en main d'Azure Data Factory][adfgetstarted].      
    
## Voir aussi

Article | Description
------ | ---------------
[Activation de vos pipelines pour les utiliser avec des données locales][use-onpremises-datasources] | Cet article contient une procédure pas à pas permettant de copier les données d'une base de données SQL Server locale vers un objet blob Azure.
[Utilisation de Pig et Hive avec Data Factory][use-pig-and-hive-with-data-factory] | Cet article contient une procédure pas à pas permettant d'exécuter un script Hive/Pig à l'aide de HDInsight Activity pour traiter les données d'entrée afin de produire des données de sortie. 
[Didacticiel : Déplacement et traitement des fichiers journaux à l'aide de Data Factory][adf-tutorial] | Cet article contient une procédure pas à pas permettant d'implémenter un scénario proche du monde réel à l'aide d'Azure Data Factory pour transformer les données des fichiers journaux en données détaillées.
[Utilisation des activités personnalisées de Data Factory][use-custom-activities] | Cet article contient une procédure pas à pas permettant de créer une activité personnalisée et de l'utiliser dans un pipeline. 
[Surveillance et gestion d'Azure Data Factory à l'aide de PowerShell][monitor-manage-using-powershell] | Cet article décrit comment surveiller une fabrique de données Azure à l'aide de cmdlets Azure PowerShell. Vous pouvez essayer les exemples de l'article portant sur ADFTutorialDataFactory.
[Résolution des problèmes liés à Data Factory][troubleshoot] | Cet article explique comment résoudre des problèmes liés à Azure Data Factory. Vous pouvez essayer la procédure pas à pas de cet article portant sur ADFTutorialDataFactory en introduisant une erreur (en supprimant la table dans la base de données SQL Azure). 
[Référence du développeur Azure Data Factory][developer-reference] | Ces informations de référence du développeur comportent des informations complètes sur les cmdlets, le script JSON, les fonctions, etc. 


[monitor-manage-using-powershell]: ../data-factory-monitor-manage-using-powershell
[use-onpremises-datasources]: ../data-factory-use-onpremises-datasources
[adf-tutorial]: ../data-factory-tutorial
[use-custom-activities]: ../data-factory-use-custom-activities
[use-pig-and-hive-with-data-factory]: ../data-factory-pig-hive-activities
[troubleshoot]: ../data-factory-troubleshoot
[data-factory-introduction]: ../data-factory-introduction


[developer-reference]: http://go.microsoft.com/fwlink/?LinkId=516908
[cmdlet-reference]: http://go.microsoft.com/fwlink/?LinkId=517456




[nuget-package]: http://go.microsoft.com/fwlink/?LinkId=517478
[azure-developer-center]: http://azure.microsoft.com/fr-fr/develop/net/
[adf-developer-reference]: http://go.microsoft.com/fwlink/?LinkId=516908
[azure-preview-portal]: https://portal.azure.com/

[adfgetstarted]: ../data-factory-get-started
[hivewalkthrough]: ../data-factory-pig-hive-activities

[image-data-factory-zip-output-binaries]: ./media/data-factory-use-custom-activities/ZipOuputBinaries.png

[image-data-factory-upload-zip-to-blob]: ./media/data-factory-use-custom-activities/UploadZipToBlob.png

[image-data-factory-ouput-from-custom-activity]: ./media/data-factory-use-custom-activities/OutputFilesFromCustomActivity.png

[image-data-factory-download-logs-from-custom-activity]: ./media/data-factory-use-custom-activities/DownloadLogsFromCustomActivity.png
