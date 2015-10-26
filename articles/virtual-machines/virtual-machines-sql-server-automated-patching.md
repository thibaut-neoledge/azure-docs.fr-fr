<properties
   pageTitle="Mise à jour corrective automatisée de SQL Server dans les machines virtuelles | Microsoft Azure"
   description="Décrit la fonctionnalité de mise à jour corrective automatisée pour les machines virtuelles SQL Server s’exécutant dans Azure."
   services="virtual-machines"
   documentationCenter="na"
   authors="rothja"
   manager="jeffreyg"
   editor="monicar"
   tags="azure-resource-manager" />
<tags
   ms.service="virtual-machines"
   ms.devlang="na"
   ms.topic="article"
   ms.tgt_pltfrm="vm-windows-sql-server"
   ms.workload="infrastructure-services"
   ms.date="08/05/2015"
   ms.author="jroth" />

# Mise à jour corrective automatisée pour SQL Server dans les machines virtuelles Azure

[AZURE.INCLUDE [learn-about-deployment-models](../../includes/learn-about-deployment-models-classic-include.md)]Modèle Resource Manager


La mise à jour corrective automatisée établit une fenêtre de maintenance pour une machine virtuelle Azure exécutant SQL Server 2012 ou 2014. Les mises à jour automatisées ne peuvent être installées qu’au cours de cette fenêtre de maintenance. Pour SQL Server, les mises à jour système et les redémarrages associés ont ainsi lieu au meilleur moment possible pour la base de données. Cela dépend de l’agent IaaS de SQL Server.

>[AZURE.NOTE]La mise à jour corrective automatisée utilise l’agent IaaS de SQL Server. Pour installer et configurer l’agent, vous devez disposer de l’agent Azure VM s’exécutant sur la machine virtuelle cible. Par défaut, cette option est activée dans les nouvelles images de galerie de machines virtuelles Azure, mais l’agent Azure VM peut être manquant sur les machines virtuelles existantes. Si vous utilisez votre propre image de machine virtuelle, vous devez également installer l’agent IaaS de SQL Server. Pour plus d’informations, consultez la page [Extensions et agent de machine virtuelle](http://azure.microsoft.com/blog/2014/04/15/vm-agent-and-extensions-part-2/).

## Configurer une mise à jour corrective automatisée dans le portail

Vous pouvez utiliser le [portail Azure en version préliminaire](http://go.microsoft.com/fwlink/?LinkID=525040&clcid=0x409) pour configurer une mise à jour corrective automatisée lorsque vous créez une machine virtuelle SQL Server. La capture d’écran suivante présente ces options sous **CONFIGURATION FACULTATIVE** | **MISE À JOUR CORRECTIVE AUTOMATISÉE SQL**.

![Mise à jour corrective automatique SQL dans le portail Azure](./media/virtual-machines-sql-server-automated-patching/IC778484.jpg)

Pour les machines virtuelles SQL Server 2012 ou 2014 existantes, sélectionnez les paramètres de **mise à jour corrective automatisée** dans la section **Configuration** des propriétés de la machine virtuelle. Dans la fenêtre **Mise à jour corrective automatisée**, vous pouvez activer la fonctionnalité, définir la planification de la maintenance et l’heure de début, et choisir la durée de la fenêtre de maintenance. Cette situation est présentée dans la capture d’écran suivante.

![Configuration d’une mise à jour corrective automatisée dans le portail Azure](./media/virtual-machines-sql-server-automated-patching/IC792132.jpg)

>[AZURE.NOTE]Lorsque vous activez la mise à jour corrective automatisée pour la première fois, Azure configure l’agent IaaS de SQL Server en arrière-plan. Pendant ce temps, le portail n’indique pas que la mise à jour corrective automatisée est configurée. Patientez quelques minutes jusqu’à ce que l’agent soit installé et configuré. Le portail reflète alors les nouveaux paramètres.

## Configurer une mise à jour corrective automatisée avec PowerShell

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

## Désactivation et désinstallation de l’agent IaaS de SQL Server

Si vous voulez désactiver l’agent IaaS de SQL Server pour la sauvegarde et la mise à jour corrective automatisées, utilisez la commande suivante :

    Get-AzureVM -ServiceName <vmservicename> -Name <vmname> | Set-AzureVMSqlServerExtension -Disable | Update-AzureVM

Pour désinstaller l’Agent IaaS de SQL Server, utilisez la syntaxe suivante :

    Get-AzureVM -ServiceName <vmservicename> -Name <vmname> | Set-AzureVMSqlServerExtension –Uninstall | Update-AzureVM

Vous pouvez également désinstaller l’extension à l’aide de la commande **Remove-AzureVMSqlServerExtension** :

    Get-AzureVM -ServiceName <vmservicename> -Name <vmname> | Remove-AzureVMSqlServerExtension | Update-AzureVM

## Compatibilité

Les produits suivants sont compatibles avec les fonctionnalités de l’agent IaaS de SQL Server pour la mise à jour corrective automatisée :

- Windows Server 2012

- Windows Server 2012 R2

- SQL Server 2012

- SQL Server 2014

## Étapes suivantes

La [sauvegarde automatisée pour SQL Server dans les machines virtuelles Azure](virtual-machines-sql-server-automated-backup.md) est une fonctionnalité associée pour les machines virtuelles SQL Server dans Azure.

Passez en revue les autres [ressources liées à l'exécution de SQL Server dans des machines virtuelles Azure](virtual-machines-sql-server-infrastructure-services.md).

<!---HONumber=Oct15_HO3-->