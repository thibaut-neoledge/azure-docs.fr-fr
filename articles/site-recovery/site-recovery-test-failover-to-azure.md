---
title: "Test de basculement vers Azure dans Site Recovery | Microsoft Docs"
description: "En savoir plus sur l’exécution d’un test de basculement depuis un site local vers Microsoft Azure"
services: site-recovery
documentationcenter: 
author: prateek9us
manager: gauravd
editor: 
ms.assetid: 44813a48-c680-4581-a92e-cecc57cc3b1e
ms.service: site-recovery
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: storage-backup-recovery
ms.date: 07/04/2017
ms.author: pratshar
ms.translationtype: Human Translation
ms.sourcegitcommit: f537befafb079256fba0529ee554c034d73f36b0
ms.openlocfilehash: 54f62af6abcdd38254fd5379b95baa05656dc90b
ms.contentlocale: fr-fr
ms.lasthandoff: 07/08/2017


---
# <a name="test--failover-to-azure-in-site-recovery"></a>Test de basculement vers Azure dans Site Recovery



Cet article fournit des informations et des instructions relatives à l’exécution d’un test de basculement ou d’un test de récupération d’urgence de machines virtuelles et de serveurs physiques protégés par Site Recovery en utilisant Azure comme site de récupération.

Publier des commentaires ou des questions au bas de cet article, ou sur le [Forum Azure Recovery Services](https://social.msdn.microsoft.com/forums/azure/home?forum=hypervrecovmgr).

Le test de basculement permet de valider votre stratégie de réplication ou d’effectuer un test de récupération d’urgence sans perte de données ou temps d’arrêt. L’exécution d’un test de basculement n’a aucun impact sur la réplication en continu ou sur votre environnement de production. Le test de basculement peut être effectué sur une machine virtuelle ou sur un [plan de récupération](site-recovery-create-recovery-plans.md). Lors du déclenchement d’un test de basculement, vous devez spécifier le réseau auquel les machines virtuelles de test se connecteraient. Une fois le test de basculement déclenché, vous pouvez suivre la progression dans la page **Tâches**.  


## <a name="supported-scenarios"></a>Scénarios pris en charge
Le test de basculement est pris en charge dans tous les scénarios de déploiement autres que le [déploiement d’un site VMware hérité vers Azure](site-recovery-vmware-to-azure-classic-legacy.md). Le test de basculement n’est pas non plus pris en charge lorsque la machine virtuelle a été basculée vers Azure.  


## <a name="run-a-test-failover"></a>Exécution d’un test de basculement
Cette procédure explique comment exécuter un test de basculement pour un plan de récupération. Vous pouvez également exécuter un test de basculement pour une machine unique en utilisant l’option appropriée sur celle-ci.

![Test Failover](./media/site-recovery-test-failover-to-azure/TestFailover.png)


1. Sélectionnez **Plans de récupération** > *nom_planrécupération*. Cliquez sur **Test de basculement**.
1. Sélectionnez un **point de récupération** vers lequel basculer. Vous pouvez utiliser l’une des options suivantes :
    1.  **Dernier point dans le temps traité** : cette option permet de basculer toutes les machines virtuelles du plan de récupération vers le dernier point de récupération ayant déjà été traité par le service Site Recovery. Lorsque vous effectuez un test de basculement d’une machine virtuelle, la date et l’heure du dernier point de récupération traité sont également affichées. Si vous effectuez le basculement d’un plan de récupération, vous pouvez accéder à une machine virtuelle individuelle et consulter la vignette **Latest Recovery Points** (Derniers points de récupération) pour obtenir ces informations. Comme aucun temps n’est passé à traiter les données non traitées, cette option assure un basculement avec un objectif de délai récupération (RTO) faible.
    1.  **Dernier point dans le temps cohérent de l’application** : cette option permet de basculer toutes les machines virtuelles du plan de récupération vers le dernier point de récupération cohérent d’application ayant déjà été traité par le service Site Recovery. Lorsque vous effectuez un test de basculement d’une machine virtuelle, la date et l’heure du dernier point de récupération cohérent d’application sont également affichées. Si vous effectuez le basculement d’un plan de récupération, vous pouvez accéder à une machine virtuelle individuelle et consulter la vignette **Latest Recovery Points** (Derniers points de récupération) pour obtenir ces informations.
    1.  **Dernier point dans le temps** : cette option permet de traiter d’abord toutes les données qui ont été envoyées au service de récupération de site afin de créer un point de récupération pour chaque machine virtuelle avant de basculer les machines virtuelles vers celui-ci. Cette option offre l’objectif de point de récupération (RPO) le plus faible, car la machine virtuelle créée après le basculement disposera de toutes les données qui ont été répliquées vers le service de récupération de site lorsque le basculement a été déclenché.
    1.  **Latest multi-VM processed** (Dernier point multimachine virtuelle traité) : cette option est disponible uniquement pour les plans de récupération qui incluent au moins une machine virtuelle avec la cohérence multimachine virtuelle activée. Les machines virtuelles appartenant à un groupe de réplication basculent vers le point de récupération multimachine virtuelle cohérent commun. Les autres machines virtuelles basculent vers leur dernier point de récupération traité.  
    1.  **Latest multi-VM app-consistent** (Dernier point d’application multimachine virtuelle cohérent) : cette option est disponible uniquement pour les plans de récupération qui incluent au moins une machine virtuelle avec la cohérence multimachine virtuelle activée. Les machines virtuelles appartenant à un groupe de réplication basculent vers le dernier point de récupération d’application multimachine virtuelle cohérent commun. Les autres machines virtuelles basculent vers leur dernier point de récupération d’application cohérent. 
    1.  **Personnalisé** : si vous effectuez un test de basculement d’une machine virtuelle, vous pouvez utiliser cette option pour réaliser le basculement vers un point de récupération spécifique.
1. **Réseau virtuel Azure** : spécifiez le réseau virtuel Azure où les machines virtuelles seront créées. Site Recovery tente de créer les machines virtuelles de test dans un sous-réseau du même nom et à l’aide de la même adresse IP que dans les paramètres **Calcul et réseau** de la machine virtuelle. Si le réseau virtuel Azure indiqué pour le test de basculement ne contient pas de sous-réseau du même nom, la machine virtuelle de test est créée dans le premier sous-réseau dans l’ordre alphabétique. Si la même adresse IP n’est pas disponible dans le sous-réseau, la machine virtuelle reçoit une autre adresse IP disponible dans le sous-réseau. Pour plus d’informations, consultez [cette section](#creating-a-network-for-test-failover).
1. Si vous effectuez le basculement vers Azure et la fonction de chiffrement des données est activée, accédez à la zone **Clé de chiffrement** et sélectionnez le certificat émis lorsque vous avez activé le chiffrement des données pendant l’installation du fournisseur. Vous pouvez ignorer cette étape si vous n’avez pas activé le chiffrement sur la machine virtuelle.
1. Effectuez un suivi de l’opération sur l’onglet **Tâches** . Vous devriez voir apparaître l’ordinateur virtuel de réplication de test sur le portail Microsoft Azure.
1. Pour initialiser une connexion RDP sur la machine virtuelle, vous devez [ajouter une adresse IP publique](site-recovery-monitoring-and-troubleshooting.md#adding-a-public-ip-on-a-resource-manager-virtual-machine) sur l’interface réseau de la machine virtuelle basculée. Si vous effectuez un basculement vers une machine virtuelle classique, vous devez [ajouter un point de terminaison](../virtual-machines/windows/classic/setup-endpoints.md) sur le port 3389
1. Une fois que vous avez terminé, cliquez sur **Nettoyer le test de basculement de nettoyage** sur le plan de récupération. Cliquez sur **Notes** pour consigner et enregistrer d’éventuelles observations associées au test de basculement. Cette opération permet de supprimer les machines virtuelles qui ont été créés au cours du test de basculement.


> [!TIP]
> Site Recovery tente de créer les machines virtuelles de test dans un sous-réseau du même nom et à l’aide de la même adresse IP que dans les paramètres **Calcul et réseau** de la machine virtuelle. Si le réseau virtuel Azure indiqué pour le test de basculement ne contient pas de sous-réseau du même nom, la machine virtuelle de test est créée dans le premier sous-réseau dans l’ordre alphabétique. Si l’adresse IP cible fait partie du sous-réseau choisi, la récupération de site tente alors de créer la machine virtuelle de test de basculement à l’aide de l’adresse IP cible. Si l’adresse IP cible ne fait pas partie du sous-réseau choisi, la machine virtuelle de test de basculement est créée à l’aide de toute adresse IP disponible dans le sous-réseau sélectionné.
>
>

## <a name="test-failover-job"></a>Tâche du test de basculement

![Test Failover](./media/site-recovery-test-failover-to-azure/TestFailoverJob.png)

Le déclenchement d’un test basculement implique les étapes suivantes :

1. Vérification des conditions requises : cette étape s’assure que toutes les conditions requises pour le basculement sont satisfaites.
1. Basculement : cette étape consiste à traiter les données et à les préparer pour les utiliser pour la création d’une machine virtuelle Azure. Si vous avez choisi le **dernier** point de récupération, cette étape crée un point de récupération à partir des données envoyées au service.
1. Démarrer : cette étape crée une machine virtuelle Azure à l’aide des données traitées à l’étape précédente.

## <a name="time-taken-for-failover"></a>Durée du basculement

Dans certains cas, le basculement des machines virtuelles nécessite une étape supplémentaire intermédiaire qui dure généralement de 8 à 10 minutes. Il s’agit des cas suivants :

* Machines virtuelles VMware utilisant une version du service Mobilité antérieure à la version 9.8
* Serveurs physiques
* Machines virtuelles VMware Linux
* Machines virtuelles Hyper-V protégées en tant que serveurs physiques
* Machines virtuelles où les pilotes suivants ne sont pas présents en tant que pilotes de démarrage
    * storvsc
    * vmbus
    * storflt
    * intelide
    * atapi
* Machines virtuelles VMware qui n’ont pas de service DHCP activé, que vous utilisiez des adresses IP statiques ou DHCP

Dans tous les autres cas, cette étape intermédiaire n’est pas nécessaire et le temps nécessaire au basculement est nettement plus faible.


## <a name="creating-a-network-for-test-failover"></a>Création d’un réseau pour le test de basculement
Lorsque vous effectuez un test de basculement, nous vous conseillons de choisir un réseau isolé de celui du site de récupération de production que vous avez indiqué dans les paramètres **Calcul et réseau** de la machine virtuelle. Lorsque vous créez un réseau virtuel Azure, celui-ci est par défaut isolé des autres réseaux. Ce réseau doit reproduire votre réseau de production :

1. Le réseau de test doit présenter le même nombre et les mêmes noms de sous-réseaux que votre réseau de production.
1. Le réseau de test doit utiliser la même plage d’adresses IP que votre réseau de production.
1. Remplacez le DNS du réseau de test par l’adresse IP que vous avez indiquée comme adresse IP cible de la machine virtuelle DNS dans les paramètres **Calcul et réseau**. Consultez la rubrique [Considérations en matière de test de basculement](site-recovery-active-directory.md#test-failover-considerations) pour plus de détails.


## <a name="test-failover-to-a-production-network-on-recovery-site"></a>Test de basculement vers un réseau de production sur le site de récupération
Lorsque vous effectuez un test de basculement, nous vous conseillons de choisir un réseau différent de celui du site de récupération de production que vous avez indiqué dans les paramètres **Calcul et réseau** de la machine virtuelle. Cependant, si vous voulez vraiment valider la connectivité réseau de bout en bout dans une machine virtuelle basculée, tenez compte des points suivants :

1. Assurez-vous que la machine virtuelle principale est arrêtée lorsque vous effectuez le test de basculement. Si ce n’est pas le cas, deux machines virtuelles avec la même identité seront exécutées simultanément dans le même réseau, ce qui peut avoir des conséquences indésirables.
1. Les modifications apportées aux machines virtuelles du test de basculement seront perdues au moment du nettoyage de ces dernières. Ces modifications ne seront pas répliquées vers la machine virtuelle principale.
1. Cette manière d’effectuer faire le test entraîne un temps d’arrêt de votre application de production. Les utilisateurs de l’application doivent être invités à ne pas utiliser l’application au cours du test de récupération d’urgence.  



## <a name="prepare-active-directory-and-dns"></a>Préparer Active Directory et DNS
Pour exécuter un test de basculement afin de tester des applications, vous devez créer une copie de l’environnement Active Directory de production dans votre environnement de test. Consultez la rubrique [Considérations en matière de test de basculement](site-recovery-active-directory.md#test-failover-considerations) pour plus de détails.

## <a name="prepare-to-connect-to-azure-vms-after-failover"></a>Préparer la connexion aux machines virtuelles Azure après le basculement

Si vous souhaitez vous connecter à des machines virtuelles Azure à l’aide de RDP après le basculement, assurez-vous que vous effectuez les actions indiquées dans le tableau.

**Type de basculement** | **Emplacement** | **Actions**
--- | --- | ---
**Machine virtuelle Azure exécutant Windows** | Sur la machine locale, avant le basculement | Pour accéder à la machine virtuelle Azure par Internet, activez la fonction RDP, vérifiez que les règles TCP et UDP sont ajoutées pour **Public** et que RDP est autorisé dans le champ **Pare-feu Windows** > **Applications autorisées**, pour tous les profils.<br/><br/> Pour permettre l’accès via une connexion site à site, activez RDP sur la machine, en vérifiant que ce dernier est autorisé dans le champ **Pare-feu Windows** -> **Applications et fonctionnalités autorisées** pour les réseaux de types **Domaine et Privé**.<br/><br/>  Vérifiez que la stratégie SAN du système d’exploitation est définie sur la valeur **OnlineAll**. [Plus d’informations](https://support.microsoft.com/kb/3031135)<br/><br/> Assurez-vous qu’aucune mise à jour de Windows n’est en attente sur la machine virtuelle lorsque vous déclenchez un basculement. Il est possible qu’une mise à jour Windows s’exécute lors du basculement, auquel cas vous ne pourrez plus vous connecter à la machine virtuelle tant que la mise à jour ne sera pas terminée. <br/><br/>
**Machine virtuelle Azure exécutant Windows** | Sur la machine virtuelle Azure après le basculement | Pour une machine virtuelle classique, [ajoutez un point de terminaison public](../virtual-machines/windows/classic/setup-endpoints.md) pour le protocole RDP (port 3389)<br/><br/>  Pour une machine virtuelle Resource Manager, [ajoutez une adresse IP publique](site-recovery-monitoring-and-troubleshooting.md#adding-a-public-ip-on-a-resource-manager-virtual-machine).<br/><br/> Les règles des groupes de sécurité réseau figurant sur la machine virtuelle basculée et le sous-réseau Azure auquel elle est connectée doivent autoriser les connexions entrantes avec le port RDP.<br/><br/> Pour une machine virtuelle Resource Manager, vous pouvez vérifier les **diagnostics de démarrage** pour examiner une capture d’écran de la machine virtuelle<br/><br/> Si vous ne pouvez pas vous connecter, vérifiez que la machine virtuelle est en cours d’exécution, puis suivez ces [conseils de dépannage](http://social.technet.microsoft.com/wiki/contents/articles/31666.troubleshooting-remote-desktop-connection-after-failover-using-asr.aspx).<br/><br/>
**Machine virtuelle Azure exécutant Linux** | Sur la machine locale, avant le basculement | Assurez-vous que le service Secure Shell, sur la machine virtuelle Azure, est défini pour démarrer automatiquement au démarrage du système.<br/><br/> Vérifiez que les règles de pare-feu autorisent une connexion SSH à ce dernier.
**Machine virtuelle Azure exécutant Linux** | Machine virtuelle Azure après le basculement | Les règles des groupes de sécurité réseau figurant sur la machine virtuelle basculée et le sous-réseau Azure auquel elle est connectée doivent autoriser les connexions entrantes avec le port SSH.<br/><br/> Pour une machine virtuelle Classic, [ajoutez un point de terminaison public](../virtual-machines/windows/classic/setup-endpoints.md) pour autoriser les connexions entrantes sur le port SSH (port TCP 22 par défaut).<br/><br/> Pour une machine virtuelle Resource Manager, [ajoutez une adresse IP publique](site-recovery-monitoring-and-troubleshooting.md#adding-a-public-ip-on-a-resource-manager-virtual-machine).<br/><br/> Pour une machine virtuelle Resource Manager, vous pouvez vérifier les **diagnostics de démarrage** pour examiner une capture d’écran de la machine virtuelle<br/><br/>



## <a name="next-steps"></a>Étapes suivantes
Une fois que votre tentative de test de basculement a réussi, vous pouvez essayer d’effectuer un [basculement](site-recovery-failover.md).

