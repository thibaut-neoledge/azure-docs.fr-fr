---
title: "Connecter un réseau virtuel à plusieurs sites en utilisant une passerelle VPN et PowerShell | Microsoft Docs"
description: "Cet article vous guidera pour la connexion de plusieurs sites locaux à un réseau virtuel à l’aide d’une passerelle VPN pour le modèle de déploiement classique."
services: vpn-gateway
documentationcenter: na
author: yushwang
manager: rossort
editor: 
tags: azure-service-management
ms.assetid: b043df6e-f1e8-4a4d-8467-c06079e2c093
ms.service: vpn-gateway
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 05/11/2016
ms.author: yushwang
translationtype: Human Translation
ms.sourcegitcommit: dcda8b30adde930ab373a087d6955b900365c4cc
ms.openlocfilehash: 3d6f2d2bc7bd55d7f7d609e66826e3a3722c8f32


---
# <a name="add-a-site-to-site-connection-to-a-vnet-with-an-existing-vpn-gateway-connection"></a>Ajouter une connexion de site à site à un réseau virtuel avec une connexion de passerelle VPN existante
> [!div class="op_single_selector"]
> * [Resource Manager - Portail](vpn-gateway-howto-multi-site-to-site-resource-manager-portal.md)
> * [Classic - PowerShell](vpn-gateway-multi-site.md)
> 
> 

Cet article vous explique comment utiliser PowerShell pour ajouter des connexions de site à site (S2S) à une passerelle VPN qui dispose déjà d’une connexion. Pour ce type de connexion, on fait souvent référence à une configuration « multisite ». 

Cet article s’applique aux réseaux virtuels créés à l’aide du modèle de déploiement classique (également connu comme le modèle Gestion des services). Ces étapes ne s’appliquent pas aux configurations de connexion coexistante ExpressRoute/site à site. Consultez l’article [ExpressRoute/S2S coexisting connections](../expressroute/expressroute-howto-coexist-classic.md) (Connexions coexistantes ExpressRoute/S2S) pour en savoir plus sur les connexions coexistantes.

### <a name="deployment-models-and-methods"></a>Outils et modèles de déploiement
[!INCLUDE [vpn-gateway-clasic-rm](../../includes/vpn-gateway-classic-rm-include.md)]

Nous mettons à jour ce tableau à mesure que de nouveaux articles et des outils supplémentaires sont disponibles pour cette configuration. Quand un article est disponible, le lien vers cet article est ajouté à ce tableau.

[!INCLUDE [vpn-gateway-table-multi-site](../../includes/vpn-gateway-table-multisite-include.md)]

## <a name="about-connecting"></a>À propos de la connexion
Vous pouvez connecter plusieurs sites locaux à un même réseau virtuel. Cela est particulièrement intéressant pour la création de solutions de cloud hybrides. La création d’une connexion sur plusieurs sites à votre passerelle de réseau virtuel Azure est très similaire à la création d’autres connexions de site à site. En fait, vous pouvez utiliser une passerelle VPN Azure existante tant que la passerelle est dynamique (basée sur un itinéraire).

Si une passerelle statique est déjà connectée à votre réseau virtuel, vous pouvez changer le type de passerelle en dynamique sans avoir à régénérer le réseau virtuel pour prendre en charge plusieurs sites. Dans ce cas, assurez-vous que votre passerelle VPN locale prend en charge les configurations VPN basées sur un itinéraire. 

![diagramme multi-sites](./media/vpn-gateway-multi-site/multisite.png "multi-site")

## <a name="points-to-consider"></a>Éléments à prendre en considération
**Vous ne pouvez pas utiliser le portail Azure Classic pour apporter des modifications à ce réseau virtuel.**  Pour cette version, vous devez apporter des modifications au fichier de configuration réseau au lieu d’utiliser le portail Azure Classic. Si vous apportez des modifications dans le portail Azure Classic, elles remplacent vos paramètres de référence multisite pour ce réseau virtuel. 

Vous aurez le temps de vous familiariser avec le fichier de configuration réseau au terme de la procédure multisite. Toutefois, si plusieurs personnes travaillent sur la configuration de votre réseau, vous devez vous assurer que tout le monde a connaissance de cette limitation. Cela ne signifie pas que vous ne pouvez pas du tout utiliser le portail Azure Classic. Vous pouvez l’utiliser pour toutes les procédures, à l’exception des modifications de configuration de ce réseau virtuel spécifique.

## <a name="before-you-begin"></a>Avant de commencer
Avant de commencer la configuration, vérifiez que les conditions suivantes sont remplies :

* Un abonnement Azure. Si vous ne disposez pas déjà d’un abonnement Azure, vous pouvez activer vos [avantages abonnés MSDN](https://azure.microsoft.com/pricing/member-offers/msdn-benefits-details/) ou créer un [compte gratuit](https://azure.microsoft.com/pricing/free-trial/).
* Matériel VPN compatible pour chaque emplacement local. Voir [À propos des périphériques VPN pour la connectivité au réseau virtuel](vpn-gateway-about-vpn-devices.md) pour vérifier si l’appareil que vous souhaitez utiliser est bien compatible.
* Une adresse IP IPv4 publique exposée en externe pour chaque périphérique VPN. L’adresse IP ne peut pas se trouver derrière un NAT. Cela est obligatoire.
* Vous aurez besoin d’installer la dernière version des applets de commande PowerShell Azure. Pour plus d’informations sur l’installation des applets de commande PowerShell, consultez [Installation et configuration d’Azure PowerShell](/powershell/azureps-cmdlets-docs) .
* Un expert en configuration du matériel VPN. Vous ne pouvez pas utiliser les scripts VPN générés automatiquement à partir du portail Azure Classic pour configurer vos périphériques VPN. Cela signifie que vous devez avoir une bonne maîtrise de la façon de configurer votre périphérique VPN ou collaborer avec quelqu’un disposant des connaissances nécessaires.
* Les plages d’adresses IP que vous souhaitez utiliser pour votre réseau virtuel (si vous n’en avez pas déjà créé). 
* Les plages d’adresses IP pour chacun des sites du réseau local auxquels vous vous connecterez. Vous devez vous assurer que les plages d’adresses IP pour chacun des sites du réseau local auxquels vous souhaitez vous connecter ne se chevauchent ne pas. Dans le cas contraire, le portail Azure Classic ou l’API REST rejette la configuration en cours de chargement. 
  
    Par exemple, si vous avez deux sites de réseau local contenant la plage d’adresse IP 10.2.3.0/24 et que vous disposez d’un package avec une adresse de destination 10.2.3.3, Azure ne saura pas à quel site envoyer le package, car les plages d’adresses se chevauchent. Pour éviter les problèmes de routage, Azure ne vous permet pas de télécharger un fichier de configuration ayant des plages qui se chevauchent.

## <a name="1-create-a-site-to-site-vpn"></a>1. Créer un VPN de site à site
Si vous avez déjà un VPN de site à site avec une passerelle de routage dynamique, bravo ! Vous pouvez [Exporter les paramètres de configuration de réseau virtuel](#export). Sinon, procédez ainsi :

### <a name="if-you-already-have-a-site-to-site-virtual-network-but-it-has-a-static-policy-based-routing-gateway"></a>Si vous avez déjà un réseau virtuel de site à site, mais que sa passerelle de routage est statique :
1. Modifiez le type de passerelle en routage dynamique. Un VPN multisite requiert une passerelle de routage dynamique (ou basée sur un itinéraire). Pour modifier le type de passerelle, vous devez d’abord supprimer la passerelle existante, puis en créer une nouvelle. Pour obtenir des instructions, consultez la page [Modification du type de routage VPN de votre passerelle](vpn-gateway-configure-vpn-gateway-mp.md#how-to-change-the-vpn-routing-type-for-your-gateway).  
2. Configurez votre nouvelle passerelle et créez votre tunnel VPN. Pour obtenir des instructions, consultez [Configuration d’une passerelle VPN dans le portail Azure Classic](vpn-gateway-configure-vpn-gateway-mp.md). Tout d’abord, changez le type de votre passerelle en dynamique. 

### <a name="if-you-dont-have-a-site-to-site-virtual-network"></a>Si vous n’avez pas de réseau virtuel de site à site :
1. Créez votre réseau virtuel de site à site en suivant la procédure décrite dans [Création d’un réseau virtuel avec une connexion VPN de site à site dans le portail Azure Classic](vpn-gateway-site-to-site-create.md).  
2. Configurez une passerelle de routage dynamique en suivant la procédure décrite dans [Configuration d’une passerelle VPN](vpn-gateway-configure-vpn-gateway-mp.md). Veillez à sélectionner le **routage dynamique** pour le type de passerelle.

## <a name="a-nameexporta2-export-the-network-configuration-file"></a><a name="export"></a>2. Exporter le fichier de configuration réseau
Exportez le fichier de configuration réseau. Le fichier que vous exportez permet de configurer vos nouveaux paramètres multisites. Si vous avez besoin d’instructions sur l’exportation d’un fichier, consultez la section dans l’article [Création d’un réseau virtuel à l’aide d’un fichier de configuration réseau dans le portail Azure](../virtual-network/virtual-networks-create-vnet-classic-portal.md#how-to-create-a-vnet-using-a-network-config-file-in-the-azure-portal). 

## <a name="3-open-the-network-configuration-file"></a>3. Ouvrir le fichier de configuration réseau
Ouvrez le fichier de configuration réseau que vous avez téléchargé à l’étape précédente. Utilisez l’éditeur xml de votre choix. Le fichier doit se présenter ainsi :

        <NetworkConfiguration xmlns:xsd="http://www.w3.org/2001/XMLSchema" xmlns:xsi="http://www.w3.org/2001/XMLSchema-instance" xmlns="http://schemas.microsoft.com/ServiceHosting/2011/07/NetworkConfiguration">
          <VirtualNetworkConfiguration>
            <LocalNetworkSites>
              <LocalNetworkSite name="Site1">
                <AddressSpace>
                  <AddressPrefix>10.0.0.0/16</AddressPrefix>
                  <AddressPrefix>10.1.0.0/16</AddressPrefix>
                </AddressSpace>
                <VPNGatewayAddress>131.2.3.4</VPNGatewayAddress>
              </LocalNetworkSite>
              <LocalNetworkSite name="Site2">
                <AddressSpace>
                  <AddressPrefix>10.2.0.0/16</AddressPrefix>
                  <AddressPrefix>10.3.0.0/16</AddressPrefix>
                </AddressSpace>
                <VPNGatewayAddress>131.4.5.6</VPNGatewayAddress>
              </LocalNetworkSite>
            </LocalNetworkSites>
            <VirtualNetworkSites>
              <VirtualNetworkSite name="VNet1" AffinityGroup="USWest">
                <AddressSpace>
                  <AddressPrefix>10.20.0.0/16</AddressPrefix>
                  <AddressPrefix>10.21.0.0/16</AddressPrefix>
                </AddressSpace>
                <Subnets>
                  <Subnet name="FE">
                    <AddressPrefix>10.20.0.0/24</AddressPrefix>
                  </Subnet>
                  <Subnet name="BE">
                    <AddressPrefix>10.20.1.0/24</AddressPrefix>
                  </Subnet>
                  <Subnet name="GatewaySubnet">
                    <AddressPrefix>10.20.2.0/29</AddressPrefix>
                  </Subnet>
                </Subnets>
                <Gateway>
                  <ConnectionsToLocalNetwork>
                    <LocalNetworkSiteRef name="Site1">
                      <Connection type="IPsec" />
                    </LocalNetworkSiteRef>
                  </ConnectionsToLocalNetwork>
                </Gateway>
              </VirtualNetworkSite>
            </VirtualNetworkSites>
          </VirtualNetworkConfiguration>
        </NetworkConfiguration>

## <a name="4-add-multiple-site-references"></a>4. Ajouter plusieurs références de site
Lorsque vous ajoutez ou supprimez les informations de référence de site, des modifications de configuration sont apportées à ConnectionsToLocalNetwork/LocalNetworkSiteRef. À l’ajout d’une nouvelle référence de site local, Azure crée un nouveau tunnel. Dans l’exemple ci-dessous, la configuration du réseau correspond à une connexion de site unique. Enregistrez le fichier une fois que vous avez terminé d’apporter vos modifications.

        <Gateway>
          <ConnectionsToLocalNetwork>
            <LocalNetworkSiteRef name="Site1"><Connection type="IPsec" /></LocalNetworkSiteRef>
          </ConnectionsToLocalNetwork>
        </Gateway>

    To add additional site references (create a multi-site configuration), simply add additional "LocalNetworkSiteRef" lines, as shown in the example below: 

        <Gateway>
          <ConnectionsToLocalNetwork>
            <LocalNetworkSiteRef name="Site1"><Connection type="IPsec" /></LocalNetworkSiteRef>
            <LocalNetworkSiteRef name="Site2"><Connection type="IPsec" /></LocalNetworkSiteRef>
          </ConnectionsToLocalNetwork>
        </Gateway>

## <a name="5-import-the-network-configuration-file"></a>5. Importer le fichier de configuration réseau
Importez le fichier de configuration réseau. Lorsque vous importez ce fichier avec les modifications, les nouveaux tunnels sont ajoutés. Les tunnels utilisent la passerelle dynamique que vous avez créée précédemment. Si vous avez besoin d’instructions sur l’importation du fichier, consultez la section dans l’article [Création d’un réseau virtuel à l’aide d’un fichier de configuration réseau dans le portail Azure](../virtual-network/virtual-networks-create-vnet-classic-portal.md#how-to-create-a-vnet-using-a-network-config-file-in-the-azure-portal). 

## <a name="6-download-keys"></a>6. Télécharger les clés
Une fois les nouveaux tunnels ajoutés, utilisez l’applet de commande PowerShell `Get-AzureVNetGatewayKey` pour obtenir les clés prépartagées IPsec/IKE de chaque tunnel.

Par exemple :

    Get-AzureVNetGatewayKey –VNetName "VNet1" –LocalNetworkSiteName "Site1"

    Get-AzureVNetGatewayKey –VNetName "VNet1" –LocalNetworkSiteName "Site2"

Si vous préférez, vous pouvez également utiliser l’API REST *Obtenir la clé partagée de la passerelle de réseau virtuel* pour obtenir les clés prépartagées.

## <a name="7-verify-your-connections"></a>7. Vérifiez vos connexions
Vérifiez l’état de tunnel multisite. Après avoir téléchargé les clés de chaque tunnel, vérifiez les connexions. Utilisez `Get-AzureVnetConnection` pour obtenir la liste des tunnels de réseau virtuel, comme indiqué dans l’exemple ci-dessous. VNet1 est le nom du réseau virtuel.

    Get-AzureVnetConnection -VNetName VNET1

    ConnectivityState         : Connected
    EgressBytesTransferred    : 661530
    IngressBytesTransferred   : 519207
    LastConnectionEstablished : 5/2/2014 2:51:40 PM
    LastEventID               : 23401
    LastEventMessage          : The connectivity state for the local network site 'Site1' changed from Not Connected to Connected.
    LastEventTimeStamp        : 5/2/2014 2:51:40 PM
    LocalNetworkSiteName      : Site1
    OperationDescription      : Get-AzureVNetConnection
    OperationId               : 7f68a8e6-51e9-9db4-88c2-16b8067fed7f
    OperationStatus           : Succeeded

    ConnectivityState         : Connected
    EgressBytesTransferred    : 789398
    IngressBytesTransferred   : 143908
    LastConnectionEstablished : 5/2/2014 3:20:40 PM
    LastEventID               : 23401
    LastEventMessage          : The connectivity state for the local network site 'Site2' changed from Not Connected to Connected.
    LastEventTimeStamp        : 5/2/2014 2:51:40 PM
    LocalNetworkSiteName      : Site2
    OperationDescription      : Get-AzureVNetConnection
    OperationId               : 7893b329-51e9-9db4-88c2-16b8067fed7f
    OperationStatus           : Succeeded

## <a name="next-steps"></a>Étapes suivantes
Pour en savoir plus sur les passerelles VPN, consultez [À propos des passerelles VPN](vpn-gateway-about-vpngateways.md).




<!--HONumber=Dec16_HO2-->


