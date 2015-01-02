<properties urlDisplayName="Log on to a VM" pageTitle="Connexion à une machine virtuelle exécutant Linux dans Azure" metaKeywords="Azure Linux vm, Linux SSH" description="Learn how to log on to an Azure virtual machine running Linux by using a Secure Shell (SSH) client." metaCanonical="" services="virtual-machines" documentationCenter="" title="How to Log on to a Virtual Machine Running Linux" authors="kathydav" solutions="" manager="timlt" editor="" />

<tags ms.service="virtual-machines" ms.workload="infrastructure-services" ms.tgt_pltfrm="vm-linux" ms.devlang="na" ms.topic="article" ms.date="11/12/2014" ms.author="kathydav" />




#Connexion à une machine virtuelle exécutant Linux #

Pour une machine virtuelle exécutant un système d'exploitation Linux, vous devez utiliser un client SSH (Secure Shell) pour vous connecter.

Installez un client SSH sur l'ordinateur utilisé pour vous connecter à la machine virtuelle. Vous pouvez opérer votre choix parmi de nombreux clients SSH, dont :

- Si vous utilisez un ordinateur qui exécute un système d'exploitation Windows, vous pouvez utiliser un client SSH tel que PuTTY. Pour plus d'informations, consultez la page [Téléchargement PuTTY](http://www.chiark.greenend.org.uk/~sgtatham/putty/download.html).
- Si vous utilisez un ordinateur qui exécute un système d'exploitation Linux, vous pouvez utiliser un client SSH tel que OpenSSH. Pour plus d'informations, consultez la page [OpenSSH](http://www.openssh.org/).

>[WACOM.NOTE] Pour obtenir des conseils concernant les exigences et le dépannage, consultez [Connexion d'une machine virtuelle Azure à l'aide de RDP or SSH](http://go.microsoft.com/fwlink/p/?LinkId=398294). 

Cette procédure montre comment utiliser le programme PuTTY pour accéder à la machine virtuelle.

1. Recherchez le **Nom d'hôte** et les **Informations sur le port** dans le[portail de gestion](http://manage.windowsazure.com). Les informations dont vous avez besoin sont disponibles dans le tableau de bord de la machine virtuelle. Cliquez sur le nom de la machine virtuelle, puis recherchez  **Détails SSH** dans la section **Aperçu rapide** du tableau de bord.

	![Obtain SSH details](./media/virtual-machines-linux-how-to-log-on/sshdetails.png)

2. Ouvrez le programme PuTTY.

3. Entrez le nom d'hôte et les informations de port disponibles dans le tableau de bord, puis cliquez sur **Ouvrir**.

	![Open PuTTY](./media/virtual-machines-linux-how-to-log-on/putty.png)

4. Connectez-vous à la machine virtuelle à l'aide du compte spécifié à la création de la machine. Par défaut, le nom d'utilisateur est azureuser.

	![Log on to the virtual machine](./media/virtual-machines-linux-how-to-log-on/sshlogin.png)

>[WACOM.NOTE] L'extension VMAccess peut vous aider à réinitialiser la clé ou le mot de passe SSH en cas d'oubli. En cas d'oubli du nom d'utilisateur, vous pouvez utiliser l'extension pour en créer un nouveau, avec autorité sudo. Pour obtenir des instructions, consultez la rubrique [Réinitialisation d'un mot de passe ou SSH pour les machines virtuelles Linux]. 
	
Vous pouvez désormais utiliser la machine virtuelle tout comme vous le feriez avec un serveur.

<!-- LINKS -->
[Réinitialisation d'un mot de passe ou SSH pour les machines virtuelles Linux]: http://go.microsoft.com/fwlink/p/?LinkId=512138

<!--HONumber=35_1-->
