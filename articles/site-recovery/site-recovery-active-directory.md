---
title: "Protéger Active Directory et DNS avec Azure Site Recovery | Documents Microsoft"
description: "Cet article décrit comment implémenter une solution de récupération d&quot;urgence pour Active Directory à l&quot;aide d&quot;Azure Site Recovery."
services: site-recovery
documentationcenter: 
author: prateek9us
manager: gauravd
editor: 
ms.assetid: af1d9b26-1956-46ef-bd05-c545980b72dc
ms.service: site-recovery
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: storage-backup-recovery
ms.date: 1/9/2017
ms.author: pratshar
translationtype: Human Translation
ms.sourcegitcommit: feb0200fc27227f546da8c98f21d54f45d677c98
ms.openlocfilehash: a583225b4f3acd747a10c1c1fd337bc1b7ac599c


---
# <a name="protect-active-directory-and-dns-with-azure-site-recovery"></a>Protéger Active Directory et DNS avec Azure Site Recovery
Les applications d'entreprise telles que SharePoint, Dynamics AX et SAP dépendent du bon fonctionnement d'Active Directory et de l'infrastructure DNS. Lorsque vous créez une solution de récupération d'urgence pour des applications, il est important de se rappeler que vous devez protéger et récupérer Active Directory et DNS avant les autres composants d'application, pour vous assurer que tout fonctionne correctement en cas de sinistre.

Site Recovery est un service Azure offrant une récupération d’urgence en coordonnant la réplication, le basculement et la récupération des machines virtuelles. Site Recovery prend en charge un certain nombre de scénarios de réplication afin de systématiquement protéger et basculer en toute transparence des machines virtuelles et des applications sur des clouds privés, publics ou hébergeurs.

À l’aide de Site Recovery, vous pouvez créer un plan de récupération d’urgence automatisé complet pour Active Directory. En cas d’interruption, vous pouvez lancer un basculement en quelques secondes, où que vous soyez, et bénéficier d’un Active Directory opérationnel en quelques minutes. Si vous avez déployé Active Directory pour plusieurs applications, telles que SharePoint et SAP dans votre site principal et que vous souhaitez basculer le site intégral, vous pouvez basculer Active Directory en premier lieu à l’aide de Site Recovery, puis basculer les autres applications en utilisant des plans de récupération spécifiques aux applications.

Cet article explique comment créer une solution de récupération d’urgence pour Active Directory, effectuer des basculements planifiés, non planifiés et de test à l’aide d’un plan de récupération d’urgence accessible en un clic, de configurations prises en charge et de conditions préalables.  Vous devez être capable d’utiliser Active Directory et Azure Site Recovery avant de commencer.

## <a name="replicating-domain-controller"></a>Réplication d’un contrôleur de domaine

Vous devez configurer la [réplication Site Recovery](#enable-protection-using-site-recovery) sur au moins une machine virtuelle qui héberge le contrôleur de domaine et le DNS. Si votre environnement contient [plusieurs contrôleurs de domaine](#environment-with-multiple-domain-controllers), en plus de répliquer la machine virtuelle de contrôleur de domaine, vous devez également configurer un [contrôleur de domaine supplémentaire](#protect-active-directory-with-active-directory-replication) sur le site cible (Azure ou un centre de données local secondaire). 

### <a name="single-domain-controller-environment"></a>Environnement avec un seul contrôleur de domaine
Si vous avez un petit nombre d’applications et un seul contrôleur de domaine, et que vous souhaitez basculer l’ensemble du site en même temps, nous vous recommandons d’utiliser Site Recovery pour répliquer le contrôleur de domaine sur le site secondaire (que le basculement s’effectue vers Azure ou un site secondaire). La même machine virtuelle de contrôleur de domaine/DNS répliquée peut également être utilisée à des fins de [test de basculement](#test-failover-considerations).

### <a name="environment-with-multiple-domain-controllers"></a>Environnement avec plusieurs contrôleurs de domaine
Si vous avez un grand nombre d’applications et plus d’un contrôleur de domaine dans l’environnement, ou si vous envisagez de basculer plusieurs applications à la fois, outre la réplication de la machine virtuelle du contrôleur de domaine avec Site Recovery, nous vous recommandons de configurer également un [contrôleur de domaine supplémentaire](#protect-active-directory-with-active-directory-replication) sur le site cible (Azure ou un centre de données local secondaire). Dans ce scénario, vous utiliserez le contrôleur de domaine répliqué par Site Recovery aux fins de [test de basculement](#test-failover-considerations) et le contrôleur de domaine supplémentaire sur le site cible lorsque vous effectuez un basculement. 


Les sections ci-dessous expliquent comment activer la protection d’un contrôleur de domaine dans Site Recovery et comment configurer un contrôleur de domaine dans Azure.

## <a name="prerequisites"></a>Composants requis
* Un déploiement local d’Active Directory et du serveur DNS.
* Un coffre Azure Site Recovery Services dans un abonnement Microsoft Azure.
* Si vous répliquez vers Azure, exécutez l’outil d’évaluation de la disponibilité des machines virtuelles Azure sur des machines virtuelles, afin de vérifier qu’elles sont compatibles avec les machines virtuelles Azure et Azure Site Recovery Services

## <a name="enable-protection-using-site-recovery"></a>Activer la protection à l'aide de Site Recovery
### <a name="protect-the-virtual-machine"></a>Protéger la machine virtuelle
Activez la protection de la machine virtuelle du contrôleur de domaine/DNS dans Site Recovery. Configurez les paramètres de Site Recovery en fonction du type de machine virtuelle (Hyper-V ou VMware). Nous vous recommandons une fréquence de réplication, cohérente en cas d’incident, de 15 minutes.

### <a name="configure-virtual-machine-network-settings"></a>Configurer les paramètres réseau de la machine virtuelle
Pour la machine virtuelle du contrôleur de domaine/DNS, configurez les paramètres réseau dans Site Recovery afin que la machine virtuelle soit connectée au réseau approprié après un basculement. Par exemple, si vous répliquez des machines virtuelles Hyper-V vers Azure, vous pouvez sélectionner la machine virtuelle dans le cloud VMM ou dans le groupe de protection afin de configurer les paramètres réseau comme indiqué ci-dessous

![Paramètres réseau de la machine virtuelle](./media/site-recovery-active-directory/DNS-Target-IP.png)

## <a name="protect-active-directory-with-active-directory-replication"></a>Protéger Active Directory avec la réplication Active Directory
### <a name="site-to-site-protection"></a>Protection de site à site
Créez un contrôleur de domaine sur le site secondaire et spécifiez le même nom de domaine qui est utilisé sur le site principal lorsque vous promouvez le serveur au rôle de contrôleur de domaine. Vous pouvez utiliser le composant logiciel enfichable **Sites et services Active Directory** pour configurer les paramètres sur l'objet du lien de sites auquel les sites sont ajoutés. En configurant des paramètres sur un lien de sites, vous pouvez contrôler le moment où la réplication a lieu entre deux sites ou plus, ainsi que la fréquence. Consultez la rubrique [Planification de la réplication entre des sites](https://technet.microsoft.com/library/cc731862.aspx) pour plus de détails.

### <a name="site-to-azure-protection"></a>Protection de site vers Azure
Suivez les instructions pour [créer un contrôleur de domaine dans un réseau virtuel Azure](../active-directory/active-directory-install-replica-active-directory-domain-controller.md). Lorsque vous allouez au serveur un rôle de contrôleur de domaine, spécifiez le même nom de domaine qui est utilisé sur le site principal.

[Reconfigurez ensuite le serveur DNS pour le réseau virtuel](../active-directory/active-directory-install-replica-active-directory-domain-controller.md#reconfigure-dns-server-for-the-virtual-network)afin de l’utiliser dans Azure.

![Réseau Azure](./media/site-recovery-active-directory/azure-network.png)

**DNS dans le réseau de production Azure**

## <a name="test-failover-considerations"></a>Considérations en matière de test de basculement
Le test de basculement est effectué dans un réseau isolé du réseau de production afin qu’il n’y ait aucun impact sur les charges de travail de production.

La plupart des applications requièrent également la présence d’un contrôleur de domaine et d’un serveur DNS pour pouvoir fonctionner. Donc, pour réaliser le basculement d’une application, un contrôleur de domaine doit être créé dans le réseau isolé pour qu’il puisse être utilisé pour le test de basculement. Pour ce faire, le plus simple consiste tout d’abord à activer la protection sur la machine virtuelle du contrôleur de domaine/DNS à l’aide de Site Recovery et à effectuer le test de basculement de cette machine virtuelle avant d’exécuter le test de basculement du plan de récupération d’urgence de l’application. Voici comment procéder :

1. Activez la protection de la machine virtuelle du contrôleur de domaine/DNS dans Site Recovery.
1. Créez un réseau isolé. Tout réseau virtuel créé dans Azure par défaut est isolé des autres réseaux. Il est recommandé que la plage d’adresses IP pour ce réseau soit identique à celle de votre réseau de production. N’activez pas la connectivité de site à site sur ce réseau.
1. Fournissez une adresse IP de DNS dans le réseau créé, comme l’adresse IP que la machine virtuelle du DNS devrait obtenir. Si vous effectuez une réplication vers Azure, indiquez l’adresse IP de la machine virtuelle qui sera utilisée lors du basculement dans le champ **Adresse IP cible** des paramètres **Calcul et réseau**. 

    ![Adresse IP cible](./media/site-recovery-active-directory/DNS-Target-IP.png)
    **Adresse IP cible**

    ![Réseau de test Azure](./media/site-recovery-active-directory/azure-test-network.png)

    **DNS dans le réseau de test Azure**

1. Si vous répliquez vers un autre site local et que vous utilisez DHCP, suivez les instructions pour [configurer DNS et DHCP pour le test de basculement](site-recovery-test-failover-vmm-to-vmm.md#prepare-dhcp)
1. Effectuez un test de basculement de la machine virtuelle du contrôleur de domaine dans le réseau isolé. Utilisez le dernier point de récupération **cohérent avec l’application** disponible de la machine virtuelle du contrôleur de domaine pour effectuer le test de basculement. 
1. Exécutez un test de basculement pour le plan de récupération de l’application.
1. Une fois le test terminé, marquez la tâche de test de basculement de la machine virtuelle du contrôleur de domaine et le plan de récupération d’urgence comme terminés dans l’onglet **Tâches** du portail Site Recovery.


> [!TIP]
> L’adresse IP affectée à une machine virtuelle durant un test de basculement est identique à l’adresse IP qu’elle obtiendrait durant un basculement planifié ou non planifié, si l’adresse IP est disponible dans le réseau de test de basculement. Si tel n’est pas le cas, la machine virtuelle reçoit une adresse IP différente qui est disponible dans le réseau de test de basculement.
> 
> 


### <a name="removing-reference-to-other-domain-controllers"></a>Suppression de la référence à d’autres contrôleurs de domaine
Lorsque vous effectuez un test de basculement, vous n’utiliserez pas tous les contrôleurs de domaine dans le réseau de test. Pour supprimer la référence des autres contrôleurs de domaine qui existent dans votre environnement de production, vous devrez [prendre les rôles FSMO Active Directory et effectuer un nettoyage des métadonnées](http://aka.ms/ad_seize_fsmo) pour les contrôleurs de domaine absents. 

### <a name="troubleshooting-domain-controller-issues-during-test-failover"></a>Résolution des problèmes liés à un contrôleur de domaine survenant pendant un test de basculement


Dans une invite de commandes, exécutez la commande suivante pour vérifier si les dossiers SYSVOL et NETLOGON sont partagés :

    NET SHARE

Dans l’invite de commandes, exécutez la commande suivante pour vous assurer que le contrôleur de domaine fonctionne correctement.

    dcdiag /v > dcdiag.txt

Dans le journal de sortie, recherchez le texte suivant pour vérifier que le contrôleur de domaine fonctionne correctement. 

* « passed test Connectivity »
* « passed test Advertising »
* « passed test MachineAccount »

Si les conditions ci-dessus sont remplies, il est probable que le contrôleur de domaine fonctionne correctement. Si ce n’est pas le cas, essayez les étapes suivantes.


* Effectuez une restauration faisant autorité du contrôleur de domaine.
    * Bien qu’il soit [déconseillé d’utiliser la réplication FRS](https://blogs.technet.microsoft.com/filecab/2014/06/25/the-end-is-nigh-for-frs/), si vous l’utilisez encore, suivez les étapes indiquées [ici](https://support.microsoft.com/en-in/kb/290762) pour effectuer une restauration faisant autorité. Vous trouverez plus d’informations sur les éléments Burflags dont il est question dans le lien précédent [ici](https://blogs.technet.microsoft.com/janelewis/2006/09/18/d2-and-d4-what-is-it-for/).
    * Si vous utilisez la réplication DFSR, suivez les étapes disponibles [ici](https://support.microsoft.com/en-us/kb/2218556) pour effectuer une restauration faisant autorité. Vous pouvez également utiliser les fonctions PowerShell disponibles [ici](https://blogs.technet.microsoft.com/thbouche/2013/08/28/dfsr-sysvol-authoritative-non-authoritative-restore-powershell-functions/) à cet effet. 
    
* Contournez l’obligation de synchronisation initiale en définissant la clé de Registre suivante sur 0. Si cette valeur DWORD n’existe pas, vous pouvez la créer sous le nœud « Parameters ». Vous trouverez plus d’informations à ce sujet [ici](https://support.microsoft.com/en-us/kb/2001093).

        HKEY_LOCAL_MACHINE\SYSTEM\CurrentControlSet\Services\NTDS\Parameters\Repl Perform Initial Synchronizations

* Désactivez l’exigence de disponibilité d’un serveur de catalogue global pour valider la connexion utilisateur en définissant la clé de Registre suivante sur 1. Si cette valeur DWORD n’existe pas, vous pouvez la créer sous le nœud « Lsa ». Vous trouverez plus d’informations à ce sujet [ici](http://support.microsoft.com/kb/241789/EN-US).

        HKEY_LOCAL_MACHINE\SYSTEM\CurrentControlSet\Control\Lsa\IgnoreGCFailures



### <a name="dns-and-domain-controller-on-different-machines"></a>DNS et contrôleur de domaine sur différentes machines
Si DNS figure sur une machine virtuelle différente de celle du contrôleur de domaine, vous devez créer une machine virtuelle DNS pour le test de basculement. Si le DNS et le contrôleur de domaine figurent sur la même machine virtuelle, vous pouvez ignorer cette section.

Vous pouvez utiliser un nouveau serveur DNS et créer toutes les zones requises. Par exemple, si votre domaine Active Directory est contoso.com, vous pouvez créer une zone DNS portant le nom contoso.com. Les entrées correspondant à Active Directory doivent être mises à jour dans le système DNS, comme suit :

1. Vérifiez que ces paramètres sont en place avant toute apparition d’une autre machine virtuelle du plan de récupération :
   
   * La zone doit être nommée en fonction du nom racine de la forêt.
   * La zone doit être sauvegardée dans un fichier.
   * La zone doit être activée pour les mises à jour sécurisées et non sécurisées.
   * Le programme de résolution de la machine virtuelle du contrôleur de domaine doit pointer vers l’adresse IP de la machine virtuelle du DNS.
2. Exécutez la commande suivante sur la machine virtuelle du contrôleur de domaine :
   
    `nltest /dsregdns`
3. Ajoutez une zone sur le serveur DNS, autorisez les mises à jour non sécurisées et ajoutez une entrée pour ce dernier dans le service DNS :
   
        dnscmd /zoneadd contoso.com  /Primary
        dnscmd /recordadd contoso.com  contoso.com. SOA %computername%.contoso.com. hostmaster. 1 15 10 1 1
        dnscmd /recordadd contoso.com %computername%  A <IP_OF_DNS_VM>
        dnscmd /config contoso.com /allowupdate 1

## <a name="next-steps"></a>Étapes suivantes
Pour en savoir plus sur la protection des charges de travail d’entreprise avec Azure Site Recovery, consultez [Quelles charges de travail pouvez-vous protéger avec Azure Site Recovery ?](site-recovery-workload.md).




<!--HONumber=Jan17_HO4-->


