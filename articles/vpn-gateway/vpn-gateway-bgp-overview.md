<properties
   pageTitle="Vue d’ensemble du protocole BGP avec les passerelles VPN Azure | Microsoft Azure"
   description="Cet article fournit une vue d’ensemble du protocole BGP avec les passerelles VPN Azure."
   services="vpn-gateway"
   documentationCenter="na"
   authors="yushwang"
   manager="rossort"
   editor=""
   tags=""/>

<tags
   ms.service="vpn-gateway"
   ms.devlang="na"
   ms.topic="get-started-article"
   ms.tgt_pltfrm="na"
   ms.workload="infrastructure-services"
   ms.date="04/26/2016"
   ms.author="yushwang"/>

# Vue d’ensemble du protocole BGP avec les passerelles VPN Azure

Cet article fournit une vue d’ensemble de la prise en charge du protocole BGP (Border Gateway Protocol) avec les passerelles VPN Azure.

## À propos du protocole BGP

BGP est le protocole de routage standard couramment utilisé sur Internet pour l’échange d’informations d’accessibilité et de routage entre plusieurs réseaux. Dans le contexte des réseaux virtuels Azure, le protocole BGP permet aux passerelles VPN Azure et à vos périphériques VPN locaux (appelés voisins ou homologues BGP) d’échanger des « itinéraires » qui informent les deux passerelles de la disponibilité et de l’accessibilité de ces préfixes pour transiter par les passerelles ou routeurs impliqués. Le protocole BGP permet également le routage de transit entre plusieurs réseaux en propageant les itinéraires qu’une passerelle BGP obtient d’un homologue BGP vers tous les autres homologues BGP.
 
### Pourquoi utiliser le protocole BGP ?

Le protocole BGP est une fonctionnalité facultative que vous pouvez utiliser avec les passerelles VPN Azure basées sur des itinéraires. Vous devez vous assurer que vos périphériques VPN locaux prennent en charge le protocole BGP avant d’activer cette fonctionnalité. Vous pouvez continuer à utiliser des passerelles VPN Azure et vos périphériques VPN locaux sans le protocole BGP. Cela revient à utiliser des itinéraires statiques (sans le protocole BGP) *au lieu* d’utiliser un routage dynamique avec le protocole BGP entre vos réseaux et Azure.

Le protocole BGP offre plusieurs avantages et de nouvelles fonctionnalités :

#### Prise en charge de mises à jour de préfixe automatiques et flexibles

Avec le protocole BGP, il vous suffit de déclarer un préfixe minimal pour un homologue BGP spécifique sur le tunnel VPN S2S IPsec. Il peut être aussi réduit que le préfixe d’un hôte (/32) de l’adresse IP de l’homologue BGP de votre périphérique VPN local. Vous pouvez décider des préfixes de réseaux locaux à publier sur Azure, qui pourront accéder à votre réseau virtuel Azure.
	
Vous pouvez également publier un préfixe plus étendu, qui peut inclure certains préfixes d’adresses de votre réseau virtuel, par exemple l’itinéraire par défaut (0.0.0.0/0) ou un large espace d’adressage IP privé (par exemple, 10.0.0.0/8). Notez cependant que les préfixes ne peuvent en aucun cas être identiques à ceux de votre réseau virtuel. Les itinéraires identiques aux préfixes de votre réseau virtuel seront rejetés.

#### Prise en charge de plusieurs tunnels entre un réseau virtuel et un site local avec basculement automatique basé sur le protocole BGP

Vous pouvez établir plusieurs connexions entre votre réseau virtuel Azure et vos périphériques VPN locaux au même emplacement. Cette fonctionnalité fournit plusieurs tunnels (chemins) entre les deux réseaux dans une configuration actif-actif. Si l’un des tunnels est déconnecté, les itinéraires correspondants seront retirés par le biais du protocole BGP et le trafic sera automatiquement déplacé vers les tunnels restants.
	
Le schéma suivant offre un exemple simple de cette configuration à haute disponibilité :
	
![Plusieurs chemins actifs](./media/vpn-gateway-bgp-overview/multiple-active-tunnels.png)

#### Prise en charge du routage de transit entre vos réseaux locaux et plusieurs réseaux virtuels Azure

Le protocole BGP permet à plusieurs passerelles d’obtenir des préfixes de différents réseaux et de les propager, que la connexion soit directe ou indirecte. Ceci peut permettre un routage de transit avec des passerelles VPN Azure entre vos sites locaux ou sur plusieurs réseaux virtuels Azure.
	
Le schéma suivant offre un exemple de topologie à tronçons multiples, avec plusieurs chemins permettant de faire transiter le trafic entre les deux réseaux locaux via les passerelles VPN Azure dans les réseaux Microsoft :

![Transit sur plusieurs tronçons](./media/vpn-gateway-bgp-overview/full-mesh-transit.png)

## Forum aux questions sur le protocole BGP

#### Le protocole BGP est-il pris en charge sur toutes les références de passerelle VPN Azure ?

Non. Le protocole BGP est pris en charge sur les passerelles VPN Azure **Standard** et **HighPerformance**. La référence **De base** n’est pas prise en charge.

#### Puis-je utiliser le protocole BGP avec les passerelles VPN Azure basées sur des stratégies ?

Non. Le protocole BGP est pris en charge uniquement sur les passerelles VPN basées sur des itinéraires.

#### Puis-je utiliser des NSA (numéros de système autonomes) privés ?

Oui, vous pouvez utiliser vos propres NSA publics ou privés pour vos réseaux locaux et les réseaux virtuels Azure.

#### Puis-je utiliser le même NSA pour les réseaux VPN locaux et les réseaux virtuels Azure ?

Non. Vous devez attribuer des NSA différents à vos réseaux locaux et vos réseaux virtuels Azure si vous les interconnectez avec le protocole BGP. Le NSA 65515 est attribué aux passerelles VPN Azure par défaut, et ce, que le protocole BGP soit activé ou non pour la connectivité entre sites locaux. Vous pouvez remplacer cette valeur par défaut en attribuant un NSA différent lors de la création de la passerelle VPN, ou modifier le NSA après avoir créé la passerelle. Vous devez affecter vos NSA locaux aux passerelles de réseau local Azure correspondantes.



#### Quels préfixes d’adresse les passerelles VPN Azure publieront-elles pour moi ?

La passerelle VPN Azure publiera les itinéraires suivants pour vos périphériques BGP locaux :

- préfixes d’adresse de votre réseau virtuel ;
- préfixes d’adresse de chaque passerelle de réseau local connectée à la passerelle VPN Azure ;
- itinéraires obtenus à partir d’autres sessions d’homologation BGP connectées à la passerelle VPN Azure, **à l’exception de l’itinéraire par défaut ou des itinéraires se chevauchant avec un préfixe de réseau virtuel**.

#### Puis-je utiliser le protocole BGP avec des connexions entre réseaux virtuels ?

Oui. Vous pouvez utiliser le protocole BGP pour les connexions entre sites locaux et entre réseaux virtuels.

#### Puis-je combiner des connexions BGP et non-BGP pour mes passerelles VPN Azure ?

Oui. Vous pouvez combiner des connexions BGP et non-BGP pour la même passerelle VPN Azure.

#### La passerelle VPN Azure prend-elle en charge le routage de transit BGP ?

Oui. Le routage de transit BGP est pris en charge. Cependant, les passerelles VPN Azure ne publieront **PAS** les itinéraires par défaut pour les autres homologues BGP. Pour activer le routage de transit via plusieurs passerelles VPN Azure, vous devez activer le protocole BGP sur toutes les connexions intermédiaires entre réseaux virtuels.

#### Puis-je avoir plusieurs tunnels entre ma passerelle VPN Azure et mon réseau local ?

Oui. Vous pouvez établir plusieurs tunnels VPN S2S entre une passerelle VPN Azure et votre réseau local. Notez que tous ces tunnels seront comptabilisés par rapport au nombre total de tunnels pour vos passerelles VPN Azure. Par exemple, si vous avez deux tunnels redondants entre votre passerelle VPN Azure et l’un de vos réseaux locaux, 2 tunnels seront utilisés sur le quota total de votre passerelle VPN Azure (10 pour la référence Standard et 30 pour la référence HighPerformance).

#### Puis-je avoir plusieurs tunnels entre deux réseaux virtuels Azure avec protocole BGP ?

Non. Les tunnels redondants entre deux réseaux virtuels ne sont pas pris en charge.

#### Quelle adresse la passerelle VPN Azure utilise-t-elle pour l’IP d’homologue BGP ?

La passerelle VPN Azure alloue une adresse IP unique à partir de la plage GatewaySubnet définie pour le réseau virtuel. Par défaut, il s’agit de l’avant-dernière adresse de la plage. Par exemple, si votre GatewaySubnet est 10.12.255.0.0/27 (de 10.42.255.0.0 à 10.42.255.31), l’adresse IP d’homologue BGP sur la passerelle VPN Azure sera 10.12.255.30. Vous pouvez trouver ces informations lorsque vous affichez la liste des informations de passerelle VPN Azure.

#### Quelles sont les conditions requises concernant les adresses IP d’homologue BGP sur mon périphérique VPN ?

Votre adresse d’homologue BGP local **ne doit pas** être la même que l’adresse IP publique de votre périphérique VPN. Utilisez une adresse IP différente de l’IP d’homologue BGP pour votre périphérique VPN. Il peut s’agir d’une adresse affectée à l’interface de bouclage sur le périphérique. Spécifiez cette adresse sur la passerelle de réseau local correspondante, représentant l’emplacement.

#### Que dois-je spécifier comme préfixes d’adresse pour la passerelle de réseau local lorsque j’utilise le protocole BGP ?

La passerelle de réseau local Azure spécifie les préfixes d’adresse initiaux pour le réseau local. Avec le protocole BGP, vous devez allouer le préfixe de l’hôte (préfixe /32) de votre adresse IP d’homologue BGP en tant qu’espace d’adressage pour ce réseau local. Si votre adresse IP d’homologue BGP est 10.52.255.254, vous devez spécifier « 10.52.255.254/32 » comme espace localNetworkAddressSpace de la passerelle de réseau local, représentant ce réseau local. Ainsi, vous vous assurez que la passerelle VPN Azure établit la session BGP via le tunnel VPN S2S.

#### Que dois-je ajouter à mon périphérique VPN local pour la session d’homologation BGP ?

Vous devez ajouter un itinéraire hôte de l’adresse IP d’homologue BGP Azure sur votre périphérique VPN pointant vers le tunnel VPN S2S IPsec. Par exemple, si l’adresse IP d’homologue VPN Azure est « 10.12.255.30 », vous devez ajouter un itinéraire hôte pour « 10.12.255.30 » avec l’interface de tronçon suivant de l’interface de tunnel IPsec correspondante sur votre périphérique VPN.

## Étapes suivantes

Consultez la page [Getting started with BGP on Azure VPN gateways](./vpn-gateway-bgp-resource-manager-ps.md) (Prise en main du protocole BGP sur les passerelles VPN Azure) pour savoir comment configurer le protocole BGP pour vos connexions entre sites locaux et entre réseaux virtuels.

<!---HONumber=AcomDC_0427_2016-->