<properties
	pageTitle="Sauvegarde automatisée pour SQL Server dans Azure Virtual Machines (Resource Manager) | Microsoft Azure"
	description="Décrit la fonctionnalité de sauvegarde automatisée pour SQL Server s’exécutant dans Azure Virtual Machines à l’aide de Resource Manager. "
	services="virtual-machines-windows"
	documentationCenter="na"
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
	ms.date="05/18/2016"
	ms.author="jroth" />

# Sauvegarde automatisée pour SQL Server dans Azure Virtual Machines (Resource Manager)

> [AZURE.SELECTOR]
- [Gestionnaire de ressources](virtual-machines-windows-sql-automated-backup.md)
- [Classique](virtual-machines-windows-classic-sql-automated-backup.md)

La sauvegarde automatisée configure automatiquement une [sauvegarde managée sur Microsoft Azure](https://msdn.microsoft.com/library/dn449496.aspx) pour toutes les bases de données nouvelles et existantes sur une machine virtuelle Azure exécutant SQL Server 2014 Standard ou Enterprise. Cela vous permet de configurer des sauvegardes régulières de base de données utilisant le stockage d’objets blob Azure durable. La sauvegarde automatisée utilise l’[extension de l’agent IaaS de SQL Server](virtual-machines-windows-sql-server-agent-extension.md).

[AZURE.INCLUDE [learn-about-deployment-models](../../includes/learn-about-deployment-models-rm-include.md)] modèle de déploiement classique. Pour plus d’informations, consultez [Sauvegarde automatisée pour SQL Server dans les machines virtuelles Azure (classiques)](virtual-machines-windows-classic-sql-automated-backup.md).

## Composants requis

Pour utiliser la sauvegarde automatisée, prenez en compte les conditions préalables suivantes :

**Système d’exploitation** :

- Windows Server 2012
- Windows Server 2012 R2

**Édition/version de SQL Server** :

- SQL Server 2014 Standard
- SQL Server 2014 Enterprise
- SQL Server 2016 Standard
- SQL Server 2016 Enterprise

**Configuration de la base de données** :

- Les bases de données cibles doivent utiliser le modèle de récupération complète

**Azure PowerShell** :

- [Installez les dernières commandes Azure PowerShell](../powershell-install-configure.md) si vous projetez de configurer la sauvegarde automatisée avec PowerShell.

>[AZURE.NOTE] La sauvegarde automatisée utilise l’extension de l’agent IaaS de SQL Server. Les images actuelles de la galerie de machines virtuelles SQL ajoutent cette extension par défaut. Pour plus d’informations, consultez [SQL Server IaaS Agent Extension](virtual-machines-windows-sql-server-agent-extension.md) (Extension de l’agent IaaS SQL Server).

## Paramètres

Le tableau suivant décrit les options qui peuvent être configurées pour une sauvegarde automatisée. Les étapes de la configuration varient selon que vous utilisez les commandes du portail Azure ou Azure Windows PowerShell.

|Paramètre|Plage (par défaut)|Description|
|---|---|---|
|**Sauvegarde automatisée**|Activer/Désactiver (désactivé)|Active ou désactive la sauvegarde automatisée d’une machine virtuelle Azure exécutant SQL Server 2014 Standard ou Enterprise.|
|**Période de rétention**|1 à 30 jours (30 jours)|Nombre de jours durant lesquels une sauvegarde est conservée.|
|**Compte de stockage**|Compte de stockage Azure (compte de stockage créé pour la machine virtuelle spécifiée)|Compte de stockage Azure à utiliser pour stocker les fichiers de sauvegarde automatisée dans le stockage d’objets blob. Un conteneur est créé à cet emplacement pour stocker tous les fichiers de sauvegarde. La convention de dénomination des fichiers de sauvegarde inclut la date, l’heure et le nom de la machine.|
|**Chiffrement**|Activer/Désactiver (désactivé)|Active ou désactive le chiffrement. Lorsque le chiffrement est activé, les certificats utilisés pour restaurer la sauvegarde se trouvent dans le compte de stockage spécifié dans le même conteneur automaticbackup à l’aide de la même convention de dénomination. Si le mot de passe change, un nouveau certificat est généré avec ce mot de passe, mais l’ancien certificat est conservé pour restaurer les sauvegardes antérieures.|
|**Mot de passe**|Texte du mot de passe (aucun)|Mot de passe pour les clés de chiffrement. Il est uniquement requis si le chiffrement est activé. Pour restaurer une sauvegarde chiffrée, vous devez disposer du mot de passe correct et du certificat associé qui a été utilisé lorsque la sauvegarde a été effectuée.|

## Configuration dans le portail

Vous pouvez utiliser le portail Azure pour configurer une sauvegarde automatisée lorsque vous créez une machine virtuelle SQL Server 2014 dans le modèle de déploiement Resource Manager.

Dans le panneau **Paramètres SQL Server**, sélectionnez **Sauvegarde automatisée**. La capture d’écran suivante du portail Azure illustre le panneau **Sauvegarde automatisée SQL**.

![Configuration d’une sauvegarde automatisée SQL dans le portail Azure](./media/virtual-machines-windows-sql-automated-backup/azure-sql-arm-autobackup.png)

Pour plus de contexte, consultez la rubrique complète intitulée [Approvisionnement d’une machine virtuelle SQL Server dans le portail Azure](virtual-machines-windows-portal-sql-server-provision.md).

Pour les machines virtuelles SQL Server existantes, vous devez utiliser PowerShell pour configurer les paramètres de la sauvegarde automatisée.

>[AZURE.NOTE] Lorsque vous activez la sauvegarde automatisée pour la première fois, Azure configure l’agent IaaS de SQL Server en arrière-plan. Pendant ce temps, le portail Azure n’indiquera peut-être pas que la sauvegarde automatisée est configurée. Patientez quelques minutes jusqu’à ce que l’agent soit installé et configuré. Le portail Azure reflète alors les nouveaux paramètres.

## Configuration avec PowerShell

Après avoir approvisionné votre machine virtuelle SQL, utilisez PowerShell pour configurer une sauvegarde automatisée.

Dans l’exemple PowerShell suivant, une sauvegarde automatisée est configurée pour une machine virtuelle SQL Server 2014 existante. La commande **AzureRM.Compute\\New-AzureVMSqlServerAutoBackupConfig** configure les paramètres de sauvegarde automatisée pour stocker les sauvegardes dans le compte de stockage Azure associé à la machine virtuelle. Ces sauvegardes sont conservées pendant 10 jours. La commande **Set-AzureRmVMSqlServerExtension** met à jour la machine virtuelle Azure spécifiée avec ces paramètres.

	$vmname = "vmname"
	$resourcegroupname = "resourcegroupname"
    $autobackupconfig = AzureRM.Compute\New-AzureVMSqlServerAutoBackupConfig -Enable -RetentionPeriodInDays 10 -ResourceGroupName $resourcegroupname

    Set-AzureRmVMSqlServerExtension -AutoBackupSettings $autobackupconfig -VMName $vmname -ResourceGroupName $resourcegroupname

L’installation et la configuration de l’agent IaaS de SQL Server peuvent prendre plusieurs minutes.

Pour activer le chiffrement, modifiez le script précédent pour transmettre le paramètre **EnableEncryption** avec un mot de passe (chaîne sécurisée) pour le paramètre **CertificatePassword**. Le script suivant active les paramètres de sauvegarde automatisée dans l’exemple précédent et ajoute le chiffrement.

	$vmname = "vmname"
	$resourcegroupname = "resourcegroupname"
	$password = "P@ssw0rd"
	$encryptionpassword = $password | ConvertTo-SecureString -AsPlainText -Force  
	$autobackupconfig = AzureRM.Compute\New-AzureVMSqlServerAutoBackupConfig -Enable -RetentionPeriod 10 -EnableEncryption -CertificatePassword $encryptionpassword -ResourceGroupName $resourcegroupname

	Set-AzureRmVMSqlServerExtension -AutoBackupSettings $autobackupconfig -VMName $vmname -ResourceGroupName $resourcegroupname

Pour désactiver la sauvegarde automatisée, exécutez le même script sans le paramètre **-Enable** pour la commande **AzureRM.Compute\\New-AzureVMSqlServerAutoBackupConfig**. L’absence du paramètre **-Enable** indique à la commande de désactiver la fonctionnalité. À l’instar de l’installation, la désactivation de la sauvegarde automatisée peut prendre plusieurs minutes.

>[AZURE.NOTE] La suppression de l’agent IaaS de SQL Server ne supprime pas les paramètres de sauvegarde automatisée précédemment configurés. Vous devez désactiver la sauvegarde automatisée avant de désactiver ou de désinstaller l’agent IaaS de SQL Server.

## Étapes suivantes

La sauvegarde automatisée configure une sauvegarde managée sur les machines virtuelles Azure. Il est donc important de [lire la documentation relative à la sauvegarde gérée](https://msdn.microsoft.com/library/dn449496.aspx) pour comprendre son comportement et ses implications.

Vous trouverez des conseils supplémentaires pour la sauvegarde et la restauration de SQL Server sur les machines virtuelles Azure dans la rubrique suivante : [Sauvegarde et restauration de SQL Server dans les machines virtuelles Azure](virtual-machines-windows-sql-backup-recovery.md).

Pour plus d’informations sur les autres tâches d’automatisation disponibles, consultez la page [SQL Server IaaS Agent Extension](virtual-machines-windows-sql-server-agent-extension.md) (Extension de l’agent IaaS SQL Server).

Pour plus d’informations sur l’exécution de SQL Server sur des machines virtuelles Azure, consultez la page [Vue d’ensemble de SQL Server dans Azure Virtual Machines](virtual-machines-windows-sql-server-iaas-overview.md).

<!---HONumber=AcomDC_0629_2016-->