<properties
	pageTitle="Connexion à une machine virtuelle | Microsoft Azure"
	description="Utilisez le portail pour vous connecter à une machine virtuelle Windows créée avec le modèle de déploiement classique"
	services="virtual-machines"
	documentationCenter=""
	authors="cynthn"
	manager="timlt"
	editor="tysonn"
	tags="azure-service-management"/>

<tags
	ms.service="virtual-machines"
	ms.workload="infrastructure-services"
	ms.tgt_pltfrm="vm-windows"
	ms.devlang="na"
	ms.topic="article"
	ms.date="09/15/2015"
	ms.author="cynthn"/>


# Connexion à une machine virtuelle Windows créée avec le modèle de déploiement classique

[AZURE.INCLUDE [learn-about-deployment-models](../../includes/learn-about-deployment-models-include.md)]Cet article traite de la gestion des ressources avec le modèle de déploiement classique.

Utilisez le bouton **Se connecter** dans le portail Azure en version préliminaire pour démarrer une session Bureau à distance. Tout d’abord, vous allez vous connecter à la machine virtuelle, puis ouvrir une session.

Vous souhaitez vous connecter à une machine virtuelle Linux ? Consultez [Connexion à une machine virtuelle exécutant Linux](virtual-machines-linux-how-to-log-on.md)

## Connectez-vous à la machine virtuelle.

Voici une procédure pas à pas de ce didacticiel.

[AZURE.VIDEO logging-on-to-vm-running-windows-server-on-azure]

1. Si ce n’est pas déjà fait, connectez-vous au [portail Azure](http://manage.windowsazure.com).

2. Cliquez sur **Machines virtuelles**, puis sélectionnez la machine virtuelle appropriée.

3. Dans la barre de commandes, cliquez sur **Connecter**.

	![Connexion à la machine virtuelle](./media/virtual-machines-log-on-windows-server/connectwindows.png)

## Connexion à la machine virtuelle

[AZURE.INCLUDE [virtual-machines-log-on-win-server](../../includes/virtual-machines-log-on-win-server.md)]

## Conseils de dépannage

Vous pouvez essayer rapidement les éléments suivants dans le portail :

-	Pour les problèmes de connexion Bureau à distance, essayez de réinitialiser la configuration. Dans le tableau de bord de la machine virtuelle, sous **Aperçu rapide**, cliquez sur **Réinitialiser la configuration à distance**.
-	Si votre mot de passe pose problème, essayez de le réinitialiser. Dans le tableau de bord de la machine virtuelle, sous **Aperçu rapide**, cliquez sur **Réinitialiser le mot de passe**.

Si ces conseils ne donnent aucun résultat ou ne vous sont pas utiles, consultez la rubrique [Résolution des problèmes de connexion du Bureau à distance à une machine virtuelle Azure sous Windows](virtual-machines-troubleshoot-remote-desktop-connections.md). Cet article vous guide tout au long des opérations de diagnostic et de résolution des problèmes courants.

<!---HONumber=Sept15_HO4-->