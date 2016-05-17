<properties
	pageTitle="Qu’est-ce qu’Azure Backup ? | Microsoft Azure"
	description="Grâce à Azure Backup et à Recovery Services, vous pouvez sauvegarder et restaurer des données et des applications à partir de serveurs Windows, d’ordinateurs clients Windows, de serveurs System Center DPM ou de machines virtuelles Azure."
	services="backup"
	documentationCenter=""
	authors="Jim-Parker"
	manager="jwhit"
	editor="tysonn"
	keywords="sauvegarde et restauration ; services de restauration ; solutions de sauvegarde"/>

<tags
	ms.service="backup"
	ms.workload="storage-backup-recovery"
	ms.tgt_pltfrm="na"
	ms.devlang="na"
	ms.topic="get-started-article"
	ms.date="05/10/2016"
	ms.author="jimpark; trinadhk"/>

# Qu’est-ce qu’Azure Backup ?
Azure Backup est un service qui vous permet de sauvegarder et restaurer vos données dans le cloud Microsoft. Il remplace votre solution de sauvegarde locale ou hors site par une solution basée dans le cloud à la fois fiable, sécurisée et économique. Ce service vous permet également de protéger les ressources exécutées dans le cloud. Azure Backup fournit des services de récupération reposant sur une infrastructure de classe mondiale extensible, durable et hautement disponible.

[Regarder une vidéo de présentation d’Azure Backup](https://azure.microsoft.com/documentation/videos/what-is-azure-backup/)

## Pourquoi utiliser Azure Backup ?
Les solutions de sauvegarde traditionnelles ont évolué et considèrent désormais le cloud comme un point de terminaison similaire aux disques ou bandes. Bien que simple, cette approche présente des limites. Elle ne tire pas pleinement parti de la plate-forme cloud sous-jacente et se transforme en solution inefficace et onéreuse. Azure Backup, en revanche, offre tous les avantages d’une solution de sauvegarde cloud performante et abordable. Voici quelques-uns des principaux avantages offerts par Azure Backup :

| Fonctionnalité | Avantage |
| ------- | ------- |
| Gestion automatique du stockage | Aucune dépense d’investissement n’est nécessaire pour l’achat de périphériques de stockage en local. Azure Backup alloue et gère automatiquement le stockage de sauvegarde sur la base d’un modèle de paiement à l’utilisation. |
| Mise à l’échelle illimitée | Bénéficiez de garanties de haute disponibilité sans frais de gestion et de surveillance. Azure Backup utilise la puissance sous-jacente et la mise à l’échelle du cloud Azure, offrant des capacités d’évolution automatique non intrusives. |
| Diverses options de stockage | Choisissez le stockage de sauvegarde adapté à vos besoins : <li>un objet blob de blocs LRS (stockage localement redondant), idéal pour les clients qui cherchent à réduire leurs coûts tout en protégeant leurs données contre les défaillances matérielles locales ; <li>un objet blob de blocs de stockage par géo-réplication, qui fournit trois copies de plus dans un centre de données associé afin de garantir la haute disponibilité de vos données de sauvegarde, même en cas de sinistre sur le site Azure. |
| Transfert de données illimité | Aucuns frais ne sont facturés pour le transfert de données sortant pendant une opération de restauration à partir du coffre Azure Backup. Les données entrantes transférées vers Azure sont également gratuites. Fonctionne avec le service d’importation dans lequel il est disponible. |
| Chiffrement des données | Le chiffrement des données garantit une transmission et un stockage sécurisés des données client dans le cloud public. La phrase secrète de chiffrement est stockée au niveau de la source et n’est jamais transmise ou stockée dans Azure. La clé de chiffrement est nécessaire pour restaurer les données et seul le client dispose d’un accès complet aux données du service. |  
| Sauvegarde cohérente avec les applications | L’exécution de sauvegardes cohérentes au niveau applicatif sur Windows évite d’avoir à appliquer des correctifs au moment de la restauration, de manière à réduire l’objectif de temps de récupération. Les clients sont ainsi en mesure de rétablir plus rapidement le fonctionnement de leurs opérations. |
| Rétention à long terme | Au lieu d’acheter des solutions de sauvegarde sur bande hors site, les clients peuvent sauvegarder leurs données dans Azure, ce qui offre une solution sémantique de type bande à bas coût. |

## Composants d’Azure Backup
Azure Backup est une solution de sauvegarde hybride qui intègre donc plusieurs composants agissant de concert pour garantir une sauvegarde et une restauration de bout en bout des flux de travail.

![Composants d’Azure Backup](./media/backup-introduction-to-azure-backup/azure-backup-overview.png)

### Scénarios de déploiement

| Composant | Déploiement possible dans Azure ? | Déploiement possible localement ? | Stockage cible pris en charge|
| --- | --- | --- | --- |
| Agent Azure Backup | <p>**Oui**</p> <p>L’agent Azure Backup peut être déployé sur n’importe quelle machine virtuelle Windows Server exécutée dans Azure.</p> | <p>**Oui**</p> <p>L’agent Azure Backup peut être déployé sur n’importe quelle machine virtuelle ou physique Windows Server.</p> | <p>Coffre de sauvegarde Azure</p> |
| System Center Data Protection Manager (DPM) | <p>**Oui**</p><p>En savoir plus sur la [protection des charges de travail dans Azure à l’aide de System Center DPM](http://blogs.technet.com/b/dpm/archive/2014/09/02/azure-iaas-workload-protection-using-data-protection-manager.aspx).</p> | <p>**Oui**</p> <p>En savoir plus sur la [protection des charges de travail et des machines virtuelles dans votre centre de données](https://technet.microsoft.com/library/hh758173.aspx).</p> | <p>Disque connecté localement,</p> <p>coffre Azure Backup,</p> <p>bande (en local uniquement)</p> |
| Azure Backup Server | <p>**Oui**</p> <p>En savoir plus sur la [protection des charges de travail dans Azure à l’aide du serveur Azure Backup](backup-azure-microsoft-azure-backup.md).</p> | <p>**Oui**</p> <p>En savoir plus sur la [protection des charges de travail dans Azure à l’aide du serveur Azure Backup](backup-azure-microsoft-azure-backup.md).</p> | <p>Disque connecté localement,</p> <p>archivage Azure Backup,</p> |
| Azure Backup (extension de machine virtuelle) | <p>**Oui**</p> <p>Partie d’Azure Fabric</p><p>Spécialisé dans la [sauvegarde des machines virtuelles Azure IaaS (infrastructure en tant que service)](backup-azure-vms-introduction.md).</p> | <p>**Non**</p> <p>Utilisez System Center DPM pour sauvegarder des machines virtuelles dans votre centre de données.</p> | <p>Coffre Azure Backup</p> |

### Avantages et limites du niveau du composant

| Composant | Avantages | Limites | Granularité de récupération |
| --- | --- | --- | --- |
| Agent Azure Backup (MARS) | <li>Peut sauvegarder des fichiers et des dossiers sur une machine avec le système d’exploitation Windows, physique ou virtuelle (les machines virtuelles peuvent se trouver n’importe où, sur site ou sur Azure)<li>Aucun serveur de sauvegarde distinct n’est requis<li>Utilise Azure Backup Vault | <li>Sauvegarde trois fois par jour/restauration au niveau fichier<li>Restauration de niveau fichier/dossier/volume uniquement, indépendante de l’application<li>Aucune prise en charge de Linux | fichiers/dossiers/volumes |
| System Center Data Protection Manager (DPM) | <li>Instantanés en fonction de l’application (VSS)<li>Flexibilité complète pour effectuer les sauvegardes<li>Granularité de récupération (tout)<li>Peut utiliser le coffre Azure Backup<li>Prise en charge de Linux (s’il est hébergé sur Hyper-V) | <li>Absence de prise en charge hétérogène (sauvegarde de machine virtuelle VMware, sauvegarde de la charge de travail Oracle). | fichiers/dossiers/volumes<br>/machines virtuelles/applications |
| Microsoft Azure Backup Server | <li>Instantanés en fonction de l’application (VSS)<li>Flexibilité complète pour effectuer les sauvegardes<li>Granularité de récupération (tout)<li>Peut utiliser le coffre Azure Backup<li>Prise en charge de Linux (s’il est hébergé sur Hyper-V)<li>Ne nécessite pas de licence System Center | <li>Absence de prise en charge hétérogène (sauvegarde de machine virtuelle VMware, sauvegarde de la charge de travail Oracle).<li>Requiert toujours un abonnement Azure en direct<li>Aucune prise en charge de la sauvegarde sur bande | fichiers/dossiers/volumes<br>/machines virtuelles/applications |
| Sauvegarde des machines virtuelles IaaS Azure | <li>Sauvegardes natives pour Windows/Linux<li>Aucune installation d’agent spécifique requise<li>Sauvegarde au niveau Fabric sans infrastructure de sauvegarde nécessaire | <li>Restauration une fois par jour/restauration au niveau du disque<li>Impossible d’effectuer une sauvegarde en local | Machines virtuelles<br>Tous les disques (avec PowerShell) |

## Quelles applications et charges de travail est-il possible de sauvegarder ?

| Charge de travail | Machine source | Solution Azure Backup |
| --- | --- |---|
| Fichiers et dossiers | Windows Server | <p>[Agent Azure Backup](backup-configure-vault.md),</p> <p>[System Center DPM](backup-azure-dpm-introduction.md) (+ l’agent Azure Backup),</p> <p>[Serveur Azure Backup](backup-azure-microsoft-azure-backup.md) (notamment l’agent Azure Backup)</p> |
| Fichiers et dossiers | Client Windows | <p>[Agent Azure Backup](backup-configure-vault.md),</p> <p>[System Center DPM](backup-azure-dpm-introduction.md) (+ l’agent Azure Backup),</p> <p>[Serveur Azure Backup](backup-azure-microsoft-azure-backup.md) (notamment l’agent Azure Backup)</p> |
| Machine virtuelle Hyper-V (Windows) | Windows Server | <p>[System Center DPM](backup-azure-backup-sql.md) (+ l’agent Azure Backup), </p> <p>[Serveur Azure Backup](backup-azure-microsoft-azure-backup.md) (inclut l’agent Azure Backup)</p> |
| Machine virtuelle Hyper-V (Linux) | Windows Server | <p>[System Center DPM](backup-azure-backup-sql.md) (+ l’agent Azure Backup), </p> <p>[Serveur Azure Backup](backup-azure-microsoft-azure-backup.md) (inclut l’agent Azure Backup)</p> |
| Microsoft SQL Server | Windows Server | <p>[System Center DPM](backup-azure-backup-sql.md) (+ l’agent Azure Backup), </p> <p>[Serveur Azure Backup](backup-azure-microsoft-azure-backup.md) (inclut l’agent Azure Backup)</p> |
| Microsoft SharePoint | Windows Server | <p>[System Center DPM](backup-azure-backup-sql.md) (+ l’agent Azure Backup), </p> <p>[Serveur Azure Backup](backup-azure-microsoft-azure-backup.md) (inclut l’agent Azure Backup)</p> |
| Microsoft Exchange | Windows Server | <p>[System Center DPM](backup-azure-backup-sql.md) (+ l’agent Azure Backup), </p> <p>[Serveur Azure Backup](backup-azure-microsoft-azure-backup.md) (inclut l’agent Azure Backup)</p> |
| Machines virtuelles IaaS Azure (Windows)| - | [Azure Backup (extension de machine virtuelle)](backup-azure-vms-introduction.md) | | Machines virtuelles IaaS Azure (Linux) | - | [Azure Backup (extension de machine virtuelle)](backup-azure-vms-introduction.md) |

## Prise en charge ARM et Linux

| Composant | Prise en charge ARM | Prise en charge Linux (approuvée par Azure) |
| --- | --- | --- |
| Agent Azure Backup (MARS) | Oui | Aucun (agent uniquement sur Windows) |
| System Center Data Protection Manager (DPM) | Oui (Agent d’invité) | Seule une sauvegarde compatible de niveau de fichier Hyper-V (non de machine virtuelle Azure) est possible. |
| Serveur Azure Backup (MABS) | Oui (Agent d’invité) | Seule une sauvegarde compatible de niveau de fichier Hyper-V (pas de machine virtuelle Azure) est possible (identique à DPM) |
| Sauvegarde des machines virtuelles IaaS Azure | Dans la version préliminaire publique | Dans la version préliminaire publique - Machies virtuelles Linux dans le modèle de déploiement Resource Manager <br>(Cohérence au niveau du système de fichiers)<br><br>Oui pour les machines virtuelles Linux dans le modèle de déploiement classique |

[AZURE.INCLUDE [learn-about-deployment-models](../../includes/learn-about-deployment-models-include.md)]


## Sauvegarder et restaurer des machines virtuelles Premium Storage

Le service Azure Backup protège maintenant les machines virtuelles Premium Storage.

### Sauvegarder des machines virtuelles Premium Storage

Lors de la sauvegarde de machines virtuelles Premium Storage, le service Backup crée un emplacement temporaire intermédiaire dans le compte Premium Storage. L'emplacement intermédiaire, nommé « AzureBackup- », est égal à la taille totale des données des disques Premium attachés à la machine virtuelle.

>[AZURE.NOTE] Évitez de modifier l'emplacement intermédiaire.

Une fois la sauvegarde terminée, l'emplacement intermédiaire est supprimé. Le prix du stockage utilisé pour l'emplacement intermédiaire est conforme à l’ensemble de la [tarification Premium Storage](../storage/storage-premium-storage.md#pricing-and-billing).

### Restaurer des machines virtuelles Premium Storage

La restauration d'un point de récupération de machines virtuelles Premium Storage dans Premium Storage est le processus de restauration classique. Toutefois, il peut être rentable de restaurer un point de récupération de machines virtuelles Premium Storage dans le stockage standard. Ce type de restauration peut être utilisé si vous avez besoin d'un sous-ensemble de fichiers de la machine virtuelle.

## Fonctionnalités
Ces cinq tableaux récapitulent la manière dont les fonctionnalités de sauvegarde sont gérées au niveau de chaque composant :

### Storage

| Fonctionnalité | Agent Azure Backup | System Center DPM | Azure Backup Server | Azure Backup (extension de machine virtuelle) |
| ------- | --- | --- | --- | ---- |
| Archivage de sauvegarde Azure | ![Oui][green] | ![Oui][green] | ![Oui][green] | ![Oui][green] |
| Stockage sur disque | | ![Oui][green] | ![Oui][green] | |
| Stockage sur bande | | ![Oui][green] | | |
| Compression (dans le coffre de sauvegarde) | ![Oui][green] | ![Oui][green]| ![Oui][green] | |
| Sauvegarde incrémentielle | ![Oui][green] | ![Oui][green] | ![Oui][green] | ![Oui][green] |
| Déduplication de disque | | ![Partiellement][yellow] | ![Partiellement][yellow]| | |

![clé de table](./media/backup-introduction-to-azure-backup/table-key.png)

Le coffre Azure Backup est la cible de stockage par défaut sur tous les composants. System Center DPM et le serveur de sauvegarde offrent également la possibilité d’avoir une copie du disque local. Toutefois, seul System Center DPM permet d’écrire des données sur un périphérique de stockage sur bande.

#### Sauvegarde incrémentielle
Chaque composant prend en charge la sauvegarde incrémentielle quel que soit le stockage cible (disque, bande, coffre de sauvegarde). La sauvegarde incrémentielle garantit un stockage efficace des sauvegardes, en transférant uniquement les modifications apportées depuis la dernière sauvegarde.

#### Compression
En outre, les sauvegardes sont compressées afin de réduire la quantité d’espace de stockage requise. L’extension de machine virtuelle est le seul composant qui n’effectue aucune compression. Avec l’extension de machine virtuelle, toutes les données de sauvegarde sont copiées du compte de stockage client vers le coffre de sauvegarde dans la même région sans être compressées. Bien que cela augmente légèrement le volume de stockage utilisé, le fait de stocker des données sans compression permet d’accélérer les restaurations.

#### Déduplication
La déduplication est prise en charge pour System Center DPM et le serveur Backup lorsqu’ils sont [déployés dans une machine virtuelle Hyper-V](http://blogs.technet.com/b/dpm/archive/2015/01/06/deduplication-of-dpm-storage-reduce-dpm-storage-consumption.aspx). La déduplication intervient au niveau de l’hôte grâce à la fonction de déduplication de Windows Server sur les disques durs virtuels attachés en tant que stockage de sauvegarde à la machine virtuelle.

>[AZURE.WARNING] La déduplication n’est en revanche pas disponible dans Azure pour aucun des composants d’Azure Backup. Lorsque System Center DPM et Azure Backup Server sont déployés dans Azure, les disques de stockage attachés à la machine virtuelle ne peuvent pas être dédupliqués.

### Sécurité

| Fonctionnalité | Agent Azure Backup | System Center DPM | Azure Backup Server | Azure Backup (extension de machine virtuelle) |
| ------- | --- | --- | --- | ---- |
| Sécurité du réseau (vers Azure) | ![Oui][green] |![Oui][green] | ![Oui][green] | ![Partiellement][yellow]|
| Sécurité des données (dans Azure) | ![Oui][green] |![Oui][green] | ![Oui][green] | ![Partiellement][yellow]|

![clé de table](./media/backup-introduction-to-azure-backup/table-key.png)

L’ensemble du trafic de sauvegarde entre vos serveurs et le coffre Azure Backup est chiffré à l’aide du protocole AES (Advanced Encryption Standard) 256. Les données sont envoyées via une connexion HTTPS sécurisée. Les données de sauvegarde sont également stockées dans le coffre Azure Backup sous une forme chiffrée. Seul le client conserve le mot de passe pour déverrouiller ces données. Microsoft ne peut déchiffrer les données de sauvegarde à aucun moment.

>[AZURE.WARNING] La clé utilisée pour chiffrer les données de sauvegarde est disponible uniquement chez le client. Microsoft ne conserve pas de copie dans Azure et n’a pas accès à la clé. Si la clé est égarée, Microsoft ne peut pas récupérer les données de sauvegarde.

La sauvegarde des machines virtuelles Azure exige la configuration du chiffrement *dans* la machine virtuelle. Utilisez BitLocker sur les machines virtuelles Windows et **dm-crypt** sur les machines virtuelles Linux. Azure Backup ne chiffre pas automatiquement les données de sauvegarde en provenance de ce chemin d’accès.

### Charges de travail prises en charge

| Fonctionnalité | Agent Azure Backup | System Center DPM | Azure Backup Server | Azure Backup (extension de machine virtuelle) |
| ------- | --- | --- | --- | ---- |
| Ordinateur Windows Server ; fichiers et dossiers | ![Oui][green] | ![Oui][green] | ![Oui][green] | |
| Ordinateur client Windows ; fichiers et dossiers | ![Oui][green] | ![Oui][green] | ![Oui][green] | |
| Machine virtuelle Hyper-V (Windows) | | ![Oui][green] | ![Oui][green] | |
| Machine virtuelle Hyper-V (Linux) | | ![Oui][green] | ![Oui][green] | |
| Microsoft SQL Server | | ![Oui][green] | ![Oui][green] | |
| Microsoft SharePoint | | ![Oui][green] | ![Oui][green] | |
| Microsoft Exchange | | ![Oui][green] | ![Oui][green] | |
| Machine virtuelle Azure (Windows) | | | | ![Oui][green] |
| Machine virtuelle Azure (Linux) | | | | ![Oui][green] |

![clé de table](./media/backup-introduction-to-azure-backup/table-key-2.png)

### Réseau

| Fonctionnalité | Agent Azure Backup | System Center DPM | Azure Backup Server | Azure Backup (extension de machine virtuelle) |
| ------- | --- | --- | --- | ---- |
| Compression réseau (sur le serveur de sauvegarde) | | ![Oui][green] | ![Oui][green] | |
| Compression réseau (sur le coffre de sauvegarde) | ![Oui][green] | ![Oui][green] | ![Oui][green] | |
| Protocole réseau (sur le serveur de sauvegarde) | | TCP | TCP | |
| Protocole réseau (sur le coffre de sauvegarde) | HTTPS | HTTPS | HTTPS | HTTPS |

![clé de table](./media/backup-introduction-to-azure-backup/table-key-2.png)

Étant donné que l’extension de machine virtuelle lit directement les données à partir du compte Azure Storage via le réseau de stockage, il n’est pas nécessaire d’optimiser ce trafic. Comme le trafic transite sur le réseau de stockage local dans le centre de données Azure, la question de la compression liée aux problèmes de bande passante ne se pose pas véritablement.

Si vous protégez vos données sur un serveur de sauvegarde (serveur DPM ou Azure Backup), le trafic généré entre le serveur principal et le serveur de sauvegarde peut également être compressé afin d’économiser de la bande passante.

#### Limitation du réseau
L’agent Azure Backup fournit la fonctionnalité de limitation qui vous permet de contrôler l’utilisation de la bande passante réseau pendant le transfert de données. Cette limitation peut s’avérer utile si vous avez besoin de sauvegarder des données pendant les heures de travail, mais ne souhaitez pas que le processus de sauvegarde interfère avec le reste du trafic internet. La limitation du transfert de données s’applique aux activités de sauvegarde et de restauration.

### Sauvegarde et rétention

| | Agent Azure Backup | System Center DPM | Azure Backup Server | Azure Backup (extension de machine virtuelle) |
| --- | --- | --- | --- | --- |
| Fréquence de sauvegarde (sur le coffre de sauvegarde) | Trois sauvegardes par jour | Deux sauvegardes par jour |Deux sauvegardes par jour | Une sauvegarde par jour |
| Fréquence de sauvegarde (sur disque) | Non applicable | <p>Toutes les 15 minutes pour SQL Server</p> <p>Toutes les heures pour les autres charges de travail</p> | <p>Toutes les 15 minutes pour SQL Server</p> <p>Toutes les heures pour les autres charges de travail</p> |Non applicable |
| Options de rétention | Quotidienne, hebdomadaire, mensuelle, annuelle | Quotidienne, hebdomadaire, mensuelle, annuelle | Quotidienne, hebdomadaire, mensuelle, annuelle |Quotidienne, hebdomadaire, mensuelle, annuelle |
| Période de rétention | Jusqu’à 99 ans | Jusqu’à 99 ans | Jusqu’à 99 ans | Jusqu’à 99 ans |
| Points de récupération dans le coffre Azure Backup | Illimité | Illimité | Illimité | Illimité |
| Points de récupération sur le disque local | Non applicable | 64 pour les serveurs de fichiers,<br><br>448 pour les serveurs d’application | 64 pour les serveurs de fichiers,<br><br>448 pour les serveurs d’application |Non applicable |
| Points de récupération sur bande | Non applicable | Illimité | Non applicable | Non applicable |

## Qu’est-ce que le fichier d’informations d’identification de coffre ?

Le fichier d’informations d’identification de coffre est un certificat qui est généré par le portail pour chaque archivage de sauvegarde. Le portail télécharge ensuite la clé publique pour le Service de contrôle d’accès (ACS). La clé privée est fournie à l’utilisateur lors du téléchargement des informations d’identification, puis entrée lors de l’enregistrement de l’ordinateur. La clé privée authentifie l’ordinateur pour envoyer des données de sauvegarde dans un archivage identifié dans le service Azure Backup.

Les informations d’identification de coffre sont utilisées uniquement pendant le flux de travail d’inscription. Il est de votre responsabilité de vous assurer que le fichier d’informations d’identification de coffre n’est pas compromis. S’il tombe entre les mains d’un utilisateur non autorisé, le fichier d’informations d’identification de coffre peut servir à inscrire d’autres ordinateurs pour le même archivage. Toutefois, comme les données de sauvegarde sont chiffrées à l’aide d’une phrase secrète appartenant au client, les données de sauvegarde existantes ne peuvent pas être compromises. Pour atténuer ce problème, les informations d’identification de coffre sont configurées pour expirer sous 48 heures. Vous pouvez télécharger les informations d’identification d’un archivage de sauvegarde autant de fois que nécessaire, seul le dernier fichier est applicable pendant le flux de travail d’inscription.

## Quelle est la différence entre Azure Backup et Azure Site Recovery ?
De nombreux clients ont tendance à confondre récupération de sauvegarde et récupération d’urgence. Les deux opérations capturent des données et fournissent une sémantique de restauration, mais chacune est associée à une proposition de valeur bien spécifique.

Azure Backup sauvegarde les données en local et dans le cloud. Azure Site Recovery coordonne la réplication, le basculement et la restauration automatique des machines virtuelles et des serveurs physiques. Les deux services sont importants, car votre solution de récupération d’urgence doit copier vos données en toute sécurité et les rendre récupérables (Backup) *et* assurer la disponibilité de vos charges de travail (Site Recovery) en cas de panne.

Les concepts qui suivent vous aideront à prendre des décisions importantes en matière de sauvegarde et de récupération.

| Concept | Détails | Sauvegarde | Récupération d’urgence |
| ------- | ------- | ------ | ----------------- |
| Objectif de point de récupération (RPO) | Quantité de perte de données acceptable si la récupération doit être exécutée. | Les solutions de sauvegarde offrent des RPO extrêmement variables. Les sauvegardes de machines virtuelles ont généralement un RPO d’un jour, contre seulement 15 minutes pour les sauvegardes de base de données. | Les solutions de récupération d’urgence ont un RPO faible. La copie de récupération d’urgence peut devoir être prête en seulement quelques secondes ou quelques minutes. |
| Objectif de délai de récupération (RTO) | Quantité de temps nécessaire pour effectuer une récupération ou une restauration complète. | Un RPO plus long est généralement synonyme pour la solution de sauvegarde d’une bien plus grande quantité de données à traiter, ce qui rallonge d’autant le RTO. Par exemple, il peut falloir plusieurs jours pour restaurer des données à partir de bandes, selon le temps nécessaire au transport de la bande depuis un site externe. | Les solutions de récupération d’urgence ont un RTO plus faible car elles sont davantage synchronisées avec la source et ont moins de modifications à traiter. |
| Rétention | Durée pendant laquelle les données doivent être stockées | Pour les scénarios qui exigent une reprise des opérations (altération des données, suppression accidentelle de fichiers, défaillances du système d’exploitation), les données de sauvegarde sont généralement conservées pendant 30 jours au maximum.<br>Du point de vue de la conformité, il se peut que vous deviez stocker les données pendant des mois, voire des années. Dans ce cas, les données de sauvegarde sont parfaitement adaptées aux besoins d’archivage. | Une récupération d’urgence porte uniquement sur les données de récupération opérationnelle, soit en général quelques heures, sans dépasser une journée. Puisque les solutions de récupération d’urgence sont conçues pour capturer les données à un niveau extrêmement précis, l’utilisation des données de récupération d’urgence n’est pas recommandée dans le cadre d’une rétention à long terme. |

## Étapes suivantes

Essayez une simple sauvegarde Azure. Pour plus d’informations, consultez l’un des didacticiels suivants :

- [Test d’Azure Backup](backup-try-azure-backup-in-10-mins.md)
- [Test de la machine virtuelle Azure Backup](backup-azure-vms-first-look.md)

Comme ces didacticiels vous aident à effectuer des sauvegardes rapides, ils n’affichent que le chemin d’accès le plus direct pour sauvegarder vos données. Pour plus d’informations sur le type de sauvegarde que vous souhaitez faire, consultez :

- [Sauvegarder un ordinateur Windows](backup-configure-vault.md)
- [Sauvegarder les charges de travail des applications](backup-azure-microsoft-azure-backup.md)
- [Sauvegarde des machines virtuelles IaaS Azure](backup-azure-vms-prepare.md)



[green]: ./media/backup-introduction-to-azure-backup/green.png
[yellow]: ./media/backup-introduction-to-azure-backup/yellow.png
[red]: ./media/backup-introduction-to-azure-backup/red.png

<!---HONumber=AcomDC_0511_2016-->