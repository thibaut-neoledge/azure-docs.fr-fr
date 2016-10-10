<properties
    pageTitle="Transférer l’état d’un travail et des flux de travail d’Automation vers Log Analytics (OMS) | Microsoft Azure"
    description="Cet article montre comment envoyer des données d’état de travaux et des flux de travaux de runbook à Microsoft Operations Management Suite Log Analytics afin de renforcer la visibilité et la simplicité de gestion."
    services="automation"
    documentationCenter=""
    authors="MGoedtel"
    manager="jwhit"
    editor="tysonn" />
<tags
    ms.service="automation"
    ms.devlang="na"
    ms.topic="article"
    ms.tgt_pltfrm="na"
    ms.workload="infrastructure-services"
    ms.date="09/22/2016"
    ms.author="magoedte" />

# Transférer l’état d’un travail et des flux de travail d’Automation vers Log Analytics (OMS)

Automation peut envoyer l’état d’un travail de runbook et des flux de travail vers votre espace de travail Log Analytics dans Microsoft Operations Management Suite (OMS). Bien que vous puissiez afficher ces informations dans le portail Azure ou à l’aide de PowerShell pour l’état d’un travail spécifique ou pour l’ensemble des travaux d’un compte Automation particulier, toutes les tâches avancées que vous pourriez souhaiter effectuer pour répondre à vos besoins opérationnels vous obligent à créer des scripts PowerShell personnalisés. Log Analytics vous offre désormais les possibilités suivantes :

- Obtenir des informations sur vos travaux Automation
- Déclencher un e-mail ou une alerte basé(e) sur le statut de votre tâche de runbook (par exemple, échec ou état suspendu)
- Écrire des requêtes avancées sur vos flux de travail
- Mettre en corrélation des travaux sur différents comptes Automation
- Visualiser l’historique de vos travaux dans le temps

## Conditions préalables et considérations relatives au déploiement

Pour commencer à envoyer vos journaux Automation à Log Analytics, vous devez disposer des éléments suivants :

1. Un abonnement OMS. Pour plus d’informations, consultez l’article [Prise en main de Log Analytics](../log-analytics/log-analytics-get-started.md).

    >[AZURE.NOTE]L’espace de travail OMS et le compte Automation doivent se trouver dans le même abonnement Azure pour que cette configuration fonctionne correctement.
  
2. Un [compte de stockage Azure](../storage/storage-create-storage-account.md).
   
    >[AZURE.NOTE]Le compte de stockage *doit* se trouver dans la même région que le compte Automation.
 
3. Azure PowerShell avec la version 1.0.8 ou une version plus récente des applets de commande Operational Insights. Pour plus d’informations sur cette version et sur la méthode d’installation, voir l’article [Installation et configuration d’Azure PowerShell](../powershell-install-configure.md).
4. Azure Diagnostic et Log Analytics PowerShell. Pour plus d’informations sur cette version et sur la méthode d’installation, consultez la page relative à [Azure Diagnostic et Log Analytics](https://www.powershellgallery.com/packages/AzureDiagnosticsAndLogAnalytics/0.1).
5. Téléchargez le script PowerShell **Enable-AzureDiagnostics.ps1** à partir de la [Galerie PowerShell](https://www.powershellgallery.com/packages/Enable-AzureDiagnostics/1.0/DisplayScript). Ce script permet de configurer les éléments suivants :
 - Un compte de stockage pour contenir les données d’état et de flux de travaux de runbook pour un compte Automation que vous spécifiez.
 - Activez la collecte de ces données à partir de votre compte Automation de manière à les stocker dans un compte Azure Blob Storage au format JSON.
 - Configurez la collecte des données entre votre compte Blob Storage et OMS Log Analytics.
 - Activez la solution Automation Log Analytics dans votre espace de travail OMS.

Le script **Enable-AzureDiagnostics.ps1** requiert les paramètres suivants lors de l’exécution :

- *AutomationAccountName* : nom de votre compte Automation
- *LogAnalyticsWorkspaceName* : nom de votre espace de travail OMS

Pour rechercher les valeurs du paramètre *AutomationAccountName*, sélectionnez votre compte Automation à partir du panneau **Compte Automation** du portail Azure, puis sélectionnez **Tous les paramètres**. Dans le panneau **Tous les paramètres**, sous **Paramètres du compte**, sélectionnez **Propriétés**. Dans le panneau **Propriétés**, vous pouvez observer ces valeurs.<br> ![Propriétés du compte Automation](media/automation-manage-send-joblogs-log-analytics/automation-account-properties.png).


## Configurer l’intégration avec Log Analytics

1. Sur votre ordinateur, démarrez **Windows PowerShell** à partir de l’écran **Démarrer**.
2. À partir de l’interface de ligne de commande PowerShell, accédez au dossier qui contient le script que vous avez téléchargé, puis exécutez-le en modifiant les valeurs des paramètres *- AutomationAccountName* et *- LogAnalyticsWorkspaceName*.

    >[AZURE.NOTE] Une fois le script exécuté, vous êtes invité à vous authentifier auprès d’Azure. Vous **devez** vous connecter avec un compte membre du rôle Administrateurs des abonnements et coadministrateur de l’abonnement.
    
        .\Enable-AzureDiagnostics -AutomationAccountName <NameofAutomationAccount> `
        -LogAnalyticsWorkspaceName <NameofOMSWorkspace> `

3. Après l’exécution de ce script, vous devriez voir les enregistrements dans Log Analytics environ 30 minutes après l’écriture de nouvelles données de diagnostic dans le stockage. Si les enregistrements ne sont pas disponibles après ce délai, consultez la section de dépannage de l’article [JSON files in blob storage](../log-analytics/log-analytics-azure-storage-json.md#troubleshooting-configuration-for-azure-diagnostics-written-to-blob-in-json) (Fichiers JSON dans Blob Storage).

### Vérifier la configuration

Pour vérifier le bon fonctionnement du script configuré dans votre compte Automation et dans votre espace de travail OMS, vous pouvez effectuer les étapes suivantes dans PowerShell. Avant cela, vous devez rechercher les valeurs pour votre nom d’espace de travail OMS et le nom du groupe de ressources. Dans le portail Azure, accédez à Log Analytics (OMS) puis, dans le panneau Log Analytics (OMS), notez la valeur du **nom** et du **groupe de ressources**.<br> ![Liste des espaces de travail OMS Log Analytics](media/automation-manage-send-joblogs-log-analytics/oms-la-workspaces-list-blade.png) Nous allons utiliser ces deux valeurs lorsque nous vérifierons la configuration de votre espace de travail OMS à l’aide de l’applet de commande PowerShell [Get-AzureRmOperationalInsightsStorageInsight](https://msdn.microsoft.com/library/mt603567.aspx).

1.  À partir du portail Azure, accédez à Comptes de stockage et recherchez le compte de stockage suivant, qui utilise la convention d’affectation de noms *AutomationAccountNameomsstorage*. Après l’exécution d’un travail de runbook, vous devriez voir rapidement deux conteneurs d’objets blob : **insights-logs-joblogs** et **insights-logs-jobstreams**.

2.  À partir de PowerShell, exécutez le code PowerShell suivant, en modifiant les valeurs des paramètres **ResourceGroupName** et **WorkspaceName** que vous avez copiés ou notés précédemment.

    Login-AzureRmAccount Get-AzureRmSubscription -SubscriptionName 'SubscriptionName' | Set-AzureRmContext Get-AzureRmOperationalInsightsStorageInsight -ResourceGroupName "OMSResourceGroupName" ` -Workspace "OMSWorkspaceName"

    Cette commande renvoie les informations de stockage pour l’espace de travail OMS spécifié. Nous voulons à présent vérifier que les informations de stockage existent bien pour le compte d’automatisation que nous avons spécifié précédemment et que l’objet **State** indique la valeur **OK**.<br> ![Résultats de l’applet de commande Get-AzureRmOperationalInsightsStorageInsights](media/automation-manage-send-joblogs-log-analytics/automation-posh-getstorageinsights-results.png).


## Enregistrements Log Analytics

Automation crée deux types d’enregistrements dans le référentiel OMS.

### Journaux de travail

Propriété | Description|
----------|----------|
Time | Date et heure d’exécution du travail du runbook.|
resourceId | Spécifie le type de ressource dans Azure. Pour Automation, la valeur est le compte Automation associé au runbook.|
operationName | Spécifie le type d’opération exécutée dans Azure. Pour Automation, la valeur sera Job.|
resultType | L’état du travail du runbook. Les valeurs possibles sont :<br>- Started<br>- Stopped<br>- Suspended<br>- Failed<br>- Succeeded|
resultDescription | Décrit l’état résultant du travail du runbook. Les valeurs possibles sont :<br>- Job is started<br>- Job Failed<br>- Job Completed|
CorrelationId | GUID représentant l’ID de corrélation du travail du runbook.|
Catégorie | Classification du type de données. Pour Automation, la valeur est JobLogs.|
RunbookName | Nom du runbook.|
JobId | GUID représentant l’ID du travail du runbook.|
Appelant | Ce qui a initié l’opération. Les valeurs possibles sont une adresse de messagerie ou un système pour les travaux planifiés.|

### Flux de travail
Propriété | Description|
----------|----------|
Time | Date et heure d’exécution du travail du runbook.|
resourceId | Spécifie le type de ressource dans Azure. Pour Automation, la valeur est le compte Automation associé au runbook.|
operationName | Spécifie le type d’opération exécutée dans Azure. Pour Automation, la valeur sera Job.|
resultType | L’état du travail du runbook. Les valeurs possibles sont :<br>- InProgress|
resultDescription | Inclut le flux de sortie du runbook.|
CorrelationId | GUID représentant l’ID de corrélation du travail du runbook.|
Catégorie | Classification du type de données. Pour Automation, la valeur est JobStreams.|
RunbookName | Nom du runbook.|
JobId | GUID représentant l’ID du travail du runbook.|
Appelant | Ce qui a initié l’opération. Les valeurs possibles sont une adresse de messagerie ou un système pour les travaux planifiés.| 
StreamType | Type de flux de travail. Les valeurs possibles sont :<br>-Progress<br>- Output<br>- Warning<br>- Error<br>- Debug<br>- Verbose|

## Affichage d’Automation Logs dans Log Analytics 

Maintenant que vous avez commencé à envoyer vos journaux de travaux Automation à Log Analytics, nous allons voir comment vous pouvez utiliser ces journaux dans OMS.

### Envoyer un e-mail en cas d’échec ou de suspension d’un travail de runbook 

Nos clients attendent souvent d’être en mesure d’envoyer un e-mail ou un message texte lorsqu’une erreur survient avec un travail de runbook.

Pour créer une règle d’alerte, vous devez commencer par créer une recherche de journal pour les enregistrements de travaux de runbook qui doivent appeler l’alerte. Vous pouvez ensuite utiliser le bouton **Alerte** pour créer et configurer la règle d’alerte.

1.	Dans la page de présentation d’OMS, cliquez sur **Recherche de journal**.
2.	Créez une requête de recherche de journal pour votre alerte en tapant ce qui suit dans le champ de requête : `Category=JobLogs (ResultType=Failed || ResultType=Suspended)`. Vous pouvez également effectuer un regroupement par nom de runbook à l’aide de : `Category=JobLogs (ResultType=Failed || ResultType=Suspended) | measure Count() by RunbookName_s`.
  
    Si vous avez configuré des journaux dans votre espace de travail à partir de plusieurs abonnements ou comptes Automation, vous pouvez également regrouper vos alertes par abonnement ou par compte Automation. Le nom du compte Automation peut être dérivé du champ Ressource dans la recherche de JobLogs.

3.	Cliquez sur **Alerte** en haut de la page pour ouvrir l’écran **Ajouter une règle d’alerte**. Pour plus d’informations sur les options de configuration de l’alerte, consultez l’article [Alertes dans Log Analytics](../log-analytics/log-analytics-alerts.md#creating-an-alert-rule).

### Rechercher tous les travaux qui ont rencontré des erreurs 

En plus des alertes basés sur les échecs, vous souhaitez probablement savoir lorsqu’un travail de runbook rencontre une erreur sans fin d’exécution (PowerShell génère un flux d’erreur, mais les erreurs sans fin d’exécution n’entraînent pas la suspension ou l’échec de votre travail).

1. Dans le portail OMS, cliquez sur **Recherche de journal**.
2. Dans le champ de requête, tapez `Category=JobStreams StreamType_s=Error | measure count() by JobId_g` puis cliquez sur **Rechercher**.


### Afficher les flux de travail pour un travail  

Lorsque vous déboguez un projet, vous pouvez également examiner les flux de travail. La requête ci-dessous montre tous les flux pour un seul travail avec le GUID 2ebd22ea-e05e-4eb9 - 9d 76-d73cbd4356e0 :

`Category=JobStreams JobId_g="2ebd22ea-e05e-4eb9-9d76-d73cbd4356e0" | sort TimeGenerated | select ResultDescription`

### Afficher l’état de travail historique 

Vous pouvez enfin souhaiter visualiser l’historique de vos travaux dans le temps. Vous pouvez utiliser cette requête pour rechercher l’état de vos travaux au fil du temps.

`Category=JobLogs NOT(ResultType="started") | measure Count() by ResultType interval 1day` <br> ![Graphique d’état de travail historique OMS](media/automation-manage-send-joblogs-log-analytics/historical-job-status-chart.png)<br>


## Résumé

En envoyant vos données d’état et de flux de travail Automation à Log Analytics, vous pouvez obtenir plus de détails sur l’état de vos travaux Automation en définissant des alertes qui vous avertissent en cas de problème, et en personnalisant vos tableaux de bord à l’aide de requêtes avancées afin de visualiser les résultats de votre runbook, l’état de votre travail de runbook ainsi que d’autres indicateurs clés ou métriques associés. Toutes ces informations vous permettent d’obtenir une meilleure visibilité opérationnelle et de résoudre plus rapidement les incidents.


## Étapes suivantes

- Pour plus d’informations sur la façon de construire différentes requêtes de recherche et sur la manière de consulter les journaux de travaux Automation avec Log Analytics, consultez [Recherches de journal dans Log Analytics](../log-analytics/log-analytics-log-searches.md)
- Pour savoir comment créer et récupérer la sortie et les messages d’erreur à partir des runbooks, consultez [Sortie et messages de Runbook dans Azure Automation](automation-runbook-output-and-messages.md)
- Pour en savoir plus sur l’exécution d’un runbook, la manière de surveiller des tâches de runbook et d’autres détails techniques, consultez [Suivre une tâche de runbook](automation-runbook-execution.md)
- Pour en savoir plus sur OMS Log Analytics et sur les sources de collecte de données, consultez [Collecting Azure storage data in Log Analytics overview](../log-analytics/log-analytics-azure-storage.md) (Vue d’ensemble de la collecte de données Azure Storage dans Log Analytics)

<!---HONumber=AcomDC_0928_2016-->