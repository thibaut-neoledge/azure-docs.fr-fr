---
title: "Connecter votre réseau local à un réseau virtuel Azure : VPN site à site : portail | Microsoft Docs"
description: "Étapes de création d’une connexion IPsec entre votre réseau local et un réseau virtuel Azure via l’Internet public. Ces étapes vous aideront à créer une connexion de passerelle VPN de site à site à l’aide du portail."
services: vpn-gateway
documentationcenter: na
author: cherylmc
manager: timlt
editor: 
tags: azure-resource-manager
ms.assetid: 827a4db7-7fa5-4eaf-b7e1-e1518c51c815
ms.service: vpn-gateway
ms.devlang: na
ms.topic: hero-article
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 01/23/2017
ms.author: cherylmc
translationtype: Human Translation
ms.sourcegitcommit: afe143848fae473d08dd33a3df4ab4ed92b731fa
ms.openlocfilehash: 37311ea17004d8c5cfe9bfc9360c3972e39fb7f5
ms.lasthandoff: 03/17/2017


---
# <a name="create-a-vnet-with-a-site-to-site-connection-using-the-azure-portal"></a>Création d’un réseau virtuel avec une connexion de site à site à l’aide du portail Azure
> [!div class="op_single_selector"]
> * [Resource Manager - Portail Azure](vpn-gateway-howto-site-to-site-resource-manager-portal.md)
> * [Resource Manager - PowerShell](vpn-gateway-create-site-to-site-rm-powershell.md)
> * [Classic - Portail Azure](vpn-gateway-howto-site-to-site-classic-portal.md)
> * [Classic - Portail Classic](vpn-gateway-site-to-site-create.md)
>
>

Cet article vous guide lors de la création d’un réseau virtuel et d’une connexion de passerelle VPN de site à site à votre réseau local, à l’aide du modèle de déploiement Azure Resource Manager et du portail Azure. Les connexions site à site peuvent être utilisées pour les configurations hybrides et entre les différents locaux.

![Schéma de connexion intersite d’une passerelle VPN site à site](./media/vpn-gateway-howto-site-to-site-resource-manager-portal/site-to-site-diagram.png)

### <a name="deployment-models-and-methods-for-site-to-site-connections"></a>Méthodes et modèles de déploiement pour les connexions de site à site
[!INCLUDE [deployment models](../../includes/vpn-gateway-deployment-models-include.md)]

Le tableau suivant présente les modèles et les méthodes de déploiement disponibles pour les configurations de site à site. Quand un article avec les étapes de configuration est disponible, le lien vers cet article est ajouté à ce tableau.

[!INCLUDE [site-to-site table](../../includes/vpn-gateway-table-site-to-site-include.md)]

#### <a name="additional-configurations"></a>Configurations supplémentaires
Si vous souhaitez établir une connexion entre des réseaux virtuels, mais si vous ne créez pas une connexion à un emplacement local, consultez [configurer une connexion de réseau virtuel à réseau virtuel](vpn-gateway-vnet-vnet-rm-ps.md). Si vous souhaitez ajouter une connexion de site à site à un réseau virtuel qui possède déjà une connexion, consultez [Connecter plusieurs sites locaux à un réseau virtuel](vpn-gateway-howto-multi-site-to-site-resource-manager-portal.md).

## <a name="before-you-begin"></a>Avant de commencer
Vérifiez que vous disposez des éléments ci-dessous avant de commencer votre configuration.

* Un périphérique VPN compatible et une personne qui est en mesure de le configurer. Consultez [À propos des périphériques VPN](vpn-gateway-about-vpn-devices.md). Si vous ne maîtrisez pas la configuration de votre appareil VPN ou les plages d’adresses IP mentionnées dans la configuration de votre réseau local, vous devez contacter une personne qui peut vous fournir ces informations.
* Une adresse IP publique exposée en externe pour votre appareil VPN. Cette adresse IP ne peut pas se trouver derrière un NAT.
* Un abonnement Azure. Si vous ne disposez pas déjà d’un abonnement Azure, vous pouvez activer vos [avantages abonnés MSDN](http://azure.microsoft.com/pricing/member-offers/msdn-benefits-details) ou créer un [compte gratuit](http://azure.microsoft.com/pricing/free-trial).

### <a name="values"></a>Exemples de valeurs de configuration pour cet exercice
Lorsque vous suivez ces étapes dans le cadre d’un exercice, vous pouvez utiliser les exemples de valeurs de configuration suivantes :

* **Nom du réseau virtuel :** TestVNet1
* **Espace d’adressage :** 10.11.0.0/16 et 10.12.0.0/16
* **Sous-réseaux :**
  * FrontEnd : 10.11.0.0/24
  * BackEnd : 10.12.0.0/24
  * GatewaySubnet : 10.12.255.0/27
* **Groupe de ressources :** TestRG1
* **Emplacement :** États-Unis de l’Est
* **Serveur DNS :** 8.8.8.8
* **Nom de passerelle :** VNet1GW
* **Adresse IP publique :** VNet1GWIP
* **Type de VPN :** Route-based
* **Type de connexion :** Site-to-site (IPsec)
* **Type de passerelle :** VPN
* **Nom de passerelle de réseau local :** Site2
* **Nom de connexion :** VNet1toSite2

## <a name="CreatVNet"></a>1. Créez un réseau virtuel
Si vous disposez déjà d’un réseau virtuel, vérifiez que les paramètres sont compatibles avec la conception de votre passerelle VPN, avec une attention particulière pour tous les sous-réseaux qui pourraient chevaucher d’autres réseaux. Si vos sous-réseaux se chevauchent, votre connexion ne fonctionnera pas correctement. Si votre réseau virtuel est correctement configuré, vous pouvez commencer à suivre les étapes de la section [Spécifier un serveur DNS](#dns).

### <a name="to-create-a-virtual-network"></a>Pour créer un réseau virtuel
[!INCLUDE [vpn-gateway-basic-vnet-rm-portal](../../includes/vpn-gateway-basic-vnet-rm-portal-include.md)]

## <a name="subnets"></a>2. Ajouter des sous-réseaux et des espaces d’adressage supplémentaires
Vous pouvez ajouter des sous-réseaux et des espaces d’adressage supplémentaires pour votre réseau virtuel une fois qu’il a été créé.

[!INCLUDE [vpn-gateway-additional-address-space](../../includes/vpn-gateway-additional-address-space-include.md)]

## <a name="dns"></a>3. Spécifier un serveur DNS
### <a name="to-specify-a-dns-server"></a>Pour spécifier un serveur DNS
[!INCLUDE [vpn-gateway-add-dns-rm-portal](../../includes/vpn-gateway-add-dns-rm-portal-include.md)]

## <a name="gatewaysubnet"></a>4. Créer un sous-réseau de passerelle
Avant de connecter votre réseau virtuel à une passerelle, vous devez créer le sous-réseau de passerelle pour le réseau virtuel auquel vous souhaitez vous connecter. Si possible, il est préférable de créer un sous-réseau de passerelle à l’aide d’un bloc CIDR de /28 ou /27 afin de fournir suffisamment d’adresses IP pour satisfaire les exigences de configuration future supplémentaires.

Si vous créez cette configuration dans le cadre d’un exercice, reportez-vous à ces [valeurs](#values) lorsque vous créez votre sous-réseau de passerelle.

### <a name="to-create-a-gateway-subnet"></a>Pour créer un sous-réseau de passerelle
[!INCLUDE [vpn-gateway-add-gwsubnet-rm-portal](../../includes/vpn-gateway-add-gwsubnet-rm-portal-include.md)]

## <a name="VNetGateway"></a>5. Créer une passerelle de réseau virtuel
Si vous créez cette configuration dans le cadre d’un exercice, vous pouvez vous reporter aux [exemples de valeurs de configuration](#values).

### <a name="to-create-a-virtual-network-gateway"></a>Pour créer une passerelle de réseau virtuel
[!INCLUDE [vpn-gateway-add-gw-rm-portal](../../includes/vpn-gateway-add-gw-rm-portal-include.md)]

## <a name="LocalNetworkGateway"></a>6. Créer une passerelle de réseau local
La « passerelle de réseau local » fait généralement référence à votre emplacement local. Donnez à la passerelle de réseau local un nom qui sera utilisé par Azure pour la référencer. 

Si vous créez cette configuration dans le cadre d’un exercice, vous pouvez vous reporter aux [exemples de valeurs de configuration](#values).

### <a name="to-create-a-local-network-gateway"></a>Pour créer une passerelle de réseau local
[!INCLUDE [vpn-gateway-add-lng-rm-portal](../../includes/vpn-gateway-add-lng-rm-portal-include.md)]

## <a name="VPNDevice"></a>7. Configuration de votre périphérique VPN
[!INCLUDE [vpn-gateway-configure-vpn-device-rm](../../includes/vpn-gateway-configure-vpn-device-rm-include.md)]

## <a name="CreateConnection"></a>8. Créer une connexion VPN de site à site.
Créez la connexion VPN de site à site entre votre passerelle de réseau virtuel et votre périphérique VPN. Assurez-vous de remplacer ces valeurs par les vôtres. La clé partagée doit correspondre à la valeur que vous avez utilisée pour la configuration de votre périphérique VPN. 

Avant de commencer cette section, vérifiez que la création de votre passerelle de réseau virtuel et des passerelles de réseau local est terminée. Si vous créez cette configuration dans le cadre d’un exercice, reportez-vous à ces [valeurs](#values) lorsque vous créez votre connexion.

### <a name="to-create-the-vpn-connection"></a>Pour créer la connexion VPN
[!INCLUDE [vpn-gateway-add-site-to-site-connection-rm-portal](../../includes/vpn-gateway-add-site-to-site-connection-rm-portal-include.md)]

## <a name="VerifyConnection"></a>9. Vérifier la connexion VPN
Vous pouvez vérifier votre connexion VPN dans le portail ou à l’aide de PowerShell.

[!INCLUDE [vpn-gateway-verify-connection-rm](../../includes/vpn-gateway-verify-connection-rm-include.md)]

## <a name="next-steps"></a>Étapes suivantes
*  Une fois la connexion achevée, vous pouvez ajouter des machines virtuelles à vos réseaux virtuels. Pour plus d’informations, consultez [Machines virtuelles](https://docs.microsoft.com/azure/#pivot=services&panel=Compute).
*  Pour plus d’informations sur le protocole BGP, consultez les articles [Vue d’ensemble du protocole BGP](vpn-gateway-bgp-overview.md) et [Comment configurer BGP](vpn-gateway-bgp-resource-manager-ps.md).


