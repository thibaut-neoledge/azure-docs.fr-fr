# Vue d'ensemble
## [Qu’est-ce que Site Recovery ?](site-recovery-overview.md)
## [Comment Site Recovery fonctionne-t-il ?](site-recovery-azure-to-azure-architecture.md)
## [Fonctionnement de la réplication Hyper-V dans Azure](site-recovery-hyper-v-azure-architecture.md)
## [Quelles charges de travail pouvez-vous protéger ?](site-recovery-workload.md)
## [Matrice de prise en charge Site Recovery](site-recovery-support-matrix-azure-to-azure.md)
## [FORUM AUX QUESTIONS](site-recovery-faq.md)
## [Voir une présentation](https://azure.microsoft.com/resources/videos/index/?services=site-recovery)

# Prise en main
## [Répliquer des machines virtuelles Azure (version préliminaire)](site-recovery-azure-to-azure.md)
## [Réplication de machines virtuelles VMware dans Azure](site-recovery-vmware-to-azure.md)
## [Répliquer des serveurs physiques dans Azure](site-recovery-physical-servers-to-azure.md)
## [Répliquer des machines virtuelles Hyper-V dans Azure (avec VMM)](site-recovery-vmm-to-azure.md)
## [Réplication de machines virtuelles Hyper-V dans Azure](site-recovery-hyper-v-site-to-azure.md)
## [Répliquer des machines virtuelles Hyper-V vers un site secondaire (avec VMM)](site-recovery-vmm-to-vmm.md)
## [Répliquer des machines virtuelles VMware et des serveurs physiques vers un site secondaire](site-recovery-vmware-to-vmware.md)
## [Répliquer des machines virtuelles VMware dans Azure dans le cadre d’un déploiement mutualisé (CSP)](site-recovery-multi-tenant-support-vmware-using-csp.md)

# Procédure
## Planification
### [Conditions préalables pour la réplication Azure](site-recovery-azure-to-azure-prereq.md)
### [Planifier la connectivité réseau sortante pour des machines virtuelles Azure (version préliminaire)](site-recovery-azure-to-azure-networking-guidance.md)
### [Planifier l’infrastructure réseau pour des ordinateurs locaux](site-recovery-network-design.md)
### [Planifier le mappage réseau](site-recovery-network-mapping-azure-to-azure.md)
### [Planifier la capacité et mettre à l’échelle la réplication VMware dans Azure](site-recovery-plan-capacity-vmware.md)
### [Deployment Planner pour la réplication de VMware vers Azure](site-recovery-deployment-planner.md)
### [Capacity Planner pour la réplication Hyper-V](site-recovery-capacity-planner.md)
### [Contrôler la réplication des machines virtuelles avec accès en fonction du rôle](site-recovery-role-based-linked-access-control.md)

## Configuration
### [Configurer l’environnement source](site-recovery-set-up-vmware-to-azure.md)
### [Configurer l’environnement cible](site-recovery-prepare-target-vmware-to-azure.md)
### [Configurer les paramètres de réplication](site-recovery-setup-replication-settings-vmware.md)
### [Déployer le service Mobilité pour la réplication VMware](site-recovery-vmware-to-azure-install-mob-svc.md)
#### [Déployer le service Mobilité avec System Center Configuration Manager](site-recovery-install-mobility-service-using-sccm.md)
#### [Déployer le service Mobilité avec Azure Automation DSC](site-recovery-automate-mobility-service-install.md)
### [Activer la réplication](site-recovery-replicate-azure-to-azure.md)
## Basculement et restauration automatique
### [Configurer des plans de récupération](site-recovery-create-recovery-plans.md)
#### [Ajouter des Runbooks Azure à des plans de récupération](site-recovery-runbook-automation.md)
### [Exécuter un test de basculement](site-recovery-test-failover-to-azure.md)
### [Basculer des machines protégées](site-recovery-failover.md)
### [Restaurer la protection des machines après un basculement](site-recovery-how-to-reprotect-azure-to-azure.md)
### [Effectuer une restauration automatique à partir d’Azure](site-recovery-failback-azure-to-vmware.md)

## Migrer
### [Migration vers Azure](site-recovery-migrate-to-azure.md)
### [Migrer entre des régions Azure](site-recovery-migrate-azure-to-azure.md)
### [Migrer des instances Windows AWS dans Azure](site-recovery-migrate-aws-to-azure.md)
### [Répliquer des ordinateurs migrés sur une autre région Azure](site-recovery-azure-to-azure-after-migration.md)

## Charges de travail
### [Active Directory et DNS](site-recovery-active-directory.md)
### [SQL Server](site-recovery-sql.md)
### [SharePoint](site-recovery-sharepoint.md)
### [Dynamics AX](site-recovery-dynamicsax.md)
### [RDS](site-recovery-workload.md#protect-rds)
### [Microsoft Exchange](site-recovery-workload.md#protect-exchange)
### [SAP](site-recovery-workload.md#protect-sap)
### [Applications web basées sur IIS](site-recovery-iis.md)
### [Citrix XenApp et XenDesktop](site-recovery-citrix-xenapp-and-xendesktop.md)
### [Autres charges de travail](site-recovery-workload.md#workload-summary)
## Automatiser la réplication
### [Automatiser la réplication Hyper-V dans Azure (sans VMM)](site-recovery-deploy-with-powershell-resource-manager.md)
### [Automatiser la réplication Hyper-V dans Azure (avec VMM)](site-recovery-vmm-to-azure-powershell-resource-manager.md)
### [Automatiser la réplication Hyper-V vers un site secondaire (avec VMM)](site-recovery-vmm-to-vmm-powershell-resource-manager.md)
## Gérer
### [Modifier les paramètres de réplication](site-recovery-setup-replication-settings-vmware.md#edit-replication-policy.md)
### [Gérer des serveurs de processus dans Azure](site-recovery-vmware-setup-azure-ps-resource-manager.md)
### [Gérer le serveur de configuration](site-recovery-vmware-to-azure-manage-configuration-server.md)
### [Gérer des serveurs de processus de montée en puissance parallèle](site-recovery-vmware-to-azure-manage-scaleout-process-server.md)
### [Gérer des serveurs vCenter](site-recovery-vmware-to-azure-manage-vCenter.md)
### [Supprimer des serveurs et désactiver la protection](site-recovery-manage-registration-and-protection.md)
## Surveiller et résoudre des problèmes
### [Problèmes de réplication de machines virtuelles Azure](site-recovery-azure-to-azure-troubleshoot-errors.md)
### [Problèmes de réplication d’un site local vers Azure](site-recovery-monitoring-and-troubleshooting.md)

# Référence
## [PowerShell](/powershell/module/azurerm.siterecovery)
## [PowerShell Classic](/powershell/module/azure/?view=azuresmps-3.7.0)
## [REST](https://msdn.microsoft.com/en-us/library/mt750497)

# Rubriques connexes
## [Azure Automation](/azure/automation/)

# les ressources
## [Parcours d’apprentissage](https://azure.microsoft.com/documentation/learning-paths/site-recovery/)
## [Forum](https://social.msdn.microsoft.com/Forums/azure/en-US/home?forum=hypervrecovmgr)
## [Blog](http://azure.microsoft.com/blog/tag/azure-site-recovery/)
## [Tarification](https://azure.microsoft.com/pricing/details/site-recovery/)
## [Mises à jour de service](https://azure.microsoft.com/updates/?product=site-recovery)
