<properties
	pageTitle="Conception de votre infrastructure réseau pour la récupération d’urgence | Microsoft Azure"
	description="Cet article aborde les considérations de conception de réseau pour Azure Site Recovery."
	services="site-recovery"
	documentationCenter=""
	authors="prateek9us"
	manager="jwhit"
	editor=""/>

<tags
	ms.service="site-recovery"
	ms.devlang="na"
	ms.topic="article"
	ms.tgt_pltfrm="na"
	ms.workload="storage-backup-recovery"
	ms.date="02/22/2016"
	ms.author="pratshar"/>

#  Conception de votre infrastructure réseau pour la récupération d’urgence

Cet article s’adresse aux professionnels de l’informatique, chargés de l’architecture, de l’implémentation et de la prise en charge de l’infrastructure BCDR (continuité des activités et récupération d’urgence), et qui souhaitent tirer parti de Microsoft Azure Site Recovery (ASR) pour prendre en charge et améliorer leurs services BCDR. Ce document traite de considérations pratiques pour le déploiement de serveur System Center Virtual Machine Manager, et des avantages et inconvénients des sous-réseaux étirés et du basculement de sous-réseau. Il indique également comment structurer la récupération d’urgence sur les sites virtuels dans Microsoft Azure.

## Vue d’ensemble

[Azure Site Recovery (ASR)](https://azure.microsoft.com/services/site-recovery/) est un service Microsoft Azure qui organise la protection et la récupération de vos applications virtualisées pour les besoins de scénarios BCDR. Ce document est conçu pour guider le lecteur dans le processus de conception des réseaux, en mettant l’accent sur l’architecture des plages d’adresses IP et des sous-réseaux sur le site de récupération d’urgence, lors de la réplication des machines virtuelles à l’aide de Site Recovery.

En outre, cet article montre comment Site Recovery permet de créer et d’implémenter un centre de données virtuel multisite pour prendre en charge les services BCDR lors d’un test ou d’une urgence.

Dans un monde où chacun compte sur une connectivité 24 heures sur 24, 7 jours sur 7, il est plus important que jamais de maintenir votre infrastructure et vos applications opérationnelles. L’objectif de BCDR est de restaurer les composants en échec afin que l’organisation puisse reprendre rapidement son fonctionnement normal. Il est très difficile de développer des stratégies de récupération d’urgence pour gérer les problèmes et les événements dévastateurs. Cela est dû à la difficulté inhérente à la prédiction, en particulier en matière d’événements improbables, et aux coûts élevés des mesures de protection appropriées contre les catastrophes importantes.

Essentiels pour la planification de BCDR, un objectif de délai de récupération (RTO) et un objectif de point de récupération (RPO) doivent être définis dans le cadre d’un plan de récupération d’urgence. Si un sinistre frappe le centre de données d’un client, en utilisant Azure Site Recovery, il peut rapidement (RTO faible) mettre en ligne ses machines virtuelles répliquées situées dans le centre de données secondaire ou dans Microsoft Azure avec un minimum de perte de données (RPO faible).

Le basculement est rendu possible par ASR, qui copie initialement les machines virtuelles désignées du centre de données principal vers le centre de données secondaire ou vers Azure (en fonction du scénario), et qui actualise ensuite régulièrement les réplicas. Lors de la planification de l’infrastructure, la conception réseau doit être considérée comme un goulot d’étranglement potentiel, qui peut vous empêcher de remplir les objectifs RTO et RPO de la société.

Si un administrateur envisage de déployer une solution de récupération d’urgence, l’une des questions clés qu’il doit se poser concerne l’accessibilité de la machine virtuelle à l’issue du basculement. À l’aide du mappage réseau dans ASR, l’administrateur peut choisir le réseau auquel la machine virtuelle sera associée après le basculement. Pour plus d’informations sur le mappage réseau, consultez l’article [Préparer le mappage réseau](site-recovery-network-mapping.md).

Lors de la conception du réseau pour le site de récupération, l’administrateur a deux possibilités :

- Utiliser une autre plage d’adresses IP pour le réseau sur le site de récupération. Dans ce scénario, à l’issue du basculement, la machine virtuelle obtient une nouvelle adresse IP, et l’administrateur doit effectuer une mise à jour DNS. Pour en savoir plus sur la procédure à suivre pour effectuer la mise à jour DNS, cliquez ici.
- Utiliser la même plage d’adresses IP pour le réseau sur le site de récupération. Dans certains scénarios, les administrateurs préféreront conserver les adresses IP dont ils disposent sur le site principal, même après le basculement. Dans un scénario normal, un administrateur devrait mettre à jour les itinéraires pour indiquer le nouvel emplacement des adresses IP, mais dans le scénario où un réseau local virtuel étiré est déployé entre le site principal et les sites de récupération, conserver les adresses IP des machines virtuelles est alors une option intéressante. Conserver les mêmes adresses IP permet de simplifier le processus de récupération en supprimant les étapes post-basculement associées au réseau.

Ce document décrit deux considérations techniques principales pour la conception de l’espace d’adressage de votre réseau pour la récupération d’urgence :

- Conception de la topologie du déploiement du gestionnaire de structure (System Center Virtual Machine Manager) pour protéger les charges de travail via ASR.
- Gestion des adresses IP dans un environnement de récupération d’urgence.


## Conception du déploiement de System Center Virtual Machine Manager (SCVMM) pour Azure Site Recovery

Les exigences professionnelles déterminent les décisions en matière de topologie. En fonction de ces exigences, une organisation peut décider d’avoir plusieurs centres de données gérés par un seul responsable de gestion ou d’avoir plusieurs responsables de gestion qui gèrent différents centres de données. Cette section explique comment une organisation conçoit une topologie de déploiement SCVMM pour protéger les charges de travail avec ASR.

Azure Site Recovery contribue à mettre en œuvre la stratégie de continuité des activités et de récupération d’urgence de votre entreprise en coordonnant la réplication, le basculement et la récupération de machines virtuelles dans divers scénarios de déploiement. Basée sur le principe de simplicité de la conception, ASR est modélisée afin de permettre aux administrateurs de créer une solution de récupération d’urgence qui s’appuie sur leurs topologies de centre de données actuelles. ASR permet ainsi aux organisations de choisir une topologie adaptée au mieux à leurs besoins professionnels.

Pour obtenir la liste complète des scénarios de déploiement, consultez l’article [Qu’est-ce que Site Recovery ?](site-recovery-overview.md)

Les clients peuvent protéger les charges de travail sur un serveur VMM sur le site principal à l’aide d’un serveur VMM gérant le site secondaire. En cas de sinistre, toutes les charges de travail basculent en toute transparence du site principal vers le site secondaire, qui est géré par le serveur VMM secondaire. Toutefois, il peut arriver que certaines organisations préfèrent utiliser un seul serveur VMM pour gérer l’ensemble de leurs centres de données afin d’éviter une surcharge de gestion. Pour un client disposant de cette topologie, il est impératif de pouvoir récupérer le serveur VMM avant les charges de travail. Par conséquent, un client doit concevoir avec précaution sa topologie VMM afin que les charges de travail « résistent aux sinistres ».

Si votre infrastructure ne contient qu’un serveur VMM, vous pouvez soit déployer Azure Site Recovery pour répliquer les machines virtuelles des clouds VMM vers Azure, soit effectuer des réplications entre les clouds d’un même serveur VMM. Pour la récupération, vous devez basculer manuellement sur le serveur VMM sans être connecté à la console Azure Site Recovery, en utilisant Réplica Hyper-V dans la console Gestionnaire Hyper-V. Il est recommandé de déployer le serveur VMM dans l’une des topologies suivantes pour pouvoir récupérer les charges de travail avec les meilleurs RTO et RPO possible.

### Déploiement autonome

Dans cette topologie, vous allez déployer un serveur VMM autonome sur une machine virtuelle sur le site principal, puis répliquer cette machine virtuelle sur un site secondaire avec Azure Site Recovery et Réplica Hyper-V. Dans certains cas, installer le serveur VMM et son serveur SQL Server de support sur une même machine virtuelle permet de réduire les temps d’arrêt, car une seule machine virtuelle doit être instanciée. Lorsque VMM utilise un SQL Server distant, vous devez récupérer l’instance SQL Server avant de récupérer le serveur VMM.

Pour déployer un seul serveur VMM sur une machine virtuelle avec Réplica Hyper-V activé, procédez comme suit :

1. Configurez le VMM sur une machine virtuelle avec SQL Server installé.
2. Ajoutez les hôtes qui doivent être gérés dans les clouds sur ce serveur VMM.
3. Connectez-vous au portail Azure, puis configurez les clouds pour la protection.
4. Activez la réplication pour toutes les machines virtuelles qui doivent être protégées par le serveur VMM.
5. Accédez à la console Hyper-V Manager, choisissez Réplica Hyper-V, puis activez la réplication sur la machine virtuelle VMM. Assurez-vous que la machine virtuelle VMM n'est pas ajoutée aux clouds protégés par le service Azure Site Recovery afin que les paramètres de Réplica Hyper-V ne soient pas remplacés par ceux d’Azure Site Recovery.

En cas de sinistre, vous pouvez récupérer les charges de travail en procédant comme suit :

1. Basculez la machine virtuelle VMM de réplication sur le site de récupération à l’aide du Gestionnaire Hyper-V.
2. Une fois la machine virtuelle VMM récupérée, l'utilisateur peut se connecter à Hyper-V Recovery Manager à partir du site secondaire.
3. Lorsque le basculement non planifié est terminé, l’utilisateur peut accéder à toutes les ressources sur le site principal.


Dans cette topologie, l’utilisateur doit nécessairement basculer manuellement la machine virtuelle VMM sur le site secondaire pour pouvoir basculer les charges de travail.

![Autonome](./media/site-recovery-network-design/standalone.png)

### Déploiement de cluster


Le déploiement d’un serveur VMM hautement disponible est une méthode permettant d’adapter le service VMM proprement dit aux clusters. Cette opération est utile si des charges de travail critiques sont gérées par le serveur VMM, car elle garantit la disponibilité de ces charges et assure une protection en cas de défaillance du serveur VMM. Il est extrêmement important que le serveur VMM soit disponible à tout moment. Rendre le service adapté aux clusters permet de protéger le serveur VMM contre le basculement matériel de l’hôte sur lequel le serveur VMM s’exécute, mais aussi de garantir une protection contre les problèmes qui peuvent se produire sur la machine virtuelle sur laquelle VMM s’exécute. Le déploiement d’un serveur VMM à haute disponibilité requiert la présence du serveur VMM dans un cluster de basculement Windows Server. Pour plus d’informations sur le déploiement d’un serveur VMM à haute disponibilité, consultez le billet de blog de System Center ici.

Lors de la protection des charges de travail à l’aide d’ASR, le serveur VMM doit être déployé sur un cluster étendu entre des sites séparés géographiquement, comme illustré figure 2. La base de données SQL Server utilisée par VMM doit être protégée avec des groupes de disponibilité SQL Server AlwaysOn avec un réplica sur le site secondaire. Si un problème survient, le serveur VMM et sa base de données SQL Server correspondante basculent automatiquement vers le site de récupération, après quoi toutes les charges de travail peuvent être basculées via ASR.

![Cluster VMM étiré](./media/site-recovery-network-design/network-design1.png)

Figure 2

## Conception de l’espace d’adressage de réseau dans un environnement de récupération d’urgence

Si un administrateur envisage de déployer une solution de récupération d’urgence, l’une des questions clés qu’il doit se poser concerne l’accessibilité des applications à l’issue du basculement. Dans une certaine mesure, les applications modernes sont presque toujours dépendantes de la mise en réseau. Par conséquent, le déplacement physique d’un service d’un site à un autre représente un défi pour la mise en réseau. Deux méthodes principales permettent de traiter ce problème dans les solutions de récupération d’urgence. La première approche consiste à conserver des adresses IP fixes. Malgré les déplacements de services et les serveurs d'hébergement qui se trouvent dans différents emplacements physiques, les applications prennent avec elles la configuration de l'adresse IP dans le nouvel emplacement. La seconde approche implique de changer complètement l’adresse IP lors de la transition vers le site récupéré. Chaque approche présente plusieurs variantes d’implémentation qui sont résumées ci-dessous.

Lors de la conception du réseau pour le site de récupération, l’administrateur a deux possibilités :

### Option 1 : Conserver les adresses IP 

Si l’on considère le processus de récupération d’urgence, l’utilisation d’adresses IP fixes semble être la méthode la plus simple à implémenter, mais elle présente plusieurs difficultés potentielles qui la rendent moins populaire dans la pratique. Azure Site Recovery offre la possibilité de conserver les adresses IP dans tous les scénarios. Avant de décider de conserver les adresses IP, il convient de réfléchir aux contraintes que cette méthode impose sur les fonctionnalités de basculement. Examinons les facteurs qui peuvent vous aider à prendre une décision pour conserver ou non les adresses IP. Dans cette catégorie, il existe deux principaux sous-types : le sous-réseau étiré et le basculement de sous-réseau. Nous aborderons séparément les scénarios lorsqu’il existe un basculement de sous-réseau ou un sous-réseau étiré sur deux emplacements.

#### Sous-réseau étiré

Ici, le sous-réseau est disponible simultanément sur le site principal et sur le site de récupération d’urgence. En termes simples, cela signifie que vous pouvez déplacer un serveur et sa configuration IP (couche 3) sur le site secondaire, et que le réseau acheminera automatiquement le trafic vers ce nouveau site. Cela est très facile à gérer du point de vue du serveur mais présente un certain nombre de défis :

- Du point de vue de la couche 2 (couche de liaison de données), un équipement de mise en réseau capable de gérer un VLAN étiré est nécessaire, mais cela n’est plus un problème, car ce type d’équipement est désormais largement disponible. Le deuxième problème, plus difficile, est qu’en étirant le VLAN, le domaine d’erreur potentielle est étendu aux deux sites, et devient essentiellement un point de défaillance unique. Bien que cela soit peu probable, il est arrivé qu’une tempête de diffusion commence, sans pouvoir être isolée. Sur ce dernier problème, les avis sont partagés. Nombre d’implémentations ont été réussies, tandis que des organisations ne souhaitent pas du tout entendre parler de cette technologie.
- La méthode utilisant un sous-réseau étiré n’est pas possible si vous utilisez Microsoft Azure comme site de récupération d’urgence.


#### Basculement d’un sous-réseau

Il est possible d’implémenter le basculement de sous-réseau pour bénéficier des avantages de la solution de sous-réseau étiré, décrite ci-dessus sans étirer le sous-réseau sur plusieurs sites. Dans ce cas, n’importe quel sous-réseau donné serait présent sur le site 1 ou le site 2, mais jamais simultanément sur les deux sites. Pour conserver l’espace d’adressage IP en cas de basculement, il est possible de mettre en place un programme pour que l’infrastructure du routeur déplace les sous-réseaux d’un site à un autre. Dans un scénario de basculement, les sous-réseaux se déplacent avec les machines virtuelles protégées associées. Le principal inconvénient de cette approche est que vous devez déplacer l’ensemble du sous-réseau en cas de défaillance, ce qui peut ne pas poser de problèmes, mais peut affecter les considérations relatives à la granularité du basculement.

Examinons comment l’entreprise fictive nommée Contoso est en mesure de répliquer ses machines virtuelles vers un emplacement de récupération, tout en basculant l’ensemble du sous-réseau. Nous allons commencer par examiner comment Contoso peut gérer ses sous-réseaux tout en répliquant des machines virtuelles entre deux emplacements locaux, puis nous allons voir le fonctionnement du basculement de sous-réseau lorsqu’Azure est utilisé en tant que site de récupération d’urgence.

##### Basculement de sous-réseau : Récupération d’urgence de niveau entreprise

Examinons un scénario dans lequel nous souhaitons conserver l’adresse IP de chacune des machines virtuelles, et basculer simultanément tout le sous-réseau. Le site principal possède des applications qui s'exécutent dans le sous-réseau 192.168.1.0/24. Lorsque le basculement se produit, toutes les machines virtuelles qui font partie de ce sous-réseau basculent vers le site de récupération et conservent leur adresse IP. Les itinéraires doivent être modifiés en conséquence afin de refléter le fait que toutes les machines virtuelles appartenant au sous-réseau 192.168.1.0/24 ont été déplacées vers le site de récupération.

Dans l’illustration suivante, les itinéraires existant entre le site principal et le site de récupération, le troisième site et le site principal, et entre le troisième site et le site de récupération doivent être modifiés comme il convient. La version initiale de ce document repose sur les hypothèses suivantes :

- Chaque centre de données est traité par sa propre instance SCVMM. Il n'y aura aucune réplication des bases de données System Center VMM entre les centres de données.
- Chaque centre de données utilise des adresses IP statiques pour les machines virtuelles.
- La connectivité entre les centres de données s’effectue via un circuit dédié et non via la connectivité VPN sur Internet.

	![Conserver l’adresse IP](./media/site-recovery-network-design/network-design3.png)

	Figure 3

	![Conserver l’adresse IP](./media/site-recovery-network-design/network-design2.png)
	
	Figure 4

Lorsque vous activez la protection d’une machine virtuelle spécifique, ASR alloue des ressources réseau conformément au flux de travail suivant :

- ASR alloue une adresse IP pour chaque interface réseau sur la machine virtuelle à partir du pool d’adresses IP statiques défini sur le réseau approprié pour chaque instance SCVMM.
- Si l’administrateur définit le même pool d’adresses IP pour le réseau du site de récupération que celui utilisé pour le réseau du site principal tout en allouant l’adresse IP à la machine virtuelle de réplication, ASR alloue la même adresse IP que celle de la machine virtuelle principale. L’adresse IP est réservée dans VMM, mais elle n’est pas définie comme adresse IP de basculement. L’adresse IP de basculement est définie juste avant le basculement.

	![Conserver l’adresse IP](./media/site-recovery-network-design/network-design4.png)
	
	Figure 5

La figure 5 montre les paramètres TCP/IP de basculement de la machine virtuelle de réplication (sur la console Hyper-V). Ces paramètres sont renseignés juste avant le démarrage de la machine virtuelle après un basculement.

Si cette même adresse IP n’est pas disponible, ASR alloue une autre adresse IP disponible à partir du pool d’adresses IP défini.

Une fois la machine virtuelle activée pour la protection, vous pouvez utiliser l’exemple de script suivant pour vérifier l’adresse IP qui lui a été allouée. La même adresse IP est définie comme adresse IP de basculement et affectée à la machine virtuelle au moment du basculement :

![Conserver l’adresse IP](./media/site-recovery-network-design/script.png)

>[AZURE.NOTE] Dans le cas où les machines virtuelles utilisent DHCP, la gestion des adresses IP se trouve complètement hors du contrôle d’ASR. Un administrateur doit s’assurer que le serveur DHCP qui fournit les adresses IP sur le site de récupération peut fournir des adresses provenant de la même plage que celle du site principal.

##### Basculement local de sous-réseaux : Récupération d’urgence dans Azure

ASR permet d’utiliser Microsoft Azure comme site de récupération d’urgence pour vos machines virtuelles. Dans ce cas, vous devez gérer plusieurs contraintes.

Examinons un scénario dans lequel la société fictive Woodgrove Bank dispose d’une infrastructure locale, qui héberge ses applications métier, et d’Azure qui héberge ses applications mobiles. La connectivité entre les machines virtuelles de la Woodgrove Bank dans Azure et les serveurs locaux est assurée par un réseau privé virtuel de site à site. Ce réseau privé virtuel de site à site permet au réseau virtuel dans Azure de la Woodgrove Bank d’être considéré comme une extension du réseau local de la Woodgrove Bank. Cette communication est permise par le réseau privé virtuel de site à site entre le périmètre de la Woodgrove Bank et le réseau virtuel Azure. À présent, la Woodgrove Bank souhaite utiliser ASR pour répliquer ses charges de travail s’exécutant dans son centre de données vers Azure. Cette option répond aux besoins de la Woodgrove Bank, qui veut une option de récupération d’urgence économique et qui souhaite pouvoir stocker des données dans les environnements de cloud public. La Woodgrove Bank doit gérer des applications et des configurations qui dépendent des adresses IP codées en dur. La société demande donc à conserver les adresses IP de ses applications après le basculement vers Azure.

L’infrastructure locale de la société Woodgrove Bank est gérée par un serveur VMM 2012 R2. Un réseau logique de type VLAN, nommé réseau d’application, a été créé sur le serveur SCVMM. Un réseau de machines virtuelles, nommé réseau de machines virtuelles d’application, est créé à l’aide du réseau logique. Comme toutes les machines virtuelles de l’application utilisent des adresses IP statiques, un pool d’IP statiques est également défini pour le réseau logique.

**Réseau logique**

![Réseau logique](./media/site-recovery-network-design/network-design5.png)

Figure 6

**Réseau de machines virtuelles**

![Réseau de machines virtuelles](./media/site-recovery-network-design/network-design6.png)

Figure 7

La Woodgrove Bank décide d’affecter des adresses IP de la plage 172.16.1.0/24 à 172.16.2.0/24 à ses ressources exécutées dans Azure.

Pour que la Woodgrove Bank puisse répliquer ses machines virtuelles vers Azure tout en conservant les adresses IP, un réseau virtuel Azure doit être créé. Ce doit être une extension du réseau local pour que les applications puissent basculer du site local vers Azure en toute transparence. Azure vous permet d’ajouter une connectivité de type réseau privé virtuel de site à site et de point à site aux réseaux virtuels créés dans Azure. Lors de la configuration de votre connexion de site à site, un réseau Azure vous permet d’acheminer le trafic vers l’emplacement local (qu’Azure appelle « réseau local ») uniquement si la plage d’adresses IP est différente de la plage d’adresses IP locale, car Azure ne prend pas en charge les sous-réseaux étirés. Cela signifie que si vous avez un sous-réseau local 192.168.1.0/24, vous ne pouvez pas ajouter un réseau local 192.168.1.0/24 dans le réseau Azure. Ceci est dû au fait que Azure ne sait pas qu'aucune machine virtuelle n’est active dans le sous-réseau et que le sous-réseau est créé uniquement à des fins de récupération d'urgence. Pour pouvoir acheminer correctement le trafic réseau à partir d’un réseau Azure, les sous-réseaux du réseau et le réseau local ne doivent pas entrer en conflit.

![Basculement de sous-réseau](./media/site-recovery-network-design/network-design7.png)

Figure 8

Pour aider la Woodgrove Bank à satisfaire à ses exigences professionnelles, nous devons implémenter les flux de travail suivants :

- Créez un autre réseau, que nous allons appeler réseau de récupération, où les machines virtuelles ayant basculé seraient créées.
- Pour vous assurer que l’adresse IP d’une machine virtuelle est conservée après un basculement, accédez à l’onglet Configurer, dans les propriétés de la machine virtuelle, spécifiez la même adresse IP que celle utilisée par la machine virtuelle en local, puis cliquez sur Enregistrer. Au basculement de la machine virtuelle, Azure Site Recovery affecte l’adresse IP fournie à la machine virtuelle. 

	![Propriétés du réseau](./media/site-recovery-network-design/network-design8.png)

	Figure 9

Une fois le basculement déclenché et les machines virtuelles créées dans le réseau de récupération avec l’adresse IP de votre choix, la connectivité avec ce réseau peut être établie à l’aide d’une connexion de réseau virtuel à réseau virtuel. Si nécessaire, cette action peut faire l’objet d’un script. Comme expliqué dans la section précédente sur le basculement de sous-réseau, même en cas de basculement vers Azure, les itinéraires doivent être modifiés comme il convient pour montrer que l’adresse IP 192.168.1.0/24 a été déplacée vers Azure.

![Propriétés du réseau](./media/site-recovery-network-design/network-design9.png)

Figure 10

### Option 2 : Modifier les adresses IP

D’après les auteurs, c’est cette approche qui semble la plus répandue. Cette approche consiste à changer l’adresse IP de toutes les machines virtuelles impliquées dans le basculement. L’inconvénient de cette approche est qu’elle implique que le réseau entrant « sache » que l’application qui était à l’adresse IPx est désormais à l’adresse IPy. Même si les adresses IPx et IPy sont des noms logiques, les entrées DNS doivent généralement être modifiées ou vidées dans le réseau, et les entrées mises en cache dans les tables de réseaux doivent être mises à jour ou vidées. Il est donc possible qu’un temps d’arrêt ait lieu en fonction de la configuration de l’infrastructure DNS. Ces problèmes peuvent être atténués en utilisant des valeurs TTL faibles dans le cas d’applications intranet et en utilisant [Azure Traffic Manager avec ASR](http://azure.microsoft.com/blog/2015/03/03/reduce-rto-by-using-azure-traffic-manager-with-azure-site-recovery/) pour les applications basées sur Internet.

#### Modification des adresses IP : Récupération d’urgence de niveau entreprise

Examinons le scénario dans lequel vous prévoyez d’utiliser différentes adresses IP sur le site principal et le site de récupération. Dans l’exemple suivant, nous disposons également d’un troisième site à partir duquel les applications hébergées sur le site principal ou le site de récupération sont accessibles.

![Adresse IP différente](./media/site-recovery-network-design/network-design10.png)

Figure 11

Figure 11, certaines applications sont hébergées dans le sous-réseau 192.168.1.0/24 sur le site principal, et elles sont configurées pour arriver sur le site de récupération dans le sous-réseau 172.16.1.0/24 après un basculement. Les itinéraires du réseau/des connexions VPN ont été correctement configurés pour que les trois sites puissent accéder l’un à l'autre.
 
Comme illustré figure 12, après leur basculement, les applications sont restaurées dans le sous-réseau de récupération. Dans ce cas, nous ne sommes pas contraints de basculer tout le sous-réseau en même temps. Aucune modification n’est nécessaire pour reconfigurer les itinéraires du réseau ou du réseau privé virtuel. Un basculement et certaines mises à jour DNS garantissent le maintien de l’accessibilité des applications. Si le système DNS est configuré pour autoriser des mises à jour dynamiques, les machines virtuelles peuvent alors s’inscrire à l’aide de la nouvelle adresse IP une fois qu’elles démarrent après un basculement.

![Adresse IP différente](./media/site-recovery-network-design/network-design11.png)

Figure 12

Après le basculement, il est possible que la machine virtuelle de réplication possède une adresse IP différente de celle de la machine virtuelle principale. Les machines virtuelles mettront à jour le serveur DNS qu'elles utilisent après leur démarrage. Les entrées DNS doivent généralement être modifiées ou supprimées du réseau, et es entrées mises en cache dans les tables de réseau doivent être mises à jour ou supprimées. Il n’est donc pas rare d’avoir des temps d'arrêt pendant que ces modifications ont lieu. Ce problème peut être atténué par :

- L’utilisation de valeurs TTL faibles pour les applications intranet.
- L’utilisation d’Azure Traffic Manager avec ASR pour les applications basées sur Internet.
- L’utilisation du script suivant dans votre plan de récupération pour mettre à jour le serveur DNS afin de garantir une mise à jour en temps voulu (le script n'est pas requis si l'inscription DNS dynamique est configurée)

![Adresse IP différente](./media/site-recovery-network-design/script2.png)

#### Modification des adresses IP : Récupération d’urgence dans Azure

Le billet de blog [Networking Infrastructure Setup for Microsoft Azure as a Disaster Recovery Site](http://azure.microsoft.com/blog/2014/09/04/networking-infrastructure-setup-for-microsoft-azure-as-a-disaster-recovery-site/) (Configuration de l’infrastructure de réseau de Microsoft Azure comme site de récupération d’urgence) explique comment configurer l’infrastructure de réseau d’Azure sans que la conservation des adresses IP ne soit nécessaire. Il commence par décrire l’application, examine la configuration de la mise en réseau localement et sur Azure, puis conclut par la procédure à suivre pour exécuter un test de basculement et un basculement planifié.




## Étapes suivantes

[Découvrir](site-recovery-network-mapping.md) comment Site Recovery mappe les réseaux source et cible.

<!---HONumber=AcomDC_0224_2016-->