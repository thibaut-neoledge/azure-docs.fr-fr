---
title: "Résoudre les problèmes de connexion VPN de site à site Azure | Microsoft Docs"
description: "Découvrez comment résoudre le problème d’arrêt intempestif de la connexion VPN de site à site et d’impossibilité de reconnexion."
services: vpn-gateway
documentationcenter: na
author: chadmath
manager: cshepard
editor: 
tags: 
ms.service: vpn-gateway
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 06/21/2017
ms.author: genli
ms.translationtype: Human Translation
ms.sourcegitcommit: 61fd58063063d69e891d294e627ae40cb878d65b
ms.openlocfilehash: a72a46e4c2c93fdb3ab1a26c8854adb8cdc488a5
ms.contentlocale: fr-fr
ms.lasthandoff: 06/23/2017

---

# <a name="troubleshooting-azure-site-to-site-vpn-connection-cannot-connect-and-stops-working"></a>Résolution de problèmes : La connexion VPN de site à site Azure cesse de fonctionner

Vous configurez la connexion VPN de site à site entre le réseau local et un réseau virtuel Microsoft Azure. La connexion VPN cesse soudain de fonctionner et ne peut pas être rétablie. Cet article fournit les étapes requises pour vous aider à résoudre ce problème. 

[!INCLUDE [support-disclaimer](../../includes/support-disclaimer.md)]

## <a name="troubleshooting-steps"></a>Étapes de dépannage

Pour résoudre le problème, essayez d’abord [réinitialiser la passerelle de réseau virtuel Azure](vpn-gateway-resetgw-classic.md) et de réinitialiser le tunnel à partir de l’appareil VPN local. Si le problème persiste, procédez comme suit pour en identifier la cause.

### <a name="prerequisite-step"></a>Étape des conditions préalables

Vérifiez le type de passerelle de réseau virtuel Azure :

1. Accédez au [portail Azure](https://portal.azure.com).
2. Vérifiez les informations de type dans la page **Vue d’ensemble** de la passerelle de réseau virtuel.
    
    ![Vue d’ensemble de la passerelle](media\vpn-gateway-troubleshoot-site-to-site-cannot-connect\gatewayoverview.png)

### <a name="step-1-check-whether-the-on-premises-vpn-device-is-validated"></a>Étape 1 : Vérifier si l’appareil VPN local est validé

1. Vérifiez si vous utilisez un [appareil VPN et une version du système d’exploitation validés](vpn-gateway-about-vpn-devices.md#a-namedevicetableavalidated-vpn-devices-and-device-configuration-guides). Si l’appareil n’est pas un appareil VPN validé, vous devez contacter son fabricant pour en vérifier la compatibilité.
2. Assurez-vous que l’appareil VPN est correctement configuré. Pour plus d’informations, consultez [Modification des exemples de configuration de périphérique](/vpn-gateway-about-vpn-devices.md#editing).

### <a name="step-2-verify-the-shared-keypsk"></a>Étape 2 : Vérifier la clé partagée (PSK)

Comparez la **clé partagée** de l’appareil VPN local et celle du VPN de réseau virtuel pour vous assurer que les clés correspondent. 

Pour afficher cette clé dans l’optique de la connexion VPN Azure, utilisez l’une des méthodes suivantes :

**Portail Azure**

1. Accédez à Passerelle de réseau virtuel > Site pour accéder à la connexion de site que vous avez créée.
2. Dans la section **Paramètres**, cliquez sur **Clé partagée**.
    
    ![Clé partagée](media/vpn-gateway-troubleshoot-site-to-site-cannot-connect/sharedkey.png)

**Azure PowerShell**

Pour le mode Gestionnaire des ressources

    Get-AzureRmVirtualNetworkGatewayConnectionSharedKey -Name <Connection name> -ResourceGroupName <Resource group name>

Pour le mode Classic

    Get-AzureVNetGatewayKey -VNetName -LocalNetworkSiteName

### <a name="step-3-verify-the-vpn-peer-ips"></a>Étape 3 : Vérifier les adresses IP d’homologue VPN

-   La définition de l’adresse IP dans l’objet **Passerelle de réseau local** dans Azure doit correspondre à l’adresse IP de l’appareil local.
-   La définition de l’adresse IP de la passerelle Azure qui est configurée sur l’appareil local doit correspondre à l’adresse IP de la passerelle Azure.

### <a name="step-4-nsg-and-udr-on-gateway-subnet"></a>Étape 4 : Groupes de sécurité réseau et itinéraire définis par l’utilisateur sur le sous-réseau de passerelle

Recherchez et supprimez l’itinéraire défini par l’utilisateur ou les groupes de sécurité réseau sur le sous-réseau de passerelle, puis testez le résultat. Si le problème est résolu, validez les paramètres des groupes de sécurité réseau ou de l’itinéraire défini par l’utilisateur appliqués.

### <a name="step-5-check-on-premises-vpn-device-external-interface-address"></a>Étape 5 : Vérifier l’adresse d’interface externe de l’appareil VPN local

- Si l’adresse IP de l’appareil VPN, accessible sur Internet, est incluse dans la définition du **réseau local** dans Azure, il se peut que vous subissiez des déconnexions occasionnelles.
- L’interface externe de l’appareil doit être directement liée à Internet. Il ne doit y avoir aucune traduction d’adresses réseau (NAT) ni aucun pare-feu entre Internet et l’appareil.
-  Si vous configurez le clustering de pare-feu dans le but d’obtenir une adresse IP virtuelle, vous devez détruire le cluster et exposer l’appliance VPN directement à une interface publique susceptible de s’interfacer avec la passerelle.

### <a name="step-6-verify--subnets-match-exactly-azure-policy-based-gateways"></a>Étape 6 : Vérifier la correspondance des sous-réseaux (passerelles Azure basées sur des stratégies)

-   Vérifiez que les sous-réseaux correspondent exactement dans le réseau virtuel Azure et dans les définitions locales pour le réseau virtuel Azure.
-   Vérifiez que les sous-réseaux correspondent exactement dans **la passerelle réseau locale** et dans les définitions locales pour le réseau local.

### <a name="step-5-verify-azure-gateway-health-probe"></a>Étape 5 : Vérifier la sonde d’intégrité de la passerelle Azure

1. Accédez à https://&lt;YourVirtualNetworkGatewayIP&gt;:8081/healthprobe
2. Cliquez sur l’avertissement de certificat.
3. Si vous recevez une réponse, cela signifie que la passerelle de réseau virtuel est considérée comme saine. Vous ne recevez pas de réponse, cela signifie que la passerelle n’est peut-être pas saine ou qu’un groupe de sécurité réseau sur le sous-réseau de passerelle pose problème. Voici un exemple de réponse :

    &lt;?xml version="1.0"?>  <string xmlns="http://schemas.microsoft.com/2003/10/Serialization/">Primary Instance: GatewayTenantWorker_IN_1 GatewayTenantVersion: 14.7.24.6</string&gt;

### <a name="step-7-check-whether-the-on-premises-vpn-device-has-perfect-forward-secrecy-pfs-feature-enabled"></a>Étape 7 : Vérifier l’activation de la fonctionnalité PFS (Perfect Forward Secrecy) sur l’appareil VPN local

La fonctionnalité Perfect Forward Secrecy peut provoquer des problèmes de déconnexion. Si la fonctionnalité Perfect Forward Secrecy de l’appareil VPN est activée, désactivez-la. Mettez ensuite à jour la stratégie IPsec de la passerelle de réseau virtuel.

## <a name="next-steps"></a>Étapes suivantes

-   [Création d’une connexion de site à site dans le portail Azure](vpn-gateway-howto-site-to-site-resource-manager-portal.md)
-   [Configure IPsec/IKE policy for S2S VPN or VNet-to-VNet connections](vpn-gateway-ipsecikepolicy-rm-powershell.md) (Configurer la stratégie IPsec/IKE pour les connexions VPN de site à site ou de réseau virtuel à réseau virtuel)

