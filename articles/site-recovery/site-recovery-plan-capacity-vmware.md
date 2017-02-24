---
title: "Planifier la capacité et la mise à l’échelle de la réplication VMware dans Azure | Microsoft Docs"
description: "Utilisez cet article pour planifier la capacité et la mise à l’échelle lors de la réplication des machines virtuelles VMware dans Azure"
services: site-recovery
documentationcenter: 
author: rayne-wiselman
manager: jwhit
editor: 
ms.assetid: 0a1cd8eb-a8f7-4228-ab84-9449e0b2887b
ms.service: site-recovery
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: storage-backup-recovery
ms.date: 02/05/2017
ms.author: rayne
translationtype: Human Translation
ms.sourcegitcommit: 6521cada7adeacd98fae46e5119ceffa0351e9b5
ms.openlocfilehash: a5c6759d9826084ae339dd291140f8383b55b6db


---
# <a name="plan-capacity-and-scaling-for-vmware-replication-with-azure-site-recovery"></a>Planifier la capacité et la mise à l’échelle de la réplication VMware avec Azure Site Recovery

Utilisez cet article pour déterminer comment planifier la capacité et la mise à l’échelle lors de la réplication de serveurs physiques et de machines virtuelles VMware locales dans Azure avec [Azure Site Recovery](site-recovery-overview.md).

## <a name="how-do-i-start-capacity-planning"></a>Comment dois-je commencer la planification de la capacité ?

1. Collectez des informations sur votre environnement de réplication à l’aide d’Azure Site Recovery Capacity Planner. Cela inclut des informations sur les machines virtuelles, les disques par machine virtuelle et le stockage par disque.
2. Déterminer le taux de modification (l’évolution) quotidien des données répliquées dans votre environnement.


## <a name="gather-information"></a>Collecter des informations

1. Téléchargez et exécutez [Capacity Planner[(https://gallery.technet.microsoft.com/Azure-Recovery-Capacity-d01dc40e)].
2. [Obtenez des instructions](site-recovery-capacity-planner.md) pour exécuter l’outil.


## <a name="estimate-the-daily-churn-rate"></a>Déterminer le taux d’évolution quotidien

Site Recovery Capacity Planner vous demande d’entrer un taux de modification des données quotidien moyen en pourcentage. Actuellement, vous pouvez collecter ces informations à l’aide de [l’Appliance de planification de la capacité vSphere](https://labs.vmware.com/flings/vsphere-replication-capacity-planning-appliance).

Dans l’outil, vous pouvez calculer le pourcentage en pointant l’outil de planification vSphere vers toutes les machines virtuelles source et obtenir le taux de modification quotidien total. Il s’agit essentiellement du trafic réseau. [En savoir plus](https://blogs.vmware.com/vsphere/2014/04/vsphere-replication-capacity-planning-appliance.html) sur l’exécution de cet outil.


## <a name="capacity-considerations"></a>Considérations relatives à la capacité

**Composant** | **Détails** |
--- | --- | ---
**Réplication** | **Taux de modification par jour maximal**: une machine protégée ne peut utiliser qu’un serveur de processus, et un seul serveur de processus peut gérer un taux de modification de 2 To par jour au maximum. Par conséquent, 2 To est le taux de modification quotidien maximal de données pris en charge pour une machine protégée.<br/><br/> **Débit maximum**: un ordinateur répliqué peut appartenir à un compte de stockage dans Azure. Un compte de stockage standard peut gérer un maximum de 20 000 requêtes par seconde, et nous vous recommandons de conserver le nombre d’IOPS sur un ordinateur source à 20 000. Par exemple, si vous disposez d’une machine source à 5 disques et si chaque disque génère 120 IOPS (taille de 8 Ko) sur la source, la limite de 500 IOPS sur le disque Azure est respectée. Nombre de comptes de stockage requis = nombre total d’IOPS source/20 000.
**Serveur de configuration** | Le serveur de configuration doit être en mesure de gérer le taux de modification quotidien pour toutes les charges de travail en cours d’exécution sur des machines protégées, et il a besoin d’une bande passante suffisante pour permettre la réplication continue des données sur Azure Storage.<br/><br/> À titre de meilleure pratique, nous conseillons de placer le serveur de configuration sur le même réseau et le même segment de réseau local que les machines à protéger. Il peut se trouver sur un réseau différent, mais les ordinateurs à protéger doivent avoir une visibilité de réseau L3.<br/><br/> Les recommandations en matière de taille du serveur de configuration sont résumées dans le tableau qui suit.
**Serveur de traitement** | Le premier serveur de processus est installé par défaut sur le serveur de configuration. Vous pouvez déployer des serveurs de processus supplémentaires pour étendre votre environnement. Notez les points suivants :<br/><br/> le serveur de processus reçoit les données de réplication des machines protégées et les optimise grâce à la mise en cache, la compression et le chiffrement avant de les transmettre à Azure. Le serveur de processus doit disposer de ressources suffisantes pour effectuer ces tâches.<br/><br/> Le serveur de processus utilise le cache disque. Nous recommandons un disque de cache distinct de 600 Go ou plus pour gérer les modifications apportées aux données stockées en cas de goulot d’étranglement ou de panne.

## <a name="size-recommendations-for-the-configuration-server"></a>Recommandations de taille pour le serveur de configuration

**UC** | **Mémoire** | **Taille du disque cache** | **Taux de modification des données** | **Machines protégées**
--- | --- | --- | --- | ---
8 processeurs virtuels (2 sockets * 4 cœurs @ 2,5 GHz) | 16 Go | 300 Go | 500 Go ou moins | Répliquez moins de 100 machines.
12 processeurs virtuels (2 sockets * 6 cœurs @ 2,5 GHz) | 18 Go | 600 Go | 500 Go à 1 To | Répliquez entre 100 et 150 machines.
16 processeurs virtuels (2 sockets * 8 cœurs @ 2,5 GHz) | 32 Go | 1 To | 1 To à 2 To | Répliquez entre 150 et 200 machines.
Déployer un autre serveur de traitement | | | > 2 To | Déployez des serveurs de traitement supplémentaires si vous effectuez la réplication de plus de 200 ordinateurs, ou si le taux de changement des données quotidien dépasse 2 To.

Où :

* Chaque ordinateur source est configuré avec 3 disques de 100 Go chacune.
* Nous avons utilisé le stockage de référence de 8 disques SAP de 10 K tr/min avec RAID 10 pour les mesures de disque cache.

## <a name="size-recommendations-for-the-process-server"></a>Recommandations de taille pour le serveur de processus

Si vous souhaitez protéger plus de 200 machines ou si le taux de modification quotidien est supérieur à 2 To, vous pouvez ajouter des serveurs de processus supplémentaires pour gérer la charge de réplication. Pour augmenter la taille des instances, vous pouvez :

* Augmentez le nombre de serveurs de configuration. Par exemple, vous pouvez protéger jusqu’à 400 machines avec deux serveurs de configuration.
* Ajoutez des serveurs de processus supplémentaires et utilisez-les pour gérer le trafic à la place (ou en plus) du serveur de configuration.

Ce tableau décrit un scénario dans lequel :

* Vous n’envisagez pas d’utiliser le serveur de configuration en tant que serveur de processus.
* Vous avez configuré un serveur de processus supplémentaire.
* Vous avez configuré des machines virtuelles protégées pour utiliser le serveur de processus supplémentaire.
* Chaque ordinateur source protégé est configuré avec trois disques de 100 Go chacun.

**Serveur de configuration** | **Serveur de traitement supplémentaire** | **Taille du disque cache** | **Taux de modification des données** | **Machines protégées**
--- | --- | --- | --- | ---
8 processeurs virtuels (2 sockets * 4 cœurs @ 2,5 GHz), 16 Go de mémoire | 4 processeurs virtuels (2 sockets * 2 cœurs @ 2,5 GHz), 8 Go de mémoire | 300 Go | 250 Go ou moins | Répliquez 85 machines ou moins.
8 processeurs virtuels (2 sockets * 4 cœurs @ 2,5 GHz), 16 Go de mémoire | 8 processeurs virtuels (2 sockets * 4 cœurs @ 2,5 GHz), 12 Go de mémoire | 600 Go | 250 Go à 1 To | Répliquez entre 85 et 150 machines.
12 processeurs virtuels (2 sockets * 6 cœurs @ 2,5 GHz), 18 Go de mémoire | 12 processeurs virtuels (2 sockets * 6 cœurs @ 2,5 GHz), 24 Go de mémoire | 1 To | 1 To à 2 To | Répliquez entre 150 et 225 machines.

La façon dont vous allez mettre à niveau vos serveurs dépend de votre préférence pour un modèle de type Scale up ou Scale out.  Vous pouvez aboutir à une montée en puissance avec le développement de serveurs de configuration et de processus haut de gamme, ou à une montée en charge avec le déploiement d’un nombre de serveurs supérieur avec moins de ressources. Par exemple, si vous avez besoin de protéger les 220 machines, vous pouvez effectuer des opérations suivantes :

* Configurer le serveur de configuration avec 12 processeurs virtuels, 18 Go de mémoire, un serveur de processus supplémentaire comptant 12 processeurs virtuels, 24 Go de mémoire, et configurer des machines protégées pour utiliser uniquement le serveur de processus supplémentaire.
* Vous pouvez également configurer deux serveurs de configuration (2 x 8 processeurs virtuels, 16 Go de RAM) et deux serveurs de processus supplémentaires (1 x 8 processeurs virtuels et 4 processeurs virtuels x 1 pour gérer les machines (220) 135 + 85 machines) et configurer des machines protégées pour utiliser uniquement les serveurs de processus supplémentaires.


## <a name="control-network-bandwidth"></a>Contrôler la bande passante réseau

Vous pouvez utiliser l’outil Capacity Planner pour calculer la bande passante requise par la réplication (réplication initiale, puis delta). Vous disposez de plusieurs options pour déterminer la quantité de bande passante utilisée pour la réplication :

* **Limiter la bande passante**: le trafic VMware qui est répliqué sur Azure passe par un serveur de processus spécifique. Vous pouvez limiter la bande passante sur les machines qui s’exécutent en tant que serveurs de processus.
* **Influer sur la bande passante**: vous pouvez influer sur la bande passante utilisée pour la réplication à l’aide de quelques clés de Registre :
  * La valeur de Registre **HKEY_LOCAL_MACHINE\SOFTWARE\Microsoft\Windows Azure Backup\UploadThreadsPerVM** détermine le nombre de threads utilisés pour le transfert des données (réplication initiale ou différentielle) sur un disque. Une valeur plus élevée permet d’augmenter la bande passante réseau utilisée pour la réplication.
  * La valeur de Registre **HKEY_LOCAL_MACHINE\SOFTWARE\Microsoft\Windows Azure Backup\DownloadThreadsPerVM** détermine le nombre de threads utilisés pour le transfert des données pendant la restauration automatique.

### <a name="throttle-bandwidth"></a>Limiter la bande passante
1. Ouvrez le composant logiciel enfichable MMC Microsoft Azure Backup sur la machine qui fait office de serveur de processus. Par défaut, un raccourci vers Microsoft Azure Backup est créé sur le Bureau. Vous pouvez également le trouver ici : C:\Program Files\Microsoft Azure Recovery Services Agent\bin\wabadmin.
2. Dans le composant logiciel enfichable, cliquez sur **Modifier les propriétés**.

    ![Limiter la bande passante](./media/site-recovery-vmware-to-azure/throttle1.png)
3. Dans l’onglet **Limitation**, sélectionnez **Activer la limitation de la bande passante sur Internet pour les opérations de sauvegarde** et définissez les limites relatives aux heures de travail et aux heures non travaillées. Les plages valides vont de 512 Kbits/s à 102 Mbits/s par seconde.

    ![Limiter la bande passante](./media/site-recovery-vmware-to-azure/throttle2.png)

Vous pouvez également utiliser l’applet de commande [Set-OBMachineSetting](https://technet.microsoft.com/library/hh770409.aspx) pour définir la limitation. Voici un exemple :

    $mon = [System.DayOfWeek]::Monday
    $tue = [System.DayOfWeek]::Tuesday
    Set-OBMachineSetting -WorkDay $mon, $tue -StartWorkHour "9:00:00" -EndWorkHour "18:00:00" -WorkHourBandwidth  (512*1024) -NonWorkHourBandwidth (2048*1024)

**Set-OBMachineSetting -NoThrottle** indique qu’aucune limitation n’est requise.

#### <a name="influence-network-bandwidth-for-a-vm"></a>Influer sur la bande passante réseau d’une machine virtuelle

1. Dans le registre de machine virtuelle, accédez à **HKEY_LOCAL_MACHINE\SOFTWARE\Microsoft\Windows Azure Backup\Replication**.
   * Pour influer sur le trafic de la bande passante sur un disque de réplication, modifiez la valeur du paramètre **UploadThreadsPerVM**, ou créez la clé si elle n’existe pas.
   * Pour influer sur la bande passante utilisée pour le trafic lié à la restauration automatique à partir d’Azure, modifiez la valeur du paramètre **DownloadThreadsPerVM**.
2. La valeur par défaut est 4. Dans un réseau « surutilisé », ces clés de Registre doivent être modifiées par rapport aux valeurs par défaut. La valeur maximale est de 32. Surveillez le trafic pour optimiser la valeur.


## <a name="deploy-additional-process-servers"></a>Déployer d’autres serveurs de traitement

Si vous devez monter en charge votre déploiement au-delà de 200 machines source ou si votre taux d’évaluation quotidien total dépasse 2 To, vous aurez besoin de serveurs de processus supplémentaires pour gérer le volume de trafic. Suivez ces instructions pour configurer le serveur de processus. Après avoir configuré le serveur, vous pouvez migrer les ordinateurs source pour les utiliser.

1. Dans **Serveurs Site Recovery**, cliquez sur le serveur de configuration > **Serveur de processus**.

    ![Ajouter un serveur de traitement](./media/site-recovery-vmware-to-azure/migrate-ps1.png)
2. Dans **Type de serveur** cliquez sur **Serveur de processus (local)**.

    ![Ajouter un serveur de traitement](./media/site-recovery-vmware-to-azure/migrate-ps2.png)
3. Téléchargez le fichier d’installation unifiée Site Recovery et exécutez-le pour installer le serveur de processus et l’inscrire dans le coffre.
4. Dans la zone **Avant de commencer**, sélectionnez **l’ajout de serveurs de traitement supplémentaires pour effectuer un déploiement avec montée en puissance**.
5. Terminez l’Assistant comme vous l’avez fait lors de la [configuration](#step-2-set-up-the-source-environment) du serveur de configuration.

    ![Ajouter un serveur de traitement](./media/site-recovery-vmware-to-azure/add-ps1.png)
6. Dans **Détails du serveur de configuration**, spécifiez l’adresse IP du serveur de configuration et la phrase secrète. Pour récupérer la phrase secrète, exécutez **<SiteRecoveryInstallationFolder>\home\sysystems\bin\genpassphrase.exe –n** sur le serveur de configuration.

    ![Ajouter un serveur de traitement](./media/site-recovery-vmware-to-azure/add-ps2.png)

### <a name="migrate-machines-to-use-the-new-process-server"></a>Migrez les machines pour utiliser le nouveau serveur de traitement
1. Dans **Paramètres** > **Serveurs Site Recovery**, cliquez sur le serveur de configuration, puis développez **Serveurs de processus**.

    ![Valider un serveur de traitement](./media/site-recovery-vmware-to-azure/migrate-ps2.png)
2. Cliquez avec le bouton droit sur le serveur de processus en cours d’utilisation et sélectionnez **Basculer**.

    ![Valider un serveur de traitement](./media/site-recovery-vmware-to-azure/migrate-ps3.png)
3. Dans **Sélectionner un serveur de traitement cible**, sélectionnez le nouveau serveur de processus à utiliser, puis les machines virtuelles que le nouveau serveur de processus va gérer. Cliquez sur l’icône d’information pour obtenir des informations sur le serveur. Pour vous aider à prendre des décisions quant à la charge, l’espace moyen nécessaire à la réplication de chaque machine virtuelle sélectionnée vers le nouveau serveur de processus s’affiche. Cliquez sur la coche pour commencer la réplication vers le nouveau serveur de traitement.










<!--HONumber=Feb17_HO2-->


