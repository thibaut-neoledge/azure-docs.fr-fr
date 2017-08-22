---
title: "Gestion d’une application web Azure à l’aide d’Azure Automation | Microsoft Docs"
description: "Découvrez comment utiliser le service Azure Automation pour gérer une application web Azure."
services: app-service\web, automation
documentationcenter: 
author: mgoedtel
manager: jwhit
editor: 
ms.assetid: c960a44f-f941-401d-afba-a4bc0f0394eb
ms.service: app-service-web
ms.workload: web
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 07/29/2016
ms.author: magoedte
ms.translationtype: Human Translation
ms.sourcegitcommit: 4edd2696c9a5709ded6e2a3e352090775335f0d2
ms.openlocfilehash: 4fcfa2e7ec2e8257407026ed4cca0e15fd0b5bb6
ms.contentlocale: fr-fr
ms.lasthandoff: 02/16/2017

---
# <a name="managing-azure-web-app-using-azure-automation"></a>Gestion d’une application web Azure à l’aide d’Azure Automation
Ce guide vous présente le service Azure Automation et décrit comment l’utiliser pour simplifier la gestion d’une application web Azure.

## <a name="what-is-azure-automation"></a>Qu'est-ce qu'Azure Automation ?
[Azure Automation](../automation/automation-intro.md) est un service Azure destiné à simplifier la gestion du cloud via l'automatisation des processus. Il automatise les tâches manuelles, répétitives, fastidieuses et sources d’erreurs pour accroître la fiabilité, l’efficacité et le retour sur investissement de votre organisation.

Azure Automation fournit un moteur d’exécution de workflow à haute fiabilité et à haute disponibilité, qui s’adapte à vos besoins. Dans Azure Automation, les processus peuvent être lancés manuellement, par des systèmes tiers ou à des intervalles planifiés, afin que les tâches interviennent exactement au moment opportun.

Réduisez les coûts opérationnels et libérez du temps pour que votre équipe d’informaticiens et de développeurs puisse se concentrer sur des tâches qui génèrent une valeur ajoutée pour l’entreprise, en déléguant l’exécution automatique de vos tâches de gestion de Cloud à Azure Automation.

## <a name="how-can-azure-automation-help-manage-azure-web-app"></a>Comment Azure Automation peut-il vous aider à gérer une application web Azure ?
Dans Azure Automation, une application web est gérée à l’aide d’applets de commande PowerShell qui sont disponibles dans les [modules Azure PowerShell](/powershell/azureps-cmdlets-docs). Vous pouvez [installer ces applets de commande PowerShell dans Azure Automation](https://azure.microsoft.com/blog/announcing-azure-resource-manager-support-azure-automation-runbooks/)afin d’exécuter toutes vos tâches de gestion d’application Web dans ce service. Dans Azure Automation, vous pouvez également associer ces applets de commande à des applets de commande d'autres services Azure, afin d'automatiser des tâches complexes entre des services Azure et des systèmes tiers.

Voici quelques exemples de gestion d’App Services avec Automation :

* [Scripts de gestion d’applications Web](https://azure.microsoft.com/documentation/scripts/)

## <a name="next-steps"></a>Étapes suivantes
Maintenant que vous connaissez les bases d’Azure Automation et que vous savez l’utiliser pour gérer une application web Azure, cliquez sur les liens ci-dessous pour en savoir plus.

* Consultez le [Didacticiel de prise en main](../automation/automation-first-runbook-graphical.md)


