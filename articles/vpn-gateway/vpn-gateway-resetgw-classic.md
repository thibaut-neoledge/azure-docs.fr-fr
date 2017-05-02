---
title: "Réinitialiser une passerelle VPN Azure pour rétablir les tunnels IPsec | Microsoft Docs"
description: "Cet article vous guide dans la réinitialisation de votre passerelle VPN Azure pour rétablir les tunnels IPsec. Cet article s’applique aux passerelles VPN dans le modèle de déploiement classique et le modèle de déploiement Resource Manager."
services: vpn-gateway
documentationcenter: na
author: cherylmc
manager: timlt
editor: 
tags: azure-resource-manager,azure-service-management
ms.assetid: 79d77cb8-d175-4273-93ac-712d7d45b1fe
ms.service: vpn-gateway
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 04/12/2017
ms.author: cherylmc
translationtype: Human Translation
ms.sourcegitcommit: 0c4554d6289fb0050998765485d965d1fbc6ab3e
ms.openlocfilehash: c63af2de6272604f4d2d1ee694ccc4272192ef9a
ms.lasthandoff: 04/13/2017


---
# <a name="reset-a-vpn-gateway"></a>Réinitialiser une passerelle VPN

La réinitialisation de la passerelle VPN Azure est utile lorsque vous perdez une connexion VPN entre locaux sur un ou plusieurs tunnels VPN de site à site. Dans ce cas, vos périphériques VPN sur site fonctionnent tous correctement, mais ils ne sont pas en mesure d’établir des tunnels IPsec avec les passerelles VPN Azure. Cet article vous guide dans le processus de réinitialisation de la passerelle VPN d’Azure. 

Chaque passerelle VPN Azure est une passerelle de réseau virtuel qui se compose de deux instances de machine virtuelle en cours d’exécution dans une configuration de serveur de secours actif. Lorsque vous réinitialisez la passerelle, elle redémarre celle-ci et lui réapplique les configurations entre différents locaux. La passerelle conserve l’adresse IP publique qu’elle a déjà. Cela signifie que vous n’avez pas à mettre à jour la configuration du routeur VPN avec une nouvelle adresse IP publique pour la passerelle VPN Azure.  

Une fois la commande émise, l’instance de la passerelle VPN Azure active actuelle est immédiatement redémarrée. Un bref laps de temps s’écoule pendant le basculement de l’instance active (en cours de redémarrage) vers l’instance de secours. Cet intervalle doit être inférieur à une minute.

Si la connexion n’est pas restaurée après le premier redémarrage, exécutez de nouveau la commande pour redémarrer la deuxième instance de machine virtuelle (la nouvelle passerelle active). Si les deux redémarrages sont demandés à la suite, il s’écoulera un délai un peu plus long pendant lequel les deux instances de machine virtuelle machine virtuelles (active/veille) sont en cours de redémarrage. En conséquence, l’intervalle de connectivité VPN permettant aux machines virtuelles de terminer les redémarrages sera un peu plus long, jusqu’à 2 à 4 minutes.

Après deux redémarrages, si vous continuez de rencontrer des problèmes de connectivité entre différents locaux, ouvrez une demande de support à partir du portail Azure.

## <a name="before-you-begin"></a>Avant de commencer
Avant de réinitialiser votre passerelle, vérifiez les éléments clés répertoriés ci-dessous pour chaque tunnel VPN IPsec Site à Site (S2S). Toute incohérence dans les éléments entraîne la déconnexion des tunnels VPN S2S. Une vérification et une correction des configurations pour vos passerelles locales et vos passerelles VPN Azure vous évitent des redémarrages et des perturbations sur les autres connexions en cours sur les passerelles.

Avant de réinitialiser votre passerelle, vérifiez les points suivants :

* Les adresses IP Internet (VIP) de la passerelle VPN Azure et de la passerelle VPN locale sont correctement configurées dans Azure et dans les stratégies VPN sur site.
* La clé prépartagée doit être la même sur la passerelle VPN Azure et la passerelle VPN locale.
* Si vous appliquez une configuration IPsec/IKE spécifique, telle que le chiffrement, des algorithmes de hachage et PFS (Perfect Forward Secrecy), vérifiez que les passerelles VPN Azure et locale ont les mêmes configurations.

## <a name="reset-a-vpn-gateway-using-the-azure-portal"></a>Réinitialiser une passerelle VPN à l’aide du portail Azure

Vous pouvez réinitialiser une passerelle VPN Resource Manager à l’aide du portail Azure. Si vous souhaitez réinitialiser une passerelle classique, consultez les étapes relatives à [PowerShell](#resetclassic).

### <a name="resource-manager-deployment-model"></a>Modèle de déploiement de Resource Manager

1. Ouvrez le portail Azure et accédez à la passerelle de réseau virtuel Resource Manager que vous souhaitez réinitialiser.
2. Dans le panneau de la passerelle de réseau virtuel, cliquez sur « Réinitialiser ».

    ![Panneau Réinitialiser la passerelle VPN](./media/vpn-gateway-howto-reset-gateway/reset-vpn-gateway-portal.png)

3. Dans le panneau Réinitialiser, cliquez sur ![Panneau Réinitialiser la passerelle VPN](./media/vpn-gateway-howto-reset-gateway/reset-button.png) .


## <a name="reset-a-vpn-gateway-using-powershell"></a>Réinitialiser une passerelle VPN à l’aide de PowerShell

### <a name="resource-manager-deployment-model"></a>Modèle de déploiement de Resource Manager

Vous avez besoin de la dernière version des applets de commande PowerShell. Pour plus d’informations, consultez [Installation et configuration d’Azure PowerShell](/powershell/azureps-cmdlets-docs) . L’applet de commande PowerShell Resource Manager permettant de réinitialiser une passerelle est `Reset-AzureRmVirtualNetworkGateway`. L’exemple suivant réinitialise la passerelle VPN Azure « VNet1GW » dans le groupe de ressources « TestRG1 ».

```powershell
$gw = Get-AzureRmVirtualNetworkGateway -Name VNet1GW -ResourceGroup TestRG1
Reset-AzureRmVirtualNetworkGateway -VirtualNetworkGateway $gw
```

### <a name="resetclassic"></a>Modèle de déploiement classique

Vous avez besoin de la dernière version des applets de commande PowerShell. Pour plus d’informations, consultez [Installation et configuration d’Azure PowerShell](/powershell/azureps-cmdlets-docs) . L’applet de commande PowerShell permettant de réinitialiser la passerelle VPN Azure est **Reset-AzureVNetGateway**. L’exemple qui suit permet de réinitialiser la passerelle VPN Azure du réseau virtuel appelé « ContosoVNet ».

```powershell
Reset-AzureVNetGateway –VnetName “ContosoVNet”
``` 

Résultat :

    Error          :
    HttpStatusCode : OK
    Id             : f1600632-c819-4b2f-ac0e-f4126bec1ff8
    Status         : Successful
    RequestId      : 9ca273de2c4d01e986480ce1ffa4d6d9
    StatusCode     : OK



