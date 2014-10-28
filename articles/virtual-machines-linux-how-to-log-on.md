<properties linkid="manage-linux-howto-logon-linux-vm" urlDisplayName="Log on to a VM" pageTitle="Log on to a virtual machine running Linux in Azure" metaKeywords="Azure Linux vm, Linux SSH" description="Learn how to log on to an Azure virtual machine running Linux by using a Secure Shell (SSH) client." metaCanonical="" services="virtual-machines" documentationCenter="" title="How to Log on to a Virtual Machine Running Linux" authors="kathydav" solutions="" manager="timlt" editor="" />

<tags ms.service="virtual-machines" ms.workload="infrastructure-services" ms.tgt_pltfrm="vm-linux" ms.devlang="na" ms.topic="article" ms.date="01/01/1900" ms.author="kathydav"></tags>

# Connexion à une machine virtuelle exécutant Linux

Pour une machine virtuelle exécutant un système d'exploitation Linux, vous devez utiliser un client SSH (Secure Shell) pour vous connecter.

Installez un client SSH sur l’ordinateur utilisé pour vous connecter à la machine virtuelle. Vous pouvez opérer votre choix parmi de nombreux clients SSH, dont :

-   Si vous utilisez un ordinateur qui exécute un système d’exploitation Windows, vous pouvez utiliser un client SSH tel que PuTTY. Pour plus d’informations, consultez la page [Téléchargement PuTTY][].
-   Si vous utilisez un ordinateur qui exécute un système d’exploitation Linux, vous pouvez utiliser un client SSH tel que OpenSSH. Pour plus d’informations, consultez la page [OpenSSH][].

> [WACOM.NOTE] Pour plus de conseils concernant les exigences et le dépannage, consultez [Connexion à machine virtuelle Azure à l'aide de RDP or SSH][].

Cette procédure montre comment utiliser le programme PuTTY pour accéder à la machine virtuelle.

1.  Recherchez le **Nom d’hôte** et les **Informations sur le port** dans le [Portail de gestion][]. Les informations dont vous avez besoin sont disponibles dans le tableau de bord de la machine virtuelle. Cliquez sur le nom de la machine virtuelle, puis recherchez **Détails SSH** dans la section **Aperçu rapide** du tableau de bord.

    ![Obtenir les détails SSH][]

2.  Ouvrez le programme PuTTY.

3.  Entrez le nom d'hôte et les informations de port disponibles dans le tableau de bord, puis cliquez sur **Ouvrir**.

    ![Ouvrir PuTTY][]

4.  Connectez-vous à la machine virtuelle à l’aide du compte spécifié à la création de la machine. Par défaut, le nom d'utilisateur est azureuser.

    ![Se connecter à la machine virtuelle][]

> [WACOM.NOTE] L'extension VMAccess peut vous aider à réinitialiser la clé ou le mot de passe SSH en cas d'oubli. En cas d'oubli du nom d'utilisateur, vous pouvez utiliser l'extension pour en créer un nouveau, avec autorité sudo. Pour la marche à suivre, consultez [Utilisation de l'extension VMAccess pour réinitialiser les informations d'identification pour une machine virtuelle Linux][].

Vous pouvez désormais utiliser la machine virtuelle tout comme vous le feriez avec un serveur.

<!-- LINKS -->

  [Téléchargement PuTTY]: http://www.chiark.greenend.org.uk/~sgtatham/putty/download.html
  [OpenSSH]: http://www.openssh.org/
  [Connexion à machine virtuelle Azure à l'aide de RDP or SSH]: http://go.microsoft.com/fwlink/p/?LinkId=398294
  [Portail de gestion]: http://manage.windowsazure.com
  [Obtenir les détails SSH]: ./media/virtual-machines-linux-how-to-log-on/sshdetails.png
  [Ouvrir PuTTY]: ./media/virtual-machines-linux-how-to-log-on/putty.png
  [Se connecter à la machine virtuelle]: ./media/virtual-machines-linux-how-to-log-on/sshlogin.png
  [Utilisation de l'extension VMAccess pour réinitialiser les informations d'identification pour une machine virtuelle Linux]: http://go.microsoft.com/fwlink/p/?LinkId=512138
