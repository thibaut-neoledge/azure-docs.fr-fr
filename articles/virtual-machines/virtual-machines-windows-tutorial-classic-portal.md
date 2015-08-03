<properties
	pageTitle="Création d'une machine virtuelle exécutant Windows dans Azure"
	description="Apprenez à créer une machine virtuelle Windows dans le Portail de gestion Azure."
	services="virtual-machines"
	documentationCenter=""
	authors="KBDAzure"
	manager="timlt"
	editor=""/>

<tags
	ms.service="virtual-machines"
	ms.workload="infrastructure-services"
	ms.tgt_pltfrm="vm-windows"
	ms.devlang="na"
	ms.topic="article"
	ms.date="06/05/2015"
	ms.author="kathydav"/>

# Création d’une machine virtuelle exécutant Windows dans le Portail de gestion Azure

> [AZURE.SELECTOR]
- [Azure Preview Portal](virtual-machines-windows-tutorial.md)
- [Azure Portal](virtual-machines-windows-tutorial-classic-portal.md)
- [PowerShell - Resource Management](virtual-machines-deploy-rmtemplates-powershell.md)
- [PowerShell - Service Management](virtual-machines-ps-create-preconfigure-windows-vms.md)


Ce didacticiel vous montre combien il est facile de créer une machine virtuelle Azure dans le Portail de gestion Azure. Nous allons utiliser une image Windows Server comme exemple, mais il s’agit simplement d’un des nombreux types d’images proposés par Azure. Notez que votre choix en matière d’images dépend de votre abonnement. Par exemple, les images de bureau sont disponibles pour les abonnés MSDN.

Vous pouvez également créer des machines virtuelles en utilisant [vos propres images](virtual-machines-create-upload-vhd-windows-server.md). Pour en savoir plus à ce sujet et connaître d’autres méthodes, consultez la page [Les différentes façons de créer une machine virtuelle Windows](virtual-machines-windows-choices-create-vm.md).

[AZURE.INCLUDE [free-trial-note](../../includes/free-trial-note.md)]

## <a id="createvirtualmachine"> </a>Comment créer la machine virtuelle

Ce didacticiel vous montre comment utiliser l’option **À partir de la galerie** dans le Portail de gestion Azure pour créer la machine virtuelle. Cette option propose davantage de possibilités de configuration que l'option **Création rapide**. Par exemple, si vous voulez associer une machine virtuelle à un réseau virtuel, vous devez utiliser l'option **À partir de la galerie**.

> [AZURE.NOTE]Vous pouvez également faire un essai avec le [Portail Azure en version préliminaire](https://portal.azure.com), plus riche et personnalisable, pour créer une machine virtuelle, automatiser le déploiement de modèles d’application sur plusieurs machines virtuelles, utiliser les fonctions avancées de surveillance et de diagnostic des machines virtuelles, etc. Bien qu’il existe des recoupements importants entre les options de configuration des machines virtuelles disponibles dans les deux portails, ces options ne sont pas identiques.

[AZURE.INCLUDE [virtual-machines-create-WindowsVM](../../includes/virtual-machines-create-WindowsVM.md)]

## Étapes suivantes

- Connectez-vous à votre machine virtuelle. Pour plus d’informations, consultez [Connexion à une machine virtuelle exécutant Windows Server](virtual-machines-log-on-windows-server.md).

- Attacher un disque pour stocker des données. Vous pouvez attacher des disques, qu'ils soient vides ou non. Pour obtenir des instructions, consultez le [didacticiel Attacher un disque de données](storage-windows-attach-disk.md).

## Ressources supplémentaires

Pour en savoir plus sur ce que vous pouvez configurer pour une machine virtuelle et le moment où vous pouvez le faire, consultez [À propos des paramètres de configuration de la machine virtuelle Azure](http://msdn.microsoft.com/library/azure/dn763935.aspx).
 

<!---HONumber=July15_HO4-->