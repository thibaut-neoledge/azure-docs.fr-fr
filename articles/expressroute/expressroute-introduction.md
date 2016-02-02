<properties 
   pageTitle="Présentation d’ExpressRoute | Microsoft Azure"
   description="Cette page fournit une vue d'ensemble du service ExpressRoute, y compris du fonctionnement d’une connexion ExpressRoute."
   documentationCenter="na"
   services="expressroute"
   authors="cherylmc"
   manager="carolz"
   editor=""/>
<tags 
   ms.service="expressroute"
   ms.devlang="na"
   ms.topic="article" 
   ms.tgt_pltfrm="na"
   ms.workload="infrastructure-services" 
   ms.date="01/16/2016"
   ms.author="cherylmc"/>

# Présentation technique d’ExpressRoute

Microsoft Azure ExpressRoute vous permet d'étendre vos réseaux locaux au cloud de Microsoft via une connexion privée dédiée assurée par un fournisseur de connectivité. Grâce à ExpressRoute, vous pouvez établir des connexions aux services de cloud computing Microsoft, comme Microsoft Azure, Office 365 et CRM Online. La connectivité peut provenir d'un réseau universel (IP VPN), d’un réseau Ethernet point à point ou d’une interconnexion virtuelle via un fournisseur de connectivité dans un centre de colocalisation. Les connexions ExpressRoute ne sont pas établies par le biais de l'Internet public. Elles offrent ainsi de meilleurs niveaux de fiabilité, de rapidité, de latence et de sécurité que les connexions classiques sur Internet.

![](./media/expressroute-introduction/expressroute-basic.png)

**Voici les principaux avantages :**

- Connectivité de couche 3 entre votre réseau local et le cloud de Microsoft via un fournisseur de connectivité. La connectivité peut provenir d'un réseau universel (IPVPN), d’une connexion Ethernet point à point, ou d’une interconnexion virtuelle via un échange Ethernet.
- Connectivité aux services de cloud de Microsoft dans toutes les régions de la zone géopolitique.
- Connectivité globale aux services de Microsoft dans toutes les régions grâce au module complémentaire ExpressRoute premium.
- Routage dynamique entre votre réseau et Microsoft via des protocoles standard (BGP).
- Redondance intégrée dans chaque emplacement d'homologation pour une plus grande fiabilité.
- [SLA](http://azure.microsoft.com/support/legal/sla/) de disponibilité de la connexion.
- QoS et prise en charge de plusieurs classes de service pour les applications spéciales, notamment Skype Entreprise.

Pour plus d’informations, consultez le [Forum Aux Questions sur ExpressRoute](expressroute-faqs.md).

## Comment puis-je utiliser ExpressRoute pour connecter mon réseau à Microsoft ?

Vous pouvez créer une connexion entre votre réseau local et le cloud de Microsoft de trois façons différentes

1. **Colocalisation avec un échange de cloud.** Si vous êtes colocalisé avec une installation dans le cadre d’un échange de cloud, vous pouvez commander des interconnexions virtuelles vers le cloud de Microsoft via l’échange Ethernet du fournisseur de colocalisation. Les fournisseurs de colocalisation peuvent offrir des interconnexions de couche 2 ou des interconnexions de couche 3 gérées entre votre infrastructure dans l’installation de colocalisation et le cloud de Microsoft.
2.	**Connexions Ethernet point à point.** Vous pouvez connecter vos centres de données/bureaux locaux au cloud de Microsoft via des liaisons Ethernet point à point. Les fournisseurs Ethernet point à point peuvent offrir des connexions de couche 2 ou des connexions de couche 3 gérées entre votre site et le cloud de Microsoft.
3.	**Réseaux universels (IPVPN).** Vous pouvez intégrer votre réseau étendu au cloud de Microsoft. Les fournisseurs IPVPN (généralement des VPN MPLS) offrent une connectivité universelle entre vos succursales et vos centres de données. Le cloud de Microsoft peut être interconnecté à votre réseau étendu afin qu’il apparaisse comme n’importe quelle autre succursale. Les fournisseurs de réseaux étendus offrent généralement une connectivité de couche 3 gérée.

![](./media/expressroute-introduction/expressroute-connectivitymodels.png)

Les capacités et fonctionnalités ExpressRoute sont identiques pour tous les modèles de connectivité ci-dessus. Les fournisseurs de connectivité peuvent offrir un ou plusieurs modèles de connectivité figurant dans la liste ci-dessus. Vous pouvez contacter votre fournisseur de connectivité pour choisir le modèle qui vous convient le mieux.

## Fonctionnalités ExpressRoute

ExpressRoute prend en charge les fonctionnalités et capacités suivantes.

### Connectivité de couche 3

Microsoft utilise le protocole de routage dynamique standard (BGP) pour échanger des routages entre votre réseau local, vos instances dans Azure et les adresses publiques Microsoft. Nous établissons plusieurs sessions BGP avec votre réseau pour tester différents profils de trafic. Vous trouverez plus de détails à ce sujet dans l’article [Circuit ExpressRoute et domaines de routage](expressroute-circuit-peerings.md).

### Redondance

Chaque circuit ExpressRoute se compose de deux connexions à deux routeurs Microsoft Enterprise Edge (MSEE) entre le fournisseur de connectivité et le périmètre de votre réseau. Microsoft nécessite une double connexion BGP entre le fournisseur de connectivité et vous : une pour chaque MSEE. Vous pouvez choisir ne pas de déployer des appareils redondants/circuits Ethernet de votre côté. Cependant, les fournisseurs de connectivité utilisent des appareils redondants pour garantir que vos connexions sont transmises à Microsoft de manière redondante. Une configuration de connectivité de couche 3 redondante est nécessaire à la validité de notre [SLA](http://azure.microsoft.com/support/legal/sla/).

### Connectivité aux services de cloud Microsoft

Les connexions ExpressRoute permettent d’accéder aux services suivants.

- Services Microsoft Azure
- Services Microsoft Office 365
- Services Microsoft CRM Online 
 
Vous pouvez visiter la page [Forum Aux Questions ExpressRoute](expressroute-faqs.md) pour consulter une liste détaillée des services pris en charge via ExpressRoute.

### Connectivité à toutes les zones d’une région géopolitique

Vous pouvez vous connecter à Microsoft dans un de nos [emplacements d’homologation](expressroute-locations.md) et ainsi accéder à toutes les zones de la région géopolitique.

Par exemple, si vous êtes connecté à Microsoft à Amsterdam via ExpressRoute, vous aurez accès à tous les services de cloud Microsoft hébergés en Europe du Nord et en Europe occidentale. Reportez-vous à l’article [Partenaires ExpressRoute et emplacements d'homologation](expressroute-locations.md) pour obtenir une vue d'ensemble des régions géopolitiques, des zones de cloud Microsoft associées et des emplacements d’homologation ExpressRoute correspondants.

### Connectivité globale avec le module complémentaire ExpressRoute premium

Vous pouvez activer la fonctionnalité du module complémentaire ExpressRoute premium pour étendre la connectivité au-delà des limites géopolitiques. Par exemple, si vous êtes connecté à Microsoft à Amsterdam via ExpressRoute, vous aurez accès à tous les services de cloud Microsoft hébergés dans toutes les régions du monde (à l’exception des clouds nationaux). Vous pouvez accéder aux services déployés en Amérique du Sud ou en Australie de la même façon que vous accédez aux régions d’Europe du Nord et d’Europe occidentales.

### Riche écosystème de partenaires de connectivité

ExpressRoute offre un écosystème sans cesse croissant de fournisseurs de connectivité et de partenaires intégrateurs de systèmes. Vous pouvez consulter l’article [Fournisseurs et emplacements ExpressRoute](expressroute-locations.md) pour obtenir les dernières informations.

### Connectivité aux clouds nationaux

Microsoft gère des environnements de cloud isolés dans des régions géopolitiques et des segments de clientèle spécifiques. Reportez-vous à la page [Fournisseurs et emplacements ExpressRoute](expressroute-locations.md) pour obtenir la liste des clouds nationaux et des fournisseurs.

### Options relatives à la bande passante prise en charge

Vous pouvez acheter des circuits ExpressRoute pour un large éventail de bandes passantes. Les bandes passantes prises en charge sont répertoriées dans la liste ci-dessous. Veillez à vérifier auprès de votre fournisseur de connectivité la liste des bandes passantes prises en charge qu'il propose.

- 50 Mbits/s
- 100 Mbits/s
- 200 Mbits/s
- 500 Mbits/s
- 1 Gbit/s
- 2 Gbit/s
- 5 Gbit/s
- 10 Gbits/s

### Mise à l'échelle dynamique de la bande passante

Vous avez la possibilité d'augmenter la bande passante du circuit ExpressRoute (dans la mesure du possible) sans ralentir vos connexions.

### Modèles de facturation flexibles

Vous pouvez choisir le modèle de facturation qui vous convient le mieux. Choisissez parmi les modèles de facturation ci-dessous. Consultez la page [Forum Aux Questions ExpressRoute](expressroute-faqs.md) pour plus d’informations.

- **Données illimitées** Le circuit ExpressRoute est facturé sur une base mensuelle, et tous les transferts de données entrants et sortants sont inclus gratuitement. 
- **Données limitées**. Le circuit ExpressRoute est facturé sur une base mensuelle. Tous les transferts de données entrants sont gratuits. Chaque transfert de données sortant est facturé par Go de données transférées. Les taux de transfert de données varient selon la région.
- **Module complémentaire ExpressRoute premium**. Le module complémentaire ExpressRoute premium est un module qui s’ajoute au circuit ExpressRoute Le module complémentaire ExpressRoute premium offre les fonctionnalités suivantes : 
	- Augmentation de 4 000 à 10 000 routages des limites de routage pour les homologations publiques et privées Azure.
	- Connectivité globale des services. Un circuit ExpressRoute créé dans une région quelconque (à l'exclusion des clouds nationaux) a accès aux ressources de n'importe quelle autre région dans le monde. Par exemple, un réseau virtuel créé en l'Europe occidentale est accessible via un circuit ExpressRoute configuré dans la Silicon Valley.
	- Augmentation de 10 à une limite supérieure du nombre de liens de réseaux virtuels par circuit ExpressRoute, selon la bande passante du circuit.

## Étapes suivantes

- Découvrez en détail les connexions ExpressRoute et les domaines de routage. Consultez la page [Circuits ExpressRoute et domaines de routage](expressroute-circuit-peerings.md).
- Recherchez un fournisseur de services. Consultez la page [Partenaires ExpressRoute et emplacements d’homologation](expressroute-locations.md).
- Vérifiez que toutes les conditions préalables sont remplies. Consultez la page [Configuration requise pour ExpressRoute](expressroute-prerequisites.md).
- Reportez-vous aux conditions requises pour le [routage](expressroute-routing.md), la [traduction d’adresses réseau](expressroute-nat.md) et la [qualité de service](expressroute-qos.md).
- Configurez votre connexion ExpressRoute.
	- [Création d’un circuit ExpressRoute](expressroute-howto-circuit-classic.md)
	- [Configuration du routage](expressroute-howto-routing-classic.md)
	- [Liaison d’un réseau virtuel à un circuit ExpressRoute](expressroute-howto-linkvnet-classic.md)

<!---HONumber=AcomDC_0121_2016-->