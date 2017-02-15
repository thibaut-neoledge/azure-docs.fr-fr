---
title: Configurer StorSimple avec Veritas Backup Exec | Microsoft Docs
description: "Décrit la configuration de la cible de sauvegarde StorSimple avec Veritas Backup Exec."
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
ms.date: 12/05/2016
ms.author: hkanna
translationtype: Human Translation
ms.sourcegitcommit: b7c6a9ffb2c598048bf0547420b1e7afac2c3e74
ms.openlocfilehash: 7a2fa4ccac6b2eda935477034c5f58a0ed90da37

---

# <a name="configure-storsimple-with-veritas-backup-exec8482"></a>Configurer StorSimple avec Veritas Backup Exec&#8482;

## <a name="overview"></a>Vue d’ensemble

Microsoft Azure StorSimple est une solution de stockage cloud hybride qui s’attaque à la complexité de la croissance exponentielle des données. Cette solution utilise un compte de stockage Azure en tant qu’extension de la solution locale et hiérarchise automatiquement les données sur le stockage local et sur le stockage cloud.

Cet article décrit l’intégration de StorSimple à Veritas Backup Exec, ainsi que les meilleures pratiques en matière d’intégration de ces deux solutions. Nous fournissons également diverses recommandations concernant la procédure de configuration de Veritas Backup Exec de manière à optimiser son intégration à StorSimple. Nous nous reposons sur les meilleures pratiques, concepteurs d’architecture de sauvegarde et administrateurs de Veritas quant à la meilleure manière de configurer Veritas Backup Exec pour répondre à vos besoins de sauvegarde et à vos contrats de niveau de service.

Cet article illustre les étapes de configuration et les concepts clés, mais ne constitue en aucun cas un guide de configuration ou d’installation pas à pas. Cet article repose sur l’hypothèse que l’infrastructure et les composants de base sont en état de fonctionnement et prêts à prendre en charge les concepts que nous décrivons.

## <a name="why-storsimple-as-a-backup-target"></a>Pourquoi utiliser StorSimple comme cible de sauvegarde ?

La solution StorSimple constitue une cible de sauvegarde idéale pour les raisons suivantes :

-   Elle offre un stockage local standard utilisable par les applications de sauvegarde sans aucune modification pour fournir une destination de sauvegarde rapide. StorSimple est également disponible pour les restaurations rapides des sauvegardes récentes.

-   Sa hiérarchisation cloud s’intègre de façon transparente à un stockage cloud Microsoft Azure économique.

-   StorSimple fournit automatiquement un stockage hors site pour la récupération d’urgence.


## <a name="target-audience"></a>Public cible

Cet article s’adresse aux administrateurs de sauvegarde, aux administrateurs de stockage et aux concepteurs d’architecture de stockage qui disposent de connaissances concernant le stockage, Windows Server 2012 R2, Ethernet, les services cloud et Veritas Backup Exec.

## <a name="supported-versions"></a>Versions prises en charge

Pour obtenir la liste complète des versions prises en charge, cliquez sur les liens suivants :

-   [Backup Exec versions 16 et ultérieures](http://backupexec.com/compatibility).

-   [StorSimple Update 3 et versions ultérieures](storsimple-overview.md#storsimple-workload-summary).

## <a name="key-concepts"></a>Concepts clés

Comme dans le cas de toute autre solution de stockage, une évaluation en profondeur des performances de stockage de la solution, des contrats de niveau de service, de la fréquence de changement et de l’augmentation des besoins en matière de capacité se révèle un facteur décisif de réussite. L’idée générale consiste à introduire un niveau cloud, de sorte que les temps d’accès et débits que vous offre le cloud joueront un rôle fondamental dans l’efficacité de StorSimple.

StorSimple est conçu pour fournir une capacité de stockage aux applications qui opèrent sur un jeu de données de travail (données chaudes) bien défini. Dans ce modèle, le jeu de données de travail est stocké sur les niveaux locaux, tandis que les données inactives/froides/archivées sont hiérarchisées vers le cloud. Ce modèle est illustré dans la figure ci-après. La ligne verte quasiment plate représente les données stockées sur les niveaux locaux de l’appareil StorSimple. La ligne rouge représente la quantité totale de données stockées sur tous les niveaux de la solution StorSimple. L’espace entre la ligne verte plate et la courbe exponentielle rouge représente la quantité totale de données stockées dans le cloud.

**Hiérarchisation StorSimple**
![Diagramme de la hiérarchisation Storsimple](./media/storsimple-configure-backup-target-using-backup-exec/image1.jpg)

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


| Capacité de stockage       | 8100          | 8600            |
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

![Diagramme logique sur l’utilisation de StorSimple comme cible de sauvegarde principale](./media/storsimple-configure-backup-target-using-backup-exec/primarybackuptargetlogicaldiagram.png)

### <a name="primary-target-backup-logical-steps"></a>Procédure logique de sauvegarde de la cible principale

1.  Le serveur de sauvegarde contacte l’agent de sauvegarde cible qui transmet alors des données au serveur de sauvegarde.

2.  Le serveur de sauvegarde écrit les données sur les volumes hiérarchisés StorSimple.

3.  Le serveur de sauvegarde met à jour la base de données de catalogues et accomplit le travail de sauvegarde.

4.  Le script de capture instantanée déclenche la gestion des captures instantanées cloud de StorSimple (démarrage-suppression).

5.  Conformément à une stratégie de rétention, le serveur de sauvegarde supprime les sauvegardes arrivées à expiration.

###  <a name="primary-target-restore-logical-steps"></a>Procédure logique de restauration de la cible principale

1.  Le serveur de sauvegarde commence à restaurer les données appropriées du référentiel de stockage.

2.  L’agent de sauvegarde reçoit les données en provenance du serveur de sauvegarde.

3.  Le serveur de sauvegarde achève le travail de restauration.

## <a name="storsimple-as-a-secondary-backup-target"></a>Utiliser StorSimple comme cible de sauvegarde secondaire

Dans ce scénario, les volumes StorSimple sont principalement utilisés à des fins de rétention à long terme ou d’archivage.

La figure ci-après illustre l’architecture dans laquelle les sauvegardes et restaurations initiales ciblent un volume hautes performances. Ces sauvegardes sont copiées et archivées sur un volume hiérarchisé StorSimple conformément à une planification donnée.

Il est important que vous dotiez votre volume hautes performances de suffisamment d’espace et de performances pour lui permettre de prendre en charge les exigences en matière de performances et de capacité de stratégie de rétention.

![Diagramme logique de l’utilisation de StorSimple comme cible de sauvegarde secondaire](./media/storsimple-configure-backup-target-using-backup-exec/secondarybackuptargetlogicaldiagram.png)

### <a name="secondary-target-backup-logical-steps"></a>Procédure logique de sauvegarde de la cible secondaire

1.  Le serveur de sauvegarde contacte l’agent de sauvegarde cible qui transmet alors des données au serveur de sauvegarde.

2.  Le serveur de sauvegarde écrit les données dans le stockage hautes performances.

3.  Le serveur de sauvegarde met à jour la base de données de catalogues et accomplit le travail de sauvegarde.

4.  Conformément à une stratégie de rétention, le serveur de sauvegarde copie les sauvegardes dans StorSimple.

5.  Le script de capture instantanée déclenche la gestion des captures instantanées cloud de StorSimple (démarrage-suppression).

6.  Conformément à une stratégie de rétention, le serveur de sauvegarde supprime les sauvegardes arrivées à expiration.

### <a name="secondary-target-restore-logical-steps"></a>Procédure logique de restauration de la cible secondaire

1.  Le serveur de sauvegarde commence à restaurer les données appropriées du référentiel de stockage.

2.  L’agent de sauvegarde reçoit les données en provenance du serveur de sauvegarde.

3.  Le serveur de sauvegarde achève le travail de restauration.

## <a name="deploy-the-solution"></a>Déployer la solution

La procédure de déploiement de cette solution comporte trois étapes : préparation de l’infrastructure réseau, déploiement de votre appareil StorSimple comme cible de sauvegarde, puis déploiement de Veritas Backup Exec. Chacune de ces étapes est abordée en détail dans les sections suivantes.

### <a name="configure-the-network"></a>configurer le réseau.

En tant que solution intégrée au cloud Azure, StorSimple requiert une connexion active et opérationnelle à ce dernier. Cette connexion est utilisée pour des opérations telles que les captures instantanées cloud, la gestion, le transfert de métadonnées et la hiérarchisation des données les plus anciennes et les moins sollicitées vers le stockage cloud Azure.

Pour que la solution fonctionne de manière optimale, nous vous recommandons d’appliquer les meilleures pratiques de mise en réseau suivantes :

-   La liaison qui connecte la hiérarchisation StorSimple à Azure doit répondre à vos exigences de bande passante en appliquant la Qualité de service (QoS) appropriée aux commutateurs de votre infrastructure conformément à vos contrats de niveau de service en matière d’objectifs de point de récupération (RPO) et de temps de récupération (RTO).

-   Les latences d’accès maximales au service Stockage Blob Azure doivent être de l’ordre de 80 ms.

### <a name="deploy-storsimple"></a>Déployer StorSimple

Pour découvrir un guide de déploiement de StorSimple pas à pas, consultez l’article [Déploiement de votre appareil StorSimple local](storsimple-deployment-walkthrough-u2.md).

### <a name="deploy-veritas-backup-exec"></a>Déployer Veritas Backup Exec

Pour découvrir les meilleures pratiques en matière d’installation de Veritas Backup Exec, consultez l’article [Best practices for Backup Exec installation (Meilleures pratiques en matière d’installation de Backup Exec)](https://www.veritas.com/support/en_US/article.000068207).

## <a name="configure-the-solution"></a>Configurer la solution

Dans cette section, nous fournissons quelques exemples de configuration. Les exemples/recommandations ci-après illustrent l’implémentation la plus élémentaire et fondamentale. Cette implémentation peut ne pas s’appliquer directement à vos exigences spécifiques en matière de sauvegarde.

### <a name="configure-storsimple"></a>Configurer StorSimple

| Tâches de déploiement StorSimple                                                                                                                 | Commentaires supplémentaires                                                                                                                                                                                                                                                                                      |
|---------------------------------------------------------------------------------------------------------------------------------------------|----------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------|
| Déploiement de votre appareil StorSimple local.                                                                                                | Version prise en charge : Update 3 et versions ultérieures.                                                                                                                                                                                                                                                                 |
| Activation du mode de cible de sauvegarde.                                                                                                                   | Utilisez les commandes ci-après pour activer ou désactiver le mode de cible de sauvegarde et obtenir le statut. Pour plus d’informations, consultez l’article [Connexion à distance à votre appareil StorSimple](storsimple-remote-connect.md).</br> Pour activer le mode de sauvegarde :`Set-HCSBackupApplianceMode -enable`</br>  Pour désactiver le mode de sauvegarde :`Set-HCSBackupApplianceMode -disable`</br> Pour obtenir l’état actuel des paramètres de mode de sauvegarde :`Get-HCSBackupApplianceMode` |
| Création d’un conteneur de volumes commun pour votre volume qui stocke les données de sauvegarde.   Toutes les données d’un conteneur de volumes sont dédupliquées. | Les conteneurs de volumes StorSimple définissent les domaines de déduplication.                                                                                                                                                                                                                                             |
| Création de volumes StorSimple.                                                                                                                 | Créez des volumes en les dimensionnant le plus conformément possible à l’usage que vous prévoyez d’en faire, car la taille du volume a une incidence sur la durée des captures instantanées cloud. Pour plus d’informations sur le dimensionnement d’un volume, consultez la section [Stratégies de rétention](#retention-policies).</br> </br> Utilisez les volumes hiérarchisés StorSimple et sélectionnez l’option **Utiliser ce volume pour les données d’archivage auxquelles vous accédez moins souvent**. </br> Les volumes qui sont uniquement attachés localement ne sont pas pris en charge.|
| Création d’une stratégie de sauvegarde StorSimple unique pour tous les volumes cibles de sauvegarde.                                                               | Une stratégie de sauvegarde StorSimple définit le groupe de cohérence de volume.                                                                                                                                                                                                                                       |
| Désactivation de la planification lorsque les captures instantanées arrivent à expiration.                                                                                                    | Les captures instantanées sont déclenchées sous la forme d’une opération post-traitement.                                                                                                                                                                                                                                                         |
|                                                                                                     |                                             |




### <a name="configure-host-backup-server-storage"></a>Configurer le stockage du serveur de sauvegarde hôte

Vérifiez que le stockage du serveur de sauvegarde hôte est configuré conformément aux instructions suivantes :  

- Les volumes fractionnés (créés par le Gestionnaire de disque Windows) ne sont pas pris en charge.
- Formatez vos volumes à l’aide du système de fichiers NTFS avec une taille d’allocation de 64 Ko.
- Mappez les volumes StorSimple directement sur le serveur Backup Exec.
    - Utilisez iSCSI dans le cas de serveurs physiques.
    - Utilisez des disques pass-through pour les serveurs virtuels.


## <a name="best-practices-for-storsimple-and-veritas-backup-exec"></a>Meilleures pratiques pour StorSimple et Veritas Backup Exec

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

### <a name="veritas-backup-exec"></a>Veritas Backup Exec

-   Veritas Backup Exec doit être installé sur un lecteur local du serveur et non sur un volume StorSimple.

-   Définissez les **opérations d’écriture simultanées** de stockage Veritas Backup Exec sur la valeur maximale autorisée.

    -   La **taille des blocs et de la mémoire tampon** de stockage Veritas Backup Exec doit être définie sur 512 Ko.

    -   Les **opérations de lecture et d’écriture en mémoire tampon** de stockage Veritas Backup Exec doivent être activées.

-   Les sauvegardes complètes et incrémentielles de Veritas Backup Exec sont prises en charge, alors que les sauvegardes différentielles et synthétiques sont déconseillées.

-   Les fichiers de données de sauvegarde doivent uniquement contenir les données d’un travail spécifique. Par exemple, aucun ajout de supports n’est autorisé entre les différents travaux.

-   Désactivez la vérification des travaux. Si nécessaire, la vérification doit être planifiée après le dernier travail de sauvegarde. Il est important de comprendre que ce travail a une incidence sur votre fenêtre de sauvegarde.

-   Dans **Storage > Your disk > Details > Properties (Stockage > votre disque > Détails > Propriétés)**, désactivez l’option **Pre-allocate disk space (Pré-allouer de l’espace disque)**.

Pour découvrir les derniers paramètres et meilleures pratiques Veritas Backup Exec en matière d’implémentation de ces exigences, consultez la documentation Veritas Backup Exec en accédant au site [www.veritas.com](https://www.veritas.com).

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

## <a name="configure-veritas-backup-exec-storage"></a>Configurer le stockage Veritas Backup Exec

1.  Dans la console d’administration de Veritas Backup Exec, sélectionnez **Storage (Stockage) &gt; Configure Storage (Configurer le stockage) &gt; Disk-Based Storage (Stockage sur disque)**. Cliquez sur **Next (Suivant)&gt;**.

    ![Console d’administration de Veritas Backup Exec, écran de configuration du stockage](./media/storsimple-configure-backup-target-using-backup-exec/image4.png)

1.  Cliquez sur **Disk Storage (Stockage sur disque)**, puis passez à la page suivante.

    ![Console d’administration de Veritas Backup Exec, écran de sélection du stockage](./media/storsimple-configure-backup-target-using-backup-exec/image5.png)

1.  Entrez un nom explicite, par exemple, Samedi complète, ainsi qu’une description associée. Passez à la page suivante.

    ![Console d’administration de Veritas Backup Exec, écran de saisie d’un nom et d’une description](./media/storsimple-configure-backup-target-using-backup-exec/image7.png)

1.  Sélectionnez le disque approprié, puis cliquez sur **Next (Suivant) &gt;**. Dans notre exemple, nous avons sélectionné le disque D:.

    ![Console d’administration de Veritas Backup Exec, écran de sélection du disque de stockage](./media/storsimple-configure-backup-target-using-backup-exec/image9.png)

1.  Incrémentez le nombre d’opérations d’écriture à 16, puis cliquez sur **Next (Suivant) &gt; **.

    ![Console d’administration de Veritas Backup Exec, écran des paramètres d’opérations d’écriture simultanées ](./media/storsimple-configure-backup-target-using-backup-exec/image10.png)

1.  Vérifiez les paramètres, puis cliquez sur **Finish (Terminer)**.

    ![Console d’administration de Veritas Backup Exec, écran récapitulatif de configuration du stockage](./media/storsimple-configure-backup-target-using-backup-exec/image11.png)

1.  À la fin de chaque affectation, modifiez les paramètres du dispositif de stockage conformément aux paramètres recommandés dans la liste des meilleures pratiques.

    ![Console d’administration de Veritas Backup Exec, écran de paramètres du dispositif de stockage](./media/storsimple-configure-backup-target-using-backup-exec/image12.png)

1.  Répétez toutes les étapes précédentes jusqu’à ce que vous ayez terminé d’affecter vos volumes StorSimple à Veritas Backup Exec.

## <a name="storsimple-as-a-primary-backup-target"></a>Utiliser StorSimple comme cible de sauvegarde principale

> [!NOTE]
> Tenez compte du fait que si vous devez restaurer des données à partir d’une sauvegarde qui a été hiérarchisée vers le cloud, la restauration se produit à la vitesse du cloud.

Dans la figure ci-après, nous illustrons le mappage d’un volume classique sur un travail de sauvegarde. Dans ce cas précis, toutes les sauvegardes hebdomadaires sont mappées sur le disque des sauvegardes complètes effectuées le samedi, et les sauvegardes incrémentielles sont mappées sur les disques des sauvegardes incrémentielles exécutées du lundi au vendredi. Toutes les sauvegardes et restaurations se produisent à partir d’un volume hiérarchisé StorSimple.

![Diagramme logique de la configuration de la cible de sauvegarde principale ](./media/storsimple-configure-backup-target-using-backup-exec/primarybackuptargetdiagram.png)

#### <a name="storsimple-as-a-primary-backup-target-grandfather-father-and-son-gfs-schedule-example"></a>Exemple de planification GFS pour StorSimple utilisé comme cible de sauvegarde principale

| Voici un exemple de planification de rotation GFS pendant 4 semaines, une fois par mois et une fois par an : |               |             |
|--------------------------------------------------------------------------|---------------|-------------|
| Fréquence/type de sauvegarde   | Complet          | Incrémentielle (jours 1 à 5)  |
| Hebdomadaire (semaines 1 à 4)    | Samedi | Lundi à vendredi | 
| Mensuelle     | Samedi  |             |
| Annuelle      | Samedi  |             |


### <a name="assigning-storsimple-volumes-to-a-veritas-backup-exec-backup-job"></a>Affecter des volumes StorSimple à un travail de sauvegarde Veritas Backup Exec

La procédure ci-après repose sur l’hypothèse que Backup Exec et l’hôte cible sont configurés conformément aux instructions de l’agent Backup Exec.

1.  Dans la console d’administration de Veritas Backup Exec, accédez à **Host (Hôte) &gt; Backup (Sauvegarde) &gt; Backup to Disk (Sauvegarde sur disque) &gt;**.

    ![Console d’administration de Veritas Backup Exec, sélection de l’option de sauvegarde sur disque](./media/storsimple-configure-backup-target-using-backup-exec/image14.png)

1.  Dans la fenêtre Backup Definition Properties (Propriétés de définition de sauvegarde), sélectionnez **Edit (Modifier)** (sous Backup (Sauvegarde)).

    ![Fenêtre des propriétés de définition de sauvegarde, sélection de l’option de modification](./media/storsimple-configure-backup-target-using-backup-exec/image15.png)

1.  Configurez vos sauvegardes complètes et incrémentielles conformément à vos exigences en matière de RPO/RTO et aux bonnes pratiques Veritas.

2.  Dans la fenêtre Backup Options (Options de sauvegarde), sélectionnez **Storage (Stockage)**.

    ![Fenêtre des options de sauvegarde, onglet relatif au stockage](./media/storsimple-configure-backup-target-using-backup-exec/image16.png)

1.  Attribuez les volumes StorSimple correspondants à votre planification de sauvegarde.

    > [!NOTE]
    > Les options **Compression (Compression)** et **Encryption type (Type de chiffrement)** sont définies sur **None (Aucun)**.

2.  Sous **Verify (Vérifier)**, cochez la case **Do not verify data for this job (Ne pas vérifier les données de ce travail)**, car cette option risque d’affecter la hiérarchisation StorSimple.

    > [!NOTE]
    > La défragmentation, l’indexation et la vérification en arrière-plan ont une incidence négative sur la hiérarchisation StorSimple.

    ![Paramètres de vérification des options de sauvegarde](./media/storsimple-configure-backup-target-using-backup-exec/image17.png)

1.  Une fois que vous avez configuré le reste de vos options de sauvegarde conformément à vos besoins, sélectionnez **OK (OK)** pour terminer.

## <a name="storsimple-as-a-secondary-backup-target"></a>Utiliser StorSimple comme cible de sauvegarde secondaire

> [!NOTE]
> Tenez compte du fait que si vous devez restaurer des données à partir d’une sauvegarde qui a été hiérarchisée vers le cloud, la restauration se produit à la vitesse du cloud.

Dans ce modèle, vous devez disposer d’un média de stockage (autre que StorSimple) faisant office de cache temporaire. Par exemple, vous pouvez utiliser un volume RAID pour prendre en charge l’espace, les E/S et la bande passante. Nous vous recommandons d’utiliser les valeurs RAID 5, 50 et 10.

Dans la figure ci-après, nous présentons des volumes locaux de rétention à court terme (sur le serveur) et des volumes d’archive de rétention à long terme classiques. Dans ce cas précis, toutes les sauvegardes s’exécutent sur le volume RAID local (sur le serveur). Ces sauvegardes sont régulièrement dupliquées et archivées sur un volume d’archive. Veillez à dimensionner votre volume RAID local (sur le serveur) conformément aux exigences en matière de performances et de capacité de rétention à court terme.

#### <a name="storsimple-as-a-secondary-backup-target-gfs-example"></a>Exemple GFS pour StorSimple utilisé comme cible de sauvegarde secondaire

![Diagramme logique de l’utilisation de StorSimple comme cible de sauvegarde secondaire ](./media/storsimple-configure-backup-target-using-backup-exec/secondarybackuptargetdiagram.png)

Le tableau ci-après illustre la façon dont les sauvegardes doivent être configurées pour s’exécuter sur les disques locaux et StorSimple, et spécifie les exigences de capacité individuelles et totale.

#### <a name="backup-configuration-and-capacity-requirements"></a>Configuration des sauvegardes et exigences de capacité

| Type de sauvegarde et rétention                    |Stockage configuré| Taille (Tio) | Multiplicateur GFS | Capacité totale (Tio)        |
|----------------------------------------------|-----|----------|----------------|------------------------|
| Semaine 1 (sauvegardes complètes et incrémentielles) |Disque local (court terme)| 1        | 1              | 1           |
| StorSimple semaines 2 à 4           |Disque StorSimple (long terme) | 1        | 4              | 4                   |
| Complète mensuelle                                 |Disque StorSimple (long terme) | 1        | 12             | 12                   |
| Complète annuelle                               |Disque StorSimple (long terme) | 1        | 1              | 1                   |
|Exigence en matière de taille des volumes GFS | |          |                | 18*|

\* La capacité totale inclut 17 Tio de disques StorSimple et 1 Tio de volume RAID local.


#### <a name="gfs-example-schedule"></a>Exemple de planification GFS :

|planification de rotation GFS hebdomadaire, mensuelle et annuelle|                    |                   |                   |                   |                   |                   |
|--------------------------------------------------------------------------|--------------------|-------------------|-------------------|-------------------|-------------------|-------------------|
| Fréquence                                                                     | Complet               | Incrémentielle jour 1        | Incrémentielle jour 2        | Incrémentielle jour 3        | Incrémentielle jour 4        | Incrémentielle jour 5        |
| Semaine 1                                                                   | Volume RAID local  | Volume RAID local | Volume RAID local | Volume RAID local | Volume RAID local | Volume RAID local |
| Semaine 2                                                                   | StorSimple semaines 2 à 4 |                   |                   |                   |                   |                   |
| Semaine 3                                                                   | StorSimple semaines 2 à 4 |                   |                   |                   |                   |                   |
| Semaine 4                                                                   | StorSimple semaines 2 à 4 |                   |                   |                   |                   |                   |
| Mensuelle                                                                  | StorSimple mensuelle |                   |                   |                   |                   |                   |
| Annuelle                                                                   | StorSimple annuelle  |                   |                   |                   |                   |                   |


### <a name="assign-storsimple-volumes-to-backup-exec-archivededuplication-job"></a>Attribuer des volumes StorSimple à un travail d’archivage/de déduplication Backup Exec

1.  Une fois le travail de sauvegarde initial configuré pour utiliser le volume RAID en tant que cible de sauvegarde principale, accédez à la console d’administration de Veritas Backup Exec. Sélectionnez le travail que vous souhaitez archiver sur un volume StorSimple, cliquez avec le bouton droit, puis sélectionnez **Backup Definition Properties (Propriétés de définition de sauvegarde)**. Cliquez sur **Modifier**.

    ![Console Backup Exec, onglet des propriétés de définition de sauvegarde](./media/storsimple-configure-backup-target-using-backup-exec/image19.png)

1.  Sélectionnez **Add Stage (Ajouter une étape)**, puis dans la liste déroulante, sélectionnez **Duplicate to Disk (Dupliquer sur disque)**. Cliquez sur **Modifier**.

    ![Console Backup Exec, définitions de sauvegarde, ajout d’une étape](./media/storsimple-configure-backup-target-using-backup-exec/image20.png)

2.  Dans la boîte de dialogue **Duplicate Options (Options de duplication)**, sélectionnez les valeurs appropriées dans les champs **Source (Source)** et **Schedule (Planification)**.

    ![Console Backup Exec, propriétés de définition de sauvegarde, options de duplication](./media/storsimple-configure-backup-target-using-backup-exec/image21.png)

1.  Dans la liste déroulante **Storage (Stockage)**, sélectionnez le volume StorSimple sur lequel vous voulez que le travail d’archivage stocke les données. Dans ce cas précis, la source est définie sur **Full (Complète)**, tandis que la cible est définie sur le volume d’archive complet StorSimple.

    ![Console Backup Exec, propriétés de définition de sauvegarde, options de duplication](./media/storsimple-configure-backup-target-using-backup-exec/image22.png)

1.  Accédez à **Verify (Vérifier)**, puis sélectionnez **Do not verify data for this job (Ne pas vérifier les données de ce travail)**.

    ![Console Backup Exec, propriétés de définition de sauvegarde, options de duplication](./media/storsimple-configure-backup-target-using-backup-exec/image23.png)

1.  Cliquez sur **OK**.

    ![Console Backup Exec, propriétés de définition de sauvegarde, options de duplication](./media/storsimple-configure-backup-target-using-backup-exec/image24.png)

1.  Dans la colonne **Backup (Sauvegarde)**, ajoutez une nouvelle étape. Choisissez une source incrémentielle et, pour la cible, un volume StorSimple sur lequel le travail de sauvegarde incrémentielle est archivé. Répétez les étapes précédentes.

## <a name="storsimple-cloud-snapshots"></a>Captures instantanées cloud StorSimple

Les captures instantanées cloud StorSimple protègent les données qui se trouvent sur l’appareil StorSimple. Ceci équivaut à l’envoi de bandes vers une installation hors site et, en cas d’utilisation du stockage géoredondant Azure (GRS), à l’envoi de bandes vers plusieurs sites. Si une restauration de l’appareil se révèle nécessaire en cas de sinistre, vous pourrez mettre un autre appareil StorSimple en ligne et procéder à un basculement. Après le basculement, vous serez ainsi en mesure d’accéder aux données (à la vitesse du cloud) à partir de la capture instantanée cloud la plus récente.

La section ci-après indique comment créer un bref script pour déclencher et supprimer des captures instantanées cloud StorSimple au cours du post-traitement de sauvegarde.

> [!NOTE]
> Les captures instantanées qui sont créées manuellement ou par programme ne suivent pas la stratégie d’expiration des captures instantanées StorSimple. Elles doivent être supprimées manuellement ou par programme.

### <a name="start-and-delete-cloud-snapshots-with-a-script"></a>Démarrer et supprimer des captures instantanées cloud avec un script

> [!NOTE]
> Évaluez soigneusement les répercussions sur la conformité et sur la rétention des données avant de supprimer une capture instantanée StorSimple. Pour plus d’informations sur l’exécution d’un script après la sauvegarde, consultez la documentation Veritas Backup Exec.

#### <a name="backup-lifecycle"></a>Cycle de vie de sauvegarde

![Diagramme du cycle de vie de sauvegarde](./media/storsimple-configure-backup-target-using-backup-exec/backuplifecycle.png)

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
        Write-Host "This snapshotdate was created on " $SnapshotStartTimeStamp.Date.ToShortDateString()
        Write-Host "Instance ID " $SnapShotInstanceID
        Write-Host "This snpashotdate is older and needs to be deleted"
        Write-host "\#\#\#\#\#\#\#\#\#\#\#\#\#\#\#\#\#\#\#\#\#"
        Remove-AzureStorSimpleDeviceBackup -DeviceName $ApplianceName -BackupId $SnapShotInstanceID -Force -Verbose
        }
    }   
    ```

1.  Ajoutez le script à votre travail de sauvegarde dans Veritas Backup Exec, en modifiant vos commandes de prétraitement et de post-traitement des options de travail Veritas Backup Exec.

    ![Console Backup Exec, options de sauvegarde, onglet de commandes de prétraitement/post-traitement](./media/storsimple-configure-backup-target-using-backup-exec/image25.png)

> [!NOTE]
> Nous vous recommandons d’exécuter votre stratégie de sauvegarde de captures instantanées cloud StorSimple à la fin de votre travail de sauvegarde quotidien sous la forme d’un script de post-traitement. Pour plus d’informations sur la façon de sauvegarder et de restaurer votre environnement d’application de sauvegarde pour atteindre vos objectifs RPO/RTO, consultez votre concepteur d’architecture de sauvegarde.

## <a name="storsimple-as-a-restore-source"></a>Utiliser StorSimple comme source de restauration

Les restaurations à partir d’un appareil StorSimple fonctionnent de la même façon que les restaurations effectuées à partir de n’importe quel dispositif de stockage de bloc. Lorsque vous restaurez des données hiérarchisées vers le cloud, les restaurations se produisent à la vitesse du cloud. Pour les données locales, les restaurations sont effectuées à la vitesse du disque local de l’appareil. Pour plus d’informations sur la façon d’exécuter une restauration, consultez la documentation Veritas Backup Exec et conformez-vous aux meilleures pratiques de restauration de Veritas Backup Exec.

## <a name="storsimple-failover-and-disaster-recovery"></a>Basculement et récupération d’urgence StorSimple

> [!NOTE]
> Pour les scénarios relatifs aux cibles de sauvegarde, l’appliance cloud StorSimple n’est pas prise en charge en tant que cible de restauration.

Un sinistre peut se produire pour différentes raisons. Le tableau ci-après répertorie les scénarios de récupération d’urgence courants.

| Scénario                                                                    | Impact                                             | Procédure de récupération                                                                                                                                                                               | Remarques                                                                                                                                                                                                                                                                                                                                                                                                                                                                  |
|-----------------------------------------------------------------------------|----------------------------------------------------|----------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------|------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------|
| Défaillance d’appareil StorSimple                                                | Les opérations de sauvegarde et de restauration sont interrompues. | Remplacez l’appareil défaillant et effectuez [un basculement et une récupération d’urgence StorSimple](storsimple-device-failover-disaster-recovery.md). | Si une restauration est effectuée immédiatement après la récupération de l’appareil, les jeux de données de travail complets sont récupérés sur le nouvel appareil à partir du cloud. Par conséquent, toutes les opérations sont exécutées à la vitesse du cloud. En outre, la nouvelle analyse des index et catalogues peut entraîner l’analyse et l’extraction de tous les jeux de données de sauvegarde du niveau cloud vers le niveau local de l’appareil. Ce processus peut donc prendre un certain temps.                                                               |
| Défaillance du serveur Veritas Backup Exec                                       | Les opérations de sauvegarde et de restauration sont interrompues. | Régénérez le serveur de sauvegarde et effectuez la restauration de base de données en suivant les instructions de l’article [How to do a manual Backup and Restore of Backup Exec (BEDB) database (Procédure de sauvegarde et de restauration manuelle d’une base de données Backup Exec (BEDB))](http://www.veritas.com/docs/000041083).            | Le serveur Veritas Backup Exec doit être régénéré ou restauré au niveau du site de récupération d’urgence. La base de données doit être restaurée à partir du point de défaillance le plus récent. Si la base de données Veritas Backup Exec restaurée n’est pas synchronisée avec vos derniers travaux de sauvegarde, vous devez procéder à une indexation et à un catalogage. Cette nouvelle analyse des index et catalogues peut entraîner l’analyse et l’extraction de tous les jeux de données de sauvegarde du niveau cloud vers un niveau d’appareil local. Ce processus peut donc prendre un certain temps. |
| Défaillance du site entraînant la perte du serveur de sauvegarde et de l’appareil StorSimple | Les opérations de sauvegarde et de restauration sont interrompues. | Commencez par restaurer StorSimple, puis restaurez Veritas Backup Exec.                                                                                                                                   | Commencez par restaurer StorSimple, puis restaurez Veritas Backup Exec.                                                                Si vous devez procéder à une restauration après la récupération de l’appareil, les jeux de données de travail complets sont récupérés sur le nouvel appareil à partir du cloud. Par conséquent, toutes les opérations sont exécutées à la vitesse du cloud.|


## <a name="references"></a>Références

Les documents référencés dans cet article sont les suivants :

- [Configuration de Multipath I/O pour votre appareil StorSimple](storsimple-configure-mpio-windows-server.md)

- [Storage scenarios: Thin provisioning (Scénarios de stockage : allocation dynamique)](http://msdn.microsoft.com/library/windows/hardware/dn265487.aspx)

- [Using GPT drives (Utilisation de disques de table de partition GUID)](http://msdn.microsoft.com/windows/hardware/gg463524.aspx#EHD)

- [Activer et configurer les clichés instantanés de dossiers partagés](http://technet.microsoft.com/library/cc771893.aspx)

## <a name="next-steps"></a>Étapes suivantes

En savoir plus :

- Découvrez comment effectuer une [restauration à partir d’un jeu de sauvegarde](storsimple-restore-from-backup-set-u2.md).
- Apprenez à effectuer [un basculement et une récupération d’urgence pour un appareil](storsimple-device-failover-disaster-recovery.md).


<!--HONumber=Dec16_HO2-->


