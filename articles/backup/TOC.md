
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
### [Matrice de protection du serveur de sauvegarde Azure](backup-mabs-protection-matrix.md)
### Installation ou mise à niveau
#### [Préparer les charges de travail du serveur de sauvegarde Azure dans le portail Azure](backup-azure-microsoft-azure-backup.md)
#### [Préparer les charges de travail du serveur de sauvegarde Azure dans le portail classique](backup-azure-microsoft-azure-backup-classic.md)
#### [Ajouter du stockage à un serveur de sauvegarde Azure](backup-mabs-add-storage.md)
#### [Mettre à jour le serveur de sauvegarde Azure vers la version 2](backup-mabs-upgrade-to-v2.md)
#### [Installation sans assistance du serveur de sauvegarde Azure](backup-mabs-unattended-install.md)
### Protection des charges de travail
#### [Utiliser le serveur de sauvegarde Azure pour sauvegarder un serveur VMware](backup-azure-backup-server-vmware.md)
#### [Utiliser Azure Backup Server pour sauvegarder Exchange](backup-azure-exchange-mabs.md)
#### [Utiliser Azure Backup Server pour sauvegarder une batterie de serveurs SharePoint](backup-azure-backup-sharepoint-mabs.md)
#### [Utiliser Azure Backup Server pour sauvegarder SQL](backup-azure-sql-mabs.md)
#### [Protéger l’état du système et la récupération complète](backup-mabs-system-state-and-bmr.md)
### Résolution des problèmes
#### [Résolution des problèmes de serveur de sauvegarde Azure](backup-azure-mabs-troubleshoot.md)


## Gestionnaire de protection des données
### [Préparer les charges de travail DPM dans le portail Azure](backup-azure-dpm-introduction.md)
### [Préparer les charges de travail DPM dans le portail classique](backup-azure-dpm-introduction-classic.md)
### [Utiliser System Center DPM pour sauvegarder un serveur Exchange](backup-azure-backup-exchange-server.md)
### [Récupérer des données dans le coffre de sauvegarde vers un autre serveur DPM](backup-azure-alternate-dpm-server.md)
### [Utiliser DPM pour sauvegarder des charges de travail SQL Server](backup-azure-backup-sql.md)
### [Utiliser DPM pour sauvegarder une batterie de serveurs SharePoint](backup-azure-backup-sharepoint.md)

## Machines virtuelles Azure
### Préparation de la machine virtuelle
#### [Préparer des machines virtuelles Azure](backup-azure-vms-prepare.md)
#### [Préparer des machines virtuelles déployées par Resource Manager](backup-azure-arm-vms-prepare.md)
### Planifier votre environnement
#### [Planifier l’infrastructure de sauvegarde de machines virtuelles](backup-azure-vms-introduction.md)
### Sauvegarder des machines virtuelles
#### [Sauvegarder des machines virtuelles Azure dans le coffre de sauvegarde](backup-azure-vms.md)
#### [Sauvegarder des machines virtuelles Azure dans un coffre Recovery Services](backup-azure-arm-vms.md)
#### [Sauvegarder des machines virtuelles chiffrées](backup-azure-vms-encryption.md)
### Gestion et surveillance des machines virtuelles
#### [Gérer et surveiller les sauvegardes de machines Virtuelles Azure dans le portail classique](backup-azure-manage-vms-classic.md)
#### [Gérer les sauvegardes de machines Virtuelles Azure dans le portail Azure](backup-azure-manage-vms.md)
#### [Surveiller des alertes pour les sauvegardes de machines virtuelles Azure dans le portail Azure](backup-azure-monitor-vms.md)
### Restauration de données à partir de machines virtuelles
#### [Récupérer des fichiers à partir de sauvegardes de machines virtuelles Azure](backup-azure-restore-files-from-vm.md)
#### [Restaurer des machines virtuelles dans Azure](backup-azure-restore-vms.md)
#### [Restaurer des machines virtuelles déployées à l’aide de Resource Manager dans le portail Azure](backup-azure-arm-restore-vms.md)
#### [Restaurer la clé et le secret du Key Vault pour les machines virtuelles chiffrées à l’aide d’Azure Backup](backup-azure-restore-key-secret.md)
#### [Restaurer des machines virtuelles chiffrées](backup-azure-vms-encryption.md)

## Base de données SQL Azure
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
### [Échec de sauvegarde des machines virtuelles Azure : impossible de communiquer avec l’agent de machine virtuelle pour obtenir l’état de l’instantané ; la sous-tâche de capture instantanée de la machine virtuelle a expiré.](backup-azure-troubleshoot-vm-backup-fails-snapshot-timeout.md)
### [Sauvegarde lente de fichiers et de dossiers dans Sauvegarde Azure](backup-azure-troubleshoot-slow-backup-performance-issue.md)

# Concepts
## [Vue d’ensemble des coffres Recovery Services](backup-azure-recovery-services-vault-overview.md)
## [Mise à niveau d’un coffre de sauvegarde vers le coffre Recovery Services](backup-azure-upgrade-backup-to-recovery-services.md)
## [Supprimer un coffre de Sauvegarde Azure](backup-azure-delete-vault.md)
## [Contrôle d’accès en fonction du rôle](backup-rbac-rs-vault.md)
## [Sécurité pour les sauvegardes hybrides](backup-azure-security-feature.md)
## [Configurer la sauvegarde hors connexion](backup-azure-backup-import-export.md)
## [Remplacer votre bibliothèque de bandes](backup-azure-backup-cloud-as-tape.md)
## [Sauvegardes cohérentes des applications des machines virtuelles Linux](backup-azure-linux-app-consistent.md)

# Référence
## [PowerShell](/powershell/module/azurerm.recoveryservices.backup)
## [.NET](/dotnet/api/microsoft.azure.management.recoveryservices.backup)

# les ressources
## [Tarification](https://azure.microsoft.com/pricing/details/backup/)
## [Forum MSDN](https://social.msdn.microsoft.com/Forums/en-US/home?forum=windowsazureonlinebackup)
## [Vidéos](https://azure.microsoft.com/documentation/videos/index/?services=backup)
## [Mises à jour de service](https://azure.microsoft.com/updates/?product=backup)
