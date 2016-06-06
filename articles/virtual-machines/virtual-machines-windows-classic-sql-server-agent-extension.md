<properties
	pageTitle="Extension Agent SQL Server pour machines virtuelles SQL Server (Classic) | Microsoft Azure"
	description="Cette rubrique décrit comment gérer l’extension Agent SQL Server, qui automatise certaines tâches d’administration SQL Server. Celles-ci incluent Sauvegarde automatisée, Mise à jour corrective automatisée et Azure Key Vault Integration. Cette rubrique utilise le modèle de déploiement classique."
	services="virtual-machines-windows"
	documentationCenter=""
	authors="rothja"
	manager="jhubbard"
	editor=""
	tags="azure-service-management"/>

<tags
	ms.service="virtual-machines-windows"
	ms.devlang="na"
	ms.topic="article"
	ms.tgt_pltfrm="vm-windows-sql-server"
	ms.workload="infrastructure-services"
	ms.date="05/16/2016"
	ms.author="jroth"/>

# Extension Agent SQL Server pour machines virtuelles SQL Server (Classic)

> [AZURE.SELECTOR]
- [Gestionnaire de ressources](virtual-machines-windows-sql-server-agent-extension.md)
- [Classique](virtual-machines-windows-classic-sql-server-agent-extension.md)

L’extension Agent IaaS SQL Server (SQLIaaSAgent) s’exécute sur les machines virtuelles Azure pour automatiser les tâches d’administration. Cette rubrique présente les services pris en charge par l’extension ainsi que des instructions d’installation, d’état et de suppression.

[AZURE.INCLUDE [learn-about-deployment-models](../../includes/learn-about-deployment-models-classic-include.md)]Modèle Resource Manager Pour afficher la version Resource Manager de cet article, consultez [Extension Agent SQL Server pour machines virtuelles SQL Server (Resource Manager)](virtual-machines-windows-sql-server-agent-extension.md).

## Services pris en charge

L’extension Agent IaaS SQL Server prend en charge les tâches d’administration suivantes :

| Fonction d’administration | Description |
|---------------------|-------------------------------|
| **Sauvegarde automatisée SQL** | Automatise la planification des sauvegardes de toutes les bases de données pour l’instance par défaut de SQL Server dans la machine virtuelle. Pour plus d’informations, consultez [Sauvegarde automatisée pour SQL Server dans les machines virtuelles Azure (Classic)](virtual-machines-windows-classic-sql-automated-backup.md).|
| **Mise à jour corrective automatisée SQL** | Configure une fenêtre de maintenance pendant laquelle les mises à jour de votre machine virtuelle peuvent avoir lieu, afin d’éviter les mises à jour pendant les heures de pointe de votre charge de travail. Pour plus d’informations, consultez [Mise à jour corrective automatisée pour SQL Server dans les machines virtuelles Azure (Classic)](virtual-machines-windows-classic-sql-automated-patching.md).|
| **Intégration du coffre de clés Azure** | Permet d’installer et de configurer automatiquement Azure Key Vault sur votre machine virtuelle SQL Server. Pour plus d’informations, consultez [Configurer Azure Key Vault Integration (Intégration du coffre de clés Azure) pour SQL Server sur des machines virtuelles Azure (Classic)](virtual-machines-windows-classic-ps-sql-keyvault.md).|

## Composants requis

Configuration requise pour utiliser l’extension Agent IaaS SQL Server sur votre machine virtuelle :

**Système d’exploitation** :

- Windows Server 2012
- Windows Server 2012 R2

**Versions de SQL Server** :

- SQL Server 2012
- SQL Server 2014
- SQL Server 2016

**Azure PowerShell** :

- [Télécharger et configurer les dernières commandes Azure PowerShell](../powershell-install-configure.md)

**Agent invité de machine virtuelle** :

- L’extension BGInfo est automatiquement installée sur les nouvelles machines virtuelles Azure.

## Installation

L’Extension Agent IaaS SQL Server s’installe automatiquement lorsque vous approvisionnez une des images de galerie de machine virtuelle SQL Server.

Si vous créez une machine virtuelle Windows Server, vous pouvez installer l’extension manuellement à l’aide de l’applet de commande PowerShell **Set-AzureVMSqlServerExtension**. Utilisez cette commande pour configurer un des services de l’agent, tel que Mise à jour corrective automatisée. La machine virtuelle installe l’agent si ce n’est déjà fait. Pour obtenir des instructions sur l’utilisation de l’applet de commande PowerShell **Set-AzureVMSqlServerExtension**, consultez les rubriques correspondantes dans la section [Service pris en charge](#supported-services) de cet article.

Si vous mettez à jour l’extension de l’agent IaaS SQL vers la dernière version, vous devez ensuite redémarrer votre machine virtuelle.

>[AZURE.NOTE] Si vous installez l’extension de l’agent IaaS SQL Server manuellement sur une machine virtuelle, vous devez utiliser et gérer ses fonctionnalités à l’aide des commandes PowerShell. L’interface du portail n’est pas disponible dans ce scénario.

## Statut

Pour vérifier que l’extension est installée, un moyen consiste à afficher l’état de l’agent dans le portail Azure. Sélectionnez **Tous les paramètres** dans le panneau de la machine virtuelle, puis cliquez sur **Extensions**. L’extension **SQLIaaSAgent** doit s’afficher.

![Extension Agent IaaS SQL Server dans le portail Azure](./media/virtual-machines-windows-classic-sql-server-agent-extension/azure-sql-server-iaas-agent-portal.png)

Vous pouvez également utiliser l’applet de commande Azure PowerShell **Get-AzureVMSqlServerExtension**.

	Get-AzureVM –ServiceName "service" –Name "vmname" | Get-AzureVMSqlServerExtension

## Suppression   

Dans le portail Azure, vous pouvez désinstaller l’extension en cliquant sur le bouton de sélection dans le panneau **Extensions** des propriétés de votre machine virtuelle. Ensuite, cliquez sur **Supprimer**.

![Désinstaller l’extension Agent IaaS SQL Server dans le portail Azure](./media/virtual-machines-windows-classic-sql-server-agent-extension/azure-sql-server-iaas-agent-uninstall.png)

Vous pouvez également utiliser l’applet de commande PowerShell **Get-AzureVMSqlServerExtension**.

	Get-AzureVM –ServiceName "service" –Name "vmname" | Remove-AzureVMSqlServerExtension | Update-AzureVM

## Étapes suivantes

Commencez par utiliser l’un des services pris en charge par l’extension. Pour plus d’informations, consultez les rubriques référencées dans la section [Services pris en charge](#supported-services) de cet article.

Pour plus d’informations sur l’exécution de SQL Server sur des machines virtuelles Azure, voir [SQL Server sur les machines virtuelles Azure](virtual-machines-windows-sql-server-iaas-overview.md).

<!---HONumber=AcomDC_0525_2016-->