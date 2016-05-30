<properties 
   pageTitle="Planification et conception de la passerelle VPN | Microsoft Azure"
   description="En savoir plus sur la planification et la conception de la passerelle VPN pour les connexions entre locaux, hybrides et entre réseaux virtuels"
   services="vpn-gateway"
   documentationCenter="na"
   authors="cherylmc"
   manager="carmonm"
   editor=""
   tags="azure-service-management,azure-resource-manager"/>
<tags 
   ms.service="vpn-gateway"
   ms.devlang="na"
   ms.topic="article"
   ms.tgt_pltfrm="na"
   ms.workload="infrastructure-services"
   ms.date="05/16/2016"
   ms.author="cherylmc"/>

# Planification et conception de la passerelle VPN

La planification et de la conception de vos configurations entre plusieurs locaux et entre deux réseaux virtuels peuvent être très simples ou très complexes, selon vos besoins de mise en réseau. Cet article vous indiquera les considérations importantes relatives à la conception et à la planification de base.

## Planification


### <a name="compare"></a>Options de connectivité intersite

Si vous avez décidé de connecter vos sites locaux à un réseau virtuel en toute sécurité, vous disposez de trois options : Site à site, Point à site et ExpressRoute. Comparer les différentes connexions entre locaux qui sont disponibles. L’option que vous choisirez peut dépendre de la réponse à différentes questions, notamment :


- Quel est le type de débit requis par votre solution ?
- Souhaitez-vous communiquer sur l’Internet public par le biais d’un VPN sécurisé ou sur une connexion privée ?
- Disposez-vous d’une adresse IP publique utilisable ?
- Envisagez-vous d’utiliser un périphérique VPN ? Si tel est le cas, est-il compatible ?
- Ne voulez-vous connecter qu’un petit nombre d’ordinateurs, ou souhaitez-vous mettre en place une connexion permanente pour votre site ?
- Quel est le type de passerelle VPN requis par la solution que vous souhaitez créer ?
- Quelle référence SKU de passerelle devez-vous utiliser ?


Le tableau ci-après peut vous aider à déterminer la meilleure option de connectivité pour votre solution.


[AZURE.INCLUDE [vpn-gateway-cross-premises](../../includes/vpn-gateway-cross-premises-include.md)]



### <a name="gwrequire"></a>Configuration requise de la passerelle par type de VPN et SKU


Lorsque vous créez une passerelle VPN, vous devez spécifier la passerelle de référence (SKU) que vous souhaitez utiliser. Il existe 3 SKU de passerelle VPN :

- De base
- Standard
- Hautes performances

[AZURE.INCLUDE [vpn-gateway-table-requirements](../../includes/vpn-gateway-table-requirements-include.md)]



### <a name="aggthroughput"></a>Types de passerelle et débit total estimé

Le tableau ci-dessous indique les types de passerelle et le débit total estimé. Le débit total estimé peut être un facteur décisif pour votre conception. La tarification varie en fonction des différents SKU de passerelle. Pour plus d'informations sur la tarification, consultez la [tarification de passerelle VPN](https://azure.microsoft.com/pricing/details/vpn-gateway/). Cette table s’applique aux modèles de déploiement classique et Resource Manager.

[AZURE.INCLUDE [vpn-gateway-table-gwtype-aggtput](../../includes/vpn-gateway-table-gwtype-aggtput-include.md)]



### <a name="wf"></a>Flux de travail

La liste suivante présente le flux de travail courant pour la connectivité au cloud :

1.	Concevez et planifiez la topologie de votre connectivité et répertoriez les espaces d’adressage pour tous les réseaux auxquels vous souhaitez vous connecter.
2.	Créez un réseau virtuel Azure. 
3.	Créez une passerelle VPN pour le réseau virtuel.
4.	Créez et configurez les connexions aux réseaux locaux ou à d’autres réseaux virtuels (en fonction des besoins).
5.	Créez et configurez une connexion Point-à-Site pour votre passerelle VPN Azure (en fonction des besoins).
 

## Conception

### <a name="topologies"></a>Topologies de connexion

Commencez par consulter les diagrammes dans l’article [Topologies de connexion](vpn-gateway-topology.md). Cet article contient des schémas de base, les modèles de déploiement de chaque topologie (Resource Manager ou classique) et les outils de déploiement que vous pouvez utiliser pour déployer votre configuration.

### <a name="designbasics"></a>Concepts de base

Les sections ci-dessous présentent les notions de base des passerelles VPN. De plus, vous devez prendre en compte les [limitations des services de mise en réseau](../articles/azure-subscription-service-limits.md#networking-limits).


#### <a name="subnets"></a>À propos des sous-réseaux

Lors de la planification et de la conception de la connexion la plus adaptée à votre environnement, il est très important de prendre en compte les plages d’adresses IP et les sous-réseaux à votre disposition.

Vous devez créer un sous-réseau de passerelle pour votre réseau virtuel afin de configurer une passerelle VPN. Tous les sous-réseaux de passerelle doivent être nommés GatewaySubnet afin de fonctionner correctement. Faites attention à ne pas donner un autre nom au sous-réseau de votre passerelle et ne déployez pas de machines virtuelles ou tout autre élément sur le sous-réseau de passerelle. Pour plus d’informations sur les sous-réseaux de passerelle, consultez la section [Sous-réseau de passerelle](vpn-gateway-about-vpngateways.md#gwsub) dans l’article À propos des passerelles VPN.

Très souvent, lorsque vous créez des connexions, vous devez faire attention aux chevauchements des plages d’adresses de sous-réseau entre vos connexions. On parle de chevauchement de sous-réseau lorsqu’un réseau virtuel ou un emplacement local contient le même espace d’adressage que l’autre emplacement. Cela signifie que vos ingénieurs réseau de vos réseaux locaux devront rechercher une plage que vous pourrez utiliser pour votre espace d’adressage IP/vos sous-réseaux Azure. Vous devrez disposer d’un espace d’adressage qui n’est pas utilisé sur le réseau local.

Il est également important d’éviter le chevauchement des sous-réseaux lorsque vous travaillez avec des connexions entre réseaux virtuels. La création d’une connexion entre réseaux virtuels échouera en cas de chevauchement de vos sous-réseaux et si une adresse IP existe à la fois dans le réseau virtuel expéditeur et de destination. Dans ce cas, Azure ne peut pas acheminer les données vers l’autre réseau virtuel, car l’adresse de destination fait partie du réseau virtuel expéditeur.



#### <a name="local"></a>À propos des passerelles de réseau local

La passerelle de réseau local fait généralement référence à votre emplacement local. Dans le modèle de déploiement classique, la passerelle de réseau local a été appelée Site local. Vous devez donner un nom à la passerelle de réseau local (l’adresse IP publique du périphérique VPN local) et spécifier les préfixes d’adresse qui se trouvent en local. Azure examinera les préfixes d’adresse de destination pour le trafic réseau, consultera la configuration que vous avez spécifiée pour votre passerelle de réseau local, puis routera les paquets en conséquence. Vous pouvez modifier ces préfixes d’adresse en fonction des besoins. Pour plus d’informations sur les passerelles de réseau local, consultez la section [Passerelles de réseau local](vpn-gateway-about-vpngateways.md#lng) dans l’article À propos des passerelles VPN.


#### <a name="gwtype"></a>À propos des types de passerelles

Il est essentiel de sélectionner le type de passerelle approprié pour votre topologie. Votre passerelle ne fonctionnera pas correctement si vous sélectionnez un type de passerelle inadapté. Le type de passerelle spécifie comment la passerelle se connecte ; il s’agit d’un paramètre de configuration requis pour le modèle de déploiement Resource Manager.

Les types de passerelles sont les suivants :

- Vpn
- ExpressRoute

#### <a name="connectiontype"></a>À propos des types de connexion

Chaque configuration nécessite un type de connexion spécifique. Les types de connexion sont les suivants :

- IPsec
- Vnet2Vnet
- ExpressRoute
- VPNClient


#### <a name="vpntype"></a>À propos des types de VPN

Chaque configuration nécessite un type de réseau privé virtuel spécifique afin de fonctionner. Si vous combinez deux configurations, telles que la création d’une connexion de site à site et une connexion de point-à-site au même réseau virtuel, vous devez utiliser un type de réseau privé virtuel qui satisfait les exigences des deux types de connexion. Pour les connexions de point-à-site et de site à site coexistantes, vous devez utiliser un type de VPN basé sur un itinéraire lorsque vous travaillez avec le modèle de déploiement Azure Resource Manager ; si vous travaillez avec le mode de déploiement classique, utilisez une passerelle dynamique.

[AZURE.INCLUDE [vpn-gateway-vpntype](../../includes/vpn-gateway-vpntype-include.md)]

Les tableaux suivants indiquent le type de VPN en fonction de chaque configuration de connexion. Assurez-vous que le type de VPN pour votre passerelle correspond à la configuration que vous souhaitez créer.


[AZURE.INCLUDE [vpn-gateway-table-vpntype](../../includes/vpn-gateway-table-vpntype-include.md)]

### <a name="devices"></a>Périphériques VPN et connexions de site à site

Pour configurer une connexion de site à site, quel que soit le modèle de déploiement, vous avez besoin des éléments suivants :

- un périphérique VPN compatible avec les passerelles VPN Azure
- une adresse IPv4 publique qui ne se trouve pas derrière un NAT

Vous devez avoir de l’expérience pour configurer votre périphérique VPN. Pour plus d’informations sur les périphériques VPN, consultez [À propos des périphériques VPN](vpn-gateway-about-vpn-devices.md). L’article sur les périphériques VPN contient des informations sur les périphériques validés, la configuration requise pour les périphériques qui n’ont pas été validés, ainsi que des liens vers les documents de configuration de périphérique pour chaque périphérique, le cas échéant.

### <a name="forcedtunnel"></a>Envisager le routage avec tunneling forcé

Dans la plupart des configurations, vous pouvez configurer le tunneling forcé. Le tunneling forcé vous permet de rediriger ou de « forcer » tout le trafic Internet vers votre emplacement local via un tunnel VPN site à site pour l’inspection et l’audit. Il s’agit d’une condition de sécurité critique pour la plupart des stratégies informatiques d’entreprise.

Sans le tunneling forcé, le trafic Internet depuis vos machines virtuelles dans Azure se fera toujours à partir de l’infrastructure du réseau Azure directement vers Internet, sans vous laisser inspecter ou vérifier le trafic. L’accès Internet non autorisés est susceptible d’entraîner la divulgation d’informations ou tout autre type de violation de sécurité.

Pour plus d’informations sur la configuration du tunneling forcé, consultez [Configuration du tunneling forcé pour les passerelles VPN à l’aide du modèle de déploiement Classic](vpn-gateway-about-forced-tunneling.md) et [Configuration du tunneling forcé pour les passerelles VPN à l’aide du modèle de déploiement Resource Manager](vpn-gateway-about-forced-tunneling.md).

**Diagramme du tunneling forcé**

![Connexion de tunneling forcé](./media/vpn-gateway-plan-design/forced-tunnel.png "tunneling forcé")

Une connexion de tunneling forcé peut être configurée dans les deux modèles de déploiement et à l’aide de différents outils. Pour plus d’informations, consultez le tableau ci-après. Nous mettons à jour ce tableau à mesure que de nouveaux articles, de nouveaux modèles de déploiement et des outils supplémentaires sont disponibles pour cette configuration. Quand un article est disponible, ce tableau contient un lien vers celui-ci.

[AZURE.INCLUDE [vpn-gateway-table-forcedtunnel](../../includes/vpn-gateway-table-forcedtunnel-include.md)]



## Étapes suivantes

Pour plus d’informations susceptibles de vous aider dans votre conception, consultez [FAQ sur la passerelle VPN](vpn-gateway-vpn-faq.md) et [À propos des passerelles VPN](vpn-gateway-about-vpngateways.md).

Pour plus d’informations sur les topologies de connexion, consultez [Topologies de connexion](vpn-gateway-topology.md).

<!---HONumber=AcomDC_0518_2016-->