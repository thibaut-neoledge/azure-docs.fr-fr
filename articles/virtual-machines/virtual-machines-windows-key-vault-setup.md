---
title: Configuration de Key Vault pour des machines virtuelles Windows dans Azure Resource Manager | Microsoft Docs
description: Comment configurer Key Vault pour une utilisation avec une machine virtuelle Azure Resource Manager.
services: virtual-machines-windows
documentationcenter: 
author: singhkays
manager: timlt
editor: 
tags: azure-resource-manager
ms.assetid: 33a483e2-cfbc-4c62-a588-5d9fd52491e2
ms.service: virtual-machines-windows
ms.workload: infrastructure-services
ms.tgt_pltfrm: vm-windows
ms.devlang: na
ms.topic: article
ms.date: 01/24/2017
ms.author: kasing
translationtype: Human Translation
ms.sourcegitcommit: bcb347e8a2ebef14100e990ef71eac5362de5a48
ms.openlocfilehash: 74124056ea77dc1007fb87853ceb10622c9ceaea


---
# <a name="set-up-key-vault-for-virtual-machines-in-azure-resource-manager"></a>Configuration de Key Vault pour des machines virtuelles dans Azure Resource Manager

[!INCLUDE [learn-about-deployment-models](../../includes/learn-about-deployment-models-rm-include.md)]

Dans la pile Azure Resource Manager, les secrets/certificats sont modélisés en tant que ressources fournies par le fournisseur de ressources de Key Vault. Pour en savoir plus sur les coffres de clés, consultez [Qu’est-ce qu’Azure Key Vault ?](../key-vault/key-vault-whatis.md)

> [!NOTE]
> 1. Pour que Key Vault puisse être utilisé avec des machines virtuelles Azure Resource Manager, la propriété **EnabledForDeployment** doit être définie sur true dans Key Vault. Vous pouvez le faire dans différents clients.
> 2. Le coffre de clés doit être créé dans le même abonnement et le même emplacement que la machine virtuelle.
> 
> 

## <a name="use-powershell-to-set-up-key-vault"></a>Utilisation de PowerShell pour configurer Key Vault
Pour créer un coffre de clés à l’aide de PowerShell, consultez [Prise en main d’Azure Key Vault](../key-vault/key-vault-get-started.md#vault).

Pour de nouveaux coffres de clé, vous pouvez utiliser l’applet de commande PowerShell suivante :

    New-AzureRmKeyVault -VaultName 'ContosoKeyVault' -ResourceGroupName 'ContosoResourceGroup' -Location 'East Asia' -EnabledForDeployment

Pour des coffres de clé existants, vous pouvez utiliser l’applet de commande PowerShell suivante :

    Set-AzureRmKeyVaultAccessPolicy -VaultName 'ContosoKeyVault' -EnabledForDeployment

## <a name="us-cli-to-set-up-key-vault"></a>Utilisation de l’interface de ligne de commande pour configurer Key Vault
Pour créer un coffre de clés à l’aide de l’interface de ligne de commande (CLI), consultez la rubrique [Gestion de Key Vault à l’aide de l’interface de ligne de commande (CLI)](../key-vault/key-vault-manage-with-cli.md#create-a-key-vault).

Pour l’interface de ligne de commande, vous devez créer d’abord le coffre de clés avant d’affecter la stratégie de déploiement. Pour ce faire, vous pouvez utiliser la commande suivante :

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




<!--HONumber=Jan17_HO4-->


