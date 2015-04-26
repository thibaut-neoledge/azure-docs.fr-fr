<properties title="Azure Stream Analytics monitor and manage jobs using Azure PowerShell" pageTitle="Stream Analytics - Surveiller et gérer les tâches à l'aide de PowerShell | Azure" description="Découvrez comment utiliser les applets de commande Azure PowerShell pour surveiller et gérer les tâches Stream Analytics." metaKeywords="" services="stream-analytics" solutions="" documentationCenter="" authors="jgao" videoId="" scriptId="" manager="paulettm" editor="cgronlun"/>

<tags ms.service="stream-analytics" ms.devlang="na" ms.topic="article" ms.tgt_pltfrm="na" ms.workload="data-services" ms.date="12/9/2014" ms.author="jgao" />


#Surveiller et gérer les tâches Stream Analytics à l'aide d'Azure PowerShell

Découvrez comment gérer les ressources Azure Stream Analytics à l'aide d'Azure PowerShell.

##Dans cet article

- [Configuration requise](#prerequisites)
- [Applets de commande PowerShell Stream Analytics](#cmdlets)
	- [Get-AzureStreamAnalyticsJob](#Get-AzureStreamAnalyticsJob)
	- [Get-AzureStreamAnalyticsInput](#Get-AzureStreamAnalyticsInput)
	- [Get-AzureStreamAnalyticsOutput](#Get-AzureStreamAnalyticsOutput)
	- [Get-AzureStreamAnalyticsQuota](#Get-AzureStreamAnalyticsQuota)
	- [Get-AzureStreamAnalyticsTransformation](#Get-AzureStreamAnalyticsTransformation)
	- [New-AzureStreamAnalyticsInput](#New-AzureStreamAnalyticsInput)
	- [New-AzureStreamAnalyticsJob](#New-AzureStreamAnalyticsJob)
	- [New-AzureStreamAnalyticsOutput](#New-AzureStreamAnalyticsOutput)
	- [New-AzureStreamAnalyticsTransformation](#New-AzureStreamAnalyticsTransformation)
	- [Remove-AzureStreamAnalyticsInput](#Remove-AzureStreamAnalyticsInput)
	- [Remove-AzureStreamAnalyticsJob](#Remove-AzureStreamAnalyticsJob)
	- [Remove-AzureStreamAnalyticsOutput](#Remove-AzureStreamAnalyticsOutput)
	- [Start-AzureStreamAnalyticsJob](#Start-AzureStreamAnalyticsJob)
	- [Stop-AzureStreamAnalyticsJob](#Stop-AzureStreamAnalyticsJob)
	- [Test-AzureStreamAnalyticsInput](#Test-AzureStreamAnalyticsInput)
	- [Test-AzureStreamAnalyticsOutput](#Test-AzureStreamAnalyticsOutput)
- [Voir aussi](#seealso)

## <a name="prerequisites"></a>Conditions requises pour l'exécution des applets de commande PowerShell Stream Analytics

1.	Installer et configurer Azure PowerShell.

	Suivez les instructions de la page [Installation et configuration d'Azure PowerShell][powershell-install] pour installer Azure PowerShell.

2.	Configurer le mode Azure

	Après avoir installé Azure PowerShell, exécutez l'applet de commande [Switch-AzureMode][msdn-switch-azuremode] pour définir le mode Azure approprié pour accéder aux applets de commande Stream Analytics :

		Switch-AzureMode AzureResourceManager

>[WACOM.NOTE] Il existe une limitation temporaire selon laquelle la surveillance n'est pas activée pour les tâches Stream Analytics créées via Azure PowerShell.  Pour contourner ce problème, accédez à la page Surveillance du travail dans le portail Azure et cliquez sur le bouton " Activer ".  

##<a name="cmdlets"></a>Applets de commande PowerShell Stream Analytics
Le tableau suivant répertorie les applets de commande que vous pouvez utiliser pour surveiller et gérer des tâches Azure Stream Analytics à l'aide d'Azure PowerShell.

###<a name="Get-AzureStreamAnalyticsJob"></a>Get-AzureStreamAnalyticsJob
Répertorie toutes les tâches Stream Analytics définies dans l'abonnement ou le groupe de ressources Azure spécifié ou obtient des informations sur un travail spécifique au sein d'un groupe de ressources.

**Exemple 1**

	Get-AzureStreamAnalyticsJob

Cette commande retourne des informations sur toutes les tâches Stream Analytics dans l'abonnement Azure.

**Exemple 2**

	Get-AzureStreamAnalyticsJob -ResourceGroupName StreamAnalytics-Default-West-US 
Cette commande retourne des informations sur toutes les tâches Stream Analytics dans le groupe de ressources treamAnalytics-Default-West-US.

**Exemple 3**

	Get-AzureStreamAnalyticsJob -ResourceGroupName StreamAnalytics-Default-West-US -Name StreamingJob
Cette commande retourne des informations sur la tâche Stream Analytics StreamingJob dans le groupe de ressources StreamAnalytics-Default-West-US.

###<a name="Get-AzureStreamAnalyticsInput"></a>Get-AzureStreamAnalyticsInput
Répertorie toutes les entrées qui sont définies dans une tâche Stream Analytics spécifiée ou obtient des informations sur une entrée spécifique.

**Exemple 1**

	Get-AzureStreamAnalyticsInput -ResourceGroupName StreamAnalytics-Default-West-US -JobName StreamingJob

Cette commande retourne des informations sur toutes les entrées définies sur la tâche StreamingJob.

**Exemple 2**

	Get-AzureStreamAnalyticsInput -ResourceGroupName StreamAnalytics-Default-West-US -JobName StreamingJob -Name EntryStream
Cette commande retourne des informations sur l'entrée nommée EntryStream définie sur la tâche StreamingJob.

###<a name="Get-AzureStreamAnalyticsOutput"></a>Get-AzureStreamAnalyticsOutput
Répertorie toutes les sorties définies dans une tâche Stream Analytics spécifiée ou obtient des informations sur une sortie spécifique.

**Exemple 1**

	Get-AzureStreamAnalyticsOutput -ResourceGroupName StreamAnalytics-Default-West-US -JobName StreamingJob
Cette commande retourne des informations sur les sorties définies sur la tâche StreamingJob.

**Exemple 2**

	Get-AzureStreamAnalyticsOutput -ResourceGroupName StreamAnalytics-Default-West-US -JobName StreamingJob -Name Output
Cette commande retourne des informations sur la sortie nommée Output définie sur la tâche StreamingJob.

###<a name="Get-AzureStreamAnalyticsQuota"></a>Get-AzureStreamAnalyticsQuota
Obtient des informations sur le quota Streaming Unit d'une région spécifiée.

**Exemple 1**

	Get-AzureStreamAnalyticsQuota -Location "West US" 
Cette commande retourne des informations sur le quota Streaming Unit et l'utilisation dans la région West US.

###<a name="Get-AzureStreamAnalyticsTransformation"></a>Get-AzureStreamAnalyticsTransformation
Obtient des informations sur une transformation spécifique définie sur une tâche Stream Analytics.

**Exemple 1**

	Get-AzureStreamAnalyticsTransformation -ResourceGroupName StreamAnalytics-Default-West-US -JobName StreamingJob -Name StreamingJob
Cette commande retourne des informations sur la transformation nommée StreamingJob sur la tâche StreamingJob.

###<a name="New-AzureStreamAnalyticsInput"></a>New-AzureStreamAnalyticsInput
Crée une entrée dans une tâche Stream Analytics ou met à jour une entrée spécifiée existante.
  
Vous pouvez spécifier le nom de l'entrée dans le fichier .JSON ou sur la ligne de commande.  Si vous spécifiez les deux, le nom indiqué sur la ligne de commande doit être identique à celui contenu dans le fichier.

Si vous spécifiez une entrée qui existe déjà et que vous ne spécifiez pas le paramètre -Force, l'applet de commande vous demande s'il faut remplacer l'entrée existante.

Si vous spécifiez le paramètre -Force et un nom d'entrée existant, l'entrée est remplacée sans confirmation.

**Exemple 1**

	New-AzureStreamAnalyticsInput -ResourceGroupName StreamAnalytics-Default-West-US -JobName StreamingJob -File "C:\Input.json" 
Cette commande crée une entrée à partir du fichier Input.json.  Si une entrée existante avec le nom spécifié dans le fichier de définition d'entrée est déjà définie, l'applet de commande vous demande s'il faut la remplacer.

**Exemple 2**
	
	New-AzureStreamAnalyticsInput -ResourceGroupName StreamAnalytics-Default-West-US -JobName StreamingJob -File "C:\Input.json" -Name EntryStream
Cette commande crée une entrée sur la tâche nommée EntryStream.  Si une entrée existante portant ce nom est déjà définie, l'applet de commande vous demande s'il faut la remplacer.

**Exemple 3**

	New-AzureStreamAnalyticsInput -ResourceGroupName StreamAnalytics-Default-West-US -JobName StreamingJob -File "C:\Input.json" -Name EntryStream -Force
Cette commande remplace la définition de la source d'entrée existante nommée EntryStream par la définition qui se trouve dans le fichier.

###<a name="New-AzureStreamAnalyticsJob"></a>New-AzureStreamAnalyticsJob
Crée une tâche Stream Analytics dans Microsoft Azure ou met à jour la définition d'une tâche existante spécifiée.

Vous pouvez spécifier le nom de la tâche dans le fichier .JSON ou sur la ligne de commande.  Si vous spécifiez les deux, le nom indiqué sur la ligne de commande doit être identique à celui contenu dans le fichier.

Si vous spécifiez un nom de tâche qui existe déjà et que vous ne spécifiez pas le paramètre -Force, l'applet de commande vous demande s'il faut remplacer la tâche existante.

Si vous spécifiez le paramètre -Force et un nom de tâche existant, la définition de tâche est remplacée sans confirmation.

**Exemple 1**

	New-AzureStreamAnalyticsJob -ResourceGroupName StreamAnalytics-Default-West-US -File "C:\JobDefinition.json" 
Cette commande crée une tâche à partir de la définition qui se trouve dans JobDefinition.json.  Si une tâche existante avec le nom spécifié dans le fichier de définition de tâche est déjà définie, l'applet de commande vous demande s'il faut la remplacer.

**Exemple 2**

	New-AzureStreamAnalyticsJob -ResourceGroupName StreamAnalytics-Default-West-US -File "C:\JobDefinition.json" -Name StreamingJob -Force
Cette commande remplace la définition de tâche pour StreamingJob.

###<a name="New-AzureStreamAnalyticsOutput"></a>New-AzureStreamAnalyticsOutput
Crée une sortie dans une tâche Stream Analytics ou met à jour une sortie existante.  

Vous pouvez spécifier le nom de la sortie dans le fichier .JSON ou sur la ligne de commande.  Si vous spécifiez les deux, le nom indiqué sur la ligne de commande doit être identique à celui contenu dans le fichier.

Si vous spécifiez une sortie qui existe déjà et que vous ne spécifiez pas le paramètre -Force, l'applet de commande vous demande s'il faut remplacer la sortie existante.

Si vous spécifiez le paramètre -Force et un nom de sortie existant, la sortie est remplacée sans confirmation.

**Exemple 1**

	New-AzureStreamAnalyticsOutput -ResourceGroupName StreamAnalytics-Default-West-US -File "C:\Output.json" -JobName StreamingJob -Name output
Cette commande crée une sortie nommée " output " dans la tâche StreamingJob.  Si une sortie existante portant ce nom est déjà définie, l'applet de commande vous demande s'il faut la remplacer.

**Exemple 2**

	New-AzureStreamAnalyticsOutput -ResourceGroupName StreamAnalytics-Default-West-US -File "C:\Output.json" -JobName StreamingJob -Name output -Force
Cette commande remplace la définition de " output " dans la tâche StreamingJob.

###<a name="New-AzureStreamAnalyticsTransformation"></a>New-AzureStreamAnalyticsTransformation
Crée une transformation dans une tâche Stream Analytics ou met à jour la transformation existante.
  
Vous pouvez spécifier le nom de la transformation dans le fichier .JSON ou sur la ligne de commande.  Si vous spécifiez les deux, le nom indiqué sur la ligne de commande doit être identique à celui contenu dans le fichier.

Si vous spécifiez une transformation qui existe déjà et que vous ne spécifiez pas le paramètre -Force, l'applet de commande vous demande s'il faut remplacer la transformation existante.

Si vous spécifiez le paramètre -Force et un nom de transformation existant, la transformation est remplacée sans confirmation.

**Exemple 1**

	New-AzureStreamAnalyticsTransformation -ResourceGroupName StreamAnalytics-Default-West-US -File "C:\Transformation.json" -JobName StreamingJob -Name StreamingJobTransform
Cette commande crée une transformation nommée StreamingJobTransform dans la tâche StreamingJob.  Si une transformation existante est déjà définie avec ce nom, l'applet de commande vous demande s'il faut la remplacer.

**Exemple 2**

	New-AzureStreamAnalyticsTransformation -ResourceGroupName StreamAnalytics-Default-West-US -File "C:\Transformation.json" -JobName StreamingJob -Name StreamingJobTransform -Force
 Cette commande remplace la définition de StreamingJobTransform dans la tâche StreamingJob.

###<a name="Remove-AzureStreamAnalyticsInput"></a>Remove-AzureStreamAnalyticsInput
Supprime de manière asynchrone une entrée spécifique d'une tâche Stream Analytics dans Microsoft Azure.  
Si vous spécifiez le paramètre -Force, l'entrée est supprimée sans confirmation.

**Exemple 1**
	
	Remove-AzureStreamAnalyticsInput -ResourceGroupName StreamAnalytics-Default-West-US -JobName StreamingJob -Name EventStream
Cette commande supprime l'entrée EventStream dans la tâche StreamingJob.  

###<a name="Remove-AzureStreamAnalyticsJob"></a>Remove-AzureStreamAnalyticsJob
Supprime de manière asynchrone une tâche Stream Analytics spécifique dans Microsoft Azure.  
Si vous spécifiez le paramètre -Force, la tâche est supprimée sans confirmation.

**Exemple 1**

	Remove-AzureStreamAnalyticsJob -ResourceGroupName StreamAnalytics-Default-West-US -Name StreamingJob 
Cette commande supprime la tâche StreamingJob.  

###<a name="Remove-AzureStreamAnalyticsOutput"></a>Remove-AzureStreamAnalyticsOutput
Supprime de manière asynchrone une sortie spécifique d'une tâche Stream Analytics dans Microsoft Azure.  
Si vous spécifiez le paramètre -Force, la sortie est supprimée sans confirmation.

**Exemple 1**

	Remove-AzureStreamAnalyticsOutput -ResourceGroupName StreamAnalytics-Default-West-US -JobName StreamingJob -Name Output
Cette commande supprime la sortie Output de la tâche StreamingJob.  

###<a name="Start-AzureStreamAnalyticsJob"></a>Start-AzureStreamAnalyticsJob
Déploie et démarre une tâche Stream Analytics dans Microsoft Azure de façon asynchrone.

**Exemple 1**

	Start-AzureStreamAnalyticsJob -ResourceGroupName StreamAnalytics-Default-West-US -Name StreamingJob 
Cette commande démarre la tâche StreamingJob.  

###<a name="Stop-AzureStreamAnalyticsJob"></a>Stop-AzureStreamAnalyticsJob
Arrête l'exécution d'une tâche Stream Analytics dans Microsoft Azure de façon asynchrone et libère les ressources qui étaient utilisées. La définition de la tâche et les métadonnées restent disponibles dans votre abonnement via le portail Azure et les API de gestion ; ainsi, la tâche peut être modifiée et redémarrée. Une tâche à l'état Arrêtée ne vous sera pas facturée.

**Exemple 1**

	Stop-AzureStreamAnalyticsJob -ResourceGroupName StreamAnalytics-Default-West-US -Name StreamingJob 
Cette commande arrête la tâche StreamingJob.  

###<a name="Test-AzureStreamAnalyticsInput"></a>Test-AzureStreamAnalyticsInput
Teste la capacité de Stream Analytics à se connecter à une entrée spécifiée.

**Exemple 1**

	Test-AzureStreamAnalyticsInput -ResourceGroupName StreamAnalytics-Default-West-US -JobName StreamingJob -Name EntryStream
Cette commande teste l'état de connexion de l'entrée EntryStream dans StreamingJob.  

###<a name="Test-AzureStreamAnalyticsOutput"></a>Test-AzureStreamAnalyticsOutput
Teste la capacité de Stream Analytics à se connecter à une sortie spécifiée.

**Exemple 1**

	Test-AzureStreamAnalyticsOutput -ResourceGroupName StreamAnalytics-Default-West-US -JobName StreamingJob -Name Output
Cette commande teste l'état de connexion de la sortie Output dans StreamingJob.  


##<a name="seealso"></a>Voir aussi

- [Présentation d'Azure Stream Analytics][stream.analytics.introduction]
- [Prise en main de Stream Analytics][stream.analytics.get.started]
- [Limites de la version préliminaire de Stream Analytics][stream.analytics.limitations]
- [Guide du développeur pour Stream Analytics][stream.analytics.developer.guide]
- [Référence du langage de requête pour Stream Analytics][stream.analytics.query.language.reference]
- [Référence de l'API REST pour Stream Analytics][stream.analytics.rest.api.reference]
 



[msdn-switch-azuremode]: http://msdn.microsoft.com/fr-fr/library/dn722470.aspx
[powershell-install]: http://azure.microsoft.com/fr-fr/documentation/articles/install-configure-powershell/


[stream-analytics-introduction]: ../
[stream-analytics-get-started]
[stream-analytics-limitations]
[stream-analytics-developer-guide]
[stream-analytics-query-language-reference]
[stream-analytics-rest-api-reference]
 


[stream.analytics.introduction]: ../stream-analytics-introduction/
[stream.analytics.get.started]: ../stream-analytics-get-started/
[stream.analytics.developer.guide]: ../stream-analytics-developer-guide/
[stream.analytics.scale.jobs]: ../stream-analytics-scale-jobs/
[stream.analytics.limitations]: ../stream-analytics-limitations/
[stream.analytics.query.language.reference]: http://go.microsoft.com/fwlink/?LinkID=513299
[stream.analytics.rest.api.reference]: http://go.microsoft.com/fwlink/?LinkId=517301

<!--HONumber=35.2-->
