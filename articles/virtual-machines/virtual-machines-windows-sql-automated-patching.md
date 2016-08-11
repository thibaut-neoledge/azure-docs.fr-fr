<properties
	pageTitle="Mise à jour corrective automatisée pour SQL Server dans Azure Virtual Machines (Resource Manager) | Microsoft Azure"
	description="Décrit la fonctionnalité de mise à jour corrective automatisée pour les machines virtuelles SQL Server s’exécutant dans Azure à l’aide de Resource Manager."
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

# Mise à jour corrective automatisée pour SQL Server dans Azure Virtual Machines (Resource Manager)

> [AZURE.SELECTOR]
- [Gestionnaire de ressources](virtual-machines-windows-sql-automated-patching.md)
- [Classique](virtual-machines-windows-classic-sql-automated-patching.md)

La mise à jour corrective automatisée établit une fenêtre de maintenance pour une machine virtuelle Azure exécutant SQL Server. Les mises à jour automatisées ne peuvent être installées qu’au cours de cette fenêtre de maintenance. Pour SQL Server, les mises à jour système et les redémarrages associés ont ainsi lieu au meilleur moment possible pour la base de données. La mise à jour corrective automatisée utilise l’[extension de l’agent IaaS de SQL Server](virtual-machines-windows-sql-server-agent-extension.md).

[AZURE.INCLUDE [learn-about-deployment-models](../../includes/learn-about-deployment-models-rm-include.md)] 
modèle de déploiement classique. Pour plus d’informations, consultez [Mise à jour corrective automatisée pour SQL Server dans les machines virtuelles Azure (classiques)](virtual-machines-windows-classic-sql-automated-patching.md).

## Composants requis

Pour utiliser la mise à jour corrective automatisée, prenez en compte les conditions préalables suivantes :

**Système d’exploitation** :

- Windows Server 2012
- Windows Server 2012 R2

**Version de SQL Server** :

- SQL Server 2012
- SQL Server 2014
- SQL Server 2016

**Azure PowerShell** :

- [Installez les dernières commandes Azure PowerShell](../powershell-install-configure.md) si vous projetez de configurer la mise à jour corrective automatisée avec PowerShell.

>[AZURE.NOTE] La mise à jour corrective automatisée utilise l’extension de l’agent IaaS SQL Server. Les images actuelles de la galerie de machines virtuelles SQL ajoutent cette extension par défaut. Pour plus d’informations, consultez [SQL Server IaaS Agent Extension](virtual-machines-windows-sql-server-agent-extension.md) (Extension de l’agent IaaS SQL Server).

## Paramètres

Le tableau suivant décrit les options qui peuvent être configurées pour une mise à jour corrective automatisée. Les étapes de la configuration varient selon que vous utilisez les commandes du portail Azure ou Azure Windows PowerShell.

|Paramètre|Valeurs possibles|Description|
|---|---|---|
|**Mise à jour corrective automatisée**|Activer/Désactiver (désactivé)|Active ou désactive la mise à jour corrective automatisée pour une machine virtuelle Azure.|
|**Planification de la maintenance**|Tous les jours, lundi, mardi, mercredi, jeudi, vendredi, samedi et dimanche|Planification du téléchargement et de l’installation des mises à jour Windows, SQL Server et Microsoft pour votre machine virtuelle.|
|**Heure de début de la maintenance**|0-24|Heure locale de début de la mise à jour de la machine virtuelle.|
|**Durée de la fenêtre de maintenance**|30 à 180|Nombre de minutes autorisées pour terminer le téléchargement et l’installation des mises à jour.|
|**Catégorie de correctif**|Important|Catégorie des mises à jour à télécharger et installer.|

## Configuration dans le portail
Vous pouvez utiliser le portail Azure pour configurer une mise à jour corrective automatisée lors du déploiement ou pour les machines virtuelles existantes.

### Nouvelles machines virtuelles
Utilisez le portail Azure pour configurer une mise à jour corrective automatisée lorsque vous créez une machine virtuelle SQL Server dans le modèle de déploiement Resource Manager.

Dans le panneau **Paramètres SQL Server**, sélectionnez **Mise à jour corrective automatisée**. La capture d’écran suivante du portail Azure illustre le panneau **Mise à jour corrective automatisée SQL**.

![Mise à jour corrective automatisée SQL dans le portail Azure](./media/virtual-machines-windows-sql-automated-patching/azure-sql-arm-patching.png)

Pour plus de contexte, consultez la rubrique complète intitulée [Approvisionnement d’une machine virtuelle SQL Server dans le portail Azure](virtual-machines-windows-portal-sql-server-provision.md).

### Machines virtuelles existantes
Pour les machines virtuelles SQL Server existantes, sélectionnez votre machine virtuelle SQL Server. Puis sélectionnez la section **Configuration de SQL Server** du panneau **Paramètres**.

![Mise à jour corrective automatisée SQL pour les machines virtuelles existantes](./media/virtual-machines-windows-sql-automated-patching/azure-sql-rm-patching-existing-vms.png)

Dans le panneau **Configuration de SQL Server**, cliquez sur le bouton **Modifier** dans la section de mise à jour corrective automatisée.

![Configurer la mise à jour corrective automatisée SQL pour les machines virtuelles existantes](./media/virtual-machines-windows-sql-automated-patching/azure-sql-rm-patching-configuration.png)

Lorsque vous avez terminé, cliquez sur le bouton **OK** au bas du panneau **Configuration de SQL Server** pour enregistrer vos modifications.

Si vous activez la mise à jour corrective automatisée pour la première fois, Azure configure l’agent IaaS de SQL Server en arrière-plan. Pendant ce temps, le portail Azure n’indique pas que la mise à jour corrective automatisée est configurée. Patientez quelques minutes jusqu’à ce que l’agent soit installé et configuré. Le portail Azure reflète alors les nouveaux paramètres.

>[AZURE.NOTE] Vous pouvez également configurer la mise à jour corrective automatisée à l’aide d’un modèle. Pour plus d’informations, consultez l’article [Azure quickstart template for Automated Patching](https://github.com/Azure/azure-quickstart-templates/tree/master/101-vm-sql-existing-autopatching-update) (Modèle de démarrage rapide d’Azure pour la mise à jour corrective automatisée).

## Configuration avec PowerShell

Après avoir approvisionné votre machine virtuelle SQL, utilisez PowerShell pour configurer une mise à jour corrective automatisée.

Dans l’exemple suivant, PowerShell permet de configurer une mise à jour corrective automatisée sur une machine virtuelle SQL Server existante. La commande **AzureRM.Compute\\New-AzureVMSqlServerAutoPatchingConfig** configure une nouvelle fenêtre de maintenance pour les mises à jour automatiques.

	$vmname = "vmname"
	$resourcegroupname = "resourcegroupname"
	$aps = AzureRM.Compute\New-AzureVMSqlServerAutoPatchingConfig -Enable -DayOfWeek "Thursday" -MaintenanceWindowStartingHour 11 -MaintenanceWindowDuration 120  -PatchCategory "Important"

    Set-AzureRmVMSqlServerExtension -AutoPatchingSettings $aps -VMName $vmname -ResourceGroupName $resourcegroupname

En s’appuyant sur cet exemple, le tableau suivant décrit les effets concrets sur la machine virtuelle Azure cible :

|Paramètre|Résultat|
|---|---|
|**DayOfWeek**|Les correctifs sont installés tous les jeudis.|
|**MaintenanceWindowStartingHour**|Les mises à jour démarrent à 11 h 00.|
|**MaintenanceWindowsDuration**|Les correctifs doivent être installés dans les 120 minutes. Selon l’heure de début, leur installation doit être terminée à 13 h 00 au plus tard.|
|**PatchCategory**|La seule définition possible pour ce paramètre est « Important ».|

L’installation et la configuration de l’agent IaaS de SQL Server peuvent prendre plusieurs minutes.

Pour désactiver la mise à jour corrective automatisée, exécutez le même script sans le paramètre **-Enable** pour la commande **AzureRM.Compute\\New-AzureVMSqlServerAutoPatchingConfig**. L’absence du paramètre **-Enable** indique à la commande de désactiver la fonctionnalité.

## Étapes suivantes

Pour plus d’informations sur les autres tâches d’automatisation disponibles, consultez la page [SQL Server IaaS Agent Extension](virtual-machines-windows-sql-server-agent-extension.md) (Extension de l’agent IaaS SQL Server).

Pour plus d’informations sur l’exécution de SQL Server sur des machines virtuelles Azure, consultez la page [Vue d’ensemble de SQL Server dans Azure Virtual Machines](virtual-machines-windows-sql-server-iaas-overview.md).

<!---HONumber=AcomDC_0720_2016-->
