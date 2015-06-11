<properties 
	pageTitle="Résolution des problèmes liés à Azure Data Factory" 
	description="Découvrez comment résoudre les problèmes liés à l&#39;utilisation de Azure Data Factory." 
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
	ms.date="04/14/2015" 
	ms.author="spelluru"/>

# Résolution des problèmes liés à Data Factory
Vous pouvez résoudre les problèmes Azure Data Factory à l'aide du portail Azure ou des cmdlets PowerShell. Cette rubrique contient des procédures pas à pas qui vous montrent comment utiliser le portail Azure pour résoudre rapidement les erreurs Data Factory.

## Problème : Pas en mesure d'exécuter des applets de commande de fabrique de données
Pour résoudre ce problème, changez de mode d'Azure pour **AzureResourceManager**:

Lancez **Azure PowerShell** et exécutez la commande suivante pour basculer vers le **AzureResourceManager** mode. Les applets de commande Azure Data Factory sont disponibles dans le **AzureResourceManager** mode.

         switch-azuremode AzureResourceManager

## Problème : Non autorisé erreur lors de l'exécution d'une applet de commande Data Factory
Vous utilisez probablement pas le compte Azure droit ou l'abonnement avec Azure PowerShell. Utilisez les applets de commande suivantes pour sélectionner le compte Azure droite et abonnement à utiliser avec Azure PowerShell.

1. Add-AzureAccount - utilisation de l'ID d'utilisateur et mot de passe
2. Get-AzureSubscription - afficher tous les abonnements pour le compte. 
3. SELECT-AzureSubscription <subscription name> -Sélectionnez l'abonnement. Utiliser le même que celui que vous utilisez pour créer une fabrique de données sur le portail Azure Preview.

## Problème : Ne parviennent pas à lancer le programme d'installation de données passerelle Express à partir du portail Azure
Le programme d'installation Express pour la passerelle de données nécessite Internet Explorer ou un navigateur compatible Microsoft ClickOnce. Si vous ne peut pas démarrer le programme d'installation Express, vous pouvez :

1. Basculez vers Internet Explorer si vous échoue avec d'autres navigateurs. Ou
2. Utilisez les liens « Installation manuelle » indiqués sur le même volet dans le portail pour effectuer l'installation, puis copier la clé est fournie à l'écran et collez lors de la configuration de la passerelle de gestion des données est prête. Si elle ne démarre pas, vérifiez votre menu Démarrer pour « Passerelle de gestion de données Microsoft » et collez la clé lorsqu'il se lance. 


## Problème : Erreur de lancement du Gestionnaire d'informations d'identification à partir du portail Azure
Lors du paramétrage ou mise à jour d'un Service SQL Server lié via le portail Azure, l'application du Gestionnaire d'informations d'identification est lancé pour garantir la sécurité. Elle nécessite Internet Explorer ou un navigateur compatible Microsoft ClickOnce. Vous pouvez basculer vers Internet Explorer si vous échoue avec d'autres navigateurs.

## Problème : Ne parviennent pas à se connecter à local SQL Server 
Vérifiez que SQL Server est accessible à partir de l'ordinateur sur lequel la passerelle est installée. Sur l'ordinateur sur lequel la passerelle est installée, vous pouvez

1. Une commande ping sur l'ordinateur où est installé le serveur SQL Server. Ou
2. Essayez de vous connecter à l'instance de SQL Server en utilisant les informations d'identification que vous avez spécifié sur le portail Azure à l'aide de SQL Server Management Studio (SSMS).


## Problème : Entrée tranches sont PendingExecution ou en attente de validation par jamais

Les tranches peut se trouver dans **PendingExecution** ou **en attente de validation** état en raison d'un nombre de raisons et une des raisons courantes est que le **waitOnExternal** propriété n'est pas spécifiée dans le **disponibilité** section de la première table/groupe de données dans le pipeline. Un dataset qui est généré à l'extérieur de la portée de la fabrique de données Azure doit être marqué avec **waitOnExternal** propriété sous **disponibilité** section. Cela indique que les données sont externes et pas sauvegardés par les pipelines au sein de la fabrique de données. Les tranches de données sont marquées comme **prêt** une fois que les données sont disponibles dans le magasin respectif.

Consultez l'exemple suivant pour l'utilisation de la **waitOnExternal** propriété. Vous pouvez spécifier **{} waitOnExternal** sans définir des valeurs pour les propriétés dans la section afin que les valeurs par défaut sont utilisées.

Consultez la rubrique de Tables dans [référence de script JSON][json-scripting-reference] pour plus d'informations sur cette propriété.
	
	{
	    "name": "CustomerTable",
	    "properties":
	    {
	        "location":
	        {
	            "type": "AzureBlobLocation",
	            "folderPath": "MyContainer/MySubFolder/",
	            "linkedServiceName": "MyLinkedService",
	            "format":
	            {
	                "type": "TextFormat",
	                "columnDelimiter": ",",
	                "rowDelimiter": ";"
	            }
	        },
	        "availability":
	        {
	            "frequency": "Hour",
	            "interval": 1,
	            "waitOnExternal":
	            {
	                "dataDelay": "00:10:00",
	                "retryInterval": "00:01:00",
	                "retryTimeout": "00:10:00",
	                "maximumRetry": 3
	            }
	        }
	    }
	}

 Pour résoudre l'erreur, ajoutez la **waitOnExternal** section à la définition de JSON de la table d'entrée et recréez la table.

## Problème : Hybride copie échoue
Pour en savoir plus :

1. Lancez le Gestionnaire de Configuration de passerelle de gestion des données sur l'ordinateur sur lequel la passerelle a été installé. Vérifiez que le **nom de la passerelle** est défini sur le nom de la passerelle logique dans le **Azure Portal**, **état clé de passerelle** est **inscrit** et **état du Service** est **démarré**. 
2. Lancez **l'Observateur d'événements**. Développez **journaux des Applications et Services** sur **passerelle de gestion des données**. Recherchez les erreurs liées à la passerelle de gestion des données. 

## Problème : Sur la demande HDInsight configuration échoue avec l'erreur

Lorsque vous utilisez un service de type HDInsightOnDemandLinkedService lié, vous devez spécifier un linkedServiceName qui pointe vers le stockage d'objets Blob Azure. Ce compte de stockage permet de copier tous les journaux et les fichiers de prise en charge pour votre cluster HDInsight de la demande. Parfois, l'activité qui effectue la mise en service à la demande sur HDInsight peut échouer avec l'erreur suivante :

		Failed to create cluster. Exception: Unable to complete the cluster create operation. Operation failed with code '400'. Cluster left behind state: 'Error'. Message: 'StorageAccountNotColocated'.

Cette erreur indique généralement que l'emplacement du compte de stockage spécifié dans le linkedServiceName n'est pas dans le même emplacement de centre de données comme où l'approvisionnement de HDInsight qui se passe. Par exemple, si l'emplacement de la fabrique de données Azure est ouest des États-Unis et l'approvisionnement de HDInsight à la demande se produit dans ouest des États-Unis, mais l'emplacement du compte de stockage blob Azure a la valeur est des États-Unis, l'approvisionnement à la demande échoue.

En outre, voici une deuxième additionalLinkedServiceNames de propriété JSON où les comptes de stockage supplémentaires peuvent être spécifiés dans HDInsight à la demande. Ces comptes de stockage supplémentaires doivent être dans le même emplacement que le cluster HDInsight ou il échouera avec la même erreur.



## Problème : Activité personnalisée échoue
Lorsque vous utilisez une activité personnalisée en usine de données Azure (type d'activité pipeline CustomActivity), l'application personnalisée s'exécute dans le service lié spécifié à HDInsight comme une carte uniquement la tâche MapReduce de diffusion en continu.

Lorsque l'activité personnalisée s'exécute, Azure Data Factory sera en mesure de capturer cette sortie à partir du cluster HDInsight et enregistrez-le dans le *adfjobs* conteneur de stockage dans votre compte de stockage d'objets Blob Azure. En cas d'erreur, vous pouvez lire le texte à partir de **stderr** fichier texte de sortie après une défaillance s'est produite. Les fichiers sont accessibles et lisibles à partir du portail Azure lui-même dans le navigateur web, ou pour accéder aux fichiers conservés dans le conteneur de stockage dans le stockage Blob Azure directement à l'aide des outils de l'Explorateur de stockage.

Pour énumérer et lire les journaux d'une activité personnalisée particulière, vous pouvez suivre parmi les procédures pas à pas détaillé plus loin sur cette page. En résumé :

1.  Dans le portail Azure **Parcourir** à trouver votre fabrique de données.
2.  Utilisez le **diagramme** bouton pour afficher le diagramme de fabrique de données, puis cliquez sur le **Dataset** tableau ci-dessous le spécifique **Pipeline** qui a l'activité personnalisée. 
3.  Dans le **Table** lame, cliquez sur la tranche d'intérêt dans le **tranches problème** pour l'intervalle de temps à examiner.
4.  Les détails **tranche de données** volet s'affiche et elle peut lister plusieurs **activité s'exécute** du secteur. Cliquez sur une **activité** dans la liste. 
5.  Le **Détails de l'exécution activité** volet s'affiche. Il répertorie le **Message d'erreur** au milieu de la lame et plusieurs **des fichiers journaux** répertoriées au bas de la lame affilié à cette activité à exécuter.
	- Système/journaux-0.log
	- État
	- État/de sortie
	- État/stderr
	- État/stdout

6. Cliquez sur la première **fichier journal** élément dans la liste et que le journal s'ouvre dans un nouveau volet avec le texte intégral, vous pouvez lire. Lisez le texte de chaque journal en cliquant sur chacun d'eux. Le volet de visionneuse de texte s'ouvre. Vous pouvez cliquer sur le **télécharger** bouton pour télécharger le fichier texte facultatif hors connexion.

Un **erreur courante** à partir d'une activité personnalisée est l'exécution du Package a échoué avec le code de sortie « 1 ». Consultez « wasb://adfjobs@storageaccount.blob.core.windows.net/PackageJobs/<guid>/<jobid>/état/stderr » pour plus de détails.

Pour voir plus de détails sur ce type d'erreur, ouvrez le **stderr** fichier. Une erreur courante vue il est une condition de délai d'attente comme cela : INFO mapreduce. Travail : Id de tâche : attempt_1424212573646_0168_m_000000_0, état : Échec de AttemptID:attempt_1424212573646_0168_m_000000_0 expiré après 600 secondes

Ce même message d'erreur peut apparaître plusieurs fois, si le travail a tenté d'exécuter trois fois par exemple, pendant au moins 30 minutes.

Cette erreur de délai d'attente indique un 600 secondes (10 minutes) délai d'expiration s'est produit. Cela signifie généralement que l'application .net personnalisée n'a pas émis toute mise à jour d'état pendant 10 minutes. Si l'application se bloque ou bloqué en attente sur quelque chose de trop de temps, 10 minutes délai d'expiration est un mécanisme de sécurité afin d'éviter d'attendre indéfiniment et retarder votre pipeline de fabrique de données Azure.

Ce délai d'attente est issue de la configuration du cluster HDInsight qui est liée à l'activité personnalisée. Le paramètre est **mapred.task.timeout**, qui par défaut est 600000 millisecondes, comme indiqué dans les paramètres de valeur par défaut d'Apache : http://hadoop.apache.org/docs/r2.4.0/hadoop-mapreduce-client/hadoop-mapreduce-client-core/mapred-default.xml

Vous pouvez remplacer cette valeur par défaut en modifiant les valeurs par défaut au moment de la configuration de votre cluster de configuration de HDInsight. Lorsque vous utilisez Azure Data Factory et **HDInsight à la demande** liés de service, la propriété JSON peut être ajoutée près de vos propriétés HDInsightOnDemandLinkedService JSON. Par exemple, vous pouvez augmenter la valeur 20 minutes à l'aide de cette propriété JSON.
		
		"mapReduceConfiguration" :
		{
			"mapreduce.task.timeout":"1200000"
		}
		

Pour plus de contexte et un exemple complet de JSON à modifier ces mappage de réduire la Configuration des propriétés, consultez Exemple n ° 3 dans la documentation MSDN ici https://msdn.microsoft.com/library/azure/dn893526.aspx

## Problème : PowerShell demande échoue avec une erreur 400 demande incorrecte « Aucun fournisseur de ressources inscrit trouvé... »

À compter du 10 mars 2015, les versions aperçu privé anticipées fabrique de données Azure PowerShell 2014-05-01-aperçu 2014-07-01-aperçu et 2014-08-01-aperçu seront supprimées. Nous vous recommandons d'utiliser la dernière version des applets de commande ADF, qui font désormais partie de l'Azure PowerShell téléchargés, tels que le téléchargement à partir de cette URL http://go.microsoft.com/?linkid=9811175&clcid=0x409

Si vous utilisez les versions supprimées du SDK Azure PowerShell, vous pouvez recevoir les erreurs suivantes :

		HTTP/1.1 400 Bad Request
		Cache-Control: no-cache
		Pragma: no-cache
		Content-Type: application/json; charset=utf-8
		Expires: -1
		x-ms-request-id: e07181e4-e421-46be-8a08-1f71d5e90494
		x-ms-correlation-request-id: e07181e4-e421-46be-8a08-1f71d5e90494
		x-ms-routing-request-id: WESTUS:20150306T234829Z:e07181e4-e421-46be-8a08-1f71d5e90494
		Strict-Transport-Security: max-age=31536000; includeSubDomains
		Date: Fri, 06 Mar 2015 23:48:29 GMT
		Content-Length: 157
		{"error":{"code":"NoRegisteredProviderFound","message":"No registered resource provider found for location 'west US' and API version '2014-05-01-preview'."}}


## <a name="copywalkthrough"></a> Procédure pas à pas : Dépannage d'une erreur à la copie de données
Dans cette procédure pas à pas, vous allez introduire une erreur dans le didacticiel de l'article Prise en main de Data Factory et découvrir comment vous pouvez utiliser le portail Azure pour la résoudre.

### Composants requis
1. Suivre le didacticiel dans le [prise en main Azure Data Factory][adfgetstarted] l'article.
2. Vérifiez que le **ADFTutorialDataFactory** génère des données dans le **emp** table dans la base de données SQL Azure.  
3. Maintenant, supprimez le **emp** table (** drop table emp **) à partir de la base de données SQL Azure. Cette action introduit une erreur.
4. Exécutez la commande suivante le **Azure PowerShell** pour mettre à jour de la période active pour le pipeline afin qu'il essaie d'écrire des données dans le **emp** table, qui n'existe plus.

         
		Set-AzureDataFactoryPipelineActivePeriod -ResourceGroupName ADFTutorialResourceGroup -DataFactoryName ADFTutorialDataFactory -StartDateTime 2014-09-29 –EndDateTime 2014-09-30 –Name ADFTutorialPipeline
	
	> [AZURE.NOTE]Remplacez <b>StartDateTime</b> valeur avec la date du jour et <b>EndDateTime</b> valeur avec le jour suivant.


### Utilisation de la version préliminaire du portail Azure pour résoudre l'erreur

1.	Connexion à [portail Azure Preview][azure-preview-portal]. 
2.	Cliquez sur **ADFTutorialDataFactory** à partir de la **tableau d'accueil**. Si vous ne voyez pas la fabrique de données sur lien du **tableau d'accueil**, cliquez sur **Parcourir** concentrateur et cliquez sur **tout**. Cliquez sur **fabriques de données...** dans les **Parcourir** lame, puis cliquez sur **ADFTutorialDataFactory**.
3.	Notez que vous consultez **avec les erreurs** sur la **Datasets** vignette. Cliquez sur **avec les erreurs**. Vous devez voir **Datasets avec des erreurs** lame.

	![Fabrique de données avec un lien d'erreurs][image-data-factory-troubleshoot-with-error-link]

4. Dans le **Datasets** avec lame d'erreurs, cliquez sur **EmpSQLTable** pour voir les **TABLE** lame.

	![Datasets avec lame d'erreurs][image-data-factory-troubleshoot-datasets-with-errors-blade]

5. Dans le **TABLE** lame, vous devez voir les tranches de problème, par exemple, les secteurs avec une erreur dans le **tranches problème** inférieure. Vous pouvez également voir les tranches récentes avec des erreurs dans le **tranches récentes** liste. Cliquez sur un secteur dans la **tranches problème** liste.

	![Lame de table avec des tranches de problème][image-data-factory-troubleshoot-table-blade-with-problem-slices]

	Si vous cliquez sur **tranches problème** (pas sur un problème spécifique), vous verrez la **tranches de données** lame, puis cliquez sur une **tranche de problème spécifique** pour voir les **tranche de données** diapositive pour la tranche de données sélectionnée.

6. Dans le **tranche de données** lame pour **EmpSQLTable**, vous voyez tous les **activité** du secteur dans la liste en bas. Cliquez sur une **activité exécuter** dans la liste qui a échoué.

	![Lame coupe avec exécutions actives][image-data-factory-troubleshoot-dataslice-blade-with-active-runs]


7. Dans le **Détails de l'exécution activité** lame pour l'activité exécuter vous avez sélectionné, vous devez voir des détails sur l'erreur. Dans ce scénario, vous voyez : **nom d'objet non valide « emp »**.

	![Activité exécuter des détails avec une erreur][image-data-factory-troubleshoot-activity-run-with-error]

Pour résoudre ce problème, créez le **emp** script de table à l'aide de l'instruction SQL à partir de [prise en main Data Factory][adfgetstarted] l'article.


### Utilisation des cmdlets Azure PowerShell pour résoudre l'erreur
1.	Lancez **Azure PowerShell**. 
2.	Basculez vers **AzureResourceManager** mode comme les applets de commande de fabrique de données sont uniquement disponibles dans ce mode.

         
		switch-azuremode AzureResourceManager

3. Exécutez la commande Get-AzureDataFactorySlice pour voir les tranches et leur état. Vous devez voir une tranche avec l'état : échec.

         
		Get-AzureDataFactorySlice -ResourceGroupName ADFTutorialResourceGroup -DataFactoryName ADFTutorialDataFactory -TableName EmpSQLTable -StartDateTime 2014-10-15

	> [AZURE.NOTE]Remplacez **StartDateTime** avec la StartDateTime valeur spécifiée pour le **Set-AzureDataFactoryPipelineActivePeriod**.

		ResourceGroupName 		: ADFTutorialResourceGroup
		DataFactoryName   		: ADFTutorialDataFactory
		TableName         		: EmpSQLTable
		Start             		: 10/15/2014 4:00:00 PM
		End               		: 10/15/2014 5:00:00 PM
		RetryCount        		: 0
		Status            		: Failed
		LatencyStatus     		:
		LongRetryCount    		: 0

	Remarque la **Démarrer** fois pour la tranche de problème (la tranche avec **état** la valeur **échec**) dans la sortie. 
4. Exécutez maintenant le **Get-AzureDataFactoryRun** pour obtenir des détails sur l'activité exécutée pour la tranche.
         
		Get-AzureDataFactoryRun -ResourceGroupName ADFTutorialResourceGroup -DataFactoryName ADFTutorialDataFactory -TableName EmpSQLTable -StartDateTime "10/15/2014 4:00:00 PM"

	La valeur de **StartDateTime** est l'heure de début de la tranche d'erreur/problème que vous avez noté à l'étape précédente. La valeur date-heure doit être entourée de guillemets doubles.
5. Vous devez voir la sortie avec les détails sur l'erreur (semblable à cela) :

		Id                  	: 2b19475a-c546-473f-8da1-95a9df2357bc
		ResourceGroupName   	: ADFTutorialResourceGroup
		DataFactoryName     	: ADFTutorialDataFactory
		TableName           	: EmpSQLTable
		ResumptionToken    		:
		ContinuationToken   	:
		ProcessingStartTime 	: 10/15/2014 11:13:39 PM
		ProcessingEndTime  	 	: 10/15/2014 11:16:59 PM
		PercentComplete     	: 0
		DataSliceStart     		: 10/15/2014 4:00:00 PM
		DataSliceEnd       		: 10/15/2014 5:00:00 PM
		Status              	: FailedExecution
		Timestamp           	: 10/15/2014 11:13:39 PM
		RetryAttempt       		: 0
		Properties          	: {}
		ErrorMessage        	: Unknown error in CopyActivity: System.Data.SqlClient.SqlException (0x80131904): **Invalid object name 'emp'.**
                         at System.Data.SqlClient.SqlConnection.OnError(SqlException exception, Boolean
                      breakConnection, Action`1 wrapCloseInAction)
                         at System.Data.SqlClient.TdsParser.ThrowExceptionAndWarning(TdsParserStateObject stateObj,

 

## <a name="pighivewalkthrough"></a> Procédure pas à pas : Dépannage d'une erreur avec le traitement de la ruche/Pig
Cette procédure pas à pas fournit les instructions pour résoudre une erreur avec un traitement Hive/Pig à l'aide de la version préliminaire du portail Azure et d'Azure PowerShell.


### Procédure pas à pas : Utilisation Azure Portal pour résoudre une erreur avec le traitement Pig/Hive
Dans ce scénario, le jeu de données est associé à un état d'erreur dû à une défaillance dans le traitement Hive sur un cluster HDInsight.

1. Cliquez sur **avec les erreurs** sur **Datasets** vignette sur la **DATA FACTORY** page d'accueil.

	![Avec un lien d'erreurs dans la vignette de Datasets][image-data-factory-troubleshoot-walkthrough2-with-errors-link]

2. Dans le **Datasets avec des erreurs** lame, cliquez sur le **table** qui vous intéressez.

	![Datasets avec lame d'erreurs][image-data-factory-troubleshoot-walkthrough2-datasets-with-errors]

3. Dans le **TABLE** lame, cliquez sur le **tranche problème** avec **état** la valeur **échec**.

	![Table avec des tranches de problème][image-data-factory-troubleshoot-walkthrough2-table-with-problem-slices]

4. Dans le **tranche de données** lame, cliquez sur le **activité exécutée** qui a échoué.

	![Tranche de données avec les séries ayant échoué][image-data-factory-troubleshoot-walkthrough2-slice-activity-runs]

5. Dans le **Détails de l'exécution activité** lames, vous pouvez télécharger les fichiers associés avec le traitement de HDInsight. Cliquez sur **télécharger** pour **état/stderr** pour télécharger le fichier journal des erreurs qui contient des détails sur l'erreur.

	![Activité exécuter des détails avec le lien de téléchargement][image-data-factory-troubleshoot-activity-run-details]

    
### Procédure : Utiliser Azure PowerShell pour résoudre une erreur avec le traitement Pig/Hive
1.	Lancez **Azure PowerShell**. 
2.	Basculez vers **AzureResourceManager** mode comme les applets de commande de fabrique de données sont uniquement disponibles dans ce mode.

         
		switch-azuremode AzureResourceManager

3. Exécutez la commande Get-AzureDataFactorySlice pour voir les tranches et leur état. Vous devez voir une tranche avec l'état : échec.

         
		Get-AzureDataFactorySlice -ResourceGroupName ADF -DataFactoryName LogProcessingFactory -TableName EnrichedGameEventsTable -StartDateTime 2014-05-04 20:00:00

	> [AZURE.NOTE]Remplacez **StartDateTime** avec la StartDateTime valeur spécifiée pour le **Set-AzureDataFactoryPipelineActivePeriod**.

		ResourceGroupName : ADF
		DataFactoryName   : LogProcessingFactory
		TableName         : EnrichedGameEventsTable
		Start             : 5/5/2014 12:00:00 AM
		End               : 5/6/2014 12:00:00 AM
		RetryCount        : 0
		Status            : Failed
		LatencyStatus     :
		LongRetryCount    : 0


	Remarque la **Démarrer** fois pour la tranche de problème (la tranche avec **état** la valeur **échec**) dans la sortie. 
4. Exécutez maintenant le **Get-AzureDataFactoryRun** pour obtenir des détails sur l'activité exécutée pour la tranche.
         
		Get-AzureDataFactoryRun -ResourceGroupName ADF -DataFactoryName LogProcessingFactory -TableName EnrichedGameEventsTable -StartDateTime "5/5/2014 12:00:00 AM"

	La valeur de **StartDateTime** est l'heure de début de la tranche d'erreur/problème que vous avez noté à l'étape précédente. La valeur date-heure doit être entourée de guillemets doubles.
5. Vous devez voir la sortie avec les détails sur l'erreur (semblable à cela) :

		Id                  : 841b77c9-d56c-48d1-99a3-8c16c3e77d39
		ResourceGroupName   : ADF
		DataFactoryName     : LogProcessingFactory3
		TableName           : EnrichedGameEventsTable
		ProcessingStartTime : 10/10/2014 3:04:52 AM
		ProcessingEndTime   : 10/10/2014 3:06:49 AM
		PercentComplete     : 0
		DataSliceStart      : 5/5/2014 12:00:00 AM
		DataSliceEnd        : 5/6/2014 12:00:00 AM
		Status              : FailedExecution
		Timestamp           : 10/10/2014 3:04:52 AM
		RetryAttempt        : 0
		Properties          : {}
		ErrorMessage        : Pig script failed with exit code '5'. See 'wasb://adfjobs@spestore.blob.core.windows.net/PigQuery
								Jobs/841b77c9-d56c-48d1-99a3-8c16c3e77d39/10_10_2014_03_04_53_277/Status/stderr' for more details.
		ActivityName        : PigEnrichLogs
		PipelineName        : EnrichGameLogsPipeline
		Type                :

6. Vous pouvez exécuter **AzureDataFactoryLog d'enregistrement** applet de commande avec la valeur d'Id vous voir dans la sortie ci-dessus et téléchargez le journal des fichiers à l'aide de la **- DownloadLogs** option pour l'applet de commande.



[adfgetstarted]: data-factory-get-started.md
[use-onpremises-datasources]: data-factory-use-onpremises-datasources.md
[use-pig-and-hive-with-data-factory]: data-factory-pig-hive-activities.md
[adf-tutorial]: data-factory-tutorial.md
[use-custom-activities]: data-factory-use-custom-activities.md
[monitor-manage-using-powershell]: data-factory-monitor-manage-using-powershell.md
[troubleshoot]: data-factory-troubleshoot.md
[developer-reference]: http://go.microsoft.com/fwlink/?LinkId=516908
[cmdlet-reference]: http://go.microsoft.com/fwlink/?LinkId=517456
[json-scripting-reference]: http://go.microsoft.com/fwlink/?LinkId=516971

[azure-preview-portal]: https://portal.azure.com/

[image-data-factory-troubleshoot-with-error-link]: ./media/data-factory-troubleshoot/DataFactoryWithErrorLink.png

[image-data-factory-troubleshoot-datasets-with-errors-blade]: ./media/data-factory-troubleshoot/DatasetsWithErrorsBlade.png

[image-data-factory-troubleshoot-table-blade-with-problem-slices]: ./media/data-factory-troubleshoot/TableBladeWithProblemSlices.png

[image-data-factory-troubleshoot-activity-run-with-error]: ./media/data-factory-troubleshoot/ActivityRunDetailsWithError.png

[image-data-factory-troubleshoot-dataslice-blade-with-active-runs]: ./media/data-factory-troubleshoot/DataSliceBladeWithActivityRuns.png

[image-data-factory-troubleshoot-walkthrough2-with-errors-link]: ./media/data-factory-troubleshoot/Walkthrough2WithErrorsLink.png

[image-data-factory-troubleshoot-walkthrough2-datasets-with-errors]: ./media/data-factory-troubleshoot/Walkthrough2DataSetsWithErrors.png

[image-data-factory-troubleshoot-walkthrough2-table-with-problem-slices]: ./media/data-factory-troubleshoot/Walkthrough2TableProblemSlices.png

[image-data-factory-troubleshoot-walkthrough2-slice-activity-runs]: ./media/data-factory-troubleshoot/Walkthrough2DataSliceActivityRuns.png

[image-data-factory-troubleshoot-activity-run-details]: ./media/data-factory-troubleshoot/Walkthrough2ActivityRunDetails.png

<!---HONumber=GIT-SubDir--> 