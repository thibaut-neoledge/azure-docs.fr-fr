---
title: "Planification de la mise en réseau pour la réplication Hyper-V sur un site secondaire VMM avec Azure Site Recovery | Microsoft Docs"
description: "Cet article décrit comment planifier la mise en réseau lors de la réplication de machines virtuelles Hyper-V sur un site secondaire System Center VMM avec Azure Site Recovery."
services: site-recovery
documentationcenter: 
author: rayne-wiselman
manager: carmonm
editor: 
ms.assetid: 095f2d73-994e-4fc0-96f2-e03a7d72e492
ms.service: site-recovery
ms.workload: storage-backup-recovery
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 07/27/2017
ms.author: raynew
ms.openlocfilehash: a1f3f6e6cba074647195e2b0cbcdc7b4f3dec475
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 10/11/2017
---
# <a name="step-3-plan-networking-for-hyper-v-vm-replication-to-a-secondary-vmm-site"></a>Étape 3 : Planifier la mise en réseau pour la réplication d’une machine virtuelle Hyper-V sur un site secondaire VMM

Après avoir passé en revue les prérequis pour le déploiement, lisez cet article pour planifier la mise en réseau lors de la réplication de machines virtuelles Hyper-V gérées dans les clouds System Center Virtual Machines Manager (VMM) sur un site secondaire à l’aide [d’Azure Site Recovery](site-recovery-overview.md) dans le portail Azure. 

Après avoir lu cet article, publiez des commentaires au bas de ce dernier ou sur le [Forum Azure Recovery Services](https://social.msdn.microsoft.com/forums/azure/home?forum=hypervrecovmgr).

## <a name="network-mapping-overview"></a>Vue d’ensemble du mappage réseau

Un mappage réseau est utilisé lors de la réplication de machines virtuelles Hyper-V (gérées dans VMM) sur un centre de données secondaire. Le mappage réseau opère entre des réseaux de machines virtuelles sur un serveur VMM source et des réseaux de machines virtuelles sur un serveur VMM cible. Il effectue les opérations suivantes :

- **Connexion réseau** : connecte les machines virtuelles aux réseaux appropriés après basculement. La machine virtuelle de réplication sera connectée au réseau cible mappé au réseau source.
- **Placement optimal** : place de manière optimale les machines virtuelles de réplication sur des serveurs hôtes Hyper-V. Les machines virtuelles de réplication sont placés sur des hôtes qui peuvent accéder aux réseaux de machines virtuelles mappés.
- **Aucun mappage réseau** : si vous ne configurez pas de mappage réseau, des machines virtuelles de réplication ne sont pas connectées à des réseaux de machines virtuelles après basculement.


### <a name="example"></a>Exemple

Voici un exemple permettant d’illustrer ce processus. Prenons l’exemple d’une entreprise ayant ouvert deux bureaux, l’un à New York et l’autre à Chicago.

**Emplacement** | **Serveur VMM** | **Réseaux de machines virtuelles** | **Mappés à**
---|---|---|---
New York | VMM-NewYork| VMNetwork1-NewYork | Mappé au réseau VMNetwork1-Chicago
 |  | VMNetwork2-NewYork | Non mappé
Chicago | VMM-Chicago| VMNetwork1-Chicago | Mappé au réseau VMNetwork1-NewYork
 | | VMNetwork1-Chicago | Non mappé

Dans cet exemple :

- Lorsqu’un ordinateur virtuel de réplication est créé pour une machine virtuelle connectée au réseau VMNetwork1-NewYork, il est connecté au réseau VMNetwork1-Chicago.
- Lorsqu’un ordinateur virtuel de réplication est créé pour le réseau VMNetwork2-NewYork ou VMNetwork2-Chicago, il n’est pas connecté à un réseau.

Voici comment les clouds VMM sont configurés dans notre exemple d’organisation, ainsi que les réseaux logiques associés aux clouds.

#### <a name="cloud-protection-settings"></a>Paramètres de protection des clouds

**Cloud protégé** | **Cloud de protection** | **Réseau logique (New York)**  
---|---|---
GoldCloud1 | GoldCloud2 |
SilverCloud1| SilverCloud2 |
GoldCloud2 | <p>N/D</p><p></p> | <p>LogicalNetwork1-NewYork</p><p>LogicalNetwork1-Chicago</p>
SilverCloud2 | <p>N/D</p><p></p> | <p>LogicalNetwork1-NewYork</p><p>LogicalNetwork1-Chicago</p>

#### <a name="logical-and-vm-network-settings"></a>Paramètres des réseaux de machines virtuelles et des réseaux logiques

**Emplacement** | **Réseau logique** | **Réseau de machines virtuelles associé**
---|---|---
New York | LogicalNetwork1-NewYork | VMNetwork1-NewYork
Chicago | LogicalNetwork1-Chicago | VMNetwork1-Chicago
 | LogicalNetwork2Chicago | VMNetwork2-Chicago

#### <a name="target-network-settings"></a>Paramètres de réseau cible

Selon ces paramètres, lorsque vous sélectionnez le réseau de machines virtuelles cible, le tableau suivant répertorie les choix disponibles.

**Sélection** | **Cloud protégé** | **Cloud de protection** | **Réseau cible disponible**
---|---|---|---
VMNetwork1-Chicago | SilverCloud1 | SilverCloud2 | Disponible
 | GoldCloud1 | GoldCloud2 | Disponible
VMNetwork2-Chicago | SilverCloud1 | SilverCloud2 | Non disponible
 | GoldCloud1 | GoldCloud2 | Disponible


Si le réseau cible est associé à plusieurs sous-réseaux et que l’un d’eux présente le même nom que le sous-réseau dans lequel se trouve la machine virtuelle source, l’ordinateur virtuel de réplication est connecté à ce sous-réseau cible après le basculement. S’il n’existe aucun sous-réseau cible avec un nom correspondant, la machine virtuelle sera connectée au premier sous-réseau du réseau.


#### <a name="failback-behavior"></a>Comportement de restauration automatique

Pour voir ce qui se produit en cas de restauration automatique (réplication inverse), supposons que le réseau VMNetwork1-NewYork est mappé au réseau VMNetwork1-Chicago, avec les paramètres suivants.


**Machine virtuelle** | **Connectée au réseau de machines virtuelles**
---|---
MV1 | VMNetwork1-Network
VM2 (réplica de VM1) | VMNetwork1-Chicago

Examinons ce qui se passe dans différents scénarios possibles avec ces paramètres.

**Scénario** | **Résultat**
---|---
Aucune modification n’est apportée aux propriétés du réseau de la machine VM2 après le basculement. | La machine VM1 reste connectée au réseau source.
Les propriétés du réseau de la machine VM2 sont modifiées après le basculement ; la machine est déconnectée. | La machine VM1 est déconnectée.
Les propriétés du réseau de la machine VM2 sont modifiées après le basculement ; la machine est connectée au réseau VMNetwork2-Chicago. | Si le réseau VMNetwork2-Chicago n’est pas mappé, la machine VM1 est déconnectée.
Le mappage réseau de VMNetwork1-Chicago est modifié. | La machine VM1 est connectée au réseau désormais mappé à VMNetwork1-Chicago.



## <a name="prepare-for-network-mapping"></a>Préparer le mappage réseau

1. Sur les serveurs VMM source et cible, vous devez disposer d’un réseau logique associé aux clouds source et cible. 
2. Sur les serveurs source et cible, vous devez disposer d’un réseau de machines virtuelles lié au réseau logique.
3. Les machines virtuelles sur les hôtes Hyper-V dans l’emplacement source doivent être liées au réseau de machines virtuelles source. Si vous utilisez un seul serveur VMM, vous pouvez configurer le mappage entre les réseaux de machines virtuelles sur le même serveur.

Voici ce qui se passe lorsque vous configurez le mappage réseau lors du déploiement de Site Recovery :

- Lorsque vous configurez le mappage réseau et sélectionnez un réseau de machines virtuelles cible, les clouds sources VMM qui utilisent ce réseau de machines virtuelles source seront affichés, ainsi que les réseaux de machines virtuelles cibles disponibles sur les clouds cibles.
- - Lorsque le mappage réseau est correctement configuré et que la réplication est activée, une machine virtuelle source est connectée à son réseau de machines virtuelles source, et son réplica à l’emplacement cible est connecté à son réseau de machines virtuelles mappé.
- Si le réseau cible est associé à plusieurs sous-réseaux et que l’un d’eux présente le même nom que le sous-réseau dans lequel se trouve la machine virtuelle source, l’ordinateur virtuel de réplication est connecté à ce sous-réseau cible après le basculement. S’il n’existe aucun sous-réseau cible avec un nom correspondant, la machine virtuelle sera connectée au premier sous-réseau du réseau.

## <a name="connect-to-vms-after-failover"></a>Se connecter aux machines virtuelles après le basculement

Lorsque vous planifiez votre stratégie de basculement et de réplication, l’une des questions clés est de savoir comment se connecter au réplica après le basculement. Vous disposez de deux options : 

- **Utiliser une adresse IP différente** : vous pouvez choisir d’utiliser une adresse IP différente pour la machine virtuelle répliquée. Dans ce scénario, la machine virtuelle reçoit une nouvelle adresse IP après le basculement, et une mise à jour DNS est nécessaire.
- **Conserver la même adresse IP** : vous pouvez choisir d’utiliser la même adresse IP pour la machine virtuelle réplica. La conservation des mêmes adresses IP simplifie la récupération en réduisant les problèmes liés au réseau après le basculement. 

## <a name="retain-ip-addresses"></a>Conserver les adresses IP

Si vous souhaitez conserver les adresses IP à partir du site principal après le basculement vers le site secondaire, vous pouvez effectuer un basculement de sous-réseau complet et mettre à jour les itinéraires pour indiquer le nouvel emplacement des adresses IP, ou vous pouvez déployer un sous-réseau étiré entre le site principal et le site de récupération.

### <a name="stretched-subnet"></a>Sous-réseau étiré

Dans un sous-réseau étiré, le sous-réseau est disponible simultanément sur le site principal et le site secondaire. Si vous déplacez un serveur et sa configuration IP (couche 3) sur le site secondaire, le réseau achemine automatiquement le trafic vers le nouvel emplacement. 

En ce qui concerne la couche 2 (couche de liaison de données), vous devez disposer d’un équipement réseau qui peut gérer un VLAN étiré. De plus, en étirant le VLAN, le domaine d’erreur potentielle s’étend aux deux sites, et devient essentiellement un point de défaillance unique. Bien que cela soit peu probable, il est possible qu’une tempête de diffusion commence sans pouvoir être isolée. 


### <a name="subnet-failover"></a>Basculement de sous-réseau

Vous pouvez exécuter un basculement de sous-réseau pour bénéficier des avantages du sous-réseau étiré, sans réellement l’étirer. Dans cette solution, un sous-réseau est disponible sur le site source ou cible, mais pas les deux simultanément. Pour conserver l'espace d'adressage IP en cas de basculement, vous pouvez mettre en place un programme pour que l'infrastructure du routeur déplace les sous-réseaux d'un site à un autre. Après le basculement, les sous-réseaux se déplacent avec les machines virtuelles associées. Le principal inconvénient est qu’en cas de défaillance, vous devez déplacer le sous-réseau tout entier.

### <a name="example"></a>Exemple

Voici un exemple de basculement de sous-réseau complet. Le site principal possède des applications qui s'exécutent dans le sous-réseau 192.168.1.0/24. Lors du basculement, toutes les machines virtuelles dans ce sous-réseau sont basculées sur le site secondaire et conservent leurs adresses IP. Les itinéraires doivent être modifiés pour refléter le fait que toutes les machines virtuelles appartenant au sous-réseau 192.168.1.0/24 ont été déplacées vers le site secondaire.

Les graphiques suivants illustrent les sous-réseaux avant et après le basculement :

- Avant le basculement, le sous-réseau 192.168.0.1/24 est actif sur le site source et devient actif sur le site secondaire après le basculement.
- Les itinéraires existant entre le site principal et le site de récupération, le troisième site et le site principal, et entre le troisième site et le site de récupération doivent être modifiés comme il convient.

**Avant le basculement**

![Avant le basculement](./media/vmm-to-vmm-walkthrough-network/network-design2.png)

**Après le basculement**

![Après le basculement](./media/vmm-to-vmm-walkthrough-network/network-design3.png)

Voici ce qui se passe après le basculement :

- Site Recovery alloue une adresse IP pour chaque interface réseau sur la machine virtuelle à partir du pool d'adresses IP statiques défini sur le réseau approprié pour chaque instance VMM.
- Si le pool d’adresses IP sur le site secondaire est identique à celui sur le site source, Site Recovery alloue la même adresse IP (de la machine virtuelle source) à la machine virtuelle réplica. L’adresse IP est réservée dans VMM, mais n’est pas définie comme l’adresse IP de basculement sur l’hôte Hyper-V. L’adresse IP de basculement sur un hôte Hyper-V est définie juste avant le basculement.
- Si la même adresse IP n'est pas disponible, Site Recovery alloue une autre adresse IP disponible du pool.
- Si les machines virtuelles utilisent DHCP, Site Recovery ne gère pas les adresses IP. Vous devez vérifier que le serveur DHCP sur le site secondaire peut allouer une adresse à partir de la même plage que le site source.

### <a name="validate-the-ip-address"></a>Valider l’adresse IP

Une fois la protection activée pour une machine virtuelle, vous pouvez utiliser l’exemple de script suivant pour vérifier l’adresse attribuée à la machine virtuelle. La même adresse IP est définie comme adresse IP de basculement et est attribuée à la machine virtuelle au moment du basculement :

    ```
    $vm = Get-SCVirtualMachine -Name <VM_NAME>
    $na = $vm[0].VirtualNetworkAdapters>
    $ip = Get-SCIPAddress -GrantToObjectID $na[0].id
    $ip.address 
    ```

## <a name="changing-ip-addresses"></a>Modifier les adresses IP

Dans ce scénario, les adresses IP des machines virtuelles basculées sont modifiées. L’inconvénient de cette solution est la maintenance requise. En règle générale, DNS sera mis à jour après le démarrage des machines virtuelles de réplica. Les entrées DNS devront peut-être être modifiées ou éliminées sur le réseau, et les entrées en cache mises à jour. Cela peut entraîner un temps d’arrêt. Le temps d’arrêt peut être atténué comme suit :

- Utilisez des valeurs TTL faibles pour les applications intranet.
- Utilisez le script suivant dans un plan de récupération Site Recovery pour mettre à jour le serveur DNS et garantir une mise à jour en temps voulu. Vous n’avez pas besoin du script si vous utilisez l’inscription DNS dynamique.

    ```
    param(
    string]$Zone,
    [string]$name,
    [string]$IP
    )
    $Record = Get-DnsServerResourceRecord -ZoneName $zone -Name $name
    $newrecord = $record.clone()
    $newrecord.RecordData[0].IPv4Address  =  $IP
    Set-DnsServerResourceRecord -zonename $zone -OldInputObject $record -NewInputObject $Newrecord
    ```
    
### <a name="example"></a>Exemple 

Consultons un scénario dans lequel vous envisagez d’utiliser des adresses IP différentes sur le site principal et le site de récupération. Dans cet exemple, nous avons des adresses IP différentes entre le site principal et le site secondaire, et il existe un troisième site à partir duquel il est possible d’accéder aux applications hébergées sur le site principal ou le site de récupération.

- Avant le basculement, les applications sont hébergées sur le sous-réseau 192.168.1.0/24 sur le site principal et sont configurées pour être hébergées sur le sous-réseau 172.16.1.0/24 sur le site secondaire après un basculement.
- Les itinéraires du réseau/des connexions VPN ont été correctement configurés pour que les trois sites puissent accéder l’un à l'autre.
- Après le basculement, les applications sont restaurées sur le sous-réseau de récupération. Dans ce scénario, il n’est pas nécessaire de basculer tout le sous-réseau et aucune modification n’est requise pour reconfigurer les itinéraires réseau ou VPN. Le basculement et certaines mises à jour DNS garantissent le maintien de l’accessibilité des applications.
- Si le système DNS est configuré pour autoriser des mises à jour dynamiques, les machines virtuelles peuvent alors s’inscrire à l’aide de la nouvelle adresse IP une fois qu’elles démarrent après un basculement.

**Avant le basculement**

![Autre adresse IP - avant le basculement](./media/vmm-to-vmm-walkthrough-network/network-design10.png)

**Après le basculement**

![Autre adresse IP - après le basculement](./media/vmm-to-vmm-walkthrough-network/network-design11.png)



## <a name="next-steps"></a>Étapes suivantes

Aller à [Étape 4 : Préparer VMM et Hyper-V](vmm-to-vmm-walkthrough-vmm-hyper-v.md).


