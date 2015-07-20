<properties 
   pageTitle="À propos des passerelles VPN de réseau virtuel | Microsoft Azure"
   description="En savoir plus sur les SKU De base, Standard et Hautes Performances, la coexistence d’une passerelle VPN et d’ExpressRoute, les types de routage de passerelle statique et dynamique et les conditions requises des passerelles pour la connectivité de réseau virtuel."
   services="vpn-gateway"
   documentationCenter="na"
   authors="cherylmc"
   manager="adinah"
   editor="tysonn" />
<tags 
   ms.service="vpn-gateway"
   ms.devlang="na"
   ms.topic="article"
   ms.tgt_pltfrm="na"
   ms.workload="infrastructure-services"
   ms.date="06/10/2015"
   ms.author="cherylmc" />

# À propos des passerelles VPN

Les passerelles VPN sont conçues pour faire circuler le trafic réseau entre les réseaux virtuels et les emplacements sur site ou entre plusieurs réseaux virtuels (réseau virtuel vers réseau virtuel). À la création d’une passerelle, il y a plusieurs facteurs à prendre en compte. Vous devez savoir quel SKU de passerelle vous souhaitez utiliser, le type de routage nécessaire pour votre configuration (dynamique ou statique) et le périphérique VPN que vous prévoyez d'utiliser si un périphérique VPN est nécessaire pour votre configuration.

## SKU de passerelle
Il existe 3 SKU de passerelle VPN : De base, Standard et Hautes performances. Le tableau ci-dessous indique les types de passerelle et le débit total estimé. La tarification varie en fonction des différents SKU de passerelle. Pour plus d'informations sur la tarification, consultez la [tarification de passerelle VPN](http://azure.microsoft.com/pricing/details/vpn-gateway/).

| SKU | Passerelle VPN et ExpressRoute coexistants | Débit de passerelle ExpressRoute | Débit de passerelle VPN | Tunnels IPsec max de passerelle VPN |
|-------------|-----------------------------------|---------------------------------|------------------------|-------------------------------|
| De base | Non | 500 Mbits/s | 100 Mbits/s | 10 |
| Standard | Oui | 1 000 Mbits/s | 100 Mbits/s | 10 |
| Hautes performances | Oui | 2 000 Mbits/s | 200 Mbits/s | 30 |

**Remarque :** le débit de la passerelle VPN est une estimation calculée à partir de mesures entre réseaux virtuels dans la même région Azure. Il ne s’agit pas d’une garantie de débit pour des connexions entre différents locaux sur Internet, ce chiffre doit être considéré comme une mesure maximale potentielle.

## Types de passerelle

Il existe deux types de passerelle, celles à *routage statique* (également appelée VPN basée sur une stratégie) et celles à *routage dynamique* (également appelée VPN basée sur l’itinéraire). Certaines configurations fonctionnent uniquement avec un type de routage spécifique, tandis que certains périphériques VPN fonctionnent uniquement avec un certain type de routage. Lorsque vous créez une passerelle VPN, vous devrez sélectionner le type de passerelle requis pour votre configuration, en vous assurant que le périphérique VPN sélectionné prend également en charge ce type de routage.

Par exemple, si vous prévoyez d'utiliser une configuration de site à site avec une configuration de point à site, vous devez configurer une passerelle VPN à routage dynamique. S'il est vrai que les configurations de site à site fonctionnent avec des passerelles à routage statique, les configurations de point à site nécessitent une passerelle avec routage dynamique. Étant donné que les deux connexions seront transmises sur la même passerelle, vous devrez sélectionner le type de passerelle qui prend en charge les deux configurations.

En outre, vous voudrez vérifier que votre périphérique VPN prend en charge le type de passerelle et les paramètres IPsec/IKE et la configuration dont vous avez besoin. Par exemple, si vous souhaitez créer une passerelle dynamique et que votre périphérique VPN ne prend pas en charge les VPN basés sur l'itinéraire, vous devrez réexaminer votre situation. Vous pouvez décider d'acquérir un autre périphérique VPN qui prend en charge les passerelles dynamiques ou créer une connexion de passerelle VPN qui prend en charge une passerelle à routage statique. Si vous acquérez ultérieurement un périphérique VPN qui prend en charge les passerelles à routage dynamique, vous pourrez toujours recréer la passerelle comme dynamique pour utiliser le périphérique. Dans ce cas, vous n’aurez qu’à recréer la passerelle, pas le réseau virtuel.

Il existe deux types de passerelle :

- **Le routage statique :** les passerelles à routage statique prennent en charge les **VPN basés sur une stratégie**. Les VPN basés sur une stratégie acheminent les paquets via les tunnels IPsec avec des sélecteurs de trafic basés sur des combinaisons de préfixes d'adresses entre votre réseau local et votre réseau virtuel Azure. Les sélecteurs de trafic ou les stratégies sont généralement définis en tant que liste d'accès de vos configurations VPN.

	>[AZURE.NOTE]Toutes les configurations ne sont pas compatibles avec les passerelles VPN à routage statique. Par exemple, les configurations sur plusieurs sites, les configurations de réseau virtuel à réseau virtuel et les connexions de point à site nécessitent des passerelles à routage dynamique. Vous pouvez consulter les conditions requises pour la passerelle dans les articles consacrés à chaque configuration.

- **Le routage dynamique :** les passerelles à routage dynamique mettent en place des **VPN basés sur l'itinéraire**. Les VPN basés sur l'itinéraire utilisent des « itinéraires » dans l'adresse IP de transfert ou la table de routage pour acheminer les paquets dans leurs interfaces de tunnel VPN correspondantes. Les interfaces de tunnel chiffrent ou déchiffrent ensuite les paquets se trouvant dans et hors des tunnels. La stratégie ou le sélecteur de trafic pour les VPN basés sur l'itinéraire sont configurés comme universels (ou en caractères génériques).

## Conditions requises de la passerelle

Le tableau ci-dessous répertorie la configuration requise pour les passerelles VPN statiques et dynamiques.


| **Propriété** | **Passerelle VPN à routage statique** | **Passerelle VPN à routage dynamique** | **Passerelle VPN standard** | **Passerelle VPN à hautes performances** |
|-----------------------------------------|--------------------------------|-----------------------------------------------------------------------|-----------------------------------|----------------------------------|
| Connectivité de site à site (S2S) | Configuration de VPN basé sur une stratégie | Configuration de VPN basé sur l’itinéraire | Configuration de VPN basé sur l’itinéraire | Configuration de VPN basé sur l’itinéraire |
| Connectivité de point à site (P2S) | Non pris en charge | Prise en charge (peut coexister avec S2S) | Prise en charge (peut coexister avec S2S) | Prise en charge (peut coexister avec S2S) |
| Méthode d’authentification | Clé prépartagée | -Clé prépartagée pour la connectivité de site à site -Certificats pour la connectivité de point à site | -Clé prépartagée pour la connectivité de site à site -Certificats pour la connectivité de point à site | -Clé prépartagée pour la connectivité de site à site -Certificats pour la connectivité de point à site |
| Nombre maximal de connexions de site à site | 1 | 10 | 10 | 30 |
| Nombre maximal de connexions de point à site | Non pris en charge | 128 | 128 | 128 |
| Prise en charge de routage actif (BGP) | Non pris en charge | Non pris en charge | Non pris en charge | Non pris en charge |


## Étapes suivantes

Sélectionnez le périphérique VPN pour votre configuration. Consultez l’article [À propos des périphériques VPN](http://go.microsoft.com/fwlink/p/?LinkID=615934).

Configurez votre réseau virtuel. Pour les connexions entre différents locaux, consultez les articles suivants :

- [Configuration d'une connexion de site à site intersite sur un réseau virtuel Azure](vpn-gateway-site-to-site-create.md)
- [Configuration d’une connexion VPN de point à site à un réseau virtuel Azure](vpn-gateway-point-to-site-create.md)
- [Configurer un VPN de site à site à l’aide du service de routage et d’accès à distance (RRAS) de Windows Server 2012](https://msdn.microsoft.com/library/dn636917.aspx)

Si vous souhaitez configurer une passerelle VPN, consultez l’article [Configurer une passerelle VPN](http://go.microsoft.com/fwlink/p/?LinkId=615106).

Si vous souhaitez modifier le type de passerelle VPN, consultez l’article [Modification du type d’une passerelle VPN de réseau virtuel](http://go.microsoft.com/fwlink/p/?LinkId=615109).

Si vous souhaitez connecter plusieurs sites sur un réseau virtuel, consultez l’article [Connexion de plusieurs sites locaux à un réseau virtuel](http://go.microsoft.com/fwlink/p/?LinkID=615106).

 

<!---HONumber=July15_HO2-->