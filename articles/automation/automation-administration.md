<properties 
   pageTitle="Administration d’Azure Automation"
   description="Cet article contient plusieurs rubriques concernant l’administration d’un environnement Azure Automation. Incluent actuellement la conservation des données et la sauvegarde Azure Automation."
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
   ms.date="10/01/2015"
   ms.author="bwren" />

# Administration d’Azure Automation

Cet article contient plusieurs rubriques concernant l’administration d’un environnement Azure Automation.

## Conservation des données

Lorsque vous supprimez une ressource dans Azure Automation, elle est conservée pendant 90 jours à des fins d’audit avant d’être supprimée définitivement. Vous ne pouvez ni voir ni utiliser la ressource pendant cette période. Cette stratégie vaut aussi pour les ressources qui appartiennent à un compte Automation supprimé.

Azure Automation supprime automatiquement et définitivement les tâches de plus de 90 jours.

Le tableau suivant récapitule la stratégie de rétention pour les différentes ressources.

|Données|Stratégie|
|:---|:---|
|Comptes|Supprimés définitivement 90 jours après leur suppression par un utilisateur.|
|Éléments multimédias|Supprimés définitivement 90 jours après suppression des éléments multimédias par un utilisateur ou 90 jours après la suppression par un utilisateur du compte qui contient l’élément multimédia.|
|Modules|Supprimés définitivement 90 jours après suppression du module par un utilisateur ou 90 jours après la suppression par un utilisateur du compte qui contient le module.|
|Runbooks|Supprimés définitivement 90 jours après suppression de la ressource par un utilisateur ou 90 jours après la suppression par un utilisateur du compte qui contient la ressource.|
|Travaux|Effacés et supprimés définitivement 90 jours après la dernière modification. Cela peut avoir lieu après la fin du travail, son arrêt ou sa suspension.|

La stratégie de rétention s’applique à tous les utilisateurs et ne peut actuellement pas être personnalisée.

## Sauvegarde d’Azure Automation

Lorsque vous supprimez un compte d’automation dans Microsoft Azure, tous les objets du compte sont supprimés, y compris les Runbooks, les modules, les paramètres, les tâches et les éléments. Il est impossible de récupérer les objets une fois que le compte a été supprimé. Vous pouvez utiliser les informations suivantes pour sauvegarder le contenu de votre compte Automation avant de le supprimer.

### Runbooks

Vous pouvez exporter vos Runbooks vers vos fichiers de script en utilisant soit le portail de gestion Azure, soit l’applet de commande [Get-AzureAutomationRunbookDefinition](https://msdn.microsoft.com/library/dn690269.aspx) dans Windows PowerShell. Ces fichiers de script peuvent être importés dans un autre compte Automation, comme indiqué dans l’article [Création ou importation d'un Runbook](https://msdn.microsoft.com/library/dn643637.aspx).


### Modules d'intégration

Vous ne pouvez pas exporter les modules d'intégration depuis Azure Automation. Vous devez vous assurer qu'ils sont disponibles à l'extérieur du compte Automation.

### Éléments multimédias

Vous ne pouvez pas exporter d’ [éléments](https://msdn.microsoft.com/library/dn939988.aspx) depuis Azure Automation. À l'aide du portail de gestion Azure, vous devez noter les détails des variables, des informations d'identification, des certificats, des connexions et des planifications. Vous devez ensuite créer manuellement les ressources qui seront utilisées par les Runbooks que vous importerez dans une autre Automation.

Vous pouvez utiliser les [applets de commande Azure](https://msdn.microsoft.com/library/dn690262.aspx) pour récupérer les détails des éléments non chiffrés et les enregistrer pour vous y reporter ultérieurement ou créer des éléments équivalents dans un autre compte Automation.

Il est impossible de récupérer la valeur des variables chiffrées ou du champ du mot de passe des informations d'identification en utilisant les applets de commande. Si vous ne connaissez pas ces valeurs, vous pouvez les récupérer à partir d'un Runbook en utilisant les activités [Get-AutomationVariable](https://msdn.microsoft.com/library/dn940012.aspx) et [Get-AutomationPSCredential](https://msdn.microsoft.com/library/dn940015.aspx).

Vous ne pouvez pas exporter de certificats depuis Azure Automation. Vous devez vous assurer que tous les certificats sont disponibles en dehors d'Azure.

<!---HONumber=Oct15_HO2-->