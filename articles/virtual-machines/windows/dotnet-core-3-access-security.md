---
title: "Accès et sécurité dans les modèles Azure pour les machines virtuelles Windows | Microsoft Docs"
description: Didacticiel sur DotNet Core pour les machines virtuelles Azure
services: virtual-machines-windows
documentationcenter: virtual-machines
author: neilpeterson
manager: timlt
editor: tysonn
tags: azure-resource-manager
ms.assetid: e671fc45-5e4d-40fd-aac5-290892713cc0
ms.service: virtual-machines-windows
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: vm-windows
ms.workload: infrastructure-services
ms.date: 05/12/2017
ms.author: nepeters
ms.custom: H1Hack27Feb2017
ms.translationtype: Human Translation
ms.sourcegitcommit: 197ebd6e37066cb4463d540284ec3f3b074d95e1
ms.openlocfilehash: db267e4374d8786e65078843e8bcc4225085d5c8
ms.contentlocale: fr-fr
ms.lasthandoff: 03/31/2017

---
# <a name="access-and-security-in-azure-resource-manager-templates-for-windows-vms"></a>Accès et sécurité dans les modèles Azure Resource Manager pour les machines virtuelles Windows

Les applications hébergées dans Azure doivent probablement être accessibles via Internet ou un réseau privé virtuel (VPN)/une connexion ExpressRoute avec Azure. Avec l’exemple d’application du Store musique, le site web est accessible sur Internet avec une adresse IP publique. Avec un accès établi, les connexions à l’application et l’accès aux ressources de machine virtuelle doivent être sécurisés. Cette sécurité d’accès est assurée à l’aide d’un groupe de sécurité réseau. 

Ce document décrit en détail comment l’application du Store musique est sécurisée dans l’exemple de modèle Azure Resource Manager. Toutes les dépendances et configurations uniques sont en surbrillance. Pour optimiser l’expérience, prédéployez une instance de la solution sur votre abonnement Azure et travaillez avec le modèle Azure Resource Manager. Pour accéder au modèle complet, consultez [Déploiement du Store musique sous Windows](https://github.com/Microsoft/dotnet-core-sample-templates/tree/master/dotnet-core-music-windows).

## <a name="public-ip-address"></a>Adresse IP publique
Pour fournir un accès public à une ressource Azure, vous pouvez utiliser une ressource d’adresse IP publique. Une adresse IP publique peut être configurée avec une adresse IP statique ou dynamique. Si une adresse dynamique est utilisée, et que la machine virtuelle est arrêtée et désallouée, l’adresse est supprimée. Lorsque la machine redémarre, une autre adresse IP publique peut lui être attribuée. Pour empêcher la modification d’une adresse IP, une adresse IP réservée peut être utilisée. 

Il est possible d’ajouter une adresse IP publique à un modèle Azure Resource Manager à l’aide de l’Assistant Ajouter une nouvelle ressource de Visual Studio, ou d’insérer un JSON valide dans un modèle. 

Pour voir l’exemple JSON dans le modèle Resource Manager, suivez ce lien : [Adresse IP publique](https://github.com/Microsoft/dotnet-core-sample-templates/blob/master/dotnet-core-music-windows/azuredeploy.json#L110).

```json
{
  "apiVersion": "2015-06-15",
  "type": "Microsoft.Network/publicIPAddresses",
  "name": "[variables('publicIpAddressName')]",
  "location": "[resourceGroup().location]",
  "dependsOn": [],
  "tags": {
    "displayName": "public-ip"
  },
  "properties": {
    "publicIPAllocationMethod": "Dynamic",
    "dnsSettings": {
      "domainNameLabel": "[parameters('publicipaddressDnsName')]"
    }
  }
}
```

Une adresse IP publique peut être associée à une carte réseau virtuelle ou à un équilibreur de charge. Dans cet exemple, étant donné que la charge du site web du Store musique est équilibrée entre plusieurs machines virtuelles, l’adresse IP publique est attachée à l’équilibreur de charge.

Pour voir l’exemple JSON dans le modèle Resource Manager, suivez ce lien : [Association d’adresse IP publique avec un équilibrage de charge](https://github.com/Microsoft/dotnet-core-sample-templates/blob/master/dotnet-core-music-windows/azuredeploy.json#L211).

```json
"frontendIPConfigurations": [
  {
    "properties": {
      "publicIPAddress": {
        "id": "[resourceId('Microsoft.Network/publicIPAddresses', variables('publicIpAddressName'))]"
      }
    },
    "name": "LoadBalancerFrontend"
  }
]
```

Adresse IP publique dans le portail Azure. Notez que l’adresse IP publique est associée à un équilibreur de charge et non à une machine virtuelle. Les équilibreurs de charge réseau sont détaillés dans le document suivant de cette série.

![Adresse IP publique](./media/dotnet-core-3-access-security/pubip-win.png)

Pour plus d’informations sur les adresses IP publiques Azure, consultez [Adresses IP dans Azure](../../virtual-network/virtual-network-ip-addresses-overview-arm.md).

## <a name="network-security-group"></a>Groupe de sécurité réseau
Une fois l’accès aux ressources Azure établi, cet accès doit être limité. Pour des machines virtuelles Azure, la sécurisation de l’accès s’effectue à l’aide d’un groupe de sécurité réseau. Dans l’exemple d’application du Store musique, tout accès à la machine virtuelle est limité, sauf via le port 80 pour l’accès HTTP et le port 3389 pour l’accès RDP. Il est possible d’ajouter un groupe de sécurité réseau à un modèle Azure Resource Manager à l’aide de l’Assistant Ajouter une nouvelle ressource de Visual Studio, ou d’insérer un JSON valide dans un modèle.

Pour voir l’exemple JSON dans le modèle Resource Manager, suivez ce lien : [Groupe de sécurité réseau](https://github.com/Microsoft/dotnet-core-sample-templates/blob/master/dotnet-core-music-windows/azuredeploy.json#L57).

```json
{
  "apiVersion": "2016-03-30",
  "type": "Microsoft.Network/networkSecurityGroups",
  "name": "[variables('networkSecurityGroup')]",
  "location": "[resourceGroup().location]",
  "tags": {
    "displayName": "network-security-group"
  },
  "properties": {
    "securityRules": [
      {
        "name": "http",
        "properties": {
          "description": "http endpoint",
          "protocol": "Tcp",
          "sourcePortRange": "*",
          "destinationPortRange": "80",
          "sourceAddressPrefix": "*",
          "destinationAddressPrefix": "*",
          "access": "Allow",
          "priority": 100,
          "direction": "Inbound"
        }
      },
      ........<truncated> 
    ]
  }
},
```

Dans cet exemple, le groupe de sécurité réseau est associé à l’objet sous-réseau déclaré dans la ressource de réseau virtuel. 

Pour voir l’exemple JSON dans le modèle Resource Manager, suivez ce lien : [Association d’un groupe de sécurité réseau avec un réseau virtuel](https://github.com/Microsoft/dotnet-core-sample-templates/blob/master/dotnet-core-music-windows/azuredeploy.json#L143).

```json
"subnets": [
  {
    "name": "[variables('subnetName')]",
    "properties": {
      "addressPrefix": "10.0.0.0/24",
      "networkSecurityGroup": {
        "id": "[resourceId('Microsoft.Network/networkSecurityGroups', variables('networkSecurityGroup'))]"
      }
    }
  }
]
```

Voici à quoi ressemble le groupe de sécurité réseau dans le portail Azure. Notez qu’un groupe de sécurité réseau peut être associé à une interface réseau et/ou un sous-réseau. Dans ce cas, le groupe de sécurité réseau est associé à un sous-réseau. Dans cette configuration, les règles de trafic entrant s’appliquent à toutes les machines virtuelles connectées au sous-réseau.

![Groupe de sécurité réseau](./media/dotnet-core-3-access-security/nsg-win.png)

Pour plus d’informations sur les groupes de sécurité réseau, consultez [Présentation du groupe de sécurité réseau](https://azure.microsoft.com/documentation/articles/virtual-networks-nsg/).

## <a name="next-step"></a>Étape suivante
<hr>

[Étape 3 : disponibilité et mise à l’échelle dans les modèles Azure Resource Manager](dotnet-core-4-availability-scale.md?toc=%2fazure%2fvirtual-machines%2fwindows%2ftoc.json)


