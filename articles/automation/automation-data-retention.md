<properties 
   pageTitle="Rétention des données dans Azure Automation"
   description="Décrit la stratégie de rétention des données pour Azure Automation."
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

# Rétention des données dans Azure Automation

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

## Articles connexes
- [Sauvegarde d’Azure Automation](https://msdn.microsoft.com/library/dn643635.aspx) 

<!---HONumber=August15_HO6-->