<properties
	pageTitle="Se connecter à une machine virtuelle Windows | Microsoft Azure"
	description="Utilisez le portail Azure pour vous connecter à une machine virtuelle Windows créée avec le modèle de déploiement Resource Manager."
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
	ms.date="02/03/2016"
	ms.author="cynthn"/>


# Ouvrir une session sur une machine virtuelle Windows à l’aide du portail Azure


Sur le portail Azure, vous utilisez le bouton **Connecter** pour démarrer une session Bureau à distance et ouvrir une session sur une machine virtuelle Windows.


[AZURE.INCLUDE [learn-about-deployment-models](../../includes/learn-about-deployment-models-rm-include.md)] [classic deployment model](virtual-machines-log-on-windows-server.md).


## Connectez-vous à la machine virtuelle.

1. Connectez-vous au portail Azure.

2. Cliquez sur **Machines virtuelles**, puis sélectionnez la machine virtuelle.

3. Sur la barre de commandes en bas de la page, cliquez sur **Connexion**.

	![Connexion à la machine virtuelle](./media/arm_log_on_windows_vm/rm_windows_connect.png)
	

4. Un clic sur **Connecter** permet de créer et de télécharger un fichier de protocole Remote Desktop Protocol (fichier .rdp). Cliquez sur **Ouvrir** pour utiliser ce fichier.

	![Connexion à la machine virtuelle](./media/arm_log_on_windows_vm/rm_connect_rdp_file.png)
	
5. Dans la fenêtre Bureau à distance, cliquez sur **Connecter** pour continuer.

	![Poursuivre la connexion](./media/arm_log_on_windows_vm/rm_connect_unknown.png)

	
6. Dans la fenêtre Sécurité de Windows, tapez les informations d’identification d’un compte sur la machine virtuelle, puis cliquez sur **OK**.

 	Dans la plupart des cas, les informations d’identification correspondent au nom d’utilisateur et au mot de passe du compte local que vous avez spécifiés quand vous avez créé la machine virtuelle. Le domaine correspond au nom de la machine virtuelle entrée sous la forme *nom\_machine\_virtuelle*&#92;*nom\_utilisateur*.
	
	Si la machine virtuelle appartient à un domaine de votre organisation, assurez-vous que le nom d’utilisateur inclut le nom du domaine sous la forme *domaine*&#92;*nom\_utilisateur*. Le compte doit être membre du groupe Administrateurs ou bénéficier de privilèges d’accès à distance à la machine virtuelle.
	
	Si la machine virtuelle est un contrôleur de domaine, tapez le nom d’utilisateur et le mot de passe d’un compte d’administrateur de domaine pour ce domaine.

7.	Cliquez sur **Oui** pour vérifier l’identité de la machine virtuelle et terminer la connexion.

	![Vérifier l'identité de la machine](./media/arm_log_on_windows_vm/rm_connect_cert.png)

## Étapes suivantes

En cas de problème de connexion, consultez [Résolution des problèmes de connexion Bureau à distance avec une machine virtuelle Azure Windows](virtual-machines-troubleshoot-remote-desktop-connections.md).

<!---HONumber=AcomDC_0211_2016-->