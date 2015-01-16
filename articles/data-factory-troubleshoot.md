<properties title="Troubleshoot Azure Data Factory issues" pageTitle="Résolution des problèmes liés à Azure Data Factory" description="Découvrez comment résoudre les problèmes liés à l'utilisation de Azure Data Factory." metaKeywords=""  services="data-factory" solutions=""  documentationCenter="" authors="spelluru" manager="jhubbard" editor="monicar" />

<tags ms.service="data-factory" ms.workload="data-services" ms.tgt_pltfrm="na" ms.devlang="na" ms.topic="article" ms.date="01/01/1900" ms.author="spelluru" />

# Résolution des problèmes liés à Data Factory
Vous pouvez résoudre les problèmes Azure Data Factory à l'aide du portail Azure ou des cmdlets PowerShell. Cette rubrique contient des procédures pas à pas qui vous montrent comment utiliser le portail Azure pour résoudre rapidement les erreurs Data Factory. 

## Dans cet article

- [Procédure pas à pas : Dépannage d'une erreur de copie des données](#copywalkthrough)
- [Procédure pas à pas : Dépannage d'une erreur de traitement Hive/Pig](#pighivewalkthrough)

## <a name="copywalkthrough"></a>Procédure pas à pas : Dépannage d'une erreur de copie des données
Dans cette procédure pas à pas, vous allez introduire une erreur dans le didacticiel de l'article Prise en main de Data Factory et découvrir comment vous pouvez utiliser le portail Azure pour la résoudre.

### Configuration requise
1. Suivez le didacticiel de l'article [Prise en main d'Azure Data Factory][adfgetstarted].
2. Confirmez que **ADFTutorialDataFactory** produit des données dans la table **emp** dans la base de données Azure SQL.  
3. Supprimez la table **emp** (**drop table emp**) de la base de données SQL Azure. Cette action introduit une erreur.
4. Exécutez la commande suivante dans **Azure PowerShell** pour mettre à jour la période active pour le pipeline de sorte qu'il tente d'écrire des données dans la table **emp**, qui n'existe plus.

         
		Set-AzureDataFactoryPipelineActivePeriod -ResourceGroupName ADFTutorialResourceGroup -DataFactoryName ADFTutorialDataFactory -StartDateTime 2014-09-29 -EndDateTime 2014-09-30 -Name ADFTutorialPipeline
	
	> [WACOM.NOTE] Remplacez la valeur <b>StartDateTime</b> par le jour actuel et la valeur <b>EndDateTime</b> par le jour suivant. 


### Utilisation de la version préliminaire du portail Azure pour résoudre l'erreur

1.	Connectez-vous à la [version préliminaire du portail Azure][azure-preview-portal]. 
2.	Cliquez sur **ADFTutorialDataFactory** depuis le **tableau d'accueil**. Si vous ne voyez pas le lien de la fabrique de données dans le **tableau d'accueil**, cliquez sur le concentrateur **PARCOURIR**, puis sur **Tout**. Cliquez sur **Fabriques de données...** dans le panneau **Parcourir**, puis cliquez sur **ADFTutorialDataFactory**.
3.	Notez que vous voyez **Avec des erreurs** sur la vignette **Jeux de données**. Cliquez sur **Avec des erreurs**. Vous devriez voir le panneau **Jeux de données avec erreurs**.

	![Data Factory with Errors link][image-data-factory-troubleshoot-with-error-link]

4. Dans le panneau **Jeux de données avec erreurs**, cliquez sur **EmpSQLTable** pour voir le panneau **TABLE**.	

	![Datasets with errors blade][image-data-factory-troubleshoot-datasets-with-errors-blade]

5. Dans le panneau **TABLE**, vous devriez voir les tranches problématiques, c'est-à-dire les tranches avec une erreur, dans la liste **Tranches problématiques** en bas. Vous pouvez également voir les tranches récentes contenant des erreurs dans la liste **Tranches récentes**. Cliquez sur une tranche dans la liste **Tranches problématiques**. 

	![Table blade with problem slices][image-data-factory-troubleshoot-table-blade-with-problem-slices]

	Cliquez sur **Tranches problématiques** (et non sur un problème spécifique) pour afficher le panneau **TRANCHES DE DONNÉS**, puis cliquez sur une **tranche problématique spécifique** pour afficher le panneau **TRANCHE DE DONNÉES** pour la tranche de données sélectionnée.

6. Dans le panneau **TRANCHE DE DONNÉES** pour **EmpSQLTable**, vous pouvez voir toutes les **exécutions d'activité** pour la tranche de la liste en bas. Cliquez sur une **exécution d'activité** depuis la liste qui a échoué.

	![Data Slice blade with active runs][image-data-factory-troubleshoot-dataslice-blade-with-active-runs]


7. Dans le panneau **Détails sur l'exécution d'activité** pour l'exécution d'activité que vous avez sélectionnée, vous devriez voir les détails de l'erreur. Dans ce scénario, vous voyez : **Nom d'objet non valide 'emp'**.

	![Activity run details with an error][image-data-factory-troubleshoot-activity-run-with-error]

Pour résoudre ce problème, créez la table **emp** à l'aide du script SQL de l'article [Prise en main de Data Factory][adfgetstarted].


### Utilisation des cmdlets Azure PowerShell pour résoudre l'erreur
1.	Lancez **Azure PowerShell**. 
2.	Passez en mode **AzureResourceManager**, car les cmdlets Data Factory sont disponibles uniquement dans ce mode.

         
		switch-azuremode AzureResourceManager

3. Exécutez la commande Get-AzureDataFactorySlice pour voir les tranches et leur état. Vous devez voir une tranche avec l'état : Échec.	

         
		Get-AzureDataFactorySlice -ResourceGroupName ADFTutorialResourceGroup -DataFactoryName ADFTutorialDataFactory -TableName EmpSQLTable -StartDateTime 2014-10-15

	> [WACOM.NOTE] Remplacez **StartDateTime** par la valeur StartDateTime spécifiée pour **Set-AzureDataFactoryPipelineActivePeriod**. 

		ResourceGroupName 		: ADFTutorialResourceGroup
		DataFactoryName   		: ADFTutorialDataFactory
		TableName         		: EmpSQLTable
		Start             		: 10/15/2014 4:00:00 PM
		End               		: 10/15/2014 5:00:00 PM
		RetryCount        		: 0
		Status            		: Failed
		LatencyStatus     		:
		LongRetryCount    		: 0

	Notez l'heure de **Début** pour la tranche problématique (celle dont l'**état** est défini sur **Échec**) dans la sortie. 
4. Exécutez la cmdlet **Get-AzureDataFactoryRun** pour obtenir les détails sur l'exécution d'activité de la tranche.
         
		Get-AzureDataFactoryRun -ResourceGroupName ADFTutorialResourceGroup -DataFactoryName ADFTutorialDataFactory -TableName EmpSQLTable -StartDateTime "10/15/2014 4:00:00 PM"

	La valeur de **StartDateTime** est l'heure de début de la tranche problématique/l'erreur que vous avez notée à l'étape précédente. La valeur date-heure doit être entourée de guillemets doubles.
5. Vous devez voir la sortie avec les détails sur l'erreur (semblable à cela) :

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

 

## <a name="pighavewalkthrough"></a>Procédure pas à pas : Dépannage d'une erreur de traitement Hive/Pig
Cette procédure pas à pas fournit les instructions pour résoudre une erreur avec un traitement Hive/Pig à l'aide de la version préliminaire du portail Azure et d'Azure PowerShell. 


### Procédure pas à pas : Utilisation du portail Azure pour résoudre une erreur avec un traitement Pig/Hive
Dans ce scénario, le jeu de données est associé à un état d'erreur dû à une défaillance dans le traitement Hive sur un cluster HDInsight.

1. Cliquez sur **Avec des erreurs** sur la vignette **Jeux de données** sur la page d'accueil **FABRIQUE DE DONNÉES**.

	![With errors link on Datasets tile][image-data-factory-troubleshoot-walkthrough2-with-errors-link]

2. Dans le panneau **Jeux de données avec erreurs**, cliquez sur la **table** qui vous intéresse.

	![Datasets with errors blade][image-data-factory-troubleshoot-walkthrough2-datasets-with-errors]

3. Dans le panneau **TABLE**, cliquez sur la **tranche problématique** dont l'**ÉTAT** est défini sur **Échec**.

	![Table with problem slices][image-data-factory-troubleshoot-walkthrough2-table-with-problem-slices]

4. Dans le panneau **TRANCHE DE DONNÉES**, cliquez sur l'**Exécution d'activité** qui a échoué.

	![Data slice with failed runs][image-data-factory-troubleshoot-walkthrough2-slice-activity-runs]

5. Dans le panneau **DÉTAILS SUR L'EXÉCUTION D'ACTIVITÉ**, vous pouvez télécharger les fichiers associés au traitement HDInsight. Cliquez sur **Télécharger** pour que **Status/stderr** télécharge le fichier journal d'erreur qui contient les détails sur l'erreur.

	![Activity run details with download link][image-data-factory-troubleshoot-activity-run-details]

    
### Procédure pas à pas : Utilisation d'Azure PowerShell pour résoudre une erreur avec un traitement Pig/Hive
1.	Lancez **Azure PowerShell**. 
2.	Passez en mode **AzureResourceManager**, car les cmdlets Data Factory sont disponibles uniquement dans ce mode.

         
		switch-azuremode AzureResourceManager

3. Exécutez la commande Get-AzureDataFactorySlice pour voir les tranches et leur état. Vous devez voir une tranche avec l'état : Échec.	

         
		Get-AzureDataFactorySlice -ResourceGroupName ADF -DataFactoryName LogProcessingFactory -TableName EnrichedGameEventsTable -StartDateTime 2014-05-04 20:00:00

	> [WACOM.NOTE] Remplacez **StartDateTime** par la valeur StartDateTime spécifiée pour **Set-AzureDataFactoryPipelineActivePeriod**. 

		ResourceGroupName : ADF
		DataFactoryName   : LogProcessingFactory
		TableName         : EnrichedGameEventsTable
		Start             : 5/5/2014 12:00:00 AM
		End               : 5/6/2014 12:00:00 AM
		RetryCount        : 0
		Status            : Failed
		LatencyStatus     :
		LongRetryCount    : 0


	Notez l'heure de **Début** pour la tranche problématique (celle dont l'**état** est défini sur **Échec**) dans la sortie. 
4. Exécutez la cmdlet **Get-AzureDataFactoryRun** pour obtenir les détails sur l'exécution d'activité de la tranche.
         
		Get-AzureDataFactoryRun -ResourceGroupName ADF -DataFactoryName LogProcessingFactory -TableName EnrichedGameEventsTable -StartDateTime "5/5/2014 12:00:00 AM"

	La valeur de **StartDateTime** est l'heure de début de la tranche problématique/l'erreur que vous avez notée à l'étape précédente. La valeur date-heure doit être entourée de guillemets doubles.
5. Vous devez voir la sortie avec les détails sur l'erreur (semblable à cela) :

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

6. Vous pouvez exécuter la cmdlet **Save-AzureDataFactoryLog** avec la valeur ID indiquée dans la sortie ci-dessus et télécharger les fichiers journaux à l'aide de l'option **-DownloadLogs** pour la cmdlet.

## Conseils de dépannage

### Échec de connexion à un serveur SQL Server local 
Vérifiez que SQL Server est accessible à partir de l'ordinateur sur lequel la passerelle est installée.


1. Exécutez un Ping sur l'ordinateur où est installé SQL Server
2. Sur l'ordinateur sur lequel la passerelle est installée, essayez de vous connecter à l'instance SQL Server en utilisant les informations d'identification que vous avez spécifiées sur le portail Azure à l'aide de SQL Server Management Studio (SSMS).

### Échec de l'opération de copie
1. Lancez le Gestionnaire de configuration des passerelles de gestion des données sur l'ordinateur sur lequel la passerelle est installée. Vérifiez que le **Nom de la passerelle** est défini sur le nom de la passerelle logique sur le **portail Azure**, l'**état de la clé de la passerelle** est **inscrite** et l'**état du service** est **Démarré**. 
2. Lancez l'**Observateur d'événements**. Développez **Journaux des applications et des services** et cliquez sur **Passerelle de gestion des données**. Recherchez les erreurs liées à la passerelle de gestion des données. 



## Voir aussi

Article | Description
------ | ---------------
[Activation de vos pipelines pour les utiliser avec des données locales][use-onpremises-datasources] | Cet article contient une procédure pas à pas permettant de copier les données d'une base de données SQL Server locale vers un objet blob Azure.
[Utilisation de Pig et Hive avec Data Factory][use-pig-and-hive-with-data-factory] | Cet article contient une procédure pas à pas permettant d'exécuter un script Hive/Pig à l'aide de HDInsight Activity pour traiter les données d'entrée afin de produire des données de sortie. 
[Didacticiel : Déplacement et traitement des fichiers journaux à l'aide de Data Factory][adf-tutorial] | Cet article contient une procédure pas à pas permettant d'implémenter un scénario proche du monde réel à l'aide d'Azure Data Factory pour transformer les données des fichiers journaux en données détaillées.
[Utilisation des activités personnalisées de Data Factory][use-custom-activities] | Cet article contient une procédure pas à pas permettant de créer une activité personnalisée et de l'utiliser dans un pipeline. 
[Surveillance et gestion d'Azure Data Factory à l'aide de PowerShell][monitor-manage-using-powershell] | Cet article décrit comment surveiller une fabrique de données Azure à l'aide de cmdlets Azure PowerShell. 
[Résolution des problèmes liés à Data Factory][troubleshoot] | Cet article explique comment résoudre des problèmes liés à Azure Data Factory. Vous pouvez essayer la procédure pas à pas de cet article portant sur ADFTutorialDataFactory en introduisant une erreur (en supprimant la table dans la base de données SQL Azure). 
[Référence du développeur Azure Data Factory][developer-reference] | Ces informations de référence du développeur comportent des informations complètes sur les cmdlets, le script JSON, les fonctions, etc. 
[Référence des cmdlets Azure Data Factory][cmdlet-reference] | Ces informations de référence comportent des détails sur toutes les **cmdlets Data Factory**.

[adfgetstarted]: ../data-factory-get-started
[use-onpremises-datasources]: ../data-factory-use-onpremises-datasources
[use-pig-and-hive-with-data-factory]: ../data-factory-pig-hive-activities
[adf-tutorial]: ../data-factory-tutorial
[use-custom-activities]: ../data-factory-use-custom-activities
[monitor-manage-using-powershell]: ../data-factory-monitor-manage-using-powershell
[troubleshoot]: ../data-factory-troubleshoot
[developer-reference]: http://go.microsoft.com/fwlink/?LinkId=516908
[cmdlet-reference]: http://go.microsoft.com/fwlink/?LinkId=517456

[azure-preview-portal]: https://portal.azure.com/

[image-data-factory-troubleshoot-with-error-link]: ./media/data-factory-troubleshoot/DataFactoryWithErrorLink.png

[image-data-factory-troubleshoot-datasets-with-errors-blade]: ./media/data-factory-troubleshoot/DatasetsWithErrorsBlade.png

[image-data-factory-troubleshoot-table-blade-with-problem-slices]: ./media/data-factory-troubleshoot/TableBladeWithProblemSlices.png

[image-data-factory-troubleshoot-activity-run-with-error]: ./media/data-factory-troubleshoot/DataSliceBladeWithActivityRuns.png

[image-data-factory-troubleshoot-dataslice-blade-with-active-runs]: ./media/data-factory-troubleshoot/ActivityRunDetailsWithError.png

[image-data-factory-troubleshoot-walkthrough2-with-errors-link]: ./media/data-factory-troubleshoot/Walkthrough2WithErrorsLink.png

[image-data-factory-troubleshoot-walkthrough2-datasets-with-errors]: ./media/data-factory-troubleshoot/Walkthrough2DataSetsWithErrors.png

[image-data-factory-troubleshoot-walkthrough2-table-with-problem-slices]: ./media/data-factory-troubleshoot/Walkthrough2TableProblemSlices.png

[image-data-factory-troubleshoot-walkthrough2-slice-activity-runs]: ./media/data-factory-troubleshoot/Walkthrough2DataSliceActivityRuns.png

[image-data-factory-troubleshoot-activity-run-details]: ./media/data-factory-troubleshoot/Walkthrough2ActivityRunDetails.png
