<properties 
   pageTitle="À propos des passerelles VPN pour la connectivité de réseau virtuel entre sites locaux | Microsoft Azure"
   description="En savoir plus sur les passerelles VPN, qui peuvent être utilisées pour les connexions entre sites locaux pour les configurations hybrides. Cet article traite des SKU de passerelle (De base, Standard et Haute performance), des configurations de coexistence entre une passerelle VPN et ExpressRoute, des types de routage de passerelle (statique, dynamique, basé sur des stratégies, basé sur un itinéraire) et des conditions requises des passerelles pour la connectivité de réseau virtuel, pour les modèles de déploiement classique et Gestionnaire de ressources."
   services="vpn-gateway"
   documentationCenter="na"
   authors="cherylmc"
   manager="carmonm"
   editor="tysonn" />
<tags 
   ms.service="vpn-gateway"
   ms.devlang="na"
   ms.topic="article"
   ms.tgt_pltfrm="na"
   ms.workload="infrastructure-services"
   ms.date="02/19/2016"
   ms.author="cherylmc" />

# À propos des passerelles VPN

Les passerelles VPN sont conçues pour faire circuler le trafic réseau entre les réseaux virtuels et les emplacements locaux. Elles sont également utilisées pour acheminer le trafic entre plusieurs réseaux virtuels dans Azure (connexion de réseau virtuel à réseau virtuel). À la création d'une passerelle, il y a quelques facteurs à prendre en compte.

- La référence (SKU) de la passerelle que vous souhaitez utiliser
- Le type de VPN pour votre connexion
- Le périphérique VPN, si nécessaire pour votre connexion

**À propos des modèles de déploiement**

[AZURE.INCLUDE [vpn-gateway-classic-rm](../../includes/vpn-gateway-classic-rm-include.md)]
 

## SKU de passerelle

[AZURE.INCLUDE [vpn-gateway-table-sku](../../includes/vpn-gateway-table-sku-include.md)]

## Types de passerelles VPN

Il existe deux types de VPN :

- **Basé sur des stratégies :** les passerelles basées sur des stratégies sont appelées *passerelles statiques* dans le modèle de déploiement classique. La fonctionnalité d'une passerelle statique n'a pas changé, même si le nom a changé. Ce type de passerelle prend en charge les VPN basés sur des stratégies. Les VPN basés sur des stratégies acheminent les paquets via les tunnels IPsec avec des sélecteurs de trafic basés sur des combinaisons de préfixes d'adresses entre votre réseau local et votre réseau virtuel Azure. Les sélecteurs de trafic ou les stratégies sont généralement définis en tant que liste d'accès de vos configurations VPN.
 
- **Basé sur un itinéraire :** les passerelles basées sur un itinéraire sont appelées *passerelles dynamiques* dans le modèle de déploiement classique. La fonctionnalité d'une passerelle dynamique n'a pas changé, même si le nom a changé. Les passerelles basées sur un itinéraire implémentent les VPN basés sur un itinéraire. Les VPN basés sur l'itinéraire utilisent des « itinéraires » dans l'adresse IP de transfert ou la table de routage pour acheminer les paquets dans leurs interfaces de tunnel VPN correspondantes. Les interfaces de tunnel chiffrent ou déchiffrent ensuite les paquets se trouvant dans et hors des tunnels. La stratégie ou le sélecteur de trafic pour les VPN basés sur l'itinéraire sont configurés comme universels (ou en caractères génériques).

Certaines connexions (par exemple, point à site et réseau virtuel à réseau virtuel) ne fonctionnent qu'avec un type de VPN spécifique. Les exigences de la passerelle sont décrites dans l'article qui correspond au scénario de connexion à créer.

Les périphériques VPN ont également les limites de configuration. Lorsque vous créez une passerelle VPN, vous devez sélectionner le type de VPN requis pour votre connexion, en vous assurant de vérifier que le périphérique VPN que vous voulez utiliser prend également en charge ce type de routage. Pour plus d'informations, consultez [À propos des périphériques VPN](vpn-gateway-about-vpn-devices.md).

Par exemple, si vous prévoyez d'utiliser une connexion de site à site avec une connexion de point à site, vous devez configurer une passerelle VPN basée sur un itinéraire. S'il est vrai que les connexions site à site fonctionnent avec les passerelles basées sur des stratégies, les connexions point à site nécessitent un type de passerelle basée sur un itinéraire. Étant donné que les deux connexions seront transmises sur la même passerelle, vous devrez sélectionner le type de passerelle qui prend en charge les deux. En outre, le périphérique VPN que vous utilisez doit également prendre en charge les configurations basées sur un itinéraire.


## Conditions requises de la passerelle


[AZURE.INCLUDE [vpn-gateway-table-requirements](../../includes/vpn-gateway-table-requirements-include.md)]

## Étapes suivantes

Sélectionnez le périphérique VPN pour votre configuration. Consultez l’article [À propos des périphériques VPN](vpn-gateway-about-vpn-devices.md).





 

<!---HONumber=AcomDC_0224_2016-->