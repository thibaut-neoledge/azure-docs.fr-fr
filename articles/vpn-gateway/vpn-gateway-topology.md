<properties 
   pageTitle="Topologie de connexion de passerelle VPN | Microsoft Azure"
   description="Affichez les topologies de connexion de passerelle VPN et les outils de configuration, ainsi que les modèles de déploiement disponibles."
   services="vpn-gateway"
   documentationCenter="na"
   authors="cherylmc"
   manager="carmonm"
   editor=""
   tags="azure-resource-manager,azure-service-management"/>
<tags 
   ms.service="vpn-gateway"
   ms.devlang="na"
   ms.topic="get-started-article"
   ms.tgt_pltfrm="na"
   ms.workload="infrastructure-services"
   ms.date="07/19/2016"
   ms.author="cherylmc" />

# Azure VPN Gateway connections (Connexions à la passerelle VPN Azure)

Cet article explique les topologies de connexion à la passerelle VPN de base. Vous pouvez vous appuyer sur les graphiques et les descriptions pour sélectionner la topologie de configuration répondant à vos besoins. Bien que cet article aborde le traitement des principales topologies de base, il est possible de créer des topologies plus complexes à l’aide des diagrammes.

Chaque topologie contient un tableau présentant le modèle de déploiement correspondant à cette dernière, ainsi que les outils de déploiement pour configurer chaque topologie. Ce tableau contient des liens vers des articles, le cas échéant. Nous mettons à jour les tableaux lorsque de nouveaux articles et outils de déploiement sont disponibles.

Après avoir décidé quelle connexion vous souhaitez créer, les instructions que vous utilisez pour créer votre passerelle VPN varient selon le modèle de déploiement que vous avez utilisé pour créer votre réseau virtuel. Par exemple, si vous avez créé votre réseau virtuel à l’aide du modèle de déploiement classique, vous utiliserez les recommandations et les instructions pour le modèle de déploiement classique afin de créer et configurer votre passerelle VPN. Il est impossible de créer une passerelle VPN Resource Manager pour un réseau virtuel à base de modèle de déploiement classique. Pour plus d’informations sur les modèles de déploiement, voir [Présentation du déploiement Resource Manager et du déploiement classique](../resource-manager-deployment-model.md).

## Connexions site à site et multi-sites

Une connexion site à site est une connexion via un tunnel VPN IPsec/IKE (IKEv1 ou IKEv2). Ce type de connexion requiert un périphérique VPN ou un serveur RRAS Windows local. Les connexions site à site peuvent être utilisées pour les configurations hybrides et entre les différents locaux.


**Diagramme S2S**

![Connexion S2S](./media/vpn-gateway-topology/site2site.png "site à site")

Vous pouvez créer et configurer plusieurs connexions VPN de site à site à vos réseaux locaux si vous utilisez des VPN Azure basés sur un itinéraire. Ce type de configuration est souvent appelé connexion « multi-sites ».
 

**Diagramme multi-sites**

![Connexion multi-sites](./media/vpn-gateway-topology/multisite.png "multi-sites")


**Méthodes et modèles de déploiement disponibles**

[AZURE.INCLUDE [vpn-gateway-table-site-to-site](../../includes/vpn-gateway-table-site-to-site-include.md)]

## Connexion entre deux réseaux virtuels

La connexion entre deux réseaux virtuels est semblable à la connexion d’un réseau virtuel à un emplacement de site local. Les deux types de connectivité font appel à une passerelle VPN Azure pour offrir un tunnel sécurisé utilisant Ipsec/IKE. Les réseaux virtuels que vous connectez peuvent être situés dans différents abonnements et différentes régions. Vous pouvez même combiner une communication de réseau virtuel à réseau virtuel avec des configurations multisites. Vous établissez ainsi des topologies réseau qui combinent une connectivité entre différents locaux et une connectivité entre différents réseaux virtuels.

Azure propose actuellement deux modes de déploiement : Gestion des services Azure (appelé classique) et Azure Resource Manager. Si vous utilisez Azure depuis un certain temps, vous exécutez certainement vos machines virtuelles et rôles d’instance sur un réseau virtuel classique, tandis que vos machines virtuelles et instances de rôle plus récentes s’exécutent probablement sur un réseau virtuel créé dans Azure Resource Manager. Vous pouvez créer une connexion entre les réseaux virtuels, même si ceux-ci se trouvent dans différents modèles de déploiement.


**Diagramme réseau virtuel à réseau virtuel**

![Connexion de réseau virtuel à réseau virtuel](./media/vpn-gateway-topology/vnet2vnet.png "Connexion entre deux réseaux virtuels")


**Méthodes et modèles de déploiement disponibles**

[AZURE.INCLUDE [vpn-gateway-table-vnet-to-vnet](../../includes/vpn-gateway-table-vnet-to-vnet-include.md)]


## De point à site

Une configuration de point à site vous permet de créer individuellement une connexion sécurisée à votre réseau virtuel à partir d’un ordinateur client. Le démarrage de la connexion à partir de l’ordinateur client permet d’établir une connexion VPN. Ceci est une solution utile lorsque vous souhaitez vous connecter à votre réseau virtuel à partir d’un site distant, comme depuis votre domicile ou une conférence ou lorsque seulement quelques clients doivent se connecter à un réseau virtuel.

Une connexion de point à site est une connexion VPN sur SSTP (Secure Socket Tunneling Protocol). Les connexions de ce type ne nécessitent pas de périphérique VPN ou d’adresse IP publique pour fonctionner.

**Diagramme P2S**

![Connexion de point à site](./media/vpn-gateway-topology/point2site.png "point à site")

**Méthodes et modèles de déploiement disponibles**

[AZURE.INCLUDE [vpn-gateway-table-point-to-site](../../includes/vpn-gateway-table-point-to-site-include.md)]


##ExpressRoute

[AZURE.INCLUDE [expressroute-intro](../../includes/expressroute-intro-include.md)]

Pour plus d’informations sur ExpressRoute, consultez [Présentation technique d’ExpressRoute](../expressroute/expressroute-introduction.md).



## Coexistence de connexions ExpressRoute et de site à site

ExpressRoute est une connexion directe et dédiée aux services Microsoft, notamment à Azure, à partir de votre WAN, qui ne passe pas par l’Internet public. Le trafic VPN de site à site transite via l’Internet public tout en étant chiffré. La possibilité de configurer des connexions VPN de site à site et ExpressRoute pour le même réseau virtuel présente plusieurs avantages. Vous pouvez configurer un VPN de site à site comme un chemin d’accès de basculement sécurisé pour ExpressRoute, ou utiliser des VPN de site à site pour vous connecter à des sites qui ne font pas partie de votre réseau, mais qui sont connectés via ExpressRoute.


**Diagramme des connexions coexistantes**

![Connexion de coexistence](./media/vpn-gateway-topology/expressroutes2s.png "Expressroute-site à site")


**Méthodes et modèles de déploiement disponibles**

[AZURE.INCLUDE [vpn-gateway-table-coexist](../../includes/vpn-gateway-table-coexist-include.md)]




## Étapes suivantes

Familiarisez-vous avec les éléments des articles [À propos des passerelles VPN](vpn-gateway-about-vpngateways.md) et [FAQ sur la passerelle VPN](vpn-gateway-vpn-faq.md) afin de mieux comprendre les paramètres des passerelles VPN.





 

<!---HONumber=AcomDC_0727_2016-->