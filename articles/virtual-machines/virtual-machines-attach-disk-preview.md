<properties
	pageTitle="Association d’un disque de données | Microsoft Azure"
	description="Explique comment attacher un disque de données nouveau ou existant à une machine virtuelle dans le portail Azure à l’aide du modèle de déploiement Resource Manager."
	services="virtual-machines"
	documentationCenter=""
	authors="cynthn"
	manager="timlt"
	editor=""
	tags="azure-resource-manager"/>

<tags
	ms.service="virtual-machines"
	ms.workload="infrastructure-services"
	ms.tgt_pltfrm="vm-multiple"
	ms.devlang="na"
	ms.topic="article"
	ms.date="09/14/2015"
	ms.author="cynthn"/>

# Attachement d’un disque de données dans le portail Azure

[AZURE.INCLUDE [learn-about-deployment-models](../../includes/learn-about-deployment-models-rm-include.md)] [classic deployment model](storage-windows-attach-disk.md).

Cet article explique comment attacher des disques nouveaux et existants à une machine virtuelle via le portail Azure. Avant cela, passez en revue les conseils suivants :

- La taille de la machine virtuelle détermine le nombre de disques de données que vous pouvez attacher . Pour en savoir plus, voir la rubrique [Tailles de machines virtuelles](virtual-machines-size-specs.md).
- Pour utiliser le stockage de Premium, vous avez besoin d’une machine virtuelle de série DS ou GS. Vous pouvez utiliser des disques de comptes de stockage Premium et Standard avec ces machines virtuelles. Le stockage Premium est disponible dans certaines régions. Pour plus d’informations, voir l’article [Stockage Premium : stockage hautes performances pour les charges de travail des machines virtuelles Azure](../storage/storage-premium-storage-preview-portal.md).
- Les disques attachés aux machines virtuelles sont en fait des fichiers .vhd dans un compte de stockage Azure. Pour en savoir plus, voir la section [À propos des disques et VHD pour machines virtuelles](virtual-machines-disks-vhds.md).
- Pour un nouveau disque, vous n’avez pas besoin de le créer au préalable, car Azure le crée lorsque vous l’attachez.
- Pour un disque existant, le fichier .vhd doit être disponible dans un compte de stockage Azure. Vous pouvez utiliser un fichier déjà présent, s’il n’est attaché à aucune autre machine virtuelle, ou télécharger votre propre fichier .vhd sur le compte de stockage.

## Recherchez la machine virtuelle.

1. Connectez-vous au portail Azure.

2. Dans le menu Hub, cliquez sur **Parcourir**.

3. Dans le panneau de recherche, faites défiler l’écran vers le bas, puis cliquez sur **Machines virtuelles**.

	![Recherche de machines virtuelles](./media/virtual-machines-attach-disk-preview/search-blade-preview-portal.png)

4.	Sélectionnez la machine virtuelle dans la liste.

5. À droite, sous **Bases**, cliquez sur **Tous les paramètres**, puis sur **Disques**.

	![Ouverture des paramètres d’un disque](./media/virtual-machines-attach-disk-preview/find-disk-settings.png)

Continuez en suivant les instructions pour attacher un disque nouveau ou existant.

## Option 1 : attacher un nouveau disque

1.	Dans le panneau **Disques**, cliquez sur **Attacher un nouveau disque**.

2.	Passez en revue les paramètres par défaut, mettez-les à jour en fonction des besoins, puis cliquez sur **OK**.

 	![Examen des paramètres d’un disque](./media/virtual-machines-attach-disk-preview/attach-new.png)

3.	Après qu’Azure a créé le disque et l’a attaché à la machine virtuelle, le nouveau disque est répertorié dans les paramètres de disque de la machine virtuelle sous **Disques de données**.

## Option 2 : attacher un disque existant

1.	Dans le panneau **Disques**, cliquez sur **Attacher un disque existant**.

2.	Sous **Attacher un disque existant**, cliquez sur **Fichier VHD**.

	![Attachement d’un disque existant](./media/virtual-machines-attach-disk-preview/attach-existing.png)

3.	Sous **Comptes de stockage**, sélectionnez le compte et le conteneur dans lequel figure le fichier .vhd.

	![Recherche d’emplacement VHD](./media/virtual-machines-attach-disk-preview/find-storage-container.png)

4.	Sélectionnez le fichier .vhd.

5.	Sous **Attacher un disque existant**, le fichier que vous venez de sélectionner est répertorié sous **Fichier VHD**. Cliquez sur **OK**.

6.	Après qu’Azure a attaché le disque à la machine virtuelle, le disque est répertorié dans les paramètres de disque de la machine virtuelle sous **Disques de données**.

## Étapes suivantes

Une fois le disque ajouté, vous devez le préparer pour utilisation dans le système d’exploitation de la machine virtuelle :

- Pour Linux, consultez « Initialisation d’un nouveau disque de données sous Linux » dans cet [article](virtual-machines-linux-how-to-attach-disk.md).
- Pour Windows, consultez « Initialisation d’un nouveau disque de données sous Windows Server » dans cet [article](storage-windows-attach-disk.md).

## Ressources supplémentaires

[À propos des comptes Azure Storage]

<!--Link references-->

[À propos des comptes Azure Storage]: ../storage-whatis-account/

<!---HONumber=AcomDC_1203_2015-->