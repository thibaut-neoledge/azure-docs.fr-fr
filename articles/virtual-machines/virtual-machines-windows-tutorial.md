<properties
	pageTitle="Création d'une machine virtuelle exécutant Windows dans le portail Azure Preview"
	description="Apprenez à créer une machine virtuelle Azure exécutant Windows à partir d’Azure Marketplace de la version préliminaire du portail Azure"
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
	ms.topic="get-started-article"
	ms.date="06/03/2015"
	ms.author="kathydav"/>

# Création d'une machine virtuelle exécutant Windows dans la version préliminaire du portail Azure#

> [AZURE.SELECTOR]
- [Azure Preview Portal](virtual-machines-windows-tutorial.md)
- [Azure Portal](virtual-machines-windows-tutorial-classic-portal.md)
- [PowerShell - Resource Management](virtual-machines-deploy-rmtemplates-powershell.md)
- [PowerShell - Service Management](virtual-machines-ps-create-preconfigure-windows-vms.md)

Ce didacticiel vous montre combien il est facile de créer une machine virtuelle Azure dans le portail Azure en version préliminaire. Nous allons utiliser une image Windows Server comme exemple, mais il s’agit simplement d’un des nombreux types d’images proposés par Azure. Notez que votre choix en matière d’images dépend de votre abonnement. Par exemple, les images de bureau sont disponibles pour les abonnés MSDN.

Vous pouvez également créer des machines virtuelles en utilisant [vos propres images](virtual-machines-create-upload-vhd-windows-server.md). Pour en savoir plus à ce sujet et connaître d’autres méthodes, consultez la page [Les différentes façons de créer une machine virtuelle Windows](virtual-machines-windows-choices-create-vm.md).

[AZURE.INCLUDE [free-trial-note](../../includes/free-trial-note.md)]


## Créer la machine virtuelle

En vous servant de Windows Server 2012 R2 Datacenter comme exemple, vous pouvez créer une machine virtuelle exploitable en quelques minutes. Vous pouvez utiliser les paramètres par défaut d’Azure pour la majorité de la configuration.

1. Connectez-vous à la [version préliminaire du portail](https://portal.azure.com).

2. Dans le menu Hub, cliquez sur **Nouveau**.

3. Dans le panneau **Nouveau**, cliquez sur **Compute** **>** **Windows Server 2012 R2 Datacenter**.

	![Sélectionner une image de machine virtuelle à partir de Marketplace](./media/virtual-machines-windows-tutorial/marketplace_portal.png)

4. Dans le volet **Créer une machine virtuelle**, remplissez le **nom d'hôte** que vous souhaitez pour la machine virtuelle, le **nom d'utilisateur** administratif et un **mot de passe** fort. **Nom d’utilisateur** fait référence au compte administratif que vous utilisez pour gérer le serveur. Créez un mot de passe difficile à deviner, mais dont vous vous souviendrez. **Vous aurez besoin du nom d'utilisateur et du mot de passe pour vous connecter à la machine virtuelle**. Si vous oubliez le mot de passe, réinitialisez-le en suivant [ces instructions](virtual-machines-windows-reset-password.md).

	![Configure host name and log on credentials](./media/virtual-machines-windows-tutorial/create_vm_name_pwd_portal.png)

5. Passez en revue les paramètres par défaut, comme le **Niveau de tarification** et la **Configuration facultative**. Ces choix affectent la taille de la machine virtuelle et des options de réseau telles que l'appartenance au domaine. Par exemple, pour essayer le stockage Premium sur une machine virtuelle, vous devez choisir une région et une taille qui le prend en charge. Pour votre première machine virtuelle, les paramètres par défaut conviennent généralement.

	>[AZURE.NOTE]Le stockage Premium est disponible pour les machines virtuelles de la série DS dans certaines régions. Le stockage Premium est l’option de stockage la mieux adaptée aux charges de travail intensives, comme une base de données. Pour plus d’informations, voir l’article [Stockage Premium : stockage hautes performances pour les charges de travail des machines virtuelles Azure](storage-premium-storage-preview-portal.md).

6. Quand vous avez terminé de passer en revue ou de mettre à jour les paramètres, cliquez sur **Créer**.

7. Lorsqu'Azure crée la machine virtuelle, vous pouvez suivre la progression dans **Notifications**, dans le menu Hub. Une fois qu’Azure a créé la machine virtuelle, elle apparaîtra dans votre tableau d’accueil, sauf si vous avez désactivé l’option **Épingler au tableau d’accueil** dans le panneau **Créer une machine virtuelle**.

## Connexion à la machine virtuelle

Une fois que vous avez créé la machine virtuelle, vous pouvez vous y connecter afin de gérer ses paramètres et les applications qui s’exécuteront dessus.

>[AZURE.NOTE]Pour des conseils concernant les exigences et le dépannage, consultez [Connexion d'une machine virtuelle Azure à l'aide de RDP or SSH](https://msdn.microsoft.com/library/azure/dn535788.aspx).

1. Si ce n’est pas déjà fait, connectez-vous à la [version préliminaire du portail](https://portal.azure.com).

2. Cliquez sur votre machine virtuelle dans le tableau d'accueil. Si vous devez la recherchez, cliquez sur **Parcourir** > **Machines virtuelles**. Sélectionnez ensuite votre machine virtuelle à partir de la liste.

3. Dans le panneau de la machine virtuelle, cliquez sur **Connecter**.

	![Connexion à la machine virtuelle](./media/virtual-machines-windows-tutorial/connect_vm_portal.png)

4. Cliquez sur **Ouvrir** pour utiliser le fichier de protocole RDP (Remote Desktop Protocol) automatiquement créé pour la machine virtuelle.

5. Cliquez sur **Connecter**.

6. Tapez le nom d’utilisateur et le mot de passe que vous avez spécifiés lorsque vous avez créé la machine virtuelle, puis cliquez sur **OK**.

7. Cliquez sur **Oui** pour vérifier l'identité de la machine virtuelle.

	Vous pouvez désormais utiliser la machine virtuelle tout comme vous le feriez avec un serveur.

## Étapes suivantes

Pour en savoir plus sur la configuration des machines virtuelles Windows sur Azure, consultez les articles suivants :

[Connexion de machines virtuelles à un réseau virtuel ou un service cloud](cloud-services-connect-virtual-machine.md)

[Association de disques de données avec une machine virtuelle](storage-windows-attach-disk.md)

[Gestion de la disponibilité des machines virtuelles](../manage-availability-virtual-machines.md)

[À propos des paramètres de configuration de machine virtuelle Azure](http://msdn.microsoft.com/library/azure/dn763935.aspx)
 

<!---HONumber=July15_HO2-->