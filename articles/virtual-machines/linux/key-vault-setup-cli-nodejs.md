---
title: Configurer Key Vault pour les machines virtuelles Linux avec Azure CLI 1.0 | Microsoft Docs
description: "Configuration de Key Vault pour une utilisation avec une machine virtuelle Azure Resource Manager au moyen d’Azure CLI 1.0."
services: virtual-machines-linux
documentationcenter: 
author: singhkays
manager: timlt
editor: 
tags: azure-resource-manager
ms.assetid: bccdd5ab-5ccf-4760-9039-92c6eafb15bd
ms.service: virtual-machines-linux
ms.workload: infrastructure-services
ms.tgt_pltfrm: vm-linux
ms.devlang: na
ms.topic: article
ms.date: 02/24/2017
ms.author: singhkay
translationtype: Human Translation
ms.sourcegitcommit: be3ac7755934bca00190db6e21b6527c91a77ec2
ms.openlocfilehash: fed612a354d45f34619f2a66bd40d78740c43ac7
ms.lasthandoff: 05/03/2017


---
# <a name="set-up-key-vault-for-virtual-machines-in-azure-resource-manager-with-the-azure-cli-10"></a>Configurer Key Vault pour des machines virtuelles dans Azure Resource Manager avec Azure CLI 1.0
Dans la pile Azure Resource Manager, les secrets/certificats sont modélisés en tant que ressources fournies par le fournisseur de ressources de Key Vault. Pour en savoir plus sur Azure Key Vault, consultez [Qu’est-ce qu’Azure Key Vault ?](../../key-vault/key-vault-whatis.md) Pour que Key Vault puisse être utilisé avec des machines virtuelles Azure Resource Manager, la propriété *EnabledForDeployment* doit être définie sur true dans Key Vault. Vous pouvez le faire dans différents clients. Cet article explique comment configurer Key Vault pour une utilisation avec des machines virtuelles Azure.

## <a name="cli-versions-to-complete-the-task"></a>Versions de l’interface de ligne de commande permettant d’effectuer la tâche
Vous pouvez exécuter la tâche en utilisant l’une des versions suivantes de l’interface de ligne de commande

- [Azure CLI 1.0](#quick-commands) : notre interface de ligne de commande pour les modèles de déploiement Classique et Resource Manager (cet article)
- [Azure CLI 2.0](../windows/key-vault-setup.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json) : notre interface de ligne de commande nouvelle génération pour le modèle de déploiement Resource Manager

## <a name="use-cli-10-to-set-up-key-vault"></a>Utiliser CLI 1.0 pour configurer Key Vault
Pour créer un coffre de clés à l’aide de l’interface de ligne de commande (CLI), consultez la rubrique [Gestion de Key Vault à l’aide de l’interface de ligne de commande (CLI)](../../key-vault/key-vault-manage-with-cli2.md#create-a-key-vault).

Pour CLI 1.0, vous devez créer le coffre de clés avant d’attribuer la stratégie de déploiement. Vous pouvez ensuite attribuer la stratégie à l’aide de la commande suivante :

    azure keyvault set-policy ContosoKeyVault –enabled-for-deployment true

## <a name="use-templates-to-set-up-key-vault"></a>Utilisation de modèles pour configurer Key Vault
Quand vous utilisez un modèle, vous devez définir la propriété `enabledForDeployment` sur `true` pour la ressource Key Vault.

    {
      "type": "Microsoft.KeyVault/vaults",
      "name": "ContosoKeyVault",
      "apiVersion": "2015-06-01",
      "location": "<location-of-key-vault>",
      "properties": {
        "enabledForDeployment": "true",
        ....
        ....
      }
    }

Pour les autres options que vous pouvez configurer lorsque vous créez un coffre de clés à l’aide de modèles, consultez la rubrique [Création d’un coffre de clés](https://azure.microsoft.com/documentation/templates/101-key-vault-create/).

