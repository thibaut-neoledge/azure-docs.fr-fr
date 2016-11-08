---
title: Automatiser la récupération d’urgence pour les partages de fichiers sur StorSimple à l’aide d’Azure Site Recovery| Microsoft Docs
description: Décrit les étapes et les meilleures pratiques de création d’une solution de récupération d’urgence pour les partages de fichiers hébergés sur le stockage StorSimple.
services: storsimple
documentationcenter: NA
author: vidarmsft
manager: syadav
editor: ''

ms.service: storsimple
ms.devlang: NA
ms.topic: article
ms.tgt_pltfrm: NA
ms.workload: NA
ms.date: 05/16/2016
ms.author: vidarmsft

---
# Solution de récupération d’urgence automatisée à l’aide d’Azure Site Recovery pour les partages de fichiers hébergés sur StorSimple
## Vue d'ensemble
Microsoft Azure StorSimple est une solution de stockage de cloud hybride qui gère les complexités des données non structurées couramment associées aux partages de fichiers. StorSimple utilise le stockage cloud pour étendre la solution sur site et hiérarchise automatiquement les données sur le stockage local et le stockage cloud. La protection des données intégrée, qui comprend à la fois des instantanés en local et des instantanés sur le cloud, évite d’avoir à s’appuyer sur une immense infrastructure de stockage.

[Azure Site Recovery](../site-recovery/site-recovery-overview.md) est un service Azure offrant des capacités de récupération d’urgence (DR) en coordonnant la réplication, le basculement et la récupération des machines virtuelles. Azure Site Recovery prend en charge un certain nombre de technologies de réplication afin de systématiquement répliquer, protéger et basculer en toute transparence des machines virtuelles et des applications sur des clouds privés/publics ou de l’hébergeur.

Vous pouvez vous appuyer sur Azure Site Recovery, sur la réplication de machines virtuelles et sur les fonctionnalités de capture d’instantanés dans le cloud intégrées à StorSimple pour protéger l’ensemble de votre environnement de serveur de fichiers. En cas d’interruption, vous pouvez, d’un seul clic, afficher vos partages de fichiers en ligne dans Azure en seulement quelques minutes.

Ce document explique en détail comment créer une solution de récupération d’urgence pour vos partages de fichiers hébergés sur le système de stockage StorSimple, et comment effectuer des basculements planifiés/non planifiés/de test à l’aide d’un plan de récupération d’urgence accessible en un clic. En bref, il vous montre comment modifier le plan de récupération dans votre coffre Azure Site Recovery pour permettre des basculements StorSimple dans les scénarios d’urgence. Il décrit également les configurations prises en charge et les conditions requises préalables. Ce document suppose que vous soyez familiarisé avec les principes de base des architectures Azure Site Recovery et StorSimple.

## Options de déploiement Azure Site Recovery prises en charge
Les clients peuvent déployer des serveurs de fichiers en tant que serveurs physiques ou en tant que machines virtuelles exécutés sur Hyper-V ou VMware, avant de créer des partages de fichiers à partir des volumes issus du stockage StorSimple. Azure Site Recovery peut protéger des déploiements physiques et virtuels aussi bien sur un site secondaire que sur Azure. Ce document décrit en détail une solution de récupération d’urgence qui utilise Azure comme site de récupération pour une machine virtuelle du serveur de fichiers hébergée sur Hyper-V et qui stocke des partages de fichiers sur StorSimple. Vous pouvez implémenter de la même façon d’autres scénarios impliquant un serveur de fichiers exécuté sur une machine virtuelle VMware ou sur un ordinateur physique.

## Composants requis
Pour implémenter une solution de récupération d’urgence en un clic qui utilise Azure Site Recovery pour les partages de fichiers hébergés sur le stockage StorSimple, vous devez disposer des éléments suivants :

* Machine virtuelle du serveur de fichiers Windows Server 2012 R2 en local hébergée sur une machine virtuelle Hyper-V ou VMware ou sur un ordinateur physique
* Périphérique de stockage StorSimple local enregistré auprès d’Azure StorSimple Manager
* Appliance cloud StorSimple créée dans Azure StorSimple Manager (peut être conservé à l’état d’arrêt)
* Partages de fichiers hébergés sur les volumes configurés sur le périphérique de stockage StorSimple
* [Coffre Azure Site Recovery Services](../site-recovery/site-recovery-vmm-to-vmm.md) créé dans un abonnement Microsoft Azure.

En outre, si Azure est votre site de récupération d’urgence, exécutez l’[outil d’évaluation de la disponibilité des machines virtuelles Azure](http://azure.microsoft.com/downloads/vm-readiness-assessment/) sur des machines virtuelles afin de vérifier qu’elles sont compatibles avec les machines virtuelles Azure et Azure Site Recovery Services.

Pour éviter des problèmes de latence (qui peuvent entraîner des coûts supplémentaires), veillez à créer votre appliance cloud StorSimple, votre compte Automation et votre compte de stockage dans la même région.

## Activer la récupération d’urgence pour les partages de fichiers StorSimple
Chaque composant de l’environnement local doit être protégé pour permettre la prise en charge complète des tâches de restauration et de réplication. Cette section décrit comment :

* configurer la réplication Active Directory et DNS (facultatif) ;
* utiliser Azure Site Recovery pour activer la protection de la machine virtuelle du serveur de fichiers ;
* activer la protection des volumes StorSimple ;
* configurer le réseau.

### Configurer la réplication Active Directory et DNS (facultatif)
Si vous souhaitez protéger les ordinateurs qui exécutent Active Directory et DNS afin qu’ils soient disponibles sur le site de récupération d’urgence, vous devez explicitement les protéger (afin que les serveurs de fichiers soient accessibles après un basculement avec authentification). En fonction de la complexité de l’environnement local du client, deux options vous sont recommandées.

#### Option 1 :
Si le client dispose d’un nombre d’applications limité et d’un seul contrôleur de domaine pour l’ensemble de son site local, et qu’il souhaite basculer l’intégralité du site, nous recommandons l’utilisation de la réplication Azure Site Recovery pour répliquer l’ordinateur contrôleur de domaine sur un site secondaire (applicable à la fois pour les scénarios de site à site et de site à Azure).

#### Option 2 :
Si le client possède un grand nombre d’applications, qu’il exécute une forêt Active Directory et qu’il souhaite basculer quelques applications à la fois, nous recommandons de configurer un contrôleur de domaine supplémentaire sur le site de récupération d’urgence (sur un site secondaire ou dans Azure).

Pour plus d’informations sur la mise à disposition d’un contrôleur de domaine sur le site de récupération d’urgence, consultez la page [Protéger Active Directory et DNS avec Azure Site Recovery](../site-recovery/site-recovery-active-directory.md). Dans la suite de ce document, nous partirons du principe qu’un contrôleur de domaine est disponible sur le site de récupération d’urgence.

### Utiliser Azure Site Recovery pour activer la protection de la machine virtuelle du serveur de fichiers
Cette étape suppose de préparer l’environnement de serveur de fichiers local, de créer et préparer un coffre Azure Site Recovery, et d’activer la protection des fichiers de la machine virtuelle.

#### Pour préparer l’environnement de serveur de fichiers local
1. Définissez l’option **Contrôle de compte d’utilisateur** sur le paramètre **Never Notify** (Ne jamais m’avertir). Vous devez effectuer ce paramétrage afin de pouvoir utiliser les scripts Azure Automation pour connecter les cibles iSCSI après un basculement par Azure Site Recovery.
   
   1. Appuyez sur la touche Windows + Q et lancez une recherche sur **UAC**.
   2. Sélectionnez **Modifier les paramètres du contrôle de compte d’utilisateur**.
   3. Faites glisser la barre vers le bas vers l’option **Never Notify** (Ne jamais m’avertir).
   4. Cliquez sur **OK**, puis sélectionnez **Oui** à l’invite.
      
      ![](./media/storsimple-dr-using-asr/image1.png)
2. Installez l’agent de machine virtuelle sur chacune des machines virtuelles du serveur de fichiers. Cela vous permettra d’exécuter les scripts Azure Automation sur les machines virtuelles qui ont fait l’objet d’un basculement.
   
   1. [Téléchargez l’agent](http://aka.ms/vmagentwin) dans le répertoire `C:\\Users\<username>\\Downloads`.
   2. Ouvrez Windows PowerShell en mode administrateur (Exécuter en tant qu’administrateur) et entrez la commande suivante pour accéder à l’emplacement de téléchargement :
      
      `cd C:\\Users\<username>\\Downloads\\WindowsAzureVmAgent.2.6.1198.718.rd\_art\_stable.150415-1739.fre.msi`
      
      > [!NOTE]
      > Le nom de fichier peut varier en fonction de la version.
      > 
      > 
3. Cliquez sur **Next**.
4. Acceptez les **termes du contrat**, puis cliquez sur **Suivant**.
5. Cliquez sur **Terminer**.
6. Créez des partages de fichiers à l’aide de volumes issus du stockage StorSimple. Pour plus d’informations, consultez la page [Utiliser le service StorSimple Manager pour gérer les volumes](storsimple-manage-volumes.md).
   
   1. Sur vos machines virtuelles en local, appuyez sur la touche Windows + Q et recherchez **iSCSI**.
   2. Sélectionnez **Initiateur iSCSI**.
   3. Cliquez sur l’onglet **Configuration** et copiez le nom de l’initiateur.
   4. Connectez-vous au [portail Azure Classic](https://manage.windowsazure.com/).
   5. Cliquez sur l’onglet **StorSimple**, puis sélectionnez le service StorSimple Manager qui contient le périphérique physique.
   6. Créez un ou plusieurs conteneurs de volumes, puis créez un ou plusieurs volumes (ces volumes sont réservés aux partages de fichiers résidant sur les machines virtuelles du serveur de fichiers). Copiez le nom de l’initiateur et attribuez un nom approprié aux enregistrements de contrôle d’accès lorsque vous créez des volumes.
   7. Cliquez sur l’onglet **Configurer** et notez l’adresse IP de l’appareil.
   8. Sur vos machines virtuelles en local, accédez de nouveau à l’**initiateur iSCSI** et entrez l’adresse IP dans la section Connexion rapide. Cliquez sur **Connexion rapide** (l’appareil doit maintenant être connecté).
   9. Ouvrez le portail de gestion Azure et cliquez sur l’onglet **Volumes et périphériques**. Cliquez sur **Configuration automatique**. Le volume que vous venez de créer doit normalement apparaître.
   10. Dans le portail, cliquez sur l’onglet **Périphériques**, puis sélectionnez **Create a New Virtual Device** (Créer un périphérique virtuel) (celui-ci sera utilisé si un basculement se produit). Ce nouveau périphérique virtuel peut être conservé à l’état hors connexion afin d’éviter des coûts supplémentaires. Pour mettre le périphérique virtuel hors connexion, accédez à la section **Machines virtuelles** du portail, puis arrêtez simplement le périphérique.
   11. Revenez à vos machines virtuelles locales et ouvrez Gestion des disques (appuyez sur la touche Windows + X et sélectionnez **Gestion des disques**).
   12. Vous remarquerez la présence de quelques disques supplémentaires (en fonction du nombre de volumes que vous avez créés). Cliquez avec le bouton droit sur le premier disque, sélectionnez **Initialiser le disque**, puis cliquez sur **OK**. Cliquez avec le bouton droit sur la section **Non alloué**, sélectionnez **Nouveau volume simple**, attribuez-lui une lettre de lecteur, puis terminez l’Assistant.
   13. Répétez l’étape l pour tous les disques. Tous les disques doivent maintenir figurer dans le répertoire **Ce PC** de l’Explorateur Windows.
   14. Utilisez le rôle Services de fichiers et de stockage pour créer des partages de fichiers sur ces volumes.

#### Pour créer et préparer un coffre Azure Site Recovery
Reportez-vous à la [documentation d’Azure Site Recovery](../site-recovery/site-recovery-hyper-v-site-to-azure.md) pour vous familiariser avec Azure Site Recovery avant de protéger la machine virtuelle du serveur de fichiers.

#### Pour activer la protection
1. Déconnectez la ou les cibles iSCSI des machines virtuelles en local que vous souhaitez protéger dans Azure Site Recovery :
   
   1. Appuyez sur la touche Windows + Q et lancez une recherche sur **iSCSI**.
   2. Sélectionnez **Configurer l’initiateur iSCSI**.
   3. Déconnectez l’appareil StorSimple que vous avez connecté auparavant. Vous pouvez également désactiver le serveur de fichiers pendant quelques minutes au moment de l’activation de la protection.
   
   > [!NOTE]
   > Les partages de fichiers seront alors temporairement indisponibles.
   > 
   > 
2. [Activez la protection des machines virtuelles](../site-recovery/site-recovery-hyper-v-site-to-azure.md##step-6-enable-replication) du serveur de fichiers à partir du portail Azure Site Recovery.
3. Vous pouvez reconnecter la cible dès le lancement de la synchronisation initiale. Accédez à l’initiateur iSCSI, sélectionnez l’appareil StorSimple, puis cliquez sur **Connecter**.
4. Lorsque la synchronisation est terminée et que la machine virtuelle passe à l’état **Protégé**, sélectionnez la machine virtuelle, puis cliquez sur l’onglet **Configurer** et mettez à jour le réseau de la machine virtuelle en conséquence (il s’agit du réseau auquel sont rattachées les machines virtuelles ayant fait l’objet du basculement). Si le réseau ne s’affiche pas, cela signifie que la synchronisation est en cours.

### Activer la protection des volumes StorSimple
Si vous n’avez pas sélectionné l’option **Autoriser une sauvegarde par défaut pour ce volume** pour les volumes StorSimple, accédez à **Stratégies de sauvegarde** dans le service StorSimple Manager et créez une stratégie de sauvegarde appropriée pour l’ensemble des volumes. Nous vous recommandons de faire coïncider la fréquence des sauvegardes avec l’objectif de point de récupération (RPO) que vous souhaitez obtenir pour l’application.

### Configurer le réseau
Pour la machine virtuelle du serveur de fichiers, configurez les paramètres réseau dans Azure Site Recovery afin que les réseaux de machines virtuelles soient attachés au réseau de récupération d’urgence approprié au terme du processus de basculement.

Vous pouvez sélectionner la machine virtuelle dans le **cloud VMM** ou dans le **groupe de protection** afin de configurer les paramètres réseau comme indiqué dans l’image ci-dessous.

![](./media/storsimple-dr-using-asr/image2.png)

## Créer un plan de récupération
Vous pouvez créer un plan de récupération dans ASR pour automatiser le processus de basculement des partages de fichiers. En cas d’interruption, vous pouvez, d’un seul clic, faire apparaître les partages de fichiers en quelques minutes. Pour activer cette automatisation, vous devez disposer d’un compte Azure Automation.

#### Pour créer le compte
1. Accédez au portail Azure Classic et accédez à la section **Automation**.
2. Créez un compte Automation. Utilisez la même région géographique dans laquelle ont été créés l’appliance cloud StorSimple et les comptes de stockage.
3. Cliquez sur **Nouveau** &gt; **App Services** &gt; **Automation** &gt; **Runbook** &gt; **From Gallery** (À partir de la galerie) pour importer tous les runbooks requis dans le compte Automation.
   
   ![](./media/storsimple-dr-using-asr/image3.png)
4. Ajoutez les runbooks suivants à partir du volet **Récupération d’urgence** de la galerie :
   
   * Fail over StorSimple volume containers (Basculer des conteneurs de volumes StorSimple)
   * Clean up of StorSimple volumes after Test Failover (TFO) (Nettoyer les volumes StorSimple après le test de basculement (TFO))
   * Mount volumes on StorSimple device after failover (Monter les volumes sur l’appareil StorSimple après le basculement)
   * Start StorSimple Virtual Appliance (Démarrer l’appliance virtuelle StorSimple)
   * Uninstall custom script extension in Azure VM (Désinstaller l’extension de script personnalisée dans la machine virtuelle Azure)
     
     ![](./media/storsimple-dr-using-asr/image4.png)
5. Publiez tous les scripts en sélectionnant le runbook dans le compte Automation, puis en cliquant sur l’onglet **Auteur**. Après cette étape, l’onglet **Runbooks** s’affiche comme suit :
   
    ![](./media/storsimple-dr-using-asr/image5.png)
6. Dans le compte Automation, sous l’onglet **Ressources**, cliquez sur **Ajouter un paramètre** &gt; **Ajouter une information d’identification**, puis ajoutez vos informations d’identification Azure, à savoir la ressource AzureCredential.
   
   Utilisez l’information d’identification Windows PowerShell. Cette information d’identification doit contenir des nom d’utilisateur et mot de passe Org ID ayant accès à cet abonnement Azure et pour lesquels l’authentification multifacteur est désactivée. Cela est nécessaire pour authentifier l’utilisateur pendant les basculements et pour afficher les volumes du serveur de fichiers sur le site de récupération d’urgence.
7. Dans le compte Automation, sélectionnez l’onglet **Ressources**, cliquez sur **Ajouter un paramètre** &gt; **Ajouter une variable**, puis ajoutez les variables suivantes. Vous pouvez choisir de chiffrer ces ressources. Ces variables sont spécifiques au plan de récupération. Si votre plan de récupération (celui que vous allez créer à l’étape suivante) porte le nom TestPlan, vous devez normalement utiliser les variables TestPlan-StorSimRegKey, TestPlan-AzureSubscriptionName, et ainsi de suite.
   
   * *RecoveryPlanName***-StorSimRegKey** : clé d’inscription du service StorSimple Manager.
   * *RecoveryPlanName***-AzureSubscriptionName** : nom de l’abonnement Azure.
   * *RecoveryPlanName***-ResourceName** : nom de la ressource StorSimple qui contient l’appareil StorSimple.
   * *RecoveryPlanName***-DeviceName** : appareil qui doit être basculé.
   * *RecoveryPlanName***-TargetDeviceName** : appliance cloud StorSimple sur laquelle les conteneurs doivent être basculés.
   * *RecoveryPlanName***-VolumeContainers** : chaîne séparée par des virgules des conteneurs de volumes présents sur l’appareil qui doit faire l’objet d’un basculement ; par exemple, volcon1, volcon2, volcon3.
   * RecoveryPlanName**-TargetDeviceDnsName** : nom de service de l’appareil cible (qui se trouve dans la section **Machines virtuelles** : le nom du service est identique au nom DNS).
   * *RecoveryPlanName***-StorageAccountName** : nom du compte de stockage dans lequel sera stocké le script (qui doit s’exécuter sur la machine virtuelle basculée). Il peut s’agir de n’importe quel compte de stockage disposant d’un minimum d’espace pour stocker temporairement le script.
   * *RecoveryPlanName***-StorageAccountKey** : clé d’accès du compte de stockage ci-dessus.
   * *RecoveryPlanName***-ScriptContainer** : nom du conteneur dans lequel le script est stocké sur le cloud. Si le conteneur n’existe pas, il sera créé.
   * *RecoveryPlanName***-VMGUIDS** : lors de la protection d’une machine virtuelle, Azure Site Recovery affecte à chaque machine virtuelle un ID unique qui fournit des détails sur la machine virtuelle basculée. Pour obtenir le VMGUID, sélectionnez l’onglet **Services de récupération**, puis cliquez sur **Élément protégé** &gt; **Groupes de protection** &gt; **Machines** &gt; **Propriétés**. Si vous disposez de plusieurs machines virtuelles, ajoutez les GUID sous forme de chaîne séparée par des virgules.
   * *RecoveryPlanName***-AutomationAccountName** : nom du compte Automation dans lequel vous avez ajouté les runbooks et les ressources.
   
   Par exemple, si le nom du plan de récupération est fileServerpredayRP, votre onglet **Ressources** doit se présenter comme suit une fois que vous avez ajoutés toutes vos ressources.
   
   ![](./media/storsimple-dr-using-asr/image6.png)
8. Accédez à la section **Services de récupération** et sélectionnez le coffre Azure Site Recovery que vous avez créé précédemment.
9. Sélectionnez l’onglet **Plans de récupération** et créez un plan de récupération en procédant de la manière suivante :
   
   a. Spécifiez un nom et sélectionnez le **groupe de protection** approprié.
   
   b. Sélectionnez les machines virtuelles du groupe de protection que vous souhaitez inclure dans le plan de récupération.
   
   c. Une fois le plan de récupération créé, sélectionnez-le pour ouvrir la vue Recovery plan customization (Personnalisation du plan de récupération).
   
   d. Sélectionnez **Arrêt de tous les groupes**, cliquez sur **Script**, puis choisissez **Add a primary side script before all Group shutdown** (Ajouter un script côté principal avant l’arrêt de tous les groupes).
   
   e. Sélectionnez le compte Automation (dans lequel vous avez ajouté les runbooks), puis sélectionnez le runbook **Fail over-StorSimple-Volume-Containers**.
   
   f. Cliquez sur **Groupe 1 : Démarrer**, choisissez **Machines virtuelles**, puis ajoutez les machines virtuelles qui doivent être protégées dans le plan de récupération.
   
   g. Cliquez sur **Groupe 1 : Démarrer**, choisissez **Script** et ajoutez tous les scripts suivants dans l’ordre comme étapes **après le groupe 1**.
   
   * Start-StorSimple-Virtual-Appliance runbook
   * Fail over-StorSimple-volume-containers runbook
   * Mount-volumes-after-failover runbook
   * Uninstall-custom-script-extension runbook
10. Ajoutez une action manuelle après les 4 scripts ci-dessus dans la même section **groupe 1 : étapes suivantes**. Cette action marque l’étape à laquelle vous pouvez vérifier que tout fonctionne correctement. Elle doit être ajoutée uniquement dans le cadre du test de basculement (vous ne devez donc cocher que la case **Test de basculement**).
11. Après l’action manuelle, ajoutez le script de nettoyage à l’aide de la même procédure que celle utilisée pour les autres runbooks. Enregistrez le plan de récupération.
    
    > [!NOTE]
    > Lorsque vous exécutez un test de basculement, vous devez vérifier tous les éléments à l’étape de l’action manuelle, car les volumes StorSimple qui avaient été clonés sur le périphérique cible seront supprimés dans le cadre du nettoyage après l’exécution de l’action manuelle.
    > 
    > 
    
    ![](./media/storsimple-dr-using-asr/image7.png)

## Exécution d’un test de basculement
Reportez-vous au guide d’accompagnement [Solution de récupération d’urgence d’Active Directory](../site-recovery/site-recovery-active-directory.md) pour obtenir des informations propres à Active Directory pendant le test de basculement. L’installation en local n’est pas perturbée au cours du test de basculement. Les volumes StorSimple qui étaient attachées à la machine virtuelle locale sont clonés sur l’appliance cloud StorSimple dans Azure. Une machine virtuelle de test s’affiche dans Azure et les volumes clonés sont attachés à la machine virtuelle.

#### Pour effectuer le test de basculement
1. Dans le portail Azure Classic, sélectionnez votre coffre de récupération de sites.
2. Cliquez sur le plan de récupération que vous avez créé pour la machine virtuelle du serveur de fichiers.
3. Cliquez sur **Test de basculement**.
4. Sélectionnez le réseau virtuel pour démarrer le processus de test de basculement.
   
   ![](./media/storsimple-dr-using-asr/image8.png)
5. Lorsque l’environnement secondaire est opérationnel, vous pouvez effectuer vos validations.
6. Une fois les validations terminées, cliquez sur **Validations Complete** (Validations terminées). L’environnement de test de basculement est supprimé et l’opération TFO est terminée.

## Exécuter un basculement non planifié
Lors d’un basculement non planifié, les volumes StorSimple sont basculés vers l’appareil virtuel, une machine virtuelle de réplication s’affiche sur Azure et les volumes sont associés à la machine virtuelle.

#### Pour exécuter un basculement non planifié
1. Dans le portail Azure Classic, sélectionnez votre coffre de récupération de sites.
2. Cliquez sur le plan de récupération que vous avez créé pour la machine virtuelle du serveur de fichiers.
3. Cliquez sur **Basculement**, puis sélectionnez **Basculement non planifié**.
   
   ![](./media/storsimple-dr-using-asr/image9.png)
4. Sélectionnez le réseau cible, puis cliquez sur l’icône de coche ✓ pour démarrer le processus de basculement.

## Exécuter un basculement planifié
Lors d’un basculement planifié, la machine virtuelle locale du serveur de fichiers s’arrête normalement et un instantané des volumes de l’appareil StorSimple est sauvegardé dans le cloud. Les volumes StorSimple sont basculés vers l’appareil virtuel, une machine virtuelle de réplication s’affiche sur Azure et les volumes sont associés à la machine virtuelle.

#### Pour exécuter un basculement planifié
1. Dans le portail Azure Classic, sélectionnez votre coffre de récupération de sites.
2. Cliquez sur le plan de récupération que vous avez créé pour la machine virtuelle du serveur de fichiers.
3. Cliquez sur **Basculement**, puis sélectionnez **Basculement planifié**.
4. Sélectionnez le réseau cible, puis cliquez sur l’icône de coche ✓ pour démarrer le processus de basculement.

## Effectuer une restauration automatique
Pendant une restauration automatique, les conteneurs de volumes StorSimple basculent vers l’appareil physique après l’exécution d’une sauvegarde.

#### Pour effectuer une restauration automatique
1. Dans le portail Azure Classic, sélectionnez votre coffre de récupération de sites.
2. Cliquez sur le plan de récupération que vous avez créé pour la machine virtuelle du serveur de fichiers.
3. Cliquez sur **Basculement** et sélectionnez **Basculement planifié** ou **Basculement non planifié**.
4. Cliquez sur **Changer de direction**.
5. Sélectionnez les options de synchronisation de données et de création de machines virtuelles appropriées.
6. Cliquez sur l’icône de coche ✓ pour démarrer le processus de restauration automatique.
   
   ![](./media/storsimple-dr-using-asr/image10.png)

## Meilleures pratiques
### Planification de la capacité et évaluation de la préparation
#### Site Hyper-V
Utilisez l’[outil de planification de capacité utilisateur](http://www.microsoft.com/download/details.aspx?id=39057) pour concevoir l’infrastructure de serveur, de stockage et de réseau adaptée à votre environnement de réplication Hyper-V.

#### Microsoft Azure
Vous pouvez exécuter l’[outil d’évaluation de la disponibilité des machines virtuelles Azure](http://azure.microsoft.com/downloads/vm-readiness-assessment/) sur des machines virtuelles, afin de vérifier qu’elles sont compatibles avec les machines virtuelles Azure et Azure Site Recovery Services. Cet outil vérifie les configurations de machines virtuelles et vous avertit lorsque des configurations ne sont pas compatibles avec Azure. Par exemple, il émet un avertissement dans le cas d’un lecteur C: supérieur à 127 Go.

La planification de la capacité comporte au moins deux processus importants :

* Mappage des machines virtuelles Hyper-V locales sur les tailles de machine virtuelle Azure (par exemple, A6, A7, A8 et A9).
* Détermination de la bande passante Internet requise.

## Limites
* Il n’est actuellement possible de basculer qu’un seul périphérique StorSimple (sur une seule appliance cloud StorSimple). Le scénario d’un serveur de fichiers qui s’étend sur plusieurs périphériques StorSimple n’est pas encore pris en charge.
* Si vous obtenez une erreur lors de l’activation de la protection d’une machine virtuelle, assurez-vous que vous avez déconnecté les cibles iSCSI.
* Tous les conteneurs de volumes qui ont été regroupés en raison des stratégies de sauvegarde communes aux différents conteneurs de volumes basculeront ensemble.
* Tous les volumes des conteneurs de volumes que vous avez choisis seront basculés.
* Les volumes qui peuvent cumuler une capacité supérieure à 64 To ne peuvent pas être basculés car la capacité maximale d’une appliance cloud StorSimple est de 64 To.
* Si le processus de basculement planifié ou non planifié échoue et que les machines virtuelles sont créées dans Azure, évitez de nettoyer les machines virtuelles. Effectuez plutôt une restauration automatique. Si vous supprimez les machines virtuelles, vous ne pourrez plus réactiver les machines virtuelles locales.
* Après un basculement, si vous n’êtes pas en mesure de voir les volumes, accédez aux machines virtuelles, ouvrez Gestion des disques, réanalysez les disques, puis mettez-les en ligne.
* Dans certains cas, les lettres de lecteur du site de récupération d’urgence peuvent être différentes de celles disponibles en local. Si cela se produit, vous devez corriger le problème manuellement après le basculement.
* L’authentification multifacteur doit être désactivée pour les informations d’identification Azure qui sont saisies en tant que ressources dans le compte Automation. Si cette authentification n’est pas désactivée, les scripts ne pourront pas s’exécuter automatiquement et le plan de récupération échouera.
* Délai d’attente de la tâche de basculement : le script StorSimple expire si le temps de basculement des conteneurs de volumes dépasse la limite par script d’Azure Site Recovery (actuellement établie à 120 minutes).
* Délai d’attente de la tâche de sauvegarde : le script StorSimple expire si le temps de sauvegarde des volumes dépasse la limite par script d’Azure Site Recovery (actuellement établie à 120 minutes).
  
  > [!IMPORTANT]
  > Exécutez manuellement la sauvegarde à partir du portail Azure et réexécutez le plan de récupération.
  > 
  > 
* Délai d’attente de la tâche de clonage : le script StorSimple expire si le temps de clonage des volumes dépasse la limite par script d’Azure Site Recovery (actuellement établie à 120 minutes).
* Erreur de synchronisation de temps : les scripts StorSimple génèrent une erreur indiquant que les sauvegardes ont échoué, même si la sauvegarde a réussi dans le portail. Ce problème peut être lié à un défaut de synchronisation de l’heure de l’appliance StorSimple avec l’heure actuelle du fuseau horaire.
  
  > [!IMPORTANT]
  > Synchronisez l’heure de l’appliance avec l’heure actuelle du fuseau horaire.
  > 
  > 
* Erreur de basculement de l’appliance : le script StorSimple peut échouer si l’appliance est en cours de basculement pendant l’exécution du plan de récupération.
  
  > [!IMPORTANT]
  > Exécutez à nouveau le plan de récupération une fois le basculement de l’appliance terminé.
  > 
  > 

## Résumé
Utilisez Azure Site Recovery pour créer un plan de récupération d’urgence automatisée complet pour une machine virtuelle du serveur de fichiers comportant des partages de fichiers sur le stockage StorSimple. Vous pouvez lancer le basculement en quelques secondes, où que vous soyez, et bénéficier d’une application opérationnelle en quelques minutes.

<!---HONumber=AcomDC_0518_2016-->