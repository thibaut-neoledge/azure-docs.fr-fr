---
title: "Surveillance et gestion des pipelines d’Azure Data Factory"
description: "Découvrez comment utiliser le portail Azure et Azure PowerShell pour analyser et gérer les fabriques de données et les pipelines Azure que vous avez créés."
services: data-factory
documentationcenter: 
author: spelluru
manager: jhubbard
editor: monicar
ms.assetid: 9b0fdc59-5bbe-44d1-9ebc-8be14d44def9
ms.service: data-factory
ms.workload: data-services
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 12/05/2016
ms.author: spelluru
translationtype: Human Translation
ms.sourcegitcommit: 6a816e56400abe281b0422abbcd3415f3762a96e
ms.openlocfilehash: 43b828bad5d21fe0f452aa23bbdf6fff758525f2


---
# <a name="monitor-and-manage-azure-data-factory-pipelines"></a>Surveillance et gestion des pipelines d’Azure Data Factory
> [!div class="op_single_selector"]
> * [Utilisation du portail Azure/d’Azure PowerShell](data-factory-monitor-manage-pipelines.md)
> * [Utilisation de l’application de surveillance et gestion](data-factory-monitor-manage-app.md)
> 
> 

Le service Data Factory offre une vision fiable et complète de vos services de stockage, de traitement et de déplacement des données. Le service fournit un tableau de bord d’analyse à partir duquel vous pouvez effectuer les tâches suivantes : 

* Évaluer rapidement l’intégrité du pipeline de données de bout en bout.
* Identifier les problèmes et prendre des mesures correctives si nécessaire. 
* Suivre le lignage des données. 
* Les relations entre relations de suivi dans toutes vos sources.
* Consultez une comptabilité historique complète de l'exécution du travail, de l'intégrité du système et des dépendances.

Dans cet article, vous apprendrez à surveiller, gérer et déboguer vos pipelines. Vous obtiendrez également des informations sur la façon de créer des alertes et être averti en cas d’échec.

## <a name="understand-pipelines-and-activity-states"></a>Présentation des pipelines et des états d’activité
À l’aide du portail Azure, vous pouvez :

* Afficher votre fabrique de données sous forme de diagramme
* Afficher les activités à l'intérieur d'un pipeline
* Afficher des jeux de données d’entrée et de sortie
* et bien plus. 

Cette section montre également comment une tranche de données passe d'un état à un autre.   

### <a name="navigate-to-your-data-factory"></a>Accédez à votre fabrique de données
1. Connectez-vous au [portail Azure](https://portal.azure.com).
2. Cliquer sur **Fabriques de données** dans le menu de gauche. Si vous ne voyez pas cette option, cliquez sur **Autres services >**, puis sur **Fabriques de données** dans la catégorie **INTELLIGENCE + ANALYSE**. 
   
   ![Parcourir tout -> Fabriques de données](./media/data-factory-monitor-manage-pipelines/browseall-data-factories.png)
   
   Vous devez voir toutes les fabriques de données dans le panneau **Fabriques de données** . 
3. Dans le panneau Fabriques de données, sélectionnez la fabrique de données que vous intéresse.
   
    ![sélectionner une fabrique de données](./media/data-factory-monitor-manage-pipelines/select-data-factory.png)  
4. Vous devriez voir la page d’accueil (panneau **Data factory**) pour la fabrique de données.
   
   ![Panneau Data Factory](./media/data-factory-monitor-manage-pipelines/data-factory-blade.png)

#### <a name="diagram-view-of-your-data-factory"></a>Vue schématique de votre fabrique de données
La vue schématique d'une fabrique de données est un point unique de surveillance et de gestion de la fabrique de données et de ses ressources.

Cliquez sur **Schématique** sur la page d'accueil de la fabrique de données ci-dessus pour afficher la vue schématique de votre fabrique de données.

![Vue schématique](./media/data-factory-monitor-manage-pipelines/diagram-view.png)

Vous pouvez faire un zoom avant, un zoom arrière, un zoom à 100 %, un zoom pour ajuster l’affichage à la taille de l’écran, figer l’affichage schématique, et positionner automatiquement les pipelines et les tables. Vous pouvez également afficher le lignage (mise en surbrillance des éléments en amont et en aval des éléments sélectionnés).

### <a name="activities-inside-a-pipeline"></a>Activités à l'intérieur d'un pipeline
1. Cliquez avec le bouton droit sur le pipeline de votre choix puis cliquez sur **Ouvrir le pipeline** pour faire apparaître toutes les activités dans le pipeline, ainsi que les jeux de données d’entrée et de sortie des activités. Cette fonctionnalité est utile quand votre pipeline comprend plusieurs activités et que vous souhaitez comprendre le lignage opérationnel d'un seul pipeline.
   
    ![Menu Ouvrir un pipeline](./media/data-factory-monitor-manage-pipelines/open-pipeline-menu.png)     
2. Dans l'exemple suivant, deux activités dans le pipeline s’affichent avec leurs entrées et leurs sorties. Les activités intitulées **JoinData** du type Activité Hive HDInsight, et **EgressDataAzure** du type Copier l’activité figurent dans cet exemple de pipeline. 
   
    ![Activités à l'intérieur d'un pipeline](./media/data-factory-monitor-manage-pipelines/activities-inside-pipeline.png) 
3. Vous pouvez revenir à la page d'accueil de Data Factory. Pour ce faire, cliquez sur le lien Data Factory dans l'arborescence de navigation située dans le coin supérieur gauche.
   
    ![Revenir à la fabrique de données](./media/data-factory-monitor-manage-pipelines/navigate-back-to-data-factory.png)

### <a name="view-state-of-each-activity-inside-a-pipeline"></a>Affichez l’état de chaque activité à l'intérieur d'un pipeline
Vous pouvez afficher l'état actuel d'une activité en consultant l'état de l'un des jeux de données générés par l'activité. 

Par exemple : dans l’exemple suivant, l’activité **BlobPartitionHiveActivity** a été correctement exécutée et a produit un jeu de données nommé **PartitionedProductsUsageTable**, dont l’état est **Prêt**.

![État du pipeline](./media/data-factory-monitor-manage-pipelines/state-of-pipeline.png)

Double-cliquez sur **PartitionedProductsUsageTable** dans la vue schématique pour afficher toutes les tranches produites par différentes exécutions d’activité à l'intérieur d'un pipeline. Comme vous pouvez le constater, l’activité **BlobPartitionHiveActivity** s’est déroulée correctement sur les huit derniers mois et a produit les tranches dont l’état est **Prêt**.

Voici la liste des différents états possibles des tranches d’un jeu de données d’une fabrique de données :

<table>
<tr>
    <th align="left">State</th><th align="left">Sous-état</th><th align="left">Description</th>
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
<td>Retry</td><td>L’exécution de l’activité est retentée.</td>
</tr>
<tr>
<td>Validation</td><td>La validation n’a pas encore démarré.</td>
</tr>
<tr>
<td>ValidationRetry</td><td>Attente d’une nouvelle tentative de validation.</td>
</tr>
<tr>
<tr
<td rowspan="2">InProgress</td><td>Validation</td><td>Validation en cours.</td>
</tr>
<td>-</td>
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
<td>-</td><td>Impossible de générer ou de valider la tranche.</td>
</tr>
<td>Ready</td><td>-</td><td>La tranche est prête à être consommée.</td>
</tr>
<tr>
<td>Ignoré</td><td>Aucun</td><td>La tranche n’est pas traitée.</td>
</tr>
<tr>
<td>Aucun</td><td>-</td><td>Tranche qui a été réinitialisée alors qu’elle existait avec un état différent.</td>
</tr>
</table>



Vous pouvez afficher les détails relatifs à une tranche de données en cliquant sur une entrée de tranche dans le panneau **Tranches mises à jour récemment** .

![Détails de la tranche](./media/data-factory-monitor-manage-pipelines/slice-details.png)

Si la tranche a été exécutée plusieurs fois, plusieurs lignes s’affichent dans la liste **Exécutions d’activité** . Vous pouvez afficher des détails sur une exécution d’activité en cliquant sur l’entrée d’exécution dans la liste **Exécutions de l’activité**. Tous les fichiers journaux, ainsi que le message d’erreur associé, le cas échéant, s’affichent. Cette fonctionnalité est utile et pour cause. Vous visualisez et déboguez les journaux sans le souci de quitter votre fabrique de données.

![Détails de l'exécution d'activité](./media/data-factory-monitor-manage-pipelines/activity-run-details.png)

Si la tranche n’est pas dans l’état **Prêt**, vous pouvez voir dans la liste **Tranches en amont qui ne sont pas prêtes** les tranches qui ne sont pas prêtes en amont et qui empêchent l’exécution de la tranche actuelle. Cette fonctionnalité est utile lorsque votre tranche présente l’état **Waiting** et que vous voulez connaître les dépendances en amont à l’origine de cette attente.

![Tranches en amont qui ne sont pas prêtes](./media/data-factory-monitor-manage-pipelines/upstream-slices-not-ready.png)

### <a name="dataset-state-diagram"></a>Diagramme d'état de jeux de données
Quand vous avez déployé une fabrique de données et que la période d’activation des pipelines est valide, les tranches de données des jeux de données passent d’un état à un autre. Actuellement, l'état de la tranche suit le schéma d'état suivant :

![Schéma d'état](./media/data-factory-monitor-manage-pipelines/state-diagram.png)

Flux de transition des états de jeu de données dans la fabrique de données : En attente -> En cours/En cours (Validation) -> Prête/Échec

Les tranches commencent dans l’état **En attente**. Elles attendent alors que les conditions préalables soient respectées avant l’exécution. Ensuite, l’exécution de l’activité commence, et la tranche passe à l’état **En cours**. L’exécution de l’activité peut réussir ou échouer. Selon le résultat de l’exécution, l’état de la tranche est **Prête** ou **Échec**. 

Vous pouvez réinitialiser la tranche pour revenir de l’état **Prête** ou **Échec** à l’état **En attente**. Vous pouvez également définir l’état de la tranche sur **Ignorer** pour empêcher l’exécution de l’activité et ne pas traiter la tranche.

## <a name="manage-pipelines"></a>Gestion des pipelines
Vous pouvez gérer vos pipelines à l’aide d’Azure PowerShell. Par exemple, vous pouvez suspendre et reprendre les pipelines en exécutant les applets de commande Azure PowerShell. 

### <a name="pause-and-resume-pipelines"></a>Suspension et reprise des pipelines
Vous pouvez suspendre des pipelines à l’aide de l’applet de commande PowerShell **Suspend-AzureRmDataFactoryPipeline** . Cette applet de commande est utile lorsque vous ne voulez pas exécuter vos pipelines jusqu'à ce qu’un problème est résolu.

Par exemple : dans la capture d’écran suivante, un problème a été identifié au niveau du pipeline **PartitionProductsUsagePipeline** dans la fabrique de données **productrecgamalbox1dev**, et nous souhaitons suspendre l’exécution du pipeline.

![Pipeline à interrompre](./media/data-factory-monitor-manage-pipelines/pipeline-to-be-suspended.png)

Pour suspendre un pipeline, exécutez la commande PowerShell suivante :

```powershell
Suspend-AzureRmDataFactoryPipeline [-ResourceGroupName] <String> [-DataFactoryName] <String> [-Name] <String>
```
Par exemple :

```powershell
Suspend-AzureRmDataFactoryPipeline -ResourceGroupName ADF -DataFactoryName productrecgamalbox1dev -Name PartitionProductsUsagePipeline 
```

Une fois le problème résolu au niveau du pipeline **PartitionProductsUsagePipeline**, vous pouvez relancer le pipeline suspendu en exécutant la commande PowerShell suivante :

```powershell
Resume-AzureRmDataFactoryPipeline [-ResourceGroupName] <String> [-DataFactoryName] <String> [-Name] <String>
```
Par exemple :

```powershell
Resume-AzureRmDataFactoryPipeline -ResourceGroupName ADF -DataFactoryName productrecgamalbox1dev -Name PartitionProductsUsagePipeline 
```
## <a name="debug-pipelines"></a>Débogage de pipelines
Azure Data Factory offre des fonctionnalités exceptionnelles à travers le portail Azure et Azure PowerShell pour déboguer et dépanner les pipelines.

### <a name="find-errors-in-a-pipeline"></a>Recherche d’erreurs dans un pipeline
En cas d'échec d'exécution de l'activité dans un pipeline, le jeu de données généré par celui-ci est alors en état d'erreur. Vous pouvez déboguer et corriger les erreurs dans la Azure Data Factory à l'aide des mécanismes suivants.

#### <a name="use-azure-portal-to-debug-an-error"></a>Utilisez le portail Azure pour déboguer une erreur :
1. Dans le panneau **TABLE**, cliquez sur la tranche qui pose problème, dont l’**ÉTAT** est défini sur **Échec**.
   
   ![Panneau de table avec tranche problématique](./media/data-factory-monitor-manage-pipelines/table-blade-with-error.png)
2. Dans le panneau **TRANCHE DE DONNÉES** , cliquez sur l’exécution d’activité qui a échoué.
   
   ![Panneau de données avec erreur](./media/data-factory-monitor-manage-pipelines/dataslice-with-error.png)
3. Dans le panneau **DÉTAILS SUR L’EXÉCUTION D’ACTIVITÉ** , vous pouvez télécharger les fichiers associés au traitement HDInsight. Cliquez sur Télécharger pour que Status/stderr télécharge le fichier journal d'erreur qui contient les détails sur l'erreur.
   
   ![Panneau de détails sur l’exécution d’activité](./media/data-factory-monitor-manage-pipelines/activity-run-details-with-error.png)     

#### <a name="use-the-powershell-to-debug-an-error"></a>Utilisez PowerShell pour déboguer une erreur
1. Lancez **Azure PowerShell**.
2. Exécutez la commande **Get-AzureRmDataFactorySlice** pour voir les tranches et leur état. Une tranche dont l’état est **FAILED**devrait apparaître.        

    ```powershell   
    Get-AzureRmDataFactorySlice [-ResourceGroupName] <String> [-DataFactoryName] <String> [-DatasetName] <String> [-StartDateTime] <DateTime> [[-EndDateTime] <DateTime> ] [-Profile <AzureProfile> ] [ <CommonParameters>]
    ```   
   Par exemple :

    ```powershell   
    Get-AzureRmDataFactorySlice -ResourceGroupName ADF -DataFactoryName LogProcessingFactory -DatasetName EnrichedGameEventsTable -StartDateTime 2014-05-04 20:00:00
    ```
   
   Remplacez **StartDateTime** par la valeur de StartDateTime spécifiée pour Set-AzureRmDataFactoryPipelineActivePeriod.
3. Exécutez maintenant l’applet de commande **Get-AzureRmDataFactoryRun** pour obtenir des détails sur l’exécution de l’activité de la tranche.

    ```powershell   
    Get-AzureRmDataFactoryRun [-ResourceGroupName] <String> [-DataFactoryName] <String> [-DatasetName] <String> [-StartDateTime] 
    <DateTime> [-Profile <AzureProfile> ] [ <CommonParameters>]
    ```
  
    Par exemple :

    ```powershell   
    Get-AzureRmDataFactoryRun -ResourceGroupName ADF -DataFactoryName LogProcessingFactory -DatasetName EnrichedGameEventsTable -StartDateTime "5/5/2014 12:00:00 AM"
    ```
   
    La valeur de StartDateTime est l’heure de début de la tranche qui pose problème/l’erreur que vous avez notée à l’étape précédente. La valeur date-heure doit être entourée de guillemets doubles.
4. Vous devez voir la sortie avec les détails sur l'erreur (semblable à cela) :

    ```   
    Id                      : 841b77c9-d56c-48d1-99a3-8c16c3e77d39
    ResourceGroupName       : ADF
    DataFactoryName         : LogProcessingFactory3
    DatasetName               : EnrichedGameEventsTable
    ProcessingStartTime     : 10/10/2014 3:04:52 AM
    ProcessingEndTime       : 10/10/2014 3:06:49 AM
    PercentComplete         : 0
    DataSliceStart          : 5/5/2014 12:00:00 AM
    DataSliceEnd            : 5/6/2014 12:00:00 AM
    Status                  : FailedExecution
    Timestamp               : 10/10/2014 3:04:52 AM
    RetryAttempt            : 0
    Properties              : {}
    ErrorMessage            : Pig script failed with exit code '5'. See wasb://        adfjobs@spestore.blob.core.windows.net/PigQuery
                                    Jobs/841b77c9-d56c-48d1-99a3-
                8c16c3e77d39/10_10_2014_03_04_53_277/Status/stderr' for
                more details.
    ActivityName            : PigEnrichLogs
    PipelineName            : EnrichGameLogsPipeline
    Type                    :
    ```
5. Vous pouvez exécuter l’applet de commande **Save-AzureRmDataFactoryLog** avec la valeur d’ID indiquée dans la sortie, et télécharger les fichiers journaux en utilisant le paramètre **-DownloadLogsoption** pour l’applet de commande.
   
    ```powershell
    Save-AzureRmDataFactoryLog -ResourceGroupName "ADF" -DataFactoryName "LogProcessingFactory" -Id "841b77c9-d56c-48d1-99a3-8c16c3e77d39" -DownloadLogs -Output "C:\Test"
    ```

## <a name="rerun-failures-in-a-pipeline"></a>Réexécuter des échecs dans un pipeline
### <a name="using-azure-portal"></a>En passant par le portail Azure
Quand vous dépannez et déboguez des échecs dans un pipeline, vous pouvez réexécuter ceux-ci en accédant à la tranche qui pose problème, puis en cliquant sur le bouton **Exécuter** dans la barre de commandes.

![Réexécuter une tranche de données ayant échoué](./media/data-factory-monitor-manage-pipelines/rerun-slice.png)

En cas d’échec de validation de la tranche à cause d’une erreur de stratégie (données indisponibles, par ex.), vous pouvez corriger l'erreur et relancer la validation en cliquant sur le bouton **VALIDER** , figurant sur la barre de commandes.
![Corriger les erreurs et valider](./media/data-factory-monitor-manage-pipelines/fix-error-and-validate.png)

### <a name="using-azure-powershell"></a>Utilisation de Microsoft Azure PowerShell
Vous pouvez réexécuter des échecs à l’aide de l’applet de commande Set-AzureRmDataFactorySliceStatus. Consultez la rubrique [Set-AzureRmDataFactorySliceStatus](https://msdn.microsoft.com/library/mt603522.aspx) pour la syntaxe et pour plus d’informations sur l’applet de commande. 

**Exemple :** l’exemple suivant définit l’état de toutes les tranches de la table 'DAWikiAggregatedData' sur 'En attente' dans la fabrique de données Azure 'WikiADF'.

UpdateType est défini sur UpstreamInPipeline. Concrètement, l’état « En attente » est défini pour chaque tranche de la table et toutes les tables (en amont) dépendantes. Sinon, la valeur « Individual » pour ce paramètre est également possible.

```powershell
Set-AzureRmDataFactorySliceStatus -ResourceGroupName ADF -DataFactoryName WikiADF -DatasetName DAWikiAggregatedData -Status Waiting -UpdateType UpstreamInPipeline -StartDateTime 2014-05-21T16:00:00 -EndDateTime 2014-05-21T20:00:00
```

## <a name="create-alerts"></a>Créez des alertes
Azure consigne les événements utilisateur lorsqu'une ressource Azure (par exemple, une fabrique de données) est créée, mise à jour ou supprimée. Vous pouvez créer des alertes relatives à ces événements. Data Factory vous permet de capturer différentes mesures et de créer des alertes associées. Nous vous recommandons d’utiliser les événements pour obtenir une surveillance en temps réel et des analyses à des fins d’historique. 

### <a name="alerts-on-events"></a>Alertes relatives à des événements
Les événements Azure fournissent des explications utiles sur ce qui se passe dans vos ressources Azure. Azure consigne les événements utilisateur lorsqu'une ressource Azure (par exemple, une fabrique de données) est créée, mise à jour ou supprimée. Lors de l'utilisation du service Azure Data Factory, les événements sont générés lorsque :

* Azure Data Factory est créé, mis à jour ou supprimé.
* Le traitement des données (appelé au démarrage) est démarré ou terminé.
* Un cluster HDInsight à la demande est créé et supprimé.

Vous pouvez créer des alertes relatives à ces événements utilisateur et les configurer pour envoyer des notifications par courrier électronique à l'administrateur et aux coadministrateurs de l'abonnement. De plus, vous pouvez spécifier des adresses de messagerie supplémentaires pour les utilisateurs devant recevoir des notifications par courrier électronique lorsque les conditions sont remplies. Cette fonctionnalité est très utile lorsque vous souhaitez être averti en cas d’échec et que vous ne souhaitez pas surveiller en continu votre fabrique de données.

> [!NOTE]
> Actuellement, le portail n'affiche pas les alertes sur les événements. Utilisez [l’Application de surveillance et gestion](data-factory-monitor-manage-app.md) pour afficher toutes les alertes.
> 
> 

#### <a name="specifying-an-alert-definition"></a>Configuration d'une définition d'alerte :
Pour spécifier une définition d'alerte, vous devez créer un fichier JSON décrivant les opérations pour lesquelles vous souhaitez être alerté. Dans l'exemple suivant, l'alerte envoie une notification par courrier électronique pour l’opération RunFinished. Pour être plus précis, une notification par courrier électronique est envoyée lorsqu'une exécution de la fabrique de données est terminée en ayant échoué (État = FailedExecution).

```JSON
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
```

Si vous ne voulez pas recevoir d’alerte relative à un échec spécifique, supprimez **subStatus** de la définition JSON ci-dessus.

L'exemple ci-dessus définit l'alerte de toutes les fabriques de données de votre abonnement. Si vous souhaitez configurer l’alerte pour une fabrique de données particulière, vous pouvez spécifier la fabrique de données **resourceUri** dans le bloc **dataSource** :

```JSON
"resourceUri" : "/SUBSCRIPTIONS/<subscriptionId>/RESOURCEGROUPS/<resourceGroupName>/PROVIDERS/MICROSOFT.DATAFACTORY/DATAFACTORIES/<dataFactoryName>"
```

Le tableau suivant dresse la liste des opérations et des états (et états secondaires) disponibles.

| Nom d’opération | État | État secondaire |
| --- | --- | --- |
| RunStarted |Démarré |Starting |
| RunFinished |Failed / Succeeded |FailedResourceAllocation<br/><br/>Succeeded<br/><br/>FailedExecution<br/><br/>TimedOut<br/><br/><Canceled<br/><br/>FailedValidation<br/><br/>Abandonné |
| OnDemandClusterCreateStarted |Démarré | |
| OnDemandClusterCreateSuccessful |Réussi | |
| OnDemandClusterDeleted |Réussi | |

Consultez [Créer une règle d’alerte](https://msdn.microsoft.com/library/azure/dn510366.aspx) pour plus d’informations sur les éléments JSON utilisés dans l’exemple. 

#### <a name="deploying-the-alert"></a>Déploiement de l’alerte
Pour déployer l’alerte, utilisez l’applet de commande Azure PowerShell **New-AzureRmResourceGroupDeployment**, comme indiqué dans l’exemple suivant :

```powershell
New-AzureRmResourceGroupDeployment -ResourceGroupName adf -TemplateFile .\ADFAlertFailedSlice.json  
```

Une fois le déploiement du groupe de ressources réussi, les messages suivants s’affichent :

```
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
```

> [!NOTE]
> Vous pouvez utiliser l’API REST [Créer une règle d’alerte](https://msdn.microsoft.com/library/azure/dn510366.aspx) pour créer une règle d’alerte. La charge utile JSON est similaire à l’exemple JSON.  
> 
> 

#### <a name="retrieving-the-list-of-azure-resource-group-deployments"></a>Récupération de la liste des déploiements de groupes de ressources Azure
Pour récupérer la liste des déploiements de groupes de ressources Azure, utilisez l’applet de commande **Get-AzureRmResourceGroupDeployment**, comme indiqué dans l’exemple suivant :

```powershell
Get-AzureRmResourceGroupDeployment -ResourceGroupName adf
```

```
DeploymentName    : ADFAlertFailedSlice
ResourceGroupName : adf
ProvisioningState : Succeeded
Timestamp         : 10/11/2014 2:01:00 AM
Mode              : Incremental
TemplateLink      :
Parameters        :
Outputs           :
```

#### <a name="troubleshooting-user-events"></a>Résolution des problèmes des événements utilisateur
1. Vous pouvez voir tous les événements générés après avoir cliqué sur la vignette **Mesures et opérations**.
   
    ![Vignette Mesures et opérations](./media/data-factory-monitor-manage-pipelines/metrics-and-operations-tile.png)
2. Cliquez sur la vignette **Événements** pour afficher les événements. 
   
    ![Vignette d'événements](./media/data-factory-monitor-manage-pipelines/events-tile.png)
3. Dans le panneau **Événements**, vous pouvez consulter des détails sur les événements, filtrer les événements, et ainsi de suite. 
   
    ![Panneau Événements](./media/data-factory-monitor-manage-pipelines/events-blade.png)
4. Dans la liste des opérations, cliquez sur une **opération** à l’origine d’une erreur.
   
    ![Sélectionner une opération](./media/data-factory-monitor-manage-pipelines/select-operation.png) 
5. Pour afficher des détails sur l’erreur, cliquez sur un événement **erreur**.
   
    ![Événement erreur](./media/data-factory-monitor-manage-pipelines/operation-error-event.png)

Consultez l’article [Applets de commande Azure Insight](https://msdn.microsoft.com/library/mt282452.aspx) pour plus d’informations sur les applets de commande PowerShell que vous pouvez utiliser pour ajouter/obtenir/supprimer des alertes. Voici quelques exemples d’utilisation de l’applet de commande **Get-AlertRule** : 

```powershell
get-alertrule -res $resourceGroup -n ADFAlertsSlice -det
```

```
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
Condition      :
Description : One or more of the data slices for the Azure Data Factory has failed processing.
Status      : Enabled
Name:       : ADFAlertsSlice
Tags       :
$type          : Microsoft.WindowsAzure.Management.Common.Storage.CasePreservedDictionary, Microsoft.WindowsAzure.Management.Common.Storage
Id: /subscriptions/<subscription ID>/resourceGroups/<resource group name>/providers/microsoft.insights/alertrules/ADFAlertsSlice
Location   : West US
Name       : ADFAlertsSlice
```

```powershell
Get-AlertRule -res $resourceGroup
```
```
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
```

```powershell
Get-AlertRule -res $resourceGroup -Name FailedExecutionRunsWest0
```

```
Properties : Microsoft.Azure.Management.Insights.Models.Rule
Tags       : {[$type, Microsoft.WindowsAzure.Management.Common.Storage.CasePreservedDictionary, Microsoft.WindowsAzure.Management.Common.Storage]}
Id         : /subscriptions/<subscription id>/resourceGroups/<resource group name>/providers/microsoft.insights/alertrules/FailedExecutionRunsWest0
Location   : West US
Name       : FailedExecutionRunsWest0
```

Exécutez les commandes get-help suivantes afinb d’afficher des détails et des exemples pour l'applet de commande Get-AlertRule. 

```powershell
get-help Get-AlertRule -detailed
```

```powershell 
get-help Get-AlertRule -examples
```


* Si des événements de génération d'alertes apparaissent sur le panneau du portail mais que vous ne recevez aucune notification par e-mail, vérifiez que votre adresse e-mail est bien configurée de manière à recevoir des e-mails de la part d’expéditeurs externes. Les alertes par e-mail peuvent avoir été bloquées en raison de vos paramètres de messagerie.

### <a name="alerts-on-metrics"></a>Alertes relatives à des mesures
Data Factory vous permet de capturer différentes mesures et de créer des alertes associées. Vous pouvez analyser et créer des alertes relatives à des mesures suivantes pour les tranches de votre fabrique de données.

* Exécutions échouées
* Exécutions réussies

Ces mesures sont très utiles et vous permettent d'obtenir une vue d'ensemble globale des exécutions réussies et échouées dans votre fabrique de données. Des mesures sont émises lors de chaque exécution de tranche de données. Toutes les heures, ces mesures sont regroupées et transférées dans votre compte de stockage. Par conséquent, pour activer les mesures, configurez un compte de stockage.

#### <a name="enabling-metrics"></a>Activation des mesures :
Pour activer les mesures, cliquez sur ce qui suit à partir du panneau Data Factory :

**Analyse** -> **Mesures** -> **Paramètres de diagnostic** -> **Diagnostic**

![Lien Diagnostics](./media/data-factory-monitor-manage-pipelines/diagnostics-link.png)

Dans le panneau **Diagnostic**, cliquez sur **Activé**, sélectionnez le compte de stockage, puis enregistrez.

![Panneau Diagnostics](./media/data-factory-monitor-manage-pipelines/diagnostics-blade.png)

Après enregistrement, vous devrez peut-être attendre une heure maximum avant que les mesures ne soient visibles sur le panneau d’analyse. En effet, le regroupement des mesures s’effectue une fois par heure.

### <a name="setting-up-alert-on-metrics"></a>Configuration d'une alerte relative à des mesures :
Cliquez sur le panneau **Mesures de fabrique de données** : 

![Vignette Mesures de fabrique de données](./media/data-factory-monitor-manage-pipelines/data-factory-metrics-tile.png)

Dans le panneau **Mesures**, dans la barre d’outils, cliquez sur **+ Ajouter une alerte**. 
![Panneau Mesures de fabrique de données - Ajouter une alerte](./media/data-factory-monitor-manage-pipelines/add-alert.png)

Dans la page **Ajouter une règle d’alerte**, procédez comme suit, puis cliquez sur **OK**.

* Entrez un nom pour l’alerte (par exemple : alerte d’échec).
* Entrez une description pour l’alerte (par exemple : envoyer un courrier électronique en cas d’échec).
* Sélectionnez une mesure (exécution en échec et exécutions réussies).
* Spécifiez une condition et une valeur de seuil.   
* Spécifiez la période. 
* Spécifiez si un courrier électronique doit être envoyé aux propriétaires, aux collaborateurs et aux lecteurs.
* et bien plus. 

![Panneau Mesures de fabrique de données - Ajouter une alerte](./media/data-factory-monitor-manage-pipelines/add-an-alert-rule.png)

Une fois la règle d’alerte correctement ajoutée, le panneau se ferme et la nouvelle alerte s’affiche dans la page **Mesure**. 

![Panneau Mesures de fabrique de données - Ajouter une alerte](./media/data-factory-monitor-manage-pipelines/failed-alert-in-metric-blade.png)

Vous devez également voir le nombre d’alertes sur la vignette **Alertes**. Cliquez sur la vignette **Alertes**.

![Panneau Mesures de fabrique de données - Règles d’alerte](./media/data-factory-monitor-manage-pipelines/alert-rules-tile-rules.png)

Dans le panneau **alertes**, vous voyez les alertes existantes. Pour ajouter une alerte, dans la barre d’outils, cliquez sur **Ajouter une alerte**.

![Panneau Règles d’alerte](./media/data-factory-monitor-manage-pipelines/alert-rules-blade.png)

### <a name="alert-notifications"></a>Notifications d'alerte :
Une fois que la règle d’alerte correspond à la condition, un e-mail d’alerte vous est envoyé. Une fois que le problème est résolu et que la condition d'alerte est caduque, un message signalant que l’alerte a été résolue vous est envoyé.

Ce comportement se distingue des événements donnant lieu à l’envoi d’une notification dès qu’un échec correspondant à une règle d’alerte a lieu.

### <a name="deploying-alerts-using-powershell"></a>Déploiement d'alertes à l'aide de PowerShell
Vous pouvez déployer des alertes relatives à des mesures de la même façon que vous le faites pour les événements. 

**Définition d’alerte :**

```JSON
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
```

Remplacez les valeurs de subscriptionId, resourceGroupName et dataFactoryName figurant dans l'exemple par des valeurs appropriées.

*metricName* prend actuellement en charge ces deux valeurs :

* FailedRuns
* SuccessfulRuns

**Déploiement de l’alerte :**

Pour déployer l’alerte, utilisez l’applet de commande Azure PowerShell **New-AzureRmResourceGroupDeployment**, comme indiqué dans l’exemple suivant :

```powershell
New-AzureRmResourceGroupDeployment -ResourceGroupName adf -TemplateFile .\FailedRunsGreaterThan5.json
```

Le message suivant devrait s’afficher après la réussite du déploiement :

```
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
```

Vous pouvez également utiliser l’applet de commande **Add-AlertRule** pour déployer une règle d’alerte. Consultez la rubrique [Add-AlertRule](https://msdn.microsoft.com/library/mt282468.aspx) pour obtenir plus d’informations et des exemples.  

## <a name="move-data-factory-to-a-different-resource-group-or-subscription"></a>Déplacer la fabrique de données vers un autre groupe de ressources ou abonnement
Vous pouvez déplacer une fabrique de données vers un autre groupe de ressources ou abonnement à l’aide du bouton de la barre de commandes **Déplacer** situé sur la page d’accueil de votre fabrique de données. 

![Déplacer la fabrique de données](./media/data-factory-monitor-manage-pipelines/MoveDataFactory.png)

Vous pouvez également déplacer toutes les ressources associées (notamment les alertes associées à la fabrique de données) en même temps que la fabrique de données.

![Boîte de dialogue Déplacer des ressources](./media/data-factory-monitor-manage-pipelines/MoveResources.png)



<!--HONumber=Nov16_HO3-->


