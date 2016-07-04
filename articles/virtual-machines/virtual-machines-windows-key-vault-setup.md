<properties
	pageTitle="Configuration de Key Vault pour des machines virtuelles dans Azure Resource Manager | Microsoft Azure"
	description="Comment configurer Key Vault pour une utilisation avec une machine virtuelle Azure Resource Manager."
	services="virtual-machines-windows"
	documentationCenter=""
	authors="singhkay"
	manager="drewm"
	editor=""
	tags="azure-resource-manager"/>

<tags
	ms.service="virtual-machines-windows"
	ms.workload="infrastructure-services"
	ms.tgt_pltfrm="vm-windows"
	ms.devlang="na"
	ms.topic="article"
	ms.date="05/31/2016"
	ms.author="singhkay"/>

# Configuration de Key Vault pour des machines virtuelles dans Azure Resource Manager

[AZURE.INCLUDE [learn-about-deployment-models](../../includes/learn-about-deployment-models-rm-include.md)]modèle de déploiement classique

Dans la pile Azure Resource Manager, les secrets/certificats sont modélisés en tant que ressources fournies par le fournisseur de ressources de Key Vault. Pour en savoir plus sur les coffres de clés, consultez [Qu’est-ce qu’Azure Key Vault ?](../key-vault/key-vault-whatis.md)

Pour que Key Vault puisse être utilisé avec des machines virtuelles Azure Resource Manager, la propriété *EnabledForDeployment* doit être définie sur true dans Key Vault. Vous pouvez le faire dans différents clients.

## Utilisation de PowerShell pour configurer Key Vault
Pour créer un coffre de clés à l’aide de PowerShell, consultez [Prise en main d’Azure Key Vault](../key-vault/key-vault-get-started.md#vault).

Pour de nouveaux coffres de clé, vous pouvez utiliser l’applet de commande PowerShell suivante :

	New-AzureRmKeyVault -VaultName 'ContosoKeyVault' -ResourceGroupName 'ContosoResourceGroup' -Location 'East Asia' -EnabledForDeployment

Pour des coffres de clé existants, vous pouvez utiliser l’applet de commande PowerShell suivante :

	Set-AzureRmKeyVaultAccessPolicy -VaultName 'ContosoKeyVault' -EnabledForDeployment

## Utilisation de l’interface de ligne de commande pour configurer Key Vault
Pour créer un coffre de clés à l’aide de l’interface de ligne de commande (CLI), consultez la rubrique [Gestion de Key Vault à l’aide de l’interface de ligne de commande (CLI)](../key-vault/key-vault-manage-with-cli.md#create-a-key-vault).

Pour l’interface de ligne de commande, vous devez créer d’abord le coffre de clés avant d’affecter la stratégie de déploiement. Pour ce faire, vous pouvez utiliser la commande suivante :

	azure keyvault set-policy ContosoKeyVault –enabled-for-deployment true

## Utilisation de modèles pour configurer Key Vault
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

<!---HONumber=AcomDC_0622_2016-->