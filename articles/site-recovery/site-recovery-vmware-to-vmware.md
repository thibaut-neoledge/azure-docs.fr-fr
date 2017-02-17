---
title: "Répliquer des serveurs physiques ou des machines virtuelles VMware sur un autre site (portail Azure Classic) | Microsoft Docs"
description: "Utilisez cet article pour répliquer les machines virtuelles VMware ou les serveurs physiques Windows/Linux sur un site secondaire avec Azure Site Recovery."
services: site-recovery
documentationcenter: 
author: nsoneji
manager: jwhit
editor: 
ms.assetid: b2cba944-d3b4-473c-8d97-9945c7eabf63
ms.service: site-recovery
ms.workload: backup-recovery
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 02/06/2017
ms.author: nisoneji
translationtype: Human Translation
ms.sourcegitcommit: 3b606aa6dc3b84ed80cd3cc5452bbe1da6c79a8b
ms.openlocfilehash: cff6ef0c1c49110edc53ec82f88d9875439aab8a


---
# <a name="replicate-on-premises-vmware-virtual-machines-or-physical-servers-to-a-secondary-site-in-the-classic-azure-portal"></a>Répliquer des serveurs physiques ou des machines virtuelles VMware locaux sur un site secondaire dans le portail Azure Classic

## <a name="overview"></a>Vue d'ensemble
InMage Scout dans Azure Site Recovery assure une réplication en temps réel entre des sites VMware locaux. InMage Scout est inclus dans les abonnements au service Azure Site Recovery.

## <a name="prerequisites"></a>Composants requis
**Compte Azure**: vous devez disposer d’un compte [Microsoft Azure](https://azure.microsoft.com/) . Vous pouvez commencer avec une [version d'évaluation gratuite](https://azure.microsoft.com/pricing/free-trial/). [En savoir plus](https://azure.microsoft.com/pricing/details/site-recovery/) sur la tarification Site Recovery.

## <a name="step-1-create-a-vault"></a>Étape 1 : Créer un coffre
1. Connectez-vous au [portail Azure](https://portal.azure.com).
2. Cliquez sur Nouveau > Gestion > Sauvegarde et récupération de sites (OMS). Vous pouvez également cliquer sur Parcourir > Coffre Recovery Services > Ajouter.
3. Dans **Nom** , spécifiez un nom convivial permettant d’identifier le coffre. Si vous avez plusieurs abonnements, sélectionnez-en un.
4. Dans **Groupe de ressources**, créez un groupe de ressources Azure ou sélectionnez un groupe existant. Spécifiez une région Azure pour remplir les champs requis.
5. Dans **Emplacement**, sélectionnez la région géographique du coffre. Pour vérifier les régions prises en charge, consultez [Tarification d’Azure Site Recovery](https://azure.microsoft.com/pricing/details/site-recovery/).
6. Si vous souhaitez accéder rapidement au coffre à partir du tableau de bord, cliquez sur Épingler au tableau de bord, puis sur Créer.
7. Le nouveau coffre s’affiche dans la zone Tableau de bord > Toutes les ressources, et dans le panneau Coffres Recovery Services principal.

## <a name="step-2-configure-the-vault-and-download-inmage-scout-components"></a>Étape 2 : Configurer le coffre et télécharger les composants InMage Scout
1. Dans le panneau Coffres Recovery Services, choisissez votre coffre et cliquez sur Paramètres.
2. Dans **Paramètres** > **Prise en main**, cliquez sur **Site Recovery** > Étape 1 : **Préparer l’infrastructure** > **Objectif de la protection**.
3. Dans **Objectif de la protection**, sélectionnez Vers le site de récupération, puis Oui, avec hyperviseur vSphere VMware. Puis cliquez sur OK.
4. Dans le **programme d’installation Scout**, cliquez sur Télécharger pour télécharger la clé d’inscription et le logiciel InMage Scout 8.0.1 GA. Les fichiers d’installation pour tous les composants requis figurent dans le fichier .zip téléchargé.

## <a name="step-3-install-component-updates"></a>Étape 3 : Installer les mises à jour de composants
Consultez les dernières [mises à jour](#updates). Vous installerez les fichiers de mises à jour sur les serveurs dans l’ordre suivant :

1. Serveur RX, s'il existe
2. Serveurs de configuration
3. Serveurs de traitement
4. Serveurs cibles maîtres
5. Serveurs vContinuum
6. Serveur source (Windows et Linux)

Installez les mises à jour comme suit :

1. Téléchargez le fichier .zip de [mise à jour](https://aka.ms/asr-scout-update4). Ce fichier .zip contient les fichiers suivants :

   * RX_8.0.4.0_GA_Update_4_8725872_16Sep16.tar.gz
   * CX_Windows_8.0.4.0_GA_Update_4_8725865_14Sep16.exe
   * UA_Windows_8.0.4.0_GA_Update_4_9035261_27Sep16.exe
   * UA_RHEL6-64_8.0.4.0_GA_Update_4_9035261_26Sep16.tar.gz
   * vCon_Windows_8.0.4.0_GA_Update_4_8921562_16Sep16.exe
   * UA update 4 bits pour RHEL5, OL5, OL6, SUSE 10, SUSE 11 : UA_<Linux OS>_8.0.4.0_GA_Update_4_9035261_26Sep16.tar.gz
2. Extrayez les fichiers .zip.<br>
3. **Pour le serveur RX** : copiez **RX_8.0.4.0_GA_Update_4_8725872_16Sep16.tar.gz** sur le serveur RX, puis extrayez-le. Dans le dossier extrait, exécutez **/Install**.<br>
4. **Pour le serveur de configuration/serveur de processus** : copiez **CX_Windows_8.0.4.0_GA_Update_4_8725865_14Sep16.exe** sur le serveur de configuration et le serveur de processus. Double-cliquez dessus pour l’exécuter.<br>
5. **Pour le serveur cible maître Windows** : pour mettre à jour l’agent unifié, copiez **UA_Windows_8.0.4.0_GA_Update_4_9035261_27Sep16.exe** sur le serveur cible maître. Double-cliquez dessus pour l’exécuter. Notez que l’agent unifié s’applique aussi au serveur source. Vous devez l’installer aussi sur le serveur source, comme indiqué plus loin dans cette liste.<br>
6. **Pour le serveur vContinuum** : copiez **vCon_Windows_8.0.4.0_GA_Update_4_8921562_16Sep16.exe** sur le serveur vContinuum.  Vérifiez que vous avez fermé l’Assistant vContinuum. Double-cliquez sur le fichier pour l’exécuter.<br>
7. **Pour le serveur cible maître Linux** : pour mettre à jour l’agent unifié, copiez **UA_RHEL6-64_8.0.4.0_GA_Update_4_9035261_26Sep16.tar.gz** sur le serveur cible maître et extrayez-le. Dans le dossier extrait, exécutez **/Install**.<br>
8. **Pour le serveur source Windows** : pour mettre à jour l’agent unifié, copiez **UA_Windows_8.0.4.0_GA_Update_4_9035261_27Sep16.exe** sur le serveur source. Double-cliquez dessus pour l’exécuter.<br>
9. **Pour le serveur source Linux** : pour mettre à jour l’agent unifié, copiez la version correspondante du fichier de l’agent utilisateur sur le serveur Linux et extrayez-le. Dans le dossier extrait, exécutez **/Install**.  Par exemple : pour le serveur RHEL 6.7 64 bits, copiez **UA_RHEL6-64_8.0.4.0_GA_Update_4_9035261_26Sep16.tar.gz** sur le serveur et extrayez-le. Dans le dossier extrait, exécutez **/Install**.

## <a name="step-4-set-up-replication"></a>Étape 4 : Configurer la réplication
1. Configurez la réplication entre les sites VMware sources et cibles.
2. Pour obtenir des instructions, consultez la documentation InMage Scout téléchargée avec le produit. Vous pouvez également accéder à la documentation comme suit :

   * [Notes de publication](https://aka.ms/asr-scout-release-notes)
   * [Matrice de compatibilité](https://aka.ms/asr-scout-cm)
   * [Guide d’utilisation](https://aka.ms/asr-scout-user-guide)
   * [Guide d’utilisation de RX](https://aka.ms/asr-scout-rx-user-guide)
   * [Guide d'installation rapide](https://aka.ms/asr-scout-quick-install-guide)

## <a name="updates"></a>Mises à jour
### <a name="azure-site-recovery-scout-801-update-4"></a>Azure Site Recovery Scout 8.0.1 Update 4
Scout Update 4 est une mise à jour cumulative. Celle-ci possède tous les correctifs de la mise à jour 1 à la mise à jour 3, ainsi que les nouveaux correctifs de bogues et améliorations ci-dessous :

**Prise en charge de nouvelles plateformes**

* Ajout de la prise en charge pour vCenter/vSphere 6.0, 6.1 et 6.2
* Ajout de la prise en charge des systèmes d’exploitation Linux suivants :
  * Red Hat Enterprise Linux (RHEL) 7.0, 7.1 et 7.2
  * CentOS 7.0, 7.1 et 7.2
  * Red Hat Enterprise Linux (RHEL) 6.8
  * CentOS 6.8

> [!NOTE]
> RHEL/CentOS 7 64 bits **InMage_UA_8.0.1.0_RHEL7-64_GA_06Oct2016_release.tar.gz** est fourni avec le package Scout GA de base **InMage_Scout_Standard_8.0.1 GA.zip**. Téléchargez le package Scout GA à partir du portail, comme indiqué à [l’étape 1](#step-1-create-a-vault).
>
>

**Résolutions de bogues et améliorations**

* Amélioration de la gestion de l’arrêt pour les systèmes d’exploitation Linux et clones suivants pour éviter les problèmes indésirables liés à la resynchronisation.
  * Red Hat Enterprise Linux (RHEL) 6.x
  * Oracle Linux (OL) 6.x
* Pour Linux, les autorisations complètes d’accès au dossier dans le répertoire d’installation de l’agent unifié sont à présent restreintes à l’utilisateur local uniquement.
* Concernant le problème d’expiration de Windows lors de l’émission d’un signet de cohérence distribué commun sur les applications distribuées très chargées telles que les clusters SQL et SharePoint.
* Ajout d’un correctif lié aux journaux dans le programme d’installation de base CX.
* Ajout d’un lien de téléchargement VMware vCLI 6.0 au programme d’installation de base du serveur cible maître Windows.
* Ajout de plusieurs vérifications et journaux pour les modifications de configuration réseau pendant le basculement et les tests de récupération d’urgence.
* Il arrive que les informations de rétention ne soient pas signalées pour CX.  
* Pour un cluster physique, l’opération de redimensionnement du volume via l’Assistant vContinuum échoue lors de la réduction du volume source.
* Échec de la protection du cluster avec l’erreur « Impossible de trouver la signature de disque » lorsque le disque de cluster est le disque PRDM.
* Blocage du serveur de transport cxps en raison de l’exception hors limites.
* Le nom du serveur et les colonnes IP sont à présent redimensionnables dans la page d’installation push de l’Assistant vContinuum.
* Améliorations de l’API RX
  * Fournit les cinq derniers points de cohérence communs disponibles (balises Only Guaranteed).
  * Fournit des informations sur la capacité et l’espace disponible pour tous les appareils protégés.
  * Fournit l’état du pilote Scout sur le serveur source.

> [!NOTE]
> * Le package de base **InMage_Scout_Standard_8.0.1_GA.zip** a maintenant mis à jour le programme d’installation de base CX **InMage_CX_8.0.1.0_Windows_GA_26Feb2015_release.exe** et le programme d’installation de base du serveur cible maître Windows **InMage_Scout_vContinuum_MT_8.0.1.0_Windows_GA_26Feb2015_release.exe**. Pour toutes les nouvelles installations, utilisez les nouveaux bits GA du serveur cible maître Windows et CX.
> * Vous pouvez directement appliquer l’Update4 à 8.0.1 GA.
> * Les mises à jour du serveur de configuration et de RX ne peuvent pas être annulées après avoir été appliquées sur le système.
>
>

### <a name="azure-site-recovery-scout-801-update-3"></a>Azure Site Recovery Scout 8.0.1 Update 3
Update 3 comprend les résolutions de bogues et améliorations suivantes :

* L’inscription du serveur de configuration et de RX dans le coffre Site Recovery échoue quand ils sont derrière un proxy.
* Le nombre d’heures duquel l’objectif de point de récupération (RPO) n’est pas atteint n’est pas mis à jour dans le rapport d’intégrité.
* Le serveur de configuration ne se synchronise pas avec RX quand les détails du matériel ESX ou du réseau contiennent des caractères UTF-8.
* Le démarrage des contrôleurs de domaine Windows Server 2008 R2 échoue après la récupération.
* La synchronisation hors connexion ne fonctionne pas comme prévu.
* Après le basculement de machine virtuelle (VM), la suppression de la paire de réplication reste bloquée dans l’interface utilisateur CX pendant un laps de temps prolongé, et les utilisateurs ne peuvent pas terminer l’opération de restauration ou de reprise.
* Les opérations d’instantané globales effectuées par la tâche de cohérence ont été optimisées pour réduire les déconnexions d’applications telles que les clients SQL.
* Les performances de l’outil de cohérence (VACP.exe) ont été améliorées en réduisant l’utilisation de la mémoire requise pour créer des instantanés sur Windows.
* Le service d’installation push se bloque quand le mot de passe comporte plus de 16 caractères.
* vContinuum ne vérifie et ne demande pas de nouvelles informations d’identification vCenter lors de leur modification.
* Sur Linux, le gestionnaire de cache cible maître (cachemgr) ne télécharge pas les fichiers à partir du serveur de traitement, ce qui provoque la limitation de la paire de réplication.
* Quand l’ordre des disques du cluster de basculement physique (MSCS) n’est pas le même sur tous les nœuds, la réplication n’est pas définie pour certains volumes de cluster.
  <br/>Notez que le cluster doit être reprotégé pour tirer parti de ce correctif.  
* La fonctionnalité SMTP ne fonctionne pas comme attendu après la mise à niveau de RX de Scout 7.1 vers Scout 8.0.1.
* Des statistiques ont été ajoutées au journal pour l’opération de restauration, pour suivre la durée nécessaire à cette dernière.
* Ajout de la prise en charge des systèmes d’exploitation Linux sur le serveur source :
  * Red Hat Enterprise Linux (RHEL) 6 Update 7
  * CentOS 6 Update 7
* L’interface utilisateur RX et CX peut maintenant afficher la notification pour la paire qui entre en mode bitmap.
* Les correctifs de sécurité suivants ont été ajoutés dans RX :

| **Description du problème** | **Procédures de mise en œuvre** |
| --- | --- |
| Contournement de l’autorisation par falsification de paramètres |Accès restreint pour des utilisateurs non applicables. |
| Falsification de requête intersite |Implémentation du concept de jeton de page généré aléatoirement pour chaque page. <br/>En voici le résultat observable : <li> Il existe une seule instance d’authentification unique pour un même utilisateur.</li><li>L’actualisation de page ne fonctionne pas. Elle redirige vers le tableau de bord.</li> |
| Téléchargement de fichiers malveillants |Fichiers restreints à certaines extensions. Extensions autorisées : 7z, aiff, asf, avi, bmp, csv, doc, docx, fla, flv, gif, gz, gzip, jpeg, jpg, log, mid, mov, mp3, mp4, mpc, mpeg, mpg, ods, odt, pdf, png, ppt, pptx, pxd, qt, ram, rar, rm, rmi, rmvb, rtf, sdc, sitd, swf, sxc, sxw, tar, tgz, tif, tiff, txt, vsd, wav, wma, wmv, xls, xlsx, xml et zip. |
| Scripts intersites permanents |Ajout des validations des entrées. |

> [!NOTE]
> * Toutes les mises à jour de Site Recovery sont cumulatives. Update 3 contient tous les correctifs d’Update 1 et 2. Vous pouvez directement appliquer l’Update3 à 8.0.1 GA.
> * Les mises à jour du serveur de configuration et de RX ne peuvent pas être annulées après avoir été appliquées sur le système.
>
>

### <a name="azure-site-recovery-scout-801-update-2-update-03dec15"></a>Azure Site Recovery Scout 8.0.1 Update 2 (Update 03Dec15)
Les révisions apportées dans Update 2 sont les suivantes :

* **Serveur de configuration**: résolution d’un problème affectant l’exécution de la fonctionnalité de contrôle (gratuite pendant 31 jours) en cas d’inscription du serveur auprès de Site Recovery.
* **Agent unifié**: résolution d’un problème dans Update 1 empêchant l’installation de la mise à jour sur le serveur cible maître lors de sa mise à niveau de la version 8.0 à la version 8.0.1.

### <a name="azure-site-recovery-scout-801-update-1"></a>Azure Site Recovery Scout 8.0.1 Update 1
Update 1 contient les correctifs de bogues et les nouvelles fonctionnalités suivants :

* 31 jours de protection gratuite par instance de serveur. Cela vous permet de tester les fonctionnalités ou de procéder à une validation technique.
  * Toutes les opérations sur le serveur, notamment le basculement et la restauration, sont gratuites pendant les 31 premiers jours à partir de l’heure à laquelle un serveur a été protégé à l’aide de Site Recovery Scout.
  * À compter du 32e jour, chaque serveur protégé sera facturé au tarif d’instance standard pour la protection Azure Site Recovery sur un site appartenant au client.
  * À tout moment, le nombre de serveurs protégés actuellement facturés est disponible sur le tableau de bord du coffre Azure Site Recovery.
* Ajout de la prise en charge de vSphere vCLI (Command-Line Interface) 5.5 Update 2.
* Ajout de la prise en charge des systèmes d'exploitation Linux sur le serveur source :
  * RHEL 6 Update 6
  * RHEL 5 Update 11
  * CentOS 6 Update 6
  * CentOS 5 Update 11
* Correctifs de bogues pour résoudre les problèmes suivants :
  * L’inscription du coffre échoue pour le serveur de configuration ou le serveur RX.
  * Les volumes de cluster n’apparaissent pas comme prévu quand les machines virtuelles en cluster sont à nouveau protégées lors de leur reprise.
  * La restauration automatique échoue quand le serveur cible maître est hébergé sur un autre serveur ESXi que les machines virtuelles de production locales.
  * Les autorisations du fichier de configuration sont modifiées lors de la mise à niveau vers la version 8.0.1, ce qui affecte la protection et les opérations.
  * Le seuil de resynchronisation n’est pas appliqué comme prévu, entraînant un comportement de réplication incohérent.
  * Les paramètres RPO n’apparaissent pas correctement dans l’interface du serveur de configuration. La valeur de données non compressée apparaît par erreur comme valeur compressée.
  * L’opération Remove n’effectue pas la suppression prévue dans l’Assistant vContinuum, et la réplication n’est pas supprimée de l’interface du serveur de configuration.
  * Dans l’Assistant vContinuum, le disque est désactivé automatiquement quand vous cliquez sur **Details** dans la vue de disque pendant la protection des machines virtuelles MSCS.
  * Pendant le scénario physique-à-virtuel (P2V), les services HP requis, tels que CIMnotify et CqMgHost, ne passent pas à l’état manuel dans la récupération de machine virtuelle. Cela allonge la durée de démarrage.
  * La protection de machine virtuelle Linux échoue quand le serveur cible maître contient plus de 26 disques.

## <a name="next-steps"></a>Étapes suivantes
Publiez vos questions sur le [Forum Azure Recovery Services](https://social.msdn.microsoft.com/forums/azure/home?forum=hypervrecovmgr).



<!--HONumber=Jan17_HO5-->


