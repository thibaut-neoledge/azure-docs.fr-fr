---
title: "Restauration automatique d’Azure vers Hyper-V | Microsoft Docs"
description: "Après avoir automatiquement restauré des machines virtuelles vers Azure, vous pouvez restaurer ces machines virtuelles vers votre site local. Découvrez comment effectuer cette restauration automatique."
services: site-recovery
documentationcenter: 
author: ruturaj
manager: gauravd
editor: 
ms.assetid: 44813a48-c680-4581-a92e-cecc57cc3b1e
ms.service: site-recovery
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: 
ms.date: 02/13/2017
ms.author: ruturajd
translationtype: Human Translation
ms.sourcegitcommit: bedb59414ed328fe2fcca2b09f3360a98c98ff60
ms.openlocfilehash: b4bdd27db343bfb4a0e47c432f726ff454f7f55a
ms.lasthandoff: 02/23/2017


---
# <a name="failback-from-azure-to-on-premises"></a>Restauration automatique d’Azure vers le site local
Cet article explique comment restaurer automatiquement des machines virtuelles Azure à partir d’Azure vers votre site local. Suivez les instructions qu’il contient lorsque vous êtes prêt à restaurer automatiquement vos machines virtuelles VMware ou vos serveurs physiques Windows/Linux après leur basculement du site local vers Azure en suivant ce [didacticiel](site-recovery-vmware-to-azure-classic.md).

## <a name="overview-of-failback"></a>Vue d’ensemble de la restauration automatique
Voici comment se déroule une restauration automatique : une fois que vous avez procédé au basculement vers Azure, vous pouvez effectuer une restauration automatique sur votre site local en quelques étapes :

1. [Reprotégez](site-recovery-how-to-reprotect.md) les machines virtuelles Azure afin qu’elles soient répliquées sur les machines virtuelles VMware qui s’exécutent sur votre site local. Pour cela vous devez également : 
    1. configurer une cible principale locale (Windows pour des machines virtuelles Windows et [Linux](site-recovery-how-to-install-linux-master-target.md) pour des machines virtuelles Linux) ;
    2. configurer un [serveur de processus](site-recovery-vmware-setup-azure-ps-resource-manager.md) ;
    3. puis lancer la procédure de [reprotection](site-recovery-how-to-reprotect.md).
5. Une fois que vos machines virtuelles Azure sont répliquées vers votre site local, initiez un basculement d’Azure vers le site local.
  
Une fois vos données restaurées automatiquement, reprotégez les machines virtuelles locales vers lesquelles vous avez procédé à la restauration automatique pour qu’elles soient répliquées vers Azure.

Pour une présentation vidéo rapide, vous pouvez également visionner la vidéo ci-dessous.
> [!VIDEO https://channel9.msdn.com/Series/Azure-Site-Recovery/VMware-to-Azure-with-ASR-Video5-Failback-from-Azure-to-On-premises/player]

### <a name="failback-to-the-original-or-alternate-location"></a>Restaurer automatiquement vers l’emplacement d’origine ou vers un autre emplacement
    
Si vous avez effectué le basculement d’une machine virtuelle VMware, vous pouvez procéder à une restauration automatique vers la même machine virtuelle source si elle existe toujours sur site. Dans ce scénario, seules les modifications delta sont restaurées automatiquement. Ce scénario est appelé restauration dans l’emplacement d’origine. Si la machine virtuelle locale n’existe pas, il existe une solution de récupération à un autre emplacement.

#### <a name="original-location-recovery"></a>Récupération à l’emplacement d’origine

Si vous procédez à une restauration automatique vers la machine virtuelle d’origine, les conditions suivantes doivent être respectées :
* Si la machine virtuelle est gérée par un serveur vCenter, l’ordinateur hôte ESX du serveur maître cible doit avoir accès à la banque de données des machines virtuelles.
* Si la machine virtuelle se trouve sur un ordinateur hôte ESX, mais n’est pas gérée par vCenter, son disque dur doit se situer dans une banque de données accessible par l’ordinateur hôte du serveur maître cible.
* Si votre machine virtuelle se trouve sur un ordinateur hôte ESX et n’utilise pas vCenter, vous devez exécuter la détection de l’ordinateur hôte ESX du serveur maître cible avant d’assurer la reprotection. Cela s’applique également si vous restaurez automatiquement des serveurs physiques.
* Vous pouvez procéder à une restauration automatique vers un vSAN ou des disques RDM si les disques existent déjà et qu’ils sont connectés à la machine virtuelle locale.

#### <a name="alternate-location-recovery"></a>Récupération à un autre emplacement
Si la machine virtuelle locale n’existe pas avant la reprotection de la machine virtuelle, elle est appelée récupération vers un autre emplacement. Ici, le flux de travail de reprotection recrée la machine virtuelle locale. Cela entraîne également le téléchargement complet de données.

* Si vous effectuez la restauration automatique vers un autre emplacement, la machine virtuelle sera récupérée sur le même ordinateur hôte ESX que celui sur lequel le serveur cible est déployé. Le magasin de données utilisé pour créer le disque sera le même magasin de données sélectionné lors de la reprotection de la machine virtuelle.
* Vous pouvez uniquement effectuer une restauration automatique vers un magasin de données VMFS. Si vous utilisez un vSAN ou RDM, la reprotection et la restauration automatique ne fonctionneront pas.
* La reprotection implique un transfert de données initial de grande taille, suivi par des modifications delta. Cela est dû au fait que la machine virtuelle n’existe pas sur le site local et que toutes les données doivent être répliquées. Cette reprotection prendra également plus de temps que la récupération de l’emplacement d’origine.
* Vous ne pouvez pas effectuer une restauration automatique vers un vSAN ou des disques RDM. Seuls de nouveaux VMDK peuvent être créés dans une banque de données VMFS.

Une machine virtuelle basculée vers Azure peut uniquement être restaurée en tant que machine virtuelle VMware (également appelée P2A2V). Ce flux s’inscrit dans le cadre de la récupération d’un autre emplacement.

* Une machine Windows Server 2008 R2 SP1 protégée et basculée vers Azure ne peut pas être restaurée.
* Veillez à découvrir au moins un serveur cible maître ainsi que les hôtes ESX/ESXi nécessaires sur lesquels vous devez effectuer une restauration automatique.

## <a name="have-you-completed-reprotection"></a>Vous avez terminé la reprotection ?
Avant de poursuivre, terminez les étapes de reprotection afin que les machines virtuelles soient à l’état répliqué et que vous puissiez opérer un basculement local.
[Reprotection d’Azure vers votre site local](site-recovery-how-to-reprotect.md)

## <a name="pre-requisites"></a>Conditions préalables
 
* Le serveur de configuration est requis en local lorsque vous effectuez une restauration automatique. Lors de la restauration automatique, la machine virtuelle doit exister dans la base de données du serveur de configuration. Sinon, la restauration automatique échouera. Vous devez donc veiller à planifier une sauvegarde régulière de votre serveur. En cas d'incident, vous devrez le restaurer avec la même adresse IP pour que la restauration automatique réussisse.
* Le serveur cible maître ne doit pas présenter de captures instantanées avant le déclenchement de la restauration automatique.

## <a name="steps-to-failback"></a>Procédure de restauration automatique

Avant de lancer la restauration automatique, **vérifiez que vous avez terminé la reprotection des machines virtuelles**. Les machines virtuelles devraient être à l’état protégé et leur intégrité devrait afficher OK. Pour reprotéger les machines virtuelles, consultez les [méthodes de reprotection](site-recovery-how-to-reprotect.md).

1. Sur la page des éléments répliqués, sélectionnez la machine virtuelle et cliquez avec le bouton droit pour sélectionner **Basculement non planifié**.
2. Dans **Confirmer le basculement** , vérifiez le sens du basculement (à partir d’Azure) et sélectionnez le point de récupération à utiliser pour le basculement (le dernier point ou le dernier point de cohérence au niveau application). Un point de cohérence au niveau application se situe derrière le dernier point dans le temps et entraîne une perte de données.
3. Site Recovery arrête les machines virtuelles Azure pendant le basculement. Après avoir vérifié que la restauration automatique s’est terminée comme prévu, vous pouvez vous assurer que les machines virtuelles Azure ont été arrêtées.

### <a name="to-what-recovery-point-can-i-failback-the-vms-to"></a>Vers quel point de récupération puis-je restaurer automatiquement les machines virtuelles ?

Lors de la restauration automatique, deux options sont disponibles pour restaurer automatiquement la machine virtuelle/le plan de récupération.

Si vous sélectionnez le dernier point traité dans le temps, toutes les machines virtuelles sont basculées vers leur dernier point disponible dans le temps. Si un groupe de réplication existe dans le plan de récupération, chaque machine virtuelle du groupe de réplication bascule vers son dernier point indépendant dans le temps.

Vous ne pouvez pas restaurer automatiquement une machine virtuelle tant qu’elle ne dispose pas au moins d’un point de récupération. Vous ne pouvez pas restaurer automatiquement un plan de récupération tant que toutes ses machines virtuelles ne disposent pas au moins d’un point de récupération.

> [!NOTE]
> Un dernier point de récupération est un point de récupération cohérent en cas d’incident.

Si vous sélectionnez le point de récupération cohérent des applications, une restauration automatique de machine virtuelle unique est effectuée à son dernier point de récupération cohérent des applications disponible. Dans le cas d’un plan de récupération avec un groupe de réplication, la récupération de chaque groupe de réplication est effectuée à son point de récupération disponible commun.
Notez que les points de récupération cohérents des applications peuvent se trouver dans le passé et qu’une perte de données est susceptible de se produire.

## <a name="next-steps"></a>Étapes suivantes

Une fois la restauration automatique terminée, vous devez valider la machine virtuelle pour garantir la suppression des machines virtuelles récupérées dans Azure.

### <a name="commit"></a>Validation
Une validation est requise pour supprimer la machine virtuelle basculée d’Azure.
Cliquez avec le bouton droit sur l’élément protégé, puis cliquez sur Valider. Ceci déclenche une tâche qui supprimera les machines virtuelles basculées dans Azure.

### <a name="reprotect-from-on-premises-to-azure"></a>Procéder à une reprotection de l’environnement local vers Azure

Une fois la validation terminée, votre machine virtuelle se trouve de nouveau sur le site local, mais elle n’est pas protégée. Pour démarrer à nouveau une réplication vers Azure, procédez comme suit :

1. Dans le coffre, sous Paramètres > Éléments répliqués, sélectionnez les machines virtuelles qui ont été restaurées automatiquement, puis cliquez sur **Reprotéger**.
2. Affectez la valeur du serveur de processus à utiliser pour renvoyer les données vers Azure.
3. Cliquez sur OK pour commencer la tâche de reprotection.

> [!NOTE]
> Après le démarrage d’une machine virtuelle en local, il faut un certain temps à l’agent pour s’inscrire à nouveau auprès du serveur de configuration (jusqu’à 15 minutes). Dans cet intervalle, la reprotection échouera et vous recevrez un message d’erreur indiquant que l’agent n’est pas installé. Patientez quelques minutes et réessayez de procéder à la reprotection.

Une fois la tâche de reprotection effectuée, la machine virtuelle est répliquée vers Azure et vous pouvez effectuer un basculement.

## <a name="common-issues"></a>Problèmes courants
* Assurez-vous que vCenter est dans un état connecté avant de procéder à une restauration automatique, sinon la déconnexion des disques et le rattachement à la machine virtuelle échouera.


