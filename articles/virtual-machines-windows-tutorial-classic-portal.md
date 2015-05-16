<properties
	pageTitle="Création d&#39;une machine virtuelle exécutant Windows dans Azure"
	description="Apprenez à créer une machine virtuelle Windows dans le portail classique Azure."
	services="virtual-machines"
	documentationCenter=""
	authors="KBDAzure"
	manager="timlt"
	editor="tysonn"
	tags="azure-classic-portal"/>

<tags
	ms.service="virtual-machines"
	ms.workload="infrastructure-services"
	ms.tgt_pltfrm="vm-windows"
	ms.devlang="na"
	ms.topic="article"
	ms.date="04/27/2015"
	ms.author="kathydav"/>



# Création d'une machine virtuelle exécutant Windows

> [AZURE.SELECTOR]
- [Azure portal](virtual-machines-windows-tutorial.md)
- [Azure classic portal](virtual-machines-windows-tutorial-classic-portal.md)
- [PowerShell](virtual-machines-ps-create-preconfigure-windows-vms.md)

Ce didacticiel vous montre combien il est facile de créer une machine virtuelle Azure. Ce didacticiel utilise une image Windows Server, qui n’est qu’une des nombreuses images disponibles via Azure. Cela inclut les systèmes d’exploitation Windows, Linux et les images avec des applications installées. Les images que vous pouvez choisir dépendent du type d’abonnement que vous possédez. Par exemple, les images de bureau sont disponibles pour les abonnés MSDN.

Vous pouvez également créer des machines virtuelles Windows en utilisant [vos propres images](virtual-machines-create-upload-vhd-windows-server-classic-portal.md). Pour en savoir plus sur les machines virtuelles Azure, consultez [Présentation des machines virtuelles Azure](http://msdn.microsoft.com/library/azure/jj156143.aspx).

[AZURE.INCLUDE [free-trial-note](../includes/free-trial-note.md)]

## <a id="createvirtualmachine"> </a>Création de la machine virtuelle

Cette section vous montre comment utiliser l’option **À partir de la galerie** dans le portail classique Azure pour créer la machine virtuelle. Cette option propose davantage de possibilités de configuration que l'option **Création rapide**. Par exemple, si vous voulez associer une machine virtuelle à un réseau virtuel, vous devez utiliser l'option **À partir de la galerie**.

> [AZURE.NOTE]Vous pouvez également faire un essai avec le [portail Azure](https://portal.azure.com), plus riche et personnalisable, pour créer une machine virtuelle, automatiser le déploiement de modèles d’application sur plusieurs machines virtuelles, utiliser les fonctions avancées de surveillance et de diagnostic des machines virtuelles, etc. Bien qu’il existe des recoupements importants entre les options de configuration des machines virtuelles disponibles dans les deux portails, ces options ne sont pas identiques.

[AZURE.INCLUDE [virtual-machines-create-WindowsVM](../includes/virtual-machines-create-WindowsVM.md)]

## Étapes suivantes

- Connectez-vous à votre machine virtuelle. Pour plus d’informations, consultez [Connexion à une machine virtuelle exécutant Windows Server](virtual-machines-log-on-windows-server.md).

- Attacher un disque pour stocker des données. Vous pouvez attacher des disques, qu'ils soient vides ou non. Pour obtenir des instructions, consultez le [didacticiel Attacher un disque de données](storage-windows-attach-disk.md).

## Ressources supplémentaires

Pour en savoir plus sur ce que vous pouvez configurer pour une machine virtuelle et le moment où vous pouvez le faire, consultez [À propos des paramètres de configuration de la machine virtuelle Azure](http://msdn.microsoft.com/library/azure/dn763935.aspx).

<!--HONumber=52-->
