<properties 
   pageTitle="Sauvegarde d’Azure Automation"
   description="Décrit comment sauvegarder le contenu d'un compte Automation afin qu'il puisse être conservé après la suppression d'un compte Automation."
   services="automation"
   documentationCenter=""
   authors="bwren"
   manager="stevenka"
   editor="tysonn" />
<tags 
   ms.service="automation"
   ms.devlang="na"
   ms.topic="article"
   ms.tgt_pltfrm="na"
   ms.workload="infrastructure-services"
   ms.date="07/22/2015"
   ms.author="bwren" />

# Sauvegarde d’Azure Automation

Lorsque vous supprimez un compte d’automation dans Microsoft Azure, tous les objets du compte sont supprimés, y compris les Runbooks, les modules, les paramètres, les tâches et les éléments. Il est impossible de récupérer les objets une fois que le compte a été supprimé. Vous pouvez utiliser les informations suivantes pour sauvegarder le contenu de votre compte Automation avant de le supprimer.

## Runbooks

Vous pouvez exporter vos Runbooks vers vos fichiers de script en utilisant soit le portail de gestion Azure, soit l’applet de commande [Get-AzureAutomationRunbookDefinition](https://msdn.microsoft.com/library/dn690269.aspx) dans Windows PowerShell. Ces fichiers de script peuvent être importés dans un autre compte Automation, comme indiqué dans l’article [Création ou importation d'un Runbook](https://msdn.microsoft.com/library/dn643637.aspx).


## Modules d'intégration

Vous ne pouvez pas exporter les modules d'intégration depuis Azure Automation. Vous devez vous assurer qu'ils sont disponibles à l'extérieur du compte Automation.

## Éléments multimédias

Vous ne pouvez pas exporter d’ [éléments](https://msdn.microsoft.com/library/dn939988.aspx) depuis Azure Automation. À l'aide du portail de gestion Azure, vous devez noter les détails des variables, des informations d'identification, des certificats, des connexions et des planifications. Vous devez ensuite créer manuellement les ressources qui seront utilisées par les Runbooks que vous importerez dans une autre Automation.

Vous pouvez utiliser les [applets de commande Azure](https://msdn.microsoft.com/library/dn690262.aspx) pour récupérer les détails des éléments non chiffrés et les enregistrer pour vous y reporter ultérieurement ou créer des éléments équivalents dans un autre compte Automation.

Il est impossible de récupérer la valeur des variables chiffrées ou du champ du mot de passe des informations d'identification en utilisant les applets de commande. Si vous ne connaissez pas ces valeurs, vous pouvez les récupérer à partir d'un Runbook en utilisant les activités [Get-AutomationVariable](https://msdn.microsoft.com/library/dn940012.aspx) et [Get-AutomationPSCredential](https://msdn.microsoft.com/library/dn940015.aspx).

Vous ne pouvez pas exporter de certificats depuis Azure Automation. Vous devez vous assurer que tous les certificats sont disponibles en dehors d'Azure.

## Articles connexes

- [Création ou importation d’un Runbook](https://msdn.microsoft.com/library/dn643637.aspx)
- [Ressources Automation](https://msdn.microsoft.com/library/dn939988.aspx)
- [Applets de commande Azure](https://msdn.microsoft.com/library/dn690262.aspx) 

<!---HONumber=August15_HO6-->