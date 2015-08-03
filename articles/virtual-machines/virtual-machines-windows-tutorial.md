<properties
	pageTitle="Création d'une machine virtuelle exécutant Windows dans le portail Azure en version préliminaire | Microsoft Azure"
	description="Apprenez à créer une ressource de machine virtuelle Azure exécutant Windows, à l’aide d’Azure Marketplace dans le portail Azure en version préliminaire"
	services="virtual-machines"
	documentationCenter=""
	authors="KBDAzure"
	manager="timlt"
	editor=""
	tags="azure-resource-manager"/>
<tags
	ms.service="virtual-machines"
	ms.workload="infrastructure-services"
	ms.tgt_pltfrm="vm-windows"
	ms.devlang="na"
	ms.topic="get-started-article"
	ms.date="07/13/2015"
	ms.author="kathydav"/>

# Création d'une machine virtuelle exécutant Windows dans le portail Azure en version préliminaire#

> [AZURE.SELECTOR]
- [Azure Preview Portal](virtual-machines-windows-tutorial.md)
- [Azure Portal](virtual-machines-windows-tutorial-classic-portal.md)
- [PowerShell - Resource Manager](virtual-machines-deploy-rmtemplates-powershell.md)
- [PowerShell - Service Management](virtual-machines-ps-create-preconfigure-windows-vms.md)

Ce didacticiel vous montre combien il est facile de créer une machine virtuelle Azure dans le portail Azure en version préliminaire, et ce en quelques minutes. Nous allons utiliser une image de Windows Server 2012 R2 Datacenter à titre d'exemple pour créer la machine virtuelle dans Azure Resource Manager. Ce n’est toutefois qu’une des nombreuses images proposées par Azure. Notez que votre choix en matière d’images dépend de votre abonnement. Par exemple, les images de bureau sont disponibles pour les abonnés MSDN.

Vous pouvez également créer des machines virtuelles à l'aide de vos propres images, avec des modèles Resource Manager ou avec les outils d'automatisation. Pour en savoir plus sur les différentes méthodes, consultez la page [Les différentes façons de créer une machine virtuelle Windows](virtual-machines-windows-choices-create-vm.md).

[AZURE.INCLUDE [free-trial-note](../../includes/free-trial-note.md)]


## Sélectionnez l'image

Accédez à Azure Marketplace dans la version préliminaire du portail pour rechercher l'image de machine virtuelle Windows Server.

1. Connectez-vous au [portail en version préliminaire](https://portal.azure.com).

2. Dans le menu Hub, cliquez sur **Nouveau** > **Calcul** > **Windows Server 2012 R2 Datacenter**.

	![Marketplace](./media/virtual-machines-windows-tutorial/marketplace_new.png)

	>[AZURE.TIP]Pour rechercher d’autres images, cliquez sur **Marketplace**, puis recherchez ou filtrez les éléments disponibles.

3. Sur la page **Windows Server 2012 R2 Datacenter**, sélectionnez **Utiliser la pile du gestionnaire de ressources** pour créer la machine virtuelle dans Azure Resource Manager. (Pour la plupart des nouvelles charges de travail, nous vous recommandons la pile du Gestionnaire de ressources. Pour connaître les aspects à prendre en compte, consultez [Fournisseurs de calcul, de réseau et de stockage Azure dans le Gestionnaire de ressources Azure](virtual-machines-azurerm-versus-azuresm.md).) Cliquez sur **Créer**.

	![Recherche dans Marketplace](./media/virtual-machines-windows-tutorial/marketplace_search_select.png)

## Créer la machine virtuelle

Après avoir sélectionné l'image, vous pouvez utiliser les paramètres par défaut d’Azure pour effectuer la plus grande partie de la configuration et créer rapidement la machine virtuelle.

1. Sur le panneau **Créer une machine virtuelle**, cliquez sur **Options de base**. Saisissez le **nom** de votre choix pour la machine virtuelle, le **nom d'utilisateur** administratif et un **mot de passe** fort. Si vous disposez de plusieurs abonnements, spécifiez celui de la nouvelle machine virtuelle, ainsi qu’un **Groupe de ressources** nouveau ou existant et un ** emplacement** de centre de données Azure.

	![Configurer les éléments de base de la machine virtuelle](./media/virtual-machines-windows-tutorial/create_vm_basics.PNG)

	>[AZURE.NOTE]**Nom d’utilisateur** fait référence au compte administratif que vous utilisez pour gérer le serveur. Créez un mot de passe difficile à deviner, mais dont vous vous souviendrez. **Vous aurez besoin du nom d'utilisateur et du mot de passe pour vous connecter à la machine virtuelle**.

2. Cliquez sur **Taille** et sélectionnez une taille de machine virtuelle adaptée à vos besoins. Chaque taille spécifie la quantité de cœurs de calcul, de mémoire et d'autres fonctionnalités, telles que la prise en charge du stockage Premium, ce qui aura un impact sur le prix. Azure recommande automatiquement certaines tailles en fonction de l'image que vous choisissez.

	![Configurer la taille de la machine virtuelle](./media/virtual-machines-windows-tutorial/create_vm_size.PNG)

	>[AZURE.NOTE]Le stockage Premium est disponible pour les machines virtuelles de la série DS dans certaines régions. Le stockage Premium est l’option de stockage la mieux adaptée aux charges de travail intensives, comme une base de données. Pour plus d’informations, voir l’article [Stockage Premium : stockage hautes performances pour les charges de travail des machines virtuelles Azure](storage-premium-storage-preview-portal.md).

3. Cliquez sur **Paramètres** pour voir les paramètres de réseau et de stockage de la nouvelle machine virtuelle. Pour une première machine virtuelle, vous pouvez généralement accepter les paramètres par défaut. Si vous avez sélectionné une taille de machine virtuelle prenant en charge le stockage Premium, vous pouvez faire un essai en sélectionnant **Premium (SSD)** sous **Type de disque**.

	![Configurer les paramètres de la machine virtuelle](./media/virtual-machines-windows-tutorial/create_vm_settings.PNG)

6. Cliquez sur **Résumé** pour passer en revue vos options de configuration. Lorsque vous avez terminé de passer en revue ou de mettre à jour les paramètres, cliquez sur **Créer**.

	![Configurer les paramètres de la machine virtuelle](./media/virtual-machines-windows-tutorial/create_vm_summary.PNG)

8. Pendant qu'Azure crée la machine virtuelle, vous pouvez suivre la progression dans **Notifications**, dans le menu Hub. Une fois qu’Azure a créé la machine virtuelle, elle apparaît dans votre tableau d’accueil, sauf si vous avez désactivé l’option **Épingler au tableau d’accueil** dans le panneau **Créer une machine virtuelle**.

## Connexion à la machine virtuelle

Une fois que vous avez créé la machine virtuelle, vous pouvez vous y connecter afin de gérer ses paramètres et les applications qui s’exécuteront dessus.

>[AZURE.NOTE]Pour des conseils concernant les exigences et le dépannage, consultez [Connexion d'une machine virtuelle Azure à l'aide de RDP or SSH](https://msdn.microsoft.com/library/azure/dn535788.aspx).

1. Si ce n’est pas déjà fait, connectez-vous à la [version préliminaire du portail](https://portal.azure.com).

2. Cliquez sur votre machine virtuelle dans le tableau d'accueil. Si vous devez la rechercher, cliquez sur **Parcourir tout** > **Récentes** ou **Parcourir tout** > **Machines virtuelles**. Sélectionnez ensuite votre machine virtuelle à partir de la liste.

3. Dans le panneau de la machine virtuelle, cliquez sur **Connecter**.

	![Connexion à la machine virtuelle](./media/virtual-machines-windows-tutorial/connect_vm_portal.png)

4. Cliquez sur **Ouvrir** pour utiliser le fichier de protocole RDP (Remote Desktop Protocol) automatiquement créé pour la machine virtuelle Windows Server.

5. Cliquez sur **Connecter**.

6. Tapez le nom d’utilisateur et le mot de passe que vous avez spécifiés lorsque vous avez créé la machine virtuelle, puis cliquez sur **OK**.

7. Cliquez sur **Oui** pour vérifier l'identité de la machine virtuelle.

	Vous pouvez désormais utiliser la machine virtuelle tout comme vous le feriez avec un serveur.

## Étapes suivantes

* Utiliser Azure PowerShell et la CLI Azure pour [rechercher et sélectionner des images de machine virtuelle](resource-groups-vm-searching.md).
* Automatiser le déploiement et la gestion de machine virtuelle et de charge de travail à l’aide d’[Azure Resource Manager](virtual-machines-how-to-automate-azure-resource-manager.md) et des [modèles Azure Resource Manager](http://azure.microsoft.com/documentation/templates/).

<!---HONumber=July15_HO4-->