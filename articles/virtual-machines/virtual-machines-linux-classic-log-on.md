<properties
	pageTitle="Ouvrir une session sur une machine virtuelle Linux dans Azure | Microsoft Azure"
	description="Apprenez à vous connecter à une machine virtuelle Azure exécutant Linux en utilisant un client SSH (Secure Shell)."
	services="virtual-machines-linux"
	documentationCenter=""
	authors="squillace"
	manager="timlt"
	editor=""
	tags="azure-service-management"/>

<tags
	ms.service="virtual-machines-linux"
	ms.workload="infrastructure-services"
	ms.tgt_pltfrm="vm-linux"
	ms.devlang="na"
	ms.topic="article"
	ms.date="04/18/2016"
	ms.author="rasquill"/>


#Connexion à une machine virtuelle exécutant Linux #

[AZURE.INCLUDE [learn-about-deployment-models](../../includes/learn-about-deployment-models-classic-include.md)] [Resource Manager deployment model](virtual-machines-linux-portal-create.md).

Installez un client SSH sur l’ordinateur utilisé pour vous connecter à la machine virtuelle. Vous pouvez opérer votre choix parmi de nombreux clients SSH, dont :

- Pour une machine virtuelle exécutant un système d’exploitation Linux, utilisez un client SSH (Secure Shell) pour ouvrir une session. La grande majorité des distribution sont, par défaut, dotées de cette fonctionnalité. Consultez la page [comment utiliser SSH](virtual-machines-linux-ssh-from-linux.md) pour plus d’informations sur Linux.
- Si vous utilisez un ordinateur qui exécute un système d’exploitation Windows, vous pouvez utiliser un client SSH tel que PuTTY. Pour plus d’informations, consultez la page [Téléchargement PuTTY](http://www.chiark.greenend.org.uk/~sgtatham/putty/download.html).


>[AZURE.NOTE] Pour obtenir des conseils concernant les exigences et le dépannage, consultez [Connexion d’une machine virtuelle Azure à l’aide de RDP or SSH](http://go.microsoft.com/fwlink/p/?LinkId=398294)-.

Cette procédure montre comment utiliser le client SSH sur OS X pour accéder à la machine virtuelle.

1. Recherchez le **Nom d’hôte** et les **Informations sur le port** dans le [Portail de gestion](http://manage.windowsazure.com). Les informations dont vous avez besoin sont disponibles dans le tableau de bord de la machine virtuelle. Cliquez sur le nom de la machine virtuelle, puis recherchez **Détails SSH** dans la section **Aperçu rapide** du tableau de bord.

	![Obtenir les détails SSH](./media/virtual-machines-linux-classic-log-on/portalsshdetails.png)

2. Connectez-vous à la machine virtuelle à l’aide du compte spécifié lors de la création de la machine, ainsi que du nom d’hôte et du port appropriés. Pour plus d’informations sur la création d’une machine virtuelle avec nom d’utilisateur et mot de passe, consultez [Création d’une machine virtuelle exécutant Linux](virtual-machines-linux-classic-createportal.md).

	![Connexion à la machine virtuelle](./media/virtual-machines-linux-classic-log-on/sshport.png)

>[AZURE.NOTE] L'extension VMAccess peut vous aider à réinitialiser la clé ou le mot de passe SSH en cas d'oubli. En cas d'oubli du nom d'utilisateur, vous pouvez utiliser l'extension pour en créer un nouveau, avec autorité sudo. Pour obtenir des instructions, consultez la rubrique [Réinitialisation d’un mot de passe ou SSH pour les machines virtuelles Linux].

Vous pouvez désormais utiliser la machine virtuelle tout comme vous le feriez avec un serveur.

<!-- LINKS -->
[Réinitialisation d’un mot de passe ou SSH pour les machines virtuelles Linux]: http://go.microsoft.com/fwlink/p/?LinkId=512138

<!---HONumber=AcomDC_0511_2016-->