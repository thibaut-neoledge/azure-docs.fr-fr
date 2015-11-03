<properties
	pageTitle="Présentation d’Azure Backup | Microsoft Azure"
	description="Cet article fournit une vue d’ensemble du service Azure Backup qui permet aux clients de sauvegarder des données au niveau local et dans Azure."
	services="backup"
	documentationCenter=""
	authors="trinadhk"
	manager="shreeshd"
	editor="tysonn"/>

<tags
	ms.service="backup"
	ms.workload="storage-backup-recovery"
	ms.tgt_pltfrm="na"
	ms.devlang="na"
	ms.topic="article"
	ms.date="10/01/2015"
	ms.author="aashishr"; "trinadhk"; "jimpark"/>

# Présentation d’Azure Backup
Cet article fournit une vue d’ensemble générale de la solution de sauvegarde intégrée au cloud de Microsoft qui permet aux clients de sauvegarder leurs données présentes au niveau local ou dans Azure.

## Qu’est-ce qu’Azure Backup ?
Azure Backup est un service Azure mutualisé qui vous permet de sauvegarder vos données où qu’elles se trouvent : au niveau local ou dans Azure. Il remplace votre solution de sauvegarde locale ou hors site par une offre basée dans le cloud, fiable, sécurisée et économiquement compétitive. Ce service vous offre également la possibilité de protéger les ressources qui s’exécutent dans le cloud. Azure Backup repose sur une infrastructure de classe mondiale extensible, durable et hautement disponible. À l’aide de cette solution, vous pouvez sauvegarder des données et des applications à partir de serveurs System Center Data Protection Manager (SCDPM), de serveurs Windows, d’ordinateurs clients Windows ou de machines virtuelles IaaS (infrastructure as a service) Azure. Azure Backup et SCDPM sont les technologies fondamentales qui composent la solution de sauvegarde intégrée au cloud de Microsoft.

> [AZURE.VIDEO what-is-azure-backup]

## Point de conception cloud
Les solutions de sauvegarde traditionnelles ont évolué et considèrent désormais le cloud comme un point de terminaison similaire aux disques ou bandes. Bien que cette approche soit simple, facile à déployer et qu’elle offre une expérience cohérente, elle présente des utilisations limitées et ne tire pas pleinement parti de la plateforme sous-jacente. Cela se traduit par une solution inefficace et coûteuse pour les clients finaux. En réduisant Azure à un « simple point de terminaison de stockage », les solutions de sauvegarde ne peuvent pas tirer parti de la richesse et la puissance de la plateforme cloud publique. En revanche, Azure Backup propose un véritable service qui utilise le cloud pour offrir une solution de sauvegarde puissante et abordable. Il s’intègre à votre solution de sauvegarde locale (SCDPM) pour fournir une solution hybride de bout en bout.

Les avantages de cette approche sont les suivants :

- Architecture de stockage cloud efficace qui fournit un stockage de données abordable et résilient
- Mise à l’échelle automatique non intrusive du service avec des garanties de haute disponibilité
- Sauvegarde de déploiements locaux, hybrides et IaaS de manière cohérente

Les fonctionnalités clés de cette solution sont les suivantes :

1. **Service fiable** : en adoptant Azure Backup, vous obtenez un service de sauvegarde à haut niveau de disponibilité. Le service est mutualisé et vous n’avez pas à vous soucier de la gestion du calcul ou du stockage sous-jacent.

2. **Stockage fiable** : Azure Backup repose sur un stockage cloud fiable qui est soutenu par des contrats SLA élevés. Vous n’avez pas à vous soucier des investissements ou des frais d’exploitation liés à l’entretien du stockage. En outre, vous pouvez choisir entre une sauvegarde dans un stockage LRS (stockage localement redondant) ou GRS (stockage de géo-réplication). Tandis que le stockage LRS permet 3 copies des données dans la même zone géographique, ce qui offre une protection contre les défaillances matérielles locales, le stockage GRS fournit 3 copies supplémentaires (pour un total de 6 copies) dans un centre de données associé. Cela garantit la haute disponibilité de vos données de sauvegarde. Même en cas de problème grave au niveau du site Azure, les données de sauvegarde sont en sécurité.

3. **Sécurisé** : les données de sauvegarde Azure sont chiffrées à la source, pendant la transmission, et stockées et chiffrées dans Azure. La clé de chiffrement est stockée au niveau de la source et n’est jamais transmise ou stockée dans Azure. La clé de chiffrement est nécessaire pour restaurer les données et seul le client dispose d’un accès complet aux données dans le service.

4. **Protection hors site** : au lieu d’acheter des solutions de sauvegarde sur bande hors site, les clients peuvent sauvegarder leurs données dans Azure, qui offre une solution attractive avec une sémantique de type bande à un coût très bas.

5. **Simplicité** : Azure Backup fournit une interface familière capable d’évoluer pour protéger un déploiement de n’importe quelle taille. À mesure de l’évolution du service, des fonctionnalités comme la gestion centralisée vous permettront de gérer votre infrastructure de sauvegarde depuis un emplacement unique.

6. **Rentable** : la tarification Azure Backup inclut des frais de gestion de sauvegarde par instance et un coût lié au stockage (prix par objet blob de blocs) consommé sur Azure. Contrairement aux autre offres de sauvegarde dans le cloud, Azure Backup ne facture pas ses clients pour chaque opération de restauration. En outre, les clients ne sont pas facturés pour les coûts de transfert de données sortant pendant une opération de restauration.

7. **Sauvegarde dans le cloud** : Azure Backup assure une sauvegarde VSS (Volume Shadow Copy Service) cohérente avec l’application des machines virtuelles IaaS Azure en cours d’exécution sans nécessiter l’arrêt de ces machines. Ce service peut également sauvegarder des machines virtuelles Linux dans Azure en assurant la cohérence avec le système de fichiers.


## Scénarios de déploiement
| Composant | Déploiement possible dans Azure ? | Déploiement possible localement ? | Stockage cible pris en charge|
| --- | --- | --- | --- |
| Agent Azure Backup | **Oui** <br><br>L’agent Azure Backup peut être déployé sur n’importe quelle machine virtuelle Windows Server dans Azure. | **Oui** <br><br>L’agent Azure Backup peut être déployé sur n’importe quelle machine virtuelle ou physique Windows Server. | Archivage de sauvegarde Azure |
| System Center Data Protection Manager (SCDPM) | **Oui** <br><br>En savoir plus sur la [protection des charges de travail dans Azure dans SCDPM](http://blogs.technet.com/b/dpm/archive/2014/09/02/azure-iaas-workload-protection-using-data-protection-manager.aspx). | **Oui** <br><br>En savoir plus sur la [protection des charges de travail et des machines virtuelles dans votre centre de données](https://technet.microsoft.com/fr-FR/library/hh758173.aspx). | Disque connecté localement,<br>archivage de sauvegarde Azure,<br>bande (sur site uniquement) |
| Azure Backup (extension de machine virtuelle) | **Oui** <br><br>Spécialisé dans la [sauvegarde de machines virtuelles Azure IaaS](backup-azure-vms-introduction.md). | **Non** <br><br>Utilisez SCDPM pour sauvegarder les machines virtuelles dans votre centre de données. | Archivage de sauvegarde Azure |


## Applications et charges de travail

| Charge de travail | Machine source | Solution Azure Backup |
| --- | --- |---|
| Fichiers et dossiers | Windows Server | [Agent Azure Backup](backup-configure-vault.md),<br> [System Center DPM](backup-azure-dpm-introduction.md) |
| Fichiers et dossiers | Client Windows | [Agent Azure Backup](backup-configure-vault.md),<br> [System Center DPM](backup-azure-dpm-introduction.md) |
| Machine virtuelle Hyper-V (Windows) | Windows Server | System Center DPM |
| Machine virtuelle Hyper-V (Linux) | Windows Server | System Center DPM |
| Microsoft SQL Server | Windows Server | [System Center DPM](backup-azure-backup-sql.md) |
| Microsoft SharePoint | Windows Server | [System Center DPM](backup-azure-backup-sharepoint.md) |
| Microsoft Exchange | Windows Server | System Center DPM |
| Azure IaaS VMs (Windows)| - | [Azure Backup (extension de machine virtuelle)](backup-azure-vms-introduction.md) |
| Azure IaaS VMs (Linux) | - | [Azure Backup (extension de machine virtuelle)](backup-azure-vms-introduction.md) |


## Étapes suivantes
- [Test d’Azure Backup](backup-try-azure-backup-in-10-mins.md)
- Le Forum Aux Questions sur le service Azure Backup se trouve [ici](backup-azure-backup-faq.md).
- Consultez le [forum Azure Backup](http://go.microsoft.com/fwlink/p/?LinkId=290933).

<!----HONumber=Oct15_HO3-->