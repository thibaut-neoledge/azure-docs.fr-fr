<properties
	pageTitle="Connexion à une machine virtuelle exécutant Windows Server"
	description="Découvrez comment utiliser le portail Azure pour se connecter à une machine virtuelle exécutant Windows Server."
	services="virtual-machines"
	documentationCenter=""
	authors="KBDAzure"
	manager="timlt"
	editor="tysonn"
	tags="azure-service-management"/>

<tags
	ms.service="virtual-machines"
	ms.workload="infrastructure-services"
	ms.tgt_pltfrm="vm-windows"
	ms.devlang="na"
	ms.topic="article"
	ms.date="06/12/2015"
	ms.author="kathydav"/>


# Comment se connecter à une machine virtuelle exécutant Windows Server#

Vous utilisez le bouton **Connecter** dans le portail Azure pour démarrer une session Bureau à distance. (Pour les machines virtuelles Linux, consultez [Comment se connecter à une machine virtuelle exécutant Linux](virtual-machines-linux-how-to-log-on.md).)

## Comment ouvrir une session

[AZURE.VIDEO logging-on-to-vm-running-windows-server-on-azure]

[AZURE.INCLUDE [virtual-machines-log-on-win-server](../../includes/virtual-machines-log-on-win-server.md)]

## Conseils de dépannage

Vous pouvez essayer rapidement les éléments suivants :

Pour les problèmes de connexion Bureau à distance, essayez de réinitialiser la configuration à partir du portail. Dans le tableau de bord de la machine virtuelle, sous **Aperçu rapide**, cliquez sur **Réinitialiser la configuration à distance**.

Si votre mot de passe pose problème, essayez de le réinitialiser à partir du portail. Dans le tableau de bord de la machine virtuelle, sous **Aperçu rapide**, cliquez sur **Réinitialiser le mot de passe**.

Si ces actions ne fonctionnent pas, vous devez effectuer un dépannage plus étendu. Pour obtenir des instructions, consultez la rubrique [Résolution des problèmes de connexion Bureau à distance avec une machine virtuelle Azure Windows](virtual-machines-troubleshoot-remote-desktop-connections.md).

<!---HONumber=September15_HO1-->