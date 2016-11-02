<properties
	pageTitle="Déplacement d’une machine virtuelle Linux | Microsoft Azure"
	description="Déplacez une machine virtuelle Linux vers un autre abonnement ou groupe de ressources Azure dans le modèle de déploiement Resource Manager."
	services="virtual-machines-linux"
	documentationCenter=""
	authors="cynthn"
	manager="timlt"
	editor=""
	tags="azure-resource-manager"/>

<tags
	ms.service="virtual-machines-linux"
	ms.workload="infrastructure-services"
	ms.tgt_pltfrm="na"
	ms.devlang="na"
	ms.topic="article"
	ms.date="08/08/2016"
	ms.author="cynthn"/>

	


# Déplacer une machine virtuelle Linux vers un autre abonnement ou groupe de ressources

Cet article vous guide tout au long du déplacement d’une machine virtuelle Linux entre des groupes de ressources ou des abonnements. Le déplacement d’une machine virtuelle entre abonnements peut être pratique si vous avez créé une machine virtuelle dans un abonnement personnel, et que vous souhaitez à présent la déplacer vers l’abonnement de votre entreprise.

> [AZURE.NOTE] De nouveaux ID de ressource sont créés dans le cadre du déplacement. Une fois que la machine virtuelle a été déplacée, vous devez mettre à jour vos outils et vos scripts pour utiliser les nouveaux ID de ressource.


## Utiliser l’interface CLI Azure pour déplacer une machine virtuelle 

Pour déplacer avec succès une machine virtuelle, vous devez déplacer la machine virtuelle et toutes les ressources qui l’accompagnent. Utilisez la commande **azure group show** pour répertorier toutes les ressources d’un groupe de ressources et leur ID. Cela permet de diriger la sortie de cette commande dans un fichier. Vous pouvez ainsi copier et coller les ID dans des commandes ultérieures.

	azure group show <resourceGroupName>

Pour déplacer une machine virtuelle et ses ressources vers un autre groupe de ressources, exécutez la commande CLI **azure resource move**. L’exemple suivant montre comment déplacer une machine virtuelle et les ressources courantes nécessaires. Nous utilisons le paramètre **-i** et transmettons une liste séparée par des virgules (sans espaces) d’ID de ressources à déplacer.

	
    vm=/subscriptions/<sourceSubscriptionID>/resourceGroups/<sourceResourceGroup>/providers/Microsoft.Compute/virtualMachines/<vmName>
	nic=/subscriptions/<sourceSubscriptionID>/resourceGroups/<sourceResourceGroup>/providers/Microsoft.Network/networkInterfaces/<nicName>
	nsg=/subscriptions/<sourceSubscriptionID>/resourceGroups/<sourceResourceGroup>/providers/Microsoft.Network/networkSecurityGroups/<nsgName>
	pip=/subscriptions/<sourceSubscriptionID>/resourceGroups/<sourceResourceGroup>/providers/Microsoft.Network/publicIPAddresses/<publicIPName>
	vnet=/subscriptions/<sourceSubscriptionID>/resourceGroups/<sourceResourceGroup>/providers/Microsoft.Network/virtualNetworks/<vnetName>
	diag=/subscriptions/<sourceSubscriptionID>/resourceGroups/<sourceResourceGroup>/providers/Microsoft.Storage/storageAccounts/<diagnosticStorageAccountName>
	storage=/subscriptions/<sourceSubscriptionID>/resourceGroups/<sourceResourceGroup>/providers/Microsoft.Storage/storageAccounts/<storageAcountName>  	
	
	azure resource move --ids $vm,$nic,$nsg,$pip,$vnet,$storage,$diag -d "<destinationResourceGroup>"
	
Si vous souhaitez déplacer la machine virtuelle et ses ressources vers un autre abonnement, ajoutez le paramètre **--destination-subscriptionId &#60;destinationSubscriptionID&#62;** pour spécifier l’abonnement de destination.

Si vous travaillez sur l’invite de commandes d’un ordinateur Windows, vous devez ajouter un **$** devant les noms de variables lorsque vous les déclarez. Cela n’est pas nécessaire sous Linux.

Vous devez confirmer que vous souhaitez déplacer la ressource spécifiée. Tapez **Y** pour confirmer que vous souhaitez déplacer les ressources.
	

[AZURE.INCLUDE [virtual-machines-common-move-vm](../../includes/virtual-machines-common-move-vm.md)]

## Étapes suivantes

Vous pouvez déplacer de nombreux types de ressources différents entre les groupes de ressources et les abonnements. Pour plus d’informations, consultez la page [Déplacement de ressources vers un nouveau groupe de ressources ou un abonnement](../resource-group-move-resources.md).

<!---HONumber=AcomDC_0810_2016-->