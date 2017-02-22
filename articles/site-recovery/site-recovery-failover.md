---
title: Basculement dans Site Recovery | Microsoft Docs
description: "Azure Site Recovery coordonne la réplication, le basculement et la récupération des machines virtuelles et des serveurs physiques. Informez-vous sur le basculement dans Microsoft Azure ou un centre de données secondaire."
services: site-recovery
documentationcenter: 
author: rayne-wiselman
manager: jwhit
editor: 
ms.assetid: 44813a48-c680-4581-a92e-cecc57cc3b1e
ms.service: site-recovery
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: storage-backup-recovery
ms.date: 1/04/2017
ms.author: raynew
translationtype: Human Translation
ms.sourcegitcommit: b70d2d709d0940964c4220b798207adaf3551d36
ms.openlocfilehash: d31e987e636b178b5aa05722ff08707a6e53c3cf


---
# <a name="failover-in-site-recovery"></a>Basculement via Microsoft Azure Site Recovery
Le service Azure Site Recovery contribue à mettre en œuvre la stratégie de continuité des activités et de récupération d’urgence de votre entreprise en coordonnant la réplication, le basculement et la récupération de machines virtuelles et de serveurs physiques. Les machines peuvent être répliquées vers Azure ou vers un centre de données local secondaire. Pour avoir un rapide aperçu, consultez la section [Qu’est-ce qu’Azure Site Recovery ?](site-recovery-overview.md)

## <a name="overview"></a>Vue d'ensemble
Cet article fournit des informations et des instructions relatives à la récupération (basculement ou restauration automatique) des machines virtuelles et des serveurs physiques protégés par Microsoft Azure Site Recovery.

Publier des commentaires ou des questions au bas de cet article, ou sur le [Forum Azure Recovery Services](https://social.msdn.microsoft.com/forums/azure/home?forum=hypervrecovmgr).

## <a name="types-of-failover"></a>Types de basculement
Une fois la protection activée pour les serveurs physiques et les machines virtuelles et la réplication en cours, vous pouvez exécuter des basculements en cas de besoin. Microsoft Azure Site Recovery prend en charge différents types de basculement.

| **Type de basculement** | **Motif de l’exécution** | **Détails** | **Processus** |
| --- | --- | --- | --- |
| **Test de basculement** |Exécutez ce basculement pour valider votre stratégie de réplication ou effectuer un exercice de récupération d’urgence. |Aucune perte de données, ni aucun temps d’arrêt.<br/><br/>Aucun impact sur la réplication<br/><br/>Aucun impact sur votre environnement de production |Démarrez le basculement<br/><br/>Spécifiez comment les machines de test seront connectées aux réseaux après le basculement<br/><br/>Effectuez un suivi de l’opération dans l’onglet **Tâches** . Les machines de test sont créées ; elles démarrent à l’emplacement secondaire<br/><br/>Microsoft Azure : connectez-vous à la machine dans le portail Azure<br/><br/>Site secondaire : accédez à la machine sur le même hôte et dans le même cloud<br/><br/>Terminez le test et lancez le nettoyage automatique des paramètres du test de basculement. |
| **Basculement planifié** |Exécutez ce basculement pour répondre aux exigences de conformité<br/><br/>Exécutez ce basculement pour les opérations de maintenance planifiée<br/><br/>Basculez les données afin de maintenir les charges de travail en fonctionnement en cas de panne connue (coupure de courant ou phénomène climatique important annoncés au préalable<br/><br/>Effectuez cette opération pour assurer la restauration automatique après le basculement, du site principal vers le site secondaire |Aucune perte de données<br/><br/>Un temps d’arrêt survient pendant l’intervalle d’arrêt de la machine virtuelle sur le site principal, puis de redémarrage de cette machine sur le site secondaire.<br/><br/>Les machines virtuelles sont affectées, car les machines cibles deviennent des machines sources après le basculement. |Démarrez le basculement<br/><br/>Effectuez un suivi de l’opération dans l’onglet **Tâches** . Les machines sources sont arrêtées<br/><br/>Machines virtuelles de réplication démarrent sur le site secondaire<br/><br/>Microsoft Azure : connectez-vous à la machine de réplication dans le portail Azure<br/><br/>Site secondaire : accédez à la machine sur le même hôte et dans le même cloud<br/><br/>Validez le basculement |
| **Basculement non planifié** |Exécutez ce type de basculement de façon réactive lorsqu’un site principal devient inaccessible en raison d’un problème inattendu, tel qu’une attaque de virus ou une panne d’alimentation <br/><br/> Vous pouvez exécuter un basculement non planifié même si le site principal n’est pas disponible. |Perte de données en fonction des paramètres de fréquence de réplication<br/><br/>Les données sont mises à jour en fonction de leur dernière synchronisation |Démarrez le basculement<br/><br/>Effectuez un suivi de l’opération dans l’onglet **Tâches** . Le cas échéant, essayez d’arrêter les machines virtuelles et de synchroniser les données les plus récentes<br/><br/>Machines virtuelles de réplication démarrent sur le site secondaire<br/><br/>Microsoft Azure : connectez-vous à la machine de réplication dans le portail Azure<br/><br/>Site secondaire : accédez à la machine sur le même hôte et dans le même cloud<br/><br/>Validez le basculement |

Les types de basculement pris en charge dépendent de votre scénario de déploiement.

| **Sens du basculement** | **Test de basculement** | **Basculement planifié** | **Basculement non planifié** |
| --- | --- | --- | --- |
| Du site VMM principal vers le site VMM secondaire |Pris en charge |Pris en charge |Pris en charge |
| Du site VMM secondaire vers le site VMM principal |Pris en charge |Pris en charge |Pris en charge |
| Entre deux clouds (serveur VMM unique) |Pris en charge |Pris en charge |Pris en charge |
| Entre un site VMM et Microsoft Azure |Pris en charge |Pris en charge |Pris en charge |
| Entre Microsoft Azure et le site VMM |Non pris en charge |Pris en charge |Non pris en charge |
| Entre un site Hyper-V et Microsoft Azure |Pris en charge |Pris en charge |Pris en charge |
| Entre Microsoft Azure et un site Hyper-V |Non pris en charge |Pris en charge |Non pris en charge |
| Entre un site VMware et Microsoft Azure |Pris en charge (scénario amélioré)<br/><br/> Non pris en charge (ancien scénario) |Non pris en charge |Pris en charge |
| Entre un serveur physique et Microsoft Azure |Pris en charge (scénario amélioré)<br/><br/> Non pris en charge (ancien scénario) |Non pris en charge |Pris en charge |

## <a name="failover-and-failback"></a>Basculement et restauration automatique
Vous effectuez le basculement des machines virtuelles vers un site local secondaire ou vers Microsoft Azure, en fonction de votre déploiement. Une machine qui effectue un basculement vers Microsoft Azure est créée en tant que machine virtuelle Azure. Vous pouvez effectuer le basculement d’une machine virtuelle ou d’un serveur physique uniques, ou d’un plan de récupération. Les plans de récupération comprennent un ou plusieurs groupes ordonnés présentant des machines virtuelles ou serveurs physiques protégés. Ils permettent d’orchestrer le basculement de plusieurs machines, en fonction du groupe auquel elles appartiennent. [Découvrez plus d’informations](site-recovery-create-recovery-plans.md) sur les plans de récupération.

Lorsque le basculement est terminé et que vos machines fonctionnent correctement à l’emplacement secondaire, notez ce qui suit :

* Si vous avez effectué le basculement vers Microsoft Azure, une fois cette opération effectuée, les machines ne sont pas protégées ou ne sont pas en cours de réplication à l’emplacement principal ou secondaire. Dans Microsoft Azure, les machines virtuelles sont stockées dans un espace de stockage géo-répliqué qui assure la résilience, mais non la réplication.
* Si vous avez effectué un basculement non planifié vers un site secondaire, une fois cette opération effectuée, les machines de l’emplacement secondaire ne sont pas protégées ou en cours de réplication.
* Si vous avez effectué un basculement planifié vers un site secondaire, une fois cette opération effectuée, les machines de l’emplacement secondaire sont protégées.

### <a name="failback-from-secondary-site"></a>Restauration automatique à partir du site secondaire
Ce type de restauration entre un site secondaire et un site principal a recours au même processus que le basculement à partir du site principal vers un site secondaire. Une fois le basculement depuis le centre de données principal vers le centre de données secondaire validé et terminé, vous pouvez lancer une réplication inverse lorsque le site principal est disponible. Le processus de réplication inverse lance la réplication entre le site secondaire et le site principal en synchronisant le delta de données. La réplication inverse affecte aux machines virtuelles l’état protégé, mais le centre de données secondaire est toujours l’emplacement actif. Pour placer le site principal à l’emplacement actif, lancez un basculement planifié entre le site secondaire et le site principal, puis une autre opération de réplication inverse.

### <a name="failback-from-azure"></a>Restauration automatique à partir de Microsoft Azure
Si vous avez effectué le basculement vers Microsoft Azure, vos machines virtuelles sont protégées par les fonctionnalités de résilience de Microsoft Azure pour les machines virtuelles. Pour placer le site principal d’origine à l’emplacement actif, lancez un basculement planifié. Si le site d’origine n’est pas disponible, vous pouvez effectuer le basculement vers l’emplacement d’origine ou vers un autre emplacement. Pour commencer la réplication après la restauration automatique vers l’emplacement principal, lancez une réplication inverse.

### <a name="failover-considerations"></a>Remarques relatives au test de basculement
* **Adresse IP après un basculement**: par défaut, une machine après basculement présente une adresse IP différente de celle de la machine source. Si vous souhaitez conserver la même adresse IP, reportez-vous aux éléments suivants :
  * **Site secondaire**: si vous effectuez le basculement vers un site secondaire et que vous souhaitez conserver une adresse IP, [consultez cet article](http://blogs.technet.com/b/scvmm/archive/2014/04/04/retaining-ip-address-after-failover-using-hyper-v-recovery-manager.aspx) . Remarque : vous pouvez conserver une adresse IP publique, si le fournisseur de services Internet peut la prendre en charge.
  * **Microsoft Azure** : si vous effectuez le basculement vers Microsoft Azure, vous pouvez spécifier l’adresse IP que vous souhaitez attribuer dans l’onglet **Configurer** de la page de propriétés de la machine virtuelle. Vous ne pouvez pas conserver une adresse IP publique après le basculement vers Microsoft Azure. Vous pouvez conserver les espaces d’adressage non compatibles avec RFC 1918 qui sont utilisés en tant qu’adresses internes.
* **Basculement partiel**: si vous souhaitez effectuer le basculement d’une partie du site seulement, tenez compte des remarques ci-après.

  * **Site secondaire**: si vous effectuez le basculement d’une partie d’un site principal vers un site secondaire et souhaitez vous reconnecter au site principal, utilisez une connexion VPN de site à site pour connecter les applications ayant basculé vers le site secondaire avec les composants exécutés sur le site principal. Si un sous-réseau dans son ensemble fait l’objet d’un basculement, vous pouvez conserver l’adresse IP de la machine virtuelle. Par contre, si vous effectuez le basculement d’un sous-réseau partiel, vous ne pouvez conserver l’adresse IP de la machine virtuelle, car les sous-réseaux ne peuvent pas être répartis sur différents sites.
  * **Microsoft Azure**: si vous effectuez le basculement d’un site partiel vers Microsoft Azure et souhaitez vous reconnecter au site principal, utilisez une connexion VPN de site à site pour connecter une application ayant basculé vers Microsoft Azure aux composants d’infrastructure exécutés sur le site principal. Remarque : si un sous-réseau dans son ensemble fait l’objet d’un basculement, vous pouvez conserver l’adresse IP de la machine virtuelle. Par contre, si vous effectuez le basculement d’un sous-réseau partiel, vous ne pouvez conserver l’adresse IP de la machine virtuelle, car les sous-réseaux ne peuvent pas être répartis sur différents sites.
* **Lettre de lecteur** : si vous souhaitez conserver la lettre de lecteur sur les machines virtuelles après basculement, vous pouvez définir la stratégie SAN de la machine virtuelle sur **Activé**. Les disques de machines virtuelles se mettent automatiquement en ligne. [En savoir plus](https://technet.microsoft.com/library/gg252636.aspx).
* **Acheminer les demandes des clients**: Microsoft Azure Site Recovery fonctionne en parallèle avec Microsoft Azure Traffic Manager pour acheminer les demandes des clients vers votre application après le basculement.

## <a name="run-a-test-failover"></a>Exécution d’un test de basculement
Reportez-vous au document [Tester le basculement vers Azure](site-recovery-test-failover-to-azure.md) si vous effectuez un test de basculement vers Azure. Reportez-vous au document [Test de basculement sur VMM](site-recovery-test-failover-vmm-to-vmm.md) si vous effectuez un test de basculement vers un autre site local géré par un serveur VMM.  

## <a name="run-a-planned-failover-primary-to-secondary"></a>Exécuter un basculement planifié (depuis un système principal vers un système secondaire)
 Cette procédure explique comment exécuter un test de basculement planifié pour un plan de récupération. Vous pouvez également exécuter le basculement d’une machine virtuelle unique, via l’onglet **Machines virtuelles** .

1. Avant de commencer, assurez-vous que toutes les machines virtuelles dont vous voulez effectuer le basculement ont terminé la réplication initiale.
2. Sélectionnez **Plans de récupération** > *nom_planrécupération*. Cliquez sur **Type de basculement** > **Planned Type de basculement**.
3. Sur la page ** **Confirmer le basculement planifié ****, choisissez les emplacements source et cible. Notez le sens du basculement.

   * Si les basculements précédents se sont exécutés comme prévu, et si l’ensemble des serveurs de machines virtuelles sont situés à l’emplacement source ou cible, les détails du sens de basculement sont indiqués à titre informatif uniquement.
   * Si les machines virtuelles sont actives sur les emplacements source et cible, le bouton **Modifier le sens** s’affiche. Ce bouton permet de modifier et de spécifier le sens dans lequel le basculement doit s’exécuter.
4. Si vous effectuez le basculement vers Microsoft Azure et que la fonction de chiffrement des données est activée pour le cloud, accédez à la zone **Clé de chiffrement** et sélectionnez le certificat émis lorsque vous avez activé le chiffrement des données pendant l’installation du fournisseur sur le serveur VMM.
5. Lorsqu’un basculement planifié commence, la première étape consiste à arrêter les machines virtuelles pour éviter toute perte de données. Vous pouvez suivre la progression du basculement sur l’onglet **Tâches** . Si une erreur se produit lors du basculement (sur une machine virtuelle ou dans un script inclus dans le plan de récupération), le basculement planifié d’un plan de récupération s’arrête. Vous pouvez le lancer à nouveau.
6. Une fois créés, les ordinateurs virtuels de réplication sont en attente de validation. Cliquez sur **Valider** pour valider le basculement.
7. Une fois la réplication terminée, les machines virtuelles démarrent à l’emplacement secondaire.

## <a name="run-an-unplanned-failover"></a>Exécuter un basculement non planifié
Cette procédure explique comment exécuter un test de basculement non planifié pour un plan de récupération. Vous pouvez également exécuter le basculement d’une machine virtuelle ou d’un serveur physique unique, via l’onglet **Machines virtuelles** .

1. Sélectionnez **Plans de récupération** > *nom_planrécupération*. Cliquez sur **Type de basculement** > **Unplanned Type de basculement**.
2. Sur la page ** **Confirmer le basculement non planifié ****, choisissez les emplacements source et cible. Notez le sens du basculement.

   * Si les basculements précédents se sont exécutés comme prévu, et si l’ensemble des serveurs de machines virtuelles sont situés à l’emplacement source ou cible, les détails du sens de basculement sont indiqués à titre informatif uniquement.
   * Si les machines virtuelles sont actives sur les emplacements source et cible, le bouton **Modifier le sens** s’affiche. Ce bouton permet de modifier et de spécifier le sens dans lequel le basculement doit s’exécuter.
3. Si vous effectuez le basculement vers Microsoft Azure et que la fonction de chiffrement des données est activée pour le cloud, accédez à la zone **Clé de chiffrement** et sélectionnez le certificat émis lorsque vous avez activé le chiffrement des données pendant l’installation du fournisseur sur le serveur VMM.
4. Sélectionnez **Arrêter les machines virtuelles et synchroniser les dernières données** pour indiquer que le logiciel Site Recovery doit essayer d’arrêter les machines virtuelles protégées et de synchroniser les données, afin que la dernière version de ces dernières fasse l’objet d’un basculement. Si vous ne sélectionnez pas cette option, ou si la tentative n’aboutit pas, le basculement sera effectué depuis le dernier point de récupération disponible pour la machine virtuelle.
5. Vous pouvez suivre la progression du basculement sur l’onglet **Tâches** . Remarque : même si des erreurs se produisent lors d’un basculement non planifié, le plan de récupération s’exécute jusqu’à la fin.
6. Après le basculement, les machines virtuelles présentent un état de **validation en attente** . Cliquez sur **Valider** pour valider le basculement.
7. Si vous configurez la réplication afin qu’elle utilise plusieurs points de récupération, vous pouvez  choisir un point de récupération qui n’est pas nécessairement le plus récent (lequel est sélectionné par défaut). Pour cela, accédez à la zone Changer le point de récupération. Uns fois la validation effectuée, les points de récupération supplémentaires sont supprimés.
8. Lorsque la réplication est terminée, les machines virtuelles démarrent et s’exécutent à l’emplacement secondaire. Toutefois, elles ne sont pas protégées ou en cours de réplication. Lorsque le site principal est disponible, avec la même infrastructure sous-jacente, cliquez sur **Réplication inverse** pour lancer la réplication inverse. Cette opération permet de garantir que toutes les données sont répliquées vers le site principal, et que la machine virtuelle est prête pour un nouveau basculement. Une réplication inverse exécutée après un basculement non planifié entraîne une surcharge lors du transfert des données. Le processus de transfert utilise la méthode configurée pour les paramètres de réplication initiale pour le cloud.

## <a name="failback-from-secondary-to-primary"></a>Restauration automatique d’un site secondaire vers un site primaire
 Après le basculement du site principal vers l’emplacement secondaire, les machines virtuelles répliquées ne sont pas protégées par le logiciel Site Recovery et l’emplacement secondaire joue le rôle de serveur principal. Suivez ces procédures pour effectuer la restauration automatique vers le site principal d’origine. Cette procédure explique comment exécuter un test de basculement planifié pour un plan de récupération. Vous pouvez également exécuter le basculement d’une machine virtuelle unique, via l’onglet **Machines virtuelles** .

1. Sélectionnez **Plans de récupération** > *nom_planrécupération*. Cliquez sur **Type de basculement** > **Planned Type de basculement**.
2. Sur la page ** **Confirmer le basculement planifié ****, choisissez les emplacements source et cible. Notez le sens du basculement. Si le basculement depuis le site principal a fonctionné comme prévu, et si toutes les machines virtuelles se trouvent à l’emplacement secondaire, ces éléments sont fournis à titre informatif uniquement.
3. Si vous effectuez la restauration à partir de Microsoft Azure, sélectionnez différents paramètres dans la zone **Synchronisation des données**:

   * **Synchroniser les données avant le basculement (synchroniser seulement les modifications d'ordre différentiel)**: cette option réduit le temps d’arrêt des machines virtuelles, car elles sont synchronisées sans être arrêtées. Il effectue les opérations suivantes :
     * Phase 1 : Un instantané de la machine virtuelle est créé dans Microsoft Azure, puis copié sur l’hôte Hyper-V local. La machine continue de s’exécuter dans Microsoft Azure.
     * Phase 2 : La machine virtuelle est arrêtée dans Microsoft Azure, afin de ne faire l’objet d’aucune nouvelle modification. Le jeu de modification final est transféré vers le serveur local ; la machine virtuelle locale est démarrée.

    - **Synchroniser les données uniquement lors du basculement (téléchargement complet)**: utilisez cette option si vous exécutez Azure depuis une longue période. Cette option est plus rapide, car nous nous attendons à ce que la majeure partie du disque ait changé et nous ne voulons pas passer du temps au calcul de la somme de contrôle. Elle effectue un téléchargement du disque. C’est également utile lorsque la machine virtuelle locale a été supprimée.

    > [!NOTE] 
    > Nous vous recommandons d’utiliser cette option si vous exécutez Azure depuis un certain temps (un mois ou plus) ou si la machine virtuelle locale a été supprimée. Cette option n’effectue aucun calcul de somme de contrôle.
    >
    >


1. Si vous effectuez le basculement vers Microsoft Azure et que la fonction de chiffrement des données est activée pour le cloud, accédez à la zone **Clé de chiffrement** et sélectionnez le certificat émis lorsque vous avez activé le chiffrement des données pendant l’installation du fournisseur sur le serveur VMM.
2. Par défaut, le dernier point de récupération est utilisé. Cependant, vous pouvez indiquer un autre point de récupération dans la zone **Changer le point de récupération**.
3. Cochez la case pour démarrer la restauration automatique.  Vous pouvez suivre la progression du basculement sur l’onglet **Tâches** .
4. Si vous avez sélectionné l’option de synchronisation des données avant le basculement, lorsque la synchronisation initiale des données est terminée et que vous êtes prêt à arrêter les machines virtuelles dans Microsoft Azure, cliquez sur **Tâches** > <planned failover job name> **Terminer le basculement**. Cette action arrête la machine Microsoft Azure et transfère les dernières modifications apportées à la machine virtuelle locale, qui est ensuite démarrée.
5. Vous pouvez maintenant vous connecter à la machine virtuelle, afin de vérifier qu’elle fonctionne comme prévu.
6. Cette dernière présente un état de validation en attente. Cliquez sur **Valider** pour valider le basculement.
7. Afin de terminer la restauration automatique, cliquez sur **Réplication inverse** pour commencer à protéger la machine virtuelle sur le site principal.

## <a name="failback-to-an-alternate-location"></a>Restauration automatique vers un autre emplacement
Si vous avez déployé la fonction de protection entre un [site Hyper-V et Microsoft Azure](site-recovery-hyper-v-site-to-azure.md) , vous avez la possibilité d’effectuer une restauration automatique depuis Microsoft Azure vers un autre emplacement local. Cette opération est utile si vous devez configurer de nouveaux composants matériels locaux. Voici comment procéder.

1. Si vous configurez un nouveau composant matériel, installez le logiciel Windows Server 2012 R2 et le rôle Hyper-V sur le serveur.
2. Créez un commutateur réseau virtuel portant le même nom que celui du serveur d’origine.
3. Sélectionnez **Éléments protégés** -> **Groupe de protection** -> <ProtectionGroupName> -> <VirtualMachineName> que vous souhaitez restaurer de manière automatique, puis sélectionnez **Basculement planifié**.
4. Cliquez sur **Confirmer le basculement planifié** select **Créer une machine virtuelle locale si elle n’existe pas**.
5. Dans **Nom d’hôte** , sélectionnez le nouveau serveur hôte Hyper-V sur lequel vous souhaitez placer la machine virtuelle.
6. Dans le champ Synchronisation des données, nous vous recommandons de sélectionner l’option **Synchroniser les données avant le basculement**. Cela permet de réduire le temps d’arrêt des machines virtuelles, car elles sont synchronisées sans être arrêtées. Il effectue les opérations suivantes :

   * Phase 1 : Un instantané de la machine virtuelle est créé dans Microsoft Azure, puis copié sur l’hôte Hyper-V local. La machine continue de s’exécuter dans Microsoft Azure.
   * Phase 2 : La machine virtuelle est arrêtée dans Microsoft Azure, afin de ne faire l’objet d’aucune nouvelle modification. Le jeu de modification final est transféré vers le serveur local ; la machine virtuelle locale est démarrée.
7. Cochez la base pour commencer le basculement (restauration automatique).
8. Lorsque la synchronisation initiale se termine et que vous êtes prêt à arrêter la machine virtuelle dans Microsoft Azure, cliquez sur **Tâches** > <planned failover job> > **Terminer le basculement**. Cette action arrête la machine Microsoft Azure et transfère les dernières modifications apportées à la machine virtuelle locale, laquelle est ensuite démarrée.
9. Vous pouvez vous connecter à la machine virtuelle locale pour vérifier que tous les composants fonctionnent comme prévu. Ensuite, cliquez sur **Valider** pour terminer le basculement.
10. Cliquez sur **Réplication inverse** pour commencer à protéger la machine virtuelle locale.

    > [!NOTE]
    > Si vous annulez l’opération de restauration automatique à l’étape de synchronisation des données, l’état de la machine virtuelle locale sera corrompu. En effet, la synchronisation des données copie les données les plus récentes de la machine virtuelle Azure vers les disques de données locaux, et tant que la synchronisation n’est pas terminée, l’état du disque de données risque de ne pas être cohérent. Si la machine virtuelle locale est démarrée après l’annulation de la synchronisation des données, l’opération risque d’échouer. Relancez le basculement pour terminer la synchronisation des données.
    >
    >



<!--HONumber=Jan17_HO2-->


