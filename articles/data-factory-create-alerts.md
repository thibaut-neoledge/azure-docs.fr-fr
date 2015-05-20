<properties 
	pageTitle="Création d’alertes pour les événements Azure Data Factory" 
	description="Découvrez comment créer des alertes sur des événements déclenchés par Azure pour les opérations de fabrication de données." 
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

# Création d'alertes pour les événements Azure
Les événements Azure fournissent des explications utiles sur ce qui se passe dans vos ressources Azure. Azure consigne les événements utilisateur lorsqu'une ressource Azure \(par exemple, une fabrique de données\) est créée, mise à jour ou supprimée. Lors de l'utilisation du service Azure Data Factory, les événements sont générés lorsque :
 
1.	Azure Data Factory est créé, mis à jour ou supprimé.
2.	Le traitement des données \(appelé au démarrage\) est démarré ou terminé.
3.	Lorsqu'un cluster HDInsight à la demande est créé et supprimé.

Vous pouvez créer des alertes relatives à ces événements utilisateur et les configurer pour envoyer des notifications par courrier électronique à l'administrateur et aux coadministrateurs de l'abonnement. De plus, vous pouvez spécifier des adresses de messagerie supplémentaires pour les utilisateurs devant recevoir des notifications par courrier électronique lorsque les conditions sont remplies.

## Spécification d'une définition d'alerte
Pour spécifier une définition d'alerte, vous devez créer un fichier JSON décrivant les opérations pour lesquelles vous souhaitez être alerté. Dans l'exemple ci-dessous, l'alerte envoie une notification par courrier électronique pour l’opération **RunFinished**. Pour être plus précis, une notification par courrier électronique est envoyée lorsqu'une exécution de la fabrique de données est terminée en ayant échoué \(État = FailedExecution\).

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

Pour obtenir la liste des opérations et états \(et états secondaires\), consultez la section [Opérations et états disponibles](#AvailableOperationsStatuses).

## Déploiement de l’alerte
Pour déployer l'alerte, utilisez l'applet de commande Azure PowerShell : **New-AzureResourceGroupDeployment**, comme indiqué dans l'exemple suivant :

	New-AzureResourceGroupDeployment -ResourceGroupName adf 	-TemplateFile .\ADFAlertFailedSlice.json -StorageAccountName testmetricsabc

L’élément StorageAccountName spécifie le compte de stockage utilisé pour stocker le fichier JSON d'alerte déployé.

Une fois le déploiement du groupe de ressources réussi, les messages suivants s’affichent :
	
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

## Récupération de la liste des déploiements de groupes de ressources Azure
Pour récupérer la liste des déploiements de groupes de ressources Azure, utilisez l'applet de commande : **Get-AzureResourceGroupDeployment**, comme indiqué dans l'exemple suivant :
	
	Get-AzureResourceGroupDeployment -ResourceGroupName adf
	
	DeploymentName    : ADFAlertFailedSlice
	ResourceGroupName : adf
	ProvisioningState : Succeeded
	Timestamp         : 10/11/2014 2:01:00 AM
	Mode              : Incremental
	TemplateLink      :
	Parameters        :
	Outputs           :

## <a name="AvailableOperationsStatuses"></a>Noms d'opération et valeurs d'états disponibles

<table>
<th align="left">Nom d’opération</th>
<th align="left">État</th>
<th align="left">État secondaire</th>

<tr>
<td>RunStarted</td>
<td>Démarré</td>
<td>Starting</td>
</tr>

<tr>
<td>RunFinished</td>
<td>Failed / Succeeded</td>
<td>
	<p>FailedResourceAllocation </p>
	<p>Succeeded</p>
	<p>FailedExecution</p>
	<p>TimedOut</p>
	<p>Canceled</p>
	<p>FailedValidation</p>
	<p>Abandoned</p>
</td>
</tr>

<tr>
<td>SliceOnTime</td>
<td>In Progress</td>
<td>Ontime</td>
</tr>

<tr>
<td>SliceDelayed</td>
<td>In Progress</td>
<td>Late</td>
</tr>

<tr>
<td>CreateTablePublication</td>
<td>Succeeded</td>
<td></td>
</tr>

<tr>
<td>UpdateTablePublication</td>
<td>Succeeded</td>
<td></td>
</tr>

<tr>
<td>RemoveTablePublication</td>
<td>Succeeded</td>
<td></td>
</tr>

<tr>
<td>OnDemandClusterCreateStarted</td>
<td>Démarré</td>
<td></td>
</tr>

<tr>
<td>OnDemandClusterCreateSuccessful</td>
<td>Succeeded</td>
<td></td>
</tr>

<tr>
<td>OnDemandClusterDeleted</td>
<td>Succeeded</td>
<td></td>
</tr>

</table>

## Résolution des problèmes des événements utilisateur
Exécutez la commande suivante pour afficher les événements générés :

	Get-AzureResourceGroupLog –Name $ResourceGroup -All | Where-Object EventSource -eq "Microsoft.DataFactory"

<!--HONumber=54-->