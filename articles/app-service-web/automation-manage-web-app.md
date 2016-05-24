<properties
	pageTitle="Gérer une application web Azure à l’aide d’Azure Automation"
	description="Découvrez comment utiliser le service Azure Automation pour gérer une application web Azure."
	services="app-service\web, automation"
	documentationCenter=""
	authors="csand-msft"
	manager="eamono"
	editor=""/>

<tags
	ms.service="app-service-web"
	ms.workload="web"
	ms.tgt_pltfrm="na"
	ms.devlang="na"
	ms.topic="article"
	ms.date="04/18/2016"
	ms.author="csand"/>



#Gestion d’une application web Azure à l’aide d’Azure Automation

Ce guide vous présente le service Azure Automation et décrit comment l’utiliser pour simplifier la gestion d’une application web Azure.

## Qu'est-ce qu'Azure Automation ?

[Azure Automation](https://azure.microsoft.com/services/automation/) est un service Azure destiné à simplifier la gestion du cloud via l'automatisation des processus. Il automatise les tâches manuelles, répétitives, fastidieuses et sources d’erreurs pour accroître la fiabilité, l’efficacité et le retour sur investissement de votre organisation.

Azure Automation fournit un moteur d’exécution de workflow à haute fiabilité et à haute disponibilité, qui s’adapte à vos besoins. Dans Azure Automation, les processus peuvent être lancés manuellement, par des systèmes tiers ou à des intervalles planifiés, afin que les tâches interviennent exactement au moment opportun.

Réduisez les coûts opérationnels et libérez du temps pour que votre équipe d’informaticiens et de développeurs puisse se concentrer sur des tâches qui génèrent une valeur ajoutée pour l’entreprise, en déléguant l’exécution automatique de vos tâches de gestion de Cloud à Azure Automation.


## Comment Azure Automation peut-il vous aider à gérer une application web Azure ?

Dans Azure Automation, une application Web est gérée à l’aide d’applets de commande PowerShell qui sont fournies dans les [modules Azure PowerShell](../powershell-install-configure/). Vous pouvez [installer ces applets de commande PowerShell dans Azure Automation](https://azure.microsoft.com/blog/announcing-azure-resource-manager-support-azure-automation-runbooks/) afin d’exécuter toutes vos tâches de gestion d’application Web dans ce service. Dans Azure Automation, vous pouvez également associer ces applets de commande à des applets de commande d'autres services Azure, afin d'automatiser des tâches complexes entre des services Azure et des systèmes tiers.

Voici quelques exemples de gestion d’App Services avec Automation :
* [Scripts de gestion d’applications Web](https://azure.microsoft.com/documentation/scripts/)

## Étapes suivantes

Maintenant que vous connaissez les bases d’Azure Automation et que vous savez l’utiliser pour gérer une application web Azure, cliquez sur les liens ci-dessous pour en savoir plus.

* Consultez le [Didacticiel de prise en main](https://azure.microsoft.com/documentation/learning-paths/automation/) d’Azure Automation

<!---HONumber=AcomDC_0511_2016-->