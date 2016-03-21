<properties 
	pageTitle="Surveillance et gestion des pipelines d’Azure Data Factory" 
	description="Découvrez comment utiliser le portail Azure Classic et Azure PowerShell pour analyser et gérer les fabriques de données et les pipelines Azure que vous avez créés." 
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
	ms.date="01/04/2016" 
	ms.author="spelluru"/>


# Surveillance et gestion des pipelines d’Azure Data Factory
> [AZURE.SELECTOR]
- [Utilisation du portail Azure/d’Azure PowerShell](data-factory-monitor-manage-pipelines.md)
- [Utilisation de l’application de surveillance et gestion](data-factory-monitor-manage-app.md)

Le service Data Factory offre une vision fiable et complète de vos services de stockage, de traitement et de déplacement des données. Il vous permet d'évaluer rapidement l'intégrité du pipeline de données de bout en bout, d'identifier les problèmes et de prendre des mesures correctives, si nécessaire. En outre, vous pouvez suivre visuellement le lignage des données et les relations entre vos données sur n'importe quelle source et consulter un historique complet de l'exécution des travaux, de l'intégrité du système et des dépendances à partir d'un tableau de bord de surveillance unique.

Dans cet article, vous apprendrez à surveiller, gérer et déboguer vos pipelines. Vous obtiendrez également des informations sur la façon de créer des alertes et être averti en cas d’échec.

## Présentation des pipelines et des états d’activité
À l'aide du portail Azure, vous pouvez afficher votre fabrique de données sous forme de diagramme, voir les activités d’un pipeline, visualiser les jeux de données d’entrée et de sortie, et bien plus encore. Cette section montre également comment une tranche de données passe d'un état à un autre.

### Accédez à votre fabrique de données
1.	Connectez-vous au [portail Azure](https://portal.azure.com).
2.	Cliquez sur **Parcourir tout** et sélectionnez **Fabriques de données**.
	
	![Parcourir tout -> Fabriques de données](./media/data-factory-monitor-manage-pipelines/browseall-data-factories.png)

	Vous devez voir toutes les fabriques de données dans le panneau **Fabriques de données**. 
4. Dans le panneau Fabriques de données, sélectionnez la fabrique de données qui vous intéresse. La page d’accueil associée devrait apparaître (panneau **Fabrique de données**).

	![Panneau Data Factory](./media/data-factory-monitor-manage-pipelines/data-factory-blade.png)

#### Vue schématique de votre fabrique de données
La vue schématique d'une fabrique de données est un point unique de surveillance et de gestion de la fabrique de données et de ses ressources.

Cliquez sur **Schématique** sur la page d'accueil de la fabrique de données ci-dessus pour afficher la vue schématique de votre fabrique de données.

![Vue schématique](./media/data-factory-monitor-manage-pipelines/diagram-view.png)

Vous pouvez faire un zoom avant, un zoom arrière, un zoom à 100 %, un zoom pour ajuster l’affichage à la taille de l’écran, figer l’affichage schématique, positionner automatiquement les pipelines et les tables, et afficher le lignage (mise en surbrillance des éléments en amont et en aval des éléments sélectionnés).
 

### Activités à l'intérieur d'un pipeline 
1. Cliquez avec le bouton droit sur le pipeline de votre choix puis cliquez sur **Ouvrir le pipeline** pour faire apparaître toutes les activités dans le pipeline, ainsi que les jeux de données d’entrée et de sortie des activités. Cette action est utile quand votre pipeline comprend plusieurs activités et que vous souhaitez comprendre le lignage opérationnel d'un seul pipeline.

	![Menu Ouvrir un pipeline](./media/data-factory-monitor-manage-pipelines/open-pipeline-menu.png)	 
2. Dans l'exemple suivant, deux activités dans le pipeline s’affichent avec leurs entrées et leurs sorties. Les activités intitulées **JoinData** du type d'activité Hive HDInsight et **EgressDataAzure** du type d'activité Copy figurent dans cet exemple de pipeline. 
	
	![Activités à l'intérieur d'un pipeline](./media/data-factory-monitor-manage-pipelines/activities-inside-pipeline.png) 
3. Vous pouvez revenir à la page d'accueil de Data Factory. Pour ce faire, cliquez sur le lien Data Factory dans l'arborescence de navigation située dans le coin supérieur gauche.

	![Revenir à la fabrique de données](./media/data-factory-monitor-manage-pipelines/navigate-back-to-data-factory.png)

### Affichez l’état de chaque activité à l'intérieur d'un pipeline
Vous pouvez afficher l'état actuel d'une activité en consultant l'état de l'un des jeux de données générés par l'activité.

Exemple : dans l'exemple suivant, **BlobPartitionHiveActivity** a été correctement exécutée et a produit le jeu de données nommé **PartitionedProductsUsageTable** dont l’état est **Ready** (prêt).

![État du pipeline](./media/data-factory-monitor-manage-pipelines/state-of-pipeline.png)

Double-cliquez sur **PartitionedProductsUsageTable** dans la vue schématique pour afficher toutes les tranches produites par différentes exécutions d’activité à l'intérieur d'un pipeline. Comme vous le constaterez, l’activité **BlobPartitionHiveActivity** s’est déroulée correctement sur les 8 derniers mois et a produit les tranches dont l’état est **Ready** (prêt).

Voici la liste des différents états possibles des tranches d’un jeu de données d’une fabrique de données :

<table>
<tr>
	<th align="left">État</th><th align="left">Sous-état</th><th align="left">Description</th>
</tr>
<tr>
	<td rowspan="8">En attente</td><td>ScheduleTime</td><td>L'heure n’est pas venue pour l’exécution de la tranche.</td>
</tr>
<tr>
<td>DatasetDependencies</td><td>Les dépendances en amont ne sont pas prêtes.</td>
</tr>
<tr>
<td>ComputeResources</td><td>Les ressources de calcul ne sont pas disponibles.</td>
</tr>
<tr>
<td>ConcurrencyLimit</td> <td>Toutes les instances d'activité sont occupées par l'exécution d'autres tranches.</td>
</tr>
<tr>
<td>ActivityResume</td><td>L’activité est suspendue et ne peut pas exécuter les tranches jusqu'à sa reprise.</td>
</tr>
<tr>
<td>Retry</td><td>L’exécution de l’activité sera retentée.</td>
</tr>
<tr>
<td>Validation</td><td>La validation n’a pas encore démarré.</td>
</tr>
<tr>
<td>ValidationRetry</td><td>Attente d’une nouvelle tentative de validation.</td>
</tr>
<tr>
&lt;tr
<td rowspan="2">InProgress</td><td>Validation</td><td>Validation en cours.</td>
</tr>
<td></td>
<td>La tranche est en cours de traitement.</td>
</tr>
<tr>
<td rowspan="4">Échec</td><td>TimedOut</td><td>L'exécution a pris plus de temps que l’activité ne l’autorise.</td>
</tr>
<tr>
<td>Canceled</td><td>Annulé par l’action de l’utilisateur.</td>
</tr>
<tr>
<td>Validation</td><td>La validation a échoué.</td>
</tr>
<tr>
<td></td><td>Impossible de générer ou de valider la tranche.</td>
</tr>
<td>Ready</td><td></td><td>La tranche est prête à être consommée.</td>
</tr>
<tr>
<td>Ignoré</td><td></td><td>La tranche n’est pas traitée.</td>
</tr>
<tr>
<td>Aucun</td><td></td><td>Tranche qui a été réinitialisée alors qu’elle existait avec un état différent.</td>
</tr>
</table>



Vous pouvez afficher les détails relatifs à une tranche de données en cliquant sur une entrée de tranche dans le panneau **Tranches mises à jour récemment**.

![Détails de la tranche](./media/data-factory-monitor-manage-pipelines/slice-details.png)
 
Si la tranche a été exécutée plusieurs fois, plusieurs lignes s’affichent dans la liste **Exécutions d’activité**.

![Exécutions d’activité pour une tranche](./media/data-factory-monitor-manage-pipelines/activity-runs-for-a-slice.png)

Vous pouvez afficher des détails sur une exécution d’activité en cliquant sur l'entrée d'exécution dans la liste **Exécutions d’activité**. Tous les fichiers journaux vont apparaître ainsi que le message d'erreur associé, le cas échéant. Cette fonction est utile et pour cause. Vous visualisez et déboguez les journaux sans le souci de quitter votre fabrique de données.

![Détails de l'exécution d'activité](./media/data-factory-monitor-manage-pipelines/activity-run-details.png)

Si la tranche n'a pas l'état **Prêt**, vous pouvez voir les tranches en amont qui ne sont pas prêtes et qui empêchent l'exécution de la tranche actuelle dans la liste **Tranches en amont qui ne sont pas prêtes**. Cette action est utile lorsque votre tranche présente l’état **Waiting** et que vous voulez connaître les dépendances en amont à l’origine de cette attente.

![Tranches en amont qui ne sont pas prêtes](./media/data-factory-monitor-manage-pipelines/upstream-slices-not-ready.png)

### Diagramme d'état de jeux de données
Quand vous avez déployé une fabrique de données et que la période d’activation des pipelines est valide, les tranches de données des jeux de données passent d’un état à un autre. Actuellement, l'état de la tranche suit le schéma d'état suivant :

![Schéma d'état](./media/data-factory-monitor-manage-pipelines/state-diagram.png)

Le flux de transition d'états des jeux de données de la fabrique de données implique les états suivants : Waiting-> In-Progress/In-Progress (Validating) -> Ready/Failed

Les tranches commencent par l’état **Waiting**. Elles attendent alors que les conditions préalables soient respectées avant l’exécution. Ensuite, l’activité est exécutée et la tranche passe à l’état **In-Progress**. L'exécution de l'activité peut alors réussir ou échouer. En fonction du résultat, la tranche passe à l’état **Ready** ou **Failed**.

L'utilisateur peut réinitialiser la tranche en vue de restaurer l’état **Ready** ou **Failed** pour passer à l’état **Waiting**. L'utilisateur peut également activer **SKIP** pour la tranche. Dans ce cas, l’activité ne sera pas exécutée et la tranche ne sera pas traitée.


## Gestion des pipelines
Vous pouvez gérer vos pipelines à l’aide d’Azure PowerShell. Par exemple, vous pouvez suspendre et reprendre les pipelines en exécutant les applets de commande Azure PowerShell.

### Suspension et reprise des pipelines
Vous pouvez suspendre des pipelines à l’aide de l’applet de commande PowerShell **Suspend-AzureRmDataFactoryPipeline**. Utile quand vous avez trouvé un problème de données et que vous ne voulez plus exécuter vos pipelines pour traiter les données tant que ce problème n’est pas résolu.

Par exemple : dans la capture d’écran ci-dessous, un problème a été identifié au niveau du **PartitionProductsUsagePipeline** dans la fabrique de données **productrecgamalbox1dev**. Nous souhaitons donc interrompre ce pipeline.

![Pipeline à interrompre](./media/data-factory-monitor-manage-pipelines/pipeline-to-be-suspended.png)

Exécutez la commande PowerShell suivante pour suspendre **PartitionProductsUsagePipeline**.

	Suspend-AzureRmDataFactoryPipeline [-ResourceGroupName] <String> [-DataFactoryName] <String> [-Name] <String>

Par exemple :

	Suspend-AzureRmDataFactoryPipeline -ResourceGroupName ADF -DataFactoryName productrecgamalbox1dev -Name PartitionProductsUsagePipeline 

Une fois que le problème a été résolu au niveau du **PartitionProductsUsagePipeline**, vous pouvez relancer le pipeline suspendu. Il suffit d’exécuter la commande PowerShell suivante.

	Resume-AzureRmDataFactoryPipeline [-ResourceGroupName] <String> [-DataFactoryName] <String> [-Name] <String>

Par exemple :

	Resume-AzureRmDataFactoryPipeline -ResourceGroupName ADF -DataFactoryName productrecgamalbox1dev -Name PartitionProductsUsagePipeline 


## Débogage de pipelines
Azure Data Factory offre des fonctionnalités exceptionnelles à travers le portail Azure Classic et Azure PowerShell pour déboguer et dépanner les pipelines.

### Recherche d’erreurs dans un pipeline
En cas d'échec d'exécution de l'activité dans un pipeline, le jeu de données généré par celui-ci est alors en état d'erreur. Vous pouvez déboguer et corriger les erreurs dans la Azure Data Factory à l'aide des mécanismes suivants.

#### Utilisez le portail Azure Classic pour déboguer une erreur :

1.	Cliquez sur **AVEC DES ERREURS** sur la mosaïque **JEUX DE DONNÉES** sur la page d’accueil de la fabrique de données.
	
	![Vignette de jeux de données avec erreur](./media/data-factory-monitor-manage-pipelines/datasets-tile-with-errors.png)
2.	Dans le panneau **JEUX DE DONNÉES AVEC ERREURS**, cliquez sur la table qui vous intéresse.

	![Panneau Jeux de données avec erreurs](./media/data-factory-monitor-manage-pipelines/datasets-with-errors-blade.png)
3.	Dans le panneau **TABLE**, cliquez sur la tranche qui pose problème, et dont l’**ÉTAT** est défini sur **Failed**.

	![Panneau de table avec tranche problématique](./media/data-factory-monitor-manage-pipelines/table-blade-with-error.png)
4.	Dans le panneau **TRANCHE DE DONNÉES**, cliquez sur l’exécution d’activité qui a échoué.
	
	![Panneau de données avec erreur](./media/data-factory-monitor-manage-pipelines/dataslice-with-error.png)
5.	Dans le panneau **DÉTAILS SUR L’EXÉCUTION D’ACTIVITÉ**, vous pouvez télécharger les fichiers associés au traitement HDInsight. Cliquez sur Télécharger pour que Status/stderr télécharge le fichier journal d'erreur qui contient les détails sur l'erreur.

	![Panneau de détails sur l’exécution d’activité](./media/data-factory-monitor-manage-pipelines/activity-run-details-with-error.png)

#### Utilisez PowerShell pour déboguer une erreur
1.	Lancez **Azure PowerShell**.
3.	Exécutez la commande **Get-AzureRmDataFactorySlice** pour voir les tranches et leur état. Une tranche dont l’état est **FAILED** devrait apparaître.

		Get-AzureRmDataFactorySlice [-ResourceGroupName] <String> [-DataFactoryName] <String> [-TableName] <String> [-StartDateTime] <DateTime> [[-EndDateTime] <DateTime> ] [-Profile <AzureProfile> ] [ <CommonParameters>]
	
	Par exemple :


		Get-AzureRmDataFactorySlice -ResourceGroupName ADF -DataFactoryName LogProcessingFactory -TableName EnrichedGameEventsTable -StartDateTime 2014-05-04 20:00:00

	Remplacez **StartDateTime** par la valeur de StartDateTime spécifiée pour Set-AzureRmDataFactoryPipelineActivePeriod.
4. Exécutez maintenant l’applet de commande **Get-AzureRmDataFactoryRun** pour obtenir des détails sur l’exécution de l’activité de la tranche.

		Get-AzureRmDataFactoryRun [-ResourceGroupName] <String> [-
		DataFactoryName] <String> [-TableName] <String> [-StartDateTime] 
		<DateTime> [-Profile <AzureProfile> ] [ <CommonParameters>]
	
	Par exemple :


		Get-AzureRmDataFactoryRun -ResourceGroupName ADF -DataFactoryName LogProcessingFactory -TableName EnrichedGameEventsTable -StartDateTime "5/5/2014 12:00:00 AM"

	La valeur de StartDateTime est l’heure de début de la tranche qui pose problème/l’erreur que vous avez notée à l’étape précédente. La valeur date-heure doit être entourée de guillemets doubles.
5. 	Vous devez voir la sortie avec les détails sur l'erreur (semblable à cela) :

		Id                  	: 841b77c9-d56c-48d1-99a3-8c16c3e77d39
		ResourceGroupName   	: ADF
		DataFactoryName     	: LogProcessingFactory3
		TableName           	: EnrichedGameEventsTable
		ProcessingStartTime 	: 10/10/2014 3:04:52 AM
		ProcessingEndTime   	: 10/10/2014 3:06:49 AM
		PercentComplete     	: 0
		DataSliceStart      	: 5/5/2014 12:00:00 AM
		DataSliceEnd        	: 5/6/2014 12:00:00 AM
		Status              	: FailedExecution
		Timestamp           	: 10/10/2014 3:04:52 AM
		RetryAttempt        	: 0
		Properties          	: {}
		ErrorMessage        	: Pig script failed with exit code '5'. See wasb://		adfjobs@spestore.blob.core.windows.net/PigQuery
		                        		Jobs/841b77c9-d56c-48d1-99a3-
					8c16c3e77d39/10_10_2014_03_04_53_277/Status/stderr' for
					more details.
		ActivityName        	: PigEnrichLogs
		PipelineName        	: EnrichGameLogsPipeline
		Type                	:
	
	
6. 	Vous pouvez exécuter l’applet de commande **Save-AzureRmDataFactoryLog** avec la valeur d’ID indiquée dans la sortie ci-dessus et télécharger les fichiers journaux à l’aide de l’option **-DownloadLogs** pour l’applet de commande.

	Save-AzureRmDataFactoryLog -ResourceGroupName "ADF" -DataFactoryName "LogProcessingFactory" -Id "841b77c9-d56c-48d1-99a3-8c16c3e77d39" -DownloadLogs -Output "C:\\Test"


## Réexécutez des échecs dans un pipeline

### Utiliser le portail Azure Classic

Quand vous dépannez et déboguez des erreurs dans un pipeline, vous pouvez réexécuter ces échecs en accédant à la tranche qui pose problème. Il suffit de cliquer sur le bouton **EXÉCUTER**, figurant sur la barre de commandes.

![Réexécuter une tranche de données ayant échoué](./media/data-factory-monitor-manage-pipelines/rerun-slice.png)

En cas d’échec de validation de la tranche à cause d’une erreur de stratégie (données indisponibles, par ex.), vous pouvez corriger l'erreur et relancer la validation en cliquant sur le bouton **VALIDER**, figurant sur la barre de commandes. ![Corriger les erreurs et valider](./media/data-factory-monitor-manage-pipelines/fix-error-and-validate.png)

### Utilisation de Microsoft Azure PowerShell

Vous pouvez réexécuter des échecs à l’aide de l’applet de commande Set-AzureRmDataFactorySliceStatus. Consultez la rubrique [Set-AzureRmDataFactorySliceStatus](https://msdn.microsoft.com/library/mt603522.aspx) pour la syntaxe et pour plus d’informations sur l’applet de commande.

**Exemple :** l’exemple suivant définit l’état de toutes les tranches de la table 'DAWikiAggregatedData' sur 'En attente' dans la fabrique de données Azure 'WikiADF'.

**Remarque :** UpdateType est défini sur UpstreamInPipeline. Concrètement, l’état « En attente » est défini pour chaque tranche de la table et toutes les tables (en amont) dépendantes qui sont utilisées en tant que tables d’entrée pour les activités du pipeline. Sinon, la valeur « Individual » pour ce paramètre est également possible.

	Set-AzureRmDataFactorySliceStatus -ResourceGroupName ADF -DataFactoryName WikiADF -TableName DAWikiAggregatedData -Status Waiting -UpdateType UpstreamInPipeline -StartDateTime 2014-05-21T16:00:00 -EndDateTime 2014-05-21T20:00:00


## Créez des alertes
Azure consigne les événements utilisateur lorsqu'une ressource Azure (par exemple, une fabrique de données) est créée, mise à jour ou supprimée. Vous pouvez créer des alertes relatives à ces événements. Data Factory vous permet de capturer différentes mesures et de créer des alertes associées. Nous vous recommandons d'utiliser des événements pour la surveillance en temps réel et des mesures pour en savoir plus sur l’utilisation au fil du temps.

### Alertes relatives à des événements
Les événements Azure fournissent des explications utiles sur ce qui se passe dans vos ressources Azure. Azure consigne les événements utilisateur lorsqu'une ressource Azure (par exemple, une fabrique de données) est créée, mise à jour ou supprimée. Lors de l'utilisation du service Azure Data Factory, les événements sont générés lorsque :

- Azure Data Factory est créé, mis à jour ou supprimé.
- Le traitement des données (appelé au démarrage) est démarré ou terminé.
- Lorsqu'un cluster HDInsight à la demande est créé et supprimé.

Vous pouvez créer des alertes relatives à ces événements utilisateur et les configurer pour envoyer des notifications par courrier électronique à l'administrateur et aux coadministrateurs de l'abonnement. De plus, vous pouvez spécifier des adresses de messagerie supplémentaires pour les utilisateurs devant recevoir des notifications par courrier électronique lorsque les conditions sont remplies. Cela est très utile lorsque vous souhaitez être averti en cas d’échec et que vous ne souhaitez pas surveiller en continu votre fabrique de données.

> [AZURE.NOTE] Le portail n'affiche pas les alertes sur les événements pour l'instant. Utilisez l’[Application de surveillance et gestion](data-factory-monitor-manage-app.md) pour afficher toutes les alertes.

#### Configuration d'une définition d'alerte :
Pour spécifier une définition d'alerte, vous devez créer un fichier JSON décrivant les opérations pour lesquelles vous souhaitez être alerté. Dans l'exemple ci-dessous, l'alerte envoie une notification par courrier électronique pour l’opération RunFinished. Pour être plus précis, une notification par courrier électronique est envoyée lorsqu'une exécution de la fabrique de données est terminée en ayant échoué (État = FailedExecution).

	{
	    "contentVersion": "1.0.0.0",
	     "$schema": "http://schema.management.azure.com/schemas/2014-04-01-preview/deploymentTemplate.json#",
	    "parameters": {},
	    "resources": 
	    [
	        {
	            "name": "ADFAlertsSlice",
	            "type": "microsoft.insights/alertrules",
	            "apiVersion": "2014-04-01",
	            "location": "East US",
	            "properties": 
	            {
	                "name": "ADFAlertsSlice",
	                "description": "One or more of the data slices for the Azure Data Factory has failed processing.",
	                "isEnabled": true,
	                "condition": 
	                {
	                    "odata.type": "Microsoft.Azure.Management.Insights.Models.ManagementEventRuleCondition",
	                    "dataSource": 
	                    {
	                        "odata.type": "Microsoft.Azure.Management.Insights.Models.RuleManagementEventDataSource",
	                        "operationName": "RunFinished",
	                        "status": "Failed",
	                        "subStatus": "FailedExecution"   
	                    }
	                },
	                "action": 
	                {
	                    "odata.type": "Microsoft.Azure.Management.Insights.Models.RuleEmailAction",
	                    "customEmails": [ "<your alias>@contoso.com" ]
	                }
	            }
	        }
	    ]
	}

Si vous ne voulez pas recevoir d’alerte relative à un échec spécifique, supprimez **subStatus** de la définition JSON précédente.

L'exemple ci-dessus définit l'alerte de toutes les fabriques de données de votre abonnement. Si vous souhaitez configurer l'alerte pour une fabrique de données particulière, vous pouvez spécifier la fabrique de données **resourceUri** dans le bloc **dataSource** comme ci-après :

	"resourceUri" : "/SUBSCRIPTIONS/<subscriptionId>/RESOURCEGROUPS/<resourceGroupName>/PROVIDERS/MICROSOFT.DATAFACTORY/DATAFACTORIES/<dataFactoryName>"

Le tableau suivant dresse la liste des opérations et des états (et états secondaires) disponibles.

Nom d’opération | État | État secondaire
-------------- | ------ | ----------
RunStarted | Démarré | Starting
RunFinished | Failed / Succeeded | FailedResourceAllocation<br/><br/>Succeeded<br/><br/>FailedExecution<br/><br/>TimedOut<br/><br/><Canceled<br/><br/>FailedValidation<br/><br/>Abandoned
OnDemandClusterCreateStarted | Démarré
OnDemandClusterCreateSuccessful | Succeeded
OnDemandClusterDeleted | Succeeded

Consultez [Créer une règle d’alerte](https://msdn.microsoft.com/library/azure/dn510366.aspx) pour plus d’informations sur les éléments JSON utilisés dans l’exemple ci-dessus.

#### Déploiement de l’alerte 
Pour déployer l’alerte, utilisez l’applet de commande Azure PowerShell : **New-AzureRmResourceGroupDeployment**, comme indiqué dans l’exemple suivant :

	New-AzureRmResourceGroupDeployment -ResourceGroupName adf -TemplateFile .\ADFAlertFailedSlice.json  

Une fois le déploiement du groupe de ressources réussi, les messages suivants s’affichent :

	VERBOSE: 7:00:48 PM - Template is valid.
	WARNING: 7:00:48 PM - The StorageAccountName parameter is no longer used and will be removed in a future release.
	Please update scripts to remove this parameter.
	VERBOSE: 7:00:49 PM - Create template deployment 'ADFAlertFailedSlice'.
	VERBOSE: 7:00:57 PM - Resource microsoft.insights/alertrules 'ADFAlertsSlice' provisioning status is succeeded
	
	DeploymentName    : ADFAlertFailedSlice
	ResourceGroupName : adf
	ProvisioningState : Succeeded
	Timestamp         : 10/11/2014 2:01:00 AM
	Mode              : Incremental
	TemplateLink      :
	Parameters        :
	Outputs           :

#### Récupération de la liste des déploiements de groupes de ressources Azure
Pour récupérer la liste des déploiements de groupes de ressources Azure, utilisez l’applet de commande : **Get-AzureRmResourceGroupDeployment**, comme indiqué dans l’exemple suivant :

	Get-AzureRmResourceGroupDeployment -ResourceGroupName adf
	
	DeploymentName    : ADFAlertFailedSlice
	ResourceGroupName : adf
	ProvisioningState : Succeeded
	Timestamp         : 10/11/2014 2:01:00 AM
	Mode              : Incremental
	TemplateLink      :
	Parameters        :
	Outputs           :


#### Résolution des problèmes des événements utilisateur


- Vous pouvez voir tous les événements générés après avoir cliqué sur la vignette **Opérations**. En outre, vous pouvez configurer des alertes relatives à n’importe quelle opération visible dans le panneau **Événements** :

	![Opérations](./media/data-factory-monitor-manage-pipelines/operations.png)


- Consultez l’article [Applets de commande Azure Insight](https://msdn.microsoft.com/library/mt282452.aspx) pour plus d’informations sur les applets de commande PowerShell que vous pouvez utiliser pour ajouter/obtenir/supprimer des alertes. Voici quelques exemples d’utilisation de l’applet de commande **Get-AlertRule** :


		PS C:\> get-alertrule -res $resourceGroup -n ADFAlertsSlice -det
			
				Properties :
		        Action      : Microsoft.Azure.Management.Insights.Models.RuleEmailAction
		        Condition   :
				DataSource :
				EventName             :
				Category              :
				Level                 :
				OperationName         : RunFinished
				ResourceGroupName     :
				ResourceProviderName  :
				ResourceId            :
				Status                : Failed
				SubStatus             : FailedExecution
				Claims                : Microsoft.Azure.Management.Insights.Models.RuleManagementEventClaimsDataSource
		        Condition  	:
				Description : One or more of the data slices for the Azure Data Factory has failed processing.
				Status      : Enabled
				Name:       : ADFAlertsSlice
				Tags       :
				$type          : Microsoft.WindowsAzure.Management.Common.Storage.CasePreservedDictionary, Microsoft.WindowsAzure.Management.Common.Storage
				Id: /subscriptions/<subscription ID>/resourceGroups/<resource group name>/providers/microsoft.insights/alertrules/ADFAlertsSlice
				Location   : West US
				Name       : ADFAlertsSlice
		
		PS C:\> Get-AlertRule -res $resourceGroup
	
				Properties : Microsoft.Azure.Management.Insights.Models.Rule
				Tags       : {[$type, Microsoft.WindowsAzure.Management.Common.Storage.CasePreservedDictionary, Microsoft.WindowsAzure.Management.Common.Storage]}
				Id         : /subscriptions/<subscription id>/resourceGroups/<resource group name>/providers/microsoft.insights/alertrules/FailedExecutionRunsWest0
				Location   : West US
				Name       : FailedExecutionRunsWest0
		
				Properties : Microsoft.Azure.Management.Insights.Models.Rule
				Tags       : {[$type, Microsoft.WindowsAzure.Management.Common.Storage.CasePreservedDictionary, Microsoft.WindowsAzure.Management.Common.Storage]}
				Id         : /subscriptions/<subscription id>/resourceGroups/<resource group name>/providers/microsoft.insights/alertrules/FailedExecutionRunsWest3
				Location   : West US
				Name       : FailedExecutionRunsWest3
	
		PS C:\> Get-AlertRule -res $resourceGroup -Name FailedExecutionRunsWest0
		
				Properties : Microsoft.Azure.Management.Insights.Models.Rule
				Tags       : {[$type, Microsoft.WindowsAzure.Management.Common.Storage.CasePreservedDictionary, Microsoft.WindowsAzure.Management.Common.Storage]}
				Id         : /subscriptions/<subscription id>/resourceGroups/<resource group name>/providers/microsoft.insights/alertrules/FailedExecutionRunsWest0
				Location   : West US
				Name       : FailedExecutionRunsWest0

	Exécutez les commandes get-help suivantes afinb d’afficher des détails et des exemples pour l'applet de commande Get-AlertRule.

		get-help Get-AlertRule -detailed 
		get-help Get-AlertRule -examples


- Si des événements de génération d'alertes apparaissent sur le panneau du portail mais que vous ne recevez aucune notification par e-mail, vérifiez que votre adresse e-mail est bien configurée de manière à recevoir des e-mails de la part d’expéditeurs externes. Les alertes par e-mail peuvent avoir été bloquées en raison de vos paramètres de messagerie.

### Alertes relatives à des mesures
Data Factory vous permet de capturer différentes mesures et de créer des alertes associées. Vous pouvez analyser et créer des alertes relatives à des mesures suivantes pour les tranches de votre fabrique de données.
 
- Exécutions échouées
- Exécutions réussies

Ces mesures sont très utiles et permettent aux utilisateurs d'obtenir une vue d'ensemble globale des exécutions réussies et échouées dans leur fabrique de données. Des mesures sont émises lors de chaque exécution de tranche de données. Toutes les heures, ces mesures sont regroupées et transférées dans votre compte de stockage. Par conséquent, pour activer les mesures, vous devrez configurer un compte de stockage.

#### Activation des mesures :
Pour activer les mesures, suivez le chemin suivant à partir du panneau Data Factory :

**Analyse** -> **Mesure** -> **Paramètres de diagnostic** -> **Diagnostic**

Dans le panneau **Diagnostic**, cliquez sur **Activé** et sélectionnez le compte de stockage. Enregistrez.

![Activer les mesures](./media/data-factory-monitor-manage-pipelines/enable-metrics.png)

Par la suite, vous devrez peut-être attendre une heure maximum avant que les mesures ne soient visibles sur le panneau d’analyse. En effet, le regroupement des mesures s’effectue une fois par heure.


### Configuration d'une alerte relative à des mesures :

Pour configurer des alertes relatives à des mesures, cliquez sur les éléments suivants à partir du panneau Data Factory : **Analyse** -> **Mesure** -> **Ajouter une alerte** -> **Ajouter une règle d’alerte**.

Indiquez les détails de la règle d’alerte, précisez les adresses de messagerie et cliquez sur **OK**.


![Configurer des alertes relatives à des mesures](./media/data-factory-monitor-manage-pipelines/setting-up-alerts-on-metrics.png)

Une fois terminé, une nouvelle règle d'alerte activée devrait apparaître sur la vignette de règles d’alerte, comme suit :

![Règles d'alerte activées](./media/data-factory-monitor-manage-pipelines/alert-rule-enabled.png)

Félicitations ! Vous avez configuré votre première alerte relative à des mesures. Dorénavant, des notifications s’afficheront chaque fois qu’une règle d’alerte est signalée, dans la fenêtre d’heure donnée.

### Notifications d'alerte :
Une fois que la règle de configuration correspond à la condition, un e-mail d’alerte vous est envoyé. Une fois que le problème est résolu et que la condition d'alerte est caduque, un message signalant que l’alerte a été résolue vous est envoyé.

Ce comportement se distingue des événements donnant lieu à l’envoi d’une notification dès qu’un échec correspondant à une règle d’alerte a lieu.

### Déploiement d'alertes à l'aide de PowerShell
Vous pouvez déployer des alertes relatives à des mesures de la même façon que vous le faites pour les événements.

**Définition d’alerte :**

	{
	    "contentVersion" : "1.0.0.0",
	    "$schema" : "http://schema.management.azure.com/schemas/2014-04-01-preview/deploymentTemplate.json#",
	    "parameters" : {},
	    "resources" : [
		{
	            "name" : "FailedRunsGreaterThan5",
	            "type" : "microsoft.insights/alertrules",
	            "apiVersion" : "2014-04-01",
	            "location" : "East US",
	            "properties" : {
	                "name" : "FailedRunsGreaterThan5",
	                "description" : "Failed Runs greater than 5",
	                "isEnabled" : true,
	                "condition" : {
	                    "$type" : "Microsoft.WindowsAzure.Management.Monitoring.Alerts.Models.ThresholdRuleCondition, Microsoft.WindowsAzure.Management.Mon.Client",
	                    "odata.type" : "Microsoft.Azure.Management.Insights.Models.ThresholdRuleCondition",
	                    "dataSource" : {
	                        "$type" : "Microsoft.WindowsAzure.Management.Monitoring.Alerts.Models.RuleMetricDataSource, Microsoft.WindowsAzure.Management.Mon.Client",
	                        "odata.type" : "Microsoft.Azure.Management.Insights.Models.RuleMetricDataSource",
	                        "resourceUri" : "/SUBSCRIPTIONS/<subscriptionId>/RESOURCEGROUPS/<resourceGroupName
	>/PROVIDERS/MICROSOFT.DATAFACTORY/DATAFACTORIES/<dataFactoryName>",
	                        "metricName" : "FailedRuns"
	                    },
	                    "threshold" : 5.0,
	                    "windowSize" : "PT3H",
	                    "timeAggregation" : "Total"
	                },
	                "action" : {
	                    "$type" : "Microsoft.WindowsAzure.Management.Monitoring.Alerts.Models.RuleEmailAction, Microsoft.WindowsAzure.Management.Mon.Client",
	                    "odata.type" : "Microsoft.Azure.Management.Insights.Models.RuleEmailAction",
	                    "customEmails" : ["abhinav.gpt@live.com"]
	                }
	            }
	        }
	    ]
	}
 
Remplacez les valeurs de subscriptionId, resourceGroupName et dataFactoryName figurant dans l'exemple ci-dessus par des valeurs appropriées.

*metricName* prend désormais en charge 2 valeurs :
- FailedRuns
- SuccessfulRuns

**Déploiement de l’alerte :**

Pour déployer l’alerte, utilisez l’applet de commande Azure PowerShell : **New-AzureRmResourceGroupDeployment**, comme indiqué dans l’exemple suivant :

	New-AzureRmResourceGroupDeployment -ResourceGroupName adf -TemplateFile .\FailedRunsGreaterThan5.json

Le message suivant devrait s’afficher après la réussite du déploiement :

	VERBOSE: 12:52:47 PM - Template is valid.
	VERBOSE: 12:52:48 PM - Create template deployment 'FailedRunsGreaterThan5'.
	VERBOSE: 12:52:55 PM - Resource microsoft.insights/alertrules 'FailedRunsGreaterThan5' provisioning status is succeeded
	
	
	DeploymentName    : FailedRunsGreaterThan5
	ResourceGroupName : adf
	ProvisioningState : Succeeded
	Timestamp         : 7/27/2015 7:52:56 PM
	Mode              : Incremental
	TemplateLink      :
	Parameters        :
	Outputs           


Vous pouvez également utiliser l’applet de commande **Add-AlertRule** pour déployer une règle d’alerte. Consultez la rubrique [Add-AlertRule](https://msdn.microsoft.com/library/mt282468.aspx) pour plus d’informations et des exemples.

## Déplacer la fabrique de données vers un autre groupe de ressources ou abonnement
Vous pouvez déplacer une fabrique de données vers un autre groupe de ressources ou abonnement à l'aide du bouton de la barre de commandes **Déplacer** situé sur la page d'accueil de votre fabrique de données.

![Déplacer la fabrique de données](./media/data-factory-monitor-manage-pipelines/MoveDataFactory.png)

Vous pouvez également déplacer toutes les ressources associées (notamment les alertes associées à la fabrique de données) en même temps que la fabrique de données.

![Boîte de dialogue Déplacer des ressources](./media/data-factory-monitor-manage-pipelines/MoveResources.png)

<!------HONumber=AcomDC_0309_2016-->