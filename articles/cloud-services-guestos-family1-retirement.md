<properties 
   pageTitle="Informations relatives à la suppression de la famille 1 des systèmes d'exploitation invités | Azure" 
   description="Fournit des informations sur la suppression de la famille 1 des SE invités d'Azure et sur la façon de déterminer si vous êtes concerné" 
   services="cloud-services" 
   documentationCenter="na" 
   authors="Thraka" 
   manager="timlt" 
   editor=""/>

<tags
   ms.service="cloud-services"
   ms.devlang="na"
   ms.topic="article"
   ms.tgt_pltfrm="na"
   ms.workload="tbd" 
   ms.date="02/27/2015"
   ms.author="adegeo"/>



# Informations relatives à la suppression de la famille 1 des systèmes d'exploitation invités

La suppression de la famille 1 des systèmes d'exploitation a été annoncée le 1er juin 2013.

**2 septembre 2014** La famille 1.x des systèmes d'exploitation invités (SE invités) d'Azure, qui est basée sur le système d'exploitation Windows Server 2008, a été officiellement supprimée. Toutes les tentatives de déploiement de nouveaux services ou de mise à niveau des services existants à l'aide de la famille 1 échoueront, et un message d'erreur vous informera que la famille 1 des systèmes d'exploitation invités a été supprimée. 

**3 novembre 2014** La prise en charge étendue de la famille 1 des SE invités a pris fin et a été complètement supprimée. Tous les services toujours liés à la famille 1 seront affectés. Nous pouvons arrêter ces services à tout moment. Il n'existe aucune garantie que vos services continueront de s'exécuter, sauf si vous les mettez à niveau manuellement vous-même.

Si vous avez d'autres questions, consultez les [Forums de services cloud](http://social.msdn.microsoft.com/Forums/home?forum=windowsazuredevelopment&filter=alltypes&sort=lastpostdesc) ou [contactez le support Azure](http://azure.microsoft.com/support/options/).




## Êtes-vous concerné ?

Vos services cloud sont concernés si l'une des conditions suivantes s'applique :

1. Vous avez une valeur de " osFamily = "1" " explicitement spécifiée dans le fichier ServiceConfiguration.cscfg pour votre service cloud. 
2. Vous n'avez pas de valeur pour osFamily explicitement spécifiée dans le fichier ServiceConfiguration.cscfg pour votre service cloud. Actuellement, le système utilise la valeur par défaut " 1 " dans ce cas.
3. Le portail de gestion Azure répertorie votre valeur de famille des systèmes d'exploitation invités en tant que " Windows Server 2008 ". Cliquez [ici](https://msdn.microsoft.com/library/azure/gg456325.aspx) pour rechercher cette valeur.

Pour connaître la famille de systèmes d'exploitation exécutée par les services cloud, vous pouvez exécuter le script ci-dessous dans Azure PowerShell. Vous devrez toutefois [configurer Azure PowerShell](install-configure-powershell.md) avant toute chose. Pour plus d'informations sur ce script, consultez [Fin de vie de la famille 1 des SE invités d'Azure : juin 2014](http://blogs.msdn.com/b/ryberry/archive/2014/04/02/azure-guest-os-family-1-end-of-life-june-2014.aspx). 

```Powershell
foreach($subscription in Get-AzureSubscription) {
    Select-AzureSubscription -SubscriptionName $subscription.SubscriptionName 
    
    $deployments=get-azureService | get-azureDeployment -ErrorAction Ignore | where {$_.SdkVersion -NE ""} 

    $deployments | ft @{Name="SubscriptionName";Expression={$subscription.SubscriptionName}}, ServiceName, SdkVersion, Slot, @{Name="osFamily";Expression={(select-xml -content $_.configuration -xpath "/ns:ServiceConfiguration/@osFamily" -namespace $namespace).node.value }}, osVersion, Status, URL
}
```

Vos services cloud seront affectés par la suppression de la famille 1 si la colonne osFamily de la sortie du script est vide ou contient " 1 ". 

## Recommandations si vous êtes concerné

Nous vous recommandons de migrer vos rôles de service cloud vers l'une des familles de SE invités prises en charge :

**Famille 4.x du SE invité** - Windows Server 2012 R2 *(recommandé)*

1. Assurez-vous que votre application utilise SDK 2.1 ou une version ultérieure avec .NET framework 4.0, 4.5 ou 4.5.1.
2. Définissez l'attribut osFamily sur " 4 " dans le fichier ServiceConfiguration.cscfg et redéployez votre service cloud.


**Famille 3.x du SE invité** - Windows Server 2012

1. Assurez-vous que votre application utilise SDK 1.8 ou une version ultérieure avec .NET framework 4.0 ou 4.5. 
2. Définissez l'attribut osFamily sur " 3 " dans le fichier ServiceConfiguration.cscfg et redéployez votre service cloud.


**Famille 2.x du SE invité** - Windows Server 2008 R2

1. Assurez-vous que votre application utilise SDK 1.3 ou une version ultérieure avec .NET framework 3.5 ou 4.0. 
2. Définissez l'attribut osFamily sur " 2 " dans le fichier ServiceConfiguration.cscfg et redéployez votre service cloud.


## Fin de la prise en charge étendue pour la famille 1 des SE invités depuis le 3 novembre 2014
Les services cloud de la famille 1 des SE invités ne sont plus pris en charge. Veuillez quitter la famille 1 dès que possible pour éviter toute interruption de service. 

<!--HONumber=52-->