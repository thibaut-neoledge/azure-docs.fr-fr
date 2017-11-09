---
title: "Gestion d’Azure Key Vault avec Azure Automation | Microsoft Docs"
description: "Découvrez comment utiliser le service Azure Automation pour gérer Azure Key Vault."
services: Key-Vault, automation
documentationcenter: 
author: mgoedtel
manager: jwhit
editor: 
ms.assetid: 4e780762-19b6-4ca6-b894-ebb44c538f35
ms.service: key-vault
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 07/29/2016
ms.author: magoedte;csand
ms.openlocfilehash: dee39662472fe54776b591977f2b1ecb39d15b00
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 10/11/2017
---
# <a name="managing-azure-key-vault-using-azure-automation"></a>Gestion d'Azure Key Vault avec Azure Automation
Ce guide vous présente le service Azure Automation et la manière de l'utiliser pour gérer plus simplement vos clés et secrets dans Azure Key Vault.

## <a name="what-is-azure-automation"></a>Qu'est-ce qu'Azure Automation ?
[Azure Automation](../automation/automation-intro.md) est un service Azure destiné à simplifier la gestion du cloud via l'automatisation des processus et la configuration de l’état souhaité. Il automatise les tâches manuelles, répétitives, fastidieuses et sources d’erreurs pour accroître la fiabilité, l’efficacité et le retour sur investissement de votre organisation.

Azure Automation fournit un moteur d’exécution de workflow à haute fiabilité et à haute disponibilité, qui s’adapte à vos besoins. Dans Azure Automation, les processus peuvent être lancés manuellement, par des systèmes tiers ou à des intervalles planifiés, afin que les tâches interviennent exactement au moment opportun.

Réduisez les coûts opérationnels et libérez du temps pour que votre équipe d’informaticiens et de développeurs puisse se concentrer sur des tâches qui génèrent une valeur ajoutée pour l’entreprise, en déléguant l’exécution automatique de vos tâches de gestion de Cloud à Azure Automation.

## <a name="how-can-azure-automation-help-manage-azure-key-vault"></a>Comment Azure Automation peut-il aider à gérer Azure Key Vault ?
Key Vault peut être géré dans Azure Automation à l’aide des [applets de commande AzureRM Key Vault](https://www.powershellgallery.com/packages/AzureRM.KeyVault/1.1.4) et des [applets de commande Azure Classic Key Vault](https://msdn.microsoft.com/library/azure/dn868052.aspx). Le module Azure pour la gestion de Classic Key Vault est disponible automatiquement dans Azure Automation. Vous pouvez importer le [module AzureRM-KeyVault](https://www.powershellgallery.com/packages/AzureRM.KeyVault/1.1.4) dans Azure Automation pour pouvoir effectuer la plupart de vos tâches de gestion Key Vault dans le service. Dans Azure Automation, vous pouvez également associer ces applets de commande à des applets de commande d'autres services Azure, afin d'automatiser des tâches complexes entre des services Azure et des systèmes tiers.

Les applets de commande Azure Key Vault vous permettent d’effectuer, entre autres, ces tâches : 

* Création et configuration d’un coffre de clés
* Création ou importation d’une clé
* Création ou mise à jour d’une clé secrète
* Mise à jour des attributs d’une clé
* Obtention d’une clé ou d’une clé secrète
* Suppression d’une clé ou d’une clé secrète

Voici quelques exemples d’utilisation de PowerShell pour Key Vault :  

* [Azure Key Vault – Étape par étape](https://blogs.technet.microsoft.com/kv/2015/06/02/azure-key-vault-step-by-step)
* [Setting Up and Configuring an Azure Key Vault (Installation et configuration d’Azure Key Vault)](https://www.simple-talk.com/cloud/platform-as-a-service/setting-up-and-configuring-an-azure-key-vault)

## <a name="next-steps"></a>Étapes suivantes
Maintenant que vous connaissez les bases d’Azure Automation et que vous savez l’utiliser pour gérer Azure Key Vault, cliquez sur les liens ci-dessous pour en savoir plus.

* Consultez le [didacticiel de mise en route](../automation/automation-first-runbook-graphical.md)d'Azure Automation.
* Consultez les [scripts PowerShell pour Azure Key Vault](https://gallery.technet.microsoft.com/scriptcenter/site/search?query=azure%20key%20vault&f%5B0%5D.Value=azure%20key%20vault&f%5B0%5D.Type=SearchText&ac=5).

