<properties title="Monitor and manage Azure Data Factory using Azure PowerShell" pageTitle="Surveillance et gestion d'Azure Data Factory à l'aide d'Azure PowerShell" description="Learn how to use Azure PowerShell to monitor and manage Azure data factories you have created." metaKeywords=""  services="data-factory" solutions=""  documentationCenter="" authors="spelluru" manager="jhubbard" editor="monicar" />

<tags ms.service="data-factory" ms.workload="data-services" ms.tgt_pltfrm="na" ms.devlang="na" ms.topic="article" ms.date="01/01/1900" ms.author="spelluru" />

# Surveillance et gestion d'Azure Data Factory à l'aide d'Azure PowerShell
Le tableau suivant répertorie les cmdlets vous pouvez utiliser pour surveiller et gérer des fabriques de données Azure à l'aide d'Azure PowerShell. 

> [WACOM.NOTE] Consultez [Référence des cmdlets Data Factory][cmdlet-reference] pour une documentation complète sur les cmdlets de fabrique de données. 


- [Get-AzureDataFactory](#get-azuredatafactory)
- [Get-AzureDataFactoryLinkedService](#get-azuredatafactorylinkedservice)
- [Get-AzureDataFactoryTable](#get-azuredatafactorytable)
- [Get-AzureDataFactoryPipeline](#get-azuredatafactorypipeline)
- [Get-AzureDataFactorySlice](#get-azuredatafactoryslice)
- [Get-AzureDataFactoryRun](#get-azuredatafactoryrun)
- [Save-AzureDataFactoryLog](#save-azuredatafactorylog)
- [Get-AzureDataFactoryGateway](#get-azuredatafactorygateway)
- [Set-AzureDataFactoryPipelineActivePeriod](#set-azuredatafactorypipelineactiveperiod)
- [Set-AzureDataFactorySliceStatus](#set-azuredatafactoryslicestatus)
- [Suspend-AzureDataFactoryPipeline](#suspend-azuredatafactorypipeline)
- [Resume-AzureDataFactoryPipeline](#resume-azuredatafactorypipeline)


##<a name="get-azuredatafactory"></a>Get-AzureDataFactory
Obtient les informations sur une fabrique de données spécifique ou toutes les fabriques de données dans un abonnement Azure du groupe de ressources spécifié.
 
###Exemple 1

    Get-AzureDataFactory -ResourceGroupName ADFTutorialResourceGroup

Cette commande renvoie toutes les fabriques de données dans le groupe de ressources ADFTutorialResourceGroup.
 
###Exemple 2

    Get-AzureDataFactory -ResourceGroupName ADFTutorialResourceGroup -Name ADFTutorialDataFactory

Cette commande retourne des détails sur la fabrique de données ADFTutorialDataFactory du groupe de ressources ADFTutorialResourceGroup. 

## <a name="get-azuredatafactorylinkedservice"></a> Get-AzureDataFactoryLinkedService ##
La cmdlet Get-AzureDataFactoryLinkedService obtient des informations sur un service lié spécifique ou tous les services liés dans une fabrique de données Azure.

### Exemple 1 ###

    Get-AzureDataFactoryLinkedService -ResourceGroupName ADFTutorialResourceGroup -DataFactoryName ADFTutorialDataFactory
 
Cette commande retourne des informations sur tous les services liés dans la fabrique de données Azure ADFTutorialDataFactory.


Vous pouvez utiliser le paramètre -DataFactory au lieu de DataFactoryName et ResourceGroupName. Il permet d'entrer les noms de groupe de ressources et de fabrique une seule fois et d'utiliser Data Factory en tant que paramètre pour toutes les cmdlets qui ont pour paramètres ResourceGroupName et DataFactoryName.

    $df = Get-AzureDataFactory -ResourceGroup ADFTutorialResourceGroup -DataFactoryName ADFTutorialDataFactory
	
	Get-AzureDataFactoryLinkedService -DataFactory $df 

### Exemple 2

    Get-AzureDataFactoryLinkedService -ResourceGroupName ADFTutorialResourceGroup -DataFactoryName ADFTutorialDataFactory -Name MyBlobStore

Cette commande retourne des informations sur le service lié MyBlobStore dans la fabrique de données Azure ADFTutorialDataFactory. 

Vous pouvez utiliser le paramètre -DataFactory au lieu de -ResourceGroup et -DataFactoryName comme indiqué ci-dessous : 


    $df = Get-AzureDataFactory -ResourceGroup ADFTutorialResourceGroup -DataFactoryName ADFTutorialDataFactory
	
	Get-AzureDataFactoryLinkedService -DataFactory $df -Name MyBlobStore


## <a name="get-azuredatafactorytable"></a> Get-AzureDataFactoryTable
La cmdlet Get-AzureDataFactoryTable obtient des informations sur une table spécifique ou toutes les tables dans une fabrique de données Azure. 

### Exemple 1

    Get-AzureDataFactoryTable -ResourceGroupName ADFTutorialResourceGroup -DataFactoryName ADFTutorialDataFactory

Cette commande retourne des informations sur toutes les tables de la fabrique de données Azure ADFTutorialDataFactory.

Vous pouvez utiliser le paramètre -DataFactory au lieu de -ResourceGroup et -DataFactoryName comme indiqué ci-dessous : 


    $df = Get-AzureDataFactory -ResourceGroup ADFTutorialResourceGroup -DataFactoryName ADFTutorialDataFactory
	
	Get-AzureDataFactoryTable -DataFactory $df

### Exemple 2

    Get-AzureDataFactoryTable -ResourceGroupName ADFTutorialResourceGroup -DataFactoryName ADFTutorialDataFactory -Name EmpTableFromBlob

Cette commande retourne des informations sur la table EmpTableFromBlob de la fabrique de données Azure ADFTutorialDataFactory.



## <a name="get-azuredatafactorypipeline"></a>Get-AzureDataFactoryPipeline
La cmdlet Get-AzureDataFactoryPipeline obtient des informations sur un pipeline spécifique ou tous les pipelines dans une fabrique de données Azure.

### Exemple 1

    Get-AzureDataFactoryPipeline -ResourceGroupName ADFTutorialResourceGroup -DataFactoryName ADFTutorialDataFactory

Cette commande retourne des informations sur tous les pipelines de la fabrique de données Azure ADFTutorialDataFactory.

### Exemple 2

    Get-AzureDataFactoryPipeline -ResourceGroupName ADFTutorialResourceGroup -DataFactoryName ADFTutorialDataFactory -Name ADFTutorialPipeline

Obtient des informations sur le pipeline ADFTutorialPipeline dans la fabrique de données Azure ADFTutorialDataFactory.

## <a name="get-azuredatafactoryslice"> </a> Get-AzureDataFactorySlice
La cmdlet Get-AzureDataFactorySlice obtient toutes les tranches d'une table dans une fabrique de données Azure qui sont produites après StartDateTime et avant EndDateTime. La tranche de données dont l'état est Ready est prête à être consommée par des tranches dépendantes.

Le tableau suivant répertorie tous les états d'une tranche et leurs descriptions.

<table border="1">	
	<tr>
		<th align="left">Statut</th>
		<th align="left">Description</th>
	</tr>	

	<tr>
		<td>PendingExecution</td>
		<td>Le traitement des données n'a pas encore démarré.</td>
	</tr>	

	<tr>
		<td>InProgress</td>
		<td>Le traitement des données est en cours.</td>
	</tr>

	<tr>
		<td>Ready</td>
		<td>Le traitement des données est terminé et la tranche de données est prête.</td>
	</tr>

	<tr>
		<td>Failed</td>
		<td>Échec de l'exécution qui produit la tranche.</td>
	</tr>

	<tr>
		<td>Skip</td>
		<td>Le traitement de la tranche est ignoré.</td>
	</tr>

	<tr>
		<td>Retry</td>
		<td>Nouvelle tentative de l'exécution qui produit la tranche.</td>
	</tr>

	<tr>
		<td>Timed Out</td>
		<td>Le traitement des données du secteur a expiré.</td>
	</tr>

	<tr>
		<td>PendingValidation</td>
		<td>La tranche de données est en attente de validation par rapport aux stratégies de validation avant d'être traitée.</td>
	</tr>

	<tr>
		<td>Retry Validation</td>
		<td>La validation de la tranche est renouvelée.</td>
	</tr>

	<tr>
		<td>Failed Validation</td>
		<td>Échec de validation de la tranche.</td>
	</tr>

	<tr>
		<td>LongRetry</td>
		<td>Une tranche est dans cet état si LongRetry est spécifié dans le tableau JSON et si les tentatives régulières de la tranche ont échoué.</td>
	</tr>

	<tr>
		<td>ValidationInProgress</td>
		<td>Validation de la tranche (en fonction des stratégies définies du tableau JSON) en cours d'exécution.</td>
	</tr>

</table>

Pour chacune des tranches, vous pouvez explorer plus en détail et obtenir plus d'informations sur l'exécution qui produit la tranche à l'aide des cmdlets Get-AzureDataFactoryRun et Save-AzureDataFactoryLog.

### Exemple

    Get-AzureDataFactorySlice -ResourceGroupName ADFTutorialResourceGroup -DataFactoryName ADFTutorialDataFactory -TableName EmpSQLTable -StartDateTime 2014-05-20T10:00:00

Cette commande obtient toutes les tranches de la table EmpSQLTable dans la fabrique de données Azure ADFTutorialDataFactory produite après 2014-05-20T10:00:00 (GMT). Remplacez date-time par la date et l'heure de début que vous avez spécifiées en exécutant Set-AzureDataFactoryPipelineActivePeriod.

## <a name="get-azuredatafactoryrun"></a> Get-AzureDataFactoryRun

La cmdlet Get-AzureDataFactoryRun obtient toutes les exécutions d'une tranche de données d'une table dans une fabrique de données Azure.  Celle-ci se compose de tranches sur l'axe temporel. La largeur d'une tranche est déterminée par la planification en heure/jour. L'exécution est une unité de traitement d'une tranche. Une tranche peut être exécutée une ou plusieurs fois en cas de nouvelles tentatives ou de défaillance. Elle est identifiée par son heure de début. Pour la cmdlet Get-AzureDataFactoryRun, vous devez donc passer l'heure de début de la tranche à partir des résultats de cmdlet Get-AzureDataFactorySlice.

Par exemple, pour obtenir une exécution de la tranche suivante, vous utilisez 2015-04-02T20:00:00. 

    ResourceGroupName  	: ADFTutorialResourceGroup
    DataFactoryName 	: ADFTutorialDataFactory
    TableName 			: EmpSQLTable
    Start 				: 5/2/2014 8:00:00 PM
    End 				: 5/3/2014 8:00:00 PM
    RetryCount 			: 0
    Status 				: Ready
    LatencyStatus 		:



### Exemple

    Get-AzureDataFactoryRun -DataFactoryName ADFTutorialDataFactory -TableName EmpSQLTable -ResourceGroupName ADFTutorialResourceGroup -StartDateTime 2014-05-21T16:00:00

Cette commande obtient toutes les exécutions des tranches de la table EmpSQLTable dans la fabrique de données Azure ADFTutorialDataFactory à partir de 16H00 le 21/05/2014.

## <a name="save-azuredatafactorylog"></a> Save-AzureDataFactoryLog
La cmdlet Save-AzureDataFactoryLog télécharge les fichiers journaux associés au traitement Azure HDInsight des projets Pig ou Hive, ou pour les activités personnalisées sur votre disque dur local. Vous exécutez d'abord la cmdlet Get-AzureDataFactoryRun pour obtenir un ID d'une activité à exécuter pour une tranche de données, puis vous utilisez cet ID pour récupérer les fichiers journaux du stockage d'objets blob (binary large object) associé au cluster HDInsight. 

Si vous ne spécifiez pas le paramètre **-DownloadLogs**, la cmdlet renvoie simplement l'emplacement des fichiers journaux. 

Si vous spécifiez le paramètre **-DownloadLogs** sans spécifier de répertoire de sortie (paramètre **-Output**), les fichiers journaux sont téléchargés dans le dossier **Documents** par défaut. 

Si vous spécifiez le paramètre **-DownloadLogs** avec un dossier de sortie (**-Output**), les fichiers journaux sont téléchargés dans le dossier spécifié. 


### Exemple 1
Cette commande enregistre les fichiers journaux pour l'exécution de l'activité avec l'ID 841b77c9-d56c-48d1-99a3-8c16c3e77d39 où l'activité appartient à un pipeline dans la fabrique de données nommée LogProcessingFactory du groupe de ressources nommé ADF. Les fichiers journaux sont enregistrés dans le dossier C:\Test. 

	Save-AzureDataFactoryLog -ResourceGroupName "ADF" -DataFactoryName "LogProcessingFactory" -Id "841b77c9-d56c-48d1-99a3-8c16c3e77d39" -DownloadLogs -Output "C:\Test"
 

### Exemple 2
Cette commande enregistre les fichiers journaux dans le dossier Documents (par défaut).


	Save-AzureDataFactoryLog -ResourceGroupName "ADF" -DataFactoryName "LogProcessingFactory" -Id "841b77c9-d56c-48d1-99a3-8c16c3e77d39" -DownloadLogs
 

### Exemple 3
Cette commande renvoie l'emplacement des fichiers journaux. Notez que le paramètre -DownloadLogs n'est pas spécifié. 
  
	Save-AzureDataFactoryLog -ResourceGroupName "ADF" -DataFactoryName "LogProcessingFactory" -Id "841b77c9-d56c-48d1-99a3-8c16c3e77d39"
 



## <a name="get-azuredatafactorygateway"></a> Get-AzureDataFactoryGateway
La cmdlet Get-AzureDataFactoryGateway obtient des informations sur une passerelle spécifique ou toutes les passerelles dans une fabrique de données Azure. Vous devez installer une passerelle sur votre ordinateur local pour pouvoir ajouter un serveur SQL Server local comme un service lié à une fabrique de données.

### Exemple 1
    Get-AzureDataFactoryGateway -ResourceGroupName ADFTutorialResourceGroup -DataFactoryName ADFTutorialDataFactory
Cette commande retourne des informations sur toutes les passerelles de la fabrique de données Azure ADFTutorialDataFactory.

### Exemple 2

    Get-AzureDataFactoryGateway -ResourceGroupName ADFTutorialResourceGroup -DataFactoryName ADFTutorialDataFactory -Name ADFTutorialGateway

Cette commande retourne des informations sur la passerelle ADFTutorialGateway de la fabrique de données Azure ADFTutorialDataFactory.

## <a name="set-azuredatafactorypipelineactiveperiod"></a> Set-AzureDataFactoryPipelineActivePeriod
Cette cmdlet définit la période active pour les tranches de données traitées par le pipeline. Si vous utilisez Set-AzureDataFactorySliceStatus, assurez-vous que les dates de début et de fin de la tranche se trouvent dans la période active du pipeline.

Une fois les pipelines créés, vous pouvez spécifier la durée de traitement des données. En spécifiant la période active pour un pipeline, vous définissez la durée de traitement des tranches de données en fonction des propriétés de disponibilité qui ont été définies pour chaque table ADF.

### Exemple

    Set-AzureDataFactoryPipelineActivePeriod  -Name ADFTutorialPipeline -ResourceGroupName ADFTutorialResourceGroup -DataFactoryName ADFTutorialDataFactory -StartDateTime 2014-05-21T16:00:00 -EndDateTime 2014-05-22T16:00:00

Cette commande définit la période active pour les tranches de données traitées par le pipeline ADFTutorialPipeline du 21/5/2014 à 16H00 au 22/5/2014 à 16H00.

## <a name="set-azuredatafactoryslicestatus"></a> Set-AzureDataFactorySliceStatus
Définit l'état d'une tranche pour une table. Les dates de début et de fin de la tranche doivent se situer dans la période active du pipeline.

### Valeurs prises en charge pour l'état
Chaque tranche de données pour une table passe par différentes étapes. Ces étapes sont légèrement différentes selon si les stratégies de validation sont spécifiées.


- Si les stratégies de validation ne  sont pas spécifiées : PendingExecution -> InProgress -> Ready
- Si les stratégies de validation sont spécifiées : PendingExecution -> Pending Validation -> InProgress -> Ready

Le tableau suivant fournit les descriptions des états possibles d'une tranche et indique si l'état peut être défini à l'aide de Set-AzureDataFactorySliceStatus ou non.

<table border="1">	
	<tr>
		<th>Statut</th>
		<th>Description</th>
		<th>Peut être défini à l'aide d'une cmdlet></th>
	</tr>	

	<tr>
		<td>PendingExecution</td>
		<td>Le traitement des données n'a pas encore démarré.</td>
		<td>O</td>
	</tr>	

	<tr>
		<td>InProgress</td>
		<td>Le traitement des données est en cours.</td>
		<td>N</td>
	</tr>

	<tr>
		<td>Ready</td>
		<td>Le traitement des données est terminé et la tranche de données est prête.</td>
		<td>O</td>
	</tr>

	<tr>
		<td>Failed</td>
		<td>Échec de l'exécution qui produit la tranche.</td>
		<td>N</td>
	</tr>

	<tr>
		<td>Skip</td>
		<td>Le traitement de la tranche est ignoré.</td>
		<td>O</td>
	</tr>

	<tr>
		<td>Retry</td>
		<td>Nouvelle tentative de l'exécution qui produit la tranche.</td>
		<td>N</td>
	</tr>

	<tr>
		<td>Timed Out</td>
		<td>Le traitement des données a expiré.</td>
		<td>N</td>
	</tr>

	<tr>
		<td>PendingValidation</td>
		<td>La tranche de données est en attente de validation par rapport aux stratégies de validation avant d'être traitée.</td>
		<td>O</td>
	</tr>

	<tr>
		<td>Retry Validation</td>
		<td>La validation de la tranche est renouvelée.</td>
		<td>N</td>
	</tr>

	<tr>
		<td>Failed Validation</td>
		<td>Échec de validation de la tranche.</td>
		<td>N</td>
	</tr>
	</table>


### Valeurs prises en charge - Type de mise à jour
Pour chaque table d'une fabrique de données Azure, quand vous définissez l'état d'une tranche, vous devez spécifier si la mise à jour de l'état s'applique à la table uniquement, ou si les mises à jour de l'état se propagent à toutes les tranches concernées.

<table border="1">	
	<tr>
		<th>Type de mise à jour</th>
		<th>Description</th>
		<th>Peut être défini à l'aide d'une cmdlet</th>
	</tr>

	<tr>
		<td>Individual</td>
		<td>Définit l'état de chaque tranche de la table dans la période spécifiée</td>
		<td>O</td>
	</tr>

	<tr>
		<td>UpstreamInPipeline</td>
		<td>Définit l'état de chaque tranche de la table et de toutes les tables (en amont) dépendantes qui sont utilisées en tant que tables d'entrée pour les activités du pipeline.</td>
		<td>O</td>
	</tr>

</table>
## <a name="suspend-azuredatafactorypipeline"></a> Suspend-AzureDataFactoryPipeline
La cmdlet Suspend-AzureDataFactoryPipeline interrompt le pipeline spécifié dans une fabrique de données Azure. Vous pouvez reprendre ultérieurement le pipeline à l'aide de la cmdlet Resume-AzureDataFactoryPipeline.

### Exemple

    Suspend-AzureDataFactoryPipeline -Name ADFTutorialPipeline -DataFactoryName ADFTutorialDataFactory -ResourceGroupName ADFTutorialResourceGroup

Cette commande interrompt le pipeline ADFTutorialPipeline dans la fabrique de données Azure ADFTutorialDataFactory.

## <a name="resume-azuredatafactorypipeline"></a> Resume-AzureDataFactoryPipeline
La cmdlet Resume-AzureDataFactoryPipeline reprend le pipeline spécifié dont l'état actuel est interrompu dans une fabrique de données Azure. 

### Exemple

    Resume-AzureDataFactoryPipeline ADFTutorialPipeline -DataFactoryName ADFTutorialDataFactory -ResourceGroupName ADFTutorialResourceGroup

Cette commande reprend le pipeline ADFTutorialPipeline dans la fabrique de données Azure ADFTutorialDataFactory qui a été interrompu avant, à l'aide de la commande Suspend-AzureDataFactoryPipeline.

## Voir aussi

Article | Description
------ | ---------------
[Surveillance et gestion d'Azure Data Factory à l'aide du portail Azure en version préliminaire][monitor-manage-using-portal] | Cet article explique comment surveiller et gérer une fabrique de données Azure à l'aide du portail Azure en version préliminaire.
[Activation de vos pipelines pour les utiliser avec des données locales][use-onpremises-datasources] | Cet article contient une procédure pas à pas permettant de copier les données d'une base de données SQL Server locale vers un objet blob Azure.
[Utilisation de Pig et Hive avec Data Factory][use-pig-and-hive-with-data-factory] | Cet article contient une procédure pas à pas permettant d'exécuter un script Hive/Pig à l'aide de HDInsight Activity pour traiter les données d'entrée afin de produire des données de sortie. 
[Didacticiel : Déplacement et traitement des fichiers journaux à l'aide de Data Factory][adf-tutorial] | Cet article contient une procédure pas à pas permettant d'implémenter un scénario proche du monde réel à l'aide d'Azure Data Factory pour transformer les données des fichiers journaux en données détaillées.
[Utilisation des activités personnalisées de Data Factory][use-custom-activities] | Cet article contient une procédure pas à pas permettant de créer une activité personnalisée et de l'utiliser dans un pipeline. 
[Résolution des problèmes liés à Data Factory][troubleshoot] | Cet article décrit comment résoudre les problèmes liés à Azure Data Factory.
[Référence du développeur Azure Data Factory][developer-reference] | Ces informations de référence du développeur comportent des informations complètes sur les cmdlets, le script JSON, les fonctions, etc. 
[Référence des cmdlets Azure Data Factory][cmdlet-reference] | Ces informations de référence comportent des détails sur toutes les **cmdlets Data Factory**.

[use-onpremises-datasources]: ../data-factory-use-onpremises-datasources
[use-pig-and-hive-with-data-factory]: ../data-factory-pig-hive-activities
[adf-tutorial]: ../data-factory-tutorial
[use-custom-activities]: ../data-factory-use-custom-activities
[monitor-manage-using-portal]: ../data-factory-monitor-manage-using-management-portal

[troubleshoot]: ../data-factory-troubleshoot
[developer-reference]: http://go.microsoft.com/fwlink/?LinkId=516908
[cmdlet-reference]: http://go.microsoft.com/fwlink/?LinkId=517456
