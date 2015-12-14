<properties
	pageTitle="Extension de l’agent IaaS SQL Server | Microsoft Azure"
	description="Cette rubrique utilise des ressources créées avec le modèle de déploiement classique et décrit l'extension de l'agent SQL Server, qui permet à une machine virtuelle exécutant SQL Server sur Azure d’utiliser des fonctionnalités d'automatisation."
	services="virtual-machines"
	documentationCenter=""
	authors="jeffgoll"
	manager="jeffreyg"
   editor="monicar"    
   tags="azure-service-management"/>

<tags
	ms.service="virtual-machines"
	ms.devlang="na"
	ms.topic="article"
	ms.tgt_pltfrm="vm-windows-sql-server"
	ms.workload="infrastructure-services"
	ms.date="10/02/2015"
	ms.author="jeffreyg"/>

# Extension de l’agent IaaS SQL Server

Cette extension permet à SQL Server dans Azure Virtual Machines d’utiliser certains services, répertoriés dans cet article, qui peuvent être utilisés seulement quand cette extension est installée. Cette extension est installée automatiquement pour les images de la galerie SQL Server dans le portail Azure. Il peut être installé sur toute machine virtuelle SQL Server dans Azure où l’agent invité de la machine virtuelle Azure est installé.

[AZURE.INCLUDE [learn-about-deployment-models](../../includes/learn-about-deployment-models-classic-include.md)]Modèle Resource Manager


## Composants requis
Configuration requise pour l’utilisation des applets de commande PowerShell :

- Le dernier Kit de développement logiciel (SDK) de ligne de commande Azure [disponible ici](http://azure.microsoft.com/downloads/)

Configuration requise pour utiliser l’extension sur votre machine virtuelle :

- Agent invité de machine virtuelle Azure
- Windows Server 2012, Windows Server 2012 R2 ou ultérieur
- SQL Server 2012, SQL Server 2014 ou ultérieur

## Services disponibles avec l’extension

- **Sauvegarde automatisée SQL** : ce service automatise la planification des sauvegardes pour toutes les bases de données pour l’instance par défaut de SQL Server dans la machine virtuelle. Pour plus d’informations sur ce service, consultez [Sauvegarde automatisée pour SQL Server dans Azure Virtual Machines](virtual-machines-sql-server-automated-backup.md).
- **Application automatisée des correctifs SQL** : ce service vous permet de configurer une fenêtre de maintenance pendant laquelle les mises à jour de votre machine virtuelle peuvent avoir lieu, afin d’éviter les mises à jour pendant les heures de pointe de votre charge de travail. Pour plus d’informations sur ce service, consultez [Application automatisée des correctifs pour SQL Server dans Azure Virtual Machines](virtual-machines-sql-server-automated-patching.md).

## Ajouter l’extension avec PowerShell
Si vous configurez votre machine virtuelle SQL Server en utilisant le [portail Azure](https://portal.azure.com/), l’extension est automatiquement installée. Pour les machines virtuelles SQL Server configurées avec le [portail Azure Classic](https://manage.windowsazure.com) ou pour les machines virtuelles où vous avez apporté votre propre licence SQL, vous pouvez ajouter cette extension à une machine virtuelle existante en utilisant l’applet de commande Azure PowerShell suivante.

**Set-AzureVMSqlServerExtension**

### Syntaxe

Set-AzureVMSqlServerExtension [-VM] <IPersistentVM> [[-Version] <string>] [-AutoBackupSettings <paramètres\_sauvegarde\_automatique>] [-AutoPatchingSetttings <AutoPatchingSetttings>] [-Confirm] [-WhatIf] [<paramètres\_courants>]

> [AZURE.NOTE]Il est recommandé d’omettre le paramètre – Version. S’il n’est pas spécifié, la valeur par défaut est la dernière version de l’extension.

### Exemple
	Get-AzureVM –ServiceName serviceName –Name vmName | Set-AzureVMSqlServerExtension –AutoBackupSettings $abs | Update-AzureVM**

## Vérifier l’état de l’extension
Si vous voulez vérifier l’état de cette extension et les services associés, vous pouvez utiliser les deux portails. Dans les détails de votre machine virtuelle existante, recherchez **Extensions** sous **Paramètres**.

Vous pouvez également utiliser l’applet de commande Azure PowerShell suivante.

**Get-AzureVMSqlServerExtension**

### Syntaxe

Get-AzureVMSqlServerExtension [[-VM] <IPersistentVM>] [[-Version] <string>] [<paramètres\_courants>]

> [AZURE.NOTE]Vous pouvez omettre le paramètre –Version. S’il n’est pas spécifié, la valeur par défaut est la dernière version de l’extension.

### Exemple
	Get-AzureVM –ServiceName "service" –Name "vmname" | Get-AzureVMSqlServerExtension

## Supprimer l’extension avec PowerShell   
Si vous voulez supprimer cette extension de votre machine virtuelle, vous pouvez utiliser l’applet de commande Azure PowerShell suivante.

**Remove-AzureVMSqlServerExtension**

### Syntaxe
Remove-AzureVMSqlServerExtension -VM <IPersistentVM> [<CommonParameters>]

<!---HONumber=AcomDC_1203_2015-->