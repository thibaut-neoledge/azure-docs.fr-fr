---
title: "Planifier la capacité et la mise à l’échelle de la réplication VMware vers Azure avec Azure Site Recovery | Microsoft Docs"
description: "Utilisez cet article pour planifier la capacité et la mise à l’échelle lors de la réplication des machines virtuelles VMware vers Azure avec Azure Site Recovery"
services: site-recovery
documentationcenter: 
author: rayne-wiselman
manager: carmonm
editor: 
ms.assetid: 0a1cd8eb-a8f7-4228-ab84-9449e0b2887b
ms.service: site-recovery
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: storage-backup-recovery
ms.date: 06/27/2017
ms.author: rayne
ms.translationtype: Human Translation
ms.sourcegitcommit: 138f04f8e9f0a9a4f71e43e73593b03386e7e5a9
ms.openlocfilehash: f5b334e594e3d002e1862b25c4faba7163efa7d4
ms.contentlocale: fr-fr
ms.lasthandoff: 06/29/2017

---
# <a name="step-3-plan-capacity-and-scaling-for-vmware-to-azure-replication"></a>Étape 3 : Planifier la capacité et la mise à l’échelle pour la réplication de VMware vers Azure

Utilisez cet article pour déterminer la planification de la capacité et de la mise à l’échelle lors de la réplication de serveurs physiques et de machines virtuelles VMware locales dans Azure avec [Azure Site Recovery](site-recovery-overview.md).

Publiez des commentaires et des questions au bas de cet article, ou sur le [Forum Azure Recovery Services](https://social.msdn.microsoft.com/forums/azure/home?forum=hypervrecovmgr).

## <a name="how-do-i-start-capacity-planning"></a>Comment dois-je commencer la planification de la capacité ?

Collectez des informations sur l’environnement de réplication, puis planifiez la capacité en tenant compte de ces informations et de celles présentes dans l’article.


## <a name="gather-information"></a>Collecter des informations

1. Télécharger [l’outil Deployment Planner](https://aka.ms/asr-deployment-planner) pour la réplication de VMware.
2. [Lisez cet article](site-recovery-deployment-planner.md) pour savoir comment exécuter l’outil.
3. Cet outil vous donnera des informations sur la compatibilité des machines virtuelles, le nombre de disques par machines virtuelles et l’activité des données par disque. Il couvre également les exigences en bande passante du réseau et l’infrastructure Azure requise pour la réussite de la réplication et du test de basculement.

## <a name="replication-considerations"></a>Informations relatives à la réplication

Prenez connaissance de ces informations avant de commencer le déploiement :

**Composant** | **Détails** |
--- | --- | ---
**Réplication** | **Taux de modification par jour maximal** : une machine protégée ne peut utiliser qu’un serveur de processus, et un seul serveur de processus peut gérer un taux de modification de 2 To par jour au maximum. Par conséquent, 2 To est le taux de modification quotidien maximal de données pris en charge pour une machine protégée.<br/><br/> **Débit maximum** : un ordinateur répliqué peut appartenir à un compte de stockage dans Azure. Un compte de stockage standard peut gérer un maximum de 20 000 requêtes par seconde, et nous vous recommandons de conserver le nombre d’opérations d’entrée/sortie par seconde (IOPS) sur un ordinateur source à 20 000. Par exemple, si vous disposez d’une machine source à 5 disques et si chaque disque génère 120 IOPS (taille de 8 Ko) sur la machine source, la limite de 500 IOPS sur le disque Azure est respectée. (Le nombre de comptes de stockage requis est égal au nombre d’E/S par seconde total de la machine source divisé par 20 000).

## <a name="configuration-server-capacity"></a>Capacité du serveur de configuration

Le serveur de configuration doit être en mesure de gérer le taux de modification quotidien pour toutes les charges de travail en cours d’exécution sur des machines protégées, et il a besoin d’une bande passante suffisante pour permettre la réplication continue des données sur Azure Storage.

À titre de meilleure pratique, placez le serveur de configuration sur le même réseau et le même segment de réseau local que les machines à protéger. Il peut se trouver sur un réseau différent, mais les ordinateurs à protéger doivent avoir une visibilité de réseau de couche 3.

## <a name="sizing-recommendations"></a>Recommandations de dimensionnement

Le tableau indique les recommandations de dimensionnement selon le processeur.

**UC** | **Mémoire** | **Taille du disque cache** | **Taux de modification des données** | **Machines protégées**
--- | --- | --- | --- | ---
8 processeurs virtuels (2 sockets * 4 cœurs à 2,5 gigahertz [GHz]) | 16 Go | 300 Go | 500 Go ou moins | Répliquez moins de 100 machines.
12 processeurs virtuels (2 sockets * 6 cœurs à 2,5 GHz) | 18 Go | 600 Go | 500 Go à 1 To | Répliquez entre 100 et 150 machines.
16 processeurs virtuels (2 sockets * 8 cœurs à 2,5 GHz) | 32 Go | 1 To | 1 To à 2 To | Répliquez entre 150 et 200 machines.
Déployer un autre serveur de traitement | | | > 2 To | Déployez des serveurs de traitement supplémentaires si vous effectuez la réplication de plus de 200 ordinateurs, ou si le taux de changement des données quotidien dépasse 2 To.

Où :

* Chaque ordinateur source est configuré avec 3 disques de 100 Go chacune.
* Nous avons utilisé le stockage de référence de 8 disques SAP de 10 K tr/min avec RAID 10 pour les mesures de disque cache.

## <a name="process-server-capacity"></a>Capacité du serveur de processus


le serveur de processus reçoit les données de réplication des machines protégées et les optimise grâce à la mise en cache, la compression et le chiffrement avant de les transmettre à Azure. Les données sont ensuite envoyées vers Azure.

- Le serveur de processus doit disposer de ressources suffisantes pour effectuer ces tâches.
- Le premier serveur de processus est installé par défaut sur le serveur de configuration. Vous pouvez déployer des serveurs de processus supplémentaires pour étendre votre environnement.
- Le serveur de processus utilise un cache disque. Utilisez un disque de cache distinct de 600 Go ou plus pour gérer les modifications apportées aux données stockées en cas de goulot d’étranglement ou de panne.
- Si vous souhaitez protéger plus de 200 machines ou si le taux de modification quotidien est supérieur à 2 To, vous pouvez ajouter des serveurs de processus pour gérer la charge de réplication. Pour augmenter la taille des instances, vous pouvez :
    - Augmentez le nombre de serveurs de configuration. Par exemple, vous pouvez protéger jusqu’à 400 machines avec deux serveurs de configuration.
    - Ajoutez d’autres serveurs de processus et utilisez-les pour gérer le trafic à la place (ou en plus) du serveur de configuration.


### <a name="example-process-server-scaling"></a>Exemple de mise à l’échelle du serveur de processus

Le tableau suivant décrit un scénario dans lequel :

* Vous n’envisagez pas d’utiliser le serveur de configuration en tant que serveur de processus.
* Vous avez configuré un serveur de processus supplémentaire.
* Vous avez configuré des machines virtuelles protégées pour utiliser le serveur de processus supplémentaire.
* Chaque ordinateur source protégé est configuré avec trois disques de 100 Go chacun.

**Serveur de configuration** | **Serveur de traitement supplémentaire** | **Taille du disque cache** | **Taux de modification des données** | **Machines protégées**
--- | --- | --- | --- | ---
8 processeurs virtuels (2 sockets * 4 cœurs @ 2,5 GHz), 16 Go de mémoire | 4 processeurs virtuels (2 sockets * 2 cœurs @ 2,5 GHz), 8 Go de mémoire | 300 Go | 250 Go ou moins | Répliquez 85 machines ou moins.
8 processeurs virtuels (2 sockets * 4 cœurs @ 2,5 GHz), 16 Go de mémoire | 8 processeurs virtuels (2 sockets * 4 cœurs @ 2,5 GHz), 12 Go de mémoire | 600 Go | 250 Go à 1 To | Répliquez entre 85 et 150 machines.
12 processeurs virtuels (2 sockets * 6 cœurs @ 2,5 GHz), 18 Go de mémoire | 12 processeurs virtuels (2 sockets * 6 cœurs @ 2,5 GHz), 24 Go de mémoire | 1 To | 1 To à 2 To | Répliquez entre 150 et 225 machines.

La façon dont vous allez mettre à niveau vos serveurs dépend de votre préférence pour un modèle de type Scale up ou Scale out.  Vous pouvez aboutir à une montée en puissance avec le développement de serveurs de configuration et de processus haut de gamme, ou à une montée en charge avec le déploiement d’un nombre de serveurs supérieur avec moins de ressources. Par exemple, si vous avez besoin de protéger les 220 machines, vous pouvez effectuer des opérations suivantes :

* Configurez le serveur de configuration avec 12 processeurs virtuels, 18 Go de mémoire et un serveur de traitement supplémentaire avec 12 processeurs virtuels, 24 Go de mémoire. Configurer les machines protégées de sorte qu’elles utilisent le serveur de traitement supplémentaire uniquement.
* Configurez deux serveurs de configuration (2 x 8 processeurs virtuels, 16 Go de RAM) et deux serveurs de traitement supplémentaires (1 x 8 processeurs virtuels et 4 processeurs virtuels x 1 pour gérer les 135 + 85 (220) machines). Configurer les machines protégées de sorte qu’elles utilisent les serveurs de traitement supplémentaires uniquement.

## <a name="deploy-additional-process-servers"></a>Déployer d’autres serveurs de traitement

Suivez ces instructions pour configurer un serveur de processus supplémentaire. Après avoir configuré le serveur, vous pouvez migrer les ordinateurs source pour les utiliser.

1. Dans **Serveurs Site Recovery**, cliquez sur le serveur de configuration > **+ Serveur de processus**.
2. Dans **Type de serveur** cliquez sur **Serveur de processus (local)**.

    ![Serveur de traitement](./media/vmware-walkthrough-capacity/migrate-ps2.png)
3. Téléchargez le fichier d’installation unifiée de Site Recovery.
4. Exécutez le programme pour installer le serveur de processus et l’enregistrer dans le coffre.
5. Dans la zone **Avant de commencer**, sélectionnez **l’ajout de serveurs de traitement supplémentaires pour effectuer un déploiement avec montée en puissance**.
6. Dans **Détails du serveur de configuration**, spécifiez l’adresse IP du serveur de configuration et la phrase secrète. Si vous n’avez pas la phrase secrète, exécutez la commande suivante : **[SiteRecoveryInstallationFolder]\home\sysystems\bin\genpassphrase.exe –n** sur le serveur de configuration.

    ![Serveur de configuration](./media/vmware-walkthrough-capacity/add-ps2.png)
7. Terminez l’installation comme vous l’avez fait lors de l’installation du serveur de configuration.

### <a name="migrate-machines-to-use-the-process-server"></a>Migrez les machines pour utiliser le serveur de processus

1. Dans **Paramètres** > **Serveurs Site Recovery**, cliquez sur le serveur de configuration, puis sur **Serveurs de processus**.
2. Cliquez avec le bouton droit sur le serveur de processus en cours d’utilisation, puis sur **Basculer**.

    ![Basculer serveur de processus](./media/vmware-walkthrough-capacity/migrate-ps3.png)
3. Dans **Sélectionner un serveur de traitement cible**, sélectionnez le serveur de processus à utiliser, puis les machines virtuelles que le serveur va gérer.
4. Cliquez sur l’icône Informations. Pour vous aider à prendre des décisions quant à la charge, l’espace moyen nécessaire à la réplication de chaque machine virtuelle sélectionnée vers le nouveau serveur de processus s’affiche.
5. Cliquez sur la coche pour commencer la réplication vers le nouveau serveur de processus.

## <a name="control-network-bandwidth"></a>Contrôler la bande passante réseau

Après avoir exécuté [l’outil Deployment Planner](site-recovery-deployment-planner.md) pour calculer la bande passante nécessaire à la réplication (réplication initiale et delta), vous pouvez contrôler la quantité de bande passante utilisée pour la réplication à l’aide de ces deux options :

* **Limiter la bande passante**: le trafic VMware qui est répliqué sur Azure passe par un serveur de processus spécifique. Vous pouvez limiter la bande passante sur les machines qui s’exécutent en tant que serveurs de processus.
* **Influer sur la bande passante** : vous pouvez influer sur la bande passante utilisée pour la réplication à l’aide de quelques clés de Registre :
  * La valeur de Registre **HKEY_LOCAL_MACHINE\SOFTWARE\Microsoft\Windows Azure Backup\UploadThreadsPerVM** détermine le nombre de threads utilisés pour le transfert des données (réplication initiale ou différentielle) sur un disque. Une valeur plus élevée permet d’augmenter la bande passante réseau utilisée pour la réplication.
  * La valeur de Registre **HKEY_LOCAL_MACHINE\SOFTWARE\Microsoft\Windows Azure Backup\DownloadThreadsPerVM** détermine le nombre de threads utilisés pour le transfert des données pendant la restauration automatique.

### <a name="throttle-bandwidth"></a>Limiter la bande passante

1. Ouvrez le composant logiciel enfichable MMC Azure Backup sur la machine qui fait office de serveur de processus. Par défaut, un raccourci vers Backup est créé sur le Bureau, ou dans le dossier suivant : C:\Program Files\Microsoft Azure Recovery Services Agent\bin\wabadmin.
2. Dans le composant logiciel enfichable, cliquez sur **Modifier les propriétés**.
3. Sous l’onglet **Limitation**, sélectionnez la case **Activer la limitation de la bande passante sur Internet pour les opérations de sauvegarde**.
4. Définissez les limites pour les heures ouvrées et non ouvrées. Les plages valides vont de 512 Kbits/s à 102 Mbits/s par seconde.

    ![Limitation](./media/vmware-walkthrough-capacity/throttle2.png)

Vous pouvez également utiliser l’applet de commande [Set-OBMachineSetting](https://technet.microsoft.com/library/hh770409.aspx) pour définir la limitation. Voici un exemple :

    $mon = [System.DayOfWeek]::Monday
    $tue = [System.DayOfWeek]::Tuesday
    Set-OBMachineSetting -WorkDay $mon, $tue -StartWorkHour "9:00:00" -EndWorkHour "18:00:00" -WorkHourBandwidth  (512*1024) -NonWorkHourBandwidth (2048*1024)

**Set-OBMachineSetting -NoThrottle** indique qu’aucune limitation n’est requise.

### <a name="influence-network-bandwidth-for-a-vm"></a>Influer sur la bande passante réseau d’une machine virtuelle

1. Dans le registre de la machine virtuelle, accédez à **HKEY_LOCAL_MACHINE\SOFTWARE\Microsoft\Windows Azure Backup\Replication**.
   * Pour influer sur le trafic de la bande passante sur un disque de réplication, modifiez la valeur du paramètre **UploadThreadsPerVM**, ou créez la clé si elle n’existe pas.
   * Pour influer sur la bande passante utilisée pour le trafic lié à la restauration automatique à partir d’Azure, modifiez la valeur du paramètre **DownloadThreadsPerVM**.
2. La valeur par défaut est 4. Dans un réseau surutilisé, ces clés de registre doivent être modifiées. La valeur maximale est de 32. Surveillez le trafic pour optimiser la valeur.




## <a name="next-steps"></a>Étapes suivantes

Allez à [Étape 4 : planifier la mise en réseau](vmware-walkthrough-network.md).

