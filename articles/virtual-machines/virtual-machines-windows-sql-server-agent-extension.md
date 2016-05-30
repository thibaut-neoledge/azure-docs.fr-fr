<properties
	pageTitle="Extension Agent SQL Server pour machines virtuelles SQL Server (Resource Manager) | Microsoft Azure"
	description="Cette rubrique décrit comment gérer l’extension Agent SQL Server, qui automatise certaines tâches d’administration SQL Server. Celles-ci incluent Sauvegarde automatisée, Mise à jour corrective automatisée et Azure Key Vault Integration. Cette rubrique utilise le mode de déploiement Resource Manager."
	services="virtual-machines-windows"
	documentationCenter=""
	authors="rothja"
	manager="jhubbard"
	editor=""
	tags="azure-resource-manager"/>

<tags
	ms.service="virtual-machines-windows"
	ms.devlang="na"
	ms.topic="article"
	ms.tgt_pltfrm="vm-windows-sql-server"
	ms.workload="infrastructure-services"
	ms.date="05/16/2016"
	ms.author="jroth"/>

# Extension Agent SQL Server pour machines virtuelles SQL Server (Resource Manager)

> [AZURE.SELECTOR]
- [Gestionnaire de ressources](virtual-machines-windows-sql-server-agent-extension.md)
- [Classique](virtual-machines-windows-classic-sql-server-agent-extension.md)

L’extension Agent IaaS SQL Server (SQLIaaSExtension) s’exécute sur les machines virtuelles Azure pour automatiser les tâches d’administration. Cette rubrique présente les services pris en charge par l’extension ainsi que des instructions d’installation, d’état et de suppression.

[AZURE.INCLUDE [learn-about-deployment-models](../../includes/learn-about-deployment-models-rm-include.md)]modèle de déploiement classique. Pour afficher la version classique de cet article, consultez [Extension Agent SQL Server pour machines virtuelles SQL Server (Classic)](virtual-machines-windows-classic-sql-server-agent-extension.md).

## Services pris en charge

L’extension Agent IaaS SQL Server prend en charge les tâches d’administration suivantes :

| Fonction d’administration | Description |
|---------------------|-------------------------------|
| **Sauvegarde automatisée SQL** | Automatise la planification des sauvegardes de toutes les bases de données pour l’instance par défaut de SQL Server dans la machine virtuelle.|
| **Mise à jour corrective automatisée SQL** | Configure une fenêtre de maintenance pendant laquelle les mises à jour de votre machine virtuelle peuvent avoir lieu, afin d’éviter les mises à jour pendant les heures de pointe de votre charge de travail.|
| **Intégration du coffre de clés Azure** | Permet d’installer et de configurer automatiquement Azure Key Vault sur votre machine virtuelle SQL Server. Pour plus d’informations, consultez [Configurer l’intégration du coffre de clés Azure SQL Server sur des machines virtuelles (Resource Manager)](virtual-machines-windows-ps-sql-keyvault.md).|

## Composants requis

Configuration requise pour utiliser l’extension Agent IaaS SQL Server sur votre machine virtuelle :

- Windows Server 2012, Windows Server 2012 R2, ou version ultérieure.
- SQL Server 2012, SQL Server 2014, ou version ultérieure.

Configuration requise pour l’utilisation des applets de commande PowerShell :

- Dernière version d’Azure PowerShell [disponible ici](../powershell-install-configure.md).

## Installation

L’Extension Agent IaaS SQL Server s’installe automatiquement lorsque vous approvisionnez une des images de galerie de machine virtuelle SQL Server.

Si vous créez une machine virtuelle Windows Server, vous pouvez installer l’extension manuellement à l’aide de l’applet de commande PowerShell **Set-AzureVMSqlServerExtension**. Par exemple, la commande suivante installe l’extension sur une machine virtuelle Windows Server pour système d’exploitation uniquement, et la nomme SQLIaaSExtension.

	Set-AzureRmVMSqlServerExtension -ResourceGroupName "resourcegroupname" -VMName "vmname" -Name "SQLIaasExtension" -Version "1.2"

## Statut

Pour vérifier que l’extension est installée, un moyen consiste à afficher l’état de l’agent dans le portail Azure. Sélectionnez **Tous les paramètres** dans le panneau de la machine virtuelle, puis cliquez sur **Extensions**. L’extension **SQLIaaSExtension** doit s’afficher.

![Extension Agent IaaS SQL Server dans le portail Azure](./media/virtual-machines-windows-sql-server-agent-extension/azure-rm-sql-server-iaas-agent-portal.png)

Vous pouvez également utiliser l’applet de commande Azure PowerShell **Get-AzureVMSqlServerExtension**.

	Get-AzureRmVMSqlServerExtension -VMName "vmname" -ResourceGroupName "resourcegroupname"

La commande précédente confirme que l’agent est installé et fournit des informations générales sur son état. Vous pouvez également obtenir des informations d’état sur Sauvegarde automatisée et Mise à jour corrective automatisée, grâce aux commandes suivantes.

	$sqlext = Get-AzureRmVMSqlServerExtension -VMName "vmname" -ResourceGroupName "resourcegroupname"
	$sqlext.AutoPatchingSettings
	$sqlext.AutoBackupSettings

## Suppression   

Dans le portail Azure, vous pouvez désinstaller l’extension en cliquant sur le bouton de sélection dans le panneau **Extensions** des propriétés de votre machine virtuelle. Ensuite, cliquez sur **Supprimer**.

![Désinstaller l’extension Agent IaaS SQL Server dans le portail Azure](./media/virtual-machines-windows-sql-server-agent-extension/azure-rm-sql-server-iaas-agent-uninstall.png)

Vous pouvez également utiliser l’applet de commande PowerShell **Remove-AzureRmVMSqlServerExtension**.

	Remove-AzureRmVMSqlServerExtension -ResourceGroupName "resourcegroupname" -VMName "vmname" -Name "SQLIaasExtension"

## Étapes suivantes

Commencez par utiliser l’un des services pris en charge par l’extension. Pour plus d’informations, consultez les rubriques référencées dans la section [Services pris en charge](#supported-services) de cet article.

Pour plus d’informations sur l’exécution de SQL Server sur des machines virtuelles Azure, voir [SQL Server sur les machines virtuelles Azure](virtual-machines-windows-sql-server-iaas-overview.md).

<!---HONumber=AcomDC_0518_2016-->