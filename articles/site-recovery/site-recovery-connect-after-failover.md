<properties
	pageTitle="Connexion à des charges de travail après le basculement avec Azure Site Recovery | Microsoft Azure"
	description="Cet article présente les options de connexion aux charges de travail après le basculement avec Azure Site Recovery"
	services="site-recovery"
	documentationCenter=""
	authors="rayne-wiselman"
	manager="jwhit"
	editor=""/>

<tags
	ms.service="site-recovery"
	ms.devlang="na"
	ms.topic="article"
	ms.tgt_pltfrm="na"
	ms.workload="storage-backup-recovery"
	ms.date="02/15/2016"
	ms.author="raynew"/>

#  Connexion à des charges de travail après le basculement avec Azure Site Recovery

Le service Azure Site Recovery contribue à mettre en œuvre la stratégie de continuité des activités et de récupération d’urgence de votre entreprise en coordonnant la réplication, le basculement et la récupération de machines virtuelles et de serveurs physiques. Les machines peuvent être répliquées vers Azure ou vers un centre de données local secondaire. Pour obtenir un rapide aperçu, consultez [Qu’est-ce qu’Azure Site Recovery ?](site-recovery-overview.md)


## Vue d'ensemble

Dans le cadre de votre stratégie de continuité d’activité et de récupération d’urgence, vous devrez vous assurer que vous pouvez vous connecter aux charges de travail après un basculement. Cet article décrit les stratégies permettant de le faire.

Dans une certaine mesure, les applications modernes sont presque toujours dépendantes de la mise en réseau. Par conséquent, le déplacement physique d’un service d'un site à un autre représente un défi pour la mise en réseau pour garantir l’accès aux applications et aux charges de travail après un basculement. Deux solutions sont possibles pour résoudre ce problème :

- **Des adresses IP fixes** : la première approche consiste à conserver des adresses IP fixes. Malgré les déplacements de services et les serveurs d'hébergement qui se trouvent dans différents emplacements physiques, les applications prennent avec elles la configuration de l'adresse IP dans le nouvel emplacement.
- **Des adresses IP modifiées** : la seconde approche requiert une modification de l'adresse IP lors de la transition vers le site récupéré.

## Option 1 : des adresses IP fixes

Du point de vue de la récupération d’urgence, l’utilisation d’adresses IP fixes semble être la méthode la plus simple à appliquer. Toutefois, il existe un certain nombre de défis potentiels qui, en pratique, rendent cette approche moins attractive. Azure Site Recovery offre la possibilité de conserver les adresses IP dans tous les scénarios. Avant de décider d'adopter cette approche, nous allons examiner les considérations pour les principaux scénarios de basculement (sous-réseau étiré et basculement d’un sous-réseau).

- **Basculement d’un sous-réseau**: dans un sous-réseau étiré, le sous-réseau est disponible simultanément dans l’emplacement principal et l’emplacement cible. En termes simples, cela signifie que vous pouvez déplacer un serveur et sa configuration IP (couche 3) sur le site secondaire, et le réseau acheminera automatiquement le trafic vers le nouvel emplacement. Cela est très facile à gérer du point de vue du serveur mais présente un certain nombre de défis :
	- Du point de vue de la couche 2 (couche de liaison de données), un équipement de mise en réseau capable de gérer un VLAN étiré est nécessaire, bien que ce type d’équipement soit désormais largement disponible.
	- Le deuxième problème, plus difficile, est qu’en étirant le VLAN, le domaine d'erreur potentielle s'étend aux deux sites, et devient essentiellement un point de défaillance unique. Bien que cela soit peu probable, il est possible qu’une tempête de diffusion commence sans pouvoir être isolée. Nous avons eu plusieurs avis à ce sujet, allant de « implémentation réussie » à « jamais ».
	- Un sous-réseau étiré n’est pas possible si vous basculez vers Azure en tant que site de récupération d’urgence.
- **Sous-réseau étiré** : il est possible d'implémenter le basculement de sous-réseaux afin d'obtenir les avantages d'un sous-réseau étiré sans que celui-ci soit réellement étiré. Dans cette configuration, n’importe quel sous-réseau donné est présent sur le site 1 ou le site 2, mais jamais sur les deux sites simultanément. Pour conserver l'espace d'adressage IP en cas de basculement, vous pouvez mettre en place un programme pour que l'infrastructure du routeur déplace les sous-réseaux d'un site à un autre. Dans un scénario de basculement, les sous-réseaux se déplacent avec leurs machines virtuelles associées. Le principal inconvénient de cette approche est que, en cas de basculement, vous devez déplacer l’ensemble du sous-réseau - ce qui peut être une solution intéressante, mais cela peut affecter les considérations relatives à la granularité du basculement.


### Exemple : basculement de sous-réseaux dans une entreprise

Examinons comment une entreprise fictive (Contoso) est en mesure de répliquer ses machines virtuelles vers un emplacement de récupération, tout en basculant l’ensemble du sous-réseau. Nous allons examiner comment Contoso est en mesure de gérer ses sous-réseaux tout en répliquant des machines virtuelles entre deux emplacements locaux. Nous allons également décrire le fonctionnement du basculement d’un sous-réseau lorsqu’Azure est utilisé en tant que site de récupération d'urgence.

- Le site principal possède des applications qui s'exécutent dans le sous-réseau 192.168.1.0/24.
- L’ensemble du sous-réseau va basculer et toutes les machines virtuelles du sous-réseau vont basculer vers le site de récupération et conserver leur adresse IP.
- Comme indiqué dans le diagramme ci-dessous, les itinéraires entre le site principal et le site de récupération, le troisième site et le site principal, et le troisième site et le site de récupération doivent être correctement modifiés afin de montrer que toutes les machines virtuelles appartenant au sous-réseau 192.168.1.0/24 ont été déplacées vers le site de récupération.
- Ce diagramme part du principe que :
	-  Chaque centre de données est traité par sa propre instance de VMM. Il n'y aura aucune réplication des bases de données System Center VMM entre les centres de données.
	-  Chaque centre de données utilise des adresses IP statiques pour les machines virtuelles.
	-  La connectivité entre les centres de données se fait via un circuit dédié et non via une connectivité VPN sur internet.

	**Avant le basculement**

	![Avant le basculement](./media/site-recovery-connect-after-failover/network-design2.png)

	**Après le basculement**

	![Après le basculement](./media/site-recovery-connect-after-failover/network-design3.png)


Lorsque vous activez la protection d'une machine virtuelle spécifique, Site Recovery alloue des ressources réseau comme suit :

1. Site Recovery alloue une adresse IP pour chaque interface réseau sur la machine virtuelle à partir du pool d'adresses IP statiques défini sur le réseau approprié pour chaque instance VMM.
2. Si l'administrateur définit le même pool d'adresses IP qui est utilisé sur le site principal pour le réseau du site de récupération, Site Recovery alloue la même adresse IP allouée à la machine virtuelle principale à la machine virtuelle à l'adresse IP de machine virtuelle de réplication. L’adresse IP est réservée dans VMM mais n'est pas définie en tant qu'adresse IP de basculement. L'adresse IP de basculement est définie juste avant le basculement. Cette capture d'écran montre les paramètres TCP/IP de basculement de la machine virtuelle de réplication (sur la console Hyper-V). Ces paramètres sont répliqués juste avant le démarrage du basculement de la machine virtuelle.

	![Configurer l’adresse IP](./media/site-recovery-connect-after-failover/network-design4.png)

3. Si la même adresse IP n'est pas disponible, Site Recovery alloue une autre adresse du pool.
4. Une fois que la machine virtuelle est activée pour la protection, que vous pouvez utiliser l’exemple de script suivant pour vérifier l'adresse IP qui a été allouée à la machine virtuelle. La même adresse IP est définie comme adresse IP de basculement et est attribuée à la machine virtuelle au moment du basculement.

    $vm = Get-SCVirtualMachine -Name $na = $vm[0].VirtualNetworkAdapters $ip = Get-SCIPAddress -GrantToObjectID $na[0].id $ip.address

Notez que si les machines virtuelles utilisent DHCP, la gestion des adresses IP n'est pas gérée par Site Recovery. Vous devez vous assurer que le serveur DHCP qui alloue les adresses IP sur le site de récupération peut allouer des adresses de la même plage que celle du site principal.

### Exemple : basculement de sous-réseaux vers Azure

Si vous basculez vers Azure, il existe certaines contraintes supplémentaires. Prenons l’exemple d’une société fictive (Woodgrove Bank) qui dispose d’une infrastructure locale qui héberge leurs applications cœurs de métier et d’Azure qui héberge leurs applications mobiles.


- La connectivité entre les machines virtuelles de la société Woodgrove Bank dans Azure et les serveurs locaux s’effectue via un VPN de site à site qui affiche le réseau virtuel dans Azure comme une extension du réseau local de Woodgrove Bank.
- Woodgrove souhaite utiliser Site Recovery pour répliquer ses charges de travail locales vers Azure.
- Woodgrove gère des applications et des configurations qui dépendent des adresses IP codées en dur. La société doit donc conserver les adresses IP de leurs applications après le basculement vers Azure.
- L’infrastructure locale de la société Woodgrove Bank est gérée par un serveur VMM 2012 R2.
- Un réseau logique basé sur VLAN (réseau d’application) a été créé sur le serveur VMM. ![Réseau logique](./media/site-recovery-connect-after-failover/network-design5.png)
- Un réseau de machines virtuelles (réseau de machines virtuelles d’application) a été créé à l'aide du réseau logique. ![Réseau de machines virtuelles](./media/site-recovery-connect-after-failover/network-design6.png)
- Toutes les machines virtuelles de l'application utilisent des adresses IP statiques. Un pool d’IP statiques est donc également défini pour le réseau logique.
- Woodgrove affecte des adresses IP à partir de la plage d'adresses IP (172.16.1.0/24, 172.16.2.0/24) à ses ressources exécutées dans Azure.


Afin que Woodgrove puisse déployer la réplication et conserver les adresses IP, les conditions suivantes sont requises :

- Une machine virtuelle Azure qui est une extension du réseau local afin que les applications puissent basculer en toute transparence.
- Notez que lorsque vous configurez votre connexion de site à site dans Azure, un réseau Azure vous permet d'acheminer le trafic vers l'emplacement local (qu’Azure appelle « réseau local ») uniquement si la plage d'adresses IP est différente de la plage d'adresses IP locale, car Azure ne prend pas en charge les sous-réseaux étirés. Cela signifie que si vous avez un sous-réseau local 192.168.1.0/24, vous ne pouvez pas ajouter un réseau local 192.168.1.0/24 dans le réseau Azure. Ceci est dû au fait que Azure ne sait pas qu'aucune machine virtuelle n’est active dans le sous-réseau et que le sous-réseau est créé uniquement à des fins de récupération d'urgence. Pour pouvoir acheminer correctement le trafic réseau à partir d’un réseau Azure, les sous-réseaux du réseau et le réseau local ne doivent pas entrer en conflit.
- Nous allons devoir créer un réseau supplémentaire dans Azure (réseau de récupération) dans lequel les machines virtuelles basculées seront créées.

	![Réseaux Azure](./media/site-recovery-connect-after-failover/network-design7.png)

- Pour vous assurer que l'adresse IP de la machine virtuelle est conservée, nous allons préciser dans les propriétés de la machine virtuelle dans Site Recovery que la même adresse IP doit être utilisée. Après le basculement, Site Recovery attribuera l’adresse IP spécifiée à la machine virtuelle. ![Réseaux Azure](./media/site-recovery-connect-after-failover/network-design8.png)


- Lorsqu'un basculement est déclenché et que les machines virtuelles sont créées dans le réseau de récupération avec l'adresse IP requise, la connectivité à la machine virtuelle peut être établie à l'aide d'un. Cette action peut être incluse dans le script. Comme expliqué dans la section précédente sur le basculement d’un sous-réseau, en cas de basculement vers Azure, des itinéraires doivent également être correctement modifiés pour montrer que 192.168.1.0/24 a été déplacé vers Azure. ![Réseaux Azure](./media/site-recovery-connect-after-failover/network-design9.png)

## Option 2 : adresses IP modifiées

Cette approche semble être la plus courante et signifie que l'adresse IP de chaque machine virtuelle qui bascule sera modifiée. Le principal inconvénient de cette approche est que votre infrastructure réseau doit savoir que les adresses IP ont été modifiées et que les entrées DNS seront typiquement modifiées ou supprimées via le réseau, de même que les entrées mises en cache dans les tables de réseau. Cela peut entraîner un temps d'arrêt selon la façon dont l'infrastructure DNS est configurée. Ces problèmes peuvent être atténués en utilisant des valeurs TTL faibles dans le cas d'applications intranet et en utilisant [Azure Traffic Manager avec Site Recovery](https://azure.microsoft.com/blog/2015/03/03/reduce-rto-by-using-azure-traffic-manager-with-azure-site-recovery/) pour les applications basées sur internet.

### Exemple : adresses IP modifiées

Examinons ce scénario avec un exemple comprenant un troisième site à partir duquel les applications hébergées sur le site principal ou de récupération sont accessibles.

![Réseaux Azure](./media/site-recovery-connect-after-failover/network-design10.png)

- Certaines applications sont hébergées dans le sous-réseau 192.168.1.0/24, sous-réseau du site principal, et elles ont été configurées pour arriver sur le site de récupération dans le sous-réseau 172.16.1.0/24 après un basculement.
- Les itinéraires du réseau/des connexions VPN ont été correctement configurés pour que les trois sites puissent accéder l’un à l'autre.
- Après avoir basculé une ou plusieurs applications, elles seront restaurées dans le sous-réseau de récupération. Dans ce cas, il n’est pas nécessaire de basculer tout le sous-réseau en même temps et aucune modification n’est requise pour reconfigurer les itinéraires réseau ou VPN.
- Un basculement et certaines mises à jour DNS conserveront l’accès aux applications. Si le serveur DNS est configuré pour autoriser les mises à jour dynamiques, les machines virtuelles peuvent alors s'enregistrer à l'aide de la nouvelle adresse IP une fois qu'elles démarrent après un basculement.

	![Réseaux Azure](./media/site-recovery-connect-after-failover/network-design11.png)

- Après le basculement, il est possible que la machine virtuelle de réplication possède une autre adresse IP que celle de la machine virtuelle principale.
- Les machines virtuelles mettront à jour le serveur DNS qu'elles utilisent après leur démarrage. Les entrées DNS doivent généralement être modifiées ou supprimées du réseau, et es entrées mises en cache dans les tables de réseau doivent être mises à jour ou supprimées. Il n’est donc pas rare d’avoir des temps d'arrêt pendant que ces modifications ont lieu. Cela peut être atténuée par :

	- L’utilisation de valeurs TTL faibles pour les applications intranet.
	- L’utilisation d’[Azure Traffic Manager avec Site Recovery](https://azure.microsoft.com/blog/2015/03/03/reduce-rto-by-using-azure-traffic-manager-with-azure-site-recovery/) pour les applications basées sur Internet.
	- L’utilisation du script suivant dans votre plan de récupération pour mettre à jour le serveur DNS afin de garantir une mise à jour en temps voulu (le script n'est pas requis si l'inscription DNS dynamique est configurée)

    [string]$Zone, [string]$name, [string]$IP ) $Record = Get-DnsServerResourceRecord -ZoneName $zone -Name $name $newrecord = $record.clone() $newrecord.RecordData[0].IPv4Address = $IP Set-DnsServerResourceRecord -zonename $zone -OldInputObject $record -NewInputObject $Newrecord


## Étapes suivantes

[Ce billet de blog](https://azure.microsoft.com/blog/2014/09/04/networking-infrastructure-setup-for-microsoft-azure-as-a-disaster-recovery-site/) explique pas à pas comment configurer l'infrastructure de mise en réseau Azure requise lorsque la conservation des adresses IP n'est pas nécessaire. Il vous montre comment configurer le réseau localement et dans Azure. Le billet se termine par les instructions pour exécuter un test de basculement et un test de basculement planifié.

<!---HONumber=AcomDC_0218_2016-->