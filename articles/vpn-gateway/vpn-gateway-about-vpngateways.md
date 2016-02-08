<properties 
   pageTitle="À propos des passerelles VPN pour la connectivité de réseau virtuel entre sites locaux | Microsoft Azure"
   description="En savoir plus sur les passerelles VPN, qui peuvent être utilisées pour les connexions entre sites locaux pour les configurations hybrides. Cet article traite des SKU de passerelle (De base, Standard et Hautes performances), des configurations de coexistence entre une passerelle VPN et ExpressRoute, des types de routage de passerelle (statique, dynamique, basé sur des stratégies, basé sur un itinéraire) et des conditions requises des passerelles pour la connectivité de réseau virtuel."
   services="vpn-gateway"
   documentationCenter="na"
   authors="cherylmc"
   manager="carolz"
   editor="tysonn" />
<tags 
   ms.service="vpn-gateway"
   ms.devlang="na"
   ms.topic="article"
   ms.tgt_pltfrm="na"
   ms.workload="infrastructure-services"
   ms.date="12/15/2015"
   ms.author="cherylmc" />

# À propos des passerelles VPN

Les passerelles VPN sont conçues pour faire circuler le trafic réseau entre les réseaux virtuels et les emplacements locaux. Elles sont également utilisées pour acheminer le trafic entre plusieurs réseaux virtuels dans Azure. À la création d'une passerelle, il y a quelques facteurs à prendre en compte.
 
Lors de la planification, tenez compte des éléments suivants :

- La référence (SKU) de la passerelle que vous souhaitez utiliser
- Le type de routage de passerelle pour votre connexion
- Le périphérique VPN, si nécessaire pour votre connexion

## SKU de passerelle

Il existe 3 SKU de passerelle VPN :

- De base
- Standard
- Hautes performances

Le tableau ci-dessous indique les types de passerelle et le débit total estimé. La tarification varie en fonction des différents SKU de passerelle. Pour plus d'informations sur la tarification, consultez la [tarification de passerelle VPN](https://azure.microsoft.com/pricing/details/vpn-gateway/).

| SKU | Passerelle VPN et ExpressRoute coexistants | Débit de passerelle ExpressRoute | Débit de passerelle VPN | Tunnels IPsec max de passerelle VPN |
|-------------|-----------------------------------|---------------------------------|------------------------|-------------------------------|
| De base | Non | 500 Mbits/s | 100 Mbits/s | 10 |
| Standard | Oui | 1 000 Mbits/s | 100 Mbits/s | 10 |
| Hautes performances | Oui | 2 000 Mbits/s | 200 Mbits/s | 30 |

**Remarque :** le débit de la passerelle VPN est une estimation calculée à partir de mesures entre réseaux virtuels dans la même région Azure. Il ne s’agit pas d’une garantie de débit pour des connexions entre différents locaux sur Internet, ce chiffre doit être considéré comme une mesure maximale potentielle.

## Types de routage de passerelle

Il existe deux types de routage de passerelle :

- **Basé sur des stratégies :** les passerelles basées sur des stratégies étaient précédemment appelées *passerelles statiques*. La fonctionnalité d'une passerelle statique n'a pas changé, même si le nom a changé. Ce type de passerelle prend en charge les VPN basés sur des stratégies. Les VPN basés sur des stratégies acheminent les paquets via les tunnels IPsec avec des sélecteurs de trafic basés sur des combinaisons de préfixes d'adresses entre votre réseau local et votre réseau virtuel Azure. Les sélecteurs de trafic ou les stratégies sont généralement définis en tant que liste d'accès de vos configurations VPN.
 
- **Basé sur un itinéraire :** les passerelles basées sur un itinéraire étaient précédemment appelées *passerelles dynamiques*. La fonctionnalité d'une passerelle dynamique n'a pas changé, même si le nom a changé. Les passerelles basées sur un itinéraire implémentent les VPN basés sur un itinéraire. Les VPN basés sur l'itinéraire utilisent des « itinéraires » dans l'adresse IP de transfert ou la table de routage pour acheminer les paquets dans leurs interfaces de tunnel VPN correspondantes. Les interfaces de tunnel chiffrent ou déchiffrent ensuite les paquets se trouvant dans et hors des tunnels. La stratégie ou le sélecteur de trafic pour les VPN basés sur l'itinéraire sont configurés comme universels (ou en caractères génériques).

Certaines connexions (par exemple, point à site et VNet à VNet) ne fonctionnent qu'avec un type de routage de passerelle spécifique. Les exigences de la passerelle sont décrites dans l'article qui correspond au scénario de connexion à créer.

Les périphériques VPN ont également les limites de configuration. Lorsque vous créez une passerelle VPN, vous devez sélectionner le type de routage de passerelle requis pour votre connexion, en vous assurant de vérifier que le périphérique VPN que vous voulez utiliser prend également en charge ce type de routage. Pour plus d'informations, consultez [À propos des périphériques VPN](vpn-gateway-about-vpn-devices.md).

Par exemple, si vous prévoyez d'utiliser une connexion de site à site avec une connexion de point à site, vous devez configurer une passerelle VPN basée sur un itinéraire. S'il est vrai que les connexions site à site fonctionnent avec les passerelles basées sur des stratégies, les connexions point à site nécessitent un type de passerelle basée sur un itinéraire. Étant donné que les deux connexions seront transmises sur la même passerelle, vous devrez sélectionner le type de passerelle qui prend en charge les deux. En outre, le périphérique VPN que vous utilisez doit également prendre en charge les configurations basées sur un itinéraire.


## Conditions requises de la passerelle

Le tableau ci-dessous répertorie la configuration requise pour les passerelles VPN statiques et dynamiques.


| **Propriété** | **Passerelle VPN basée sur des stratégies** | **Passerelle VPN basée sur un itinéraire** | **Passerelle VPN standard** | **Passerelle VPN à hautes performances** |
|-----------------------------------------|--------------------------------|-----------------------------------------------------------------------|-----------------------------------|----------------------------------|
| Connectivité de site à site (S2S) | Configuration de VPN basé sur une stratégie | Configuration de VPN basé sur l’itinéraire | Configuration de VPN basé sur l’itinéraire | Configuration de VPN basé sur l’itinéraire |
| Connectivité de point à site (P2S) | Non pris en charge | Prise en charge (peut coexister avec S2S) | Prise en charge (peut coexister avec S2S) | Prise en charge (peut coexister avec S2S) |
| Méthode d’authentification | Clé prépartagée | -Clé prépartagée pour la connectivité de site à site -Certificats pour la connectivité de point à site | -Clé prépartagée pour la connectivité de site à site -Certificats pour la connectivité de point à site | -Clé prépartagée pour la connectivité de site à site -Certificats pour la connectivité de point à site |
| Nombre maximal de connexions de site à site | 1 | 10 | 10 | 30 |
| Nombre maximal de connexions de point à site | Non pris en charge | 128 | 128 | 128 |
| Prise en charge de routage actif (BGP) | Non pris en charge | Non pris en charge | Non pris en charge | Non pris en charge |


## Étapes suivantes

Sélectionnez le périphérique VPN pour votre configuration. Consultez l’article [À propos des périphériques VPN](vpn-gateway-about-vpn-devices.md).





 

<!---HONumber=AcomDC_0128_2016-->