---
title: "Supprimer une passerelle de réseau virtuel : PowerShell : Azure Classic | Microsoft Docs"
description: "Supprimez une passerelle de réseau virtuel avec PowerShell dans le modèle de déploiement classique."
services: vpn-gateway
documentationcenter: na
author: cherylmc
manager: timlt
editor: 
tags: azure-service-management
ms.assetid: 
ms.service: vpn-gateway
ms.devlang: na
ms.topic: 
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 03/20/2017
ms.author: cherylmc
translationtype: Human Translation
ms.sourcegitcommit: 0d8472cb3b0d891d2b184621d62830d1ccd5e2e7
ms.openlocfilehash: b5d90aa60de4a24b6d76414204ceae24670e48a3
ms.lasthandoff: 03/21/2017


---
# <a name="delete-a-virtual-network-gateway-using-powershell-classic"></a>Supprimer une passerelle de réseau virtuel avec PowerShell (classique)
> [!div class="op_single_selector"]
> * [Resource Manager - PowerShell](vpn-gateway-delete-vnet-gateway-powershell.md)
> * [Classic - PowerShell](vpn-gateway-delete-vnet-gateway-classic-powershell.md)
>
>

Vous pouvez supprimer une passerelle VPN dans le modèle de déploiement classique à l’aide de PowerShell. Une fois la passerelle de réseau virtuel supprimée, modifiez le fichier de configuration réseau pour supprimer les éléments que vous n’utilisez plus.

##<a name="part-1-connect-to-azure"></a>Partie 1. Connexion à Azure

### <a name="1-install-the-latest-powershell-cmdlets"></a>1. Installez les dernières applets de commande PowerShell.

Téléchargez et installez la dernière version des applets de commande PowerShell Azure Service Management (SM). Pour plus d’informations, consultez la rubrique [Installation et configuration d’Azure PowerShell](/powershell/azureps-cmdlets-docs).

### <a name="2-connect-to-your-azure-account"></a>2. Connectez-vous à votre compte Azure. 

Ouvrez la console PowerShell avec des droits élevés et connectez-vous à votre compte. Utilisez l’exemple suivant pour faciliter votre connexion :

    Login-AzureRmAccount

Vérifiez les abonnements associés au compte.

    Get-AzureRmSubscription

Si vous avez plusieurs abonnements, sélectionnez celui que vous souhaitez utiliser.

    Select-AzureRmSubscription -SubscriptionName "Replace_with_your_subscription_name"

Utilisez l’applet de commande suivante pour ajouter votre abonnement Azure à PowerShell pour le modèle de déploiement classique.

    Add-AzureAccount

## <a name="part-2-export-and-view-the-network-configuration-file"></a>Partie 2. Exporter et afficher le fichier de configuration réseau

Créez un répertoire sur votre ordinateur, puis exportez le fichier de configuration réseau dans ce répertoire. Vous utilisez ce fichier aussi bien pour afficher les informations de configuration actuelles que pour modifier la configuration réseau.

Dans cet exemple, le fichier de configuration réseau est exporté vers C:\AzureNet.

     Get-AzureVNetConfig -ExportToFile C:\AzureNet\NetworkConfig.xml

Dans un éditeur de texte, ouvrez le fichier, puis affichez le nom de votre réseau virtuel classique. Lorsque vous créez un réseau virtuel dans le portail Azure, le nom complet utilisé par Azure n’est pas visible dans le portail Azure. Par exemple, un réseau virtuel qui semble être nommé « ClassicVNet1 » dans le portail Azure peut avoir un nom beaucoup plus long dans le fichier de configuration réseau. Le nom peut ressembler à ceci : « Group ClassicRG1 ClassicVNet1 ». Les noms des réseaux virtuels sont répertoriés comme suit : « VirtualNetworkSite name = ».<br>Utilisez les noms indiqués dans le fichier de configuration réseau lors de l’exécution de vos applets de commande PowerShell.

## <a name="part-3-delete-the-virtual-network-gateway"></a>Partie 3. Supprimer la passerelle de réseau virtuel

Lorsque vous supprimez une passerelle de réseau virtuel, toutes les connexions au réseau virtuel via la passerelle sont déconnectées. Si vous disposez de clients P2S connectés au réseau virtuel, ils sont déconnectés sans avertissement.

Dans l’exemple ci-dessous, la passerelle de réseau virtuel est supprimée. Lorsque vous exécutez cet exemple, utilisez le nom complet du réseau virtuel indiqué dans le fichier de configuration réseau.

    Remove-AzureVNetGateway -VNetName "Group ClassicRG1 ClassicVNet1"

Si l’opération réussit, la valeur de retour s’affiche :

    Status : Successful

## <a name="part-4-modify-the-network-configuration-file"></a>Partie 4. Modifier le fichier de configuration réseau

Lorsque vous supprimez une passerelle de réseau virtuel, l’applet de commande ne modifie pas le fichier de configuration réseau. Vous devez le modifier pour supprimer les éléments qui ne sont plus utilisés. Les sections suivantes vous aident à modifier le fichier de configuration réseau que vous avez téléchargé.

###<a name="local-network-site-references"></a>Informations de référence de site de réseau local

Pour supprimer les informations de référence de site, apportez des modifications de configuration à ConnectionsToLocalNetwork/LocalNetworkSiteRef. La suppression d’une référence de site local déclenche la suppression d’un tunnel par Azure. En fonction de la configuration que vous avez créée, il se peut qu’une référence LocalNetworkSiteRef ne soit pas répertoriée.

    <Gateway>
       <ConnectionsToLocalNetwork>
         <LocalNetworkSiteRef name="D1BFC9CB_Site2">
           <Connection type="IPsec" />
         </LocalNetworkSiteRef>
       </ConnectionsToLocalNetwork>
    </Gateway>

Exemple :

    <Gateway>
       <ConnectionsToLocalNetwork>
       </ConnectionsToLocalNetwork>
     </Gateway>

###<a name="local-network-sites"></a>Sites de réseau local

Supprimez les sites locaux que vous n’utilisez plus. Selon la configuration que vous avez créée, il est possible que vous ne disposiez pas d’un site de réseau local répertorié.

    <LocalNetworkSites>
      <LocalNetworkSite name="Site1">
        <AddressSpace>
          <AddressPrefix>192.168.0.0/16</AddressPrefix>
        </AddressSpace>
        <VPNGatewayAddress>5.4.3.2</VPNGatewayAddress>
      </LocalNetworkSite>
      <LocalNetworkSite name="Site3">
        <AddressSpace>
          <AddressPrefix>192.168.0.0/16</AddressPrefix>
        </AddressSpace>
        <VPNGatewayAddress>57.179.18.164</VPNGatewayAddress>
      </LocalNetworkSite>
    </LocalNetworkSites>

Dans cet exemple, nous avons supprimé uniquement Site3.

    <LocalNetworkSites>
        <LocalNetworkSite name="Site1">
        <AddressSpace>
          <AddressPrefix>192.168.0.0/16</AddressPrefix>
        </AddressSpace>
        <VPNGatewayAddress>5.4.3.2</VPNGatewayAddress>
      </LocalNetworkSite>
    </LocalNetworkSites>

### <a name="client-addresspool"></a>Pool d’adresses de client

Si vous possédez une connexion P2S à votre réseau virtuel, vous disposez d’un pool VPNClientAddressPool. Supprimez les pools d’adresses de client qui correspondent à la passerelle de réseau virtuel que vous avez supprimée.

    <Gateway>
       <VPNClientAddressPool>
         <AddressPrefix>10.1.0.0/24</AddressPrefix>
       </VPNClientAddressPool>
       <ConnectionsToLocalNetwork />
    </Gateway>

Exemple :

     <Gateway>
       <ConnectionsToLocalNetwork />
     </Gateway>

### <a name="gatewaysubnet"></a>Sous-réseau de passerelle

Supprimez le sous-réseau de passerelle qui correspond au réseau virtuel.

    <Subnets>
       <Subnet name="FrontEnd">
         <AddressPrefix>10.11.0.0/24</AddressPrefix>
       </Subnet>
       <Subnet name="GatewaySubnet">
         <AddressPrefix>10.11.1.0/29</AddressPrefix>
       </Subnet>
     </Subnets>

Exemple :

    <Subnets>
       <Subnet name="FrontEnd">
         <AddressPrefix>10.11.0.0/24</AddressPrefix>
       </Subnet>
     </Subnets>

## <a name="part-5-upload-the-network-configuration-file"></a>Partie 5. Charger le fichier de configuration réseau

Enregistrez vos modifications et chargez le fichier de configuration réseau dans Azure. Assurez-vous que vous modifiez le chemin d'accès selon les besoins de votre environnement.

     Set-AzureVNetConfig -ConfigurationPath C:\AzureNet\NetworkConfig.xml

Si l’opération réussit, la valeur de retour affiche un contenu semblable à cet exemple :

     OperationDescription        OperationId                      OperationStatus                                                
     --------------------        -----------                      ---------------                                                
     Set-AzureVNetConfig        e0ee6e66-9167-cfa7-a746-7casb9    Succeeded
