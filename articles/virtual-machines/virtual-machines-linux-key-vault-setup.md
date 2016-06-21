<properties
	pageTitle="Configuration du coffre de clés pour les machines virtuelles dans Azure Resource Manager | Microsoft Azure"
	description="Comment configurer un coffre de clés pour une utilisation avec une machine virtuelle Azure Resource Manager"
	services="virtual-machines-linux"
	documentationCenter=""
	authors="singhkay"
	manager="drewm"
	editor=""
	tags="azure-resource-manager"/>

<tags
	ms.service="virtual-machines-linux"
	ms.workload="infrastructure-services"
	ms.tgt_pltfrm="vm-linux"
	ms.devlang="na"
	ms.topic="article"
	ms.date="05/31/2016"
	ms.author="singhkay"/>

# Configuration du coffre de clés pour les machines virtuelles dans Azure Resource Manager

[AZURE.INCLUDE [learn-about-deployment-models](../../includes/learn-about-deployment-models-rm-include.md)]modèle de déploiement classique

Dans la pile Azure Resource Manager, les secrets/certificats sont modélisés en tant que ressources fournies par le fournisseur de ressources du coffre de clés. Pour en savoir plus sur les coffres de clés, consultez [Qu’est-ce qu’Azure Key Vault ?](../key-vault/key-vault-whatis.md)

## Paramétrage
Pour qu’un coffre de clés puisse être utilisé avec les machines virtuelles Azure Resource Manager, la propriété *EnabledForDeployment* sur le coffre de clés doit être définie sur true. Vous pouvez faire cela dans les différents clients comme indiqué ci-dessous. »

## Interface de ligne de commande
Pour créer le coffre de clés à l’aide de la CLI, consultez [Gérer le coffre de clés à l’aide de la CLI](../key-vault/key-vault-manage-with-cli.md#create-a-key-vault)

Pour l’interface CLI, vous devez créez d’abord le coffre de clés, puis activer la stratégie de déploiement. Pour ce faire, vous pouvez utiliser la commande suivante

	azure keyvault set-policy ContosoKeyVault –enabled-for-deployment true

## Modèles
Quand vous utilisez des modèles, vous devez définir la propriété `enabledForDeployment` sur `true` pour la ressource du coffre de clés.

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

Pour voir d’autres options que vous pouvez configurer lors de la création d’un coffre de clés avec des modèles, cliquez [ici](https://azure.microsoft.com/documentation/templates/101-key-vault-create/)

<!---HONumber=AcomDC_0608_2016-->