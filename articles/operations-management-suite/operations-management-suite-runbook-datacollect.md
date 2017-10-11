---
title: "Collecte des données Log Analytics avec un runbook dans Azure Automation | Microsoft Docs"
description: "Didacticiel étape par étape expliquant comment créer un runbook dans Azure Automation pour collecter des données dans le référentiel OMS en vue de l’analyse par Log Analytics."
services: log-analytics
documentationcenter: 
author: bwren
manager: carmonm
editor: 
ms.assetid: a831fd90-3f55-423b-8b20-ccbaaac2ca75
ms.service: operations-management-suite
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 05/27/2017
ms.author: bwren
ms.openlocfilehash: 59f674c9c6404da7f5384539189f41a4ba1a939a
ms.sourcegitcommit: f537befafb079256fba0529ee554c034d73f36b0
ms.translationtype: MT
ms.contentlocale: fr-FR
ms.lasthandoff: 07/11/2017
---
# <a name="collect-data-in-log-analytics-with-an-azure-automation-runbook"></a>Collecter des données dans Log Analytics avec un runbook Azure Automation
Vous pouvez collecter une quantité importante de données dans Log Analytics à partir de diverses sources, y compris des [sources de données](../log-analytics/log-analytics-data-sources.md) sur les agents et les [données recueillies à partir d’Azure](../log-analytics/log-analytics-azure-storage.md).  Il existe cependant des cas où vous devez recueillir des données qui ne sont pas accessibles via ces sources standard.  Dans ce cas, vous pouvez utiliser l’[API Collecte de données HTTP](../log-analytics/log-analytics-data-collector-api.md) pour écrire des données dans Log Analytics à partir de n’importe quel client API REST.  Une méthode courante pour effectuer cette collecte de données consiste à utiliser un runbook dans Azure Automation.   

Ce didacticiel vous guide dans le processus de création et de planification d’un runbook dans Azure Automation pour écrire des données dans Log Analytics.


## <a name="prerequisites"></a>Composants requis
Ce scénario nécessite que les ressources suivantes soient configurées dans votre abonnement Azure.  Les deux peuvent être des comptes gratuits.

- [Espace de travail Log Analytics](../log-analytics/log-analytics-get-started.md).
- [Compte Azure Automation](../automation/automation-offering-get-started.md).

## <a name="overview-of-scenario"></a>Présentation du scénario
Pour ce didacticiel, vous allez écrire un runbook qui collecte les informations sur les tâches Automation.  Dans Azure Automation, les runbooks sont implémentés avec PowerShell. Vous devrez donc commencer par écrire et tester un script dans l’éditeur d’Azure Automation.  Une fois que vous avez vérifié que vous collectez les informations requises, vous écrirez ces données dans Log Analytics et vérifierez le type de données personnalisé.  Enfin, vous créerez une planification pour démarrer le runbook à intervalles réguliers.

> [!NOTE]
> Vous pouvez configurer Azure Automation de manière à envoyer les informations sur les tâches à Log Analytics sans ce runbook.  Ce scénario est principalement utilisé pour prendre en charge le didacticiel. Il est recommandé d’envoyer les données à un espace de travail de test.  


## <a name="1-install-data-collector-api-module"></a>1. Installer le module de l’API du collecteur de données
Chaque [requête émanant de l’API du collecteur de données HTTP](../log-analytics/log-analytics-data-collector-api.md#create-a-request) doit être correctement mise en forme et inclure un en-tête d’autorisation.  Vous pouvez effectuer ces actions dans votre runbook, mais vous pouvez réduire la quantité de code nécessaire en utilisant un module qui simplifie ce processus.  Vous pouvez par exemple utiliser le [module OMSIngestionAPI](https://www.powershellgallery.com/packages/OMSIngestionAPI) disponible dans PowerShell Gallery.

Pour utiliser un [module](../automation/automation-integration-modules.md) dans un runbook, il doit être installé dans votre compte Automation.  Tout runbook du même compte peut ensuite utiliser les fonctionnalités du module.  Vous pouvez installer un module en sélectionnant **Ressources** > **Modules** > **Ajouter un module** dans votre compte Automation.  

PowerShell Gallery offre cependant une option rapide pour déployer un module directement sur votre compte Automation afin de pouvoir utiliser cette option pour ce didacticiel.  

![Module OMSIngestionAPI](media/operations-management-suite-runbook-datacollect/OMSIngestionAPI.png)

1. Accédez à [PowerShell Gallery](https://www.powershellgallery.com/).
2. Recherchez **OMSIngestionAPI**.
3. Cliquez sur le bouton **Deploy to Azure Automation** (Déployer sur Azure Automation).
4. Sélectionnez votre compte Automation, puis cliquez sur **OK** pour installer le module.


## <a name="2-create-automation-variables"></a>2. Créer des variables Automation
Les [variables Automation](..\automation\automation-variables.md) contiennent des valeurs qui peuvent être utilisées par tous les runbooks de votre compte Automation.  Elles rendent les runbooks plus souples en vous permettant de modifier ces valeurs sans modifier le runbook en lui-même. Chaque requête émanant de l’API du collecteur de données HTTP nécessite l’ID et la clé de l’espace de travail OMS. Les ressources de variables sont idéales pour stocker ces informations.  

![Variables](media/operations-management-suite-runbook-datacollect/variables.png)

1. Sur le Portail Azure, accédez à votre compte Automation.
2. Sélectionnez **Variables** sous **Ressources partagées**.
2. Cliquez sur **Ajouter une variable** et créez les deux variables dans le tableau suivant.

| Propriété | Valeur d’ID d’espace de travail | Valeur de clé d’espace de travail |
|:--|:--|:--|
| Nom | WorkspaceID | WorkspaceKey |
| Type | String | String |
| Valeur | Collez l’ID de votre espace de travail Log Analytics. | Collez la clé principale ou secondaire de votre espace de travail Log Analytics. |
| Chiffré | Non | Oui |



## <a name="3-create-runbook"></a>3. Créer un runbook

Azure Automation fournit un éditeur dans le portail, où vous pouvez modifier et tester votre runbook.  Vous pouvez utiliser l’éditeur de script pour travailler avec [PowerShell directement](../automation/automation-edit-textual-runbook.md) ou [créer un runbook graphique](../automation/automation-graphical-authoring-intro.md).  Pour ce didacticiel, vous allez travailler avec un script PowerShell. 

![Modifier un runbook](media/operations-management-suite-runbook-datacollect/edit-runbook.png)

1. Accédez à votre compte Automation.  
2. Cliquez sur **Runbooks** > **Ajouter un runbook** > **Create a new runbook** (Créer un runbook).
3. Pour le nom du runbook, saisissez **Collect-Automation-jobs**.  Pour le type de runbook, sélectionnez **PowerShell**.
4. Cliquez sur **Créer** pour créer le runbook et démarrer l’éditeur.
5. Copiez et collez le code suivant dans le runbook.  Consultez les commentaires dans le script pour obtenir une explication du code.
    
        # Get information required for the automation account from parameter values when the runbook is started.
        Param
        (
            [Parameter(Mandatory = $True)]
            [string]$resourceGroupName,
            [Parameter(Mandatory = $True)]
            [string]$automationAccountName
        )
        
        # Authenticate to the Automation account using the Azure connection created when the Automation account was created.
        # Code copied from the runbook AzureAutomationTutorial.
        $connectionName = "AzureRunAsConnection"
        $servicePrincipalConnection=Get-AutomationConnection -Name $connectionName         
        Add-AzureRmAccount `
            -ServicePrincipal `
            -TenantId $servicePrincipalConnection.TenantId `
            -ApplicationId $servicePrincipalConnection.ApplicationId `
            -CertificateThumbprint $servicePrincipalConnection.CertificateThumbprint 
        
        # Set the $VerbosePreference variable so that we get verbose output in test environment.
        $VerbosePreference = "Continue"
        
        # Get information required for Log Analytics workspace from Automation variables.
        $customerId = Get-AutomationVariable -Name 'WorkspaceID'
        $sharedKey = Get-AutomationVariable -Name 'WorkspaceKey'
        
        # Set the name of the record type.
        $logType = "AutomationJob"
        
        # Get the jobs from the past hour.
        $jobs = Get-AzureRmAutomationJob -ResourceGroupName $resourceGroupName -AutomationAccountName $automationAccountName -StartTime (Get-Date).AddHours(-1)
        
        if ($jobs -ne $null) {
            # Convert the job data to json
            $body = $jobs | ConvertTo-Json
        
            # Write the body to verbose output so we can inspect it if verbose logging is on for the runbook.
            Write-Verbose $body
        
            # Send the data to Log Analytics.
            Send-OMSAPIIngestionFile -customerId $customerId -sharedKey $sharedKey -body $body -logType $logType -TimeStampField CreationTime
        }


## <a name="4-test-runbook"></a>4. Tester le runbook
Azure Automation inclut un environnement permettant de [tester votre runbook](../automation/automation-testing-runbook.md) avant de le publier.  Vous pouvez inspecter les données collectées par le runbook et vérifiez que l’écriture dans Log Analytics est correcte avant de publier le runbook pour production. 
 
![Tester le runbook](media/operations-management-suite-runbook-datacollect/test-runbook.png)

6. Cliquez sur **Enregistrer** pour enregistrer le runbook.
1. Cliquez sur **Volet de test** pour ouvrir le runbook dans l’environnement de test.
3. Étant donné que votre runbook possède des paramètres, vous êtes invité à entrer les valeurs de ces derniers.  Entrez le nom du groupe de ressources et du compte Automation à partir desquels vous allez collecter des informations sur les tâches.
4. Cliquez sur **Démarrer** pour démarrer le runbook.
3. Le runbook démarre avec l’état **Mis en file d’attente** avant de passer à l’état **Exécution**.  
3. Le runbook doit afficher une sortie détaillée avec les tâches collectées au format json.  Si aucune tâche n’est répertoriée, il se peut qu’aucune tâche n’ait été créée dans le compte Automation dans la dernière heure.  Essayez de démarrer un autre runbook dans le compte Automation, puis effectuez de nouveau le test.
4. Vérifiez que la sortie n’affiche pas d’erreur dans la commande post pour Log Analytics.  Un message similaire à celui ci-dessous doit s’afficher.

    ![Sortie post](media/operations-management-suite-runbook-datacollect/post-output.png)

## <a name="5-verify-records-in-log-analytics"></a>5. Vérifier les enregistrements dans Log Analytics
Une fois le test du runbook terminé, et après avoir vérifié que la sortie a bien été reçue, vous pouvez vérifier que les enregistrements ont été créés à l’aide d’une [recherche dans les journaux de Log Analytics](../log-analytics/log-analytics-log-searches.md).

![Sortie du journal](media/operations-management-suite-runbook-datacollect/log-output.png)

1. Dans le portail Azure, sélectionnez votre espace de travail Log Analytics.
2. Cliquez sur **Recherche dans les journaux**.
3. Tapez la commande suivante `Type=AutomationJob_CL` et cliquez sur le bouton de recherche. Notez que le type d’enregistrement inclut _CL, qui n’est pas spécifié dans le script.  Ce suffixe est ajouté automatiquement au type de journal pour indiquer qu’il s’agit d’un type de journal personnalisé.
4. Un ou plusieurs enregistrements devraient être renvoyés pour signaler que le runbook fonctionne comme prévu.


## <a name="6-publish-the-runbook"></a>6. Publier le runbook
Une fois que vous avez vérifié que le runbook fonctionne correctement, vous devez le publier afin de pouvoir l’exécuter en production.  Vous pouvez continuer à modifier et tester le runbook sans modifier la version publiée.  

![Publier le runbook](media/operations-management-suite-runbook-datacollect/publish-runbook.png)

1. Revenez à votre compte Automation.
2. Cliquez sur **Runbooks** et sélectionnez **Collect-Automation-jobs**.
3. Cliquez sur **Modifier**, puis sur **Publier**.
4. Cliquez sur **Oui** lorsque vous êtes invité à vérifier que vous souhaitez remplacer la version précédemment publiée.

## <a name="7-set-logging-options"></a>7. Définir les options de journalisation 
Pour le test, vous pouviez afficher une [sortie détaillée](../automation/automation-runbook-output-and-messages.md#message-streams) parce que vous aviez défini la variable $VerbosePreference dans le script.  Pour la production, vous devez définir les propriétés de journalisation du runbook si vous souhaitez afficher une sortie détaillée.  Pour le runbook utilisé dans ce didacticiel, cette opération affiche les données json envoyées à Log Analytics.

![Journalisation et suivi](media/operations-management-suite-runbook-datacollect/logging.png)

1. Dans les propriétés de votre runbook, sélectionnez **Journalisation et suivi** sous **Paramètres des runbooks**.
2. Modifiez le paramètre **Log verbose records** (Journaliser les enregistrements détaillés) sur **Activé**.
3. Cliquez sur **Save**.

## <a name="8-schedule-runbook"></a>8. Planifier un runbook
La méthode la plus courante pour démarrer un runbook qui collecte les données de surveillance consiste à planifier son exécution automatique.  Pour cela, vous devez créer une [planification dans Azure Automation](../automation/automation-schedules.md) et la lier à votre runbook.

![Planifier un runbook](media/operations-management-suite-runbook-datacollect/schedule-runbook.png)

1. Dans les propriétés de votre runbook, sélectionnez **Planifications** sous **Ressources**.
2. Cliquez sur **Add a schedule (Ajouter une planification)** > **Link a schedule to your runbook (Lier une planification à votre runbook)** > **Create a new schedule (Créer une planification)**.
5. Saisissez les valeurs suivantes pour la planification, puis cliquez sur **Créer**.

| Propriété | Valeur |
|:--|:--|
| Nom | AutomationJobs-Hourly |
| Démarrages | Sélectionnez une heure au moins 5 minutes après l’heure actuelle. |
| Périodicité | Récurrent |
| Répéter toutes les | 1 heure |
| Définir une expiration | Non |

Une fois que la planification est créée, vous devez définir les valeurs de paramètre qui seront utilisées chaque fois que cette planification démarre le runbook.

6. Cliquez sur **Configure parameters and run settings (Configurer les paramètres et les paramètres d’exécution)**.
7. Renseignez les valeurs pour **ResourceGroupName** et **AutomationAccountName**.
8. Cliquez sur **OK**. 

## <a name="9-verify-runbook-starts-on-schedule"></a>9. Vérifier la conformité du démarrage du runbook avec la planification
À chaque démarrage d’un runbook, [un travail est créé](../automation/automation-runbook-execution.md) et toute sortie est enregistrée.  En fait, il s’agit des mêmes travaux que ceux que le runbook collecte.  Vous pouvez vérifier que le runbook démarre comme prévu en vérifiant les travaux du runbook après l’heure de démarrage indiquée dans la planification.

![Travaux](media/operations-management-suite-runbook-datacollect/jobs.png)

1. Dans les propriétés de votre runbook, sélectionnez **Travaux** sous **Ressources**.
2. Vous devez voir une liste des travaux pour chaque démarrage du runbook.
3. Cliquez sur l’un des travaux pour en afficher les détails.
4. Cliquez sur **Tous les journaux** pour afficher les journaux et les sorties du runbook.
5. Faites défiler vers le bas pour rechercher une entrée similaire à l’image ci-dessous.<br>![Détaillé](media/operations-management-suite-runbook-datacollect/verbose.png)
6. Cliquez sur cette entrée pour afficher les données json détaillées qui ont été envoyées à Log Analytics.



## <a name="next-steps"></a>Étapes suivantes
- Utilisez le [concepteur de vue](../log-analytics/log-analytics-view-designer.md) pour créer une vue affichant les données que vous avez collectées dans le référentiel Log Analytics.
- Intégrez votre runbook dans une [solution de gestion](operations-management-suite-solutions-creating.md) à distribuer aux clients.
- En savoir plus sur [Log Analytics](https://docs.microsoft.com/azure/log-analytics/).
- En savoir plus sur [Azure Automation](https://docs.microsoft.com/azure/automation/).
- En savoir plus sur l’[API du collecteur de données HTTP](../log-analytics/log-analytics-data-collector-api.md).