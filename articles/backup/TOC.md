
# Vue d'ensemble
## [Qu’est-ce qu’Azure Backup ?](backup-introduction-to-azure-backup.md)

# Prise en main
## [Sauvegarder des fichiers et dossiers](backup-try-azure-backup-in-10-mins.md)
## [Sauvegarde des machines virtuelles Azure](backup-azure-vms-first-look.md)
## [Protéger des machines virtuelles Azure](backup-azure-vms-first-look-arm.md)

# Procédures
## Utiliser PowerShell
### [Machines virtuelles Azure dans le portail Azure](backup-azure-vms-automation.md)
### [Machines virtuelles Azure dans le portail classique](backup-azure-vms-classic-automation.md)
### [DPM dans le portail Azure](backup-dpm-automation.md)
### [DPM dans le portail classique](backup-dpm-automation-classic.md)
### [Windows Server dans le portail Azure](backup-client-automation.md)
### [Windows Server dans le portail classique](backup-client-automation-classic.md)

## Azure Backup Server
### [Préparer les charges de travail du serveur de sauvegarde Azure dans le portail Azure](backup-azure-microsoft-azure-backup.md)
### [Préparer les charges de travail du serveur de sauvegarde Azure dans le portail classique](backup-azure-microsoft-azure-backup-classic.md)
### [Utiliser Azure Backup Server pour sauvegarder Exchange](backup-azure-exchange-mabs.md)
### [Utiliser Azure Backup Server pour sauvegarder une batterie de serveurs SharePoint](backup-azure-backup-sharepoint-mabs.md)
### [Utiliser Azure Backup Server pour sauvegarder SQL](backup-azure-sql-mabs.md)

## Gestionnaire de protection des données
### [Préparer les charges de travail DPM dans le portail Azure](backup-azure-dpm-introduction.md)
### [Préparer les charges de travail DPM dans le portail classique](backup-azure-dpm-introduction-classic.md)
### [Utiliser System Center DPM pour sauvegarder un serveur Exchange](backup-azure-backup-exchange-server.md)
### [Récupérer des données dans le coffre de sauvegarde vers un autre serveur DPM](backup-azure-alternate-dpm-server.md)
### [Utiliser DPM pour sauvegarder des charges de travail SQL Server](backup-azure-backup-sql.md)
### [Utiliser DPM pour sauvegarder une batterie de serveurs SharePoint](backup-azure-backup-sharepoint.md)

## Machines virtuelles Azure
### [Préparer des machines virtuelles Azure](backup-azure-vms-prepare.md)
### [Préparer des machines virtuelles déployées par Resource Manager](backup-azure-arm-vms-prepare.md)
### [Planifier l’infrastructure de sauvegarde de machines virtuelles](backup-azure-vms-introduction.md)
### [Sauvegarde des machines virtuelles Azure dans le coffre de sauvegarde](backup-azure-vms.md)
### [Sauvegarder des machines virtuelles Azure dans un coffre Recovery Services](backup-azure-arm-vms.md)
### [Sauvegarfder et Restaurer des machines virtuelles chiffrées](backup-azure-vms-encryption.md)
### [Gérer et surveiller les sauvegardes de machines Virtuelles Azure dans le portail classique](backup-azure-manage-vms-classic.md)
### [Gérer les sauvegardes de machines Virtuelles Azure dans le portail Azure](backup-azure-manage-vms.md)
### [Surveiller des alertes pour les sauvegardes de machines virtuelles Azure dans le portail Azure](backup-azure-monitor-vms.md)
### [Récupérer des fichiers à partir de sauvegardes de machines virtuelles Azure](backup-azure-restore-files-from-vm.md)
### [Restaurer des machines virtuelles dans Azure](backup-azure-restore-vms.md)
### [Restaurer des machines virtuelles déployées à l’aide de Resource Manager dans le portail Azure](backup-azure-arm-restore-vms.md)
### [Restaurer la clé et le secret du Key Vault pour les machines virtuelles chiffrées à l’aide d’Azure Backup](backup-azure-restore-key-secret.md)

## Azure SQL Database
### [Configurer la rétention des sauvegardes à long terme](../sql-database/sql-database-configure-long-term-retention.md?toc=%2fazure%2fbackup%2ftoc.json)
### [Afficher les sauvegardes dans un coffre Recovery Services](../sql-database/sql-database-view-backups-in-vault.md?toc=%2fazure%2fbackup%2ftoc.json)
### [Restaurer à partir d’une rétention des sauvegardes à long terme](../sql-database/sql-database-restore-from-long-term-retention.md?toc=%2fazure%2fbackup%2ftoc.json)
### [Supprimer des sauvegardes à long terme SQL Azure](../sql-database/sql-database-long-term-retention-delete.md?toc=%2fazure%2fbackup%2ftoc.json)

## Fichiers et dossiers Windows
### [Windows Server avec le modèle de déploiement classique](backup-configure-vault-classic.md)
### [Windows Server avec le modèle de déploiement Resource Manager](backup-configure-vault.md)
### [Gérer les coffres Sauvegarde à l’aide du modèle de déploiement classique](backup-azure-manage-windows-server-classic.md)
### [Surveiller et gérer les coffres Recovery Services n](backup-azure-manage-windows-server.md)
### [Récupérer des fichiers sur un serveur Windows à l’aide du modèle de déploiement Resource Manager](backup-azure-restore-windows-server.md)
### [Récupérer des fichiers sur un client Windows à l’aide du modèle de déploiement classique](backup-azure-restore-windows-server-classic.md)

## [FAQ](backup-azure-backup-faq.md)

## Résolution des problèmes
### [Problèmes de sauvegarde de machines virtuelles Azure dans le portail Azure](backup-azure-vms-troubleshoot.md)
### [Problèmes de sauvegarde de machines virtuelles Azure dans le portail classique](backup-azure-vms-troubleshoot-classic.md)
### [Azure Backup Server](backup-azure-mabs-troubleshoot.md)
### [Échec de sauvegarde des machines virtuelles Azure : impossible de communiquer avec l’agent de machine virtuelle pour obtenir l’état de l’instantané ; la sous-tâche de capture instantanée de la machine virtuelle a expiré.](backup-azure-troubleshoot-vm-backup-fails-snapshot-timeout.md)
### [Sauvegarde lente de fichiers et de dossiers dans Sauvegarde Azure](backup-azure-troubleshoot-slow-backup-performance-issue.md)

# Concepts
## [Contrôle d’accès en fonction du rôle](backup-rbac-rs-vault.md)
## [Sécurité pour les sauvegardes hybrides](backup-azure-security-feature.md)
## [Supprimer un coffre de Sauvegarde Azure](backup-azure-delete-vault.md)
## [Configurer la sauvegarde hors connexion](backup-azure-backup-import-export.md)
## [Remplacer votre bibliothèque de bandes](backup-azure-backup-cloud-as-tape.md)

# Référence
## [PowerShell](/powershell/resourcemanager/azurerm.recoveryservices.backup/v2.3.0/azurerm.recoveryservices.backup)
## [.NET](/dotnet/api/microsoft.azure.management.recoveryservices.backup)

# les ressources
## [Tarification](https://azure.microsoft.com/pricing/details/backup/)
## [Forum MSDN](https://social.msdn.microsoft.com/Forums/en-US/home?forum=windowsazureonlinebackup)
## [Vidéos](https://azure.microsoft.com/documentation/videos/index/?services=backup)
## [Mises à jour de service](https://azure.microsoft.com/updates/?product=backup)
