<properties
 pageTitle="Informations de référence sur les applets de commande PowerShell de Scheduler"
 description="Informations de référence sur les applets de commande PowerShell de Scheduler"
 services="scheduler"
 documentationCenter=".NET"
 authors="krisragh"
 manager="dwrede"
 editor=""/>
<tags
 ms.service="scheduler"
 ms.workload="infrastructure-services"
 ms.tgt_pltfrm="na"
 ms.devlang="dotnet"
 ms.topic="article"
 ms.date="08/18/2016"
 ms.author="krisragh"/>

# Informations de référence sur les applets de commande PowerShell de Scheduler

Le tableau suivant décrit chacune des principales applets de commande disponibles dans Azure Scheduler et établit un lien vers la page de référence correspondante.

Pour installer Azure PowerShell et l’associer à votre abonnement Azure, consultez l’article [Installation et configuration d’Azure PowerShell](../powershell-install-configure.md).

Pour plus d’informations sur les [applets de commande Azure Resource Manager](https://msdn.microsoft.com/library/mt125356(v=azure.200).aspx), consultez [Utilisation d’Azure PowerShell avec Azure Resource Manager](../powershell-azure-resource-manager.md).

|Applet de commande|Description de l'applet de commande|
|---|---|
[Disable-AzureRmSchedulerJobCollection](https://msdn.microsoft.com/library/mt490133(v=azure.200).aspx) |Désactive une collection de travaux. 
[Enable-AzureRmSchedulerJobCollection](https://msdn.microsoft.com/library/mt490135(v=azure.200).aspx) |Active une collection de travaux.
[Get-AzureRmSchedulerJob](https://msdn.microsoft.com/library/mt490125(v=azure.200).aspx) |Récupère les travaux du Planificateur.
[Get-AzureRmSchedulerJobCollection](https://msdn.microsoft.com/library/mt490132(v=azure.200).aspx) |Récupère les collections de travaux.
[Get-AzureRmSchedulerJobHistory](https://msdn.microsoft.com/library/mt490126(v=azure.200).aspx) |Affiche l’historique des travaux.
[New-AzureRmSchedulerHttpJob](https://msdn.microsoft.com/library/mt490136(v=azure.200).aspx) |Crée un travail HTTP.
[New-AzureRmSchedulerJobCollection](https://msdn.microsoft.com/library/mt490141(v=azure.200).aspx) |Crée une collection de travaux.
[New-AzureRmSchedulerServiceBusQueueJob](https://msdn.microsoft.com/library/mt490134(v=azure.200).aspx) |Crée un travail de file d’attente Service Bus.
[New-AzureRmSchedulerServiceBusTopicJob](https://msdn.microsoft.com/library/mt490142(v=azure.200).aspx) |Crée un travail de rubrique Service Bus.
[New-AzureRmSchedulerStorageQueueJob](https://msdn.microsoft.com/library/mt490127(v=azure.200).aspx) |Crée un travail de file d’attente de stockage. 
[Remove-AzureRmSchedulerJob](https://msdn.microsoft.com/library/mt490140(v=azure.200).aspx) |Supprime un travail du Planificateur.  
[Remove-AzureRmSchedulerJobCollection](https://msdn.microsoft.com/library/mt490131(v=azure.200).aspx) |Supprime une collection de travaux. 
[Set-AzureRmSchedulerHttpJob](https://msdn.microsoft.com/library/mt490130(v=azure.200).aspx) |Modifie un travail HTTP du Planificateur.
[Set-AzureRmSchedulerJobCollection](https://msdn.microsoft.com/library/mt490129(v=azure.200).aspx) |Modifie une collection de travaux. 
[Set-AzureRmSchedulerServiceBusQueueJob](https://msdn.microsoft.com/library/mt490143(v=azure.200).aspx) |Modifie un travail de file d’attente Service Bus.  
[Set-AzureRmSchedulerServiceBusTopicJob](https://msdn.microsoft.com/library/mt490137(v=azure.200).aspx) |Modifie un travail de rubrique Service Bus. 
[Set-AzureRmSchedulerStorageQueueJob](https://msdn.microsoft.com/library/mt490128(v=azure.200).aspx) |Modifie un travail de file d’attente de stockage.   

Pour plus d’informations, vous pouvez exécuter les applets de commande suivantes :

```
Get-Help <cmdlet name> -Detailed
```
```
Get-Help <cmdlet name> -Examples
```
```
Get-Help <cmdlet name> -Full
```

## Voir aussi


 [Présentation d'Azure Scheduler](scheduler-intro.md)

 [Concepts, terminologie et hiérarchie d’entités d’Azure Scheduler](scheduler-concepts-terms.md)

 [Prise en main de Scheduler dans le portail Azure](scheduler-get-started-portal.md)

 [Plans et facturation dans Azure Scheduler](scheduler-plans-billing.md)

 [Informations de référence sur l’API REST d’Azure Scheluler](https://msdn.microsoft.com/library/mt629143)

 [Haute disponibilité et fiabilité d’Azure Scheluler](scheduler-high-availability-reliability.md)

 [Limites, valeurs par défaut et codes d’erreur d’Azure Scheluler](scheduler-limits-defaults-errors.md)

 [Authentification sortante d’Azure Scheluler](scheduler-outbound-authentication.md)

<!---HONumber=AcomDC_0914_2016-->