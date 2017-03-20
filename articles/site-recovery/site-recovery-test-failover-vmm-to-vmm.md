---
title: "Test de basculement (VMM vers VMM) dans Site Recovery (Azure) | Microsoft Docs"
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
ms.date: 2/15/2017
ms.author: pratshar
translationtype: Human Translation
ms.sourcegitcommit: 6b1a5b2879a7b98ec4ad3e8ebbc9e95c0740d89f
ms.openlocfilehash: 3aaa005319b1ce2a10cd913c63b31860d31b797e
ms.lasthandoff: 02/23/2017


---
# <a name="test-failover-vmm-to-vmm-in-site-recovery"></a>Test de basculement (VMM vers VMM) dans Site Recovery
> [!div class="op_single_selector"]
> * [Test de basculement vers Azure](./site-recovery-test-failover-to-azure.md)
> * [Test de basculement (VMM à VMM)](./site-recovery-test-failover-vmm-to-vmm.md)


Cet article fournit des informations et des instructions relatives à l’exécution d’un test de basculement ou d’un test de récupération d’urgence de machines virtuelles et de serveurs physiques protégés par Site Recovery en utilisant un site local géré par VMM comme site de récupération. 

Publier des commentaires ou des questions au bas de cet article, ou sur le [Forum Azure Recovery Services](https://social.msdn.microsoft.com/forums/azure/home?forum=hypervrecovmgr).

Le test de basculement permet de valider votre stratégie de réplication ou d’effectuer un test de récupération d’urgence sans perte de données ou temps d’arrêt. L’exécution d’un test de basculement n’a aucun impact sur la réplication en continu ou sur votre environnement de production. Le test de basculement peut être effectué sur une machine virtuelle ou sur un [plan de récupération](site-recovery-create-recovery-plans.md). Lors du déclenchement d’un test de basculement, vous devez spécifier le réseau auquel les machines virtuelles de test se connecteraient. Une fois le test de basculement déclenché, vous pouvez suivre la progression dans la page **Tâches**.  


## <a name="preparing-infrastructure-for-test-failover"></a>Préparation de l’infrastructure pour le test de basculement
* Si vous souhaitez exécuter un test de basculement à l’aide d’un réseau existant, vous devez préparer les systèmes Active Directory, DHCP et DNS de ce réseau.
* Si vous souhaitez exécuter un test de basculement via l’option de création automatique des réseaux de machines virtuelles, ajoutez une étape manuelle avant Group1 dans le plan de récupération que vous utiliserez pour le basculement de test. Ensuite, ajoutez les ressources d’infrastructure au réseau créé de manière automatique avant de procéder au basculement de test.

### <a name="things-to-note"></a>Points à noter
* Lors de la réplication vers un site secondaire, le type de réseau utilisé par l’ordinateur de réplication ne doit pas nécessairement correspondre au type de réseau logique utilisé pour le test de basculement. Cependant, il se peut que certaines combinaisons ne fonctionnent pas. Si le réplica utilise l’isolement basé sur VLAN ou DHCP, le réseau de machines virtuelles associé au réplica n’a pas besoin d’un pool d’adresses IP statiques. Ainsi, l’utilisation de la fonction de virtualisation de réseau Windows pour un test de basculement ne peut pas aboutir, car aucun pool d’adresses n’est disponible. En outre, le test de basculement ne fonctionne pas si le réseau du réplica n’est associé à aucun isolement et si le réseau de test utilise la fonction de virtualisation de réseau Windows. En effet, un réseau ne présentant aucun isolement n’inclut aucun sous-réseau requis pour créer un réseau utilisant la fonction de virtualisation de réseau Windows.
* Le mode de connexion des ordinateurs virtuels de réplication aux réseaux de machines virtuelles mappés après le basculement dépend de la configuration choisie pour le réseau de machines virtuelles dans la console VMM :
  * **Réseau de machines virtuelles configuré sans isolement ou isolement VLAN**: si le protocole DHCP est défini pour le réseau de machines virtuelles, l’ordinateur virtuel de réplication est connecté à l’ID VLAN au moyen des paramètres spécifiés pour le site réseau dans le réseau logique associé. La machine virtuelle reçoit son adresse IP du serveur DHCP disponible. Vous n’avez pas besoin de définir un pool d’adresses IP statiques pour le réseau de machines virtuelles cible. Si un pool d’adresses IP statiques est utilisé pour le réseau de machines virtuelles, l’ordinateur virtuel de réplication est connecté à l’ID VLAN au moyen des paramètres spécifiés pour le site réseau dans le réseau logique associé. La machine virtuelle reçoit son adresse IP du pool défini pour le réseau de machines virtuelles. Si aucun pool d’adresses IP statiques n’est défini sur le réseau de machines virtuelles cible, le processus d’allocation d’une adresse IP échoue. Le pool d’adresses IP doit être créé sur les serveurs VMM source et cible que vous allez utiliser à des fins de protection et de récupération.
  * **Réseau de machines virtuelles avec virtualisation de réseau Windows**: si un réseau de machines virtuelles est configuré avec ce paramètre, un pool statique doit être défini pour le réseau de machines virtuelles cible, que le réseau de machines virtuelles source soit configuré pour utiliser le protocole DHCP ou un pool d’adresses IP statiques ou non. Si vous définissez le protocole DHCP, le serveur VMM cible joue le rôle de serveur DHCP et fournit une adresse IP provenant du pool défini pour le réseau de machines virtuelles cible. Si l’utilisation d’un pool d’adresses IP statiques est définie pour le serveur source, le serveur VMM cible alloue une adresse IP à partir du pool. Dans les deux cas, le processus d’allocation d’une adresse IP échoue si aucun pool d’adresses IP statiques n’est défini.


### <a name="prepare-dhcp"></a>Préparer le service DHCP
Si les machines virtuelles impliquées dans le test de basculement utilisent le protocole DHCP, un serveur DHCP de test doit être créé dans le réseau isolé est créé pour les besoins du test de basculement.

### <a name="prepare-active-directory"></a>Préparation du système Active Directory
Pour exécuter un test de basculement afin de tester des applications, vous devez créer une copie de l’environnement Active Directory de production dans votre environnement de test. Consultez la rubrique [Considérations en matière de test de basculement](site-recovery-active-directory.md#test-failover-considerations) pour plus de détails. 

### <a name="prepare-dns"></a>Préparer le service DNS
Préparer un serveur DNS pour le test de basculement en procédant comme suit :

* **DHCP**: si les machines virtuelles utilisent DHCP, l’adresse IP du serveur DNS de test doit être mise à jour sur le serveur DHCP de test. Si vous utilisez un type de réseau associé à la virtualisation de réseau Windows, le serveur VMM joue le rôle de serveur DHCP. Par conséquent, l’adresse IP du serveur DNS doit être mise à jour dans le réseau de test de basculement. Dans ce cas, les machines virtuelles s’enregistrent auprès du serveur DNS pertinent.
* **Adresse statique**: si les machines virtuelles utilisent une adresse IP statique, l’adresse IP du serveur DNS de test doit être mise à jour dans le réseau de test de basculement. Vous devrez peut-être mettre à jour le service DNS en indiquant l’adresse IP des machines virtuelles de test. À cette fin, vous pouvez utiliser l’exemple de script suivant :

        Param(
        [string]$Zone,
        [string]$name,
        [string]$IP
        )
        $Record = Get-DnsServerResourceRecord -ZoneName $zone -Name $name
        $newrecord = $record.clone()
        $newrecord.RecordData[0].IPv4Address  =  $IP
        Set-DnsServerResourceRecord -zonename $zone -OldInputObject $record -NewInputObject $Newrecord



## <a name="run-a-test-failover"></a>Exécution d’un test de basculement
Cette procédure explique comment exécuter un test de basculement pour un plan de récupération. Vous pouvez également exécuter le basculement d’une machine virtuelle ou d’un serveur physique unique, via l’onglet **Machines virtuelles** .

![Test Failover](./media/site-recovery-test-failover-vmm-to-vmm/TestFailover.png)

1. Sélectionnez **Plans de récupération** > *nom_planrécupération*. Cliquez sur **Type de basculement** > **Test Type de basculement**.
1. Dans le panneau **Test de basculement**, indiquez le mode à utiliser pour la connexion des machines virtuelles aux réseaux après le test de basculement. Pour plus d’informations, consultez les [options réseau](#network-options-in-site-recovery).
1. Effectuez un suivi de l’opération sur l’onglet **Tâches** . 
1. Une fois l’opération terminée, vérifiez que les machines virtuelles démarrent correctement.
1. Une fois que vous avez terminé, cliquez sur **Nettoyer le test de basculement de nettoyage** sur le plan de récupération. Cliquez sur **Notes** pour consigner et enregistrer d’éventuelles observations associées au test de basculement. Cette opération supprimera les machines et les réseaux virtuels qui ont été créés au cours du test de basculement. 


## <a name="network-options-in-site-recovery"></a>Options réseau de Site Recovery

Lorsque vous exécutez un test de basculement, vous êtes invité à sélectionner les paramètres réseau des ordinateurs virtuels de réplication utilisés pour le test. Vous avez plusieurs possibilités.  

| **Option de test de basculement** | **Description** | **Vérification du basculement** | **Détails** |
| --- | --- | --- | --- |
| **Basculement vers un site VMM secondaire, sans réseau** |Aucun réseau de machines virtuelles n’est sélectionné. |Vérifie que les machines de test sont créées.<br/><br/>La machine virtuelle de test est créée sur l’hôte qui héberge déjà la machine virtuelle de réplication. Elle n'est pas ajoutée au cloud où se trouve la machine virtuelle de réplication. |<p>La machine ayant basculé ne sera connectée à aucun réseau.<br/><br/>La machine peut être connectée à un réseau de machines virtuelles après sa création |
| **Basculement vers un site VMM secondaire, via un réseau** |Un réseau de machines virtuelles existant est sélectionné. |Vérifie la création des machines virtuelles. |La machine virtuelle de test est créée sur l’hôte qui héberge déjà la machine virtuelle de réplication. Elle n'est pas ajoutée au cloud où se trouve la machine virtuelle de réplication.<br/><br/>Créer un réseau de machines virtuelles isolé de votre réseau de production<br/><br/>Si vous utilisez un réseau basé sur un réseau VLAN, nous vous recommandons de créer un réseau logique distinct (non utilisé en production) dans VMM, à cet effet. Ce réseau logique est utilisé pour créer des réseaux de machines virtuelles à des fins de test du basculement.<br/><br/>Le réseau logique doit être associé à au moins une des cartes réseau de tous les serveurs Hyper-V hébergeant des machines virtuelles.<br/><br/>Pour les réseaux logiques VLAN, les sites de réseau que vous ajoutez au réseau logique doivent être isolés.<br/><br/>Si vous utilisez un réseau logique basé sur la fonction de virtualisation réseau Windows, Azure Site Recovery crée automatiquement des réseaux de machines virtuelles isolés. |
| **Basculement vers un site VMM secondaire : création d’un réseau** |Un réseau de test temporaire est créé automatiquement, en fonction du paramètre que vous spécifiez dans le champ **Réseau logique** et sur les sites réseau associés. |Vérifie la création des machines virtuelles. |Utilisez cette option si le plan de récupération fait appel à plusieurs réseaux de machines virtuelles. Si vous exploitez des réseaux de virtualisation de réseau Windows, cette option peut être utilisée pour créer automatiquement des réseaux de machines virtuelles à partir des mêmes paramètres (sous-réseaux et pools d’adresses IP) que ceux du réseau de l’ordinateur virtuel de réplication. Ces réseaux de machines virtuelles sont automatiquement nettoyés une fois le test de basculement terminé.</p><p>La machine virtuelle de test est créée sur l’hôte qui héberge déjà la machine virtuelle de réplication. Elle n'est pas ajoutée au cloud où se trouve la machine virtuelle de réplication. |

> [!TIP]
> L’adresse IP donnée à une machine virtuelle durant un test de basculement est identique à l’adresse IP qu’elle recevrait durant un basculement planifié ou non planifié (à condition que l’adresse IP soit disponible dans le réseau de test de basculement). Si la même adresse IP n’est pas disponible dans le réseau de test de basculement, la machine virtuelle reçoit une autre adresse IP disponible dans le réseau de test de basculement.
>
>


## <a name="test-failover-to-a-production-network-on-recovery-site"></a>Test de basculement vers un réseau de production sur le site de récupération 
Lorsque vous effectuez un test de basculement, nous vous conseillons de choisir un réseau différent de celui du site de récupération de production que vous avez indiqué dans **Mappage réseau**. Cependant, si vous voulez vraiment valider la connectivité réseau de bout en bout dans une machine virtuelle basculée, tenez compte des points suivants :

1. Assurez-vous que la machine virtuelle principale est arrêtée lorsque vous effectuez le test de basculement. Si ce n’est pas le cas, deux machines virtuelles avec la même identité seront exécutées simultanément dans le même réseau, ce qui peut avoir des conséquences indésirables. 
1. Les modifications apportées aux machines virtuelles du test de basculement seront perdues au moment du nettoyage de ces dernières. Ces modifications ne seront pas répliquées vers la machine virtuelle principale.
1. Cette manière d’effectuer faire le test entraîne un temps d’arrêt de votre application de production. Les utilisateurs de l’application doivent être invités à ne pas utiliser l’application au cours du test de récupération d’urgence.  


## <a name="next-steps"></a>Étapes suivantes
Une fois que votre tentative de test de basculement a réussi, vous pouvez essayer d’effectuer un [basculement](site-recovery-failover.md).

