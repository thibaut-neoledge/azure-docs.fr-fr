# Vue d'ensemble
## [Qu’est-ce que Site Recovery ?](site-recovery-overview.md)
## [Comment Site Recovery fonctionne-t-il ?](site-recovery-components.md)
## [Quelles charges de travail pouvez-vous protéger ?](site-recovery-workload.md)
## [Matrice de prise en charge Site Recovery](site-recovery-support-matrix.md)
## [FORUM AUX QUESTIONS](site-recovery-faq.md)
## [Voir une présentation](https://www.youtube.com/watch?v=eOOwMQPBKfM)

# Prise en main
## [Répliquer des machines virtuelles VMware dans Azure](site-recovery-vmware-to-azure.md)
## [Répliquer des machines virtuelles VMware dans Azure dans le cadre d’un déploiement mutualisé (CSP)](site-recovery-multi-tenant-support-vmware-using-csp.md)
## [Répliquer des machines virtuelles Hyper-V dans Azure (avec VMM)](site-recovery-vmm-to-azure.md)
## [Réplication de machines virtuelles Hyper-V dans Azure](site-recovery-hyper-v-site-to-azure.md)
## [Répliquer des machines virtuelles VMware et des serveurs physiques vers un site secondaire](site-recovery-vmware-to-vmware.md)
## [Répliquer des machines virtuelles Hyper-V vers un site secondaire (avec VMM)](site-recovery-vmm-to-vmm.md)

# Procédure
## Planification
### [Conditions préalables au déploiement](site-recovery-prereq.md)
### [Considérations sur l’infrastructure réseau](site-recovery-network-design.md)
### [Utiliser l’outil Site Recovery Capacity Planner](site-recovery-capacity-planner.md)
### [Planifier la capacité et mettre à l’échelle la réplication VMware dans Azure](site-recovery-plan-capacity-vmware.md)
## Configuration
### [Configurer l’environnement de réplication source](site-recovery-set-up-vmware-to-azure.md)
### [Configurer les paramètres de réplication](site-recovery-setup-replication-settings-vmware.md)
### [Déployer le service Mobilité pour la réplication VMware](site-recovery-vmware-to-azure-install-mob-svc.md)
#### [Déployer le service Mobilité à l’aide de System Center Configuration Manager](site-recovery-install-mobility-service-using-sccm.md)
#### [Déployer le service Mobilité à l’aide d’Azure Automation DSC](site-recovery-automate-mobility-service-install.md)
### [Supprimer des serveurs et désactiver la protection](site-recovery-manage-registration-and-protection.md)
## Migrer
### [Migration vers Azure](site-recovery-migrate-to-azure.md)
### [Migrer entre des régions Azure](site-recovery-migrate-azure-to-azure.md)
### [Migrer des instances Windows AWS dans Azure](site-recovery-migrate-aws-to-azure.md)
## Charges de travail
### [Active Directory et DNS](site-recovery-active-directory.md)
### [SQL Server](site-recovery-sql.md)
### [SharePoint](site-recovery-workload.md#protect-sharepoint)
### [Dynamics AX](site-recovery-workload.md#protect-dynamics-ax)
### [RDS](site-recovery-workload.md#protect-rds)
### [Microsoft Exchange](site-recovery-workload.md#protect-exchange)
### [SAP](site-recovery-workload.md#protect-sap)
### [Autres charges de travail](site-recovery-workload.md#workload-summary)
## Automatiser la réplication
### [Automatiser la réplication Hyper-V dans Azure (sans VMM)](site-recovery-deploy-with-powershell-resource-manager.md)
### [Automatiser la réplication Hyper-V dans Azure (avec VMM)](site-recovery-vmm-to-azure-powershell-resource-manager.md)
### [Automatiser la réplication Hyper-V vers un site secondaire (avec VMM)](site-recovery-vmm-to-vmm-powershell-resource-manager.md)
## Basculement et restauration automatique
### [Basculement dans Site Recovery](site-recovery-failover.md)
### [Configurer des plans de récupération](site-recovery-create-recovery-plans.md)
#### [Ajouter des Runbooks Azure à des plans de récupération](site-recovery-runbook-automation.md)
### [Exécuter un test de basculement de VMware vers Azure](site-recovery-test-failover-to-azure.md)
### [Exécuter un test de basculement entre deux sites VMM](site-recovery-test-failover-vmm-to-vmm.md)
### [Restaurer automatiquement des serveurs physiques et des machines virtuelles VMware](site-recovery-failback-azure-to-vmware.md)
## [Surveiller et résoudre des problèmes](site-recovery-monitoring-and-troubleshooting.md)

# Référence
## [PowerShell](/powershell/resourcemanager/azurerm.siterecovery/v3.2.0/azurerm.siterecovery)
## [PowerShell Classic](/powershell/servicemanagement/azure.siterecovery/v3.1.0/azure.siterecovery)
## [REST](https://msdn.microsoft.com/en-us/library/mt750497)

# Rubriques connexes
## [Azure Automation](/azure/automation/)

# les ressources
## [Parcours d’apprentissage](https://azure.microsoft.com/documentation/learning-paths/site-recovery/)
## [Forum](https://social.msdn.microsoft.com/Forums/azure/en-US/home?forum=hypervrecovmgr)
## [Blog](http://azure.microsoft.com/blog/tag/azure-site-recovery/)
## [Tarification](https://azure.microsoft.com/pricing/details/site-recovery/)
## [Mises à jour de service](https://azure.microsoft.com/updates/?product=site-recovery)


<!--HONumber=Feb17_HO3-->


