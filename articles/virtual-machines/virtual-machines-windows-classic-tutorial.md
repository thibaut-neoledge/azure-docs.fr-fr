<properties
	pageTitle="Créer une machine virtuelle exécutant Windows dans le portail classique | Microsoft Azure"
	description="Création d'une machine virtuelle Windows dans le portail Azure Classic."
	services="virtual-machines-windows"
	documentationCenter=""
	authors="cynthn"
	manager="timlt"
	editor=""
	tags="azure-service-management"/>

<tags
	ms.service="virtual-machines-windows"
	ms.workload="infrastructure-services"
	ms.tgt_pltfrm="vm-windows"
	ms.devlang="na"
	ms.topic="article"
	ms.date="07/12/2016"
	ms.author="cynthn"/>

# Créer une machine virtuelle exécutant Windows dans le portail Azure Classic

> [AZURE.SELECTOR]
- [Portail Azure Classic](virtual-machines-windows-classic-tutorial.md)
- [PowerShell : Déploiement classique](virtual-machines-windows-classic-create-powershell.md)

<br>

[AZURE.INCLUDE [learn-about-deployment-models](../../includes/learn-about-deployment-models-classic-include.md)] Découvrez comment [effectuer ces étapes à l’aide du modèle de déploiement Resource Manager](virtual-machines-windows-hero-tutorial.md).

Ce didacticiel vous montre à quel point il est facile de créer une machine virtuelle Azure exécutant Windows dans le portail Azure Classic. Nous allons utiliser une image Windows Server comme exemple, mais il s’agit simplement d’un des nombreux types d’images proposés par Azure. Notez que votre choix en matière d’images dépend de votre abonnement. Par exemple, les images de bureau Windows peuvent être accessibles aux abonnés MSDN.

Cette section vous montre comment utiliser l’option **À partir de la galerie** dans le portail classique Azure pour créer la machine virtuelle. Cette option propose davantage de possibilités de configuration que l'option **Création rapide**. Par exemple, si vous voulez associer une machine virtuelle à un réseau virtuel, vous devez utiliser l'option **À partir de la galerie**.

Vous pouvez également créer des machines virtuelles en utilisant [vos propres images](virtual-machines-windows-classic-createupload-vhd.md). Pour en savoir plus à ce sujet et connaître d’autres méthodes, consultez [les différentes façons de créer une machine virtuelle Windows](virtual-machines-windows-creation-choices.md).



## Vidéo de procédure pas à pas

Voici une procédure pas à pas de ce didacticiel.

[AZURE.VIDEO creating-a-windows-vm-on-microsoft-azure-classic-portal]

## <a id="createvirtualmachine"> </a>Créer la machine virtuelle

[AZURE.INCLUDE [virtual-machines-create-WindowsVM](../../includes/virtual-machines-create-windowsvm.md)]

## Étapes suivantes

- Connectez-vous à votre machine virtuelle. Pour obtenir des instructions, consultez [Connexion à une machine virtuelle exécutant Windows Server](virtual-machines-windows-classic-connect-logon.md).

- Attacher un disque pour stocker des données. Vous pouvez attacher des disques, qu'ils soient vides ou non. Pour obtenir des instructions, consultez la page [Attacher un disque de données à une machine virtuelle Windows créée avec le modèle de déploiement Classic](virtual-machines-windows-classic-attach-disk.md).

<!---HONumber=AcomDC_0713_2016-->