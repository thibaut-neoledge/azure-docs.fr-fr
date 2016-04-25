<properties
	pageTitle="Extension de l’agent IaaS SQL Server (classique) | Microsoft Azure"
	description="Cette rubrique décrit l’extension de l’agent SQL Server, qui permet à une machine virtuelle exécutant SQL Server sur Azure d’utiliser des fonctionnalités d’automatisation. Elle utilise le modèle de déploiement classique."
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
	ms.date="04/08/2016"
	ms.author="jroth"/>

# Extension de l’agent IaaS SQL Server (classique)

Cette extension permet à SQL Server dans Azure Virtual Machines d’utiliser certains services, répertoriés dans cet article, qui peuvent être utilisés seulement quand cette extension est installée. Cette extension est installée automatiquement pour les images de la galerie SQL Server dans le portail Azure. Il peut être installé sur toute machine virtuelle SQL Server dans Azure où l’agent invité de la machine virtuelle Azure est installé.

[AZURE.INCLUDE [learn-about-deployment-models](../../includes/learn-about-deployment-models-classic-include.md)]Modèle Resource Manager


## Composants requis
Configuration requise pour l’utilisation des applets de commande PowerShell :

- La dernière version d’Azure PowerShell est [disponible ici](../powershell-install-configure.md)

Configuration requise pour utiliser l’extension sur votre machine virtuelle :

- Agent invité de machine virtuelle Azure
- Windows Server 2012, Windows Server 2012 R2 ou ultérieur
- SQL Server 2012, SQL Server 2014 ou ultérieur

## Services disponibles avec l’extension

- **Sauvegarde automatisée SQL** : ce service automatise la planification des sauvegardes pour toutes les bases de données pour l’instance par défaut de SQL Server dans la machine virtuelle. Pour plus d’informations, consultez [Sauvegarde automatisée pour SQL Server dans les machines virtuelles Azure (classiques)](virtual-machines-windows-classic-sql-automated-backup.md).
- **Mise à jour corrective automatisée SQL** : ce service vous permet de configurer une fenêtre de maintenance pendant laquelle les mises à jour de votre machine virtuelle peuvent avoir lieu, afin d’éviter les mises à jour pendant les heures de pointe de votre charge de travail. Pour plus d’informations, consultez [Mise à jour corrective automatisée pour SQL Server dans Azure Virtual Machines (Classic)](virtual-machines-windows-classic-sql-automated-patching.md).
- **Azure Key Vault Integration** : ce service permet d’installer et de configurer automatiquement Azure Key Vault sur votre machine virtuelle SQL Server. Pour plus d’informations, consultez [Configurer l’intégration du coffre de clés Azure SQL Server sur des machines virtuelles (classiques)](virtual-machines-windows-classic-ps-sql-keyvault.md).

## Ajouter l’extension avec PowerShell
Si vous configurez votre machine virtuelle SQL Server en utilisant le [portail Azure](virtual-machines-windows-portal-sql-server-provision.md), l’extension est automatiquement installée. Pour les machines virtuelles SQL Server approvisionnées avec le portail Azure Classic ou pour les machines virtuelles pour lesquelles vous avez apporté votre propre licence SQL, vous pouvez ajouter cette extension en utilisant l’applet de commande Azure PowerShell **Set-AzureVMSqlServerExtension**.

### Syntaxe

Set-AzureVMSqlServerExtension [[-ReferenceName] [String]] [-VM] IPersistentVM [[-Version] [String]] [[-AutoPatchingSettings] [AutoPatchingSettings]] [-AutoBackupSettings[AutoBackupSettings]] [-Profile [AzureProfile]] [CommonParameters]

> [AZURE.NOTE] Il est recommandé d’omettre le paramètre – Version. S’il n’est pas spécifié, la valeur par défaut est la dernière version de l’extension.

### Exemple
L’exemple suivant configure les paramètres de sauvegarde automatique à l’aide d’une configuration définie dans $abs (non illustré ici). Le nom du service est le nom du service cloud qui héberge la machine virtuelle. Pour un exemple complet, consultez [Sauvegarde automatisée pour SQL Server dans Azure Virtual Machines (Classic)](virtual-machines-windows-classic-sql-automated-backup.md).

	Get-AzureVM –ServiceName "serviceName" –Name "vmName" | Set-AzureVMSqlServerExtension –AutoBackupSettings $abs | Update-AzureVM**

## Vérifier l’état de l’extension
Si vous voulez vérifier l’état de cette extension et les services associés, vous pouvez utiliser les deux portails. Dans les détails de votre machine virtuelle existante, recherchez **Extensions** sous **Paramètres**.

Vous pouvez également utiliser l’applet de commande Azure PowerShell **Get-AzureVMSqlServerExtension**.

### Syntaxe

Get-AzureVMSqlServerExtension [[-VM] [IPersistentVM]] [-Profile [AzureProfile]] [CommonParameters]

### Exemple
	Get-AzureVM –ServiceName "service" –Name "vmname" | Get-AzureVMSqlServerExtension

## Supprimer l’extension avec PowerShell   
Si vous voulez supprimer cette extension de votre machine virtuelle, vous pouvez utiliser l’applet de commande Azure PowerShell **Remove-AzureVMSqlServerExtension**.

### Syntaxe

Remove-AzureVMSqlServerExtension [-Profile [AzureProfile]] -VM IPersistentVM [CommonParameters]

<!---HONumber=AcomDC_0413_2016-->