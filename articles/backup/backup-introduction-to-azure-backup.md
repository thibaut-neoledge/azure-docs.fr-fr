<properties
	pageTitle="Qu’est-ce qu’Azure Backup ? | Microsoft Azure"
	description="Grâce aux services de sauvegarde et de récupération d’Azure, vous pouvez sauvegarder et restaurer des données et des applications à partir de serveurs Windows, d’ordinateurs clients Windows, de serveurs System Center DPM ou de machines virtuelles Azure."
	services="backup"
	documentationCenter=""
	authors="Jim-Parker"
	manager="jwhit"
	editor="tysonn"
	keywords="sauvegarde et restauration ; services de restauration ; solutions de sauvegarde"/>

<tags
	ms.service="backup"
	ms.workload="storage-backup-recovery"
	ms.tgt_pltfrm="na"
	ms.devlang="na"
	ms.topic="get-started-article"
	ms.date="03/14/2016"
	ms.author="trinadhk;jimpark"/>

# Qu’est-ce qu’Azure Backup ?
Azure Backup est un service qui vous permet de sauvegarder et restaurer vos données dans le cloud Microsoft. Il remplace votre solution de sauvegarde locale ou hors site par une solution basée dans le cloud à la fois fiable, sécurisée et économique. Ce service vous permet également de protéger les ressources exécutées dans le cloud. Azure Backup fournit des services de récupération reposant sur une infrastructure de classe mondiale extensible, durable et hautement disponible.

[Regarder une vidéo de présentation d’Azure Backup](https://azure.microsoft.com/documentation/videos/what-is-azure-backup/)

## Pourquoi utiliser Azure Backup ?
Les solutions de sauvegarde traditionnelles ont évolué et considèrent désormais le cloud comme un point de terminaison similaire aux disques ou bandes. Bien que simple, cette approche présente des limites. Elle ne tire pas pleinement parti de la plate-forme cloud sous-jacente et se transforme en solution inefficace et onéreuse. Azure Backup, en revanche, offre tous les avantages d’une solution de sauvegarde cloud performante et abordable. Voici quelques-uns des principaux avantages offerts par Azure Backup :

| Fonctionnalité | Avantage |
| ------- | ------- |
| Gestion automatique du stockage | Aucune dépense d’investissement n’est nécessaire pour l’achat de périphériques de stockage en local. Azure Backup alloue et gère automatiquement le stockage de sauvegarde sur la base d’un modèle de paiement à l’utilisation. |
| Mise à l’échelle illimitée | Bénéficiez de garanties de haute disponibilité sans frais de gestion et de surveillance. Azure Backup utilise la puissance sous-jacente et la mise à l’échelle du cloud Azure, offrant des capacités d’évolution automatique non intrusives. |
| Diverses options de stockage | Choisissez le stockage de sauvegarde adapté à vos besoins : <li>un objet blob de blocs LRS (stockage localement redondant), idéal pour les clients qui cherchent à réduire leurs coûts tout en protégeant leurs données contre les défaillances matérielles locales ; <li>un objet blob de blocs de stockage par géo-réplication, qui fournit trois copies de plus dans un centre de données associé afin de garantir la haute disponibilité de vos données de sauvegarde, même en cas de sinistre sur le site Azure. |
| Transfert de données illimité | Aucuns frais ne sont facturés pour le transfert de données sortant pendant une opération de restauration à partir du coffre Azure Backup. Les données entrantes transférées vers Azure sont également gratuites. |
| Chiffrement des données | Le chiffrement des données garantit une transmission et un stockage sécurisés des données client dans le cloud public. La phrase secrète de chiffrement est stockée au niveau de la source et n’est jamais transmise ou stockée dans Azure. La clé de chiffrement est nécessaire pour restaurer les données et seul le client dispose d’un accès complet aux données du service. |  
| Sauvegarde cohérente avec les applications | Des sauvegardes compatibles avec les applications sous Windows garantissent que les correctifs sont inutiles au moment de la restauration, ce qui réduit l’objectif de temps de récupération et permet aux clients de revenir à l’état d’exécution plus vite. |
| Rétention à long terme | Au lieu d’acheter des solutions de sauvegarde sur bande hors site, les clients peuvent sauvegarder leurs données dans Azure, ce qui offre une solution sémantique de type bande à bas coût. |

## Composants d’Azure Backup
Azure Backup est une solution de sauvegarde hybride qui intègre donc plusieurs composants agissant de concert pour garantir une sauvegarde et une restauration de bout en bout des flux de travail.

![Composants d’Azure Backup](./media/backup-introduction-to-azure-backup/azure-backup-overview.png)

## Scénarios de déploiement

| Composant | Déploiement possible dans Azure ? | Déploiement possible localement ? | Stockage cible pris en charge|
| --- | --- | --- | --- |
| Agent Azure Backup | <p>**Oui**</p> <p>L’agent Azure Backup peut être déployé sur n’importe quelle machine virtuelle Windows Server exécutée dans Azure.</p> | <p>**Oui**</p> <p>L’agent Azure Backup peut être déployé sur n’importe quelle machine virtuelle ou physique Windows Server.</p> | <p>Coffre de sauvegarde Azure</p> |
| System Center Data Protection Manager (DPM) | <p>**Oui**</p> <p>En savoir plus sur la [protection des charges de travail dans Azure à l’aide de System Center DPM](http://blogs.technet.com/b/dpm/archive/2014/09/02/azure-iaas-workload-protection-using-data-protection-manager.aspx).</p> | <p>**Oui**</p> <p>En savoir plus sur la [protection des charges de travail et des machines virtuelles dans votre centre de données](https://technet.microsoft.com/library/hh758173.aspx).</p> | <p>Disque connecté localement,</p> <p>coffre Azure Backup,</p> <p>bande (en local uniquement)</p> |
| Azure Backup Server | <p>**Oui**</p> <p>En savoir plus sur la [protection des charges de travail dans Azure à l’aide du serveur Azure Backup](backup-azure-microsoft-azure-backup.md).</p> | <p>**Oui**</p> <p>En savoir plus sur la [protection des charges de travail dans Azure à l’aide du serveur Azure Backup](backup-azure-microsoft-azure-backup.md).</p> | <p>Disque connecté localement,</p> <p>archivage Azure Backup,</p> |
| Azure Backup (extension de machine virtuelle) | <p>Oui</p> <p>Spécialisé dans la [sauvegarde des machines virtuelles Azure IaaS (infrastructure en tant que service)](backup-azure-vms-introduction.md).</p> | <p>**Non**</p> <p>Utilisez System Center DPM pour sauvegarder des machines virtuelles dans votre centre de données.</p> | <p>Coffre de sauvegarde Azure</p> |

## Quelles applications et charges de travail est-il possible de sauvegarder ?

| Charge de travail | Machine source | Solution Azure Backup |
| --- | --- |---|
| Fichiers et dossiers | Windows Server | <p>[Agent Azure Backup](backup-configure-vault.md),</p> <p>[System Center DPM](backup-azure-dpm-introduction.md),</p> <p>[Serveur Azure Backup](backup-azure-microsoft-azure-backup.md)</p> |
| Fichiers et dossiers | Client Windows | <p>[Agent Azure Backup](backup-configure-vault.md),</p> <p>[System Center DPM](backup-azure-dpm-introduction.md),</p> <p>[Serveur Azure Backup](backup-azure-microsoft-azure-backup.md)</p> |
| Machine virtuelle Hyper-V (Windows) | Windows Server | <p>[System Center DPM](backup-azure-backup-sql.md),</p> <p>[Serveur Azure Backup](backup-azure-microsoft-azure-backup.md)</p> |
| Machine virtuelle Hyper-V (Linux) | Windows Server | <p>[System Center DPM](backup-azure-backup-sql.md),</p> <p>[Serveur Azure Backup](backup-azure-microsoft-azure-backup.md)</p> |
| Microsoft SQL Server | Windows Server | <p>[System Center DPM](backup-azure-backup-sql.md),</p> <p>[Serveur Azure Backup](backup-azure-microsoft-azure-backup.md)</p> |
| Microsoft SharePoint | Windows Server | <p>[System Center DPM](backup-azure-backup-sql.md),</p> <p>[Serveur Azure Backup](backup-azure-microsoft-azure-backup.md)</p> |
| Microsoft Exchange | Windows Server | <p>[System Center DPM](backup-azure-backup-sql.md),</p> <p>[Serveur Azure Backup](backup-azure-microsoft-azure-backup.md)</p> |
| Machines virtuelles IaaS Azure (Windows)| - | [Azure Backup (extension de machine virtuelle)](backup-azure-vms-introduction.md) | 
| Machines virtuelles IaaS Azure (Linux) | - | [Azure Backup (extension de machine virtuelle)](backup-azure-vms-introduction.md) |

## Fonctionnalités
Ces cinq tableaux récapitulent la manière dont les fonctionnalités Azure Backup sont gérées dans chaque composant :

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
Chaque composant prend en charge la sauvegarde incrémentielle quel que soit le stockage cible (disque, bande, coffre de sauvegarde). Une sauvegarde incrémentielle permet de s’assurer que les sauvegardes sont rentables en termes de stockage et de temps. Pour ce faire, il transfère seulement les modifications effectuées depuis la dernière sauvegarde sur le stockage cible.

#### Compression
En outre, les sauvegardes sont compressées afin de réduire la quantité d’espace de stockage requise. L’extension de machine virtuelle est le seul composant qui n’effectue aucune compression. Avec l’extension de machine virtuelle, toutes les données de sauvegarde sont copiées du compte de stockage client vers le coffre de sauvegarde dans la même région sans être compressées. Bien que cela augmente légèrement le volume de stockage utilisé, le fait de stocker des données sans compression permet d’accélérer les restaurations.

#### Déduplication
La déduplication est prise en charge par System Center DPM et Serveur Azure Backup lorsqu’ils sont [déployés dans une machine virtuelle Hyper-V](http://blogs.technet.com/b/dpm/archive/2015/01/06/deduplication-of-dpm-storage-reduce-dpm-storage-consumption.aspx). La déduplication intervient au niveau de l’hôte grâce à la fonction de déduplication de Windows Server sur les disques durs virtuels attachés en tant que stockage de sauvegarde à la machine virtuelle.

>[AZURE.WARNING] La déduplication n’est en revanche pas disponible dans Azure pour aucun des composants d’Azure Backup. Lorsque System Center DPM et Azure Backup Server sont déployés dans Azure, les disques de stockage attachés à la machine virtuelle ne peuvent pas être dédupliqués.

### Sécurité

| Fonctionnalité | Agent Azure Backup | System Center DPM | Azure Backup Server | Azure Backup (extension de machine virtuelle) |
| ------- | --- | --- | --- | ---- |
| Sécurité du réseau (vers Azure) | ![Oui][green] |![Oui][green] | ![Oui][green] | ![Partiellement][yellow]|
| Sécurité des données (dans Azure) | ![Oui][green] |![Oui][green] | ![Oui][green] | ![Partiellement][yellow]|

![clé de table](./media/backup-introduction-to-azure-backup/table-key.png)

L’ensemble du trafic de sauvegarde entre vos serveurs et le coffre Azure Backup est chiffré à l’aide du protocole AES (Advanced Encryption Standard) 256. Les données sont envoyées via une connexion HTTPS sécurisée. Les données de sauvegarde sont également stockées dans le coffre Azure Backup sous une forme chiffrée. Seul le client conserve le mot de passe pour déverrouiller ces données. Microsoft ne peut déchiffrer les données de sauvegarde à aucun moment.

>[AZURE.WARNING] La clé utilisée pour chiffrer les données de sauvegarde est disponible uniquement chez le client. Microsoft ne conserve pas de copie dans Azure et n’a pas accès à la clé. Si la clé est égarée, Microsoft ne peut pas récupérer les données de sauvegarde.

La sauvegarde des machines virtuelles Azure exige la configuration du chiffrement *dans* la machine virtuelle. Utilisez BitLocker sur les machines virtuelles Windows et **dm-crypt** sur les machines virtuelles Linux. Azure Backup ne chiffre pas automatiquement les données de sauvegarde en provenance de ce chemin d’accès.

### Charges de travail prises en charge

| Fonctionnalité | Agent Azure Backup | System Center DPM | Azure Backup Server | Azure Backup (extension de machine virtuelle) |
| ------- | --- | --- | --- | ---- |
| Ordinateur Windows Server ; fichiers et dossiers | ![Oui][green] | ![Oui][green] | ![Oui][green] | |
| Ordinateur client Windows ; fichiers et dossiers | ![Oui][green] | ![Oui][green] | ![Oui][green] | |
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

Pour les clients qui protègent leurs données sur un serveur de sauvegarde (Serveurs DPM ou Azure Backup), le trafic généré entre le serveur principal et le serveur de sauvegarde peut également être compressé afin d’économiser de la bande passante.

#### Limitation du réseau
L’agent Azure Backup fournit la fonctionnalité de limitation qui vous permet de contrôler l’utilisation de la bande passante réseau pendant le transfert de données. Cela peut s’avérer utile si vous avez besoin de sauvegarder des données pendant les heures de travail, mais ne souhaitez pas que le processus de sauvegarde interfère avec le reste du trafic internet. La limitation du transfert de données s’applique aux activités de sauvegarde et de restauration.

### Sauvegarde et rétention

| | Agent Azure Backup | System Center DPM et Azure Backup Server | Azure Backup (extension de machine virtuelle) |
| --- | --- | --- | --- |
| Fréquence de sauvegarde (sur le coffre de sauvegarde) | Trois sauvegardes par jour | Deux sauvegardes par jour | Une sauvegarde par jour |
| Fréquence de sauvegarde (sur disque) | Non applicable | <p>Toutes les 15 minutes pour SQL Server</p> <p>Toutes les heures pour les autres charges de travail</p> | Non applicable |
| Options de rétention | Quotidienne, hebdomadaire, mensuelle, annuelle | Quotidienne, hebdomadaire, mensuelle, annuelle | Quotidienne, hebdomadaire, mensuelle, annuelle |
| Période de rétention | Jusqu’à 99 ans | Jusqu’à 99 ans | Jusqu’à 99 ans |
| Points de récupération dans le coffre Azure Backup | Illimité | Illimité | Illimité |
| Points de récupération sur le disque local | Non applicable | Non applicable | Non applicable |
| Points de récupération sur bande | Non applicable | Non applicable | Non applicable |

## Qu’est-ce que le fichier d’informations d’identification de coffre ?

Le fichier d’informations d’identification de coffre est un certificat qui est généré par le portail pour chaque archivage de sauvegarde. Le portail télécharge ensuite la clé publique pour le Service de contrôle d’accès (ACS). La clé privée est fournie à l’utilisateur lors du téléchargement des informations d’identification, puis entrée lors de l’enregistrement de l’ordinateur. La clé privée authentifie l’ordinateur pour envoyer des données de sauvegarde dans un archivage identifié dans le service Azure Backup.

Les informations d’identification de coffre sont utilisées uniquement pendant le flux de travail d’inscription. Il est de votre responsabilité de vous assurer que le fichier d’informations d’identification de coffre n’est pas compromis. S’il tombe entre les mains d’un utilisateur non autorisé, le fichier d’informations d’identification de coffre peut servir à inscrire d’autres ordinateurs pour le même archivage. Toutefois, comme les données de sauvegarde sont chiffrées à l’aide d’une phrase secrète appartenant au client, les données de sauvegarde existantes ne peuvent pas être compromises. Pour atténuer ce problème, les informations d’identification de coffre sont configurées pour expirer sous 48 heures. Vous pouvez télécharger les informations d’identification d’un archivage de sauvegarde autant de fois que nécessaire, seul le dernier fichier est applicable pendant le flux de travail d’inscription.

## Quelle est la différence entre Azure Backup et Azure Site Recovery ?
De nombreux clients ont tendance à confondre récupération de sauvegarde et récupération d’urgence. Les deux opérations capturent des données et fournissent une sémantique de restauration, mais chacune est associée à une proposition de valeur bien spécifique.

Azure Backup sauvegarde les données en local et dans le cloud. Azure Site Recovery coordonne la réplication, le basculement et la restauration automatique des machines virtuelles et des serveurs physiques. Les deux services sont importants, car votre solution de récupération d’urgence doit copier vos données en toute sécurité et les rendre récupérables (Azure Backup) *et* assurer la disponibilité et l’accessibilité de vos charges de travail (Azure Site Recovery) en cas de panne.

Les concepts qui suivent vous aideront à prendre des décisions importantes en matière de sauvegarde et de récupération.

| Concept | Détails | Sauvegarde | Récupération d’urgence |
| ------- | ------- | ------ | ----------------- |
| Objectif de point de récupération (RPO) | Quantité de perte de données acceptable si la récupération doit être exécutée. | Les solutions de sauvegarde offrent des RPO extrêmement variables. Les sauvegardes de machines virtuelles ont généralement un RPO d’un jour, contre seulement 15 minutes pour les sauvegardes de base de données. | Les solutions de récupération d’urgence ont un RPO faible. La copie de récupération d’urgence peut devoir être prête en seulement quelques secondes ou quelques minutes. |
| Objectif de délai de récupération (RTO) | Quantité de temps nécessaire pour effectuer une récupération ou une restauration complète. | Un RPO plus long est généralement synonyme pour la solution de sauvegarde d’une bien plus grande quantité de données à traiter, ce qui rallonge d’autant le RTO. Par exemple, il peut falloir plusieurs jours pour restaurer des données à partir de bandes, selon le temps nécessaire au transport de la bande depuis un site externe. | Les solutions de récupération d’urgence ont un RTO plus faible car elles sont davantage synchronisées avec la source et ont moins de modifications à traiter. |
| Rétention | Durée pendant laquelle les données doivent être stockées | <p>Pour les scénarios qui requièrent une reprise des opérations (altération des données, suppression accidentelle de fichiers, défaillances du système d’exploitation), les données de sauvegarde sont généralement conservées pendant 30 jours au maximum.</p> <p>Du point de vue de la conformité, il peut être nécessaire de conserver les données pendant des mois, voire des années. Dans ce cas, les données de sauvegarde sont parfaitement adaptées aux besoins d’archivage.</p> | Une récupération d’urgence porte uniquement sur les données de récupération opérationnelle, soit en général quelques heures, sans dépasser une journée. Puisque les solutions de récupération d’urgence sont conçues pour capturer les données à un niveau extrêmement précis, l’utilisation des données de récupération d’urgence n’est pas recommandée dans le cadre d’une rétention à long terme. |


## Étapes suivantes

- [Test d’Azure Backup](backup-try-azure-backup-in-10-mins.md)
- [Forum aux questions sur le service Azure Backup](backup-azure-backup-faq.md)
- Consultez le [forum Azure Backup](http://go.microsoft.com/fwlink/p/?LinkId=290933)


[green]: ./media/backup-introduction-to-azure-backup/green.png
[yellow]: ./media/backup-introduction-to-azure-backup/yellow.png
[red]: ./media/backup-introduction-to-azure-backup/red.png

<!---HONumber=AcomDC_0316_2016-->