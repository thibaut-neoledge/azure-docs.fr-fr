---
title: Utiliser PowerShell pour créer des alertes pour les services Azure | Microsoft Docs
description: Utilisez PowerShell pour créer des alertes Azure, qui peuvent déclencher des notifications ou Automation lorsque les conditions spécifiées sont remplies.
author: rboucher
manager: ''
editor: ''
services: monitoring-and-diagnostics
documentationcenter: monitoring-and-diagnostics

ms.service: monitoring-and-diagnostics
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 09/23/2016
ms.author: robb

---
# Utiliser PowerShell pour créer des alertes pour les services Azure
> [!div class="op_single_selector"]
> * [Portail](../monitoring-and-diagnostics/insights-alerts-portal.md)
> * [PowerShell](../monitoring-and-diagnostics/insights-alerts-powershell.md)
> * [INTERFACE DE LIGNE DE COMMANDE](insights-alerts-command-line-interface.md)
> 
> 

## Vue d'ensemble
Cet article vous montre comment définir des alertes Azure avec PowerShell.

Vous pouvez recevoir une alerte en fonction de métriques de surveillance pour vos services Azure ou d'événements sur ces derniers.

* **Valeurs de métriques** : l’alerte se déclenche lorsque la valeur d’une métrique spécifiée dépasse un seuil que vous affectez dans un des deux sens. C’est-à-dire que le déclenchement se fait à la fois lorsque la condition est remplie et par la suite une fois que la condition n’est plus remplie.
* **Événements du journal d’activité** : une alerte peut se déclencher sur *chaque* événement ou uniquement lorsqu’un certain nombre d’événements se produisent.

Vous pouvez configurer une alerte pour effectuer les opérations suivantes lors de son déclenchement :

* envoyer des notifications par courrier électronique à l’administrateur du service et aux coadministrateurs
* envoyer un courrier électronique à d’autres adresses que vous spécifiez.
* appeler un webhook
* démarrer l’exécution d’un runbook Azure (uniquement à partir du Portail Azure)

Vous pouvez configurer et obtenir des informations sur les règles d’alerte avec

* [Portail Azure](../monitoring-and-diagnostics/insights-alerts-portal.md)
* [PowerShell](../monitoring-and-diagnostics/insights-alerts-powershell.md)
* [interface de ligne de commande (CLI)](insights-alerts-command-line-interface.md)
* [API REST Azure Insights](https://msdn.microsoft.com/library/azure/dn931945.aspx)

Pour obtenir des informations complémentaires, vous pouvez à tout moment taper ```get-help```, suivi de la commande PowerShell sur laquelle vous souhaitez de l’aide.

## Créer des règles d’alerte dans PowerShell
1. Connectez-vous à Azure.
   
    ```PowerShell
    Login-AzureRmAccount
   
    ```
2. Récupérez la liste des abonnements dont vous disposez. Vérifiez que vous travaillez avec le bon abonnement. Dans le cas contraire, choisissez le bon à l’aide de la sortie de `Get-AzureRmSubscription`.
   
    ```PowerShell
    Get-AzureRmSubscription
    Get-AzureRmContext
    Set-AzureRmContext -SubscriptionId <subscriptionid>
    ```
3. Pour répertorier les règles existantes sur un groupe de ressources, utilisez la commande suivante :
   
   ```PowerShell
   Get-AzureRmAlertRule -ResourceGroup <myresourcegroup> -DetailedOutput
   ```
4. Pour créer une règle, vous avez d’abord besoin de différentes informations importantes.
   
   * **L’ID de la ressource** pour laquelle vous souhaitez définir une alerte
   * Les **définitions de métriques** disponibles pour cette ressource
     
     Pour obtenir l’ID de la ressource, vous pouvez utiliser le Portail Azure. À supposer que la ressource soit déjà créée, sélectionnez-la dans le portail. Puis, dans le panneau suivant, sélectionnez *Propriétés* sous la section *Paramètres*. L’ID DE RESSOURCE est un champ du panneau suivant. Une autre méthode consiste à utiliser [l’Explorateur de ressources Azure](https://resources.azure.com/).
     
     Voici un exemple d’ID de ressource d’une application web
     
     ```
     /subscriptions/dededede-7aa0-407d-a6fb-eb20c8bd1192/resourceGroups/myresourcegroupname/providers/Microsoft.Web/sites/mywebsitename
     ```
     
     Vous pouvez utiliser `Get-AzureRmMetricDefinition` pour afficher la liste de toutes les définitions de métriques d’une ressource spécifique.
     
     ```PowerShell
     Get-AzureRmMetricDefinition -ResourceId <resource_id>
     ```
     
     L’exemple suivant génère une table avec le nom de la métrique et son unité.
     
     ```PowerShell
     Get-AzureRmMetricDefinition -ResourceId <resource_id> | Format-Table -Property Name,Unit
     
     ```
     La liste complète des options disponibles pour Get-AzureRmMetricDefinition s’obtient en exécutant la commande Get-MetricDefinitions.
5. L’exemple suivant configure une alerte sur une ressource de site web. L’alerte se déclenche chaque fois qu’il reçoit constamment du trafic pendant cinq minutes, et à nouveau lorsqu’il ne reçoit aucun trafic pendant cinq minutes.
   
    ```PowerShell
    Add-AzureRmMetricAlertRule -Name myMetricRuleWithWebhookAndEmail -Location "East US" -ResourceGroup myresourcegroup -TargetResourceId /subscriptions/dededede-7aa0-407d-a6fb-eb20c8bd1192/resourceGroups/myresourcegroupname/providers/Microsoft.Web/sites/mywebsitename -MetricName "BytesReceived" -Operator GreaterThan -Threshold 2 -WindowSize 00:05:00 -TimeAggregationOperator Total -Description "alert on any website activity"
   
    ```
6. Pour créer un webhook ou envoyer un courrier électronique lorsqu’une alerte se déclenche, commencez par créer le message et/ou les webhooks. Puis créez la règle immédiatement après avec la balise -Actions, comme l’indique l’exemple suivant. Vous ne pouvez pas associer un webhook ou des messages électroniques à des règles déjà créées à l’aide de PowerShell.

    ```PowerShell
    $actionEmail = New-AzureRmAlertRuleEmail -CustomEmail myname@company.com
    $actionWebhook = New-AzureRmAlertRuleWebhook -ServiceUri https://www.contoso.com?token=mytoken

    Add-AzureRmMetricAlertRule -Name myMetricRuleWithWebhookAndEmail -Location "East US" -ResourceGroup myresourcegroup -TargetResourceId /subscriptions/dededede-7aa0-407d-a6fb-eb20c8bd1192/resourceGroups/myresourcegroupname/providers/Microsoft.Web/sites/mywebsitename -MetricName "BytesReceived" -Operator GreaterThan -Threshold 2 -WindowSize 00:05:00 -TimeAggregationOperator Total -Actions $actionEmail, $actionWebhook -Description "alert on any website activity"
    ```


1. Pour créer une alerte qui se déclenche selon une condition spécifique dans le journal d’activité, utilisez des commandes au format suivant
   
    ```PowerShell
    $actionEmail = New-AzureRmAlertRuleEmail -CustomEmail myname@company.com
    $actionWebhook = New-AzureRmAlertRuleWebhook -ServiceUri https://www.contoso.com?token=mytoken
   
    Add-AzureRmLogAlertRule -Name myLogAlertRule -Location "East US" -ResourceGroup myresourcegroup -OperationName microsoft.web/sites/start/action -Status Succeeded -TargetResourceGroup resourcegroupbeingmonitored -Actions $actionEmail, $actionWebhook
    ```
   
    operationName correspond à un type d’événement dans le journal d’activité. Par exemple, *Microsoft.Compute/virtualMachines/delete* et *microsoft.insights/diagnosticSettings/write*.
   
    Vous pouvez utiliser la commande PowerShell [Get-AzureRmProviderOperation](https://msdn.microsoft.com/library/mt603720.aspx) pour obtenir la liste des valeurs operationName possibles. De même, vous pouvez utiliser le Portail Azure pour interroger le journal d’activité et trouver les opérations passées pour lesquelles vous souhaitez créer une alerte. Les opérations indiquées dans l’affichage graphique du journal sont les noms conviviaux. Recherchez l’entrée dans JSON et récupérez la valeur operationName.
2. Vérifiez que vos alertes ont été correctement créées en examinant les règles en particulier.
   
    ```PowerShell
    Get-AzureRmAlertRule -Name myMetricRuleWithWebhookAndEmail -ResourceGroup myresourcegroup -DetailedOutput
   
    Get-AzureRmAlertRule -Name myLogAlertRule -ResourceGroup myresourcegroup -DetailedOutput
    ```
3. Supprimez vos alertes. Ces commandes suppriment les règles créées précédemment dans cet article.
   
    ```PowerShell
    Remove-AzureRmAlertRule -ResourceGroup myresourcegroup -Name myrule
    Remove-AzureRmAlertRule -ResourceGroup myresourcegroup -Name myMetricRuleWithWebhookAndEmail
    Remove-AzureRmAlertRule -ResourceGroup myresourcegroup -Name myLogAlertRule
    ```

## Étapes suivantes
* [Consultez une vue d’ensemble de la surveillance Azure](../monitoring-and-diagnostics/monitoring-overview.md), notamment les types d’informations que vous pouvez collecter et surveiller.
* Découvrez plus en détails la [configuration des webhooks dans les alertes](../monitoring-and-diagnostics/insights-webhooks-alerts.md).
* Découvrez plus en détails les [runbooks Azure Automation](../automation/automation-starting-a-runbook.md).
* Consultez une [vue d’ensemble de la collecte des journaux de diagnostic](../monitoring-and-diagnostics/monitoring-overview-of-diagnostic-logs.md) pour collecter des métriques détaillées à fréquence élevée sur votre service.
* Consultez une [vue d’ensemble de la collecte des métriques](insights-how-to-customize-monitoring.md) pour vous assurer que votre service est disponible et réactif.

<!---HONumber=AcomDC_0928_2016-->