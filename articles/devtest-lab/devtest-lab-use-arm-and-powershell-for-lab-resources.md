---
title: "Créer ou modifier des laboratoires automatiquement à l’aide de modèles Azure Resource Manager et PowerShell | Microsoft Docs"
description: "Découvrez comment utiliser des modèles Azure Resource Manager avec PowerShell pour créer ou modifier des laboratoires automatiquement dans un DevTest Lab"
services: devtest-lab,virtual-machines,visual-studio-online
documentationcenter: na
author: tomarcher
manager: douge
editor: 
ms.assetid: dad9944c-0b20-48be-ba80-8f4aa0950903
ms.service: devtest-lab
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 03/21/2017
ms.author: tarcher
translationtype: Human Translation
ms.sourcegitcommit: db7cb109a0131beee9beae4958232e1ec5a1d730
ms.openlocfilehash: 8f54553bc60d6ba374462d87285dfaf4ca6395c5
ms.lasthandoff: 04/18/2017


---

# <a name="create-or-modify-labs-automatically-using-azure-resource-manager-templates-and-powershell"></a>Créer ou modifier des laboratoires automatiquement à l’aide de modèles Azure Resource Manager et PowerShell

DevTest Labs fournit de nombreux modèles Azure Resource Manager et scripts PowerShell qui peuvent vous aider à créer rapidement et automatiquement de nouveaux laboratoires ou à modifier des laboratoires existants, puis à déployer ces ressources.

Cet article vous guide dans le processus d’utilisation de ces modèles et scripts pour automatiser la création, la modification et le déploiement de vos laboratoires. Il vous montre également où trouver des informations supplémentaires sur l’utilisation de PowerShell pour effectuer certaines tâches courantes dans DevTest Labs.

## <a name="step-1-gather-your-templates-and-scripts"></a>Étape 1 : rassembler vos modèles et scripts
Vous pouvez prédéfinir des [modèles Azure Resource Manager](https://github.com/Azure/azure-devtestlab/tree/master/ARMTemplates) et [scripts PowerShell](https://github.com/Azure/azure-devtestlab/tree/master/Scripts) dans notre [référentiel Github](https://github.com/Azure/azure-devtestlab) public. Utilisez-les tels quels ou adaptez-les à vos besoins et stockez-les dans votre propre [référentiel Git privé](devtest-lab-add-repo.md). 

## <a name="step-2-modify-your-azure-resource-manager-template"></a>Étape 2 : modifier votre modèle Azure Resource Manager
[Créer des environnements de plusieurs machines virtuelles et des ressources PaaS avec les modèles Azure Resource Manager](devtest-lab-create-environment-from-arm.md) montre comment utiliser des modèles Azure Resource Manager dans des DevTest Labs pour définir l’infrastructure et la configuration de votre solution Azure, et déployer à maintes reprises plusieurs machines virtuelles dans un état cohérent.

Par exemple, si vous avez créé un réseau virtuel et voulu l’appliquer à tous vos laboratoires existants, vous pouvez le faire rapidement en utilisant un modèle Azure Resource Manager.

## <a name="step-3-deploy-resources-with-powershell"></a>Étape 3 : déployer des ressources avec PowerShell
Après avoir personnalisé vos modèle et scripts, suivez les étapes nécessaires pour [Déployer des ressources à l’aide de modèles Resource Manager et d’Azure PowerShell](https://docs.microsoft.com/azure/azure-resource-manager/resource-group-template-deploy). Cet article fournit des informations générales sur l’utilisation d’Azure PowerShell avec des modèles Azure Resource Manager pour déployer vos ressources sur Azure.


## <a name="common-tasks-you-can-perform-in-devtest-labs-using-powershell"></a>Tâches courantes que vous pouvez effectuer dans DevTest Labs à l’aide de PowerShell
Il existe de nombreuses autres tâches courantes que vous pouvez automatiser à l’aide de PowerShell. Les sections suivantes de la documentation décrivent les étapes requises pour effectuer ces tâches.

* [Créer une image personnalisée à partir d’un fichier de disque dur virtuel à l’aide de PowerShell](devtest-lab-create-custom-image-from-vhd-using-powershell.md)
* [Charger le fichier de disque dur virtuel sur le compte de stockage du laboratoire à l’aide de PowerShell](devtest-lab-upload-vhd-using-powershell.md)
* [Ajouter un utilisateur externe à un laboratoire à l’aide de PowerShell](devtest-lab-add-devtest-user.md#add-an-external-user-to-a-lab-using-powershell)
* [Créer un rôle personnalisé de laboratoire à l’aide de PowerShell](devtest-lab-grant-user-permissions-to-specific-lab-policies.md#creating-a-lab-custom-role-using-powershell)

### <a name="next-steps"></a>Étapes suivantes
* Découvrez comment créer un [référentiel Git privé](devtest-lab-add-repo.md) dans lequel stocker vos modèles ou scripts personnalisés.
* Explorez les [modèles Azure Resource Manager de la galerie de modèles de démarrage rapide Azure](https://github.com/Azure/azure-quickstart-templates).

