<properties 
	pageTitle="Résolution des problèmes liés à Azure Data Factory" 
	description="Découvrez comment résoudre les problèmes liés à l'utilisation de Azure Data Factory." 
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
	ms.date="06/04/2015" 
	ms.author="spelluru"/>

# Résolution des problèmes liés à Data Factory
Vous pouvez résoudre les problèmes Azure Data Factory à l'aide du portail Azure ou des cmdlets PowerShell. Cette rubrique contient des procédures pas à pas qui vous montrent comment utiliser le portail Azure pour résoudre rapidement les erreurs Data Factory.

## Problème : impossible d’exécuter des applets de commande Data Factory
Pour résoudre ce problème, passez au mode Azure **AzureResourceManager** :

Lancez **Azure PowerShell** et exécutez la commande suivante pour basculer vers le mode **AzureResourceManager**. Les applets de commande Azure Data Factory sont disponibles en mode **AzureResourceManager**.

         switch-azuremode AzureResourceManager

## Problème : erreur non autorisée lors de l’exécution d’une applet de commande Data Factory
Vous n’utilisez probablement pas le compte ou l’abonnement Azure correct pour Azure PowerShell. Utilisez les applets de commande suivantes pour sélectionner le compte et l’abonnement Azure corrects à utiliser avec Azure PowerShell.

1. Add-AzureAccount - Utilisez l’ID d’utilisateur et le mot de passe corrects.
2. Get-AzureSubscription - Affichez tous les abonnements pour le compte. 
3. Select-AzureSubscription <subscription name> - Sélectionnez l’abonnement correct. Utilisez le même que celui que vous utilisez pour créer une fabrique de données sur le portail Azure en version préliminaire.

## Problème : échec du lancement de l’installation rapide de la passerelle de données depuis le portail Azure
L’installation rapide de la passerelle de données nécessite Internet Explorer ou un navigateur web compatible avec Microsoft ClickOnce. Si vous ne pouvez pas démarrer le programme d’installation rapide, vous pouvez :

1. basculer vers Internet Explorer si le processus échoue avec d’autres navigateurs ; Ou
2. utiliser les liens « Installation manuelle » figurant dans le même panneau du portail pour effectuer l’installation, puis copier la clé fournie à l’écran et la coller lorsque la configuration de la passerelle de gestion des données est prête. Si elle ne démarre pas, vérifiez le menu de démarrage de la passerelle de gestion des données Microsoft et collez la clé lors du lancement. 


## Problème : échec du lancement du Gestionnaire des informations d’identification depuis le portail Azure
Lors du paramétrage ou de la mise à jour d’un service lié SQL Server par le biais du portail Azure, l’application Gestionnaire des informations d’identification est lancée pour garantir la sécurité. Elle nécessite Internet Explorer ou un navigateur web compatible Microsoft ClickOnce. Vous pouvez basculer vers Internet Explorer si le processus échoue avec d’autres navigateurs.

## Problème : échec de la connexion à un serveur SQL local 
Vérifiez que SQL Server est accessible à partir de l'ordinateur sur lequel la passerelle est installée. Sur cet ordinateur, vous pouvez :

1. exécuter une commande Ping sur l’ordinateur sur lequel est installé le serveur SQL ; Ou
2. essayer de vous connecter à l’instance SQL Server en utilisant les informations d’identification que vous avez spécifiées sur le portail Azure à l’aide de SQL Server Management Studio (SSMS).


## Problème : l’état des tranches d’entrée est PendingExecution ou PendingValidation depuis longtemps.

Les tranches peuvent avoir l’état **PendingExecution** ou **PendingValidation** pour plusieurs raisons, par exemple parce que la propriété **waitOnExternal** n’est pas spécifiée dans la section **availability** de la première table ou du premier jeu de données dans le pipeline. Tout jeu de données généré en dehors de la portée d’Azure Data Factory doit être marqué avec la propriété **waitOnExternal** dans la section **availability**. Cela indique que les données sont externes et qu’elles ne sont sauvegardées par aucun pipeline au sein de la fabrique de données. Les tranches de données sont marquées comme prêtes (**Ready**) une fois que les données sont disponibles dans le magasin respectif.

Consultez l’exemple suivant pour l’utilisation de la propriété **waitOnExternal**. Vous pouvez spécifier **waitOnExternal{}** sans définir de valeurs pour les propriétés figurant dans la section afin d’utiliser les valeurs par défaut.

Consultez la section Tables dans [Référence sur la création de scripts JSON][json-scripting-reference] pour plus d’informations sur cette propriété.
	
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

 Pour résoudre l’erreur, ajoutez la section **waitOnExternal** à la définition JSON de la table d’entrée, puis recréez la table.

## Problème : échec de l’opération de copie hybride
Pour en savoir plus :

1. Lancez le Gestionnaire de configuration de la passerelle de gestion des données sur l’ordinateur sur lequel la passerelle est installée. Vérifiez que le **nom de la passerelle** est défini sur le nom de passerelle logique dans le **portail Azure**, que l’**état de la clé de la passerelle** est **inscrite** et que l’**état du service** est **Démarré**. 
2. Lancez l’**Observateur d’événements**. Développez **Journaux des applications et des services**, puis cliquez sur **Passerelle de gestion des données**. Recherchez les erreurs liées à la passerelle de gestion des données. 

## Problème : échec de l’approvisionnement HDInsight à la demande avec erreur

Lorsque vous utilisez un service lié de type HDInsightOnDemandLinkedService, vous devez spécifier un linkedServiceName qui pointe vers le stockage d’objets blob Azure. Ce compte de stockage permet de copier tous les journaux et les fichiers de prise en charge pour votre cluster HDInsight à la demande. Parfois, l’activité qui effectue l’approvisionnement à la demande sur HDInsight peut échouer avec l’erreur suivante :

		Failed to create cluster. Exception: Unable to complete the cluster create operation. Operation failed with code '400'. Cluster left behind state: 'Error'. Message: 'StorageAccountNotColocated'.

Cette erreur indique généralement que l’emplacement du compte de stockage spécifié dans linkedServiceName ne se trouve pas dans le même emplacement de centre de données dans lequel l’approvisionnement de HDInsight est effectué. Par exemple, si l’emplacement d’Azure Data Factory est Ouest des États-Unis et que l’approvisionnement de HDInsight à la demande est effectué dans la région Ouest des États-Unis, alors que l’emplacement du compte de stockage d’objets blob Azure est défini sur Est des États-Unis, l’approvisionnement à la demande échoue.

En outre, il existe une seconde propriété JSON additionalLinkedServiceNames avec laquelle les comptes de stockage supplémentaires peuvent être spécifiés dans HDInsight à la demande. Ces comptes de stockage supplémentaires liés doivent avoir le même emplacement que le cluster HDInsight, ou l’approvisionnement échouera avec la même erreur.



## Problème : échec de l’activité personnalisée
Lorsque vous utilisez une activité personnalisée dans Azure Data Factory (activité pipeline de type CustomActivity), l’application personnalisée s’exécute dans le service lié spécifié pour HDInsight en tant que tâche MapReduce de diffusion en continu Map uniquement.

Lorsque l’activité personnalisée est exécutée, Azure Data Factory est en mesure de capturer cette sortie à partir du cluster HDInsight et de l’enregistrer dans le conteneur de stockage *adfjobs* dans votre compte de stockage d’objets blob Azure. En cas d’erreur, vous pouvez lire le texte dans le fichier texte de sortie **stderr** après une défaillance. Les fichiers sont accessibles et lisibles depuis le portail Azure lui-même dans le navigateur web, ou grâce aux outils d’exploration du stockage qui permettent d’accéder aux fichiers sauvegardés dans le conteneur de stockage directement dans le stockage d’objets blob Azure.

Pour énumérer et lire les journaux d’une activité personnalisée spécifique, vous pouvez suivre l’une des procédures pas à pas détaillées plus loin sur cette page. En résumé :

1.  Dans le portail Azure, sélectionnez **Parcourir** pour rechercher votre fabrique de données.
2.  Utilisez le bouton **Diagramme** pour afficher le diagramme de fabrique de données, puis cliquez sur la table **Jeu de données** ci-dessous qui suit le **Pipeline** spécifique présentant l’activité personnalisée. 
3.  Dans le panneau **Table**, cliquez sur la tranche qui vous intéresse dans les **Tranches problématiques** pour l’intervalle de temps à examiner.
4.  Le panneau **Tranche de données** détaillé s’affiche et peut lister plusieurs **Exécutions d’activité** pour la tranche. Cliquez sur une **activité** dans la liste. 
5.  Le panneau **Détails sur l’exécution d’activité** s’affiche. Il répertorie le **Message d’erreur** au milieu du panneau et plusieurs des **fichiers journaux** répertoriés au bas du panneau affilié à cette exécution d’activité.
	- Logs/system-0.log
	- Statut
	- Status/exit
	- Status/stderr
	- Status/stdout

6. Cliquez sur le premier élément **Fichier journal** dans la liste pour que le journal s’ouvre dans un nouveau panneau avec le texte intégral à votre disposition pour lecture. Lisez le texte de chaque journal en cliquant sur chacun d’eux. Le panneau de la visionneuse de texte s’ouvre. Vous pouvez cliquer sur le bouton **Télécharger** pour télécharger le fichier texte en vue d’un affichage hors connexion facultatif.

Un **erreur courante** liée à une activité personnalisée est l’échec de l’exécution du package avec le code de sortie « 1 ». Consultez « wasb://adfjobs@storageaccount.blob.core.windows.net/PackageJobs/<guid>/<jobid>/Status/stderr » pour plus de détails.

Pour voir plus de détails sur ce type d’erreur, ouvrez le fichier **stderr**. Une erreur courante est liée à une condition de délai d’expiration telle que celle-ci : INFO mapreduce.Job: Task Id : attempt_1424212573646_0168_m_000000_0, Status : FAILED AttemptID:attempt_1424212573646_0168_m_000000_0 Timed out after 600 secs

Ce même message d’erreur peut apparaître plusieurs fois, si le travail a tenté de s’exécuter 3 fois par exemple, pendant au moins 30 minutes.

Cette erreur de délai d’attente indique qu’un délai d’expiration de 600 secondes (10 minutes) s’est produit. Cela signifie généralement que l’application .Net personnalisée n’a émis aucune mise à jour d’état pendant 10 minutes. Si l’application se bloque ou est en attente de quelque chose depuis trop longtemps, le délai d’expiration de 10 minutes est un mécanisme de sécurité permettant d’éviter qu’elle n’attende indéfiniment et qu’elle ne retarde votre pipeline Azure Data Factory.

Ce délai d’attente est issu de la configuration du cluster HDInsight qui est lié à l’activité personnalisée. Le paramètre est **mapred.task.timeout**, qui correspond par défaut à 600 000 millisecondes, comme indiqué dans les paramètres par défaut d’Apache ici : http://hadoop.apache.org/docs/r2.4.0/hadoop-mapreduce-client/hadoop-mapreduce-client-core/mapred-default.xml

Vous pouvez remplacer cette valeur par défaut en modifiant les valeurs par défaut au moment de l’approvisionnement de votre cluster d’approvisionnement HDInsight. Lorsque vous utilisez Azure Data Factory et le service lié **HDInsight à la demande**, la propriété JSON peut être ajoutée près de vos propriétés JSON HDInsightOnDemandLinkedService. Par exemple, vous pouvez augmenter la valeur à 20 minutes à l’aide de cette propriété JSON.
		
		"mapReduceConfiguration" :
		{
			"mapreduce.task.timeout":"1200000"
		}
		

Pour plus de contexte et un exemple complet de JSON pour la modification de ces propriétés de configuration mapReduce, consultez l'exemple n°3 dans la documentation MSDN ici https://msdn.microsoft.com/library/azure/dn893526.aspx

## Problème : la requête PowerShell échoue avec une erreur de requête incorrecte 400 « No registered resource provider found... »

À compter du 10 mars 2015, les versions préliminaires privées anticipées d'Azure Data Factory PowerShell suivantes seront abandonnées : 2014-05-01-preview, 2014-07-01-preview et 2014-08-01-preview. Nous vous recommandons d’utiliser la dernière version des applets de commande ADF, qui font désormais partie du téléchargement d’Azure PowerShell, tel que le téléchargement à partir de cette URL http://go.microsoft.com/?linkid=9811175&clcid=0x409

Si vous utilisez les versions abandonnées du Kit de développement logiciel (SDK) Azure PowerShell, les erreurs suivantes peuvent s’afficher :

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


## <a name="copywalkthrough"></a>Procédure pas à pas : dépannage d’une erreur de copie des données
Dans cette procédure pas à pas, vous allez introduire une erreur dans le didacticiel de l'article Prise en main de Data Factory et découvrir comment vous pouvez utiliser le portail Azure pour la résoudre.

### Composants requis
1. Suivez le didacticiel de l’article [Prise en main d’Azure Data Factory][adfgetstarted].
2. Confirmez qu'**ADFTutorialDataFactory** produit des données dans la table **emp** de la base de données Azure SQL.  
3. Supprimez la table **emp** (**drop table emp**) de la base de données SQL Azure. Cette action introduit une erreur.
4. Exécutez la commande suivante dans **Azure PowerShell** pour mettre à jour la période active pour le pipeline de sorte qu’il tente d’écrire des données dans la table **emp**, qui n’existe plus.

         
		Set-AzureDataFactoryPipelineActivePeriod -ResourceGroupName ADFTutorialResourceGroup -DataFactoryName ADFTutorialDataFactory -StartDateTime 2014-09-29 –EndDateTime 2014-09-30 –Name ADFTutorialPipeline
	
	Remplacez la valeur de **StartDateTime** par le jour actuel et la valeur de **EndDateTime** par le jour suivant.


### Utilisation de la version préliminaire du portail Azure pour résoudre l'erreur

1.	Connectez-vous au [portail Azure en version préliminaire][azure-preview-portal]. 
2.	Cliquez sur **ADFTutorialDataFactory** depuis le **tableau d’accueil**. Si vous ne voyez pas le lien de la fabrique de données dans le **tableau d’accueil**, cliquez sur le concentrateur **PARCOURIR**, puis sur **Tout**. Cliquez sur **Fabriques de données…** dans le panneau **Parcourir**, puis cliquez sur **ADFTutorialDataFactory**.
3.	Notez que **Avec des erreurs** s’affiche sur la vignette **Jeux de données**. Cliquez sur **Avec des erreurs**. Vous devez voir le panneau **Jeux de données avec erreurs**.

	![Liaison Data Factory avec erreurs][image-data-factory-troubleshoot-with-error-link]

4. Dans le panneau **Jeux de données** avec erreurs, cliquez sur **EmpSQLTable** pour voir le panneau **TABLE**.

	![Panneau Jeux de données avec erreurs][image-data-factory-troubleshoot-datasets-with-errors-blade]

5. Dans le panneau **TABLE**, vous devez voir les tranches problématiques, c’est-à-dire les tranches avec une erreur, dans la liste **Tranches problématiques** en bas. Vous pouvez également voir les tranches récentes contenant des erreurs dans la liste **Tranches récentes**. Cliquez sur une tranche dans la liste **Tranches problématiques**.

	![Panneau de table avec tranches problématiques][image-data-factory-troubleshoot-table-blade-with-problem-slices]

	Cliquez sur **Tranches problématiques** (et non sur un problème spécifique) pour afficher le panneau **TRANCHES DE DONNÉS**, puis cliquez sur une **tranche problématique spécifique** pour afficher le panneau **TRANCHE DE DONNÉES** pour la tranche de données sélectionnée.

6. Dans le panneau **TRANCHE DE DONNÉES** pour **EmpSQLTable**, vous pouvez voir toutes les **exécutions d’activité** pour la tranche de la liste en bas. Cliquez sur une **exécution d’activité** depuis la liste qui a échoué.

	![Panneau Tranche de données avec exécutions actives][image-data-factory-troubleshoot-dataslice-blade-with-active-runs]


7. Dans le panneau **Détails sur l’exécution d’activité** pour l’exécution d’activité que vous avez sélectionnée, vous devez voir les détails de l’erreur. Dans ce scénario, vous voyez : **Invalid object name ’emp’**.

	![Détails sur l’exécution d’activité avec une erreur][image-data-factory-troubleshoot-activity-run-with-error]

Pour résoudre ce problème, créez la table **emp** à l’aide du script SQL de l’article [Prise en main de Data Factory][adfgetstarted].


### Utilisation des cmdlets Azure PowerShell pour résoudre l'erreur
1.	Lancez **Azure PowerShell**. 
2.	Passez en mode **AzureResourceManager**, car les applets de commande Data Factory sont disponibles uniquement dans ce mode.

         
		switch-azuremode AzureResourceManager

3. Exécutez la commande Get-AzureDataFactorySlice pour voir les tranches et leur état. Vous devez voir une tranche avec l’état : Échec (Failed).

         
		Get-AzureDataFactorySlice -ResourceGroupName ADFTutorialResourceGroup -DataFactoryName ADFTutorialDataFactory -TableName EmpSQLTable -StartDateTime 2014-10-15

	Remplacez **StartDateTime** par la valeur de StartDateTime spécifiée pour **Set-AzureDataFactoryPipelineActivePeriod**.

		ResourceGroupName 		: ADFTutorialResourceGroup
		DataFactoryName   		: ADFTutorialDataFactory
		TableName         		: EmpSQLTable
		Start             		: 10/15/2014 4:00:00 PM
		End               		: 10/15/2014 5:00:00 PM
		RetryCount        		: 0
		Status            		: Failed
		LatencyStatus     		:
		LongRetryCount    		: 0

	Notez l’heure de **Début** pour la tranche problématique (celle dont l’**état** est défini sur **Échec**) dans la sortie. 
4. Exécutez maintenant l'applet de commande **Get-AzureDataFactoryRun** pour obtenir des détails sur l'exécution d'activité de la tranche.
         
		Get-AzureDataFactoryRun -ResourceGroupName ADFTutorialResourceGroup -DataFactoryName ADFTutorialDataFactory -TableName EmpSQLTable -StartDateTime "10/15/2014 4:00:00 PM"

	La valeur de **StartDateTime** est l’heure de début de la tranche problématique/l’erreur que vous avez notée à l’étape précédente. La valeur date-heure doit être entourée de guillemets doubles.
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

 

## <a name="pighivewalkthrough"></a> Procédure pas à pas : dépannage d’une erreur de traitement Hive/Pig
Cette procédure pas à pas fournit les instructions pour résoudre une erreur avec un traitement Hive/Pig à l'aide de la version préliminaire du portail Azure et d'Azure PowerShell.


### Procédure pas à pas : utilisation du portail Azure pour résoudre une erreur de traitement Pig/Hive
Dans ce scénario, le jeu de données est associé à un état d'erreur dû à une défaillance dans le traitement Hive sur un cluster HDInsight.

1. Cliquez sur **Avec des erreurs** sur la vignette **Jeux de données** sur la page d’accueil **FABRIQUE DE DONNÉES**.

	![Lien Avec erreurs dans la vignette Jeux de données][image-data-factory-troubleshoot-walkthrough2-with-errors-link]

2. Dans le panneau **Jeux de données avec erreurs**, cliquez sur la **table** qui vous intéresse.

	![Panneau Jeux de données avec erreurs][image-data-factory-troubleshoot-walkthrough2-datasets-with-errors]

3. Dans le panneau **TABLE**, cliquez sur la **tranche problématique** dont l’**ÉTAT** est défini sur **Échec**.

	![Table avec tranches problématiques][image-data-factory-troubleshoot-walkthrough2-table-with-problem-slices]

4. Dans le panneau **TRANCHE DE DONNÉES**, cliquez sur l’**Exécution d’activité** qui a échoué.

	![Tranche de données avec des exécutions ayant échoué][image-data-factory-troubleshoot-walkthrough2-slice-activity-runs]

5. Dans le panneau **DÉTAILS SUR L’EXÉCUTION D’ACTIVITÉ**, vous pouvez télécharger les fichiers associés au traitement HDInsight. Cliquez sur **Télécharger** pour que **Status/stderr** télécharge le fichier journal d’erreur qui contient les détails sur l’erreur.

	![Détails sur l’exécution d’activité avec lien de téléchargement][image-data-factory-troubleshoot-activity-run-details]

    
### Procédure pas à pas : utilisation d’Azure PowerShell pour résoudre une erreur avec un traitement Pig/Hive
1.	Lancez **Azure PowerShell**. 
2.	Passez en mode **AzureResourceManager**, car les applets de commande Data Factory sont disponibles uniquement dans ce mode.

         
		switch-azuremode AzureResourceManager

3. Exécutez la commande Get-AzureDataFactorySlice pour voir les tranches et leur état. Vous devez voir une tranche avec l’état : Échec (Failed).

         
		Get-AzureDataFactorySlice -ResourceGroupName ADF -DataFactoryName LogProcessingFactory -TableName EnrichedGameEventsTable -StartDateTime 2014-05-04 20:00:00

	Remplacez **StartDateTime** par la valeur de StartDateTime spécifiée pour **Set-AzureDataFactoryPipelineActivePeriod**.

		ResourceGroupName : ADF
		DataFactoryName   : LogProcessingFactory
		TableName         : EnrichedGameEventsTable
		Start             : 5/5/2014 12:00:00 AM
		End               : 5/6/2014 12:00:00 AM
		RetryCount        : 0
		Status            : Failed
		LatencyStatus     :
		LongRetryCount    : 0


	Notez l’heure de **Début** pour la tranche problématique (celle dont l’**état** est défini sur **Échec**) dans la sortie. 
4. Exécutez maintenant l'applet de commande **Get-AzureDataFactoryRun** pour obtenir des détails sur l'exécution d'activité de la tranche.
         
		Get-AzureDataFactoryRun -ResourceGroupName ADF -DataFactoryName LogProcessingFactory -TableName EnrichedGameEventsTable -StartDateTime "5/5/2014 12:00:00 AM"

	La valeur de **StartDateTime** est l’heure de début de la tranche problématique/l’erreur que vous avez notée à l’étape précédente. La valeur date-heure doit être entourée de guillemets doubles.
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

6. Vous pouvez exécuter l’applet de commande **Save-AzureDataFactoryLog** avec la valeur ID indiquée dans la sortie ci-dessus et télécharger les fichiers journaux à l’aide de l’option **-DownloadLogs** pour l’applet de commande.



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
 

<!---HONumber=62-->