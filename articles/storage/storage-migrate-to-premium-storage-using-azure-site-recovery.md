---
title: "Migration vers le stockage Premium Azure à l&quot;aide d&quot;Azure Site Recovery | Microsoft Docs"
description: "Migration de vos machines virtuelles existantes vers le stockage Premium Azure à l&quot;aide de Site Recovery. Premium Storage offre une prise en charge très performante et à faible latence des disques pour les charges de travail utilisant beaucoup d&quot;E/S exécutées sur les machines virtuelles Azure."
services: storage
cloud: Azure
documentationcenter: na
author: luywang
manager: kavithag
ms.assetid: 
ms.service: <service per approved list>
ms.workload: storage
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 04/06/2017
ms.author: luywang
translationtype: Human Translation
ms.sourcegitcommit: e851a3e1b0598345dc8bfdd4341eb1dfb9f6fb5d
ms.openlocfilehash: 522fd46e8c0ccc64eb97ee6622e9886bb51f1e24
ms.lasthandoff: 04/15/2017


---
# <a name="migrating-to-premium-storage-using-azure-site-recovery"></a>Migration vers le stockage Premium à l'aide d'Azure Site Recovery

Le [stockage Premium Azure](storage-premium-storage.md) offre une prise en charge des disques haute performance et à faible latence pour les machines virtuelles exécutant des charges de travail qui utilisent beaucoup d'E/S. Ce guide vise à aider les utilisateurs à migrer leurs disques de machine virtuelle d’un compte de stockage Standard vers un compte de stockage Premium à l’aide [d'Azure Site Recovery](../site-recovery/site-recovery-overview.md).

Site Recovery est un service Azure qui participe à votre stratégie de continuité des activités et de récupération d’urgence en orchestrant la réplication des machines virtuelles et des serveurs physiques locaux dans le cloud (Azure) ou sur un centre de données secondaire. Lorsque des pannes se produisent sur votre site principal, vous effectuez un basculement sur le site secondaire pour préserver la disponibilité des applications et des charges de travail. Vous restaurez votre site principal dès lors qu’il retrouve un fonctionnement normal. Site Recovery fournit des tests de basculement pour prendre en charge des exercices de récupération d’urgence sans affecter les environnements de production. Vous pouvez exécuter des basculements avec une perte de données minimale (en fonction de la fréquence de réplication) pour les sinistres inattendus. Dans le scénario de migration vers le Stockage Premium, vous pouvez utiliser le [Basculement dans Azure Site Recovery](../site-recovery/site-recovery-failover.md) pour migrer des disques cibles vers un compte de stockage Premium.

Nous recommandons de migrer vers un stockage Premium à l’aide de Site Recovery, car cette option limite les temps d'arrêt au minimum et permet d’éviter l’exécution manuelle de la copie de disques et la création de nouvelles machines virtuelles. Site Recovery copie systématiquement vos disques et crée de nouvelles machines virtuelles pendant le basculement. Site Recovery prend en charge différents types de basculement avec très peu de temps d'arrêt ou aucun temps d'arrêt. Pour planifier votre temps d'arrêt et estimer la perte de données, consultez la table [Types de basculement](../site-recovery/site-recovery-failover.md) dans Site Recovery. Si vous [préparez des connexions à des machines virtuelles Azure après le basculement](../site-recovery/site-recovery-vmware-to-azure.md#prepare-vms-for-replication), vous devez être à même de vous connecter à la machine virtuelle Azure à l'aide de RDP après le basculement.

![][1]

## <a name="azure-site-recovery-components"></a>Composants Azure Site Recovery

Il s’agit des composants Site Recovery qui sont pertinents pour ce scénario de migration.

* Un **serveur de configuration** est une machine virtuelle Azure qui coordonne la communication et gère les processus de réplication et de récupération des données. Sur cette machine virtuelle, vous allez exécuter un fichier d’installation unique pour installer le serveur de configuration et un composant supplémentaire, appelé un serveur de processus, comme passerelle de réplication. En savoir plus sur la [configuration requise du serveur](../site-recovery/site-recovery-vmware-to-azure.md#prerequisites). Le serveur de configuration doit simplement être configuré une seule fois et peut être utilisé pour toutes les migrations vers la même région.

* Un **serveur de processus** est une passerelle de réplication qui reçoit les données de réplication à partir de machines virtuelles sources, optimise les données avec la mise en cache, la compression et le chiffrement, et envoie ces données à un compte de stockage. Il gère également l’installation Push du service Mobilité sur des machines virtuelles source protégées et assure la détection automatique des machines virtuelles source. Le serveur de processus par défaut est installé sur le serveur de configuration. Vous pouvez déployer des serveurs de processus autonomes supplémentaires pour étendre votre déploiement. En savoir plus sur les [meilleures pratiques de déploiement de serveur de processus](https://azure.microsoft.com/blog/best-practices-for-process-server-deployment-when-protecting-vmware-and-physical-workloads-with-azure-site-recovery/) et le [déploiement de serveurs de processus supplémentaires](../site-recovery/site-recovery-plan-capacity-vmware.md#deploy-additional-process-servers). Le serveur de processus doit simplement être configuré une seule fois et peut être utilisé pour toutes les migrations vers la même région.

* Un **service Mobilité** est un composant qui est déployé sur chaque machine virtuelle standard que vous souhaitez répliquer. Il enregistre les opérations d'écritures de données sur la machine virtuelle standard et les transmet au serveur de processus. En savoir plus sur la [configuration requise pour les machines répliquées](../site-recovery/site-recovery-vmware-to-azure.md#prerequisites).

Ce graphique montre comment ces composants interagissent.

![][15]

> [!NOTE]
> Site Recovery ne prend pas en charge la migration des disques des espaces de stockage.

Pour obtenir des composants supplémentaires pour d’autres scénarios, reportez-vous à la rubrique [Architecture du scénario](../site-recovery/site-recovery-vmware-to-azure.md).

## <a name="azure-essentials"></a>Éléments principaux d’Azure

Il s’agit des conditions requises par Azure pour ce scénario de migration.

* Un abonnement Azure
* Un compte de stockage Premium Azure pour stocker les données répliquées.
* Un réseau virtuel (VNet) Azure auquel les machines virtuelles se connectent quand elles sont créées au moment du basculement. Le réseau virtuel Azure doit se trouver dans la région dans laquelle Site Recovery est exécuté.
* Un compte de stockage standard Azure dans lequel stocker les journaux de réplication. Il peut s'agir du même compte de stockage que pour les disques de machine virtuelle en cours de migration

## <a name="prerequisites"></a>Composants requis

* Comprendre les composants de scénario de migration pertinents dans la section précédente
* Planifier votre temps d’arrêt en vous familiarisant avec le [Basculement dans Site Recovery](../site-recovery/site-recovery-failover.md)

## <a name="setup-and-migration-steps"></a>Configuration et étapes de la migration

Vous pouvez utiliser Site Recovery pour migrer des machines virtuelles IaaS Azure entre différentes régions ou au sein de la même région. Les instructions suivantes ont été adaptées à ce scénario de migration à partir de l’article [Réplication de machines virtuelles VMware ou de serveurs physiques sur Azure dans le portail Azure](../site-recovery/site-recovery-vmware-to-azure.md). Suivez les liens pour obtenir des étapes détaillées des instructions de cet article.

1. **Créer un coffre Recovery Services**. Créez et gérez le coffre Site Recovery par le biais du [portail Azure](https://portal.azure.com). Cliquez sur **Nouveau** > **Gestion** > **Sauvegarde** et **Récupération de sites (OMS)**. Vous pouvez également sélectionner **Parcourir** > **Coffres Recovery Services** > **Ajouter**. Les machines virtuelles sont répliquées dans la région que vous spécifiez à cette étape. Pour effectuer une migration au sein de la même région, sélectionnez la région dans laquelle se trouvent vos machines virtuelles source et les comptes de stockage source. Notez que la migration vers des comptes de stockage Premium est uniquement prise en charge dans le [portail Azure](https://portal.azure.com), et non dans le [portail classique](https://manage.windowsazure.com).

2. Les étapes suivantes vous aident à **sélectionner vos objectifs en matière de protection**.

    2a. Sur la machine virtuelle sur laquelle vous souhaitez installer le serveur de configuration, ouvrez le [portail Azure](https://portal.azure.com). Accédez à **Coffres Recovery Services** > **Paramètres**. Sous **Paramètres**, sélectionnez **Site Recovery**. Sous **Site Recovery**, sélectionnez **Étape 1 : Préparez l'infrastructure**. Sous **Préparer l'infrastructure**, sélectionnez **Objectif de protection**.

    ![][2]

    2b. Sous **Objectif de protection**, dans la première liste déroulante, sélectionnez **Vers Azure**. Dans la deuxième liste déroulante, sélectionnez **Non virtualisé / autre**, puis cliquez sur **OK**.

    ![][3]

3. Les étapes suivantes vous aident à **configurer l’environnement source (serveur de configuration)**.

    3a. Téléchargez le **fichier d'installation unifiée Azure Site Recovery** et la **clé d’inscription du coffre** en accédant au panneau **Préparer l'infrastructure** > **Préparer la source** > **Ajouter un serveur**. Vous aurez besoin de la clé d’inscription de coffre pour exécuter le programme d’installation unifiée. Une fois générée, la clé est valide pendant 5 jours.

    ![][4]

    3b. Ajoutez le serveur de configuration dans le panneau **Ajouter un serveur**.

    ![][5]

    3c. Sur la machine virtuelle qui vous sert de serveur de configuration, exécutez le programme d’installation unifiée afin d’installer le serveur de configuration et le serveur de processus. Vous pouvez parcourir les captures d’écran [ici](../site-recovery/site-recovery-vmware-to-azure.md#set-up-the-source-environment) pour exécuter l’installation. Vous pouvez consulter les captures d’écran ci-dessous pour connaître les étapes spécifiées pour ce scénario de migration.

    Dans **Avant de commencer**, sélectionnez **l’installation du serveur de configuration et du serveur de processus**.

    ![][6]

    3d. Dans **Inscription**, recherchez et sélectionnez la clé d’inscription que vous avez téléchargée à partir du coffre.

    ![][7]

    3e. Dans **Détails de l’environnement**, indiquez si vous voulez répliquer des machines virtuelles VMware. Pour ce scénario de migration, choisissez **non**.

    ![][8]

    3f. Une fois l’installation terminée, la fenêtre **Serveur de configuration Microsoft Azure Site Recovery** s'affiche. Utilisez l'onglet **Gérer les comptes** pour créer le compte que Site Recovery peut utiliser pour la découverte automatique (Dans le scénario sur la protection des machines physiques, la configuration du compte n’est pas pertinente, mais vous avez besoin d'au moins un compte pour activer l’une des étapes suivantes. Dans ce cas, vous pouvez nommer le compte et définir le mot de passe comme vous le souhaitez). Utilisez l'onglet **Inscription du coffre** pour télécharger le fichier d’informations d’identification du coffre.

    ![][9]

4. **Configurer l’environnement cible**. Cliquez sur **Préparer l'infrastructure** > **Cible** et spécifiez le modèle de déploiement que vous souhaitez utiliser pour les machines virtuelles après le basculement. Vous pouvez choisir **Classique** ou **Resource Manager**, en fonction de votre scénario.

    ![][10]

    Site Recovery vérifie que vous disposez d’un ou de plusieurs réseaux et comptes Azure Storage compatibles. Notez que, si vous utilisez un compte de stockage Premium pour les données répliquées, vous devez configurer un compte de stockage Standard supplémentaire afin de stocker les journaux de réplication.

5. **Configurer les paramètres de réplication**. Suivez les étapes présentées dans [Configurer les paramètres de réplication](../site-recovery/site-recovery-vmware-to-azure.md#set-up-replication-settings) pour vérifier que votre serveur de configuration est correctement associé à la stratégie de réplication que vous créez.

6. **Planification de la capacité**. Utilisez le [Capacity Planner](../site-recovery/site-recovery-capacity-planner.md) pour évaluer de manière précise la bande passante réseau, le stockage et les autres conditions requises pour répondre à vos besoins en matière de réplication. Lorsque vous avez terminé, sélectionnez **Oui** dans **Avez-vous effectué une planification de la capacité ?**

    ![][11]

7. Les étapes suivantes vous aident à **installer le service Mobilité et activer la réplication**.

    7a. Vous pouvez choisir [d'installer le service Mobilité en mode Push](../site-recovery/site-recovery-vmware-to-azure.md#prepare-for-automatic-discovery-and-push-installation) sur vos machines virtuelles source ou [de l'installer manuellement](../site-recovery/site-recovery-vmware-to-azure-install-mob-svc.md) sur vos machines virtuelles source. Vous trouverez les conditions requises pour l'installation en mode Push et le chemin d’accès pour l'installation manuelle dans le lien fourni. Si vous effectuez une installation manuelle, vous devrez peut-être utiliser une adresse IP interne pour rechercher le serveur de configuration.

    ![][12]

    La machine virtuelle ayant basculé aura deux disques temporaires : l'un de la machine virtuelle principale et l’autre créé lors de l'approvisionnement de machines virtuelles dans la région de récupération. Pour exclure le disque temporaire avant la réplication, installez le service Mobilité avant d’activer la réplication. Pour plus d’informations sur la façon d’exclure le disque temporaire, reportez-vous à la rubrique [Exclure les disques de la réplication](../site-recovery/site-recovery-vmware-to-azure.md#exclude-disks-from-replication).

    7b. À présent, activez la réplication comme suit :
      * Cliquez sur **Répliquer l’application** > **Source**. Après avoir activé la réplication pour la première fois, cliquez sur l’option +Répliquer dans le coffre pour activer la réplication d'autres machines.
      * À l’étape 1, configurez la source en tant que votre serveur de processus.
      * À l’étape 2, spécifiez le modèle de déploiement après le basculement, un compte de stockage Premium vers lequel migrer, un compte de stockage standard pour enregistrer les journaux et un réseau virtuel vers lequel basculer.
      * À l’étape 3, ajoutez des machines virtuelles protégées par adresse IP (vous aurez peut-être besoin d'une adresse IP interne pour les rechercher).
      * À l’étape 4, configurez les propriétés en sélectionnant les comptes que vous avez configurés précédemment sur le serveur de processus.
      * À l’étape 5, choisissez la stratégie de réplication que vous avez créée précédemment et définissez les paramètres de réplication.
      Cliquez sur **OK** pour activer la réplication.

    > [!NOTE]
    > Lorsqu’une machine virtuelle Azure est libérée et redémarrée, il n’existe aucune garantie qu’elle aura la même adresse IP. Si l’adresse IP du serveur de configuration/serveur de processus ou les machines virtuelles Azure protégées changent, il se peut que la réplication dans ce scénario ne fonctionne pas correctement.

    ![][13]

    Lorsque vous concevez votre environnement de stockage Azure, nous vous recommandons d’utiliser des comptes de stockage distincts pour chaque machine virtuelle dans un groupe à haute disponibilité. Nous vous recommandons de suivre la bonne pratique de la couche de stockage concernant [l‘utilisation de plusieurs comptes de stockage pour chaque groupe à haute disponibilité](../virtual-machines/windows/manage-availability.md). La distribution de disques de machines virtuelles sur plusieurs comptes de stockage permet d’améliorer la disponibilité du stockage et distribue les E/S sur l’infrastructure de stockage Azure. Si vos machines virtuelles se trouvent dans un groupe à haute disponibilité sans réplication des disques de toutes les machines virtuelles dans un compte de stockage, nous vous recommandons de migrer plusieurs machines virtuelles à plusieurs reprises, afin que les machines virtuelles dans le même groupe à haute disponibilité ne partagent pas un seul compte de stockage. Utilisez le panneau **Activer la réplication** pour configurer un compte de stockage de destination pour chaque machine virtuelle, un à la fois. Vous pouvez choisir un modèle de déploiement post-basculement en fonction de vos besoins. Si vous choisissez Resource Manager (RM) en tant que modèle de déploiement post-basculement, vous pouvez basculer d’une machine virtuelle RM vers une machine virtuelle RM, ou vous pouvez basculer d’une machine virtuelle classique vers une machine virtuelle RM.

8. **Exécuter un test de basculement**. Pour vérifier si la réplication est terminée, cliquez sur votre Site Recovery, puis sur **Paramètres** > **Éléments répliqués**. Vous verrez l’état et le pourcentage de votre processus de réplication. Une fois la réplication initiale terminée, exécutez le test de basculement pour valider votre stratégie de réplication. Pour obtenir des instructions détaillées sur les étapes du test de basculement, reportez-vous à la rubrique [Exécution d'un test de basculement dans Site Recovery](../site-recovery/site-recovery-vmware-to-azure.md#run-a-test-failover). Vous pouvez consulter l’état du test de basculement sous **Paramètres** > **Tâches** > **LE_NOM_DE_VOTRE_PLAN_DE_BASCULEMENT**. Dans le panneau, vous verrez une répartition des étapes et leurs résultats (réussite/échec). Si le test de basculement échoue à n'importe quelle étape, cliquez sur l’étape pour vérifier le message d’erreur. Assurez-vous que vos machines virtuelles et la stratégie de réplication répondent aux exigences avant d’exécuter un basculement. Lisez la page [Tester le basculement vers Azure dans Site Recovery](../site-recovery/site-recovery-test-failover-to-azure.md) pour plus d’informations et d’instructions sur les tests de basculement.

9. **Exécutez un basculement**. Une fois le test de basculement terminé, exécutez un basculement pour migrer vos disques vers le Stockage Premium et répliquer les instances de machines virtuelles. Suivez les étapes détaillées dans [Exécuter un basculement](../site-recovery/site-recovery-failover.md#run-a-failover). Assurez-vous de sélectionner **Arrêter les machines virtuelles et synchroniser les dernières données** pour indiquer que le logiciel Site Recovery doit essayer d’arrêter les machines virtuelles protégées et de synchroniser les données, afin que la dernière version de ces dernières fasse l’objet d’un basculement. Si vous ne sélectionnez pas cette option ou si la tentative n’aboutit pas, le basculement sera effectué depuis le dernier point de récupération disponible pour la machine virtuelle. Site Recovery créera une instance de machine virtuelle dont le type est identique ou similaire à une machine virtuelle compatible avec le stockage Premium. Vous pouvez vérifier les performances et le prix des différentes instances de machine virtuelle en accédant à [Tarification des machines virtuelles Windows](https://azure.microsoft.com/pricing/details/virtual-machines/windows/) ou à [Tarification des machines virtuelles Linux](https://azure.microsoft.com/pricing/details/virtual-machines/linux/).

## <a name="post-migration-steps"></a>Étapes de post-migration

1. **Configurer des machines virtuelles répliquées dans le groupe à haute disponibilité, le cas échéant**. Site Recovery ne prend pas en charge la migration de machines virtuelles avec le groupe à haute disponibilité. En fonction du déploiement de votre machine virtuelle répliquée, effectuez l’une des opérations suivantes :
  * Pour une machine virtuelle créée à l’aide du modèle de déploiement classique : ajoutez la machine virtuelle au groupe à haute disponibilité dans le portail Azure. Pour connaître les détails des étapes, accédez à [Ajout d’une machine virtuelle existante à un groupe à haute disponibilité](../virtual-machines/windows/classic/configure-availability.md#a-idaddmachine-aoption-2-add-an-existing-virtual-machine-to-an-availability-set).
  * Pour le modèle de déploiement Resource Manager : enregistrez votre configuration de machine virtuelle, puis supprimez et recréez les machines virtuelles dans le groupe à haute disponibilité. Pour ce faire, utilisez le script figurant dans [Définir le groupe à haute disponibilité de machine virtuelle Azure Resource Manager](https://gallery.technet.microsoft.com/Set-Azure-Resource-Manager-f7509ec4). Vérifiez la limite de ce script et planifiez un temps d’arrêt avant d’exécuter le script.

2. **Supprimer les anciennes machines virtuelles et les anciens disques**. Avant de les supprimer, assurez-vous que les disques Premium sont cohérents avec les disques sources et que les nouvelles machines virtuelles exécutent la même fonction que les machines virtuelles source. Dans le modèle de déploiement Resource Manager (RM), supprimez la machine virtuelle et les disques de vos comptes de stockage source dans le portail Azure. Dans le modèle de déploiement classique, vous pouvez supprimer la machine virtuelle et les disques dans le portail classique ou dans le portail Azure. Si un problème survient et que le disque n’est pas supprimé même après la suppression de la machine virtuelle, consultez la rubrique [Résoudre les erreurs lorsque vous supprimez des disques durs virtuels dans un déploiement Resource Manager](storage-resource-manager-cannot-delete-storage-account-container-vhd.md) ou [Résoudre les problèmes de suppression de disques durs virtuels dans un déploiement classique](storage-cannot-delete-storage-account-container-vhd.md).

3. **Nettoyer l’infrastructure Azure Site Recovery**. Si Site Recovery n’est plus nécessaire, vous pouvez nettoyer son infrastructure en supprimant des éléments répliqués, le serveur de configuration et la stratégie de récupération, puis en supprimant le coffre Azure Site Recovery.

## <a name="troubleshooting"></a>résolution des problèmes

* [Surveiller et résoudre les problèmes de protection pour les machines virtuelles et les serveurs physiques](../site-recovery/site-recovery-monitoring-and-troubleshooting.md)
* [Forum Microsoft Azure Site Recovery](https://social.msdn.microsoft.com/Forums/azure/home?forum=hypervrecovmgr)

## <a name="next-steps"></a>Étapes suivantes

Consultez les ressources suivantes pour des scénarios spécifiques de migration des machines virtuelles :

* [Migrer des machines virtuelles Azure entre les comptes de stockage](https://azure.microsoft.com/blog/2014/10/22/migrate-azure-virtual-machines-between-storage-accounts/)
* [Création et téléchargement d’un disque dur virtuel Windows Server dans Azure.](../virtual-machines/windows/classic/createupload-vhd.md?toc=%2fazure%2fvirtual-machines%2fwindows%2fclassic%2ftoc.json)
* [Création et téléchargement d’un disque dur virtuel contenant le système d’exploitation Linux](../virtual-machines/linux/classic/create-upload-vhd.md?toc=%2fazure%2fvirtual-machines%2flinux%2fclassic%2ftoc.json)
* [Migration de machines virtuelles à partir d’Amazon AWS vers Microsoft Azure](http://channel9.msdn.com/Series/Migrating-Virtual-Machines-from-Amazon-AWS-to-Microsoft-Azure)

Consultez également les ressources suivantes pour en savoir plus sur Azure Storage et les machines virtuelles Azure :

* [Stockage Azure](https://azure.microsoft.com/documentation/services/storage/)
* [Azure Virtual Machines](https://azure.microsoft.com/documentation/services/virtual-machines/)
* [Stockage Premium : stockage hautes performances pour les charges de travail des machines virtuelles Azure](storage-premium-storage.md)

[1]:./media/storage-migrate-to-premium-storage-using-azure-site-recovery/migrate-to-premium-storage-using-azure-site-recovery-1.png
[2]:./media/storage-migrate-to-premium-storage-using-azure-site-recovery/migrate-to-premium-storage-using-azure-site-recovery-2.png
[3]:./media/storage-migrate-to-premium-storage-using-azure-site-recovery/migrate-to-premium-storage-using-azure-site-recovery-3.png
[4]:./media/storage-migrate-to-premium-storage-using-azure-site-recovery/migrate-to-premium-storage-using-azure-site-recovery-4.png
[5]:./media/storage-migrate-to-premium-storage-using-azure-site-recovery/migrate-to-premium-storage-using-azure-site-recovery-5.png
[6]:./media/storage-migrate-to-premium-storage-using-azure-site-recovery/migrate-to-premium-storage-using-azure-site-recovery-6.PNG
[7]:./media/storage-migrate-to-premium-storage-using-azure-site-recovery/migrate-to-premium-storage-using-azure-site-recovery-7.PNG
[8]:./media/storage-migrate-to-premium-storage-using-azure-site-recovery/migrate-to-premium-storage-using-azure-site-recovery-8.PNG
[9]:./media/storage-migrate-to-premium-storage-using-azure-site-recovery/migrate-to-premium-storage-using-azure-site-recovery-9.PNG
[10]:./media/storage-migrate-to-premium-storage-using-azure-site-recovery/migrate-to-premium-storage-using-azure-site-recovery-10.png
[11]:./media/storage-migrate-to-premium-storage-using-azure-site-recovery/migrate-to-premium-storage-using-azure-site-recovery-11.PNG
[12]:./media/storage-migrate-to-premium-storage-using-azure-site-recovery/migrate-to-premium-storage-using-azure-site-recovery-12.PNG
[13]:./media/storage-migrate-to-premium-storage-using-azure-site-recovery/migrate-to-premium-storage-using-azure-site-recovery-13.png
[14]:../site-recovery/media/site-recovery-vmware-to-azure/v2a-architecture-henry.png
[15]:./media/storage-migrate-to-premium-storage-using-azure-site-recovery/migrate-to-premium-storage-using-azure-site-recovery-14.png

