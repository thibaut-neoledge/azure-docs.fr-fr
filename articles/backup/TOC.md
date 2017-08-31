
# Vue d'ensemble
## [Qu’est-ce qu’Azure Backup ?](backup-introduction-to-azure-backup.md)

# Prise en main
## [Sauvegarder des machines virtuelles Azure](backup-azure-vms-first-look-arm.md)
## [Sauvegarder les ordinateurs Windows ou les serveurs Windows Server](backup-try-azure-backup-in-10-mins.md)
## [Sauvegarder des serveurs VMware](backup-azure-backup-server-vmware.md)

# Procédures

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
### [Récupérer des données à partir d’un serveur de sauvegarde Azure](backup-azure-alternate-dpm-server.md)

## Machines virtuelles Azure
### Préparation de la machine virtuelle
#### [Préparer des machines virtuelles déployées par Resource Manager](backup-azure-arm-vms-prepare.md)
#### [Sauvegardes cohérentes des applications des machines virtuelles Linux](backup-azure-linux-app-consistent.md)
#### [Préparer des machines virtuelles Azure](backup-azure-vms-prepare.md)
### Planifier votre environnement
#### [Planifier l’infrastructure de sauvegarde de machines virtuelles](backup-azure-vms-introduction.md)
### Sauvegarder des machines virtuelles
#### [Sauvegarder des machines virtuelles Azure dans un coffre Recovery Services](backup-azure-arm-vms.md)
#### [Sauvegarder des machines virtuelles chiffrées](backup-azure-vms-encryption.md)
#### [Sauvegarde des machines virtuelles Azure](backup-azure-vms.md)
### Gestion et surveillance des machines virtuelles
#### [Gérer les sauvegardes de machines Virtuelles Azure dans le portail Azure](backup-azure-manage-vms.md)
#### [Surveiller des alertes pour les sauvegardes de machines virtuelles Azure dans le portail Azure](backup-azure-monitor-vms.md)
#### [Gérer et surveiller les sauvegardes de machines Virtuelles Azure dans le portail classique](backup-azure-manage-vms-classic.md)
### Restauration de données à partir de machines virtuelles
#### [Récupérer des fichiers à partir de sauvegardes de machines virtuelles Azure](backup-azure-restore-files-from-vm.md)
#### [Restaurer des machines virtuelles déployées à l’aide de Resource Manager dans le portail Azure](backup-azure-arm-restore-vms.md)
#### [Restaurer des machines virtuelles chiffrées](backup-azure-vms-encryption.md)
#### [Restaurer des machines virtuelles dans Azure](backup-azure-restore-vms.md)
#### [Restaurer la clé et le secret du Key Vault pour les machines virtuelles chiffrées](backup-azure-restore-key-secret.md)

## Configurer les rapports de la Sauvegarde Azure
### [Configurer les rapports de sauvegarde Azure](backup-azure-configure-reports.md)
### [Modèle de données pour les rapports de sauvegarde Azure](backup-azure-reports-data-model.md)
### [Modèle de données Log Analytics pour la sauvegarde Azure](backup-azure-log-analytics-data-model.md)

## Gestionnaire de protection des données
### [Préparer les charges de travail DPM dans le portail Azure](backup-azure-dpm-introduction.md)
### [Préparer les charges de travail DPM dans le portail classique](backup-azure-dpm-introduction-classic.md)
### [Utiliser System Center DPM pour sauvegarder un serveur Exchange](backup-azure-backup-exchange-server.md)
### [Récupérer des données sur un autre serveur DPM](backup-azure-alternate-dpm-server.md)
### [Utiliser DPM pour sauvegarder des charges de travail SQL Server](backup-azure-backup-sql.md)
### [Utiliser DPM pour sauvegarder une batterie de serveurs SharePoint](backup-azure-backup-sharepoint.md)

## Utiliser PowerShell
### [Machines virtuelles Azure dans le portail Azure](backup-azure-vms-automation.md)
### [Machines virtuelles Azure dans le portail classique](backup-azure-vms-classic-automation.md)
### [DPM dans le portail Azure](backup-dpm-automation.md)
### [DPM dans le portail classique](backup-dpm-automation-classic.md)
### [Windows Server dans le portail Azure](backup-client-automation.md)
### [Windows Server dans le portail classique](backup-client-automation-classic.md)

## Base de données SQL Azure
### [Configurer la rétention des sauvegardes à long terme](../sql-database/sql-database-configure-long-term-retention.md?toc=%2fazure%2fbackup%2ftoc.json)
### [Afficher les sauvegardes dans un coffre Recovery Services](../sql-database/sql-database-view-backups-in-vault.md?toc=%2fazure%2fbackup%2ftoc.json)
### [Restaurer à partir d’une rétention des sauvegardes à long terme](../sql-database/sql-database-restore-from-long-term-retention.md?toc=%2fazure%2fbackup%2ftoc.json)
### [Supprimer des sauvegardes à long terme SQL Azure](../sql-database/sql-database-long-term-retention-delete.md?toc=%2fazure%2fbackup%2ftoc.json)

## Windows Server
### [Sauvegarder les fichiers et dossiers de Windows Server](backup-configure-vault.md)
### [Sauvegarder l’état du système Windows Server](backup-azure-system-state.md)
### [Récupérer des fichiers à partir d’Azure vers Windows Server](backup-azure-restore-windows-server.md)
### [Restaurer l’état du système Windows Server](backup-azure-restore-system-state.md)
### [Surveiller et gérer les coffres Recovery Services n](backup-azure-manage-windows-server.md)
### Sauvegarder et restaurer à l’aide du portail classique
#### [Windows Server avec le modèle de déploiement classique](backup-configure-vault-classic.md)
#### [Gérer les coffres Sauvegarde à l’aide du modèle de déploiement classique](backup-azure-manage-windows-server-classic.md)
#### [Récupérer des fichiers sur un client Windows à l’aide du modèle de déploiement classique](backup-azure-restore-windows-server-classic.md)

## Coffre Recovery Services
### [Vue d’ensemble des coffres Recovery Services](backup-azure-recovery-services-vault-overview.md)
### [Mise à niveau d’un coffre de sauvegarde vers le coffre Recovery Services](backup-azure-upgrade-backup-to-recovery-services.md)
### [Supprimer un coffre Recovery Services](backup-azure-delete-vault.md)

## Résolution des problèmes
### [Problèmes de sauvegarde de machines virtuelles Azure dans le portail Azure](backup-azure-vms-troubleshoot.md)
### [Problèmes de sauvegarde de machines virtuelles Azure dans le portail classique](backup-azure-vms-troubleshoot-classic.md)
### [Échec de sauvegarde des machines virtuelles Azure : impossible de communiquer avec l’agent de machine virtuelle pour obtenir l’état de l’instantané ; la sous-tâche de capture instantanée de la machine virtuelle a expiré.](backup-azure-troubleshoot-vm-backup-fails-snapshot-timeout.md)
### [Sauvegarde lente de fichiers et de dossiers dans Sauvegarde Azure](backup-azure-troubleshoot-slow-backup-performance-issue.md)
### [Résolution des problèmes de serveur de sauvegarde Azure](backup-azure-mabs-troubleshoot.md)

# Concepts

## Forum Aux Questions
### [FAQ sur le coffre Recovery Services](backup-azure-backup-faq.md)
### [FAQ sur la sauvegarde de la machine virtuelle Azure](backup-azure-vm-backup-faq.md)
### [FAQ sur la sauvegarde de fichiers-dossiers à l’aide de l’agent Azure Backup](backup-azure-file-folder-backup-faq.md)

## [Contrôle d’accès en fonction du rôle](backup-rbac-rs-vault.md)
## [Sécurité pour les sauvegardes hybrides](backup-azure-security-feature.md)
## [Configurer la sauvegarde hors connexion](backup-azure-backup-import-export.md)
## [Remplacer votre bibliothèque de bandes](backup-azure-backup-cloud-as-tape.md)


# Référence
## [PowerShell](/powershell/module/azurerm.recoveryservices.backup)
## [.NET](/dotnet/api/microsoft.azure.management.recoveryservices.backup)

# Ressources
## [Feuille de route Azure](https://azure.microsoft.com/roadmap/)
## [Forum MSDN](https://social.msdn.microsoft.com/Forums/en-US/home?forum=windowsazureonlinebackup)
## [Tarification](https://azure.microsoft.com/pricing/details/backup/)
## [Calculatrice de prix](https://azure.microsoft.com/pricing/calculator/)
## [Mises à jour de service](https://azure.microsoft.com/updates/?product=backup)
## [Vidéos](https://azure.microsoft.com/documentation/videos/index/?services=backup)
