---
title: "Déployer des modèles en ligne de commande dans Azure Stack | Microsoft Docs"
description: "Découvrez comment utiliser l’interface de ligne de commande (CLI) multiplateforme pour déployer des modèles sur Azure Stack."
services: azure-stack
documentationcenter: 
author: heathl17
manager: byronr
editor: 
ms.assetid: 9584177f-4af3-4834-864d-930b09ae0995
ms.service: azure-stack
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 09/25/2017
ms.author: helaw
ms.translationtype: HT
ms.sourcegitcommit: c3a2462b4ce4e1410a670624bcbcec26fd51b811
ms.openlocfilehash: d58d80d89bb2544c4d4a34f608177a96760406ba
ms.contentlocale: fr-fr
ms.lasthandoff: 09/25/2017

---
# <a name="deploy-templates-in-azure-stack-using-the-command-line"></a>Déployer des modèles dans Azure Stack à l’aide de la ligne de commande

*S’applique à : systèmes intégrés Azure Stack et Kit de développement Azure Stack*

Utilisez la ligne de commande pour déployer des modèles Azure Resource Manager dans le kit de développement Azure Stack. Les modèles Azure Resource Manager déploient et approvisionnent toutes les ressources de l’application en une seule opération coordonnée.

## <a name="before-you-begin"></a>Avant de commencer
 - [Procédez à l’installation et à la connexion](azure-stack-connect-cli.md) à Azure Stack avec Azure CLI.
 - Télécharger les fichiers *azuredeploy.json* et *azuredeploy.parameters.json* à partir du [modèle Créer un exemple de compte de stockage](https://github.com/Azure/AzureStack-QuickStart-Templates/tree/master/101-create-storage-account).
 
## <a name="deploy-template"></a>Déployer un modèle
Accédez au dossier dans lequel ces fichiers ont été téléchargés et exécutez la commande suivante pour déployer le modèle :

    azure group create "cliRG" "local" –f azuredeploy.json –d "testDeploy" –e azuredeploy.parameters.json

Cette commande déploie le modèle dans le groupe de ressources **cliRG** à l’emplacement par défaut d’Azure Stack POC.

## <a name="validate-template-deployment"></a>Valider le déploiement du modèle
Pour voir ce groupe de ressources et le compte de stockage associé, utilisez les commandes suivantes :

    azure group list

    azure storage account list





