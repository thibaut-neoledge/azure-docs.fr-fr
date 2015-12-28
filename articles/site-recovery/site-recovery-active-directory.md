<properties
	pageTitle="Protéger Active Directory et DNS avec Azure Site Recovery | Microsoft Azure" 
	description="Cet article décrit comment implémenter une solution de récupération d'urgence pour Active Directory à l'aide d'Azure Site Recovery." 
	services="site-recovery" 
	documentationCenter="" 
	authors="prateek9us" 
	manager="abhiag" 
	editor=""/>

<tags 
	ms.service="site-recovery" 
	ms.devlang="na"
	ms.topic="article"
	ms.tgt_pltfrm="na"
	ms.workload="storage-backup-recovery" 
	ms.date="12/14/2015" 
	ms.author="pratshar"/>

# Protéger Active Directory et DNS avec Azure Site Recovery

Les applications d'entreprise telles que SharePoint, Dynamics AX et SAP dépendent du bon fonctionnement d'Active Directory et de l'infrastructure DNS. Lorsque vous créez une solution de récupération d'urgence pour des applications, il est important de se rappeler que vous devez protéger et récupérer Active Directory et DNS avant les autres composants d'application, pour vous assurer que tout fonctionne correctement en cas de sinistre.

Site Recovery est un service Azure offrant une récupération d’urgence en coordonnant la réplication, le basculement et la récupération des machines virtuelles. Site Recovery prend en charge un certain nombre de scénarios de réplication afin de systématiquement protéger et basculer en toute transparence des machines virtuelles et des applications sur des clouds privés, publics ou de l’hébergeur.

À l’aide de Site Recovery, vous pouvez créer un plan de récupération d’urgence automatisé complet pour Active Directory. En cas d’interruption, vous pouvez lancer un basculement en quelques secondes, où que vous soyez, et bénéficier d’un Active Directory opérationnel en quelques minutes. Si vous avez déployé Active Directory pour plusieurs applications, telles que SharePoint et SAP dans votre site principal et que vous souhaitez basculer le site intégral, vous pouvez basculer Active Directory en premier lieu à l’aide de Site Recovery, puis basculer les autres applications en utilisant des plans de récupération spécifiques aux applications.

Cet article explique comment créer une solution de récupération d’urgence pour Active Directory, effectuer des basculements planifiés, non planifiés et de test à l’aide d’un plan de récupération d’urgence accessible en un clic, de configurations prises en charge et de conditions préalables. Vous devez être capable d’utiliser Active Directory et Azure Site Recovery avant de commencer.

En fonction de la complexité de votre environnement, deux options vous sont recommandées.

### Option 1 :

Si vous avez un petit nombre d'applications et un seul contrôleur de domaine, et que vous souhaitez basculer l'ensemble du site, nous vous recommandons d'utiliser Site Recovery pour répliquer le contrôleur de domaine sur le site secondaire (que vous effectuiez un basculement vers Azure ou vers un site secondaire). La même machine virtuelle répliquée peut également servir pour le test de basculement.

### Option 2 :

Si vous avez un grand nombre d’applications et plus d’un contrôleur de domaine dans l’environnement, ou si vous envisagez de basculer plusieurs applications à la fois, outre la réplication de la machine virtuelle du contrôleur de domaine avec Site Recovery, nous vous recommandons de configurer également un contrôleur de domaine supplémentaire sur le site cible (Azure ou un centre de données local secondaire).

>[AZURE.NOTE]Même si vous implémentez Option-2 pour effectuer un test de basculement, vous devez toujours répliquer le contrôleur de domaine à l'aide de Site Recovery. Pour plus d’informations, consultez la rubrique [Considérations en matière de test de basculement](#considerations-for-test-failover).


Les sections ci-dessous expliquent comment activer la protection d’un contrôleur de domaine dans Site Recovery et comment configurer un contrôleur de domaine dans Azure.


## Composants requis

- Un déploiement local d’Active Directory et du serveur DNS.
- Un coffre Azure Site Recovery Services dans un abonnement Microsoft Azure. 
- Si vous répliquez vers Azure, exécutez l’outil d’évaluation de la disponibilité des machines virtuelles Azure sur des machines virtuelles, afin de vérifier qu’elles sont compatibles avec les machines virtuelles Azure et Azure Site Recovery Services


## Activer la protection à l'aide de Site Recovery


### Protéger la machine virtuelle

Activez la protection de la machine virtuelle du contrôleur de domaine/DNS dans Site Recovery. Configurez les paramètres de Site Recovery en fonction du type de machine virtuelle (Hyper-V ou VMware). Nous vous recommandons une fréquence de réplication, cohérente en cas d’incident, de 15 minutes.

###Configurer les paramètres réseau de la machine virtuelle

Pour la machine virtuelle du contrôleur de domaine/DNS, configurez les paramètres réseau dans Site Recovery afin que la machine virtuelle soit connectée au réseau approprié après un basculement. Par exemple, si vous répliquez des machines virtuelles Hyper-V vers Azure, vous pouvez sélectionner la machine virtuelle dans le cloud VMM ou dans le groupe de protection afin de configurer les paramètres réseau comme indiqué ci-dessous.

![Paramètres réseau de la machine virtuelle](./media/site-recovery-active-directory/VM-Network-Settings.png)

## Protéger Active Directory avec la réplication Active Directory 

### Protection de site à site

Créez un contrôleur de domaine sur le site secondaire et spécifiez le même nom de domaine qui est utilisé sur le site principal lorsque vous attribuez au serveur un rôle de contrôleur de domaine. Vous pouvez utiliser le composant logiciel enfichable **Sites et services Active Directory** pour configurer les paramètres sur l'objet du lien de sites auquel les sites sont ajoutés. En configurant des paramètres sur un lien de sites, vous pouvez contrôler le moment où la réplication a lieu entre deux sites ou plus, ainsi que la fréquence. Consultez la rubrique [Planification de la réplication entre des sites](https://technet.microsoft.com/library/cc731862.aspx) pour plus de détails.

###Protection de site vers Azure

Suivez les instructions pour [créer un contrôleur de domaine dans un réseau virtuel Azure](../virtual-network/virtual-networks-install-replica-active-directory-domain-controller.md). Lorsque vous attribuez au serveur un rôle de contrôleur de domaine, spécifiez le même nom de domaine qui est utilisé sur le site principal.

[Reconfigurez ensuite le serveur DNS pour le réseau virtuel](../virtual-network/virtual-networks-install-replica-active-directory-domain-controller.md#reconfigure-dns-server-for-the-virtual-network) afin de l’utiliser dans Azure.
  
![Réseau Azure](./media/site-recovery-active-directory/azure-network.png)

## Considérations en matière de test de basculement

Le test de basculement est effectué dans un réseau isolé du réseau de production afin qu’il n’y ait aucun impact sur les charges de travail de production.

La plupart des applications requièrent également la présence d'un contrôleur de domaine et d’un serveur DNS pour pouvoir fonctionner. Donc, pour réaliser le basculement d’une application, un contrôleur de domaine doit être créé dans le réseau isolé pour qu’il puisse être utilisé pour le test de basculement. Pour ce faire, le plus simple consiste tout d’abord à activer la protection sur la machine virtuelle du contrôleur de domaine/DNS à l’aide de Site Recovery et à effectuer le test de basculement de cette machine virtuelle avant d’exécuter le test de basculement du plan de récupération d’urgence de l’application. Voici comment procéder :

1. Activez la protection de la machine virtuelle du contrôleur de domaine/DNS dans Site Recovery.
2. Créez un réseau isolé. Tout réseau virtuel créé dans Azure par défaut est isolé des autres réseaux. Il est recommandé que la plage d’adresses IP pour ce réseau soit identique à celle de votre réseau de production. N’activez pas la connectivité de site à site sur ce réseau.
3. Fournissez une adresse IP DNS dans le réseau créé, comme l'adresse IP que vous attendez que la machine virtuelle du DNS obtienne. Si vous répliquez vers Azure, fournissez l’adresse IP de la machine virtuelle qui sera utilisée lors du basculement dans le paramètre **Adresse IP cible**, dans les propriétés de la machine virtuelle. Si vous répliquez vers un autre site local et que vous utilisez DHCP, suivez les instructions pour [configurer DNS et DHCP pour le test de basculement](site-recovery-failover.md#prepare-dhcp) 

>[AZURE.NOTE]L’adresse IP affectée à une machine virtuelle durant un test de basculement est identique à l’adresse IP qu’elle obtiendrait durant un basculement planifié ou non planifié, si l’adresse IP est disponible dans le réseau de test de basculement. Si ce n'est pas le cas, la machine virtuelle reçoit une adresse IP différente qui est disponible dans le réseau de test de basculement.

4. Sur la machine virtuelle du contrôleur de domaine, exécutez un test de basculement de celle-ci dans le réseau isolé. 
5. Exécutez un test de basculement pour le plan de récupération de l’application.
6. Une fois le test terminé, marquez le test de basculement de la tâche de la machine virtuelle du contrôleur de domaine et le plan de récupération d’urgence comme terminés dans l’onglet **Tâches** du portail Site Recovery. 

### DNS et contrôleur de domaine sur différentes machines
 
Si DNS figure sur une machine virtuelle différente de celle du contrôleur de domaine, vous devez créer une machine virtuelle DNS pour le test de basculement. Si le DNS et le contrôleur de domaine figurent sur la même machine virtuelle, vous pouvez ignorer cette section.

Vous pouvez utiliser un nouveau serveur DNS et créer toutes les zones requises. Par exemple, si votre domaine Active Directory est contoso.com, vous pouvez créer une zone DNS portant le nom contoso.com. Les entrées correspondant à Active Directory doivent être mises à jour dans le système DNS, comme suit :

1. Vérifiez que ces paramètres sont en place avant toute apparition d’une autre machine virtuelle du plan de récupération :

	- La zone doit être nommée en fonction du nom racine de la forêt.
	- La zone doit être sauvegardée dans un fichier.
	- La zone doit être activée pour les mises à jour sécurisées et non sécurisées.
	- Le programme de résolution de la machine virtuelle du contrôleur de domaine doit pointer vers l’adresse IP de la machine virtuelle du DNS.

2. Exécutez la commande suivante dans le répertoire de la machine virtuelle du contrôleur de domaine :

	`nltest /dsregdns`

3. Ajoutez une zone sur le serveur DNS, autorisez les mises à jour non sécurisées et ajoutez une entrée pour ce dernier dans le service DNS :

	    dnscmd /zoneadd contoso.com  /Primary 
	    dnscmd /recordadd contoso.com  contoso.com. SOA %computername%.contoso.com. hostmaster. 1 15 10 1 1 
	    dnscmd /recordadd contoso.com %computername%  A <IP_OF_DNS_VM> 
	    dnscmd /config contoso.com /allowupdate 1


## Étapes suivantes

Pour en savoir plus sur la protection des charges de travail d’entreprise avec Azure Site Recovery, consultez la rubrique [Quelles charges de travail puis-je protéger ?](../site-recovery/site-recovery-workload.md).

<!---HONumber=AcomDC_1217_2015-->