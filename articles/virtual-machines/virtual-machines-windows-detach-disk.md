<properties
	pageTitle="Détachement d’un disque de données d’une machine virtuelle Windows | Microsoft Azure"
	description="Apprenez à détacher un disque de données d’une machine virtuelle dans Azure à l’aide du modèle de déploiement Ressource Manager."
	services="virtual-machines-windows"
	documentationCenter=""
	authors="cynthn"
	manager="timlt"
	editor=""
	tags="azure-service-management"/>

<tags
	ms.service="virtual-machines-windows"
	ms.workload="infrastructure-services"
	ms.tgt_pltfrm="vm-windows"
	ms.devlang="na"
	ms.topic="article"
	ms.date="06/02/2016"
	ms.author="cynthn"/>



# Détachement d’un disque de données d’une machine virtuelle Windows


Lorsque vous n’avez plus besoin d’un disque de données qui est attaché à une machine virtuelle, vous pouvez le détacher facilement. Cela supprime le disque de la machine virtuelle, mais pas du stockage.

> [AZURE.WARNING] Si vous détachez un disque, il n’est pas supprimé automatiquement. Si vous êtes abonné au stockage Premium, vous continuerez à engager des frais de stockage pour le disque. Pour plus d’informations, consultez [Tarification et facturation de Premium Storage](../storage/storage-premium-storage.md#pricing-and-billing).

Si vous souhaitez réutiliser les données du disque, vous pouvez l’attacher à la même machine virtuelle ou à une autre.


## Détacher un disque de données avec le portail

1. Dans le concentrateur du portail, sélectionnez **Machines virtuelles**.

2. Sélectionnez la machine virtuelle disposant du disque de données que vous souhaitez détacher, puis cliquez sur **Tous les paramètres**.

3. Dans le panneau **Paramètres**, sélectionnez **Disques**.

4. Dans le panneau **Disques**, sélectionnez le disque de données que vous souhaitez détacher.

5. Dans le panneau du disque de données, cliquez sur **Détacher**.


	![Capture d’écran montrant le bouton Détacher.](./media/virtual-machines-windows-detach-disk/detach-disk.png)

Le disque reste dans le stockage, mais il n’est plus attaché à une machine virtuelle.


## Détacher un disque de données avec PowerShell

Dans cet exemple, la première commande récupère la machine virtuelle nommée **MyVM07** dans le groupe de ressources **RG11** à l’aide de l’applet de commande Get-AzureRmVM. La commande stocke la machine virtuelle dans la variable **$VirtualMachine**.

La deuxième commande supprime le disque de données nommé DataDisk3 de la machine virtuelle.

La dernière commande met à jour l’état de la machine virtuelle pour terminer le processus de suppression du disque de données.

	$VirtualMachine = Get-AzureRmVM -ResourceGroupName "RG11" -Name "MyVM07" 
	Remove-AzureRmVMDataDisk -VM $VirtualMachine -Name "DataDisk3"
	Update-AzureRmVM -ResourceGroupName "RG11" -Name "MyVM07" -VM $VirtualMachine


Pour plus d’informations, consultez [Remove-AzureRmVMDataDisk](https://msdn.microsoft.com/library/mt603614.aspx)

## Étapes suivantes

Si vous souhaitez réutiliser le disque de données, vous pouvez simplement [l’attacher à une autre machine virtuelle](virtual-machines-windows-attach-disk-portal.md)

<!---HONumber=AcomDC_0608_2016-->