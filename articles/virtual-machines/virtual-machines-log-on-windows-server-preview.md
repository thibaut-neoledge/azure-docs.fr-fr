<properties
	pageTitle="Connexion à une machine virtuelle exécutant Windows Server"
	description="Découvrez comment utiliser le portail Azure en version préliminaire pour se connecter à une machine virtuelle exécutant Windows Server."
	services="virtual-machines"
	documentationCenter=""
	authors="cynthn"
	manager="timlt"
	editor="tysonn"
	tags="azure-resource-manager"/>

<tags
	ms.service="virtual-machines"
	ms.workload="infrastructure-services"
	ms.tgt_pltfrm="vm-windows"
	ms.devlang="na"
	ms.topic="article"
	ms.date="09/15/2015"
	ms.author="cynthn"/>

# Comment se connecter à une machine virtuelle exécutant Windows Server#

Vous utilisez le bouton **Connecter** dans le portail Azure en version préliminaire pour démarrer une session Bureau à distance. Tout d’abord, vous allez vous connecter à la machine virtuelle, puis ouvrir une session.

## Connectez-vous à la machine virtuelle.

1. Si ce n’est pas déjà fait, connectez-vous au [portail Azure en version préliminaire](https://portal.azure.com/).

2.	Dans le menu Hub, cliquez sur **Parcourir**.

3.	Dans le panneau de recherche, faites défiler l’écran vers le bas et cliquez sur **Machines virtuelles**.

	![Recherchez des machines virtuelles.](./media/virtual-machines-log-on-windows-server-preview/search-blade-preview-portal.png)

4.	Sélectionnez la machine virtuelle dans la liste.

5. Dans le panneau de la machine virtuelle, cliquez sur **Connecter**.

	![Connectez-vous à la machine virtuelle.](./media/virtual-machines-log-on-windows-server-preview/preview-portal-connect.png)

## Connexion à la machine virtuelle

[AZURE.INCLUDE [virtual-machines-log-on-win-server](../../includes/virtual-machines-log-on-win-server.md)]

## Résolution de problèmes

Si les conseils de connexion ne donnent aucun résultat ou ne vous sont pas utiles, consultez la rubrique [Résolution des problèmes de connexion Bureau à distance à une machine virtuelle Microsoft Azure](virtual-machines-troubleshoot-remote-desktop-connections.md). Cet article vous guide tout au long des opérations de diagnostic et de résolution des problèmes courants.

<!---HONumber=Sept15_HO3-->