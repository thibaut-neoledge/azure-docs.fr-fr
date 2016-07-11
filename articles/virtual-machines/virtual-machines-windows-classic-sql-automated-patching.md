<properties
	pageTitle="Mise à jour corrective automatisée pour les machines virtuelles SQL Server (classiques) | Microsoft Azure"
	description="Décrit la fonctionnalité de mise à jour corrective automatisée pour les machines virtuelles SQL Server exécutées dans Azure à l’aide du modèle de déploiement classique."
	services="virtual-machines-windows"
	documentationCenter="na"
	authors="rothja"
	manager="jhubbard"
	editor=""
	tags="azure-service-management" />
<tags
	ms.service="virtual-machines-windows"
	ms.devlang="na"
	ms.topic="article"
	ms.tgt_pltfrm="vm-windows-sql-server"
	ms.workload="infrastructure-services"
	ms.date="05/18/2016"
	ms.author="jroth" />

# Mise à jour corrective automatisée pour SQL Server dans les machines virtuelles Azure (classiques)

> [AZURE.SELECTOR]
- [Gestionnaire de ressources](virtual-machines-windows-sql-automated-patching.md)
- [Classique](virtual-machines-windows-classic-sql-automated-patching.md)

La mise à jour corrective automatisée établit une fenêtre de maintenance pour une machine virtuelle Azure exécutant SQL Server. Les mises à jour automatisées ne peuvent être installées qu’au cours de cette fenêtre de maintenance. Pour SQL Server, les mises à jour système et les redémarrages associés ont ainsi lieu au meilleur moment possible pour la base de données. La mise à jour corrective automatisée utilise l’[extension de l’agent IaaS de SQL Server](virtual-machines-windows-classic-sql-server-agent-extension.md).

[AZURE.INCLUDE [learn-about-deployment-models](../../includes/learn-about-deployment-models-classic-include.md)] 
Pour afficher la version Resource Manager de cet article, consultez [Mise à jour corrective automatisée pour SQL Server dans les machines virtuelles Azure (classiques)](virtual-machines-windows-sql-automated-patching.md).

## Composants requis

Pour utiliser la mise à jour corrective automatisée, prenez en compte les conditions préalables suivantes :

**Système d’exploitation** :

- Windows Server 2012
- Windows Server 2012 R2

**Version de SQL Server** :

- SQL Server 2012
- SQL Server 2014
- SQL Server 2016

**Azure PowerShell** :

- [Installez les dernières commandes Azure PowerShell](../powershell-install-configure.md) si vous projetez de configurer la mise à jour corrective automatisée avec PowerShell.

>[AZURE.NOTE] La mise à jour corrective automatisée utilise l’extension de l’agent IaaS SQL Server. Les images actuelles de la galerie de machines virtuelles SQL ajoutent cette extension par défaut. Pour plus d’informations, consultez [SQL Server IaaS Agent Extension](virtual-machines-windows-classic-sql-server-agent-extension.md) (Extension de l’agent IaaS SQL Server).

## Paramètres

Le tableau suivant décrit les options qui peuvent être configurées pour une mise à jour corrective automatisée. Les étapes de la configuration varient selon que vous utilisez les commandes du portail Azure ou Azure Windows PowerShell.

|Paramètre|Valeurs possibles|Description|
|---|---|---|
|**Mise à jour corrective automatisée**|Activer/Désactiver (désactivé)|Active ou désactive la mise à jour corrective automatisée pour une machine virtuelle Azure.|
|**Planification de la maintenance**|Tous les jours, lundi, mardi, mercredi, jeudi, vendredi, samedi et dimanche|Planification du téléchargement et de l’installation des mises à jour Windows, SQL Server et Microsoft pour votre machine virtuelle.|
|**Heure de début de la maintenance**|0-24|Heure locale de début de la mise à jour de la machine virtuelle.|
|**Durée de la fenêtre de maintenance**|30 à 180|Nombre de minutes autorisées pour terminer le téléchargement et l’installation des mises à jour.|
|**Catégorie de correctif**|Important|Catégorie des mises à jour à télécharger et installer.|

## Configuration dans le portail

Vous pouvez utiliser le [portail Azure](http://go.microsoft.com/fwlink/?LinkID=525040&clcid=0x409) pour configurer une mise à jour corrective automatisée lorsque vous créez une machine virtuelle SQL Server dans le modèle de déploiement classique.

La capture d’écran du portail Azure suivante présente ces options sous **CONFIGURATION FACULTATIVE** | **MISE À JOUR CORRECTIVE AUTOMATISÉE SQL**.

![Mise à jour corrective automatique SQL dans le portail Azure](./media/virtual-machines-windows-classic-sql-automated-patching/IC778484.jpg)

Pour les machines virtuelles SQL Server 2012 ou 2014 existantes, sélectionnez les paramètres de **mise à jour corrective automatisée** dans la section **Configuration** des propriétés. Dans la fenêtre **Mise à jour corrective automatisée**, vous pouvez activer la fonctionnalité, définir la planification de la maintenance et l’heure de début, et choisir la durée de la fenêtre de maintenance. Cette situation est présentée dans la capture d’écran suivante.

![Configuration d’une mise à jour corrective automatisée dans le portail Azure](./media/virtual-machines-windows-classic-sql-automated-patching/IC792132.jpg)

>[AZURE.NOTE] Lorsque vous activez la mise à jour corrective automatisée pour la première fois, Azure configure l’agent IaaS de SQL Server en arrière-plan. Pendant ce temps, le portail Azure n’indique pas que la mise à jour corrective automatisée est configurée. Patientez quelques minutes jusqu’à ce que l’agent soit installé et configuré. Le portail Azure reflète alors les nouveaux paramètres.

## Configuration avec PowerShell

Vous pouvez également utiliser PowerShell pour configurer une mise à jour corrective automatisée.

Dans l’exemple suivant, PowerShell permet de configurer une mise à jour corrective automatisée sur une machine virtuelle SQL Server existante. La commande **New-AzureVMSqlServerAutoPatchingConfig** configure une nouvelle fenêtre de maintenance pour les mises à jour automatiques.

    $aps = New-AzureVMSqlServerAutoPatchingConfig -Enable -DayOfWeek "Thursday" -MaintenanceWindowStartingHour 11 -MaintenanceWindowDuration 120  -PatchCategory "Important"

    Get-AzureVM -ServiceName <vmservicename> -Name <vmname> | Set-AzureVMSqlServerExtension -AutoPatchingSettings $aps | Update-AzureVM

En s’appuyant sur cet exemple, le tableau suivant décrit les effets concrets sur la machine virtuelle Azure cible :

|Paramètre|Résultat|
|---|---|
|**DayOfWeek**|Les correctifs sont installés tous les jeudis.|
|**MaintenanceWindowStartingHour**|Les mises à jour démarrent à 11 h 00.|
|**MaintenanceWindowsDuration**|Les correctifs doivent être installés dans les 120 minutes. Selon l’heure de début, leur installation doit être terminée à 13 h 00 au plus tard.|
|**PatchCategory**|La seule définition possible pour ce paramètre est « Important ».|

L’installation et la configuration de l’agent IaaS de SQL Server peuvent prendre plusieurs minutes.

Pour désactiver la mise à jour corrective automatisée, exécutez le même script sans le paramètre -Enable pour la commande New-AzureVMSqlServerAutoPatchingConfig. À l’instar de l’installation, la désactivation de la mise à jour corrective automatisée peut prendre plusieurs minutes.

## Étapes suivantes

Pour plus d’informations sur les autres tâches d’automatisation disponibles, consultez la page [SQL Server IaaS Agent Extension](virtual-machines-windows-classic-sql-server-agent-extension.md) (Extension de l’agent IaaS SQL Server).

Pour plus d’informations sur l’exécution de SQL Server sur des machines virtuelles Azure, consultez la page [Vue d’ensemble de SQL Server dans Azure Virtual Machines](virtual-machines-windows-sql-server-iaas-overview.md).

<!---HONumber=AcomDC_0629_2016-->