
---
title: "Création d’un environnement Linux complet à l’aide de l’interface Azure CLI 1.0 | Microsoft Docs"
description: "Créez un stockage, une machine virtuelle Linux, un réseau virtuel et un sous-réseau, un équilibreur de charge, une carte d’interface réseau, une adresse IP publique et un groupe de sécurité réseau à partir de zéro à l’aide de l’interface de ligne de commande Azure 1.0."
services: virtual-machines-linux
documentationcenter: virtual-machines
author: iainfoulds
manager: timlt
editor: 
tags: azure-resource-manager
ms.assetid: 4ba4060b-ce95-4747-a735-1d7c68597a1a
ms.service: virtual-machines-linux
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: vm-linux
ms.workload: infrastructure
ms.date: 02/09/2017
ms.author: iainfou
ms.translationtype: HT
ms.sourcegitcommit: d941879aee6042b38b7f5569cd4e31cb78b4ad33
ms.openlocfilehash: 201ccd523e49d638ace50fbc0ffdceb705b35473
ms.contentlocale: fr-fr
ms.lasthandoff: 07/10/2017


---
# <a name="create-a-complete-linux-environment-with-the-azure-cli-10"></a>Créer un environnement Linux complet à l’aide de l’interface Azure CLI 1.0
Dans cet article, nous créons un réseau simple avec un équilibreur de charge et deux machines virtuelles à des fins de développement et de calcul simple. Nous suivons ce processus, commande par commande, jusqu’à ce que vous disposiez de deux machines virtuelles Linux sécurisées opérationnelles, auxquelles vous pouvez vous connecter à partir de n’importe quel emplacement via Internet. Vous pourrez ensuite créer des réseaux et des environnements plus complexes.

Vous allez découvrir la hiérarchie des dépendances et la puissance que vous offre le modèle de déploiement Resource Manager. Une fois que vous avez compris la façon dont le système est créé, vous pouvez le reconstruire beaucoup plus rapidement en utilisant des [modèles Azure Resource Manager](../../resource-group-authoring-templates.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json). De même, après avoir compris la façon dont les différentes parties de votre environnement s’imbriquent, la création de modèles pour automatiser ces dernières se révèle plus simple.

L’environnement contient :

* Deux machines virtuelles dans un groupe à haute disponibilité.
* Un équilibreur de charge avec une règle d’équilibrage de charge sur le port 80.
* Des règles de groupe de sécurité réseau (NSG) pour protéger votre machine virtuelle de tout trafic indésirable.

Pour créer cet environnement personnalisé, [l’interface de ligne de commande 1.0 Azure](../../cli-install-nodejs.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json) la plus récente doit être installée en mode Resource Manager (`azure config mode arm`). Vous avez également besoin d’un outil d’analyse JSON. Cet exemple utilise [jq](https://stedolan.github.io/jq/).


## <a name="cli-versions-to-complete-the-task"></a>Versions de l’interface de ligne de commande permettant d’effectuer la tâche
Vous pouvez exécuter la tâche en utilisant l’une des versions suivantes de l’interface de ligne de commande (CLI) :

- [Azure CLI 1.0](#quick-commands) : notre interface de ligne de commande pour les modèles de déploiement Classique et Resource Manager (cet article)
- [Azure CLI 2.0](create-cli-complete.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json) : notre interface Azure CLI nouvelle génération pour le modèle de déploiement Resource Manager


## <a name="quick-commands"></a>Commandes rapides
Si vous avez besoin d’accomplir rapidement cette tâche, la section suivante décrit les commandes de base qui vous permettront de charger une machine virtuelle dans Azure. Pour obtenir plus d’informations et davantage de contexte pour chaque étape, lisez la suite de ce document, à partir de [cette section](#detailed-walkthrough).

Veillez à ce que [l’interface de ligne de commande 1.0 Azure](../../cli-install-nodejs.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json) soit connectée et utilise le mode Resource Manager :

```azurecli
azure config mode arm
```

Dans les exemples suivants, remplacez les exemples de noms de paramètre par vos propres valeurs. Exemples de noms de paramètre : `myResourceGroup`, `mystorageaccount` et `myVM`.

Créez le groupe de ressources. L’exemple suivant crée un groupe de ressources nommé `myResourceGroup` à l’emplacement `westeurope` :

```azurecli
azure group create -n myResourceGroup -l westeurope
```

Vérifiez le groupe de ressources à l’aide de l’analyseur JSON :

```azurecli
azure group show myResourceGroup --json | jq '.'
```

Créez le compte de stockage. L’exemple suivant permet de créer un compte de stockage nommé `mystorageaccount`. (Le nom du compte de stockage doit être unique, fournissez donc votre propre nom unique.)

```azurecli
azure storage account create -g myResourceGroup -l westeurope \
  --kind Storage --sku-name GRS mystorageaccount
```

Vérifiez le compte de stockage à l’aide de l’analyseur JSON :

```azurecli
azure storage account show -g myResourceGroup mystorageaccount --json | jq '.'
```

Création du réseau virtuel. L’exemple suivant crée un réseau virtuel nommé `myVnet` :

```azurecli
azure network vnet create -g myResourceGroup -l westeurope\
  -n myVnet -a 192.168.0.0/16
```

Créez un sous-réseau. L’exemple suivant permet de créer un sous-réseau nommé `mySubnet` :

```azurecli
azure network vnet subnet create -g myResourceGroup \
  -e myVnet -n mySubnet -a 192.168.1.0/24
```

Vérifiez le réseau virtuel et le sous-réseau à l’aide de l’analyseur JSON :

```azurecli
azure network vnet show myResourceGroup myVnet --json | jq '.'
```

Créez une adresse IP publique. L’exemple suivant permet de créer une adresse IP publique nommée `myPublicIP` avec le nom DNS de `mypublicdns`. (Le nom DNS doit être unique, fournissez donc votre propre nom unique.)

```azurecli
azure network public-ip create -g myResourceGroup -l westeurope \
  -n myPublicIP  -d mypublicdns -a static -i 4
```

Créez l’équilibreur de charge. L’exemple suivant permet de créer un équilibrage de charge nommé `myLoadBalancer` :

```azurecli
azure network lb create -g myResourceGroup -l westeurope -n myLoadBalancer
```

Créez un pool d’adresses IP frontal pour l’équilibrage de charge et associez-le à l’adresse IP publique. L’exemple suivant permet de créer un pool d’adresses IP frontal nommé `mySubnetPool` :

```azurecli
azure network lb frontend-ip create -g myResourceGroup -l myLoadBalancer \
  -i myPublicIP -n myFrontEndPool
```

Créez le pool d’adresses IP principal pour l’équilibrage de charge. L’exemple suivant permet de créer un pool d’adresses IP principal nommé `myBackEndPool` :

```azurecli
azure network lb address-pool create -g myResourceGroup -l myLoadBalancer \
  -n myBackEndPool
```

Créez les règles de traduction d’adresse réseau (NAT) de trafic entrant SSH pour l’équilibrage de charge. L’exemple suivant permet de créer deux règles d’équilibrage de charge, `myLoadBalancerRuleSSH1` et `myLoadBalancerRuleSSH2` :

```azurecli
azure network lb inbound-nat-rule create -g myResourceGroup -l myLoadBalancer \
  -n myLoadBalancerRuleSSH1 -p tcp -f 4222 -b 22
azure network lb inbound-nat-rule create -g myResourceGroup -l myLoadBalancer \
  -n myLoadBalancerRuleSSH2 -p tcp -f 4223 -b 22
```

Créez les règles NAT entrantes Web pour l’équilibrage de charge. L’exemple suivant permet de créer une règle d’équilibrage de charge nommée `myLoadBalancerRuleWeb` :

```azurecli
azure network lb rule create -g myResourceGroup -l myLoadBalancer \
  -n myLoadBalancerRuleWeb -p tcp -f 80 -b 80 \
  -t myFrontEndPool -o myBackEndPool
```

Créez la sonde d’intégrité d’équilibrage de charge. L’exemple suivant permet de créer une sonde TCP nommée `myHealthProbe` :

```azurecli
azure network lb probe create -g myResourceGroup -l myLoadBalancer \
  -n myHealthProbe -p "tcp" -i 15 -c 4
```

Vérifier l’équilibreur de charge, les pools d’adresses IP et les règles NAT à l’aide de l’analyseur JSON :

```azurecli
azure network lb show -g myResourceGroup -n myLoadBalancer --json | jq '.'
```

Créez la première carte d’interface réseau (NIC). Remplacez les sections `#####-###-###` par votre propre ID d’abonnement Azure. Votre ID d’abonnement est indiqué dans la sortie de **jq** lorsque vous examinez les ressources créées. Vous pouvez également afficher votre ID d’abonnement avec `azure account list`.

L’exemple suivant permet de créer une carte d’interface réseau nommée `myNic1` :

```azurecli
azure network nic create -g myResourceGroup -l westeurope \
  -n myNic1 -m myVnet -k mySubnet \
  -d "/subscriptions/########-####-####-####-############/resourceGroups/myResourceGroup/providers/Microsoft.Network/loadBalancers/myLoadBalancer/backendAddressPools/myBackEndPool" \
  -e "/subscriptions/########-####-####-####-############/resourceGroups/myResourceGroup/providers/Microsoft.Network/loadBalancers/myLoadBalancer/inboundNatRules/myLoadBalancerRuleSSH1"
```

Créez la deuxième carte d’interface réseau. L’exemple suivant permet de créer une carte d’interface réseau nommée `myNic2` :

```azurecli
azure network nic create -g myResourceGroup -l westeurope \
  -n myNic2 -m myVnet -k mySubnet \
  -d "/subscriptions/########-####-####-####-############/resourceGroups/myResourceGroup/providers/Microsoft.Network/loadBalancers/myLoadBalancer/backendAddressPools/myBackEndPool" \
  -e "/subscriptions/########-####-####-####-############/resourceGroups/myResourceGroup/providers/Microsoft.Network/loadBalancers/myLoadBalancer/inboundNatRules/myLoadBalancerRuleSSH2"
```

Vérifiez les deux cartes d’interface réseau à l’aide de l’analyseur JSON :

```azurecli
azure network nic show myResourceGroup myNic1 --json | jq '.'
azure network nic show myResourceGroup myNic2 --json | jq '.'
```

Créez le groupe de sécurité réseau. L’exemple suivant permet de créer un groupe de sécurité réseau nommé `myNetworkSecurityGroup` :

```azurecli
azure network nsg create -g myResourceGroup -l westeurope \
  -n myNetworkSecurityGroup
```

Ajoutez deux règles de trafic entrant pour le groupe de sécurité réseau. L’exemple suivant permet de créer deux règles, `myNetworkSecurityGroupRuleSSH` et `myNetworkSecurityGroupRuleHTTP` :

```azurecli
azure network nsg rule create -p tcp -r inbound -y 1000 -u 22 -c allow \
  -g myResourceGroup -a myNetworkSecurityGroup -n myNetworkSecurityGroupRuleSSH
azure network nsg rule create -p tcp -r inbound -y 1001 -u 80 -c allow \
  -g myResourceGroup -a myNetworkSecurityGroup -n myNetworkSecurityGroupRuleHTTP
```

Vérifiez le groupe de sécurité réseau et les règles de trafic entrant à l’aide de l’analyseur JSON :

```azurecli
azure network nsg show -g myResourceGroup -n myNetworkSecurityGroup --json | jq '.'
```

Liez le groupe de sécurité réseau aux deux cartes réseau :

```azurecli
azure network nic set -g myResourceGroup -o myNetworkSecurityGroup -n myNic1
azure network nic set -g myResourceGroup -o myNetworkSecurityGroup -n myNic2
```

Créez le groupe à haute disponibilité. L’exemple suivant permet de créer un groupe à haute disponibilité nommé `myAvailabilitySet` :

```azurecli
azure availset create -g myResourceGroup -l westeurope -n myAvailabilitySet
```

Créez la première machine virtuelle Linux. L’exemple suivant permet de créer une machine virtuelle nommée `myVM1` :

```azurecli
azure vm create \
    --resource-group myResourceGroup \
    --name myVM1 \
    --location westeurope \
    --os-type linux \
    --availset-name myAvailabilitySet \
    --nic-name myNic1 \
    --vnet-name myVnet \
    --vnet-subnet-name mySubnet \
    --storage-account-name mystorageaccount \
    --image-urn canonical:UbuntuServer:16.04.0-LTS:latest \
    --ssh-publickey-file ~/.ssh/id_rsa.pub \
    --admin-username azureuser
```

Créez la deuxième machine virtuelle Linux. L’exemple suivant permet de créer une machine virtuelle nommée `myVM2` :

```azurecli
azure vm create \
    --resource-group myResourceGroup \
    --name myVM2 \
    --location westeurope \
    --os-type linux \
    --availset-name myAvailabilitySet \
    --nic-name myNic2 \
    --vnet-name myVnet \
    --vnet-subnet-name mySubnet \
    --storage-account-name mystorageaccount \
    --image-urn canonical:UbuntuServer:16.04.0-LTS:latest \
    --ssh-publickey-file ~/.ssh/id_rsa.pub \
    --admin-username azureuser
```

Utilisez l’analyseur JSON pour vérifier tout ce qui a été créé :

```azurecli
azure vm show -g myResourceGroup -n myVM1 --json | jq '.'
azure vm show -g myResourceGroup -n myVM2 --json | jq '.'
```

Exportez votre nouvel environnement dans un modèle pour recréer rapidement des instances nouvelles :

```azurecli
azure group export myResourceGroup
```

## <a name="detailed-walkthrough"></a>Procédure pas à pas
Les étapes détaillées qui suivent expliquent ce que chaque commande fait lorsque vous générez votre environnement. Ces concepts sont utiles lorsque vous créez vos propres environnements personnalisés pour le développement ou la production.

Veillez à ce que [l’interface de ligne de commande 1.0 Azure](../../cli-install-nodejs.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json) soit connectée et utilise le mode Resource Manager :

```azurecli
azure config mode arm
```

Dans les exemples suivants, remplacez les exemples de noms de paramètre par vos propres valeurs. Exemples de noms de paramètre : `myResourceGroup`, `mystorageaccount` et `myVM`.

## <a name="create-resource-groups-and-choose-deployment-locations"></a>Créer des groupes de ressources et choisir les emplacements de déploiement
Les groupes de ressources Azure sont des entités de déploiement logiques qui contiennent des informations de configuration et des métadonnées pour permettre la gestion logique des déploiements de ressources. L’exemple suivant crée un groupe de ressources nommé `myResourceGroup` à l’emplacement `westeurope` :

```azurecli
azure group create --name myResourceGroup --location westeurope
```

Output:

```azurecli                        
info:    Executing command group create
+ Getting resource group myResourceGroup
+ Creating resource group myResourceGroup
info:    Created resource group myResourceGroup
data:    Id:                  /subscriptions/guid/resourceGroups/myResourceGroup
data:    Name:                myResourceGroup
data:    Location:            westeurope
data:    Provisioning State:  Succeeded
data:    Tags: null
data:
info:    group create command OK
```

## <a name="create-a-storage-account"></a>Créez un compte de stockage.
Vous avez besoin de comptes de stockage pour vos disques de machine virtuelle et pour tous les disques de données que vous souhaitez ajouter. Vous créez des comptes de stockage presque immédiatement après avoir créé des groupes de ressources.

Ici, nous utilisons la commande `azure storage account create` pour transmettre l’emplacement du compte, le groupe de ressources qui le contrôle, ainsi que le type de support de stockage souhaité. L’exemple qui suit permet de créer un compte de stockage nommé `mystorageaccount` :

```azurecli
azure storage account create \  
  --location westeurope \
  --resource-group myResourceGroup \
  --kind Storage --sku-name GRS \
  mystorageaccount
```

Output:

```azurecli
info:    Executing command storage account create
+ Creating storage account
info:    storage account create command OK
```

Pour examiner notre groupe de ressources à l’aide de la commande `azure group show`, nous nous servons de l’outil [jq](https://stedolan.github.io/jq/) avec l’option de ligne de commande Azure `--json`. (Vous pouvez utiliser **jsawk** ou la bibliothèque de langue de votre choix pour analyser le fichier JSON.)

```azurecli
azure group show myResourceGroup --json | jq '.'
```

Output:

```json
{
  "tags": {},
  "id": "/subscriptions/guid/resourceGroups/myResourceGroup",
  "name": "myResourceGroup",
  "provisioningState": "Succeeded",
  "location": "westeurope",
  "properties": {
    "provisioningState": "Succeeded"
  },
  "resources": [
    {
      "id": "/subscriptions/guid/resourceGroups/myResourceGroup/providers/Microsoft.Storage/storageAccounts/mystorageaccount",
      "name": "mystorageaccount",
      "type": "storageAccounts",
      "location": "westeurope",
      "tags": null
    }
  ],
  "permissions": [
    {
      "actions": [
        "*"
      ],
      "notActions": []
    }
  ]
}
```

Pour examiner le compte de stockage à l’aide de l’interface de ligne de commande, vous devez d’abord définir les noms de comptes et les clés. Remplacez le nom du compte de stockage dans l’exemple suivant par le nom de votre choix :

```bash
export AZURE_STORAGE_CONNECTION_STRING="$(azure storage account connectionstring show mystorageaccount --resource-group myResourceGroup --json | jq -r '.string')"
```

Vous pouvez alors visualiser facilement vos informations de stockage :

```azurecli
azure storage container list
```

Output:

```azurecli
info:    Executing command storage container list
+ Getting storage containers
data:    Name  Public-Access  Last-Modified
data:    ----  -------------  -----------------------------
data:    vhds  Off            Sun, 27 Sep 2015 19:03:54 GMT
info:    storage container list command OK
```

## <a name="create-a-virtual-network-and-subnet"></a>Créer un réseau virtuel et un sous-réseau
Vous allez maintenant créer un réseau virtuel dans Azure et un sous-réseau dans lesquels vous pouvez créer vos machines virtuelles. L’exemple suivant permet de créer un réseau virtuel nommé `myVnet` avec le préfixe d’adresse `192.168.0.0/16` :

```azurecli
azure network vnet create --resource-group myResourceGroup --location westeurope \
  --name myVnet --address-prefixes 192.168.0.0/16
```

Output:

```azurecli
info:    Executing command network vnet create
+ Looking up virtual network "myVnet"
+ Creating virtual network "myVnet"
+ Loading virtual network state
data:    Id                              : /subscriptions/guid/resourceGroups/myResourceGroup/providers/Microsoft.Network/virtualNetworks/myVnet
data:    Name                            : myVnet
data:    Type                            : Microsoft.Network/virtualNetworks
data:    Location                        : westeurope
data:    ProvisioningState               : Succeeded
data:    Address prefixes:
data:      192.168.0.0/16
info:    network vnet create command OK
```

Là encore, nous allons utiliser l’option --json de `azure group show` et de `jq` pour examiner la façon dont nous créons nos ressources. Nous disposons maintenant d’une ressource `storageAccounts` et d’une ressource `virtualNetworks`.  

```azurecli
azure group show myResourceGroup --json | jq '.'
```

Output:

```json
{
  "tags": {},
  "id": "/subscriptions/guid/resourceGroups/myResourceGroup",
  "name": "myResourceGroup",
  "provisioningState": "Succeeded",
  "location": "westeurope",
  "properties": {
    "provisioningState": "Succeeded"
  },
  "resources": [
    {
      "id": "/subscriptions/guid/resourceGroups/myResourceGroup/providers/Microsoft.Network/virtualNetworks/myVnet",
      "name": "myVnet",
      "type": "virtualNetworks",
      "location": "westeurope",
      "tags": null
    },
    {
      "id": "/subscriptions/guid/resourceGroups/myResourceGroup/providers/Microsoft.Storage/storageAccounts/mystorageaccount",
      "name": "mystorageaccount",
      "type": "storageAccounts",
      "location": "westeurope",
      "tags": null
    }
  ],
  "permissions": [
    {
      "actions": [
        "*"
      ],
      "notActions": []
    }
  ]
}
```

À présent, créons dans le réseau virtuel `myVnet` un sous-réseau dans lequel les machines virtuelles seront déployées. Nous utilisons la commande `azure network vnet subnet create` ainsi que les ressources que nous avons déjà créées : le groupe de ressources `myResourceGroup` et le réseau virtuel `myVnet`. Dans l’exemple suivant, nous ajoutons le sous-réseau nommé `mySubnet` avec le préfixe d’adresse de sous-réseau `192.168.1.0/24` :

```azurecli
azure network vnet subnet create --resource-group myResourceGroup \
  --vnet-name myVnet --name mySubnet --address-prefix 192.168.1.0/24
```

Output:

```azurecli
info:    Executing command network vnet subnet create
+ Looking up the subnet "mySubnet"
+ Creating subnet "mySubnet"
+ Looking up the subnet "mySubnet"
data:    Id                              : /subscriptions/guid/resourceGroups/myResourceGroup/providers/Microsoft.Network/virtualNetworks/myVnet/subnets/mySubnet
data:    Type                            : Microsoft.Network/virtualNetworks/subnets
data:    ProvisioningState               : Succeeded
data:    Name                            : mySubnet
data:    Address prefix                  : 192.168.1.0/24
data:
info:    network vnet subnet create command OK
```

Étant donné que le sous-réseau figure logiquement à l’intérieur du réseau virtuel, nous recherchons les informations de sous-réseau avec une commande légèrement différente. Nous utilisons la commande `azure network vnet show`, mais nous continuons à examiner la sortie JSON à l’aide de `jq`.

```azurecli
azure network vnet show myResourceGroup myVnet --json | jq '.'
```

Output:

```json
{
  "subnets": [
    {
      "ipConfigurations": [],
      "addressPrefix": "192.168.1.0/24",
      "provisioningState": "Succeeded",
      "name": "mySubnet",
      "etag": "W/\"974f3e2c-028e-4b35-832b-a4b16ad25eb6\"",
      "id": "/subscriptions/guid/resourceGroups/myResourceGroup/providers/Microsoft.Network/virtualNetworks/myVnet/subnets/mySubnet"
    }
  ],
  "tags": {},
  "addressSpace": {
    "addressPrefixes": [
      "192.168.0.0/16"
    ]
  },
  "dhcpOptions": {
    "dnsServers": []
  },
  "provisioningState": "Succeeded",
  "etag": "W/\"974f3e2c-028e-4b35-832b-a4b16ad25eb6\"",
  "id": "/subscriptions/guid/resourceGroups/myResourceGroup/providers/Microsoft.Network/virtualNetworks/myVnet",
  "name": "myVnet",
  "location": "westeurope"
}
```

## <a name="create-a-public-ip-address"></a>Créer une adresse IP publique
Créons à présent l’adresse IP publique (PIP) à attribuer à votre équilibreur de charge. Elle permet de vous connecter à vos machines virtuelles depuis Internet à l’aide de la commande `azure network public-ip create` . Étant donné que l’adresse par défaut est dynamique, nous créons une entrée DNS nommée dans le domaine **cloudapp.azure.com** à l’aide de l’option `--domain-name-label`. L’exemple suivant permet de créer une adresse IP publique nommée `myPublicIP` avec le nom DNS de `mypublicdns`. Étant donné que le nom DNS doit être unique, choisissez un autre nom DNS qui n’est pas susceptible d’être déjà utilisé :

```azurecli
azure network public-ip create --resource-group myResourceGroup \
  --location westeurope --name myPublicIP --domain-name-label mypublicdns
```

Output:

```azurecli
info:    Executing command network public-ip create
+ Looking up the public ip "myPublicIP"
+ Creating public ip address "myPublicIP"
+ Looking up the public ip "myPublicIP"
data:    Id                              : /subscriptions/guid/resourceGroups/myResourceGroup/providers/Microsoft.Network/publicIPAddresses/myPublicIP
data:    Name                            : myPublicIP
data:    Type                            : Microsoft.Network/publicIPAddresses
data:    Location                        : westeurope
data:    Provisioning state              : Succeeded
data:    Allocation method               : Dynamic
data:    Idle timeout                    : 4
data:    Domain name label               : mypublicdns
data:    FQDN                            : mypublicdns.westeurope.cloudapp.azure.com
info:    network public-ip create command OK
```

L’adresse IP publique étant également une ressource de niveau supérieur, vous pouvez la visualiser avec `azure group show`.

```azurecli
azure group show myResourceGroup --json | jq '.'
```

Output:

```json
{
"tags": {},
"id": "/subscriptions/guid/resourceGroups/myResourceGroup",
"name": "myResourceGroup",
"provisioningState": "Succeeded",
"location": "westeurope",
"properties": {
    "provisioningState": "Succeeded"
},
"resources": [
    {
    "id": "/subscriptions/guid/resourceGroups/myResourceGroup/providers/Microsoft.Network/publicIPAddresses/myPublicIP",
    "name": "myPublicIP",
    "type": "publicIPAddresses",
    "location": "westeurope",
    "tags": null
    },
    {
    "id": "/subscriptions/guid/resourceGroups/myResourceGroup/providers/Microsoft.Network/virtualNetworks/myVnet",
    "name": "myVnet",
    "type": "virtualNetworks",
    "location": "westeurope",
    "tags": null
    },
    {
    "id": "/subscriptions/guid/resourceGroups/myResourceGroup/providers/Microsoft.Storage/storageAccounts/mystorageaccount",
    "name": "mystorageaccount",
    "type": "storageAccounts",
    "location": "westeurope",
    "tags": null
    }
],
"permissions": [
    {
    "actions": [
        "*"
    ],
    "notActions": []
    }
]
}
```

Vous pouvez examiner d’autres détails de la ressource, dont le nom de domaine complet (FQDN) du sous-domaine, en utilisant la commande `azure network public-ip show` complète. La ressource d’adresse IP publique a été allouée de façon logique, mais aucune adresse spécifique n’a encore été attribuée. Pour obtenir une adresse IP, vous aurez besoin d’un équilibreur de charge que nous n’avons pas encore créé.

```azurecli
azure network public-ip show myResourceGroup myPublicIP --json | jq '.'
```

Output:

```json
{
"tags": {},
"publicIpAllocationMethod": "Dynamic",
"dnsSettings": {
    "domainNameLabel": "mypublicdns",
    "fqdn": "mypublicdns.westeurope.cloudapp.azure.com"
},
"idleTimeoutInMinutes": 4,
"provisioningState": "Succeeded",
"etag": "W/\"c63154b3-1130-49b9-a887-877d74d5ebc5\"",
"id": "/subscriptions/guid/resourceGroups/myResourceGroup/providers/Microsoft.Network/publicIPAddresses/myPublicIP",
"name": "myPublicIP",
"location": "westeurope"
}
```

## <a name="create-a-load-balancer-and-ip-pools"></a>Créer un équilibreur de charge et des pools d’adresses IP
Lorsque vous créez un équilibreur de charge, celui-ci vous permet de répartir le trafic entre plusieurs machines virtuelles. Cela assure également la redondance de votre application en exécutant plusieurs machines virtuelles qui répondent aux demandes des utilisateurs en cas de maintenance ou de lourdes charges. L’exemple suivant permet de créer un équilibrage de charge nommé `myLoadBalancer` :

```azurecli
azure network lb create --resource-group myResourceGroup --location westeurope \
  --name myLoadBalancer
```

Output:

```azurecli
info:    Executing command network lb create
+ Looking up the load balancer "myLoadBalancer"
+ Creating load balancer "myLoadBalancer"
data:    Id                              : /subscriptions/guid/resourceGroups/myResourceGroup/providers/Microsoft.Network/loadBalancers/myLoadBalancer
data:    Name                            : myLoadBalancer
data:    Type                            : Microsoft.Network/loadBalancers
data:    Location                        : westeurope
data:    Provisioning state              : Succeeded
info:    network lb create command OK
```

Notre équilibreur de charge est relativement vide. Nous allons donc créer des pools d’adresses IP. Nous souhaitons créer deux pools d’adresses IP pour notre équilibreur de charge : une pour le pool frontal et une pour le pool principal. Le pool d’adresses IP frontal est visible publiquement. Il s’agit également de l’emplacement où nous attribuons le PIP que nous avons créé précédemment. Nous utilisons ensuite le pool principal comme emplacement auquel connecter nos machines virtuelles. Ainsi, le trafic peut transiter via l’équilibreur de charge vers les machines virtuelles.

Commençons tout d’abord par créer notre pool d’adresses IP frontal. L’exemple suivant permet de créer un pool frontal nommé `myFrontEndPool` :

```azurecli
azure network lb frontend-ip create --resource-group myResourceGroup \
  --lb-name myLoadBalancer --public-ip-name myPublicIP \
  --name myFrontEndPool
```

Output:

```azurecli
info:    Executing command network lb frontend-ip create
+ Looking up the load balancer "myLoadBalancer"
+ Looking up the public ip "myPublicIP"
+ Updating load balancer "myLoadBalancer"
data:    Name                            : myFrontEndPool
data:    Provisioning state              : Succeeded
data:    Private IP allocation method    : Dynamic
data:    Public IP address id            : /subscriptions/guid/resourceGroups/myResourceGroup/providers/Microsoft.Network/publicIPAddresses/myPublicIP
info:    network lb mySubnet-ip create command OK
```

Notez comment nous avons utilisé le commutateur `--public-ip-name` pour transmettre le `myPublicIP` créé précédemment. L’attribution de l’adresse IP publique à l’équilibreur de charge vous permet d’atteindre vos machines virtuelles via Internet.

Nous allons maintenant créer notre deuxième pool d’adresses IP pour le trafic principal. L’exemple suivant permet de créer un pool principal nommé `myBackEndPool` :

```azurecli
azure network lb address-pool create --resource-group myResourceGroup \
  --lb-name myLoadBalancer --name myBackEndPool
```

Output:

```azurecli
info:    Executing command network lb address-pool create
+ Looking up the load balancer "myLoadBalancer"
+ Updating load balancer "myLoadBalancer"
data:    Name                            : myBackEndPool
data:    Provisioning state              : Succeeded
info:    network lb address-pool create command OK
```

Nous pouvons vérifier la façon dont notre équilibreur de charge apparaît avec `azure network lb show` et en examinant la sortie JSON :

```azurecli
azure network lb show myResourceGroup myLoadBalancer --json | jq '.'
```

Output:

```json
{
  "etag": "W/\"29c38649-77d6-43ff-ab8f-977536b0047c\"",
  "provisioningState": "Succeeded",
  "resourceGuid": "f1446acb-09ba-44d9-b8b6-849d9983dc09",
  "outboundNatRules": [],
  "inboundNatPools": [],
  "inboundNatRules": [],
  "id": "/subscriptions/guid/resourceGroups/myResourceGroup/providers/Microsoft.Network/loadBalancers/myLoadBalancer",
  "name": "myLoadBalancer",
  "type": "Microsoft.Network/loadBalancers",
  "location": "westeurope",
  "mySubnetIPConfigurations": [
    {
      "etag": "W/\"29c38649-77d6-43ff-ab8f-977536b0047c\"",
      "name": "myFrontEndPool",
      "provisioningState": "Succeeded",
      "publicIPAddress": {
        "id": "/subscriptions/guid/resourceGroups/myResourceGroup/providers/Microsoft.Network/publicIPAddresses/myPublicIP"
      },
      "privateIPAllocationMethod": "Dynamic",
      "id": "/subscriptions/guid/resourceGroups/myResourceGroup/providers/Microsoft.Network/loadBalancers/myLoadBalancer/mySubnetIPConfigurations/myFrontEndPool"
    }
  ],
  "backendAddressPools": [
    {
      "etag": "W/\"29c38649-77d6-43ff-ab8f-977536b0047c\"",
      "name": "myBackEndPool",
      "provisioningState": "Succeeded",
      "id": "/subscriptions/guid/resourceGroups/myResourceGroup/providers/Microsoft.Network/loadBalancers/myLoadBalancer/backendAddressPools/myBackEndPool"
    }
  ],
  "loadBalancingRules": [],
  "probes": []
}
```

## <a name="create-load-balancer-nat-rules"></a>Créer des règles NAT d’équilibreur de charge
Pour que le trafic transite par notre équilibrage de charge, nous devons créer des règles de traduction d’adresse réseau (NAT) spécifiant les actions entrantes ou sortantes. Vous pouvez spécifier le protocole utilisé, puis mapper les ports externes aux ports internes comme vous le souhaitez. Pour notre environnement, nous allons créer des règles autorisant SSH à accéder à nos machines virtuelles par le biais de notre équilibreur de charge. Nous configurons les ports TCP 4222 et 4223 de manière à orienter le trafic vers le port TCP 22 sur nos machines virtuelles (que nous créerons plus tard). L’exemple suivant crée une règle nommée `myLoadBalancerRuleSSH1` pour mapper le port TCP 4222 sur le port 22 :

```azurecli
azure network lb inbound-nat-rule create --resource-group myResourceGroup \
  --lb-name myLoadBalancer --name myLoadBalancerRuleSSH1 \
  --protocol tcp --frontend-port 4222 --backend-port 22
```

Output:

```azurecli
info:    Executing command network lb inbound-nat-rule create
+ Looking up the load balancer "myLoadBalancer"
warn:    Using default enable floating ip: false
warn:    Using default idle timeout: 4
warn:    Using default mySubnet IP configuration "myFrontEndPool"
+ Updating load balancer "myLoadBalancer"
data:    Name                            : myLoadBalancerRuleSSH1
data:    Provisioning state              : Succeeded
data:    Protocol                        : Tcp
data:    mySubnet port                   : 4222
data:    Backend port                    : 22
data:    Enable floating IP              : false
data:    Idle timeout in minutes         : 4
data:    mySubnet IP configuration id    : /subscriptions/guid/resourceGroups/myResourceGroup/providers/Microsoft.Network/loadBalancers/myLoadBalancer/mySubnetIPConfigurations/myFrontEndPool
info:    network lb inbound-nat-rule create command OK
```

Répétez cette procédure pour votre deuxième règle NAT pour le protocole SSH. L’exemple suivant crée une règle nommée `myLoadBalancerRuleSSH2` pour mapper le port TCP 4223 sur le port 22 :

```azurecli
azure network lb inbound-nat-rule create --resource-group myResourceGroup \
  --lb-name myLoadBalancer --name myLoadBalancerRuleSSH2 --protocol tcp \
  --frontend-port 4223 --backend-port 22
```

Nous allons maintenant créer une règle NAT pour le port TCP 80 du trafic web , en liant la règle à nos pools d’adresses IP. Si nous lions l’adresse à un pool d’adresses IP au lieu de la lier à nos machines virtuelles individuellement, nous pouvons ajouter ou supprimer des machines virtuelles dans le pool d’adresses IP. L’équilibrage de charge ajuste alors automatiquement le flux de trafic. L’exemple suivant crée une règle nommée `myLoadBalancerRuleWeb` pour mapper le port TCP 80 sur le port 80 :

```azurecli
azure network lb rule create --resource-group myResourceGroup \
  --lb-name myLoadBalancer --name myLoadBalancerRuleWeb --protocol tcp \
  --frontend-port 80 --backend-port 80 --frontend-ip-name myFrontEndPool \
  --backend-address-pool-name myBackEndPool
```

Output:

```azurecli
info:    Executing command network lb rule create
+ Looking up the load balancer "myLoadBalancer"
warn:    Using default idle timeout: 4
warn:    Using default enable floating ip: false
warn:    Using default load distribution: Default
+ Updating load balancer "myLoadBalancer"
data:    Name                            : myLoadBalancerRuleWeb
data:    Provisioning state              : Succeeded
data:    Protocol                        : Tcp
data:    mySubnet port                   : 80
data:    Backend port                    : 80
data:    Enable floating IP              : false
data:    Load distribution               : Default
data:    Idle timeout in minutes         : 4
data:    mySubnet IP configuration id    : /subscriptions/guid/resourceGroups/myResourceGroup/providers/Microsoft.Network/loadBalancers/myLoadBalancer/mySubnetIPConfigurations/myFrontEndPool
data:    Backend address pool id         : /subscriptions/guid/resourceGroups/myResourceGroup/providers/Microsoft.Network/loadBalancers/myLoadBalancer/backendAddressPools/myBackEndPool
info:    network lb rule create command OK
```

## <a name="create-a-load-balancer-health-probe"></a>Créer une sonde d’intégrité d’équilibreur de charge
Une sonde d’intégrité contrôle régulièrement les machines virtuelles situées derrière notre équilibreur de charge pour s’assurer qu’elles fonctionnent correctement et répondent aux demandes, telles que définies. Dans le cas contraire, elles sont supprimées du processus pour garantir que les utilisateurs ne sont pas orientés vers elles. Vous pouvez définir les contrôles personnalisés de la sonde d’intégrité, ainsi que des valeurs d’intervalle et de délai d’attente. Pour plus d'informations sur les sondes d’intégrité, consultez [Sondes d’équilibreur de charge](../../load-balancer/load-balancer-custom-probe-overview.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json). L’exemple suivant permet de créer une sonde d’intégrité TCP nommée `myHealthProbe` :

```azurecli
azure network lb probe create --resource-group myResourceGroup \
  --lb-name myLoadBalancer --name myHealthProbe --protocol "tcp" \
  --interval 15 --count 4
```

Output:

```azurecli
info:    Executing command network lb probe create
warn:    Using default probe port: 80
+ Looking up the load balancer "myLoadBalancer"
+ Updating load balancer "myLoadBalancer"
data:    Name                            : myHealthProbe
data:    Provisioning state              : Succeeded
data:    Protocol                        : Tcp
data:    Port                            : 80
data:    Interval in seconds             : 15
data:    Number of probes                : 4
info:    network lb probe create command OK
```

Ici, nous avons spécifié un intervalle de 15 secondes pour nos contrôles d’intégrité. Nous pouvons manquer au maximum quatre sondes (une minute) avant que l’équilibreur de charge considère que l’hôte ne fonctionne plus.

## <a name="verify-the-load-balancer"></a>Vérifier l’équilibreur de charge
Vérifiez maintenant que la configuration de l’équilibreur de charge est terminée. Voici la procédure que vous avez suivie :

1. Vous avez créé un équilibrage de charge.
2. Vous avez créé un pool d’adresses IP frontal et lui avez affecté une adresse IP publique.
3. Vous avez créé un pool d’adresses IP principal auquel les machines virtuelles peuvent se connecter.
4. Vous avez créé des règles NAT qui autorisent le protocole SSH pour les machines virtuelles à des fins de gestion, ainsi qu’une règle autorisant le port TCP 80 de notre application web.
5. Vous avez ajouté une sonde d’intégrité pour contrôler périodiquement les machines virtuelles. Cette sonde d’intégrité s’assure que des utilisateurs n’essaient d’accéder à une machine virtuelle qui ne fonctionne plus ou qui ne sert plus de contenu.

Regardons maintenant à quoi ressemble votre équilibreur de charge :

```azurecli
azure network lb show --resource-group myResourceGroup \
  --name myLoadBalancer --json | jq '.'
```

Output:

```json
{
  "etag": "W/\"62a7c8e7-859c-48d3-8e76-5e078c5e4a02\"",
  "provisioningState": "Succeeded",
  "resourceGuid": "f1446acb-09ba-44d9-b8b6-849d9983dc09",
  "outboundNatRules": [],
  "inboundNatPools": [],
  "inboundNatRules": [
    {
      "etag": "W/\"62a7c8e7-859c-48d3-8e76-5e078c5e4a02\"",
      "name": "myLoadBalancerRuleSSH1",
      "id": "/subscriptions/guid/resourceGroups/myResourceGroup/providers/Microsoft.Network/loadBalancers/myLoadBalancer/inboundNatRules/myLoadBalancerRuleSSH1",
      "mySubnetIPConfiguration": {
        "id": "/subscriptions/guid/resourceGroups/myResourceGroup/providers/Microsoft.Network/loadBalancers/myLoadBalancer/mySubnetIPConfigurations/myFrontEndPool"
      },
      "protocol": "Tcp",
      "mySubnetPort": 4222,
      "backendPort": 22,
      "idleTimeoutInMinutes": 4,
      "enableFloatingIP": false,
      "provisioningState": "Succeeded"
    },
    {
      "etag": "W/\"62a7c8e7-859c-48d3-8e76-5e078c5e4a02\"",
      "name": "myLoadBalancerRuleSSH2",
      "id": "/subscriptions/guid/resourceGroups/myResourceGroup/providers/Microsoft.Network/loadBalancers/myLoadBalancer/inboundNatRules/myLoadBalancerRuleSSH2",
      "mySubnetIPConfiguration": {
        "id": "/subscriptions/guid/resourceGroups/myResourceGroup/providers/Microsoft.Network/loadBalancers/myLoadBalancer/mySubnetIPConfigurations/myFrontEndPool"
      },
      "protocol": "Tcp",
      "mySubnetPort": 4223,
      "backendPort": 22,
      "idleTimeoutInMinutes": 4,
      "enableFloatingIP": false,
      "provisioningState": "Succeeded"
    }
  ],
  "id": "/subscriptions/guid/resourceGroups/myResourceGroup/providers/Microsoft.Network/loadBalancers/myLoadBalancer",
  "name": "myLoadBalancer",
  "type": "Microsoft.Network/loadBalancers",
  "location": "westeurope",
  "mySubnetIPConfigurations": [
    {
      "etag": "W/\"62a7c8e7-859c-48d3-8e76-5e078c5e4a02\"",
      "name": "myFrontEndPool",
      "provisioningState": "Succeeded",
      "publicIPAddress": {
        "id": "/subscriptions/guid/resourceGroups/myResourceGroup/providers/Microsoft.Network/publicIPAddresses/myPublicIP"
      },
      "privateIPAllocationMethod": "Dynamic",
      "loadBalancingRules": [
        {
          "id": "/subscriptions/guid/resourceGroups/myResourceGroup/providers/Microsoft.Network/loadBalancers/myLoadBalancer/loadBalancingRules/myLoadBalancerRuleWeb"
        }
      ],
      "inboundNatRules": [
        {
          "id": "/subscriptions/guid/resourceGroups/myResourceGroup/providers/Microsoft.Network/loadBalancers/myLoadBalancer/inboundNatRules/myLoadBalancerRuleSSH1"
        },
        {
          "id": "/subscriptions/guid/resourceGroups/myResourceGroup/providers/Microsoft.Network/loadBalancers/myLoadBalancer/inboundNatRules/myLoadBalancerRuleSSH2"
        }
      ],
      "id": "/subscriptions/guid/resourceGroups/myResourceGroup/providers/Microsoft.Network/loadBalancers/myLoadBalancer/mySubnetIPConfigurations/myFrontEndPool"
    }
  ],
  "backendAddressPools": [
    {
      "etag": "W/\"62a7c8e7-859c-48d3-8e76-5e078c5e4a02\"",
      "name": "myBackEndPool",
      "provisioningState": "Succeeded",
      "loadBalancingRules": [
        {
          "id": "/subscriptions/guid/resourceGroups/myResourceGroup/providers/Microsoft.Network/loadBalancers/myLoadBalancer/loadBalancingRules/myLoadBalancerRuleWeb"
        }
      ],
      "id": "/subscriptions/guid/resourceGroups/myResourceGroup/providers/Microsoft.Network/loadBalancers/myLoadBalancer/backendAddressPools/myBackEndPool"
    }
  ],
  "loadBalancingRules": [
    {
      "etag": "W/\"62a7c8e7-859c-48d3-8e76-5e078c5e4a02\"",
      "name": "myLoadBalancerRuleWeb",
      "provisioningState": "Succeeded",
      "enableFloatingIP": false,
      "id": "/subscriptions/guid/resourceGroups/myResourceGroup/providers/Microsoft.Network/loadBalancers/myLoadBalancer/loadBalancingRules/myLoadBalancerRuleWeb",
      "mySubnetIPConfiguration": {
        "id": "/subscriptions/guid/resourceGroups/myResourceGroup/providers/Microsoft.Network/loadBalancers/myLoadBalancer/mySubnetIPConfigurations/myFrontEndPool"
      },
      "backendAddressPool": {
        "id": "/subscriptions/guid/resourceGroups/myResourceGroup/providers/Microsoft.Network/loadBalancers/myLoadBalancer/backendAddressPools/myBackEndPool"
      },
      "protocol": "Tcp",
      "loadDistribution": "Default",
      "mySubnetPort": 80,
      "backendPort": 80,
      "idleTimeoutInMinutes": 4
    }
  ],
  "probes": [
    {
      "etag": "W/\"62a7c8e7-859c-48d3-8e76-5e078c5e4a02\"",
      "name": "myHealthProbe",
      "provisioningState": "Succeeded",
      "numberOfProbes": 4,
      "intervalInSeconds": 15,
      "port": 80,
      "protocol": "Tcp",
      "id": "/subscriptions/guid/resourceGroups/myResourceGroup/providers/Microsoft.Network/loadBalancers/myLoadBalancer/probes/myHealthProbe"
    }
  ]
}
```

## <a name="create-an-nic-to-use-with-the-linux-vm"></a>Créer une carte d’interface réseau (NIC) à utiliser avec la machine virtuelle Linux
La disponibilité des cartes d’interface réseau peut être déterminée par programme car vous pouvez définir des règles régissant leur utilisation. Vous pouvez également avoir plusieurs cartes. Dans la commande `azure network nic create` suivante, vous avez lié la carte d’interface réseau au pool d’adresses IP principal de charge, et l’avez associée à la règle NAT pour autoriser le trafic SSH.

Remplacez les sections `#####-###-###` par votre propre ID d’abonnement Azure. Votre ID d’abonnement est indiqué dans la sortie de `jq` lorsque vous examinez les ressources créées. Vous pouvez également afficher votre ID d’abonnement avec `azure account list`.

L’exemple suivant permet de créer une carte d’interface réseau nommée `myNic1` :

```azurecli
azure network nic create --resource-group myResourceGroup --location westeurope \
  --subnet-vnet-name myVnet --subnet-name mySubnet --name myNic1 \
  --lb-address-pool-ids /subscriptions/########-####-####-####-############/resourceGroups/myResourceGroup/providers/Microsoft.Network/loadBalancers/myLoadBalancer/backendAddressPools/myBackEndPool \
  --lb-inbound-nat-rule-ids /subscriptions/########-####-####-####-############/resourceGroups/myResourceGroup/providers/Microsoft.Network/loadBalancers/myLoadBalancer/inboundNatRules/myLoadBalancerRuleSSH1
```

Output:

```azurecli
info:    Executing command network nic create
+ Looking up the subnet "mySubnet"
+ Looking up the network interface "myNic1"
+ Creating network interface "myNic1"
data:    Id                              : /subscriptions/guid/resourceGroups/myResourceGroup/providers/Microsoft.Network/networkInterfaces/myNic1
data:    Name                            : myNic1
data:    Type                            : Microsoft.Network/networkInterfaces
data:    Location                        : westeurope
data:    Provisioning state              : Succeeded
data:    Enable IP forwarding            : false
data:    IP configurations:
data:      Name                          : Nic-IP-config
data:      Provisioning state            : Succeeded
data:      Private IP address            : 192.168.1.4
data:      Private IP allocation method  : Dynamic
data:      Subnet                        : /subscriptions/guid/resourceGroups/myResourceGroup/providers/Microsoft.Network/virtualNetworks/myVnet/subnets/mySubnet
data:      Load balancer backend address pools:
data:        Id                          : /subscriptions/guid/resourceGroups/myResourceGroup/providers/Microsoft.Network/loadBalancers/myLoadBalancer/backendAddressPools/myBackEndPool
data:      Load balancer inbound NAT rules:
data:        Id                          : /subscriptions/guid/resourceGroups/myResourceGroup/providers/Microsoft.Network/loadBalancers/myLoadBalancer/inboundNatRules/myLoadBalancerRuleSSH1
data:
info:    network nic create command OK
```

Vous pouvez visualiser les détails de la ressource en examinant directement cette dernière. Vous examinez la ressource à l’aide de la commande `azure network nic show` :

```azurecli
azure network nic show myResourceGroup myNic1 --json | jq '.'
```

Output:

```json
{
  "etag": "W/\"fc1eaaa1-ee55-45bd-b847-5a08c7f4264a\"",
  "provisioningState": "Succeeded",
  "id": "/subscriptions/guid/resourceGroups/myResourceGroup/providers/Microsoft.Network/networkInterfaces/myNic1",
  "name": "myNic1",
  "type": "Microsoft.Network/networkInterfaces",
  "location": "westeurope",
  "ipConfigurations": [
    {
      "etag": "W/\"fc1eaaa1-ee55-45bd-b847-5a08c7f4264a\"",
      "id": "/subscriptions/guid/resourceGroups/myResourceGroup/providers/Microsoft.Network/networkInterfaces/myNic1/ipConfigurations/Nic-IP-config",
      "loadBalancerBackendAddressPools": [
        {
          "id": "/subscriptions/guid/resourceGroups/myResourceGroup/providers/Microsoft.Network/loadBalancers/myLoadBalancer/backendAddressPools/myBackEndPool"
        }
      ],
      "loadBalancerInboundNatRules": [
        {
          "id": "/subscriptions/guid/resourceGroups/myResourceGroup/providers/Microsoft.Network/loadBalancers/myLoadBalancer/inboundNatRules/myLoadBalancerRuleSSH1"
        }
      ],
      "privateIPAddress": "192.168.1.4",
      "privateIPAllocationMethod": "Dynamic",
      "subnet": {
        "id": "/subscriptions/guid/resourceGroups/myResourceGroup/providers/Microsoft.Network/virtualNetworks/myVnet/subnets/mySubnet"
      },
      "provisioningState": "Succeeded",
      "name": "Nic-IP-config"
    }
  ],
  "dnsSettings": {
    "appliedDnsServers": [],
    "dnsServers": []
  },
  "enableIPForwarding": false,
  "resourceGuid": "a20258b8-6361-45f6-b1b4-27ffed28798c"
}
```

Nous créons maintenant la deuxième carte réseau en la liant à nouveau à notre pool d’adresses IP principal. Cette fois, la deuxième règle NAT autorise le trafic SSH. L’exemple suivant permet de créer une carte d’interface réseau nommée `myNic2` :

```azurecli
azure network nic create --resource-group myResourceGroup --location westeurope \
  --subnet-vnet-name myVnet --subnet-name mySubnet --name myNic2 \
  --lb-address-pool-ids  /subscriptions/########-####-####-####-############/resourceGroups/myResourceGroup/providers/Microsoft.Network/loadBalancers/myLoadBalancer/backendAddressPools/myBackEndPool \
  --lb-inbound-nat-rule-ids /subscriptions/########-####-####-####-############/resourceGroups/myResourceGroup/providers/Microsoft.Network/loadBalancers/myLoadBalancer/inboundNatRules/myLoadBalancerRuleSSH2
```

## <a name="create-a-network-security-group-and-rules"></a>Créer un groupe de sécurité réseau et les règles associées
Nous allons à présent créer un groupe de sécurité réseau et les règles de trafic entrant qui régissent l’accès à la carte d’interface réseau. Un groupe de sécurité réseau peut être appliqué à une carte d’interface réseau ou à un sous-réseau. Vous définissez des règles pour contrôler le flux de trafic vers et à partir de vos machines virtuelles. L’exemple suivant permet de créer un groupe de sécurité réseau nommé `myNetworkSecurityGroup` :

```azurecli
azure network nsg create --resource-group myResourceGroup --location westeurope \
  --name myNetworkSecurityGroup
```

Ajoutons la règle de trafic entrant pour le groupe de sécurité réseau afin d’autoriser les connexions entrantes sur le port 22 (pour prendre en charge le protocole SSH). L’exemple suivant permet de créer une règle nommée `myNetworkSecurityGroupRuleSSH` pour autoriser le trafic TCP sur le port 22 :

```azurecli
azure network nsg rule create --resource-group myResourceGroup \
  --nsg-name myNetworkSecurityGroup --protocol tcp --direction inbound \
  --priority 1000 --destination-port-range 22 --access allow \
  --name myNetworkSecurityGroupRuleSSH
```

À présent, nous ajoutons la règle de trafic entrant pour le groupe de sécurité réseau afin d’autoriser les connexions entrantes sur le port 80 (pour prendre en charge le trafic web). L’exemple suivant permet de créer une règle nommée `myNetworkSecurityGroupRuleHTTP` pour autoriser le trafic TCP sur le port 80 :

```azurecli
azure network nsg rule create --resource-group myResourceGroup \
  --nsg-name myNetworkSecurityGroup --protocol tcp --direction inbound \
  --priority 1001 --destination-port-range 80 --access allow \
  --name myNetworkSecurityGroupRuleHTTP
```

> [!NOTE]
> La règle de trafic entrant est un filtre pour les connexions réseau entrantes. Dans cet exemple, nous lions le NSG à la carte d’interface réseau (NIC) virtuelle des machines virtuelles, ce qui signifie que toute demande adressée au port 22 est transmise à la carte d’interface réseau sur notre machine virtuelle. Cette règle de trafic entrant concerne une connexion réseau, et non à un point de terminaison, comme dans le cas de déploiements classiques. Pour ouvrir un port, vous devez laisser la commande `--source-port-range` définie sur « \* » (valeur par défaut) afin d’accepter les demandes entrantes de **tous** les ports demandeurs. Ces ports sont généralement dynamiques.
>
>

## <a name="bind-to-the-nic"></a>Liaison avec la carte d’interface réseau
Liez le groupe de sécurité réseau aux cartes d’interface réseau. Vous devez connecter les cartes d’interface réseau avec le groupe de sécurité réseau. Exécutez les deux commandes, pour connecter les deux cartes d’interface réseau :

```azurecli
azure network nic set --resource-group myResourceGroup --name myNic1 \
  --network-security-group-name myNetworkSecurityGroup
```

```azurecli
azure network nic set --resource-group myResourceGroup --name myNic2 \
  --network-security-group-name myNetworkSecurityGroup
```

## <a name="create-an-availability-set"></a>Créer un groupe à haute disponibilité
Les groupes à haute disponibilité aident à diffuser vos machines virtuelles sur des domaines d’erreur et des domaines de mise à niveau. Nous allons maintenant voir comment créer un groupe à haute disponibilité pour vos machines virtuelles. L’exemple suivant permet de créer un groupe à haute disponibilité nommé `myAvailabilitySet` :

```azurecli
azure availset create --resource-group myResourceGroup --location westeurope
  --name myAvailabilitySet
```

Les domaines d’erreur désignent un groupe de machines virtuelles partageant une source d’alimentation et un commutateur réseau communs. Par défaut, les machines virtuelles configurées dans votre groupe à haute disponibilité sont réparties entre trois domaines de défaillance au maximum. Ainsi, en cas de problème matériel dans l’un de ces domaines d’erreur, aucune machine virtuelle exécutant votre application n’est affectée. Azure distribue automatiquement les machines virtuelles sur les domaines d’erreur lorsque vous les placez dans un groupe à haute disponibilité.

Les domaines de mise à niveau indiquent les groupes de machines virtuelles et les équipements physiques sous-jacents pouvant être redémarrés en même temps. L’ordre de redémarrage des domaines de mise à jour peut ne pas être séquentiel au cours de la maintenance planifiée, mais les mises à niveau sont redémarrées une par une. Cette fois encore, Azure distribue automatiquement vos machines virtuelles entre les domaines de mise à niveau lorsque vous les placez dans un site de disponibilité.

Vous pouvez obtenir des informations supplémentaires sur [la gestion de la disponibilité des machines virtuelles](manage-availability.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json).

## <a name="create-the-linux-vms"></a>Créer les machines virtuelles Linux
Vous avez créé les ressources de stockage et de réseau pour prendre en charge des machines virtuelles accessibles par Internet. Créons maintenant ces machines virtuelles et sécurisons-les avec une clé SSH sans mot de passe. Dans ce cas précis, nous allons créer une machine virtuelle Ubuntu basée sur la dernière version LTS. Nous recherchons les informations de cette image en utilisant `azure vm image list`, comme décrit dans [Recherche d’images de machine virtuelle Azure](../windows/cli-ps-findimage.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json).

Nous avons sélectionné une image à l’aide de la commande `azure vm image list westeurope canonical | grep LTS`. Dans ce cas, nous utilisons `canonical:UbuntuServer:16.04.0-LTS:16.04.201608150`. Pour le dernier champ, nous passons `latest` afin de toujours obtenir la build la plus récente. (La chaîne que nous utilisons est `canonical:UbuntuServer:16.04.0-LTS:16.04.201608150`).

Cette étape est familière à toute personne ayant déjà créé une paire de clés publique et privée SSH RSA sur Linux ou Mac à l’aide de la commande **ssh-keygen -t rsa -b 2048**. Si vous ne disposez pas de toutes les paires de clés de certificat dans votre répertoire `~/.ssh` , vous pouvez les créer ainsi :

* Automatiquement à l’aide de l’option `azure vm create --generate-ssh-keys` .
* Manuellement en suivant [les instructions pour les créer vous-même](mac-create-ssh-keys.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json).

Vous pouvez également utiliser la méthode `--admin-password` pour authentifier vos connexions SSH une fois la machine virtuelle créée. Cette méthode est généralement moins sécurisée.

Nous créons la machine virtuelle en regroupant toutes nos ressources et informations avec la commande `azure vm create` :

```azurecli
azure vm create \
  --resource-group myResourceGroup \
  --name myVM1 \
  --location westeurope \
  --os-type linux \
  --availset-name myAvailabilitySet \
  --nic-name myNic1 \
  --vnet-name myVnet \
  --vnet-subnet-name mySubnet \
  --storage-account-name mystorageaccount \
  --image-urn canonical:UbuntuServer:16.04.0-LTS:latest \
  --ssh-publickey-file ~/.ssh/id_rsa.pub \
  --admin-username azureuser
```

Output:

```azurecli
info:    Executing command vm create
+ Looking up the VM "myVM1"
info:    Verifying the public key SSH file: /home/ahmet/.ssh/id_rsa.pub
info:    Using the VM Size "Standard_DS1"
info:    The [OS, Data] Disk or image configuration requires storage account
+ Looking up the storage account mystorageaccount
+ Looking up the availability set "myAvailabilitySet"
info:    Found an Availability set "myAvailabilitySet"
+ Looking up the NIC "myNic1"
info:    Found an existing NIC "myNic1"
info:    Found an IP configuration with virtual network subnet id "/subscriptions/guid/resourceGroups/myResourceGroup/providers/Microsoft.Network/virtualNetworks/myVnet/subnets/mySubnet" in the NIC "myNic1"
info:    This is an NIC without publicIP configured
info:    The storage URI 'https://mystorageaccount.blob.core.windows.net/' will be used for boot diagnostics settings, and it can be overwritten by the parameter input of '--boot-diagnostics-storage-uri'.
info:    vm create command OK
```

Vous pouvez alors vous connecter immédiatement à votre machine virtuelle à l’aide de vos clés SSH par défaut. Assurez-vous de spécifier le port approprié dans la mesure où nous transitions par l’équilibreur de charge. (Pour notre première machine virtuelle, nous configurons la règle NAT pour transférer le port 4222 vers votre machine virtuelle.)

```bash
ssh ops@mypublicdns.westeurope.cloudapp.azure.com -p 4222
```

Output:

```bash
The authenticity of host '[mypublicdns.westeurope.cloudapp.azure.com]:4222 ([xx.xx.xx.xx]:4222)' can't be established.
ECDSA key fingerprint is 94:2d:d0:ce:6b:fb:7f:ad:5b:3c:78:93:75:82:12:f9.
Are you sure you want to continue connecting (yes/no)? yes
Warning: Permanently added '[mypublicdns.westeurope.cloudapp.azure.com]:4222,[xx.xx.xx.xx]:4222' (ECDSA) to the list of known hosts.
Welcome to Ubuntu 16.04.1 LTS (GNU/Linux 4.4.0-34-generic x86_64)

 * Documentation:  https://help.ubuntu.com
 * Management:     https://landscape.canonical.com
 * Support:        https://ubuntu.com/advantage

  Get cloud support with Ubuntu Advantage Cloud Guest:
    http://www.ubuntu.com/business/services/cloud

0 packages can be updated.
0 updates are security updates.

ops@myVM1:~$
```

Vous allez maintenant créer votre deuxième machine virtuelle en procédant de la même manière :

```azurecli
azure vm create \
  --resource-group myResourceGroup \
  --name myVM2 \
  --location westeurope \
  --os-type linux \
  --availset-name myAvailabilitySet \
  --nic-name myNic2 \
  --vnet-name myVnet \
  --vnet-subnet-name mySubnet \
  --storage-account-name mystorageaccount \
  --image-urn canonical:UbuntuServer:16.04.0-LTS:latest \
  --ssh-publickey-file ~/.ssh/id_rsa.pub \
  --admin-username azureuser
```

Et vous pouvez à présent utiliser la commande `azure vm show myResourceGroup myVM1` pour examiner ce que vous avez créé. À ce stade, vous exécutez vos machines virtuelles Ubuntu derrière un équilibreur de charge dans Azure auquel vous pouvez uniquement vous connecter avec votre paire de clés SSH (car les mots de passe sont désactivés). Vous pouvez installer nginx ou httpd, déployer une application web et voir le flux de trafic transitant par l’équilibreur de charge vers toutes les machines virtuelles.

```azurecli
azure vm show --resource-group myResourceGroup --name myVM1
```

Output:

```azurecli
info:    Executing command vm show
+ Looking up the VM "TestVM1"
+ Looking up the NIC "myNic1"
data:    Id                              :/subscriptions/guid/resourceGroups/myResourceGroup/providers/Microsoft.Compute/virtualMachines/myVM1
data:    ProvisioningState               :Succeeded
data:    Name                            :myVM1
data:    Location                        :westeurope
data:    Type                            :Microsoft.Compute/virtualMachines
data:
data:    Hardware Profile:
data:      Size                          :Standard_DS1
data:
data:    Storage Profile:
data:      Image reference:
data:        Publisher                   :canonical
data:        Offer                       :UbuntuServer
data:        Sku                         :16.04.0-LTS
data:        Version                     :latest
data:
data:      OS Disk:
data:        OSType                      :Linux
data:        Name                        :clib45a8b650f4428a1-os-1471973896525
data:        Caching                     :ReadWrite
data:        CreateOption                :FromImage
data:        Vhd:
data:          Uri                       :https://mystorageaccount.blob.core.windows.net/vhds/clib45a8b650f4428a1-os-1471973896525.vhd
data:
data:    OS Profile:
data:      Computer Name                 :myVM1
data:      User Name                     :ops
data:      Linux Configuration:
data:        Disable Password Auth       :true
data:
data:    Network Profile:
data:      Network Interfaces:
data:        Network Interface #1:
data:          Primary                   :true
data:          MAC Address               :00-0D-3A-24-D4-AA
data:          Provisioning State        :Succeeded
data:          Name                      :LmyNic1
data:          Location                  :westeurope
data:
data:    AvailabilitySet:
data:      Id                            :/subscriptions/guid/resourceGroups/myResourceGroup/providers/Microsoft.Compute/availabilitySets/myAvailabilitySet
data:
data:    Diagnostics Profile:
data:      BootDiagnostics Enabled       :true
data:      BootDiagnostics StorageUri    :https://mystorageaccount.blob.core.windows.net/
data:
data:      Diagnostics Instance View:
info:    vm show command OK

```


## <a name="export-the-environment-as-a-template"></a>Exportation de l’environnement en tant que modèle
Maintenant que vous avez créé cet environnement, que se passe-t-il si vous souhaitez créer un environnement de développement supplémentaire avec les mêmes paramètres ou un environnement de production correspondant ? Resource Manager utilise des modèles JSON qui définissent tous les paramètres pour votre environnement. Vous créez des environnements entiers en faisant référence à ce modèle JSON. Vous pouvez [créer manuellement des modèles JSON](../../resource-group-authoring-templates.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json) ou exporter un environnement existant qui créera le modèle JSON pour vous :

```azurecli
azure group export --name myResourceGroup
```

Cette commande crée le fichier `myResourceGroup.json` dans votre répertoire de travail actuel. Lorsque vous créez un environnement à partir de ce modèle, vous êtes invité à fournir les noms de toutes les ressources, dont l’équilibreur de charge, les interfaces réseau ou les machines virtuelles. Vous pouvez entrer ces éléments dans votre fichier de modèle en ajoutant le paramètre `-p` ou `--includeParameterDefaultValue` à la commande `azure group export` indiquée ci-dessus. Modifiez votre modèle JSON pour spécifier les noms de ressources, ou [créez un fichier parameters.json](../../resource-group-authoring-templates.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json) qui spécifie les noms de ressources.

Pour créer un nouvel environnement à partir de votre modèle :

```azurecli
azure group deployment create --resource-group myNewResourceGroup \
  --template-file myResourceGroup.json
```

Vous pouvez lire la section contenant [plus de détails sur le déploiement à partir de modèles](../../resource-group-template-deploy-cli.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json). Découvrez notamment comment mettre à jour des environnements de manière incrémentielle, utiliser le fichier de paramètres et accéder aux modèles à partir d’un emplacement de stockage unique.

## <a name="next-steps"></a>Étapes suivantes
Vous voici en mesure de commencer à utiliser plusieurs composants réseau et machines virtuelles. Vous pouvez utiliser cet exemple d’environnement pour générer votre application en utilisant les composants de base présentés ici.

