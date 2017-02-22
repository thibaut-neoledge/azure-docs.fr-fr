---
title: "Sauvegarde automatisée pour les machines virtuelles Azure SQL Server 2014 | Microsoft Docs"
description: "Décrit la fonctionnalité de sauvegarde automatisée pour les machines virtuelles exécutant SQL Server 2014 dans Azure. Cet article est spécifique aux machines virtuelles utilisant Resource Manager."
services: virtual-machines-windows
documentationcenter: na
author: rothja
manager: jhubbard
editor: 
tags: azure-resource-manager
ms.assetid: bdc63fd1-db49-4e76-87d5-b5c6a890e53c
ms.service: virtual-machines-sql
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: vm-windows-sql-server
ms.workload: iaas-sql-server
ms.date: 01/30/2017
ms.author: jroth
translationtype: Human Translation
ms.sourcegitcommit: 253c504fa433c7ca37c0065ebf01d13dafc76231
ms.openlocfilehash: c4cf6ab29ebf5b3397017cf754ee04bf57ab1555


---
# <a name="automated-backup-for-sql-server-2014-virtual-machines-resource-manager"></a>Sauvegarde automatisée pour les machines virtuelles SQL Server 2014 (Resource Manager)
> [!div class="op_single_selector"]
> * [Gestionnaire de ressources](virtual-machines-windows-sql-automated-backup.md)
> * [Classique](../sqlclassic/virtual-machines-windows-classic-sql-automated-backup.md)
> 
> 

La sauvegarde automatisée configure automatiquement une [sauvegarde managée sur Microsoft Azure](https://msdn.microsoft.com/library/dn449496.aspx) pour toutes les bases de données nouvelles et existantes sur une machine virtuelle Azure exécutant SQL Server 2014 Standard ou Enterprise. Cela vous permet de configurer des sauvegardes régulières de base de données utilisant le stockage d’objets blob Azure durable. La sauvegarde automatisée dépend l’ [extension de l’agent IaaS de SQL Server](virtual-machines-windows-sql-server-agent-extension.md).

[!INCLUDE [learn-about-deployment-models](../../../../includes/learn-about-deployment-models-rm-include.md)]

Pour plus d’informations, consultez [Sauvegarde automatisée pour SQL Server dans les machines virtuelles Azure (classiques)](../sqlclassic/virtual-machines-windows-classic-sql-automated-backup.md).

## <a name="prerequisites"></a>Composants requis
Pour utiliser la sauvegarde automatisée, prenez en compte les conditions préalables suivantes :

**Système d’exploitation**:

- Windows Server 2012
- Windows Server 2012 R2

**Édition/version de SQL Server**:

- SQL Server 2014 Standard
- SQL Server 2014 Enterprise

> [!IMPORTANT]
> La sauvegarde automatisée fonctionne avec SQL Server 2014. Si vous utilisez SQL Server 2016, vous pouvez utiliser la sauvegarde automatisée en version 2 pour sauvegarder vos bases de données. Pour plus d’informations, consultez [Sauvegarde automatisée en version 2 pour les machines virtuelles Azure SQL Server 2016](virtual-machines-windows-sql-automated-backup-v2.md).

**Configuration de la base de données**:

- Les bases de données cibles doivent utiliser le modèle de récupération complète.

Pour plus d’informations sur l’impact du modèle de récupération complète sur les sauvegardes, consultez [Sauvegarde en mode de récupération complète](https://technet.microsoft.com/library/ms190217.aspx).

**Modèle de déploiement Azure** :

- Gestionnaire de ressources

**Azure PowerShell**:

- [Installez les dernières commandes Azure PowerShell](/powershell/azureps-cmdlets-docs) si vous projetez de configurer la sauvegarde automatisée avec PowerShell.

> [!NOTE]
> La sauvegarde automatisée utilise l’extension de l’agent IaaS de SQL Server. Les images actuelles de la galerie de machines virtuelles SQL ajoutent cette extension par défaut. Pour plus d’informations, consultez [SQL Server IaaS Agent Extension](virtual-machines-windows-sql-server-agent-extension.md)(Extension de l’agent IaaS SQL Server).

## <a name="settings"></a>Paramètres
Le tableau suivant décrit les options qui peuvent être configurées pour une sauvegarde automatisée. Les étapes de la configuration varient selon que vous utilisez les commandes du portail Azure ou Azure Windows PowerShell.

| Paramètre | Plage (par défaut) | Description |
| --- | --- | --- |
| **Sauvegarde automatisée** | Activer/Désactiver (désactivé) | Active ou désactive la sauvegarde automatisée d’une machine virtuelle Azure exécutant SQL Server 2014 Standard ou Enterprise. |
| **Période de rétention** | 1 à&30; jours (30 jours) | Nombre de jours durant lesquels une sauvegarde est conservée. |
| **Compte de stockage** | Compte Azure Storage | Compte de stockage Azure à utiliser pour stocker les fichiers de sauvegarde automatisée dans le stockage d’objets blob. Un conteneur est créé à cet emplacement pour stocker tous les fichiers de sauvegarde. La convention de dénomination des fichiers de sauvegarde inclut la date, l’heure et le nom de la machine. |
| **Chiffrement** | Activer/Désactiver (désactivé) | Active ou désactive le chiffrement. Quand le chiffrement est activé, les certificats utilisés pour restaurer la sauvegarde se trouvent dans le compte de stockage spécifié dans le même conteneur `automaticbackup` avec la même convention de dénomination. Si le mot de passe change, un nouveau certificat est généré avec ce mot de passe, mais l’ancien certificat est conservé pour restaurer les sauvegardes antérieures. |
| **Mot de passe** | Texte du mot de passe | Mot de passe pour les clés de chiffrement. Il est uniquement requis si le chiffrement est activé. Pour restaurer une sauvegarde chiffrée, vous devez disposer du mot de passe correct et du certificat associé qui a été utilisé lorsque la sauvegarde a été effectuée. |

## <a name="configuration-in-the-portal"></a>Configuration dans le portail
Vous pouvez utiliser le portail Azure pour configurer la sauvegarde automatisée lors de l’approvisionnement ou pour des machines virtuelles SQL Server 2014 existantes.

### <a name="new-vms"></a>Nouvelles machines virtuelles
Utilisez le portail Azure pour configurer la sauvegarde automatisée quand vous créez une machine virtuelle SQL Server 2014 dans le modèle de déploiement Resource Manager.

Dans le panneau **Paramètres SQL Server**, sélectionnez **Sauvegarde automatisée**. La capture d’écran suivante du portail Azure illustre le panneau **Sauvegarde automatisée SQL** .

![Configuration d’une sauvegarde automatisée SQL dans le portail Azure](./media/virtual-machines-windows-sql-automated-backup/azure-sql-arm-autobackup.png)

Pour plus de contexte, voir la rubrique complète intitulée [Configuration d’une machine virtuelle SQL Server dans Azure](virtual-machines-windows-portal-sql-server-provision.md).

### <a name="existing-vms"></a>Machines virtuelles existantes
Pour les machines virtuelles SQL Server existantes, sélectionnez votre machine virtuelle SQL Server. Puis sélectionnez la section **Configuration de SQL Server** du panneau **Paramètres**.

![Sauvegarde automatisée SQL pour les machines virtuelles existantes](./media/virtual-machines-windows-sql-automated-backup/azure-sql-rm-autobackup-existing-vms.png)

Dans le panneau **Configuration de SQL Server**, cliquez sur le bouton **Modifier** dans la section de sauvegarde automatisée.

![Configurer la sauvegarde automatisée SQL pour les machines virtuelles existantes](./media/virtual-machines-windows-sql-automated-backup/azure-sql-rm-autobackup-configuration.png)

Lorsque vous avez terminé, cliquez sur le bouton **OK** au bas du panneau **Configuration de SQL Server** pour enregistrer vos modifications.

Si vous activez la sauvegarde automatisée pour la première fois, Azure configure l’agent IaaS de SQL Server en arrière-plan. Pendant ce temps, le portail Azure n’indiquera peut-être pas que la sauvegarde automatisée est configurée. Patientez quelques minutes jusqu’à ce que l’agent soit installé et configuré. Le portail Azure reflète alors les nouveaux paramètres.

> [!NOTE]
> Vous pouvez également configurer la sauvegarde automatisée à l’aide d’un modèle. Pour plus d’informations, consultez l’article [Azure quickstart template for Automated Backup](https://github.com/Azure/azure-quickstart-templates/tree/master/101-vm-sql-existing-autobackup-update)(Modèle de démarrage rapide d’Azure pour la sauvegarde automatisée).
> 
> 

## <a name="configuration-with-powershell"></a>Configuration avec PowerShell
Après avoir approvisionné votre machine virtuelle SQL, utilisez PowerShell pour configurer une sauvegarde automatisée. Avant de commencer, vous devez :

- [Télécharger et installer la version la plus récente d’Azure PowerShell](http://aka.ms/webpi-azps).
- Ouvrir Windows PowerShell et l’associer à votre compte. Pour cela, procédez selon les étapes de la section [Configurer votre abonnement](https://docs.microsoft.com/azure/virtual-machines/windows/sql/virtual-machines-windows-ps-sql-create#configure-your-subscription) de la rubrique consacrée à l’approvisionnement.

Dans l’exemple PowerShell suivant, une sauvegarde automatisée est configurée pour une machine virtuelle SQL Server 2014 existante. La commande **AzureRM.Compute\New-AzureVMSqlServerAutoBackupConfig** configure les paramètres de sauvegarde automatisée pour stocker les sauvegardes dans le compte de stockage Azure associé à la machine virtuelle. Ces sauvegardes sont conservées pendant 10 jours. La commande **Set-AzureRmVMSqlServerExtension** met à jour la machine virtuelle Azure spécifiée avec ces paramètres.

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

Pour désactiver la sauvegarde automatisée, exécutez le même script sans le paramètre **-Enable** pour la commande **AzureRM.Compute\New-AzureVMSqlServerAutoBackupConfig**. L’absence du paramètre **-Enable** indique à la commande de désactiver la fonctionnalité. À l’instar de l’installation, la désactivation de la sauvegarde automatisée peut prendre plusieurs minutes.

> [!NOTE]
> La suppression de l’agent IaaS de SQL Server ne supprime pas les paramètres de sauvegarde automatisée précédemment configurés. Vous devez désactiver la sauvegarde automatisée avant de désactiver ou de désinstaller l’agent IaaS de SQL Server.
> 
> 

## <a name="next-steps"></a>Étapes suivantes
La sauvegarde automatisée configure une sauvegarde managée sur les machines virtuelles Azure. Il est donc important de [lire la documentation relative à la sauvegarde gérée](https://msdn.microsoft.com/library/dn449496.aspx) pour comprendre son comportement et ses implications.

Vous trouverez des conseils supplémentaires pour la sauvegarde et la restauration de SQL Server sur les machines virtuelles Azure dans la rubrique suivante : [Sauvegarde et restauration de SQL Server dans les machines virtuelles Azure](virtual-machines-windows-sql-backup-recovery.md).

Pour plus d’informations sur les autres tâches d’automatisation disponibles, voir [Extension de l’agent IaaS SQL Server](virtual-machines-windows-sql-server-agent-extension.md).

Pour plus d’informations sur l’exécution de SQL Server sur des machines virtuelles Azure, voir [Vue d’ensemble de SQL Server sur les machines virtuelles Azure](virtual-machines-windows-sql-server-iaas-overview.md).




<!--HONumber=Jan17_HO5-->


