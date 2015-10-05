<properties
	pageTitle="Créer une machine virtuelle Azure exécutant Linux dans le portail Azure"
	description="Utilisez le portail Azure pour créer une machine virtuelle Azure (VM) exécutant Linux avec les groupes de ressources Azure."
	services="virtual-machines"
	documentationCenter=""
	authors="squillace"
	manager="timlt"
	editor="tysonn"
	tags="azure-resource-manager"/>

<tags
	ms.service="virtual-machines"
	ms.workload="infrastructure-services"
	ms.tgt_pltfrm="vm-linux"
	ms.devlang="na"
	ms.topic="article"
	ms.date="07/13/2015"
	ms.author="rasquill"/>

# Création d’une machine virtuelle exécutant Windows dans le portail Azure en version préliminaire

> [AZURE.SELECTOR]
- [Azure CLI](virtual-machines-linux-tutorial.md)
- [Azure Preview Portal](virtual-machines-linux-tutorial-portal-rm.md)

La création d’une machine virtuelle Azure (VM) exécutant Linux est facile. Ce didacticiel vous montre comment utiliser le portail Azure en version préliminaire pour en créer une rapidement et utilise le `~/.ssh/id_rsa.pub`Fichier de clé publique pour sécuriser votre **SSH**connexion à la machine virtuelle. Vous pouvez également créer des machines virtuelles Windows en utilisant [vos propres images en tant que modèles](virtual-machines-linux-create-upload-vhd.md).

> [AZURE.NOTE]Ce didacticiel crée une machine virtuelle Azure qui est gérée par l’API de groupe de ressources Azure. Pour plus d’informations, consultez la section [Vue d’ensemble du groupe de ressources Azure](resource-group-overview.md).

[AZURE.INCLUDE [free-trial-note](../../includes/free-trial-note.md)]

## Sélectionnez l'image

Accédez à Azure Marketplace dans la version préliminaire du portail pour rechercher l'image de machine virtuelle Windows Server.

1. Connectez-vous au [Portail Azure en version préliminaire](https://portal.azure.com).

2. Dans le menu Hub, cliquez sur **Nouveau**> **Compute**>**Ubuntu Server 14.04 LTS**.

	![choix d’une image de machine virtuelle](media/virtual-machines-linux-tutorial-portal-rm/chooseubuntuvm.png)

	> [AZURE.TIP]Pour rechercher d’autres images, cliquez sur **Marketplace**, puis recherchez ou filtrez les éléments disponibles.

3. En bas de la page **Ubuntu Server 14.04 LTS**, sélectionnez **Utiliser la pile du gestionnaire de ressources** pour créer la machine virtuelle dans Azure Resource Manager. Notez que pour la plupart des nouvelles charges de travail, nous vous recommandons la pile du Gestionnaire de ressources. Pour connaître les aspects à prendre en compte, consultez [Calcul Azure, Réseau et Sockage sous Azure Resource Manager](virtual-machines-azurerm-versus-azuresm.md).

4. Cliquez ensuite sur ![créer un bouton](media/virtual-machines-linux-tutorial-portal-rm/createbutton.png).

	![changer pour la pile de calcul de gestionnaire de ressources](media/virtual-machines-linux-tutorial-portal-rm/changetoresourcestack.png)

## Créer la machine virtuelle

Après avoir sélectionné l'image, vous pouvez utiliser les paramètres par défaut d’Azure pour effectuer la plus grande partie de la configuration et créer rapidement la machine virtuelle.

1. Sur le panneau **Créer une machine virtuelle**, cliquez sur**Options de base**. Saisissez le **nom** de machine virtuelle que vous avez choisi et un fichier de clé publique (au format **ssh rsa**, dans ce cas, à partir du fichier `~/.ssh/id_rsa.pub`). Si vous disposez de plusieurs abonnements, spécifiez celui de la nouvelle machine virtuelle, ainsi qu’un **Groupe de ressources** nouveau ou existant et un ** emplacement** de centre de données Azure.

	![](media/virtual-machines-linux-tutorial-portal-rm/step-1-thebasics.png)

	> [AZURE.NOTE]Vous pouvez également choisir l’authentification par nom d’utilisateur/mot de passe ici et saisir ces informations ici si vous ne souhaitez pas sécuriser votre session **ssh** par échange de clés publique et privée.

2. Cliquez sur **Taille** et sélectionnez une taille de machine virtuelle adaptée à vos besoins. Chaque taille spécifie la quantité de cœurs de calcul, de mémoire et d'autres fonctionnalités, telles que la prise en charge du stockage Premium, ce qui aura un impact sur le prix. Azure recommande automatiquement certaines tailles en fonction de l’image que vous choisissez. Lorsque vous avez terminé, cliquez sur ![sélectionner le bouton](media/virtual-machines-linux-tutorial-portal-rm/selectbutton-size.png).

	>[AZURE.NOTE]Le stockage Premium est disponible pour les machines virtuelles de la série DS dans certaines régions. Le stockage Premium est l’option de stockage la mieux adaptée aux charges de travail intensives, comme une base de données. Pour plus d’informations, voir l’article [Stockage Premium : stockage hautes performances pour les charges de travail des machines virtuelles Azure](storage-premium-storage-preview-portal.md).

3. Cliquez sur **Paramètres** pour voir les paramètres de réseau et de stockage de la nouvelle machine virtuelle. Pour une première machine virtuelle, vous pouvez généralement accepter les paramètres par défaut. Si vous avez sélectionné une taille de machine virtuelle prenant en charge le stockage Premium, vous pouvez faire un essai en sélectionnant **Premium (SSD)** sous **Type de disque**. Une fois que vous avez terminé, cliquez sur ![bouton OK](media/virtual-machines-linux-tutorial-portal-rm/okbutton.png).

	![](media/virtual-machines-linux-tutorial-portal-rm/step-3-settings.png)

6. Cliquez sur **Résumé** pour passer en revue vos options de configuration. Une fois que vous avez terminé de passer en revue ou de mettre à jour les paramètres, cliquez sur ![bouton OK](media/virtual-machines-linux-tutorial-portal-rm/createbutton.png).

	![résumé de la création](media/virtual-machines-linux-tutorial-portal-rm/summarybeforecreation.png)

8. Lorsqu’Azure crée la machine virtuelle, vous pouvez suivre la progression dans **Notifications**, dans le menu Hub. Une fois qu’Azure a créé la machine virtuelle, elle apparaît dans votre tableau d’accueil, sauf si vous avez désactivé l’option **Épingler au tableau d’accueil** dans le panneau **Créer un ordinateur virtuel**.

	> [AZURE.NOTE]Notez que le résumé ne contient pas de nom DNS public, contrairement à ce qui est fait lorsqu’une machine virtuelle est créée à l’intérieur d’un Service Cloud à l’aide de la pile de calcul de gestion de service.

## Connectez-vous à votre machine virtuelle Linux Azure en utilisant **ssh**

Vous pouvez désormais vous connecter à votre machine virtuelle Ubuntu à l’aide de **ssh** selon la méthode standard. Toutefois, vous devrez découvrir l’adresse IP allouée à la machine virtuelle Azure en ouvrant la vignette correspondant à la machine virtuelle et à ses ressources. Vous pouvez pour cela cliquer sur **Parcourir**, puis sélectionner **Récent** et rechercher la machine virtuelle créée, ou cliquer sur la vignette créée pour vous sur le Tableau d’accueil. Dans les deux cas, localisez et copiez la valeur **Adresse IP publique** comme indiqué dans le graphique suivant.

![résumé de création réussie](media/virtual-machines-linux-tutorial-portal-rm/successresultwithip.png)

Maintenant vous pouvez exécuter la commande **ssh** dans votre machine virtuelle Azure, et vous êtes prêt à travailler.

	ssh ops@23.96.106.1 -p 22
	The authenticity of host '23.96.106.1 (23.96.106.1)' can't be established.
	ECDSA key fingerprint is bc:ee:50:2b:ca:67:b0:1a:24:2f:8a:cb:42:00:42:55.
	Are you sure you want to continue connecting (yes/no)? yes
	Warning: Permanently added '23.96.106.1' (ECDSA) to the list of known hosts.
	Welcome to Ubuntu 14.04.2 LTS (GNU/Linux 3.16.0-43-generic x86_64)

	 * Documentation:  https://help.ubuntu.com/

	  System information as of Mon Jul 13 21:36:28 UTC 2015

	  System load: 0.31              Memory usage: 5%   Processes:       208
	  Usage of /:  42.1% of 1.94GB   Swap usage:   0%   Users logged in: 0

	  Graph this data and manage this system at:
	    https://landscape.canonical.com/

	  Get cloud support with Ubuntu Advantage Cloud Guest:
	    http://www.ubuntu.com/business/services/cloud

	0 packages can be updated.
	0 updates are security updates.

	The programs included with the Ubuntu system are free software;
	the exact distribution terms for each program are described in the
	individual files in /usr/share/doc/*/copyright.

	Ubuntu comes with ABSOLUTELY NO WARRANTY, to the extent permitted by
	applicable law.

	ops@ubuntuvm:~$


> [AZURE.NOTE]Vous pouvez également configurer un nom de domaine complet (FQDN) pour votre machine virtuelle dans le portail. En savoir plus sur le nom de domaine complet [ici](virtual-machines-create-fqdn-on-portal.md).

## Étapes suivantes

Pour en savoir plus sur Linux sur Microsoft Azure, consultez les pages suivantes :

- [Linux et informatique open-source sur Microsoft Azure](virtual-machines-linux-opensource.md)

- [Utilisation des outils en ligne de commande Azure pour Mac et Linux](virtual-machines-command-line-tools.md)

- [Déployer une application LAMP à l’aide de l’extension CustomScript Azure pour Linux](virtual-machines-linux-script-lamp.md)

- [Extension Docker VM pour Linux sur Azure](virtual-machines-docker-vm-extension.md)

<!---HONumber=Sept15_HO4-->