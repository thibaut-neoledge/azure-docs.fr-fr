---
title: "Connecter votre réseau local à un réseau virtuel Azure : VPN site à site : interface de ligne de commande | Microsoft Docs"
description: "Étapes de création d’une connexion IPsec entre votre réseau local et un réseau virtuel Azure via l’Internet public. Ces étapes vous aideront à créer une connexion de passerelle VPN de site à site à l’aide de l’interface de ligne de commande (CLI)."
services: vpn-gateway
documentationcenter: na
author: cherylmc
manager: timlt
editor: 
tags: azure-resource-manager
ms.assetid: 
ms.service: vpn-gateway
ms.devlang: na
ms.topic: hero-article
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 04/21/2017
ms.author: cherylmc
translationtype: Human Translation
ms.sourcegitcommit: 1cc1ee946d8eb2214fd05701b495bbce6d471a49
ms.openlocfilehash: c3563f3a3fa46d40ba02fe97b3b0ebe3c45caddd
ms.lasthandoff: 04/26/2017


---
# <a name="create-a-virtual-network-with-a-site-to-site-vpn-connection-using-cli"></a>Créer un réseau virtuel avec une connexion VPN de site à site à l’aide de l’interface de ligne de commande

Cet article vous explique comment utiliser l’interface de ligne de commande Azure pour créer une connexion de passerelle VPN de site à site à partir de votre réseau local vers le réseau virtuel. Les étapes mentionnées dans cet article s’appliquent au modèle de déploiement Resource Manager. Vous pouvez également créer cette configuration à l’aide d’un autre outil ou modèle de déploiement en sélectionnant une option différente dans la liste suivante :

> [!div class="op_single_selector"]
> * [Resource Manager - Portail Azure](vpn-gateway-howto-site-to-site-resource-manager-portal.md)
> * [Resource Manager - PowerShell](vpn-gateway-create-site-to-site-rm-powershell.md)
> * [Resource Manager - CLI](vpn-gateway-howto-site-to-site-resource-manager-cli.md)
> * [Classic - Portail Azure](vpn-gateway-howto-site-to-site-classic-portal.md)
> * [Classic - Portail Classic](vpn-gateway-site-to-site-create.md)
> 
>


![Schéma de connexion intersite d’une passerelle VPN site à site](./media/vpn-gateway-howto-site-to-site-resource-manager-cli/site-to-site-connection-diagram.png)

Une connexion de passerelle VPN de site à site permet de connecter votre réseau local à un réseau virtuel Azure via un tunnel VPN IPsec/IKE (IKEv1 ou IKEv2). Ce type de connexion requiert un périphérique VPN local disposant d’une adresse IP publique exposée en externe. Pour plus d’informations sur les passerelles VPN, consultez l’article [À propos de la passerelle VPN](vpn-gateway-about-vpngateways.md).

## <a name="before-you-begin"></a>Avant de commencer

Vérifiez que vous disposez des éléments ci-dessous avant de commencer votre configuration :

* Assurez-vous de vouloir utiliser le modèle de déploiement Resource Manager. [!INCLUDE [deployment models](../../includes/vpn-gateway-deployment-models-include.md)] 
* Un périphérique VPN compatible et une personne qui est en mesure de le configurer. Pour plus d’informations sur les périphériques VPN compatibles et la configuration de votre périphérique, consultez l’article [À propos des périphériques VPN](vpn-gateway-about-vpn-devices.md).
* Une adresse IPv4 publique exposée en externe pour votre périphérique VPN. Cette adresse IP ne peut pas se trouver derrière un NAT.
* Si vous ne maîtrisez pas les plages d’adresses IP situées dans votre configuration de réseau local, vous devez contacter une personne en mesure de vous aider. Lorsque vous créez cette configuration, vous devez spécifier les préfixes des plages d’adresses IP qu’Azure acheminera vers votre emplacement local. Aucun des sous-réseaux de votre réseau local ne peut chevaucher les sous-réseaux du réseau virtuel auquel vous souhaitez vous connecter. 
* La dernière version des commandes CLI (version 2.0 ou ultérieure). Pour plus d’informations sur l’installation des commandes CLI consultez l’article [Installer l’interface de ligne de commande Azure 2.0](https://docs.microsoft.com/cli/azure/install-azure-cli).

### <a name="example-values"></a>Valeurs utilisées dans l’exemple

Vous pouvez utiliser ces valeurs pour créer un environnement de test ou vous y référer pour mieux comprendre les exemples de cet article :

```
#Example values

VnetName                = TestVNet1 
ResourceGroup           = TestRG1 
Location                = eastus 
AddressSpace            = 10.12.0.0/16 
SubnetName              = Subnet1 
Subnet                  = 10.12.0.0/24 
GatewaySubnet           = 10.12.255.0/27 
LocalNetworkGatewayName = Site2 
LNG Public IP           = <VPN device IP address>
LocalAddrPrefix1        = 10.0.0.0/24 
LocalAddrPrefix2        = 20.0.0.0/24   
GatewayName             = VNet1GW 
PublicIP                = VNet1GWIP 
VPNType                 = RouteBased 
GatewayType             = Vpn 
ConnectionName          = VNet1toSite2
```

## <a name="Login"></a>1. Connexion à Azure

Connectez-vous à votre abonnement Azure avec la commande [az login](/cli/azure/#login) et suivez les instructions à l’écran.

```azurecli
az login
```

Si vous disposez de plusieurs abonnements Azure, répertoriez les abonnements associés au compte.

```azurecli
Az account list --all
```

Spécifiez l’abonnement que vous souhaitez utiliser.

```azurecli
Az account set --subscription <replace_with_your_subscription_id>
```

## <a name="2-create-a-resource-group"></a>2. Créer un groupe de ressources

L’exemple suivant crée un groupe de ressources nommé « TestRG1 » à l’emplacement « eastus ». Si vous disposez déjà d’un groupe de ressources dans la région où vous souhaitez créer votre réseau virtuel, vous pouvez l’utiliser à la place de l’exemple donné.

```azurecli
az group create -n TestRG1 -l eastus
```

## <a name="VNet"></a>3. Créez un réseau virtuel

Si vous n’avez pas de réseau virtuel, créez-en un. Lorsque vous créez un réseau virtuel, vérifiez que les espaces d’adressage que vous spécifiez ne chevauchent pas les espaces d’adressage de votre réseau local. 

L’exemple suivant permet de créer un réseau virtuel nommé « TestVNet1 » et un sous-réseau nommé « Subnet1 ».

```azurecli
az network vnet create -n TestVNet1 -g TestRG1 --address-prefix 10.12.0.0/16 -l eastus --subnet-name Subnet1 --subnet-prefix 10.12.0.0/24
```

## 4. <a name="gwsub"></a>Créer le sous-réseau de passerelle

[!INCLUDE [vpn-gateway-no-nsg](../../includes/vpn-gateway-no-nsg-include.md)]

Pour cette configuration, vous avez également besoin d’un sous-réseau de passerelle. La passerelle de réseau virtuel utilise un sous-réseau de passerelle qui contient les adresses IP utilisées par les services de passerelle VPN. Le sous-réseau de passerelle doit être nommé « GatewaySubnet ». Si vous choisissez un autre nom, vous créez un sous-réseau, mais Azure ne le traitera pas comme un sous-réseau de passerelle.

La taille du sous-réseau de passerelle que vous spécifiez dépend de la configuration de la passerelle VPN que vous souhaitez créer. Bien qu’il soit possible de créer un sous-réseau de passerelle aussi petit que /29, nous vous recommandons de créer un sous-réseau plus vaste qui inclut un plus grand nombre d’adresses en sélectionnant /27 ou /28. En choisissant un sous-réseau de passerelle plus vaste, vous disposez de suffisamment d’adresses IP pour prendre en charge d’éventuelles configurations futures.


```azurecli
az network vnet subnet create --address-prefix 10.12.255.0/27 -n GatewaySubnet -g TestRG1 --vnet-name TestVNet1
```

## <a name="localnet"></a>5. Créer la passerelle de réseau local

La passerelle de réseau local fait généralement référence à votre emplacement local. Donnez au site un nom auquel Azure pourra se référer, puis spécifiez l’adresse IP du périphérique VPN local vers lequel vous allez créer une connexion. Spécifiez également les préfixes d’adresses IP qui seront acheminés via la passerelle VPN vers le périphérique VPN. Les préfixes d’adresses que vous spécifiez sont les préfixes situés sur votre réseau local. Vous pouvez facilement mettre à jour ces préfixes si votre réseau local change.

Utilisez les valeurs suivantes :

* La valeur *--gateway-ip-address* est l’adresse IP de votre périphérique VPN local. Votre périphérique VPN ne peut pas se trouver derrière un NAT.
* La valeur *--local-address-prefixes* représente vos espaces d’adressage local.

L’exemple suivant vous montre comment ajouter une passerelle de réseau local avec des préfixes d’adresse multiples :

```azurecli
az network local-gateway create --gateway-ip-address 23.99.221.164 -n Site2 -g TestRG1 --local-address-prefixes 10.0.0.0/24 20.0.0.0/24
```

## <a name="PublicIP"></a>6. Demander une adresse IP publique

Demandez une adresse IP publique qui sera allouée à votre passerelle VPN de réseau virtuel. Il s’agit de l’adresse IP à laquelle votre périphérique VPN devra se connecter.

Actuellement, la passerelle de réseau virtuel du modèle de déploiement Resource Manager ne prend en charge que les adresses IP publiques à l’aide de la méthode d’allocation dynamique. Néanmoins, cela ne signifie pas que l’adresse IP est modifiée. L’adresse IP de la passerelle VPN change uniquement lorsque la passerelle est supprimée, puis recréée. L’adresse IP publique de la passerelle de réseau virtuel n’est pas modifiée lors du redimensionnement, de la réinitialisation ou d’autres opérations de maintenance/mise à niveau internes de votre passerelle VPN. 

```azurecli
az network public-ip create -n VNet1GWIP -g TestRG1 --allocation-method Dynamic
```

## <a name="CreateGateway"></a>7. Créer la passerelle VPN

Créez la passerelle VPN de réseau virtuel. La création d’une passerelle VPN peut prendre 45 minutes ou plus.

Utilisez les valeurs suivantes :

* Le paramètre *--gateway-type* pour une configuration de site à site est *Vpn*. Le type de passerelle dépend toujours de la configuration que vous implémentez. Pour plus d’informations, consultez l’article [Types de passerelle](vpn-gateway-about-vpn-gateway-settings.md#gwtype).
* Le paramètre *--vpn-type* peut avoir pour valeur *RouteBased* (appelé passerelle dynamique dans certaines documentations) ou *PolicyBased* (appelé passerelle statique dans certaines documentations). Le paramètre est spécifique aux exigences du périphérique auquel vous vous connectez. Pour plus d’informations sur les types de passerelle VPN, consultez [À propos des paramètres de configuration de la passerelle VPN](vpn-gateway-about-vpn-gateway-settings.md#vpntype).
* La valeur *--sku* peut être Basic, Standard ou HighPerformance. Des limites de configuration s’appliquent à certaines références (SKU). Pour plus d’informations, consultez l’article [Références (SKU) de passerelle](vpn-gateway-about-vpngateways.md#gateway-skus).

Après avoir exécuté cette commande, vous ne verrez aucun commentaire ni résultat. La création d’une passerelle prend environ 45 minutes.

```azurecli
az network vnet-gateway create -n VNet1GW --public-ip-address VNet1GWIP -g TestRG1 --vnet TestVNet1 --gateway-type Vpn --vpn-type RouteBased --sku Standard --no-wait 
```

## <a name="VPNDevice"></a>8. Configuration de votre périphérique VPN

[!INCLUDE [vpn-gateway-configure-vpn-device-rm](../../includes/vpn-gateway-configure-vpn-device-rm-include.md)]
  Pour trouver l’adresse IP publique de votre passerelle de réseau virtuel, utilisez l’exemple suivant en remplaçant les valeurs par les vôtres. Pour faciliter la lecture, la sortie est mise en forme pour afficher la liste des adresses IP publiques sous forme de tableau.

  ```azurecli
  az network public-ip list -g TestRG1 -o table
  ```

## <a name="CreateConnection"></a>9. Créer la connexion VPN

Créez la connexion VPN de site à site entre votre passerelle de réseau virtuel et votre périphérique VPN local. Soyez particulièrement attentif à la valeur de clé partagée qui doit correspondre à la valeur de clé partagée configurée pour votre périphérique VPN.

```azurecli
az network vpn-connection create -n VNet1toSite2 -g TestRG1 --vnet-gateway1 VNet1GW -l eastus --shared-key abc123 --local-gateway2 Site2
```

Après un bref délai, la connexion sera établie.

## <a name="toverify"></a>10. Vérifier la connexion VPN

[!INCLUDE [verify connection](../../includes/vpn-gateway-verify-connection-cli-rm-include.md)] 

Si vous souhaitez utiliser une autre méthode pour vérifier votre connexion, consultez l’article [Vérifier une connexion de passerelle VPN](vpn-gateway-verify-connection-resource-manager.md).

## <a name="common-tasks"></a>Tâches courantes

### <a name="to-view-local-network-gateways"></a>Pour afficher les passerelles de réseau local

```azurecli
az network local-gateway list --resource-group TestRG1
```

### <a name="modify"></a>Pour modifier des préfixes d’adresses IP d’une passerelle de réseau local
Si vous devez modifier les préfixes de votre passerelle de réseau local, suivez les instructions suivantes. Chaque fois que vous apportez une modification, vous devez renseigner l’intégralité des préfixes, et pas uniquement les préfixes que vous souhaitez modifier.

- **Si une connexion a déjà été spécifiée**, utilisez l’exemple ci-dessous. Indiquez la liste complète des préfixes incluant les préfixes existants et ceux que vous souhaitez ajouter. Dans cet exemple, 10.0.0.0/24 et 20.0.0.0/24 sont déjà présents. Nous ajoutons les préfixes 30.0.0.0/24 et 40.0.0.0/24.

  ```azurecli
  az network local-gateway update --local-address-prefixes 10.0.0.0/24 20.0.0.0/24 30.0.0.0/24 40.0.0.0/24 -n VNet1toSite2 -g TestRG1
  ```

- **Si aucune connexion n’a été spécifiée**, utilisez la même commande que vous utilisez pour créer une passerelle de réseau local. Vous pouvez également utiliser cette commande pour mettre à jour l’adresse IP de passerelle du périphérique VPN. Utilisez uniquement cette commande si vous ne disposez pas déjà d’une connexion. Dans cet exemple, 10.0.0.0/24, 20.0.0.0/24, 30.0.0.0/24 et 40.0.0.0/24 sont déjà présents. Nous spécifions uniquement les préfixes que nous souhaitons conserver. Dans ce cas, 10.0.0.0/24 et 20.0.0.0/24.

  ```azurecli
  az network local-gateway create --gateway-ip-address 23.99.221.164 -n Site2 -g TestRG1 --local-address-prefixes 10.0.0.0/24 20.0.0.0/24
  ```

### <a name="modifygwipaddress"></a>Pour modifier l’adresse IP d’une passerelle de réseau local

L’adresse IP de cette configuration est l’adresse IP du périphérique VPN vers lequel vous créez une connexion. Si l’adresse IP du périphérique VPN change, vous pouvez modifier la valeur. L’adresse IP peut être modifiée même s’il existe une connexion de passerelle.

```azurecli
az network local-gateway update --gateway-ip-address 23.99.222.170 -n Site2 -g TestRG1
```

Lorsque vous affichez le résultat, vérifiez que les préfixes d’adresses IP sont inclus.

  ```azurecli
  "localNetworkAddressSpace": { 
    "addressPrefixes": [ 
      "10.0.0.0/24", 
      "20.0.0.0/24", 
      "30.0.0.0/24" 
    ] 
  }, 
  "location": "eastus", 
  "name": "Site2", 
  "provisioningState": "Succeeded",  
  ```

### <a name="to-view-the-virtual-network-gateway-public-ip-address"></a>Pour afficher l’adresse IP publique de la passerelle de réseau virtuel

Pour trouver l’adresse IP publique de votre passerelle de réseau virtuel, utilisez l’exemple suivant. Pour faciliter la lecture, la sortie est mise en forme pour afficher la liste des adresses IP publiques sous forme de tableau.

```azurecli
az network public-ip list -g TestRG1 -o table
```

### <a name="to-verify-the-shared-key-values"></a>Pour vérifier les valeurs de clé partagée

Vérifiez que la valeur de clé partagée est identique à celle utilisée pour la configuration de votre périphérique VPN. Si ce n’est pas le cas, exécutez à nouveau la connexion en utilisant la valeur du périphérique ou mettez à jour le périphérique avec la valeur obtenue. Les valeurs doivent correspondre.

```azurecli
az network vpn-connection shared-key show --connection-name VNet1toSite2 -g TestRG1
```

## <a name="next-steps"></a>Étapes suivantes

*  Une fois la connexion achevée, vous pouvez ajouter des machines virtuelles à vos réseaux virtuels. Pour plus d’informations, consultez [Machines virtuelles](https://docs.microsoft.com/azure/#pivot=services&panel=Compute).
* Pour plus d’informations sur le protocole BGP, consultez les articles [Vue d’ensemble du protocole BGP](vpn-gateway-bgp-overview.md) et [Comment configurer BGP](vpn-gateway-bgp-resource-manager-ps.md).
* Pour plus d’informations sur le tunneling forcé, consultez l’article [Configurer un tunneling forcé](vpn-gateway-forced-tunneling-rm.md).
* Pour obtenir la liste des commandes de mise en réseau de l’interface de ligne de commande Azure, consultez l’article [Interface de ligne de commande Azure](https://docs.microsoft.com/cli/azure/network).
