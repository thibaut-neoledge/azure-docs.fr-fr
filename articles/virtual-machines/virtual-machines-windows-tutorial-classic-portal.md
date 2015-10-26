<properties
	pageTitle="Création d'une machine virtuelle exécutant Windows dans Azure"
	description="Création d'une machine virtuelle Windows dans le portail Azure."
	services="virtual-machines"
	documentationCenter=""
	authors="cynthn"
	manager="timlt"
	editor=""
	tags="azure-service-management"/>

<tags
	ms.service="virtual-machines"
	ms.workload="infrastructure-services"
	ms.tgt_pltfrm="vm-windows"
	ms.devlang="na"
	ms.topic="article"
	ms.date="08/11/2015"
	ms.author="cynthn"/>

# Créer une machine virtuelle exécutant Windows dans le portail Azure

> [AZURE.SELECTOR]
- [Azure preview portal](virtual-machines-windows-tutorial.md)
- [Azure portal](virtual-machines-windows-tutorial-classic-portal.md)
- [PowerShell: Resource Manager deployment](virtual-machines-deploy-rmtemplates-powershell.md)
- [PowerShell: Classic deployment](virtual-machines-ps-create-preconfigure-windows-vms.md)

[AZURE.INCLUDE [learn-about-deployment-models](../../includes/learn-about-deployment-models-classic-include.md)] [Resource Manager deployment model](virtual-machines-windows-tutorial.md).

Ce didacticiel vous montre combien il est facile de créer une machine virtuelle Azure dans le portail Azure. Nous allons utiliser une image Windows Server comme exemple, mais il s’agit simplement d’un des nombreux types d’images proposés par Azure. Notez que votre choix en matière d’images dépend de votre abonnement. Par exemple, les images de bureau sont disponibles pour les abonnés MSDN.

Vous pouvez également créer des machines virtuelles en utilisant [vos propres images](virtual-machines-create-upload-vhd-windows-server.md). Pour en savoir plus à ce sujet et connaître d’autres méthodes, consultez [les différentes façons de créer une machine virtuelle Windows](virtual-machines-windows-choices-create-vm.md).

[AZURE.INCLUDE [free-trial-note](../../includes/free-trial-note.md)]

## Vidéo de procédure pas à pas

Voici une procédure pas à pas de ce didacticiel.

[AZURE.VIDEO creating-a-windows-vm-on-microsoft-azure-classic-portal]

## <a id="createvirtualmachine"> </a>Comment créer la machine virtuelle

Cette section vous montre comment utiliser l’option **À partir de la galerie** dans le portail Azure pour créer la machine virtuelle. Cette option propose davantage de possibilités de configuration que l'option **Création rapide**. Par exemple, si vous voulez associer une machine virtuelle à un réseau virtuel, vous devez utiliser l'option **À partir de la galerie**.

> [AZURE.NOTE]Vous pouvez également faire un essai avec le [portail Azure en version préliminaire](https://portal.azure.com) pour créer une machine virtuelle, utiliser les fonctionnalités avancées de surveillance et de diagnostic des machines virtuelles, utiliser le stockage Premium, etc. Bien qu'il existe des recoupements importants entre les options de configuration d'une machine virtuelle disponibles dans les deux portails, ces options ne sont pas identiques. Par exemple, utilisez le portail en version préliminaire pour configurer une machine virtuelle avec le stockage Premium.

[AZURE.INCLUDE [virtual-machines-create-WindowsVM](../../includes/virtual-machines-create-windowsvm.md)]

## Étapes suivantes

- Connectez-vous à votre machine virtuelle. Pour plus d’informations, consultez [Connexion à une machine virtuelle exécutant Windows Server](virtual-machines-log-on-windows-server.md).

- Attacher un disque pour stocker des données. Vous pouvez attacher des disques, qu'ils soient vides ou non. Pour obtenir des instructions, consultez le [didacticiel Attacher un disque de données](storage-windows-attach-disk.md).

<!---HONumber=Oct15_HO3-->