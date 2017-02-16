---
title: "Test de basculement vers Azure dans Site Recovery | Microsoft Docs"
description: "Azure Site Recovery coordonne la réplication, le basculement et la récupération des machines virtuelles et des serveurs physiques. Informez-vous sur le basculement dans Microsoft Azure ou un centre de données secondaire."
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
ms.date: 1/09/2017
ms.author: pratshar
translationtype: Human Translation
ms.sourcegitcommit: f430d81889ecf7f3210358556b99d7d88b0e7a76
ms.openlocfilehash: 8d848385863aeea43dbad3991e7776e399ac3994


---
# <a name="test--failover-to-azure-in-site-recovery"></a>Test de basculement vers Azure dans Site Recovery

Cet article fournit des informations et des instructions relatives à l’exécution d’un test de basculement ou d’un test de récupération d’urgence de machines virtuelles et de serveurs physiques protégés par Site Recovery en utilisant Azure comme site de récupération. 

Publier des commentaires ou des questions au bas de cet article, ou sur le [Forum Azure Recovery Services](https://social.msdn.microsoft.com/forums/azure/home?forum=hypervrecovmgr).

Le test de basculement permet de valider votre stratégie de réplication ou d’effectuer un test de récupération d’urgence sans perte de données ou temps d’arrêt. L’exécution d’un test de basculement n’a aucun impact sur la réplication en continu ou sur votre environnement de production. Le test de basculement peut être effectué sur une machine virtuelle ou sur un [plan de récupération](site-recovery-create-recovery-plans.md). Lors du déclenchement d’un test de basculement, vous devez spécifier le réseau auquel les machines virtuelles de test se connecteraient. Une fois le test de basculement déclenché, vous pouvez suivre la progression dans la page **Tâches**.  


## <a name="supported-scenarios"></a>Scénarios pris en charge
Le test de basculement est pris en charge dans tous les scénarios de déploiement autres que le [déploiement d’un site VMware hérité vers Azure](site-recovery-vmware-to-azure-classic-legacy.md). Le test de basculement n’est pas non plus pris en charge lorsque la machine virtuelle a été basculée vers Azure.  


## <a name="run-a-test-failover"></a>Exécution d’un test de basculement
Cette procédure explique comment exécuter un test de basculement pour un plan de récupération. Vous pouvez également exécuter un test de basculement pour une machine unique en utilisant l’option appropriée sur celle-ci. 

1. Sélectionnez **Plans de récupération** > *nom_planrécupération*. Cliquez sur **Test de basculement**.
1. Sélectionnez un **point de récupération** vers lequel basculer. Vous pouvez utiliser l’une des options suivantes :
    1.  **Dernier point dans le temps traité** : cette option permet de basculer toutes les machines virtuelles du plan de récupération vers le dernier point de récupération ayant déjà été traité par le service de récupération de site. Lorsque vous effectuez un test de basculement d’une machine virtuelle, la date et l’heure du dernier point de récupération traité sont également affichées. Si vous effectuez le basculement d’un plan de récupération, vous pouvez accéder à une machine virtuelle individuelle et consulter la vignette **Latest Recovery Points** (Derniers points de récupération) pour obtenir ces informations. Comme aucun temps n’est passé à traiter les données non traitées, cette option assure un basculement avec un objectif de délai récupération (RTO) faible. 
    1.    **Dernier point dans le temps cohérent de l’application** : cette option permet de basculer toutes les machines virtuelles du plan de récupération vers le dernier point de récupération cohérent d’application ayant déjà été traité par le service de récupération de site. Lorsque vous effectuez un test de basculement d’une machine virtuelle, la date et l’heure du dernier point de récupération cohérent d’application sont également affichées. Si vous effectuez le basculement d’un plan de récupération, vous pouvez accéder à une machine virtuelle individuelle et consulter la vignette **Latest Recovery Points** (Derniers points de récupération) pour obtenir ces informations. 
    1.    **Dernier point dans le temps** : cette option permet de traiter d’abord toutes les données qui ont été envoyées au service de récupération de site afin de créer un point de récupération pour chaque machine virtuelle avant de basculer les machines virtuelles vers celui-ci. Cette option offre l’objectif de point de récupération (RPO) le plus faible, car la machine virtuelle créée après le basculement disposera de toutes les données qui ont été répliquées vers le service de récupération de site lorsque le basculement a été déclenché. 
    1.  **Personnalisé** : si vous effectuez un test de basculement d’une machine virtuelle, vous pouvez utiliser cette option pour réaliser le basculement vers un point de récupération spécifique.
1. **Réseau virtuel Azure** : spécifiez le réseau virtuel Azure où les machines virtuelles seront créées. Le service de récupération de site tente de créer les machines virtuelles de test dans un sous-réseau du même nom et à l’aide de la même adresse IP que dans les paramètres **Calcul et réseau** de la machine virtuelle. Si le réseau virtuel Azure indiqué pour le test de basculement ne contient pas de sous-réseau du même nom, la machine virtuelle de test est créée dans le premier sous-réseau selon l’ordre alphabétique. Si la même adresse IP n’est pas disponible dans le sous-réseau, la machine virtuelle reçoit une autre adresse IP disponible dans le sous-réseau. Pour plus d’informations, consultez [cette section](#creating-a-network-for-test-failover).
1. Si vous effectuez le basculement vers Azure et la fonction de chiffrement des données est activée, accédez à la zone **Clé de chiffrement** et sélectionnez le certificat émis lorsque vous avez activé le chiffrement des données pendant l’installation du fournisseur. Vous pouvez ignorer cette étape si vous n’avez pas activé le chiffrement sur la machine virtuelle.
1. Effectuez un suivi de l’opération sur l’onglet **Tâches** . Vous devriez voir apparaître l’ordinateur virtuel de réplication de test sur le portail Microsoft Azure.
1. Pour initialiser une connexion RDP sur la machine virtuelle, vous devez [ajouter une adresse IP publique](site-recovery-monitoring-and-troubleshooting.md#adding-a-public-ip-on-a-resource-manager-virtual-machine) sur l’interface réseau de la machine virtuelle basculée. Si vous effectuez un basculement vers une machine virtuelle classique, vous devez [ajouter un point de terminaison](../virtual-machines/virtual-machines-windows-classic-setup-endpoints.md) sur le port 3389.
1. Une fois que vous avez terminé, cliquez sur **Nettoyer le test de basculement de nettoyage** sur le plan de récupération. Cliquez sur **Notes** pour consigner et enregistrer d’éventuelles observations associées au test de basculement. Cette opération supprimera les machines virtuelles qui ont été créées au cours du test de basculement. 


> [!TIP]
> Le service de récupération de site tente de créer les machines virtuelles de test dans un sous-réseau du même nom et à l’aide de la même adresse IP que dans les paramètres **Calcul et réseau** de la machine virtuelle. Si le réseau virtuel Azure indiqué pour le test de basculement ne contient pas de sous-réseau du même nom, la machine virtuelle de test est créée dans le premier sous-réseau selon l’ordre alphabétique. Si la même adresse IP n’est pas disponible dans le sous-réseau, la machine virtuelle reçoit une autre adresse IP disponible dans le sous-réseau. 
>
> 


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

## <a name="next-steps"></a>Étapes suivantes
Une fois que votre tentative de test de basculement a réussi, vous pouvez essayer d’effectuer un [basculement](site-recovery-failover.md).




<!--HONumber=Jan17_HO4-->


