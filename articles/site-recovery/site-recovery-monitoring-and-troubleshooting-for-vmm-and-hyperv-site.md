<properties
	pageTitle="Guide de surveillance et de résolution des problèmes pour la protection de sites VMM et Hyper-V" 
	description="Microsoft Azure Site Recovery coordonne la réplication, le basculement et la récupération des machines virtuelles hébergées, sur des serveurs locaux, vers Microsoft Azure ou un centre de données secondaire. Cet article permet de surveiller et de résoudre les problèmes de protection d’un site VMM ou Hyper-V." 
	services="site-recovery" 
	documentationCenter="" 
	authors="anbacker" 
	manager="mkjain" 
	editor=""/>

<tags 
	ms.service="site-recovery" 
	ms.devlang="na"
	ms.topic="article"
	ms.tgt_pltfrm="na"
	ms.workload="storage-backup-recovery" 
	ms.date="06/16/2015" 
	ms.author="anbacker"/>
	
# Guide de surveillance et de résolution des problèmes pour la protection de sites VMM et Hyper-V

Ce guide de surveillance et de résolution des problèmes présente des techniques de suivi d’intégrité de réplication et de résolution des problèmes pour Azure Site Recovery.

## Vue d’ensemble des composants

### Déploiement du site VMM pour la réplication entre des sites locaux

Dans le cadre de la configuration de la récupération d’urgence entre deux emplacements locaux ; le fournisseur Azure Site Recovery doit être téléchargé et installé sur le serveur VMM. Le fournisseur a besoin d’une connexion à Internet pour s’assurer que toutes les opérations déclenchées à partir du portail Azure sont transmises sur des opérations locales telles que l’activation de la protection, l’arrêt des machines virtuelles du côté principal dans le cadre de basculements, etc.

![](media/site-recovery-monitoring-and-troubleshooting-for-vmm-and-hyperv-site/image1.png)

### Déploiement du site VMM pour la réplication entre des sites locaux et Azure

Dans le cadre de la configuration de la récupération d’urgence entre des sites locaux et Azure ; le fournisseur Azure Site Recovery doit être téléchargé et installé sur le serveur VMM avec l’agent Azure Recovery Services qui doit être installé sur chaque hôte Hyper-V.

![](media/site-recovery-monitoring-and-troubleshooting-for-vmm-and-hyperv-site/image2.png)

### Déploiement du site Hyper-V pour la réplication entre des sites locaux et Azure

Cette opération est similaire à celle du déploiement VMM, la seule différence étant que le fournisseur et l’agent sont installés sur l’hôte Hyper-V lui-même.

## Opérations de surveillance de la configuration, de protection et de récupération

Chaque opération ASR est auditée et suivie sous l’onglet « TÂCHES ». Si une erreur de configuration, de protection ou de récupération survient, accédez à l’onglet TÂCHES et vérifiez s’il y a une défaillance.

![](media/site-recovery-monitoring-and-troubleshooting-for-vmm-and-hyperv-site/image3.png)

Si vous trouvez des défaillances sous la vue TÂCHES, sélectionnez la TÂCHE et cliquez sur DÉTAILS DE L’ERREUR pour cette tâche.

![](media/site-recovery-monitoring-and-troubleshooting-for-vmm-and-hyperv-site/image4.png)

Les détails des erreurs permettent d’identifier les causes possibles et des recommandations relatives au problème.

![](media/site-recovery-monitoring-and-troubleshooting-for-vmm-and-hyperv-site/image5.png)

Dans le cas ci-dessus, il semble qu’une autre opération soit en cours, à cause de laquelle la configuration de la protection a échoué. Veuillez résoudre le problème en suivant les recommandations affichées, puis cliquez sur REDÉMARRER pour réinitialiser l’opération.

![](media/site-recovery-monitoring-and-troubleshooting-for-vmm-and-hyperv-site/image6.png)

L’option REDÉMARRER n’est pas disponible pour toutes les opérations : dans ce cas, revenez à l’objet et relancez l’opération. Vous pouvez annuler chaque TÂCHE à tout moment lors de son exécution en utilisant le bouton ANNULER.

![](media/site-recovery-monitoring-and-troubleshooting-for-vmm-and-hyperv-site/image7.png)

## Surveillance de l’intégrité de la réplication de la machine virtuelle

Une surveillance centralisée et à distance du fournisseur ASR est disponible via le portail Azure pour chacune des entités protégées. Accédez à ÉLÉMENTS PROTÉGÉS, puis sélectionnez CLOUDS VMM ou GROUPES DE PROTECTION. L’onglet CLOUDS VMM est uniquement disponible pour les déploiements VMM, tandis que tous les autres scénarios ont des entités protégées, disponibles sous l’onglet GROUPES de PROTECTION.

![](media/site-recovery-monitoring-and-troubleshooting-for-vmm-and-hyperv-site/image8.png)

Sélectionnez ensuite l’entité protégée sous le cloud ou le groupe de protection correspondant. Une fois que vous avez sélectionné l’entité protégée, toutes les opérations autorisées sont affichées dans le volet inférieur.

![](media/site-recovery-monitoring-and-troubleshooting-for-vmm-and-hyperv-site/image9.png)

Comme indiqué ci-dessus, au cas où l’INTÉGRITÉ de la machine virtuelle est critique, vous pouvez cliquer sur le bouton DÉTAILS DE L’ERREUR en bas de la page pour afficher l’erreur. En fonction des « Causes probables » et des « Recommandations » indiquées pour résoudre le problème (ici, la machine virtuelle doit être resynchronisée, ce qui peut être fait à partir du portail lui-même en cliquant sur le bouton RESYNCHRONISER).

![](media/site-recovery-monitoring-and-troubleshooting-for-vmm-and-hyperv-site/image10.png)

![](media/site-recovery-monitoring-and-troubleshooting-for-vmm-and-hyperv-site/image11.png)

Remarque : si une opération active est en cours ou a échoué, accédez à la vue TÂCHES comme indiqué précédemment pour afficher l’erreur propre à la TÂCHE.

## Résolution des problèmes locaux

Connectez-vous à la console du gestionnaire Hyper-V, sélectionnez la machine virtuelle et vérifiez l’intégrité de la réplication.

![](media/site-recovery-monitoring-and-troubleshooting-for-vmm-and-hyperv-site/image12.png)

Dans ce cas, l’*Intégrité de la réplication* est indiquée comme Critique : cliquez sur *Afficher l’intégrité de la réplication* pour afficher les détails.

![](media/site-recovery-monitoring-and-troubleshooting-for-vmm-and-hyperv-site/image13.png)

#### Observateur d’événements

| Scénarios | Sources de l’événement |
|-------------------------	|:------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------	|
| Protection du site VMM | Serveur VMM <ul><li> **Journaux des applications et des services/Microsoft/VirtualMachineManager/Serveur/Admin** </li></ul> Hôte Hyper-V <ul><li> **Journaux des applications et des services/MicrosoftAzureRecoveryServices/Réplication** (Pour Azure comme cible)</li><li> **Journaux des applications et des services/Microsoft/Windows/Hyper-V-VMMS/Admin** </li></ul> |
| Protection du site Hyper-V | <ul><li> **Journaux des applications et des services/MicrosoftAzureRecoveryServices/Réplication** </li><li> **Journaux des applications et des services/Microsoft/Azure Site Recovery/Fournisseur/Opérationnel** </li><li> **Journaux des applications et des services/Microsoft/Windows/Hyper-V-VMMS/Admin** </li><ul>|

#### Options de journalisation de réplication Hyper-V

Tous les événements liés à Hyper-V Replica sont inscrits dans le journal Hyper-V-VMMS\Admin situé sous **Journaux des applications et des services\Microsoft\Windows**. De plus, un journal d’analyse peut être activé pour Hyper-V-VMMS. Pour activer ce journal, commencez par afficher les journaux d’analyse et de débogage dans l’Observateur d’événements. Ouvrez l’Observateur d’événements, puis dans le **menu Affichage**, cliquez sur **Afficher les journaux d’analyse et débogage**.

![](media/site-recovery-monitoring-and-troubleshooting-for-vmm-and-hyperv-site/image14.png)

Un journal d’analyse s’affiche sous Hyper-V-VMMS

![](media/site-recovery-monitoring-and-troubleshooting-for-vmm-and-hyperv-site/image15.png)

Puis, dans le volet **Actions**, cliquez sur **Activer le journal**. Une fois activé, il apparaît dans **Analyseur de performances** comme une session de suivi d’événement située sous **Ensembles de collecteurs de données**.

![](media/site-recovery-monitoring-and-troubleshooting-for-vmm-and-hyperv-site/image16.png)

Pour afficher les informations collectées, commencez par arrêter la session de suivi en désactivant le journal, puis enregistrez le journal et rouvrez-le dans l’Observateur d’événements ou en utilisant d’autres outils pour le convertir selon vos besoins.


## Présentation du cycle de vie d’une machine virtuelle

![](media/site-recovery-monitoring-and-troubleshooting-for-vmm-and-hyperv-site/image17.png)


## Contacter le support Microsoft

### Collection de journaux

Pour la protection de site VMM, consultez la page [Collecte de journaux ASR à l’aide d’un outil SDP (Support Diagnostics Platform, plateforme de diagnostics de support)](http://social.technet.microsoft.com/wiki/contents/articles/28198.asr-data-collection-and-analysis-using-the-vmm-support-diagnostics-platform-sdp-tool.aspx) pour collecter les journaux requis.

Pour la protection des sites de succursales Hyper-V et SMB, téléchargez l’[outil](https://dcupload.microsoft.com/tools/win7files/DIAG_ASRHyperV_global.DiagCab) et exécutez-le sur l’hôte Hyper-V pour collecter les journaux.

Pour les scénarios VMware/physiques, consultez la page [Collecte de journaux Azure Site Recovery pour la protection de sites VMware et physiques](http://social.technet.microsoft.com/wiki/contents/articles/30677.azure-site-recovery-log-collection-for-vmware-and-physical-site-protection.aspx) pour collecter les journaux requis.

L’outil SDP collecte le fichier journal en local, que vous pouvez également trouver dans un sous-dossier nommé de façon aléatoire sous **%LocalAppData%\ElevatedDiagnostics**

### Ouverture d’un ticket de support

Pour déclencher un ticket de support pour ASR, accédez au support Azure en utilisant l’URL disponible sur <http://aka.ms/getazuresupport>

## Articles de la Base de connaissances

-   [Comment conserver la lettre de lecteur pour les machines virtuelles protégées
    > http://support.microsoft.com/kb/3031135

-   [Comment résoudre les problèmes Azure Recovery
    > Services](http://support.microsoft.com/kb/3005185)

-   [Comment activer la journalisation du débogage pour Azure Site Recovery pour la protection de site dans Hyper-V
    > ](http://support.microsoft.com/kb/3033922)

-   [ASR : erreur « La ressource de cluster n’a pas pu être trouvée » lorsque vous essayez
    > d’activer la protection d’une machine virtuelle](http://support.microsoft.com/kb/3010979)
    
-   [Guide de présentation et de résolution des problèmes relatifs à la réplication Hyper-V
    > ](http://www.microsoft.com/fr-fr/download/details.aspx?id=29016)

<!---HONumber=62-->