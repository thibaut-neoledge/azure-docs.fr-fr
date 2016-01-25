<properties
	pageTitle="Qu’est-ce qu’Azure Backup ? | Microsoft Azure"
	description="Avec Azure Backup et les services de récupération, vous pouvez sauvegarder et restaurer des données et des applications à partir de serveurs Windows, d’ordinateurs clients Windows, de serveurs SCDPM ou de machines virtuelles Azure."
	services="backup"
	documentationCenter=""
	authors="Jim-Parker"
	manager="jwhit"
	editor="tysonn"
	keywords="sauvegarde et restauration ; services de restauration"/>

<tags
	ms.service="backup"
	ms.workload="storage-backup-recovery"
	ms.tgt_pltfrm="na"
	ms.devlang="na"
	ms.topic="article"
	ms.date="01/08/2016"
	ms.author="trinadhk;jimpark"/>

# Qu’est-ce qu’Azure Backup ?
Azure Backup est le service qui vous permet de sauvegarder et restaurer vos données dans le cloud Microsoft. Il remplace votre solution de sauvegarde locale ou hors site par une solution basée dans le cloud à la fois fiable, sécurisée et économique. Il protège également les ressources qui s’exécutent dans le cloud. Azure Backup fournit des services de récupération reposant sur une infrastructure de classe mondiale extensible, durable et hautement disponible.

[Regarder une vidéo de présentation d’Azure Backup](https://azure.microsoft.com/documentation/videos/what-is-azure-backup/)

## Pourquoi utiliser Azure Backup ?
Les solutions de sauvegarde traditionnelles ont évolué et considèrent désormais le cloud comme un point de terminaison similaire aux disques ou bandes. Bien que simple, cette approche est limitée et ne prend pas pleinement parti d’une plateforme de cloud sous-jacente, ce qui se traduit par une solution inefficace et coûteuse. Azure Backup offre, en revanche, tous les avantages d’une solution de sauvegarde cloud puissante et abordable. Voici quelques-uns des principaux avantages offerts par Azure Backup :

| FONCTIONNALITÉ | AVANTAGE |
| ------- | ------- |
| Gestion automatique du stockage | Aucune dépense d’investissement n’est nécessaire pour l’achat de périphériques de stockage en local. Azure Backup alloue et gère automatiquement le stockage de sauvegarde sur la base d’un modèle de paiement à l’utilisation. |
| Mise à l’échelle illimitée | Garanties de haute disponibilité sans frais de gestion et de surveillance. Azure Backup utilise la puissance sous-jacente et la mise à l’échelle du cloud Azure, qui offre des capacités d’évolution automatique non intrusives. |
| Diverses options de stockage | Choisissez le stockage de sauvegarde adapté à vos besoins : l’objet bloc de blocs <li>LRS (stockage localement redondant) est idéal pour les clients qui cherchent à réduire leurs coûts tout en protégeant leurs données contre les défaillances matérielles locales. L’objet blob de blocs <li>GRS (stockage géo-redondant) fournit 3 copies supplémentaires dans un centre de données associé pour garantir la haute disponibilité de vos données de sauvegarde, même en cas d’incident au niveau du site Azure. |
| Transfert de données illimité | Aucuns frais facturés pour les coûts de transfert de données sortant pendant une opération de restauration à partir du coffre Azure Backup. Les données entrantes transférées vers Azure sont également gratuites. |
| Gestion centralisée | Simplicité et convivialité du portail Azure. À mesure de l’évolution du service, des fonctionnalités comme la gestion centralisée vous permettront de gérer votre infrastructure de sauvegarde depuis un emplacement unique. |
| Chiffrement des données | Transmission et stockage sécurisés des données client dans le cloud public. La phrase secrète de chiffrement est stockée au niveau de la source et n’est jamais transmise ou stockée dans Azure. La clé de chiffrement est nécessaire pour restaurer les données et seul le client dispose d’un accès complet aux données dans le service. |  
| Intégration de VSS | L’exécution de sauvegardes cohérentes au niveau applicatif sur Windows évite l’application de correctifs au moment de la restauration. Cela permet de réduire le RTO et de rétablir plus rapidement l’environnement du client. |
| Rétention à long terme | Au lieu d’acheter des solutions de sauvegarde sur bande hors site, les clients peuvent sauvegarder leurs données dans Azure, qui offre une solution attractive avec une sémantique de type bande à un coût très bas. |

## Composants d’Azure Backup
Azure Backup est une solution de sauvegarde hybride qui intègre donc plusieurs composants agissant de concert pour garantir une sauvegarde et une restauration de bout en bout des flux de travail.

![Composants d’Azure Backup](./media/backup-introduction-to-azure-backup/azure-backup-overview.png)

## Scénarios de déploiement

| Composant | Déploiement possible dans Azure ? | Déploiement possible localement ? | Stockage cible pris en charge|
| --- | --- | --- | --- |
| Agent Azure Backup | <p>**Oui**</p> <p>L’agent Azure Backup peut être déployé sur n’importe quelle machine virtuelle Windows Server exécutée dans Azure.</p> | <p>**Oui**</p> <p>L’agent Azure Backup peut être déployé sur n’importe quelle machine virtuelle ou physique Windows Server.</p> | Archivage de sauvegarde Azure |
| System Center Data Protection Manager (SCDPM) | <p>**Oui**</p> <p>En savoir plus sur la [protection des charges de travail dans Azure à l’aide de SCDPM](http://blogs.technet.com/b/dpm/archive/2014/09/02/azure-iaas-workload-protection-using-data-protection-manager.aspx).</p> | <p>**Oui**</p> <p>En savoir plus sur la [protection des charges de travail et des machines virtuelles dans votre centre de données](https://technet.microsoft.com/library/hh758173.aspx). | Disque connecté localement,</p> <p>coffre Azure Backup,</p> <p>bande (sur site uniquement)</p> |
| Azure Backup Server | <p>**Oui**</p> <p>En savoir plus sur la [protection des charges de travail dans Azure à l’aide d’Azure Backup Server](backup-azure-microsoft-azure-backup.md).</p> | <p>**Oui**</p> <p>En savoir plus sur la [protection des charges de travail dans Azure à l’aide d’Azure Backup Server](backup-azure-microsoft-azure-backup.md).</p> | Archivage de sauvegarde Azure |
| Azure Backup (extension de machine virtuelle) | <p>Oui</p> <p>Spécialisé dans la [sauvegarde de machines virtuelles Azure IaaS](backup-azure-vms-introduction.md).</p> | <p>**Non**</p> <p>Utilisez SCDPM pour sauvegarder des machines virtuelles dans votre centre de données. | Coffre Azure Backup</p> |

## Applications et charges de travail

| Charge de travail | Machine source | Solution Azure Backup |
| --- | --- |---|
| Fichiers et dossiers | Windows Server | <p>[Agent Azure Backup](backup-configure-vault.md),</p> <p>[System Center DPM](backup-azure-dpm-introduction.md),</p> <p>[Azure Backup Server](backup-azure-microsoft-azure-backup.md)</p> |
| Fichiers et dossiers | Client Windows | <p>[Agent Azure Backup](backup-configure-vault.md),</p> <p>[System Center DPM](backup-azure-dpm-introduction.md),</p> <p>[Azure Backup Server](backup-azure-microsoft-azure-backup.md)</p> |
| Machine virtuelle Hyper-V (Windows) | Windows Server | <p>System Center DPM,</p> <p>[Azure Backup Server](backup-azure-microsoft-azure-backup.md)</p> |
| Machine virtuelle Hyper-V (Linux) | Windows Server | <p>System Center DPM,</p> <p>[Azure Backup Server](backup-azure-microsoft-azure-backup.md)</p> |
| Microsoft SQL Server | Windows Server | <p>[System Center DPM](backup-azure-backup-sql.md),</p> <p>[Azure Backup Server](backup-azure-microsoft-azure-backup.md)</p> |
| Microsoft SharePoint | Windows Server | <p>[System Center DPM](backup-azure-backup-sql.md),</p> <p>[Azure Backup Server](backup-azure-microsoft-azure-backup.md)</p> |
| Microsoft Exchange | Windows Server | <p>[System Center DPM](backup-azure-backup-sql.md),</p> <p>[Azure Backup Server](backup-azure-microsoft-azure-backup.md)</p> |
| Azure IaaS VMs (Windows)| - | [Azure Backup (extension de machine virtuelle)](backup-azure-vms-introduction.md) | | Azure IaaS VMs (Linux) | - | [Azure Backup (extension de machine virtuelle)](backup-azure-vms-introduction.md) |

## Fonctionnalités
Ces tableaux récapitulent la manière dont les fonctionnalités Azure Backup sont gérées dans chaque composant :

### Storage

| Fonctionnalité | Agent Azure Backup | SCDPM | Azure Backup Server | Azure Backup (extension de machine virtuelle) |
| ------- | --- | --- | --- | ---- |
| Archivage de sauvegarde Azure | ![Oui][green] | ![Oui][green] | ![Oui][green] | ![Oui][green] |
| Stockage sur disque | | ![Oui][green] | ![Oui][green] | |
| Stockage sur bande | | ![Oui][green] | | |
| Compression (dans le coffre de sauvegarde) | ![Oui][green] | ![Oui][green]| ![Oui][green] | |
| Sauvegarde incrémentielle | ![Oui][green] | ![Oui][green] | ![Oui][green] | ![Oui][green] |
| Déduplication de disque | | ![Partiellement][yellow] | ![Partiellement][yellow]| | |

Le coffre Azure Backup est la cible de stockage par défaut sur tous les composants. SCDPM et Azure Backup Server permettent également de disposer d’une copie du disque local, mais seul SCDPM prend en charge l’écriture de données sur un périphérique de stockage à bande.

#### Sauvegarde incrémentielle
Quel que soit le stockage cible (disque, bande, coffre de sauvegarde), chaque composant prend en charge les sauvegardes incrémentielles. Les sauvegardes sont ainsi efficaces en termes de stockage et de temps puisqu’elles se concentrent uniquement sur les modifications incrémentielles apportées depuis la dernière sauvegarde pour les transférer vers le stockage cible. En outre, les sauvegardes sont compressées afin de réduire l’encombrement de stockage.

>[AZURE.NOTE]L’extension de machine virtuelle est le seul composant qui n’effectue aucune compression. Toutes les données de sauvegarde sont copiées du compte de stockage client vers le coffre de sauvegarde dans la même région, sans compression. Bien que cela augmente légèrement le stockage utilisé, le fait de stocker des données sans compression garantir des restaurations plus rapides.

#### Déduplication
La déduplication est prise en charge par SCDPM et Azure Backup Server lorsqu’ils sont [déployés au sein d’une machine virtuelle Hyper-V](http://blogs.technet.com/b/dpm/archive/2015/01/06/deduplication-of-dpm-storage-reduce-dpm-storage-consumption.aspx). La déduplication intervient au niveau de l’hôte grâce à la fonction de déduplication de Windows Server (sur les disques durs virtuels attachés en tant que stockage de sauvegarde à la machine virtuelle).

>[AZURE.WARNING]La déduplication n’est en revanche pas disponible dans Azure pour tous les composants d’Azure Backup. Lorsque SCDPM et Azure Backup Server sont déployés dans Azure, les disques de stockage attachés à la machine virtuelle ne peuvent pas être dédupliqués.

### Sécurité

| Fonctionnalité | Agent Azure Backup | SCDPM | Azure Backup Server | Azure Backup (extension de machine virtuelle) |
| ------- | --- | --- | --- | ---- |
| Sécurité du réseau (vers Azure) | ![Oui][green] |![Oui][green] | ![Oui][green] | ![Partiellement][yellow]|
| Sécurité des données (dans Azure) | ![Oui][green] |![Oui][green] | ![Oui][green] | ![Partiellement][yellow]|

L’ensemble du trafic de sauvegarde entre vos serveurs et le coffre Azure Backup est chiffré à l’aide du protocole AES256 et les données sont envoyées via une connexion HTTPS sécurisée. Les données de sauvegarde sont également stockées dans le coffre de sauvegarde sous une forme chiffrée. Seul le client détient la phrase secrète permettant de déverrouiller ces données ; Microsoft ne peut à aucun moment déchiffrer les données de sauvegarde.

>[AZURE.WARNING]La clé utilisée pour chiffrer les données de sauvegarde est disponible uniquement chez le client. Microsoft ne conserve pas de copie dans Azure et n’a pas accès à la clé. Si la clé est égarée, Microsoft ne peut pas récupérer les données de sauvegarde.

Pour la sauvegarde de machines virtuelles Azure, vous devez configurer explicitement le chiffrement *dans* la machine virtuelle. Utilisez BitLocker sur les machines virtuelles Windows et dm-crypt sur les machines virtuelles Linux. Azure Backup ne chiffre pas automatiquement les données de sauvegarde via ce chemin d’accès.

### Charges de travail prises en charge

| Fonctionnalité | Agent Azure Backup | SCDPM | Azure Backup Server | Azure Backup (extension de machine virtuelle) |
| ------- | --- | --- | --- | ---- |
| Machine Windows Server - fichiers/dossiers | ![Oui][green] | ![Oui][green] | ![Oui][green] | |
| Machine client Windows - fichiers/dossiers | ![Oui][green] | ![Oui][green] | ![Oui][green] | |
| Machine virtuelle Hyper-V (Windows) | | ![Oui][green] | ![Oui][green] | |
| Machine virtuelle Hyper-V (Linux) | | ![Oui][green] | ![Oui][green] | |
| Microsoft SQL Server | | ![Oui][green] | ![Oui][green] | |
| Microsoft SharePoint | | ![Oui][green] | ![Oui][green] | |
| Microsoft Exchange | | ![Oui][green] | ![Oui][green] | |
| Machine virtuelle Azure (Windows) | | | | ![Oui][green] |
| Machine virtuelle Azure (Linux) | | | | ![Oui][green] |

### Réseau

| Fonctionnalité | Agent Azure Backup | SCDPM | Azure Backup Server | Azure Backup (extension de machine virtuelle) |
| ------- | --- | --- | --- | ---- |
| Compression réseau (sur le serveur de sauvegarde) | | ![Oui][green] | ![Oui][green] | |
| Compression réseau (sur le coffre de sauvegarde) | ![Oui][green] | ![Oui][green] | ![Oui][green] | |
| Protocole réseau (sur le serveur de sauvegarde) | | TCP | TCP | |
| Protocole réseau (sur le coffre de sauvegarde) | HTTPS | HTTPS | HTTPS | HTTPS |

Étant donné que l’extension de machine virtuelle lit directement les données à partir du compte Azure Storage via le réseau de stockage, l’optimisation de ce trafic n’est pas nécessaire. Comme le trafic transite sur le réseau de stockage local dans le centre de données Azure, la question de la compression liée aux problèmes de bande passante ne se pose pas véritablement.

Pour les clients qui protègent des données sur un serveur de sauvegarde (SCDPM ou Azure Backup Server), le trafic généré entre le serveur principal et le serveur de sauvegarde peut également être compressé afin de réduire l’utilisation de la bande passante.

### Sauvegarde et rétention

| Fréquence de sauvegarde (sur le coffre Azure) | 3 sauvegardes par jour | 2 sauvegardes par jour | 1 sauvegarde par jour |
| --- | --- | --- | --- |
| Fréquence de sauvegarde (sur disque) | Non applicable | <p>Toutes les 15 minutes pour SQL Server</p> <p>Toutes les heures pour les autres charges de travail</p> | Non applicable |
| Options de rétention | Quotidienne, hebdomadaire, mensuelle, annuelle | Quotidienne, hebdomadaire, mensuelle, annuelle | Quotidienne, hebdomadaire, mensuelle, annuelle |
| Période de rétention | Jusqu’à 99 ans | Jusqu’à 99 ans | Jusqu’à 99 ans |
| Points de récupération dans le coffre Azure | illimitée | illimitée | illimitée |
| Points de récupération sur le disque local | Non applicable | Non applicable | Non applicable |
| Points de récupération sur bande | Non applicable | Non applicable | Non applicable |

## Quelle est la différence entre Azure Backup et Azure Site Recovery ?
De nombreux clients ont tendance à confondre sauvegarde et récupération d’urgence. Les deux opérations capturent des données et fournir une sémantique de restauration, mais chacune est associée à une proposition de valeur bien spécifique.

Azure Backup sauvegarde les données en local ou dans le cloud. Azure Site Recovery coordonne la réplication, le basculement et la restauration automatique des machines virtuelles et des serveurs physiques. Ces deux services sont indispensables pour garantir une solution de récupération d’urgence complète, car votre stratégie en la matière doit couvrir À LA FOIS la protection et la récupération de vos données (Azure Backup) et la disponibilité et l’accessibilité de vos charges de travail (Azure Site Recovery) en cas de panne.

Avant de prendre toute décision en matière de sauvegarde et de récupération d’urgence, il est important de comprendre quelques concepts :

| CONCEPT | DÉTAILS | SAUVEGARDE | RÉCUPÉRATION D’URGENCE |
| ------- | ------- | ------ | ----------------- |
| Objectif de point de récupération (RPO) | Quantité de perte de données acceptable dans l’éventualité où une restauration serait nécessaire. | Une large plage de RPO est réputée acceptable pour les solutions de sauvegarde. Les sauvegardes de machines virtuelles ont généralement un RPO de 1 jour, contre seulement 15 minutes pour les sauvegardes de base de données. | Les solutions de récupération d’urgence ont un RPO extrêmement faible. La copie de récupération d’urgence peut devoir être prête en seulement quelques secondes ou quelques minutes. |
| Objectif de délai de récupération (RTO) | Quantité de temps nécessaire pour effectuer une récupération/restauration complète. | Un RPO plus long est généralement synonyme pour la solution de sauvegarde d’une bien plus grande quantité de données à traiter, ce qui allonge d’autant le RTO. Par exemple, la restauration de données à partir de bandes peut prendre plusieurs jours, selon le temps nécessaire au transport de la bande depuis un site externe. | Les solutions de récupération d’urgence ont un RTO plus faible car elles sont davantage synchronisées avec la source et ont moins de modifications à traiter. |
| Rétention | Durée pendant laquelle les données doivent être stockées | <p>Pour les scénarios qui requièrent une reprise des opérations (corruption de données, suppression accidentelle de fichiers, défaillances du système d’exploitation), les données de sauvegarde sont généralement conservées pendant un maximum de 30 jours.</p> <p>Du point de vue de la conformité, il peut être nécessaire de conserver les données pendant des mois, voire des années. Dans ce cas, les données de sauvegarde sont parfaitement adaptées aux besoins d’archivage.</p> | Une récupération d’urgence porte uniquement sur les données de récupération opérationnelle, soit en général quelques heures sans dépasser une journée. Puisque les solutions de récupération d’urgence sont conçues pour capturer les données à un niveau extrêmement précis, les données de récupération d’urgence ne sont pas recommandées pour une rétention à long terme. |

## Étapes suivantes

- [Test d’Azure Backup](backup-try-azure-backup-in-10-mins.md)
- Le Forum Aux Questions sur le service Azure Backup se trouve [ici](backup-azure-backup-faq.md).
- Consultez le [forum Azure Backup](http://go.microsoft.com/fwlink/p/?LinkId=290933).


[green]: ./media/backup-introduction-to-azure-backup/green.png
[yellow]: ./media/backup-introduction-to-azure-backup/yellow.png
[red]: ./media/backup-introduction-to-azure-backup/red.png

<!---HONumber=AcomDC_0114_2016-->