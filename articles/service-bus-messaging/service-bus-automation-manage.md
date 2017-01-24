---
title: "Gérer Azure Service Bus à l’aide d’Azure Automation | Microsoft Docs"
description: "Découvrez comment utiliser le service Azure Automation pour gérer Azure Service Bus."
services: service-bus-messaging, automation
documentationcenter: 
author: mgoedtel
manager: jwhit
editor: 
ms.assetid: 2a06e94b-92ef-4b5d-a2b7-fe827063df82
ms.service: service-bus-messaging
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 01/10/2017
ms.author: magoedte;csand
translationtype: Human Translation
ms.sourcegitcommit: 994a379129bffd7457912bc349f240a970aed253
ms.openlocfilehash: 4817cda757b9f85f6225237f79293860c990ca88


---
# <a name="managing-azure-service-bus-using-azure-automation"></a>Gestion d'Azure Service Bus à l'aide d'Azure Automation
Ce guide vous présente le service Azure Automation et décrit comment l'utiliser pour simplifier la gestion d'une application Azure Service Bus.

## <a name="what-is-azure-automation"></a>Qu'est-ce qu'Azure Automation ?
[Azure Automation](../automation/automation-intro.md) est un service Azure destiné à simplifier la gestion du cloud via l'automatisation des processus et la configuration de l’état souhaité. Il automatise les tâches manuelles, répétitives, fastidieuses et sources d’erreurs pour accroître la fiabilité, l’efficacité et le retour sur investissement de votre organisation.

Azure Automation fournit un moteur d’exécution de workflow à haute fiabilité et à haute disponibilité, qui s’adapte à vos besoins. Dans Azure Automation, les processus peuvent être lancés manuellement, par des systèmes tiers ou à des intervalles planifiés, afin que les tâches interviennent exactement au moment opportun.

Réduisez les coûts opérationnels et libérez du temps pour que votre équipe d’informaticiens et de développeurs puisse se concentrer sur des tâches qui génèrent une valeur ajoutée pour l’entreprise, en déléguant l’exécution automatique de vos tâches de gestion de Cloud à Azure Automation.

## <a name="how-can-azure-automation-help-manage-azure-service-bus"></a>Comment Azure Automation peut-il vous aider à gérer Azure Service Bus ?
Vous pouvez gérer Service Bus avec Azure Automation à l'aide de l' [API REST Service Bus](https://docs.microsoft.com/rest/api/servicebus/). Azure Automation vous permet d’exécuter des scripts PowerShell pour effectuer la plupart de vos tâches Service Bus à l’aide de l’API REST. Vous pouvez également associer ces appels de l’API REST dans Azure Automation avec les applets de commande d'autres services Azure, afin d'automatiser des tâches complexes entre des services Azure et des systèmes tiers.

Voici quelques exemples d'utilisation de PowerShell pour gérer Azure Service Bus :

* [Applets de commande PowerShell personnalisées pour gérer des files d'attente Azure Service Bus](https://blogs.technet.microsoft.com/uktechnet/2014/12/04/sample-of-custom-powershell-cmdlets-to-manage-azure-servicebus-queues)
* [Comment créer des files d'attente, des rubriques et des abonnements Service Bus à l'aide d'un script PowerShell](http://blogs.msdn.com/b/paolos/archive/2014/12/02/how-to-create-a-service-bus-queues-topics-and-subscriptions-using-a-powershell-script.aspx)
* [Créer des espaces de noms Azure Service Bus à l'aide de PowerShell](http://buildazure.com/2015/09/24/create-azure-service-bus-namespaces-using-powershell-and-x-plat-cli/)

Le module PowerShell compatible avec Azure Service Bus dans les Runbooks Automation peut être téléchargé à partir de la [Galerie PowerShell](https://www.powershellgallery.com/packages/AzureServiceBusCreation/1.0).

## <a name="next-steps"></a>Étapes suivantes
Maintenant que vous avez appris les bases d'Azure Automation et la manière de l'utiliser pour gérer Azure Service Bus, cliquez sur les liens suivants pour en savoir plus sur Azure Automation.

* Consultez le [Didacticiel de prise en main](../automation/automation-first-runbook-graphical.md)
* Découvrez comment [manage Service Bus avec PowerShell](service-bus-powershell-how-to-provision.md)




<!--HONumber=Jan17_HO2-->


