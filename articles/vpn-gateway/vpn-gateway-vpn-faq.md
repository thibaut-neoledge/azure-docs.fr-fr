<properties 
   pageTitle="FAQ sur la passerelle VPN de Virtual Network | Microsoft Azure"
	description="Le FAQ sur la passerelle VPN FAQ sur les connexions entre différents locaux de Microsoft Azure Virtual Network, les connexions de configuration hybride et les passerelles VPN"
	services="vpn-gateway"
	documentationCenter="na"
	authors="cherylmc"
	manager="carolz"
	editor=""/>
<tags 
   ms.service="vpn-gateway"
	ms.devlang="na"
	ms.topic="article"
	ms.tgt_pltfrm="na"
	ms.workload="infrastructure-services"
	ms.date="08/20/2015"
	ms.author="cherylmc"/>

# FAQ sur la passerelle VPN

## Connexion à des réseaux virtuels

### Puis-je me connecter à des réseaux virtuels dans différentes régions Azure ?

Oui. En fait, il n'existe aucune contrainte de région. Un réseau virtuel peut se connecter à un autre réseau virtuel dans la même région ou dans une autre région Azure.

### Puis-je me connecter à des réseaux virtuels avec différents abonnements ?

Oui.
### Puis-je me connecter à plusieurs sites à partir d'un seul réseau virtuel ?

Vous pouvez vous connecter à plusieurs sites à l'aide de Windows PowerShell et des API REST Azure. Consultez la rubrique FAQ sur la[Connectivité multisite et de réseau virtuel à réseau virtuel](#multi-site-and-vnet-to-vnet-connectivity).
## Quelles sont mes options de connexion entre différents locaux ?

Les connexions intersites suivantes sont prises en charge :

- [Site à Site](vpn-gateway-site-to-site-create.md) : connexion VPN sur IPsec (IKE v1 et IKE v2). Ce type de connexion requiert un périphérique VPN ou le service RRAS.

- [Point à site](vpn-gateway-point-to-site-create.md) : connexion VPN sur SSTP (Secure Socket Tunneling Protocol). Cette connexion ne nécessite pas un périphérique VPN.

- [Réseau virtuel à réseau virtuel](virtual-networks-configure-vnet-to-vnet-connection.md) : ce type de connexion est identique à une configuration de site à site. La connexion de réseau virtuel à réseau virtuel est une connexion VPN sur IPsec (IKE v1 et IKE v2). Cette connexion ne nécessite pas un périphérique VPN.

- [Multisite](vpn-gateway-multi-site.md) : il s'agit d'une variante d'une configuration de site à site qui vous permet de connecter plusieurs sites locaux à un réseau virtuel.

- [ExpressRoute](../expressroute/expressroute-introduction.md) : ExpressRoute est une connexion directe à Azure à partir de votre WAN, pas sur l'Internet public. Pour plus d'informations, consultez[ Vue d’ensemble technique d’ExpressRoute](../expressroute/expressroute-introduction.md) et le [FAQ ExpressRoute](../expressroute/expressroute-faqs.md).

### Quelle est la différence entre une connexion de site à site et de point à site ?

Les connexions de **site à site** vous permettent de vous connecter à partir de n'importe quel ordinateur se trouvant sur votre serveur local vers une machine virtuelle ou une instance de rôle au sein de votre réseau virtuel, selon le mode de configuration du routage que vous choisissez. Il s’agit d’une excellente solution pour une connexion entre différents locaux toujours disponibles et elle convient parfaitement aux configurations hybrides. Ce type de connexion s'appuie sur une appliance VPN IPsec (matériel ou application logicielle), qui doit être déployée à la périphérie de votre réseau. Pour créer ce type de connexion, vous devrez disposer du matériel VPN nécessaire et d’une adresse IPv4 externe.

Les connexions de **point à site** vous permettent de vous connecter à partir d'un seul ordinateur depuis n’importe où vers n’importe quel emplacement sur votre réseau virtuel. Ces connexions utilisent le client VPN fourni avec Windows. Dans le cadre de la configuration de point à site, vous installez un certificat et un package de configuration client VPN qui contient les paramètres permettant à votre ordinateur de se connecter à une machine virtuelle ou à une instance de rôle au sein du réseau virtuel. Cela est très intéressant lorsque vous souhaitez vous connecter à un réseau virtuel mais que vous ne vous trouvez pas en local. Il s’agit également d’une solution intéressante lorsque vous n'avez pas accès au matériel VPN ou à une adresse IPv4 externe, tous deux étant nécessaires pour une connexion de site à site.

Remarque : vous pouvez configurer votre réseau virtuel pour utiliser la connexion de site à site et de point à site simultanément, à condition de créer votre connexion de site à site à l'aide d'une passerelle de routage dynamique.

Pour plus d'informations, consultez [À propos de la connectivité intersite sécurisée pour les réseaux virtuels](vpn-gateway-cross-premises-options.md).

### Présentation d’ExpressRoute

ExpressRoute vous permet de créer des connexions privées entre les centres de données Microsoft et une infrastructure locale ou un environnement de colocalisation. Grâce à ExpressRoute, vous pouvez établir des connexions aux services cloud Microsoft tels que Microsoft Azure et Office 365 à un emplacement de colocalisation partenaire ExpressRoute ou vous connecter directement à Azure depuis votre réseau étendu existant (comme un VPN MPLS offert par un fournisseur de services réseau).

Les connexions ExpressRoute offrent une meilleure sécurité, une plus grande fiabilité, une bande passante plus élevée et des latences moindres par rapport aux connexions classiques sur Internet. Dans certains cas, l’utilisation de connexions ExpressRoute pour le transfert de données entre des réseaux locaux et Azure peut également avoir des avantages significatifs en matière de coûts. Si vous avez déjà créé une connexion entre les locaux de votre réseau local vers Azure, vous pouvez migrer vers une connexion ExpressRoute tout en conservant votre réseau virtuel tel qu’il est.

Pour plus d’informations, consultez le [Forum Aux Questions sur ExpressRoute](../expressroute/expressroute-faqs.md).

## Connexions de site à site et périphériques VPN

### Que dois-je prendre en compte lors de la sélection d'un périphérique VPN ?

Nous avons validé un ensemble de périphériques VPN de site à site standard en partenariat avec des fournisseurs de périphériques. Vous trouverez une liste de périphériques VPN compatibles, leurs instructions de configuration correspondantes ainsi que les caractéristiques du périphérique [ici](vpn-gateway-about-vpn-devices.md). Tous les périphériques des familles de périphériques répertoriées et reconnus comme compatibles doivent fonctionner avec Virtual Network. Pour aider à configurer votre périphérique VPN, reportez-vous à l'exemple de configuration de périphérique ou au lien qui correspond à la famille de périphériques appropriée.

### Que faire si je dispose d'un périphérique VPN qui ne figure pas dans la liste de périphériques compatibles connus ?

Si votre périphérique n’est pas répertorié comme un périphérique VPN compatible connu et que vous souhaitez l'utiliser pour votre connexion VPN, vous devez vérifier qu'il offre les paramètres et les options de configuration IPsec/IKE pris en charge [ici](vpn-gateway-about-vpn-devices.md#devices-not-on-the-compatible-list). Les périphériques qui répondent à la configuration requise minimale sont censés fonctionner avec les passerelles VPN. Contactez le fabricant de votre périphérique pour obtenir une prise en charge et des instructions de configuration supplémentaires.

### Puis-je utiliser le logiciel des VPN logiciels pour me connecter à Azure ?

Nous prenons en charge les serveurs de routage et accès distant (RRAS) Windows Server 2012 pour la configuration de site à site entre différents locaux.

D’autres solutions VPN logicielles fonctionnent avec notre passerelle tant qu'elles sont conformes aux implémentations IPsec standard. Contactez le fournisseur du logiciel pour obtenir des instructions de configuration et de prise en charge.

## Connectivité de point à site

### Quels systèmes d'exploitation puis-je utiliser avec une connexion de point à site ?

Les systèmes d’exploitation pris en charge sont les suivants :

- Windows 7 (version 64 bits uniquement)

- Windows Server 2008 R2

- Windows 8 (version 64 bits uniquement)

- Windows Server 2012

### Puis-je utiliser un client VPN logiciel pour une connexion de point à site qui prend en charge SSTP ?

Non. La prise en charge est limitée aux versions de système d'exploitation Windows répertoriées ci-dessus.

### Combien de points de terminaison clients VPN puis-je avoir dans ma configuration de point à site ?

Nous prenons en charge jusqu'à 128 clients VPN pouvant se connecter à un réseau virtuel en même temps.

### Puis-je utiliser ma propre AC racine PKI interne pour une connectivité de point à site ?

À ce stade, seuls les certificats racines autosignés sont pris en charge.

### Puis-je parcourir les serveurs proxy et les pare-feu à l'aide de la fonctionnalité de point à site ?

Oui. Nous utilisons SSTP (Secure Socket Tunneling Protocol) pour avoir un tunnel traversant les pare-feu. Ce tunnel apparaît comme une connexion HTTPS.

### Si je redémarre un ordinateur client avec une configuration de point à site, le VPN va-t-il se reconnecter automatiquement ?

Par défaut, l'ordinateur client ne rétablit pas automatiquement la connexion VPN.

### La configuration de point à site prend-elle en charge la reconnexion automatique et DDNS sur les clients VPN ?

La reconnexion automatique et DDNS ne sont actuellement pas pris en charge dans les configurations VPN de point à site.

### Puis-je avoir des configurations coexistantes de site à site et de point à site pour le même réseau virtuel ?

Oui. Ces deux solutions fonctionnent si vous avez une passerelle VPN à routage dynamique pour votre réseau virtuel. Nous ne prenons pas en charge la configuration de point à site des passerelles VPN à routage statique.

### Puis-je configurer un client de point à site pour me connecter à plusieurs réseaux virtuels en même temps ?

Oui, vous pouvez. Mais les réseaux virtuels ne peuvent pas avoir des préfixes IP qui se chevauchent et les espaces d'adressage de point à site ne doivent pas se chevaucher entre les réseaux virtuels.

### Quel débit puis-je attendre des connexions de site à site ou de point à site ?

Il est difficile de maintenir le débit exact des tunnels VPN. IPsec et SSTP sont des protocoles VPN de chiffrement lourd. Le débit est également limité par la latence et la bande passante entre vos locaux et Internet.

## Passerelles

### Qu'est une passerelle de routage statique ?

Les passerelles de routage statiques implémentent des VPN basés sur une stratégie. Les VPN basés sur une stratégie chiffrent et acheminent les paquets via les tunnels IPsec basés sur des combinaisons de préfixes d'adresses entre votre réseau local et votre réseau virtuel Azure. La stratégie (ou le sélecteur de trafic) est généralement définie en tant que liste d'accès dans les configurations VPN.

### Qu’est-ce qu’une passerelle de routage dynamique ?

Les passerelles de routage dynamique mettent en place des VPN basés sur l'itinéraire. Les VPN basés sur l'itinéraire utilisent des « itinéraires » dans l'adresse IP de transfert ou la table de routage pour acheminer des paquets dans leurs interfaces de tunnel correspondantes. Les interfaces de tunnel chiffrent ou déchiffrent ensuite les paquets se trouvant dans et hors des tunnels. La stratégie ou le sélecteur de trafic pour les VPN basés sur l'itinéraire sont configurés comme universels (ou en caractères génériques).

### Puis-je obtenir mon adresse IP de passerelle VPN avant de la créer ?

Non. Vous devez d’abord créer votre passerelle pour obtenir l'adresse IP. L'adresse IP change si vous supprimez et recréez votre passerelle VPN.

### Comment mon tunnel VPN est-il authentifié ?

Azure VPN utilise l'authentification PSK (clé prépartagée). Nous générons une clé prépartagée (PSK) lorsque nous créons le tunnel VPN. Vous pouvez modifier la PSK générée automatiquement pour votre VPN avec la cmdlet PowerShell ou l’API REST de clé prépartagée définie.

### Puis-je utiliser l'API de clé prépartagée définie pour configurer ma passerelle VPN de routage statique ?

Oui, la cmdlet PowerShell et les API de clé prépartagée définie peuvent servir à configurer un VPN de routage statique Azure et un VPN de routage dynamique.

### Puis-je utiliser les autres options d'authentification ?

Nous sommes limités à l'utilisation de clés prépartagées (PSK) pour l'authentification.

### Qu’est-ce que le « sous-réseau de passerelle » et pourquoi est-il nécessaire ?

Nous avons un service de passerelle que nous exécutons pour activer la connectivité entre différents locaux. Nous avons besoin de deux adresses IP de votre domaine de routage pour nous permettre d’activer le routage entre vos serveurs locaux et le cloud. Nous vous demandons de spécifier au moins un réseau /29 à partir duquel nous pouvons choisir des adresses IP pour la configuration d'itinéraires. Même si vous pouvez créer un sous-réseau /29, notez que certaines fonctionnalités exigent une taille de passerelle spécifique. Veillez à respecter la configuration requise de sous-réseau de passerelle pour la fonctionnalité que vous voulez configurer.

Veuillez noter que vous ne devez pas déployer des machines virtuelles ou des instances de rôle dans le sous-réseau de passerelle.

### Comment puis-je indiquer quel trafic traverse la passerelle VPN ?

Si vous utilisez le portail Azure, ajoutez chaque plage que vous souhaitez envoyer via la passerelle pour votre réseau virtuel dans la page Réseaux sous Réseaux locaux.

### Puis-je configurer un tunneling forcé ?

Oui. Consultez [Configurer un tunneling forcé](vpn-gateway-about-forced-tunneling.md).

### Puis-je configurer mon propre serveur VPN dans Azure et l'utiliser pour me connecter à mon réseau local ?

Oui, vous pouvez déployer vos propres serveurs ou passerelles VPN dans Azure depuis Azure Marketplace ou en créant vos propres routeurs VPN. Vous devez configurer des itinéraires définis par l'utilisateur dans votre réseau virtuel pour vous assurer que le trafic est acheminé correctement entre vos réseaux locaux et les sous-réseaux de votre réseau virtuel.

### Plus d'informations sur les types de passerelle, la configuration requise et le débit

Pour plus d'informations, consultez [À propos des passerelles VPN](vpn-gateway-about-vpngateways.md).

## Connectivité multisite et de réseau virtuel à réseau virtuel

### Quel type de passerelles peut prendre en charge une connectivité multisite et de réseau virtuel à réseau virtuel ?

Uniquement les VPN à routage dynamique.

### Puis-je connecter un réseau virtuel avec un VPN de routage dynamique à un autre réseau virtuel avec VPN de routage statique ?

Non, les deux réseaux virtuels DOIVENT utiliser des VPN de routage dynamique.

### Le trafic de réseau virtuel à réseau virtuel est-il sécurisé ?

Oui, il est protégé par le chiffrement IPsec/IKE.

### Le trafic de réseau virtuel à réseau virtuel circule-t-il sur la dorsale principale d'Azure ?

Oui.

### À combien de sites locaux et de réseaux virtuels peut se connecter un réseau virtuel ?

Bande passante 10 combinés pour les passerelles de routage dynamique de base et standard ; 30 pour les passerelles VPN de performances élevées.

### Puis-je utiliser les VPN de point à site avec mon réseau virtuel comportant plusieurs tunnels VPN ?

Oui, les VPN de point à site peuvent être utilisés avec les passerelles VPN se connectant à plusieurs sites locaux et à d'autres réseaux virtuels.

### Puis-je configurer plusieurs tunnels entre mon réseau virtuel et mon site local à l'aide d’un VPN multisite ?

Non, les tunnels redondants entre un réseau virtuel Azure et un site local ne sont pas pris en charge.

### Des espaces d'adressage peuvent-ils se chevaucher entre les réseaux virtuels connectés et les sites locaux ?

Non. Le chevauchement des espaces d'adressage entraîne le téléchargement du fichier netcfg ou l'échec de la création d'un réseau virtuel.

### Puis-je obtenir plus de bande passante avec plus de VPN de site à site à obtenir que pour un seul réseau virtuel ?

Non, tous les tunnels VPN, y compris les VPN de point à site, partagent la même passerelle VPN Azure et la bande passante disponible.

### Puis-je utiliser une passerelle VPN Azure pour faire transiter le trafic entre mes sites locaux ou vers un autre réseau virtuel ?

Il est possible de faire transiter le trafic via la passerelle VPN Azure, mais cela dépend des espaces d'adressage définis statiquement dans le fichier de configuration netcfg. BGP n'est pas encore pris en charge avec les réseaux virtuels Azure et les passerelles VPN. Sans BGP, la définition manuelle des espaces d'adressage de transit dans le fichier netcfg peut entraîner des erreurs et n'est pas recommandée.

### Azure génère-t-il la même clé prépartagée IPsec/IKE pour toutes les connexions VPN d'un même réseau virtuel ?

Non, Azure génère par défaut différentes clés prépartagées pour différentes connexions VPN. Toutefois, vous pouvez utiliser l’API REST de clé de passerelle VPN définie ou la cmdlet PowerShell pour définir la valeur clé de votre choix. La clé DOIT être une chaîne alphanumérique d'une longueur comprise entre 1 et 128 caractères.

### Le trafic entre les réseaux virtuels est-il facturé par Azure ?

Pour le trafic entre différents réseaux virtuels Azure, seul le trafic passant d'une région Azure à une autre est facturé par Azure. Le tarif facturé est répertorié dans la page de [Tarification de la passerelle VPN](https://azure.microsoft.com/pricing/details/vpn-gateway/) Azure.


### Puis-je me connecter à un réseau virtuel avec les VPN IPsec sur mon circuit ExpressRoute ?

Oui, cette méthode est prise en charge. Pour plus d'informations, consultez [Configurer des connexions ExpressRoute et VPN de site à site coexistantes](../expressroute/expressroute-coexist.md).

## Connectivité et machines virtuelles

### Si ma machine virtuelle est dans un réseau virtuel et que j'ai une connexion entre différents locaux, comment dois-je me connecter à la machine virtuelle ?

Vous disposez de plusieurs options. Si vous avez activé le protocole RDP et que vous avez créé un point de terminaison, vous pouvez vous connecter à votre machine virtuelle à l'aide de l'adresse IP virtuelle. Dans ce cas, vous allez spécifier l'adresse IP virtuelle et le port auquel vous souhaitez vous connecter. Vous devez configurer le port sur votre machine virtuelle pour le trafic. En règle générale, vous accédez au Portail de gestion et vous enregistrez les paramètres de la connexion RDP sur votre ordinateur. Les paramètres contiennent les informations de connexion nécessaires.

Si vous avez un réseau virtuel avec une connectivité entre différents locaux configurée, vous pouvez vous connecter à votre machine virtuelle à l'aide de l'adresse DIP interne ou de l’adresse IP privée. Vous pouvez également vous connecter à votre machine virtuelle en DIP interne à partir d'une autre machine virtuelle qui se trouve sur le même réseau virtuel. Vous ne pouvez pas vous connecter en RDP à votre machine virtuelle à l'aide d’un DIP si vous vous connectez à partir d'un emplacement en dehors de votre réseau virtuel. Par exemple, si vous avez un réseau virtuel de point à site configuré et que vous n’établissez pas de connexion à partir de votre ordinateur, vous ne pouvez pas vous connecter à la machine virtuelle en DIP.

### Si ma machine virtuelle est dans un réseau virtuel avec connectivité entre différents locaux, tout le trafic de ma machine virtuelle passe-t-il par cette connexion ?

Non. Seul le trafic qui possède une IP de destination contenue dans les plages d'adresse IP du réseau local du réseau virtuel que vous avez spécifié passera par la passerelle de réseau virtuel. Le trafic avec un IP de destination qui se trouve dans le réseau virtuel restera dans le réseau virtuel. Tout autre trafic est envoyé via l'équilibrage de charge aux réseaux publics, ou si le tunneling forcé est utilisé, il est envoyé via la passerelle VPN Azure. Si vous résolvez un problème, il est important de s'assurer que vous disposez de toutes les plages répertoriées dans votre réseau local et que vous souhaitez envoyer via la passerelle. Vérifiez que les plages d'adresses du réseau local ne se chevauchent pas avec les plages d'adresses du réseau virtuel. Par ailleurs, vérifiez que le serveur DNS que vous utilisez résout le nom à l'adresse IP appropriée.

## Étapes suivantes

Afficher plus de FAQ relatifs à la mise en réseau pour plus de détails :

- [FAQ sur le réseau virtuel](../virtual-network/virtual-networks-faq.md)

- [FAQ sur ExpressRoute](../expressroute/expressroute-faqs.md)

 

<!---HONumber=August15_HO9-->