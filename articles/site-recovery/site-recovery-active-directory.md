<properties
	pageTitle="Guide ASR pour Active Directory | Microsoft Azure" 
	description="Cet article explique en détail comment vous pouvez créer une solution de récupération d’urgence pour votre AD à l’aide d’Azure Site Recovery, effectuer des basculements planifiés/non planifiés/de test à l’aide d’un plan de récupération d’urgence accessible en un clic, de configurations prises en charge et de conditions préalables." 
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
	ms.date="10/12/2015" 
	ms.author="pratshar"/>

#Solution de récupération d’urgence automatisée pour Active Directory et DNS à l’aide d’ASR


Toutes les applications d'entreprise telles que SharePoint, Dynamics AX et SAP dépendent du bon fonctionnement d'Active Directory et de l'infrastructure DNS. Lors de la création d'une solution de récupération d'urgence pour une application de ce type, il est important de protéger et de restaurer Active Directory avant que les autres composants de l'application ne soient à nouveau disponibles en cas d'interruption.

Azure Site Recovery est un service Azure offrant des capacités de récupération d’urgence en coordonnant la réplication, le basculement et la récupération des machines virtuelles. Azure Site Recovery prend en charge un certain nombre de technologies de réplication afin de systématiquement répliquer, protéger et basculer en toute transparence des machines virtuelles et des applications sur des clouds privés/publics ou de l’hébergeur.

À l'aide d'Azure Site Recovery, vous pouvez créer un plan de récupération d'urgence automatisé complet pour votre AD. En cas d’interruption, vous pouvez lancer le basculement en quelques secondes, où que vous soyez, et bénéficier d’un AD opérationnel en quelques minutes. Si vous avez un AD pour plusieurs applications, comme SharePoint et SAP dans votre site principal, et que vous décidez de basculer le site complet, vous pouvez commencer par basculer AD à l’aide d’ASR, puis basculer les autres applications à l’aide de plans de récupération d’urgence spécifiques à chaque application.

Cet article explique en détail comment vous pouvez créer une solution de récupération d’urgence pour votre AD, effectuer des basculements planifiés/non planifiés/de test à l’aide d’un plan de récupération d’urgence accessible en un clic, de configurations prises en charge et de conditions préalables. Le public est censé être familiarisé avec AD et Azure Site Recovery. Il existe deux options recommandées en fonction de la complexité de l’environnement local du client pour protéger AD.

####Option 1 :

S’il existe un petit nombre d’applications et un seul contrôleur de domaine dans l’environnement, et que vous basculez l’intégralité du site, nous recommandons l’utilisation de la réplication ASR pour répliquer l’ordinateur contrôleur de domaine sur le site secondaire (applicable à la fois pour les scénarios de site à site et de site à Azure). La même machine virtuelle répliquée peut également servir pour le test de basculement.

####Option 2 :
S’il existe un grand nombre d’applications et plus d’un contrôleur de domaine dans l’environnement, ou si vous envisagez de basculer plusieurs applications à la fois, outre l’activation de la réplication ASR sur la machine virtuelle du contrôleur de domaine (à utiliser pour le test de basculement), nous recommandons que vous configuriez également un contrôleur de domaine supplémentaire sur le site de récupération d’urgence (site local secondaire ou dans Azure).

>[AZURE.NOTE]Même si vous choisissez l’option 2, pour effectuer un test de basculement, il vous sera toujours demandé de configurer la réplication ASR pour le contrôleur de domaine. Consultez la section [Considérations en matière de test de basculement](#considerations-for-test-failover) pour plus de détails.


Les sections ci-dessous expliquent comment activer la protection sur le contrôleur de domaine dans ASR et comment configurer un contrôleur de domaine dans Azure.


##Composants requis

La mise en œuvre de la récupération d’urgence pour AD à l’aide d’Azure Site Recovery requiert que les conditions préalables suivantes soient en place.

- Un déploiement local d’Active Directory et du serveur DNS
- Le coffre Azure Site Recovery Services a été créé dans l’abonnement Microsoft Azure 
- Si Azure est votre site de récupération d’urgence, exécutez l’outil d’évaluation de la disponibilité des machines virtuelles Azure sur des machines virtuelles, afin de vérifier qu’elles sont compatibles avec les machines virtuelles Azure et Azure Site Recovery Services


##Activer la protection pour le contrôleur de domaine/DNS à l’aide d’ASR


###Protéger la machine virtuelle
Activez la protection de la machine virtuelle du contrôleur de domaine/DNS dans ASR. Effectuez une configuration Azure Site Recovery appropriée selon que la machine virtuelle est déployée sur Hyper-V ou sur VMware. La fréquence de cohérence des incidents recommandée à configurer est de 15 minutes.

###Configurer les paramètres réseau de la machine virtuelle
- Pour la machine virtuelle du contrôleur de domaine/DNS, configurez les paramètres réseau dans ASR afin que la machine virtuelle soit connectée au réseau approprié après un basculement. 
- Par exemple, lorsque vous utilisez Azure en tant que site de récupération d’urgence, vous pouvez sélectionner la machine virtuelle dans le cloud VMM ou dans le groupe de protection afin de configurer les paramètres réseau comme indiqué dans l’image ci-dessous.
- 
![Paramètres réseau de la machine virtuelle](./media/site-recovery-active-directory/VM-Network-Settings.png)

##Activer la protection pour AD à l’aide de la réplication AD
###Scénario de site à site

Créez un contrôleur de domaine sur le site (de récupération d’urgence) secondaire et, lors de la promotion du serveur au rôle de contrôleur de domaine, attribuez le nom du domaine utilisé sur le site principal. Vous pouvez utiliser le composant logiciel enfichable Sites et services Active Directory pour configurer les paramètres sur l’objet de lien de sites auquel les sites sont ajoutés. En configurant des paramètres sur un lien de sites, vous pouvez contrôler le moment où la réplication a lieu entre deux sites ou plus, ainsi que la fréquence. Consultez [Planification de la réplication entre des sites](https://technet.microsoft.com/fr-FR/library/cc731862.aspx) pour plus de détails.

###Scénario de site à Azure
Suivez ces instructions pour créer un [contrôleur de domaine dans un réseau virtuel Azure](../virtual-network/virtual-networks-install-replica-active-directory-domain-controller.md). Lors de la promotion du serveur au rôle de contrôleur de domaine, attribuez le nom du domaine utilisé sur le site principal.

Après cela, vous devez [reconfigurer le serveur DNS pour le réseau virtuel](../virtual-network/virtual-networks-install-replica-active-directory-domain-controller.md#reconfigure-dns-server-for-the-virtual-network) afin de l’utiliser dans Azure
  
![Réseau Azure](./media/site-recovery-active-directory/azure-network.png)

##Considérations en matière de test de basculement
Le test de basculement est effectué dans un réseau isolé du réseau de production afin qu’il n’y ait aucun impact sur la charge de travail de production. La plupart des applications requièrent également la présence d’un contrôleur de domaine et d’un serveur DNS pour fonctionner. Par conséquent, avant qu’une application soit basculée, un contrôleur de domaine doit être créé dans le réseau isolé à utiliser pour le test de basculement. Pour ce faire, le plus simple consiste à tout d’abord activer la protection sur la machine virtuelle du contrôleur de domaine/DNS à l’aide d’ASR et, avant de déclencher le test de basculement du plan de récupération d’urgence de l’application, à déclencher le test de basculement de la machine virtuelle du contrôleur de domaine. Vous trouverez ci-dessous les instructions étape par étape pour effectuer le test de basculement :

1. Activez la protection sur la machine virtuelle du contrôleur de domaine/DNS comme vous le feriez pour toute autre machine virtuelle.
2. Créez un réseau isolé. Tout réseau virtuel créé dans Azure par défaut est isolé des autres réseaux. Il est recommandé que la plage IP pour ce réseau soit identique à celle de votre réseau de production. N’activez pas la connectivité de site à site sur ce réseau.
3. Fournissez l’adresse IP DNS dans le réseau créé à l’étape précédente comme l’adresse IP que vous attendez que la machine virtuelle du DNS obtienne. Si vous utilisez Azure en tant que site de récupération d’urgence, vous pouvez fournir l’adresse IP de la machine virtuelle qui sera utilisée lors du basculement dans le paramètre Adresse IP cible, dans les propriétés de la machine virtuelle. Si votre site de récupération d’urgence est local et que vous utilisez le protocole DHCP, suivez les instructions fournies ici pour [configurer les systèmes DNS et DHCP pour le test de basculement](site-recovery-failover.md#prepare-dhcp). 

>[AZURE.NOTE]L’adresse IP affectée à une machine virtuelle dans un test de basculement est identique à l’adresse IP qu’elle obtiendrait dans un basculement planifié ou non planifié, étant donné que cette adresse IP est disponible dans le réseau de test de basculement. Si la même adresse IP n’est pas disponible dans le réseau de test de basculement, la machine virtuelle obtient une autre adresse IP disponible dans le réseau de test de basculement.

4. Accédez à la machine virtuelle du contrôleur de domaine et effectuez le test de basculement de celle-ci dans le réseau isolé. 
5. Effectuez le test de basculement du plan de récupération d’urgence de l’application.
6. Une fois le test terminé, marquez le test de basculement de la tâche de la machine virtuelle du contrôleur de domaine et le plan de récupération d’urgence comme terminés dans l’onglet des tâches d’ASR. 

###DNS sur une machine virtuelle différente de celle du contrôleur de domaine : 
En cas de DNS figurant sur une machine virtuelle différente de celle du contrôleur de domaine, vous devez créer un DNS pour le test de basculement. Si le DNS et le contrôleur de domaine figurent sur la même machine virtuelle, vous pouvez ignorer cette section. Vous pouvez utiliser un nouveau serveur DNS et créer toutes les zones requises. Par exemple, si votre domaine Active Directory est contoso.com, vous pouvez créer une zone portant le nom contoso.com. Les entrées correspondant à Active Directory doivent être mises à jour dans le système DNS. Procédez comme suit :

- Vérifiez que les paramètres suivants sont en place avant toute apparition d’une autre machine virtuelle du plan de récupération :
	- La zone doit être nommée en fonction du nom racine de la forêt.
	- La zone doit être sauvegardée dans un fichier.
	- La zone doit être activée pour les mises à jour sécurisées et non sécurisées.
	- Le programme de résolution de la machine virtuelle du contrôleur de domaine doit pointer vers l’adresse IP de la machine virtuelle du DNS.
- Exécutez la commande suivante dans le répertoire de la machine virtuelle du contrôleur de domaine : nltest /dsregdns

- **Ajout d’une zone** : utilisez l’exemple de script suivant pour ajouter une zone sur le serveur DNS, autoriser les mises à jour non sécurisées et ajouter une entrée pour ce dernier dans le service DNS :

	    dnscmd /zoneadd contoso.com  /Primary 
	    dnscmd /recordadd contoso.com  contoso.com. SOA %computername%.contoso.com. hostmaster. 1 15 10 1 1 
	    dnscmd /recordadd contoso.com %computername%  A <IP_OF_DNS_VM> 
	    dnscmd /config contoso.com /allowupdate 1


##Résumé
À l'aide d'Azure Site Recovery, vous pouvez créer un plan de récupération d'urgence automatisé complet pour votre AD. En cas d’interruption, vous pouvez lancer le basculement en quelques secondes, où que vous soyez, et bénéficier d’un AD opérationnel en quelques minutes. Si vous avez un AD pour plusieurs applications, comme SharePoint et SAP dans votre site principal, et que vous décidez de basculer le site complet, vous pouvez basculer AD à l’aide d’ASR en premier lieu, puis basculer les autres applications à l’aide de plans de récupération d’urgence spécifiques à chaque application.


Consultez [Aide relative à la charge de travail Site Recovery](../site-recovery/site-recovery-workload.md) pour plus d’informations sur la protection de différentes charges de travail à l’aide d’ASR.

<!---HONumber=Oct15_HO3-->