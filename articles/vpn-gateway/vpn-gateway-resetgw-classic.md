---
title: Réinitialiser une passerelle VPN Azure | Microsoft Docs
description: Cet article vous guide dans le processus de réinitialisation de la passerelle VPN d’Azure. Cet article s’applique aux passerelles VPN dans le modèle de déploiement classique et le modèle de déploiement Resource Manager.
services: vpn-gateway
documentationcenter: na
author: cherylmc
manager: carmonm
editor: ''
tags: azure-resource-manager,azure-service-management

ms.service: vpn-gateway
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 09/23/2016
ms.author: cherylmc

---
# <a name="reset-an-azure-vpn-gateway-using-powershell"></a>Réinitialiser une passerelle VPN Azure à l’aide de PowerShell
Cet article vous guide dans la réinitialisation de votre passerelle VPN Azure à l’aide des applets de commande PowerShell. Ces instructions incluent le modèle de déploiement classique et le modèle de déploiement Resource Manager.

La réinitialisation de la passerelle VPN Azure est utile lorsque vous perdez une connexion VPN entre locaux sur un ou plusieurs tunnels VPN S2S. Dans ce cas, vos périphériques VPN sur site fonctionnent tous correctement, mais ils ne sont pas en mesure d’établir des tunnels IPsec avec les passerelles VPN Azure. 

Chaque passerelle VPN Azure se compose de deux instances de machines virtuelles en cours d’exécution dans une configuration de serveur de secours actif. Lorsque vous utilisez l’applet de commande PowerShell pour réinitialiser la passerelle, elle redémarre celle-ci et lui réapplique les configurations entre différents locaux. La passerelle conserve l’adresse IP publique qu’elle a déjà. Cela signifie que vous n’avez pas à mettre à jour la configuration du routeur VPN avec une nouvelle adresse IP publique pour la passerelle VPN Azure.  

Une fois la commande émise, l’instance de la passerelle VPN Azure active actuelle est immédiatement redémarrée. Un bref laps de temps s’écoule pendant le basculement de l’instance active (en cours de redémarrage) vers l’instance de secours. Cet intervalle doit être inférieur à une minute.

Si la connexion n’est pas restaurée après le premier redémarrage, exécutez de nouveau la commande pour redémarrer la deuxième instance de machine virtuelle (la nouvelle passerelle active). Si les deux redémarrages sont demandés à la suite, il s’écoulera un délai un peu plus long pendant lequel les deux instances de machine virtuelle machine virtuelles (active/veille) sont en cours de redémarrage. En conséquence, l’intervalle de connectivité VPN permettant aux machines virtuelles de terminer les redémarrages sera un peu plus long, jusqu’à 2 à 4 minutes.

Après deux redémarrages, si vous continuez de rencontrer des problèmes de connectivité entre différents locaux, ouvrez une demande de support à partir du portail Azure.

## <a name="before-you-begin"></a>Avant de commencer
Avant de réinitialiser votre passerelle, vérifiez les éléments clés répertoriés ci-dessous pour chaque tunnel VPN IPsec Site à Site (S2S). Toute incohérence dans les éléments entraîne la déconnexion des tunnels VPN S2S. Une vérification et une correction des configurations pour vos passerelles locales et vos passerelles VPN Azure vous évitent des redémarrages et des perturbations sur les autres connexions en cours sur les passerelles.

Avant de réinitialiser votre passerelle, vérifiez les points suivants :

* Les adresses IP Internet (VIP) de la passerelle VPN Azure et de la passerelle VPN locale sont correctement configurées dans Azure et dans les stratégies VPN sur site.
* La clé prépartagée doit être la même sur la passerelle VPN Azure et la passerelle VPN locale.
* Si vous appliquez une configuration IPsec/IKE spécifique, telle que le chiffrement, des algorithmes de hachage et PFS (Perfect Forward Secrecy), vérifiez que les passerelles VPN Azure et locale ont les mêmes configurations.

## <a name="reset-a-vpn-gateway-using-the-resource-management-deployment-model"></a>Réinitialiser une passerelle VPN avec le modèle de déploiement Gestion des ressources
L’applet de commande PowerShell Resource Manager permettant de réinitialiser une passerelle est `Reset-AzureRmVirtualNetworkGateway`. L’exemple suivant réinitialise la passerelle VPN Azure « VNet1GW » dans le groupe de ressources « TestRG1 ».

    $gw = Get-AzureRmVirtualNetworkGateway -Name VNet1GW -ResourceGroup TestRG1
    Reset-AzureRmVirtualNetworkGateway -VirtualNetworkGateway $gw

## <a name="reset-a-vpn-gateway-using-the-classic-deployment-model"></a>Réinitialiser une passerelle VPN avec le modèle de déploiement classique
L’applet de commande PowerShell permettant de réinitialiser la passerelle VPN Azure est `Reset-AzureVNetGateway`. L’exemple qui suit permet de réinitialiser la passerelle VPN Azure du réseau virtuel appelé « ContosoVNet ».

    Reset-AzureVNetGateway –VnetName “ContosoVNet” 

Résultat :

    Error          :
    HttpStatusCode : OK
    Id             : f1600632-c819-4b2f-ac0e-f4126bec1ff8
    Status         : Successful
    RequestId      : 9ca273de2c4d01e986480ce1ffa4d6d9
    StatusCode     : OK


## <a name="next-steps"></a>Étapes suivantes
Pour plus d’informations, consultez [Référence sur les applets de commande PowerShell de gestion des services](https://msdn.microsoft.com/library/azure/mt617104.aspx) et [Référence sur l’applet de commande PowerShell Resource Manager](http://go.microsoft.com/fwlink/?LinkId=828732).

<!--HONumber=Oct16_HO2-->


