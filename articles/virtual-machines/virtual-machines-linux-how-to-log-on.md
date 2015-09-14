<properties
	pageTitle="Connexion à une machine virtuelle exécutant Linux dans Azure"
	description="Apprenez à vous connecter à une machine virtuelle Azure exécutant Linux en utilisant un client SSH (Secure Shell)."
	services="virtual-machines"
	documentationCenter=""
	authors="squillace"
	manager="timlt"
	editor=""/>

<tags
	ms.service="virtual-machines"
	ms.workload="infrastructure-services"
	ms.tgt_pltfrm="vm-linux"
	ms.devlang="na"
	ms.topic="article"
	ms.date="08/25/2015"
	ms.author="rasquill"/>




#Connexion à une machine virtuelle exécutant Linux #

Pour une machine virtuelle exécutant un système d'exploitation Linux, vous devez utiliser un client SSH (Secure Shell) pour vous connecter.

Installez un client SSH sur l’ordinateur utilisé pour vous connecter à la machine virtuelle. Vous pouvez opérer votre choix parmi de nombreux clients SSH, dont :

- Si vous utilisez un ordinateur qui exécute un système d’exploitation Windows, vous pouvez utiliser un client SSH tel que PuTTY. Pour plus d’informations, consultez la page [Téléchargement PuTTY](http://www.chiark.greenend.org.uk/~sgtatham/putty/download.html).
- Si vous utilisez un ordinateur qui exécute un système d’exploitation Linux, vous pouvez utiliser un client SSH tel que OpenSSH. Pour plus d’informations, consultez la page [OpenSSH](http://www.openssh.org/).

>[AZURE.NOTE]Pour obtenir des conseils concernant les exigences et le dépannage, consultez [Connexion d’une machine virtuelle Azure à l’aide de RDP or SSH](http://go.microsoft.com/fwlink/p/?LinkId=398294)-.

Cette procédure montre comment utiliser le programme PuTTY pour accéder à la machine virtuelle.

1. Recherchez le **Nom d’hôte** et les **Informations sur le port** dans le [Portail de gestion](http://manage.windowsazure.com). Les informations dont vous avez besoin sont disponibles dans le tableau de bord de la machine virtuelle. Cliquez sur le nom de la machine virtuelle, puis recherchez **Détails SSH** dans la section **Aperçu rapide** du tableau de bord.

	![Obtenir les détails SSH](./media/virtual-machines-linux-how-to-log-on/sshdetails.png)

2. Ouvrez le programme PuTTY.

3. Entrez le nom d'hôte et les informations de port disponibles dans le tableau de bord, puis cliquez sur **Ouvrir**.

	![Ouvrir PuTTY](./media/virtual-machines-linux-how-to-log-on/putty.png)

4. Connectez-vous à la machine virtuelle à l’aide du compte spécifié à la création de la machine. Pour plus d’informations sur la création d’une machine virtuelle avec nom d’utilisateur et mot de passe, consultez [Création d’une machine virtuelle exécutant Linux](virtual-machines-linux-tutorial.md).

	![Connexion à la machine virtuelle](./media/virtual-machines-linux-how-to-log-on/sshlogin.png)

>[AZURE.NOTE]L'extension VMAccess peut vous aider à réinitialiser la clé ou le mot de passe SSH en cas d'oubli. En cas d'oubli du nom d'utilisateur, vous pouvez utiliser l'extension pour en créer un nouveau, avec autorité sudo. Pour obtenir des instructions, consultez la rubrique [Réinitialisation d’un mot de passe ou SSH pour les machines virtuelles Linux].

Vous pouvez désormais utiliser la machine virtuelle tout comme vous le feriez avec un serveur.

<!-- LINKS -->
[Réinitialisation d’un mot de passe ou SSH pour les machines virtuelles Linux]: http://go.microsoft.com/fwlink/p/?LinkId=512138

<!---HONumber=September15_HO1-->