---
title: "Disponibilité et scalabilité dans les modèles Azure Resource Manager | Microsoft Docs"
description: Didacticiel sur DotNet Core pour les machines virtuelles Azure
services: virtual-machines-linux
documentationcenter: virtual-machines
author: neilpeterson
manager: timlt
editor: tysonn
tags: azure-service-management
ms.assetid: 8fcfea79-f017-4658-8c51-74242fcfb7f6
ms.service: virtual-machines-linux
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: vm-linux
ms.workload: infrastructure
ms.date: 11/21/2016
ms.author: nepeters
translationtype: Human Translation
ms.sourcegitcommit: 0dee2c3b92adc4098a51014dd7379aa3d53c1627
ms.openlocfilehash: 6f352b000ce4e284a1f1cc9ba9b384827ee62003


---
# <a name="availability-and-scale-in-azure-resource-manager-templates"></a>Disponibilité et scalabilité dans les modèles Azure Resource Manager
La disponibilité et la scalabilité font référence au temps de fonctionnement et à la capacité de répondre à la demande. Si une application doit être opérationnelle pendant jusqu’à 99,9 % du temps, elle doit présenter une architecture permettant l’utilisation de plusieurs ressources de calcul simultanées. Par exemple, au lieu d’avoir un seul site web, une configuration avec un niveau supérieur de disponibilité comprend plusieurs instances du même site, avec une technologie d’équilibrage placée devant celles-ci. Dans cette configuration, une instance de l’application peut être retirée à des fins de maintenance, tandis que les autres continuent de fonctionner. Par ailleurs, la scalabilité fait référence à la capacité d’une application à répondre à la demande. Avec une application dont la charge est équilibrée, l’ajout ou la suppression d’instances du pool permet la scalabilité d’une application pour répondre à la demande.

Ce document décrit en détail comment l’exemple de déploiement du Store musique est configuré pour la disponibilité et la scalabilité. Toutes les dépendances et configurations uniques sont en surbrillance. Pour optimiser l’expérience, prédéployez une instance de la solution sur votre abonnement Azure et travaillez avec le modèle Azure Resource Manager. Pour le modèle complet, consultez [Déploiement du Store musique sur Ubuntu](https://github.com/Microsoft/dotnet-core-sample-templates/tree/master/dotnet-core-music-linux).

## <a name="availability-set"></a>Groupe à haute disponibilité
Un groupe à haute disponibilité étend logiquement des machines virtuelles Azure sur des hôtes physiques et d’autres composants infrastructurels, tels que l’alimentation électrique et le matériel réseau physique. Des groupes à haute disponibilité garantissent que, pendant la maintenance, en cas de panne d’appareil ou lors d’autres temps d’arrêt, les machines virtuelles ne sont pas toutes affectées. Il est possible d’ajouter un groupe à haute disponibilité à un modèle Azure Resource Manager à l’aide de l’Assistant Ajouter une nouvelle ressource de Visual Studio, ou d’insérer un JSON valide dans un modèle.

Pour voir l’exemple JSON dans le modèle Resource Manager, suivez ce lien : [Groupe à haute disponibilité](https://github.com/Microsoft/dotnet-core-sample-templates/blob/master/dotnet-core-music-linux/azuredeploy.json#L387).

```json
{
  "apiVersion": "2015-06-15",
  "type": "Microsoft.Compute/availabilitySets",
  "name": "[variables('availabilitySetName')]",
  "location": "[resourceGroup().location]",
  "dependsOn": [],
  "tags": {
    "displayName": "avalibility-set"
  },
  "properties": {
    "platformUpdateDomainCount": 5,
    "platformFaultDomainCount": 3
  }
}
```

Un groupe à haute disponibilité est déclaré en tant que propriété d’une ressource de machine virtuelle. 

Pour voir l’exemple JSON dans le modèle Resource Manager, suivez ce lien : [Association d’un groupe à haute disponibilité avec une machine virtuelle](https://github.com/Microsoft/dotnet-core-sample-templates/blob/master/dotnet-core-music-linux/azuredeploy.json#L313).

```json
"properties": {
  "availabilitySet": {
    "id": "[resourceId('Microsoft.Compute/availabilitySets', variables('availabilitySetName'))]"
  }
```
Groupe à haute disponibilité dans le portail Azure. Chaque machine virtuelle est décrite ici avec des détails sur sa configuration.

![Groupe à haute disponibilité](./media/virtual-machines-linux-dotnet-core/aset.png)

Pour des informations détaillées sur les groupes à haute disponibilité, consultez [Gérer la disponibilité des machines virtuelles](virtual-machines-linux-manage-availability.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json). 

## <a name="network-load-balancer"></a>Équilibreur de charge réseau
Si un groupe à haute disponibilité offre à une application une tolérance de panne, un équilibreur de charge rend plusieurs instances de l’application disponibles sur une adresse réseau unique. Plusieurs instances d’une application peuvent être hébergées sur un grand nombre de machines virtuelles, chacune étant connectée à un équilibreur de charge. Lors de l’accès à l’application, l’équilibreur de charge route la demande entrante vers les membres attachés. Il est possible d’ajouter un équilibreur de charge à l’aide de l’Assistant Ajouter une nouvelle ressource de Visual Studio, ou en insérant une ressource JSON correctement mise en forme dans le modèle Azure Resource Manager.

Pour voir l’exemple JSON dans le modèle Resource Manager, suivez ce lien : [Équilibreur de charge réseau](https://github.com/Microsoft/dotnet-core-sample-templates/blob/master/dotnet-core-music-linux/azuredeploy.json#L208).

```json
{
  "apiVersion": "2015-06-15",
  "type": "Microsoft.Network/loadBalancers",
  "name": "[variables('loadBalancerName')]",
  "location": "[resourceGroup().location]",
  "tags": {
    "displayName": "load-balancer-front"
  },
  ........<truncated>
}
```

Étant donné que l’exemple d’application est exposé à Internet avec une adresse IP publique, celle-ci est associée à l’équilibreur de charge. 

Pour voir l’exemple JSON dans le modèle Resource Manager, suivez ce lien : [Association d’équilibreur de charge réseau avec une adresse IP publique](https://github.com/Microsoft/dotnet-core-sample-templates/blob/master/dotnet-core-music-linux/azuredeploy.json#L221).

```json
"frontendIPConfigurations": [
  {
    "properties": {
      "publicIPAddress": {
        "id": "[resourceId('Microsoft.Network/publicIPAddresses', variables('publicipaddressName'))]"
      }
    },
    "name": "LoadBalancerFrontend"
  }
]
```

Dans le portail Azure, la vue d’ensemble de l’équilibreur de charge réseau présente l’association avec l’adresse IP publique.

![Équilibreur de charge réseau](./media/virtual-machines-linux-dotnet-core/nlb.png)

## <a name="load-balancer-rule"></a>Règle d’équilibrage de charge
Lorsque vous utilisez un équilibreur de charge, des règles sont configurées. Elles contrôlent la manière dont le trafic est équilibré entre les ressources souhaitées. Avec l’exemple d’application du Store musique, le trafic arrive sur le port 80 de l’adresse IP publique, puis il est distribué au port 80 de toutes les machines virtuelles. 

Pour voir l’exemple JSON dans le modèle Resource Manager, suivez ce lien : [Règle d’équilibreur de charge](https://github.com/Microsoft/dotnet-core-sample-templates/blob/master/dotnet-core-music-linux/azuredeploy.json#L270).

```json
"loadBalancingRules": [
  {
    "name": "[variables('loadBalencerRule')]",
    "properties": {
      "frontendIPConfiguration": {
        "id": "[concat(resourceId('Microsoft.Network/loadBalancers', variables('loadBalancerName')), '/frontendIPConfigurations/LoadBalancerFrontend')]"
      },
      "backendAddressPool": {
        "id": "[variables('lbPoolID')]"
      },
      "protocol": "Tcp",
      "frontendPort": 80,
      "backendPort": 80,
      "enableFloatingIP": false,
      "idleTimeoutInMinutes": 5,
      "probe": {
        "id": "[variables('lbProbeID')]"
      }
    }
  }
]
```

Affichage de la règle d’équilibreur de charge réseau dans le portail.

![Règle d’équilibreur de charge réseau](./media/virtual-machines-linux-dotnet-core/lbrule.png)

## <a name="load-balancer-probe"></a>Sonde d’équilibreur de charge
L’équilibreur de charge doit également surveiller chaque machine virtuelle afin que les demandes soient servies uniquement aux systèmes en cours d’exécution. Cette surveillance s’effectue par sondage constant d’un port prédéfini. Le déploiement du Store musique est configuré pour sonder le port 80 de toutes les machines virtuelles incluses. 

Pour voir l’exemple JSON dans le modèle Resource Manager, suivez ce lien : [Sonde d’équilibreur de charge](https://github.com/Microsoft/dotnet-core-sample-templates/blob/master/dotnet-core-music-linux/azuredeploy.json#L257).

```json
"probes": [
  {
    "properties": {
      "protocol": "Tcp",
      "port": 80,
      "intervalInSeconds": 15,
      "numberOfProbes": 2
    },
    "name": "lbprobe"
  }
]
```

Sonde d’équilibreur de charge dans le portail Azure.

![Sonde d’équilibreur de charge réseau](./media/virtual-machines-linux-dotnet-core/lbprobe.png)

## <a name="inbound-nat-rules"></a>Règles NAT de trafic entrant
Lorsque vous utilisez un équilibreur de charge, des règles doivent être mises en place pour offrir un accès sans équilibrage de charge à chaque machine virtuelle. Par exemple, lors de la création d’une connexion SSH avec chaque machine virtuelle, ce trafic ne doit pas faire l’objet d’un équilibrage de charge. Au lieu de cela, un chemin d’accès prédéterminé doit être configuré. Des chemins d’accès prédéterminés sont configurés à l’aide d’une ressource de règle NAT de trafic entrant. Cette ressource permet de mapper les communications entrantes à des machines virtuelles individuelles. 

Avec l’application Store musique, un port commençant à 5000 est mappé au port 22 de chaque machine virtuelle pour l’accès SSH. La fonction `copyindex()` est utilisée pour incrémenter le port entrant, de façon à ce que la deuxième machine virtuelle reçoive le port entrant 5001, la troisième le port entrant 5002, et ainsi de suite. 

Pour voir l’exemple JSON dans le modèle Resource Manager, suivez ce lien : [Règles NAT de trafic entrant](https://github.com/Microsoft/dotnet-core-sample-templates/blob/master/dotnet-core-music-linux/azuredeploy.json#L270). 

```json
{
  "apiVersion": "2015-06-15",
  "type": "Microsoft.Network/loadBalancers/inboundNatRules",
  "name": "[concat(variables('loadBalancerName'), '/', 'SSH-VM', copyIndex())]",
  "tags": {
    "displayName": "load-balancer-nat-rule"
  },
  "location": "[resourceGroup().location]",
  "copy": {
    "name": "lbNatLoop",
    "count": "[parameters('numberOfInstances')]"
  },
  "dependsOn": [
    "[concat('Microsoft.Network/loadBalancers/', variables('loadBalancerName'))]"
  ],
  "properties": {
    "frontendIPConfiguration": {
      "id": "[variables('ipConfigID')]"
    },
    "protocol": "tcp",
    "frontendPort": "[copyIndex(5000)]",
    "backendPort": 22,
    "enableFloatingIP": false
  }
}
```

Exemple de règle NAT de trafic entrant dans le portail Azure. Une règle NAT SSH est créée pour chaque machine virtuelle dans le déploiement.

![Règle NAT de trafic entrant](./media/virtual-machines-linux-dotnet-core/natrule.png)

Pour des informations détaillées sur l’équilibreur de charge réseau Azure, consultez [Équilibrage de charge pour les services d’infrastructure Azure](virtual-machines-linux-load-balance.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json).

## <a name="deploy-multiple-vms"></a>Déployer plusieurs machines virtuelles
Enfin, pour qu’un groupe à haute disponibilité ou un équilibreur de charge fonctionnent effectivement, plusieurs machines virtuelles sont nécessaires. Plusieurs machines virtuelles peuvent être déployées à l’aide de la fonction de copie du modèle Azure Resource Manager. En utilisant la fonction de copie, il n’est pas nécessaire de définir un nombre fini de machines virtuelles. Au lieu de cela, cette valeur peut être fournie de façon dynamique au moment du déploiement. La fonction de copie utilise le nombre d’instances créées. Elle gère le déploiement du nombre approprié de machines virtuelles et de ressources associées.

Dans l’exemple de modèle de Store musique, un paramètre est défini, qui prend un nombre d’instances. Ce nombre est utilisé dans le modèle lors de la création des machines virtuelles et des ressources associées.

```json
"numberOfInstances": {
  "type": "int",
  "minValue": 1,
  "defaultValue": 1,
  "metadata": {
    "description": "Number of VM instances to be created behind load balancer."
  }
}
```

Sur la ressource de machine virtuelle, la boucle de copie reçoit un nom et le paramètre de nombre d’instances utilisé pour contrôler le nombre de copies obtenues.

Pour voir l’exemple JSON dans le modèle Resource Manager, suivez ce lien : [Fonction de copie de machine virtuelle](https://github.com/Microsoft/dotnet-core-sample-templates/blob/master/dotnet-core-music-linux/azuredeploy.json#L300). 

```json
"apiVersion": "2015-06-15",
"type": "Microsoft.Compute/virtualMachines",
"name": "[concat(variables('vmName'),copyindex())]",
"location": "[resourceGroup().location]",
"copy": {
  "name": "virtualMachineLoop",
  "count": "[parameters('numberOfInstances')]"
}
```

L’itération actuelle de la fonction de copie est accessible à l’aide de la fonction `copyIndex()` . La valeur de la fonction de copie d’index peut être utilisée pour nommer les machines virtuelles et autres ressources. Par exemple, si deux instances d’une machine virtuelle sont déployées, elles doivent porter des noms différents. La fonction `copyIndex()` peut être utilisée en tant que partie du nom de machine virtuelle pour créer un nom unique. Un exemple d’utilisation de la fonction `copyindex()` pour nommer est visible dans la ressource de machine virtuelle. Ici, le nom de la machine est une concaténation du paramètre `vmName` et de la fonction `copyIndex()`. 

Pour voir l’exemple JSON dans le modèle Resource Manager, suivez ce lien : [Fonction de copie d’index](https://github.com/Microsoft/dotnet-core-sample-templates/blob/master/dotnet-core-music-linux/azuredeploy.json#L319). 

```json
"osProfile": {
  "computerName": "[concat(parameters('vmName'),copyindex())]",
  "adminUsername": "[parameters('adminUsername')]",
  "linuxConfiguration": {
    "disablePasswordAuthentication": "true",
    "ssh": {
      "publicKeys": [
        {
          "path": "[variables('sshKeyPath')]",
          "keyData": "[parameters('sshKeyData')]"
        }
      ]
    }
  }
}
```

La fonction `copyIndex` est utilisée plusieurs fois dans l’exemple de modèle du Store musique. Les ressources et fonctions utilisant `copyIndex` contiennent tous les éléments spécifiques d’une instance unique de la machine virtuelle, tels que l’interface réseau, les règles d’équilibreur de charge et les fonctions de dépendance. 

Pour plus d’informations sur l’utilisation de la fonction de copie, consultez [Création de plusieurs instances de ressources dans Azure Resource Manager](../azure-resource-manager/resource-group-create-multiple.md).

## <a name="next-step"></a>Étape suivante
<hr>

[Étape 4 : Déploiement d’application avec des modèles Azure Resource Manager](virtual-machines-linux-dotnet-core-5-app-deployment.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json)




<!--HONumber=Feb17_HO2-->


