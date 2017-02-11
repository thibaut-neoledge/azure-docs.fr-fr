---
title: Configurer Microsoft Azure StorSimple avec Veeam | Microsoft Docs
description: "Décrit la configuration de la cible de sauvegarde StorSimple avec Veeam."
services: storsimple
documentationcenter: 
author: hkanna
manager: matd
editor: 
ms.assetid: 
ms.service: storsimple
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 12/06/2016
ms.author: hkanna
translationtype: Human Translation
ms.sourcegitcommit: dfecc291c441b862499d7eaaab421c7b72f929b2
ms.openlocfilehash: 2f71afc6c60cc22ef73a902ee67466313221def2

---

# <a name="configure-storsimple-with-veeam"></a>Configurer StorSimple avec Veeam

## <a name="overview"></a>Vue d'ensemble

Microsoft Azure StorSimple est une solution de stockage cloud hybride qui s’attaque à la complexité de la croissance exponentielle des données. Cette solution utilise un compte de stockage Azure en tant qu’extension de la solution locale et hiérarchise automatiquement les données sur le stockage local et sur le stockage cloud.

Cet article décrit l’intégration de StorSimple à Veeam, ainsi que les meilleures pratiques en matière d’intégration de ces deux solutions. Nous fournissons également diverses recommandations concernant la procédure de configuration de Veeam de manière à optimiser son intégration à StorSimple. Nous nous reposons sur les meilleures pratiques, concepteurs d’architecture de sauvegarde et administrateurs de Veeam quant à la meilleure manière de configurer Veeam pour répondre à vos besoins de sauvegarde et à vos contrats de niveau de service.

Cet article illustre les étapes de configuration et les concepts clés, mais ne constitue en aucun cas un guide de configuration ou d’installation pas à pas. Cet article repose sur l’hypothèse que l’infrastructure et les composants de base sont en état de fonctionnement et prêts à prendre en charge les concepts que nous décrivons.

## <a name="why-storsimple-as-a-backup-target"></a>Pourquoi utiliser StorSimple comme cible de sauvegarde ?

La solution StorSimple constitue une cible de sauvegarde idéale pour les raisons suivantes :

-   Elle offre un stockage local standard utilisable par les applications de sauvegarde sans aucune modification pour fournir une destination de sauvegarde rapide. StorSimple est également disponible pour les restaurations rapides des sauvegardes récentes.

-   Sa hiérarchisation cloud s’intègre de façon transparente à un compte de stockage cloud pour tirer parti d’un stockage Microsoft Azure économique.

-   StorSimple fournit automatiquement un stockage hors site pour la récupération d’urgence.


## <a name="target-audience"></a>Public cible

Cet article s’adresse aux administrateurs de sauvegarde, aux administrateurs de stockage et aux concepteurs d’architecture de stockage qui disposent de connaissances concernant le stockage, Windows Server 2012 R2, Ethernet, les services cloud et Veeam.

## <a name="supported-versions"></a>Versions prises en charge

-   Veeam 9 et versions ultérieures.

-   [StorSimple Update 3 et versions ultérieures](/storsimple-overview#storsimple-workload-summary).



## <a name="key-concepts"></a>Concepts clés

Comme dans le cas de toute autre solution de stockage, une évaluation en profondeur des performances de stockage de la solution, des contrats de niveau de service, de la fréquence de changement et de l’augmentation des besoins en matière de capacité se révèle un facteur décisif de réussite. L’idée générale consiste à introduire un niveau cloud, de sorte que les temps d’accès et débits que vous offre le cloud joueront un rôle fondamental dans l’efficacité de StorSimple.

StorSimple est conçu pour fournir une capacité de stockage aux applications qui opèrent sur un jeu de données de travail (données chaudes) bien défini. Dans ce modèle, le jeu de données de travail est stocké sur les niveaux locaux, tandis que les données inactives/froides/archivées sont hiérarchisées vers le cloud. Ce modèle est illustré dans la figure ci-après. La ligne verte quasiment plate représente les données stockées sur les niveaux locaux de l’appareil StorSimple. La ligne rouge représente la quantité totale de données stockées sur tous les niveaux de la solution StorSimple. L’espace entre la ligne verte plate et la courbe exponentielle rouge représente la quantité totale de données stockées dans le cloud.

**Hiérarchisation StorSimple**
![Diagramme de la hiérarchisation Storsimple](./media/storsimple-configure-backup-target-using-veeam/image1.jpg)

En gardant cette architecture à l’esprit, vous découvrirez que la solution StorSimple constitue une cible de sauvegarde idéale. Grâce à StorSimple, vous pouvez :

-   procéder aux restaurations les plus fréquentes à partir du jeu de données de travail local ;

-   utiliser le cloud pour la récupération d’urgence hors site et pour les données plus anciennes impliquant des restaurations moins fréquentes.

## <a name="storsimple-benefits"></a>Avantages de StorSimple

StorSimple fournit une solution locale qui s’intègre parfaitement à Microsoft Azure en tirant parti d’un accès transparent au stockage local et cloud.

StorSimple utilise la hiérarchisation automatique entre l’appareil local, qui contient un stockage SSD (solid-state device) et SAS (serial-attached SCSI), et le service Stockage Azure. La hiérarchisation automatique conserve les données fréquemment sollicitées en local, sur les niveaux SSD et SAS, et déplace les données rarement utilisées vers Stockage Azure.

StorSimple offre les avantages suivants :

-   Algorithmes de déduplication et de compression uniques qui tirent parti du cloud pour atteindre des niveaux de déduplication inégalés

-   Haute disponibilité

-   Géoréplication reposant sur la fonctionnalité de géoréplication Azure

-   Intégration à Azure

-   Chiffrement de données dans le cloud

-   Récupération d’urgence et conformité améliorées

Bien que StorSimple présente deux principaux scénarios de déploiement (cible de sauvegarde principale et secondaire), gardez à l’esprit qu’il s’agit essentiellement d’un dispositif de stockage de bloc. StorSimple exécute la totalité des tâches de compression et de déduplication, et assure de façon transparente l’envoi et la récupération des données stockées dans le cloud à destination de l’application et du système de fichiers.

Pour plus d’informations sur StorSimple, consultez l’article [StorSimple série 8000 : une solution de stockage de cloud hybride](storsimple-overview.md) et passez en revue les [caractéristiques techniques de l’appareil StorSimple série 8000](storsimple-technical-specifications-and-compliance.md).

> [!IMPORTANT]
> L’utilisation d’un appareil StorSimple comme cible de sauvegarde est uniquement prise en charge avec StorSimple 8000 Update 3 ou une version ultérieure.

## <a name="architecture-overview"></a>Présentation de l'architecture

Les tableaux ci-après contiennent les informations d’aide initiales concernant l’architecture liée aux différents modèles d’appareil.

#### <a name="storsimple-capacities-for-local-and-cloud-storage"></a>Capacités de stockage local et cloud offertes par StorSimple


| Capacité de stockage       | 8100       | 8600       |
|------------------------|---------------|-----------------|
| Capacité de stockage local | &lt; 10 Tio\*  | &lt; 20 Tio\*  |
| Capacité de stockage cloud | &gt; 200 Tio\* | &gt; 500 Tio\* |

\* La taille de stockage indiquée ne prend en compte aucune déduplication ni compression.

#### <a name="storsimple-capacities-for-primary-and-secondary-backups"></a>Capacités de StorSimple dans les scénarios de sauvegarde principale et secondaire


| Scénario de sauvegarde  | Capacité de stockage local                                         | Capacité de stockage cloud                      |
|------------------|----------------------------------------------------------------|---------------------------------------------|
| Sauvegarde principale   | Sauvegardes récentes stockées sur le stockage local afin d’accélérer la récupération (conformément à l’objectif de point de récupération, ou RPO) | Capacité du cloud adaptée à l’historique des sauvegardes (RPO) |
| Sauvegarde secondaire | Possibilité de stocker une copie secondaire des données de sauvegarde dans la capacité du cloud  |

## <a name="storsimple-as-a-primary-backup-target"></a>Utiliser StorSimple comme cible de sauvegarde principale

Dans ce scénario, les volumes StorSimple sont présentés à l’application de sauvegarde comme l’unique référentiel pour les sauvegardes. La figure ci-dessous illustre l’architecture de la solution dans laquelle toutes les sauvegardes utilisent les volumes hiérarchisés StorSimple aussi bien pour les sauvegardes que pour les restaurations.

![Diagramme logique de l’utilisation de StorSimple comme cible de sauvegarde principale](./media/storsimple-configure-backup-target-using-veeam/primarybackuptargetlogicaldiagram.png)

### <a name="primary-target-backup-logical-steps"></a>Procédure logique de sauvegarde de la cible principale

1.  Le serveur de sauvegarde contacte l’agent de sauvegarde cible qui transmet alors des données au serveur de sauvegarde.

2.  Le serveur de sauvegarde écrit les données sur les volumes hiérarchisés StorSimple.

3.  Le serveur de sauvegarde met à jour la base de données de catalogues et accomplit le travail de sauvegarde.

4.  Le script de capture instantanée déclenche la gestion des captures instantanées cloud de StorSimple.

5.  Conformément à une stratégie de rétention, le serveur de sauvegarde supprime les sauvegardes arrivées à expiration.

### <a name="primary-target-restore-logical-steps"></a>Procédure logique de restauration de la cible principale

1.  Le serveur de sauvegarde commence à restaurer les données appropriées du référentiel de stockage.

2.  L’agent de sauvegarde reçoit les données en provenance du serveur de sauvegarde.

3.  Le serveur de sauvegarde achève le travail de restauration.

## <a name="storsimple-as-a-secondary-backup-target"></a>Utiliser StorSimple comme cible de sauvegarde secondaire

Dans ce scénario, les volumes StorSimple sont principalement utilisés à des fins de rétention à long terme ou d’archivage.

La figure ci-après illustre l’architecture dans laquelle les sauvegardes et restaurations initiales ciblent un volume hautes performances. Ces sauvegardes sont copiées et archivées sur un volume hiérarchisé StorSimple conformément à une planification donnée.

Il est important que vous dotiez votre volume hautes performances de suffisamment d’espace et de performances pour lui permettre de prendre en charge les exigences en matière de performances et de capacité de stratégie de rétention.

![Diagramme logique de l’utilisation de StorSimple comme cible de sauvegarde secondaire](./media/storsimple-configure-backup-target-using-veeam/secondarybackuptargetlogicaldiagram.png)

### <a name="secondary-target-backup-logical-steps"></a>Procédure logique de sauvegarde de la cible secondaire

1.  Le serveur de sauvegarde contacte l’agent de sauvegarde cible qui transmet alors des données au serveur de sauvegarde.

2.  Le serveur de sauvegarde écrit les données dans le stockage hautes performances.

3.  Le serveur de sauvegarde met à jour la base de données de catalogues et accomplit le travail de sauvegarde.

4.  Conformément à une stratégie de rétention, le serveur de sauvegarde copie les sauvegardes dans StorSimple.

5.  Le script de capture instantanée déclenche la gestion des captures instantanées cloud de StorSimple.

6.  Conformément à une stratégie de rétention, le serveur de sauvegarde supprime les sauvegardes arrivées à expiration.

### <a name="secondary-target-restore-logical-steps"></a>Procédure logique de restauration de la cible secondaire

1.  Le serveur de sauvegarde commence à restaurer les données appropriées du référentiel de stockage.

2.  L’agent de sauvegarde reçoit les données en provenance du serveur de sauvegarde.

3.  Le serveur de sauvegarde achève le travail de restauration.

## <a name="deploy-the-solution"></a>Déployer la solution

La procédure de déploiement de cette solution comporte trois étapes : préparation de l’infrastructure réseau, déploiement de votre appareil StorSimple comme cible de sauvegarde, puis déploiement du logiciel Veeam. Chacune de ces étapes est abordée en détail dans les sections suivantes.

### <a name="configure-the-network"></a>configurer le réseau.

En tant que solution intégrée au cloud Azure, StorSimple requiert une connexion active et opérationnelle à ce dernier. Cette connexion est utilisée pour des opérations telles que les captures instantanées cloud, la gestion, le transfert de métadonnées et la hiérarchisation des données les plus anciennes et les moins sollicitées vers le stockage cloud Azure.

Pour que la solution fonctionne de manière optimale, nous vous recommandons d’appliquer les meilleures pratiques de mise en réseau suivantes :

-   La liaison qui connecte la hiérarchisation StorSimple à Azure doit répondre à vos exigences de bande passante en appliquant la Qualité de service (QoS) appropriée aux commutateurs de votre infrastructure conformément à vos contrats de niveau de service en matière d’objectifs de point de récupération (RPO) et de temps de récupération (RTO).

-   Les latences d’accès maximales au service Stockage Blob Azure doivent être de l’ordre de 80 ms.

### <a name="deploy-storsimple"></a>Déployer StorSimple

Pour découvrir un guide de déploiement de StorSimple pas à pas, consultez l’article [Déploiement de votre appareil StorSimple local](storsimple-deployment-walkthrough-u2.md).

### <a name="deploy-veeam"></a>Déployer Veeam

Pour découvrir les meilleures pratiques en matière d’installation de Veeam, consultez l’article [Best practices for Veeam9 (Meilleures pratiques concernant Veeam 9) ](https://bp.veeam.expert/) et le guide de l’utilisateur accessible sur le site du [Centre d’aide Veeam (documentation technique)](https://www.veeam.com/documentation-guides-datasheets.html).

## <a name="configure-the-solution"></a>Configurer la solution

Dans cette section, nous fournissons quelques exemples de configuration. Les exemples/recommandations ci-après illustrent l’implémentation la plus élémentaire et fondamentale. Cette implémentation peut ne pas s’appliquer directement à vos exigences spécifiques en matière de sauvegarde.

### <a name="configure-storsimple"></a>Configurer StorSimple

| Tâches de déploiement StorSimple                                                                                                                 | Commentaires supplémentaires                                                                                                                                                                                                                                                                                      |
|---------------------------------------------------------------------------------------------------------------------------------------------|----------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------|
| Déploiement de votre appareil StorSimple local                                                                                    | Version prise en charge : Update 3 et versions ultérieures.                                                                                                                                                                                                                                                                 |
| Activation du mode de cible de sauvegarde.                                                                                                                | Utilisez les commandes ci-après pour activer ou désactiver le mode de cible de sauvegarde et obtenir le statut. Pour plus d’informations, consultez l’article [Connexion à distance à votre appareil StorSimple](storsimple-remote-connect.md).</br> Pour activer le mode de sauvegarde :`Set-HCSBackupApplianceMode -enable`</br>  Pour désactiver le mode de sauvegarde :`Set-HCSBackupApplianceMode -disable`</br> Pour obtenir l’état actuel des paramètres de mode de sauvegarde :`Get-HCSBackupApplianceMode` |
| Création d’un conteneur de volumes commun pour votre volume qui stocke les données de sauvegarde. Toutes les données d’un conteneur de volumes sont dédupliquées. | Les conteneurs de volumes StorSimple définissent les domaines de déduplication.                                                                                                                                                                                                                                             |
| Création de volumes StorSimple.                                                                                                                 | Créez des volumes en les dimensionnant le plus conformément possible à l’usage que vous prévoyez d’en faire, car la taille du volume a une incidence sur la durée des captures instantanées cloud. Pour plus d’informations sur le dimensionnement d’un volume, consultez la section [Stratégies de rétention](#retention-policies).</br> </br> Utilisez les volumes hiérarchisés StorSimple et sélectionnez l’option **Utiliser ce volume pour les données d’archivage auxquelles vous accédez moins souvent**. </br> Les volumes qui sont uniquement attachés localement ne sont pas pris en charge.        |
| Création d’une stratégie de sauvegarde StorSimple unique pour tous les volumes cibles de sauvegarde.                                                               | Une stratégie de sauvegarde StorSimple définit le groupe de cohérence de volume.                                                                                                                                                                                                                                       |
| Désactivation de la planification lorsque les captures instantanées arrivent à expiration.                                                                                                    | Les captures instantanées sont déclenchées sous la forme d’une opération post-traitement.                                                                                                                                                                                                                                                         |
|                                                                                                     |                                             |


### <a name="configure-host-backup-server-storage"></a>Configurer le stockage du serveur de sauvegarde hôte

Vérifiez que le stockage du serveur de sauvegarde hôte est configuré conformément aux instructions suivantes :  

- N’utilisez pas de volumes fractionnés (créés par le Gestionnaire de disque Windows), car ces volumes ne sont pas pris en charge.
- Formatez vos volumes à l’aide du système de fichiers NTFS avec une taille d’unité d’allocation de 64 Ko.
- Mappez les volumes StorSimple directement sur le serveur Veeam. 
    - Utilisez iSCSI pour les serveurs physiques.


## <a name="best-practices-for-storsimple-and-veeam"></a>Meilleures pratiques pour StorSimple et Veeam

Configurez votre solution conformément aux instructions suivantes.

### <a name="operating-system"></a>Système d’exploitation

-   Désactivez le chiffrement et la déduplication Windows Server pour le système de fichiers NTFS.

-   Désactivez la défragmentation Windows Server sur les volumes StorSimple.

-   Désactivez l’indexation Windows Server sur les volumes StorSimple.

-   Exécutez une analyse antivirus au niveau de l’hôte source (et non par rapport aux volumes StorSimple).

-   Désactivez l’activité [Maintenance Windows Server](https://msdn.microsoft.com/library/windows/desktop/hh848037.aspx) par défaut dans le Gestionnaire des tâches.

    - Désactivez le programme de configuration de l’activité de maintenance dans le Planificateur de tâches Windows.

        Ou

    - Téléchargez : [PSEXEC – Microsoft Sysinternals](https://technet.microsoft.com/sysinternals/bb897553.aspx).

      - Après avoir téléchargé PSEXEC, exécutez Windows PowerShell en tant qu’administrateur, puis tapez :

            `psexec \\%computername% -s schtasks /change /tn “MicrosoftWindowsTaskSchedulerMaintenance Configurator" /disable`

### <a name="storsimple"></a>StorSimple

-   Assurez-vous que l’appareil StorSimple est mis à jour avec la version [Update 3 ou une version ultérieure](storsimple-install-update-3.md).

-   Isolez le trafic iSCSI et cloud. Utilisez des connexions iSCSI dédiées pour le trafic entre StorSimple et le serveur de sauvegarde.

-   Assurez-vous que l’appareil StorSimple est une cible de sauvegarde dédiée. Les charges de travail mixtes ne sont pas prises en charge, car elles ont une incidence sur vos objectifs RTO/RPO.

### <a name="veeam"></a>Veeam

-   La base de données Veeam doit être locale sur le serveur et ne doit pas se trouver sur un volume StorSimple.

-   Pour la récupération d’urgence, sauvegardez votre base de données Veeam sur un volume StorSimple.

-   Nous prenons en charge les sauvegardes complètes et incrémentielles Veeam pour cette solution. Nous vous déconseillons d’utiliser les sauvegardes synthétiques et différentielles.

-   Les fichiers de données de sauvegarde doivent uniquement contenir les données d’un travail spécifique. Par exemple, aucun ajout de supports n’est autorisé entre les différents travaux.

-   Désactivez la vérification des travaux. Si nécessaire, la vérification doit être planifiée après le dernier travail de sauvegarde. Il est important de comprendre que ce travail a une incidence sur votre fenêtre de sauvegarde.

-   Désactivez la préallocation de supports.

-   Assurez-vous que le traitement parallèle est activé.

-   Désactivez la compression.

-   Désactivez la déduplication sur le travail de sauvegarde.

-   Définissez l’optimisation sur **LAN Target (Cible LAN)**.

-   Activez l’option **Create active full backup (Créer une sauvegarde complète active)** (toutes les 2 semaines).

-   Dans le référentiel de sauvegarde, configurez l’option **Use per-VM backup files (Utiliser des fichiers de sauvegarde par machine virtuelle)**.

-   Définissez l’option **Use multiple upload streams per job (Utiliser plusieurs flux de chargement par travail)** sur 8 (la valeur maximale autorisée est de 16). Augmentez ou diminuez cette valeur en fonction de l’utilisation du processeur sur l’appareil StorSimple.

## <a name="retention-policies"></a>Stratégies de rétention

L’une des stratégies de rétention de sauvegarde les plus utilisées est la stratégie GFS (Grandfather, Father, and Son - Grand-père, père et fils). Dans le cadre de cette stratégie, une sauvegarde incrémentielle est effectuée une fois par jour. Les sauvegardes complètes sont exécutées à une fréquence hebdomadaire et mensuelle. Cette stratégie donne lieu à 6 volumes hiérarchisés StorSimple.

-   Un même volume contient les sauvegardes complètes hebdomadaires, mensuelles et annuelles.

-   Les 5 autres volumes stockent les sauvegardes incrémentielles quotidiennes.

Dans l’exemple ci-après, nous utilisons une rotation GFS. Cet exemple repose sur les données suivantes :

-   Utilisation de données non dédupliquées ni compressées.

-   Sauvegardes complètes de 1 Tio chacune.

-   Sauvegardes incrémentielles quotidiennes de 500 Gio chacune.

-   4 sauvegardes hebdomadaires conservées pendant un mois.

-   12 sauvegardes mensuelles conservées pendant un an.

-   1 sauvegarde annuelle conservée pendant 10 ans.

Sur la base des hypothèses qui précèdent, créez un volume hiérarchisé StorSimple de 26 Tio pour les sauvegardes complètes mensuelles et annuelles. Créez un volume hiérarchisé StorSimple de 5 Tio pour chacune des sauvegardes incrémentielles quotidiennes.

| Type de sauvegarde et rétention | Taille en Tio | Multiplicateur GFS\*                                       | Capacité totale en Tio          |
|-----------------------|----------|--------------------------------------------------------|-----------------------------|
| Complète hebdomadaire           | 1        | 4                                                      | 4                           |
| Incrémentielle quotidienne     | 0.5      | 20 (cycles correspondant au nombre de semaines par mois) | 12 (2 pour le quota supplémentaire) |
| Complète mensuelle          | 1        | 12                                                     | 12                          |
| Complète annuelle           | 1        | 10                                                     | 10                          |
| Exigence GFS       |          |                                                        | 38                          |
| Quota supplémentaire      | 4        |                                                        | 42 au total pour l’exigence GFS   |

\*Le multiplicateur GFS est le nombre de copies que vous devez protéger et conserver pour vous conformer à vos stratégies de sauvegarde.

## <a name="configuring-veeam-storage"></a>Configuration du stockage Veeam

1.  Accédez aux paramètres **Backup Infrastructure (Infrastructure de sauvegarde**. Sélectionnez **Backup Repositories (Référentiels de sauvegarde)**, puis cliquez avec le bouton droit et sélectionnez **Add Backup Repository (Ajouter un référentiel de sauvegarde)**.

    ![Console d’administration de Veeam, écran du référentiel de sauvegarde](./media/storsimple-configure-backup-target-using-veeam/veeamimage1.png)

1.  Renseignez les champs **Name (Nom)** et **Description (Description)** pour le référentiel. Cliquez sur **Next > (Suivant)**.

    ![Console d’administration de Veeam, écran de saisie d’un nom et d’une description](./media/storsimple-configure-backup-target-using-veeam/veeamimage2.png)

1.  Sélectionnez le référentiel de sauvegarde avec le type **Microsoft Windows Server**. Choisissez le serveur Veeam. Cliquez sur **Next > (Suivant)**.

    ![Console d’administration de Veeam, sélection du type de référentiel de sauvegarde](./media/storsimple-configure-backup-target-using-veeam/veeamimage3.png)

1.  Pour renseigner le champ **Location (Emplacement)**, accédez au volume requis et sélectionnez-le. Définissez l’option **Limit maximum concurrent tasks to: (Limiter le nombre maximal de tâches simultanées à :)** sur 4. Ceci garantit le fait que seulement 4 disques virtuels sont traités simultanément lorsque chaque machine virtuelle est traitée.
Cliquez sur **Avancé**.

    ![Console d’administration de Veeam, sélection du volume](./media/storsimple-configure-backup-target-using-veeam/veeamimage4.png)


1.  Cochez la case **Use per-VM backup files (Utiliser des fichiers de sauvegarde par machine virtuelle)**.

    ![Console de gestion Veeam, paramètres de compatibilité de stockage](./media/storsimple-configure-backup-target-using-veeam/veeamimage5.png)

1.  Cochez la case **vPower NFS service on the mount server (recommended) (Service NFS vPower sur le serveur de montage (recommandé))**.

    ![Console d’administration de Veeam, écran du référentiel de sauvegarde](./media/storsimple-configure-backup-target-using-veeam/veeamimage6.png)

1.  Vérifiez les paramètres, puis passez à la page suivante.

    ![Console d’administration de Veeam, écran du référentiel de sauvegarde](./media/storsimple-configure-backup-target-using-veeam/veeamimage7.png)

    Un référentiel est ajouté au serveur Veeam.

## <a name="storsimple-as-a-primary-backup-target"></a>Utiliser StorSimple comme cible de sauvegarde principale

> [!IMPORTANT]
> Si vous devez restaurer des données à partir d’une sauvegarde qui a été hiérarchisée vers le cloud, la restauration se produit à la vitesse du cloud.

Dans la figure ci-après, nous illustrons le mappage d’un volume classique sur un travail de sauvegarde. Dans ce cas précis, toutes les sauvegardes hebdomadaires sont mappées sur le disque des sauvegardes complètes effectuées le samedi, et les sauvegardes incrémentielles sont mappées sur les disques des sauvegardes incrémentielles exécutées du lundi au vendredi. Toutes les sauvegardes et restaurations se produisent à partir d’un volume hiérarchisé StorSimple.

![Diagramme logique de la configuration de la cible de sauvegarde principale ](./media/storsimple-configure-backup-target-using-veeam/primarybackuptargetdiagram.png)

#### <a name="storsimple-as-a-primary-backup-target-grandfather-father-and-son-gfs-schedule-example"></a>Exemple de planification GFS pour StorSimple utilisé comme cible de sauvegarde principale

| Voici un exemple de planification de rotation GFS pendant 4 semaines, une fois par mois et une fois par an : |               |             |
|--------------------------------------------------------------------------|---------------|-------------|
| Fréquence/type de sauvegarde   | Complet          | Incrémentielle (jours 1 à 5)  |
| Hebdomadaire (semaines 1 à 4)    | Samedi | Lundi à vendredi |
| Mensuelle     | Samedi  |             |
| Annuelle      | Samedi  |             |


### <a name="assigning-storsimple-volumes-to-a-veeam-backup-job"></a>Affecter des volumes StorSimple à un travail de sauvegarde Veeam

1.  Créez un travail quotidien à l’aide de votre volume StorSimple Veeam principal dans le cas d’un scénario de cible de sauvegarde principale, ou en utilisant un stockage en attachement direct (DAS), un stockage réseau (NAS) ou un stockage constitué de plusieurs disques (JBOD) dans le cas d’un scénario de cible de sauvegarde secondaire. Accédez à **Backup &#38; Replication (Sauvegarde et réplication) &gt; Backup (Sauvegarde)**. Cliquez avec le bouton droit, puis sélectionnez VMware ou Hyper-V en fonction de votre environnement.

    ![Console d’administration de Veeam, nouveau travail de sauvegarde](./media/storsimple-configure-backup-target-using-veeam/veeamimage8.png)

1.  Renseignez les champs **Name (Nom)** et **Description (Description)** pour le travail de sauvegarde quotidien.

    ![Console d’administration de Veeam, écran de nouveau travail de sauvegarde](./media/storsimple-configure-backup-target-using-veeam/veeamimage9.png)

1.  Sélectionnez une machine virtuelle sur laquelle effectuer la sauvegarde.

    ![Console d’administration de Veeam, écran de nouveau travail de sauvegarde](./media/storsimple-configure-backup-target-using-veeam/veeamimage10.png)

1.  Sélectionnez les valeurs appropriées dans les champs **Backup proxy (Proxy de sauvegarde)** et **Backup repository (Référentiel de sauvegarde)**. Définissez l’option **Restore points to keep on disk (Points de restauration à conserver sur le disque)** conformément aux définitions des objectifs RPO/RTO de votre environnement sur le stockage connecté localement. Cliquez sur Avancé.

    ![Console d’administration de Veeam, écran de nouveau travail de sauvegarde](./media/storsimple-configure-backup-target-using-veeam/veeamimage11.png)

    Dans l’onglet **Backup (Sauvegarde)**, choisissez **Incremental (Incrémentielle)**. Assurez-vous que la case **Create synthetic full backups periodically (Créer régulièrement des sauvegardes complètes synthétiques)** est décochée. Cochez la case **Create active full backups periodically (Créer régulièrement des sauvegardes complètes actives)**, puis activez l’option Weekly on selected days (Une fois par semaine les jours sélectionnés) définie sur chaque samedi sous **Active full backups (Sauvegardes complètes actives)**.

    ![Console d’administration de Veeam, écran de paramètres avancés du nouveau travail de sauvegarde](./media/storsimple-configure-backup-target-using-veeam/veeamimage12.png)

    Dans l’onglet Storage (Stockage), assurez-vous que la déduplication et la compression sont désactivées, et que les options d’exclusion des blocs de fichiers d’échange et des blocs de fichiers supprimés sont activées. Définissez l’option **Storage optimization (Optimisation du stockage)** sur **LAN Target (Cible LAN)** pour équilibrer les performances et la déduplication.

    ![Console d’administration de Veeam, écran de paramètres avancés du nouveau travail de sauvegarde](./media/storsimple-configure-backup-target-using-veeam/veeamimage13.png)

    Pour plus d’informations sur les paramètres de déduplication et de compression, consultez l’article [Data Compression and Deduplication (Compression et déduplication des données)](https://helpcenter.veeam.com/backup/vsphere/compression_deduplication.html)

1.  Vous pouvez cocher la case **Enable application-aware processing (Activer le traitement adapté aux applications)**.

    ![Console d’administration de Veeam, écran de traitement de système d’exploitation invité du nouveau travail de sauvegarde](./media/storsimple-configure-backup-target-using-veeam/veeamimage14.png)

1.  Définissez la planification sur une seule exécution quotidienne à l’heure de votre choix.

    ![Console d’administration de Veeam, écran de planification du nouveau travail de sauvegarde](./media/storsimple-configure-backup-target-using-veeam/veeamimage15.png)

## <a name="storsimple-as-a-secondary-backup-target"></a>Utiliser StorSimple comme cible de sauvegarde secondaire

> [!NOTE]
> En cas de restauration de données à partir d’une sauvegarde qui a été hiérarchisée vers le cloud, la restauration se produit à la vitesse du cloud.

Dans ce modèle, vous devez disposer d’un média de stockage (autre que StorSimple) faisant office de cache temporaire. Par exemple, vous pouvez utiliser un volume RAID pour prendre en charge l’espace, les E/S et la bande passante. Nous vous recommandons d’utiliser les valeurs RAID 5, 50 et 10.

Dans la figure ci-après, nous présentons des volumes locaux de rétention à court terme (sur le serveur) et des volumes d’archive de rétention à long terme classiques. Dans ce cas précis, toutes les sauvegardes s’exécutent sur le volume RAID local (sur le serveur). Ces sauvegardes sont régulièrement dupliquées et archivées sur un volume d’archive. Veillez à dimensionner votre volume RAID local (sur le serveur) conformément aux exigences en matière de performances et de capacité de rétention à court terme.



| Type de sauvegarde et rétention                    |Stockage configuré| Taille (Tio) | Multiplicateur GFS | Capacité totale (Tio)        |
|----------------------------------------------|-----|----------|----------------|------------------------|
| Semaine 1 (sauvegardes complètes et incrémentielles) |Disque local (court terme)| 1        | 1              | 1           |
| StorSimple semaines 2 à 4           |Disque StorSimple (long terme) | 1        | 4              | 4                   |
| Complète mensuelle                                 |Disque StorSimple (long terme) | 1        | 12             | 12                   |
| Complète annuelle                               |Disque StorSimple (long terme) | 1        | 1              | 1                   |
|Exigence en matière de taille des volumes GFS | |          |                | 18*|

\* La capacité totale inclut 17 Tio de disques StorSimple et 1 Tio de volume RAID local.

![Diagramme logique de l’utilisation de StorSimple comme cible de sauvegarde secondaire ](./media/storsimple-configure-backup-target-using-veeam/secondarybackuptargetdiagram.png)

#### <a name="gfs-example-schedule"></a>Exemple de planification GFS :

| planification de rotation GFS hebdomadaire, mensuelle et annuelle|                    |                   |                   |                   |                   |                   |
|--------------------------------------------------------------------------|--------------------|-------------------|-------------------|-------------------|-------------------|-------------------|
| Semaine                                                                     | Complet               | Incrémentielle jour 1        | Incrémentielle jour 2        | Incrémentielle jour 3        | Incrémentielle jour 4        | Incrémentielle jour 5        |
| Semaine 1                                                                   | Volume RAID local  | Volume RAID local | Volume RAID local | Volume RAID local | Volume RAID local | Volume RAID local |
| Semaine 2                                                                   | StorSimple semaines 2 à 4 |                   |                   |                   |                   |                   |
| Semaine 3                                                                   | StorSimple semaines 2 à 4 |                   |                   |                   |                   |                   |
| Semaine 4                                                                   | StorSimple semaines 2 à 4 |                   |                   |                   |                   |                   |
| Mensuelle                                                                  | StorSimple mensuelle |                   |                   |                   |                   |                   |
| Annuelle                                                                   | StorSimple annuelle  |                   |                   |                   |                   |                   |


### <a name="assigning-storsimple-volumes-to-a-veeam-copy-job"></a>Affecter des volumes StorSimple à un travail de copie Veeam

1.  Lancez l’Assistant New Backup Copy Job (Nouveau travail de copie de sauvegarde). Accédez à **Jobs (Travaux) > Backup Copy (Copie de sauvegarde)**. Sélectionnez VMware ou Hyper-V en fonction de votre environnement.

    ![Console d’administration de Veeam, écran de nouveau travail de copie de sauvegarde](./media/storsimple-configure-backup-target-using-veeam/veeamimage16.png)

1.  Spécifiez le nom et la description du travail.

    ![Console d’administration de Veeam, écran de nouveau travail de copie de sauvegarde](./media/storsimple-configure-backup-target-using-veeam/veeamimage17.png)

1.  Sélectionnez les machines virtuelles à traiter, sélectionnez l’option From backups (À partir des sauvegardes), puis sélectionnez la sauvegarde quotidienne créée précédemment.

    ![Console d’administration de Veeam, écran de nouveau travail de copie de sauvegarde](./media/storsimple-configure-backup-target-using-veeam/veeamimage18.png)

1.  Le cas échéant, excluez des objets du travail de copie de sauvegarde.

1.  Sélectionnez **Backup repository (Référentiel de sauvegarde)**, définissez la valeur de l’option **Restore points to keep (Points de restauration à conserver)** et prenez soin de cocher la case **Keep the following restore points for archival purposes (Conserver les points de restauration ci-dessous à des fins d’archivage)**. Définissez la fréquence de sauvegarde, puis cliquez sur **Advanced (Avancé)**.

    ![Console d’administration de Veeam, écran de nouveau travail de copie de sauvegarde](./media/storsimple-configure-backup-target-using-veeam/veeamimage19.png)

1.  Spécifiez les paramètres avancés suivants :

    -   Dans l’onglet **Maintenance (Maintenance)**, désactivez la protection contre l’altération des niveaux de stockage.

    -   Dans l’onglet **Storage (Stockage)**, assurez-vous que la déduplication et la compression sont désactivées.

    ![Console d’administration de Veeam, écran de paramètres avancés du nouveau travail de copie de sauvegarde](./media/storsimple-configure-backup-target-using-veeam/veeamimage20.png)

    ![Console d’administration de Veeam, écran de paramètres avancés du nouveau travail de copie de sauvegarde](./media/storsimple-configure-backup-target-using-veeam/veeamimage21.png)

1.  Indiquez que le transfert de données est direct.

1.  Définissez la planification de la fenêtre de copie de sauvegarde selon vos besoins, puis terminez la procédure.


Pour plus d’informations, consultez l’article [Creating backup copy jobs (Création de travaux de copie de sauvegarde)](https://helpcenter.veeam.com/backup/hyperv/backup_copy_create.html).

## <a name="storsimple-cloud-snapshots"></a>Captures instantanées cloud StorSimple

Les captures instantanées cloud StorSimple protègent les données qui se trouvent sur l’appareil StorSimple. Ceci équivaut à l’envoi de bandes vers une installation hors site. En cas d’utilisation du stockage géoredondant Azure (GRS), cette approche équivaut à l’envoi de bandes vers plusieurs sites. Si une restauration de l’appareil se révèle nécessaire en cas de sinistre, vous pourrez mettre un autre appareil StorSimple en ligne et procéder à un basculement. Après le basculement, vous serez ainsi en mesure d’accéder aux données (à la vitesse du cloud) à partir de la capture instantanée cloud la plus récente.


La section ci-après indique comment créer un bref script pour déclencher et supprimer des captures instantanées cloud StorSimple au cours du post-traitement de sauvegarde. 

> [!NOTE] 
> Les captures instantanées qui sont créées manuellement ou par programme ne suivent pas la stratégie d’expiration des captures instantanées StorSimple. Ces captures instantanées doivent être supprimées manuellement ou par programme.

### <a name="start-and-delete-cloud-snapshots-with-a-script"></a>Démarrer et supprimer des captures instantanées cloud avec un script

> [!NOTE] 
> Évaluez soigneusement les répercussions sur la conformité et sur la rétention des données avant de supprimer une capture instantanée StorSimple. Pour plus d’informations sur l’exécution d’un script après la sauvegarde, consultez la documentation Veeam.


#### <a name="backup-lifecycle"></a>Cycle de vie de sauvegarde


![Diagramme du cycle de vie de sauvegarde](./media/storsimple-configure-backup-target-using-veeam/backuplifecycle.png)

#### <a name="requirements"></a>Requirements:

-   Le serveur qui exécute le script doit avoir accès au cloud Azure.

-   Le compte d’utilisateur doit disposer des autorisations nécessaires.

-   Une stratégie de sauvegarde StorSimple avec les volumes StorSimple associés doit être configurée mais non activée.

-   Vous devez disposer du nom de la ressource StorSimple, de la clé d’inscription, du nom de l’appareil et de l’ID de la stratégie de sauvegarde.

#### <a name="steps"></a>Étapes :

1.  [Installez Azure PowerShell](/powershell-install-configure/).

2.  [Téléchargez et importez les paramètres de publication et les informations d’abonnement](https://msdn.microsoft.com/library/dn385850.aspx).

3.  Dans le Portail Azure Classic, obtenez le nom de la ressource et la [clé d’inscription pour votre service StorSimple Manager](storsimple-deployment-walkthrough-u2.md#step-2-get-the-service-registration-key).

4.  Sur le serveur qui exécute le script, exécutez Windows PowerShell en tant qu’administrateur. Entrez :

    -   `Get-AzureStorSimpleDeviceBackupPolicy –DeviceName <device name>`

    Notez l’ID de la stratégie de sauvegarde.

5.  Dans Bloc-notes, créez un script Windows PowerShell et enregistrez-le à l’emplacement où vous avez enregistré les paramètres de publication Azure. Par exemple : `C:\\CloudSnapshot\\StorSimpleCloudSnapshot.ps1`.

    Copiez et collez l’extrait de code suivant :

    ```powershell
    Import-AzurePublishSettingsFile "c:\\CloudSnapshot Snapshot\\myAzureSettings.publishsettings"
    Disable-AzureDataCollection
    $ApplianceName = <myStorSimpleApplianceName>
    $RetentionInDays = 20
    $RetentionInDays = -$RetentionInDays
    $Today = Get-Date
    $ExpirationDate = $Today.AddDays($RetentionInDays)
    Select-AzureStorSimpleResource -ResourceName "myResource" –RegistrationKey
    Start-AzureStorSimpleDeviceBackupJob –DeviceName $ApplianceName -BackupType CloudSnapshot -BackupPolicyId <BackupId> -Verbose
    $CompletedSnapshots =@()
    $CompletedSnapshots = Get-AzureStorSimpleDeviceBackup -DeviceName $ApplianceName
    Write-Host "The Expiration date is " $ExpirationDate
    Write-Host

    ForEach ($SnapShot in $CompletedSnapshots)
    {
        $SnapshotStartTimeStamp = $Snapshot.CreatedOn
        if ($SnapshotStartTimeStamp -lt $ExpirationDate)

        {
            $SnapShotInstanceID = $SnapShot.InstanceId
            Write-Host "This snpashotdate was created on " $SnapshotStartTimeStamp.Date.ToShortDateString()
            Write-Host "Instance ID " $SnapShotInstanceID
            Write-Host "This snpashotdate is older and needs to be deleted"
            Write-host "\#\#\#\#\#\#\#\#\#\#\#\#\#\#\#\#\#\#\#\#\#"
            Remove-AzureStorSimpleDeviceBackup -DeviceName $ApplianceName -BackupId $SnapShotInstanceID -Force -Verbose
        }
    }
    ```

6.  Pour ajouter le script à votre travail de sauvegarde, modifiez les options avancées de votre travail Veeam. 

![Paramètres avancés de sauvegarde Veeam, onglet Scripts](./media/storsimple-configure-backup-target-using-veeam/veeamimage22.png)

Nous vous recommandons d’exécuter votre stratégie de sauvegarde de captures instantanées cloud StorSimple à la fin de votre travail de sauvegarde quotidien sous la forme d’un script de post-traitement. Pour plus d’informations sur la façon de sauvegarder et de restaurer votre environnement d’application de sauvegarde pour atteindre vos objectifs RPO/RTO, consultez votre concepteur d’architecture de sauvegarde.

## <a name="storsimple-as-a-restore-source"></a>Utiliser StorSimple comme source de restauration
==============================

Les restaurations à partir d’un appareil StorSimple fonctionnent comme les restaurations effectuées à partir de n’importe quel dispositif de stockage de bloc. Lorsque vous restaurez des données hiérarchisées vers le cloud, les restaurations se produisent à la vitesse du cloud. Pour les données locales, les restaurations sont effectuées à la vitesse du disque local de l’appareil.

Veeam autorise une récupération rapide granulaire au niveau fichier par le biais de StorSimple à l’aide des outils Explorer intégrés dans la console Veeam. Les produits Veeam Explorer vous permettent de récupérer des éléments spécifiques tels que des e-mails, des objets Active Directory ou des éléments SharePoint à partir des sauvegardes. La récupération peut s’effectuer sans aucune interruption des machines virtuelles locales. Vous pouvez également effectuer la récupération jusqu’à une date et heure spécifiques pour Microsoft SQL et les bases de données Oracle. Veeam et StorSimple contribuent à accélérer et à simplifier le processus de récupération au niveau élément à partir d’Azure. Pour plus d’informations sur l’exécution d’une restauration, consultez la documentation Veeam.


- [https://www.veeam.com/microsoft-exchange-recovery.html](https://www.veeam.com/microsoft-exchange-recovery.html)

- [https://www.veeam.com/microsoft-active-directory-explorer.html](https://www.veeam.com/microsoft-active-directory-explorer.html)

- [https://www.veeam.com/microsoft-sql-server-explorer.html](https://www.veeam.com/microsoft-sql-server-explorer.html)

- [https://www.veeam.com/microsoft-sharepoint-recovery-explorer.html](https://www.veeam.com/microsoft-sharepoint-recovery-explorer.html)

- [https://www.veeam.com/oracle-backup-recovery-explorer.html](https://www.veeam.com/oracle-backup-recovery-explorer.html)


## <a name="storsimple-failover-and-disaster-recovery"></a>Basculement et récupération d’urgence StorSimple

> [!NOTE]
> Pour les scénarios relatifs aux cibles de sauvegarde, l’appliance cloud StorSimple n’est pas prise en charge en tant que cible de restauration.

Un sinistre peut se produire pour différentes raisons. Le tableau ci-après répertorie les scénarios de récupération d’urgence courants.


| Scénario                                                                    | Impact                                             | Procédure de récupération                                                                                                                                                                               | Remarques                                                                                                                                                                                                                                                                                                                                                                                                                                    |
|-----------------------------------------------------------------------------|----------------------------------------------------|----------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------|------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------|
| Défaillance d’appareil StorSimple                                               | Les opérations de sauvegarde et de restauration sont interrompues. | Remplacez l’appareil défaillant et effectuez [un basculement et une récupération d’urgence StorSimple](storsimple-device-failover-disaster-recovery.md). | Si une restauration est effectuée immédiatement après la récupération de l’appareil, les jeux de données de travail complets sont récupérés sur le nouvel appareil à partir du cloud. Par conséquent, toutes les opérations sont exécutées à la vitesse du cloud. En outre, la nouvelle analyse des index et catalogues peut entraîner l’analyse et l’extraction de tous les jeux de données de sauvegarde du niveau cloud vers le niveau local de l’appareil. Ce processus peut donc prendre un certain temps.                                 |
| Défaillance du serveur Veeam                                                     | Les opérations de sauvegarde et de restauration sont interrompues. | Régénérez le serveur de sauvegarde et effectuez la restauration de base de données en suivant les instructions du [Centre d’aide Veeam (documentation technique)](https://www.veeam.com/documentation-guides-datasheets.html).     | Le serveur Veeam doit être régénéré ou restauré sur le site de récupération d’urgence. Restaurez la base de données à partir du point de défaillance le plus récent. Si la base de données Veeam restaurée n’est pas synchronisée avec vos derniers travaux de sauvegarde, vous devez procéder à une indexation et à un catalogage. Cette nouvelle analyse des index et catalogues peut entraîner l’analyse et l’extraction de tous les jeux de données de sauvegarde du niveau cloud vers un niveau d’appareil local. Ce processus peut donc prendre un certain temps. |
| Défaillance du site entraînant la perte du serveur de sauvegarde et de l’appareil StorSimple | Les opérations de sauvegarde et de restauration sont interrompues. | Commencez par restaurer StorSimple, puis restaurez Veeam.                                                                                                                                                  | Commencez par restaurer StorSimple, puis restaurez Veeam. Si vous devez procéder à une restauration après la récupération de l’appareil, les jeux de données de travail complets sont récupérés sur le nouvel appareil à partir du cloud. Par conséquent, toutes les opérations sont exécutées à la vitesse du cloud.                                                                                                                                                                            |


## <a name="references"></a>Références

Les documents référencés dans cet article sont les suivants :

- [Configuration de Multipath I/O pour votre appareil StorSimple](storsimple-configure-mpio-windows-server.md)

- [Storage scenarios: Thin provisioning (Scénarios de stockage : allocation dynamique)](http://msdn.microsoft.com/library/windows/hardware/dn265487.aspx)

- [Using GPT drives (Utilisation de disques de table de partition GUID)](http://msdn.microsoft.com/windows/hardware/gg463524.aspx#EHD)

- [Activer et configurer les clichés instantanés de dossiers partagés](http://technet.microsoft.com/library/cc771893.aspx)



<!--HONumber=Dec16_HO2-->


