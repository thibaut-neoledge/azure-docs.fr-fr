---
title: "Création d’un environnement Linux à l’aide d’Azure CLI 2.0 | Microsoft Docs"
description: "Créez un stockage, une machine virtuelle Linux, un réseau virtuel et un sous-réseau, un équilibreur de charge, une carte d’interface réseau, une adresse IP publique et un groupe de sécurité réseau à partir de zéro à l’aide de l’interface Azure CLI 2.0."
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
ms.date: 12/8/2016
ms.author: iainfou
translationtype: Human Translation
ms.sourcegitcommit: d4cff286de1abd492ce7276c300b50d71f06345b
ms.openlocfilehash: f07a326aa2fcd659f69265001293c9ed332bb842
ms.lasthandoff: 02/27/2017


---
# <a name="create-a-complete-linux-environment-with-the-azure-cli-20"></a>Créer un environnement Linux complet à l’aide de l’interface Azure CLI 2.0
Dans cet article, nous créons un réseau simple avec un équilibreur de charge et deux machines virtuelles à des fins de développement et de calcul simple. Nous suivons ce processus, commande par commande, jusqu’à ce que vous disposiez de deux machines virtuelles Linux sécurisées opérationnelles, auxquelles vous pouvez vous connecter à partir de n’importe quel emplacement via Internet. Vous pourrez ensuite créer des réseaux et des environnements plus complexes. Cet article décrit le développement de l’environnement à l’aide de l’interface Azure CLI 2.0. Vous pouvez également effectuer ces étapes à l’aide [d’Azure CLI 1.0](virtual-machines-linux-create-cli-complete-nodejs.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json).

Vous allez découvrir la hiérarchie des dépendances et la puissance que vous offre le modèle de déploiement Resource Manager. Une fois que vous avez compris la façon dont le système est créé, vous pouvez le reconstruire beaucoup plus rapidement en utilisant des [modèles Azure Resource Manager](../azure-resource-manager/resource-group-authoring-templates.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json). De même, après avoir compris la façon dont les différentes parties de votre environnement s’imbriquent, la création de modèles pour automatiser ces dernières se révèle plus simple.

L’environnement contient :

* Deux machines virtuelles dans un groupe à haute disponibilité.
* Un équilibreur de charge avec une règle d’équilibrage de charge sur le port 80.
* Des règles de groupe de sécurité réseau (NSG) pour protéger votre machine virtuelle de tout trafic indésirable.

![Vue d’ensemble de l’environnement de base](./media/virtual-machines-linux-create-cli-complete/environment_overview.png)

## <a name="quick-commands"></a>Commandes rapides
Si vous avez besoin d’accomplir rapidement cette tâche, la section suivante décrit les commandes de base qui vous permettront de charger une machine virtuelle dans Azure. Pour obtenir plus d’informations et davantage de contexte pour chaque étape, lisez la suite de ce document, à partir de [cette section](#detailed-walkthrough).

Dans les exemples suivants, remplacez les exemples de noms de paramètre par vos propres valeurs. Exemples de noms de paramètre : `myResourceGroup`, `mystorageaccount` et `myVM`.

Pour créer cet environnement personnalisé, la dernière version de l’interface [Azure CLI 2.0](/cli/azure/install-az-cli2) doit être installée et connectée à un compte Azure à l’aide de la commande [az login](/cli/azure/#login).

Tout d’abord, créez le groupe de ressources avec la commande [az group create](/cli/azure/group#create). L’exemple suivant crée un groupe de ressources nommé `myResourceGroup` à l’emplacement `westeurope` :

```azurecli
az group create --name myResourceGroup --location westeurope
```

L’étape suivante est facultative. L’action par défaut lorsque vous créez une machine virtuelle avec Azure CLI 2.0 est d’utiliser Azure Managed Disks. Pour plus d’informations sur les disques gérés, consultez [Vue d’ensemble d’Azure Managed Disks](../storage/storage-managed-disks-overview.md). Si vous souhaitez plutôt utiliser des disques non gérés, vous devez créer un compte de stockage avec la commande [az storage account create](/cli/azure/storage/account#create). L’exemple suivant permet de créer un compte de stockage nommé `mystorageaccount`. (Le nom du compte de stockage doit être unique ; choisissez donc un autre nom qui n’est pas susceptible d’être déjà utilisé.)

```azurecli
az storage account create --resource-group myResourceGroup --location westeurope \
  --name mystorageaccount --kind Storage --sku Standard_LRS
```

Créez le réseau virtuel avec la commande [az network vnet create](/cli/azure/network/vnet#create). L’exemple suivant permet de créer un réseau virtuel nommé `myVnet` et un sous-réseau nommé `mySubnet` :

```azurecli
az network vnet create --resource-group myResourceGroup --location westeurope --name myVnet \
  --address-prefix 192.168.0.0/16 --subnet-name mySubnet --subnet-prefix 192.168.1.0/24
```

Créez une adresse IP publique avec la commande [az network public-ip create](/cli/azure/network/public-ip#create). L’exemple suivant permet de créer une adresse IP publique nommée `myPublicIP` avec le nom DNS de `mypublicdns`. (Le nom DNS doit être unique ; choisissez donc un autre nom qui n’est pas susceptible d’être déjà utilisé.)

```azurecli
az network public-ip create --resource-group myResourceGroup --location westeurope \
  --name myPublicIP --dns-name mypublicdns --allocation-method static --idle-timeout 4
```

Créez l’équilibrage de charge avec la commande [az network lb create](/cli/azure/network/lb#create). L’exemple suivant permet :

- de créer un équilibrage de charge nommé `myLoadBalancer` ;
- d’associer l’adresse IP publique `myPublicIP` ;
- de créer un pool d’adresses IP frontal nommé `mySubnetPool` ;
- de créer un pool d’adresses IP principal nommé `myBackEndPool`.

```azurecli
az network lb create --resource-group myResourceGroup --location westeurope \
  --name myLoadBalancer --public-ip-address myPublicIP \
  --frontend-ip-name myFrontEndPool --backend-pool-name myBackEndPool
```

Créez les règles de traduction d’adresse réseau (NAT) de trafic SSH entrant pour l’équilibrage de charge avec la commande [az network lb inbound-nat-rule create](/cli/azure/network/lb/inbound-nat-rule#create). L’exemple suivant permet de créer deux règles d’équilibrage de charge, `myLoadBalancerRuleSSH1` et `myLoadBalancerRuleSSH2` :

```azurecli
az network lb inbound-nat-rule create --resource-group myResourceGroup \
  --lb-name myLoadBalancer --name myLoadBalancerRuleSSH1 --protocol tcp \
  --frontend-port 4222 --backend-port 22 --frontend-ip-name myFrontEndPool
az network lb inbound-nat-rule create --resource-group myResourceGroup \
  --lb-name myLoadBalancer --name myLoadBalancerRuleSSH2 --protocol tcp \
  --frontend-port 4223 --backend-port 22 --frontend-ip-name myFrontEndPool
```

Créez la sonde d’intégrité de l’équilibrage de charge avec la commande [az network lb probe create](/cli/azure/network/lb/probe#create). L’exemple suivant permet de créer une sonde TCP nommée `myHealthProbe` :

```azurecli
az network lb probe create --resource-group myResourceGroup --lb-name myLoadBalancer \
  --name myHealthProbe --protocol tcp --port 80 --interval 15 --threshold 4
```

Créez les règles NAT de trafic web entrant pour l’équilibrage de charge avec la commande [az network lb rule create](/cli/azure/network/lb/rule#create). L’exemple suivant permet de créer une règle d’équilibrage de charge nommé `myLoadBalancerRuleWeb` et de l’associer à la sonde `myHealthProbe` :

```azurecli
az network lb rule create --resource-group myResourceGroup --lb-name myLoadBalancer \
  --name myLoadBalancerRuleWeb --protocol tcp --frontend-port 80 --backend-port 80 \
  --frontend-ip-name myFrontEndPool --backend-pool-name myBackEndPool \
  --probe-name myHealthProbe
```

Vérifiez l’équilibreur de charge, les pools d’adresses IP et les règles NAT avec la commande [az network lb show](/cli/azure/network/lb#show) :

```azurecli
az network lb show --resource-group myResourceGroup --name myLoadBalancer
```

Créez le groupe de sécurité réseau avec la commande [az network nsg create](/cli/azure/network/nsg#create). L’exemple suivant permet de créer un groupe de sécurité réseau nommé `myNetworkSecurityGroup` :

```azurecli
az network nsg create --resource-group myResourceGroup --location westeurope \
  --name myNetworkSecurityGroup
```

Ajoutez deux règles de trafic entrant pour le groupe de sécurité réseau avec la commande [az network nsg rule create](/cli/azure/network/nsg/rule#create). L’exemple suivant permet de créer deux règles, `myNetworkSecurityGroupRuleSSH` et `myNetworkSecurityGroupRuleHTTP` :

```azurecli
az network nsg rule create --resource-group myResourceGroup \
  --nsg-name myNetworkSecurityGroup --name myNetworkSecurityGroupRuleSSH \
  --protocol tcp --direction inbound --priority 1000 --source-address-prefix '*' \
  --source-port-range '*' --destination-address-prefix '*' --destination-port-range 22 \
  --access allow
az network nsg rule create --resource-group myResourceGroup \
  --nsg-name myNetworkSecurityGroup --name myNetworkSecurityGroupRuleHTTP \
  --protocol tcp --direction inbound --priority 1001 --source-address-prefix '*' \
  --source-port-range '*' --destination-address-prefix '*' --destination-port-range 80 \
  --access allow
```

Créez la première carte d’interface réseau (NIC) avec la commande [az network nic create](/cli/azure/network/nic#create). L’exemple suivant permet de créer une carte d’interface réseau nommée `myNic1` et de l’attacher à l’équilibrage de charge `myLoadBalancer` aux pools appropriés, ainsi qu’au groupe de sécurité réseau `myNetworkSecurityGroup` :

```azurecli
az network nic create --resource-group myResourceGroup --location westeurope --name myNic1 \
  --vnet-name myVnet --subnet mySubnet --network-security-group myNetworkSecurityGroup \
  --lb-name myLoadBalancer --lb-address-pools myBackEndPool \
  --lb-inbound-nat-rules myLoadBalancerRuleSSH1
```

Créez la deuxième carte d’interface réseau avec la commande **az réseau nic créer**. L’exemple suivant permet de créer une carte d’interface réseau nommée `myNic2` :

```azurecli
az network nic create --resource-group myResourceGroup --location westeurope --name myNic2 \
  --vnet-name myVnet --subnet mySubnet --network-security-group myNetworkSecurityGroup \
  --lb-name myLoadBalancer --lb-address-pools myBackEndPool \
  --lb-inbound-nat-rules myLoadBalancerRuleSSH2
```

Créez le groupe à haute disponibilité avec la commande [az vm availability-set create](/cli/azure/vm/availability-set#create). L’exemple suivant permet de créer un groupe à haute disponibilité nommé `myAvailabilitySet` :

```azurecli
az vm availability-set create --resource-group myResourceGroup --location westeurope \
  --name myAvailabilitySet \
  --platform-fault-domain-count 3 --platform-update-domain-count 2
```

Créez la première machine virtuelle Linux avec la commande [az vm create](/cli/azure/vm#create). L’exemple qui suit permet de créer une machine virtuelle nommée `myVM1` qui utilise Azure Managed Disks. Si vous souhaitez utiliser des disques non gérés, consultez la remarque supplémentaire ci-dessous.

```azurecli
az vm create \
    --resource-group myResourceGroup \
    --name myVM1 \
    --location westeurope \
    --availability-set myAvailabilitySet \
    --nics myNic1 \
    --image UbuntuLTS \
    --ssh-key-value ~/.ssh/id_rsa.pub \
    --admin-username azureuser
```

Si vous utilisez Azure Managed Disks, ignorez cette étape. Si vous souhaitez utiliser des disques non gérés et que vous avez créé un compte de stockage dans les étapes précédentes, vous devez ajouter des paramètres supplémentaires à la commande de traitement. Ajoutez les paramètres supplémentaires suivants à la commande de traitement pour créer des disques non gérés dans le compte de stockage nommé `mystorageaccount` : 

```azurecli
  --use-unmanaged-disk \
  --storage-account mystorageaccount
```

Créez la seconde machine virtuelle Linux, de nouveau avec la commande **az vm create**. L’exemple qui suit permet de créer une machine virtuelle nommée `myVM2` :

```azurecli
az vm create \
    --resource-group myResourceGroup \
    --name myVM2 \
    --location westeurope \
    --availability-set myAvailabilitySet \
    --nics myNic2 \
    --image UbuntuLTS \
    --ssh-key-value ~/.ssh/id_rsa.pub \
    --admin-username azureuser
```

Là encore, si vous n’utilisez pas par défaut Azure Managed Disks, ajoutez les paramètres supplémentaires suivants à la commande de traitement pour créer les disques non gérés dans le compte de stockage nommé `mystorageaccount` :

```azurecli
  --use-unmanaged-disk \
  --storage-account mystorageaccount
``` 

Vérifiez que tout a été correctement généré avec la commande [az vm show](/cli/azure/vm#show) :

```azurecli
az vm show --resource-group myResourceGroup --name myVM1
az vm show --resource-group myResourceGroup --name myVM2
```

Exportez votre nouvel environnement dans un modèle avec la commande [az group export](/cli/azure/group#export) pour recréer rapidement des instances nouvelles :

```azurecli
az group export --name myResourceGroup > myResourceGroup.json
```

## <a name="detailed-walkthrough"></a>Procédure pas à pas
Les étapes détaillées qui suivent expliquent ce que chaque commande fait lorsque vous générez votre environnement. Ces concepts sont utiles lorsque vous créez vos propres environnements personnalisés pour le développement ou la production.

Assurez-vous que vous avez installé la dernière version de l’interface [Azure CLI 2.0](/cli/azure/install-az-cli2) et que vous vous êtes connecté à un compte Azure avec la commande [az login](/cli/azure/#login).

Dans les exemples suivants, remplacez les exemples de noms de paramètre par vos propres valeurs. Exemples de noms de paramètre : `myResourceGroup`, `mystorageaccount` et `myVM`.

## <a name="create-resource-groups-and-choose-deployment-locations"></a>Créer des groupes de ressources et choisir les emplacements de déploiement
Les groupes de ressources Azure sont des entités de déploiement logiques qui contiennent des informations de configuration et des métadonnées pour permettre la gestion logique des déploiements de ressources. Créez le groupe de ressources avec la commande [az group create](/cli/azure/group#create). L’exemple suivant crée un groupe de ressources nommé `myResourceGroup` à l’emplacement `westeurope` :

```azurecli
az group create --name myResourceGroup --location westeurope
```

Par défaut, la sortie est au format JSON (JavaScript Object Notation). Pour obtenir une sortie sous forme de liste ou de table, par exemple, utilisez [az configure --output](/cli/azure/#configure). Vous pouvez également ajouter `--output` à n’importe quelle commande pour modifier ponctuellement le format de sortie. L’exemple suivant illustre la sortie JSON obtenue avec la commande **az group create** :

```json                       
{
  "id": "/subscriptions/guid/resourceGroups/myResourceGroup",
  "location": "westeurope",
  "name": "myResourceGroup",
  "properties": {
    "provisioningState": "Succeeded"
  },
  "tags": null
}
```

## <a name="create-a-storage-account"></a>Créez un compte de stockage.
L’étape suivante est facultative. L’action par défaut lorsque vous créez une machine virtuelle avec Azure CLI 2.0 est d’utiliser Azure Managed Disks. Ces disques sont gérés par la plateforme Azure et ne nécessitent pas de préparation ou d’emplacement pour les stocker. Pour plus d’informations sur Azure Managed Disks, voir la page [Azure Managed Disks overview](../storage/storage-managed-disks-overview.md) (Vue d’ensemble d’Azure Managed Disks). Passez à [Créer un réseau virtuel et un sous-réseau](#create-a-virtual-network-and-subnet) si vous souhaitez utiliser Azure Managed Disks. 

Si vous souhaitez utiliser des disques non gérés, vous devez créer un compte de stockage pour vos disques de machine virtuelle et pour tous les disques de données complémentaires que vous souhaitez ajouter.

Ici, nous utilisons la commande [az storage account create](/cli/azure/storage/account#create) et transmettons l’emplacement du compte, le groupe de ressources qui le contrôle, ainsi que le type de support de stockage souhaité. L’exemple qui suit permet de créer un compte de stockage nommé `mystorageaccount` :

```azurecli
az storage account create --resource-group myResourceGroup --location westeurope \
  --name mystorageaccount --kind Storage --sku Standard_LRS
```

Output:

```json
{
  "accessTier": null,
  "creationTime": "2016-12-07T17:59:50.090092+00:00",
  "customDomain": null,
  "encryption": null,
  "id": "/subscriptions/guid/resourceGroups/myresourcegroup/providers/Microsoft.Storage/storageAccounts/mystorageaccount",
  "kind": "Storage",
  "lastGeoFailoverTime": null,
  "location": "westeurope",
  "name": "mystorageaccount",
  "primaryEndpoints": {
    "blob": "https://mystorageaccount.blob.core.windows.net/",
    "file": "https://mystorageaccount.file.core.windows.net/",
    "queue": "https://mystorageaccount.queue.core.windows.net/",
    "table": "https://mystorageaccount.table.core.windows.net/"
  },
  "primaryLocation": "westeurope",
  "provisioningState": "Succeeded",
  "resourceGroup": "myresourcegroup",
  "secondaryEndpoints": null,
  "secondaryLocation": null,
  "sku": {
    "name": "Standard_LRS",
    "tier": "Standard"
  },
  "statusOfPrimary": "Available",
  "statusOfSecondary": null,
  "tags": {},
  "type": "Microsoft.Storage/storageAccounts"
}
```

Pour examiner le compte de stockage à l’aide de l’interface de ligne de commande, vous devez d’abord définir les noms de comptes et les clés. Utilisez la commande [az storage account show-connection-string](/cli/azure/storage/account#show-connection-string). Remplacez le nom du compte de stockage dans l’exemple suivant par le nom de votre choix :

```bash
export AZURE_STORAGE_CONNECTION_STRING="$(az storage account show-connection-string --resource-group myResourceGroup --name mystorageaccount --query connectionString)"
```

Vous pouvez ensuite afficher les informations sur votre stockage avec la commande [az storage container list](/cli/azure/storage/container#list) :

```azurecli
az storage container list
```

Output:

```azurecli
[
  {
    "metadata": null,
    "name": "vhds",
    "properties": {
      "etag": "\"0x8D41F912D472F94\"",
      "lastModified": "2016-12-08T17:39:35+00:00",
      "lease": {
        "duration": null,
        "state": null,
        "status": null
      },
      "leaseDuration": "infinite",
      "leaseState": "leased",
      "leaseStatus": "locked"
    }
  }
]
```

## <a name="create-a-virtual-network-and-subnet"></a>Créer un réseau virtuel et un sous-réseau
Vous allez maintenant créer un réseau virtuel dans Azure et un sous-réseau dans lesquels vous pouvez créer vos machines virtuelles. L’exemple suivant utilise la commande [az network vnet create](/cli/azure/network/vnet#create) pour créer un réseau virtuel nommé `myVnet` avec le préfixe d’adresse `192.168.0.0/16` et un sous-réseau nommé `mySubnet` avec le préfixe d’adresse de sous-réseau `192.168.1.0/24` :

```azurecli
az network vnet create --resource-group myResourceGroup --location westeurope \
  --name myVnet --address-prefix 192.168.0.0/16 \
  --subnet-name mySubnet --subnet-prefix 192.168.1.0/24
```

La sortie montre le sous-réseau comme logiquement créé à l’intérieur du réseau virtuel :

```json
{
  "addressSpace": {
    "addressPrefixes": [
      "192.168.0.0/16"
    ]
  },
  "dhcpOptions": {
    "dnsServers": []
  },
  "etag": "W/\"e95496fc-f417-426e-a4d8-c9e4d27fc2ee\"",
  "id": "/subscriptions/guid/resourceGroups/myResourceGroup/providers/Microsoft.Network/virtualNetworks/myVnet",
  "location": "westeurope",
  "name": "myVnet",
  "provisioningState": "Succeeded",
  "resourceGroup": "myResourceGroup",
  "resourceGuid": "ed62fd03-e9de-430b-84df-8a3b87cacdbb",
  "subnets": [
    {
      "addressPrefix": "192.168.1.0/24",
      "etag": "W/\"e95496fc-f417-426e-a4d8-c9e4d27fc2ee\"",
      "id": "/subscriptions/guid/resourceGroups/myResourceGroup/providers/Microsoft.Network/virtualNetworks/myVnet/subnets/mySubnet",
      "ipConfigurations": null,
      "name": "mySubnet",
      "networkSecurityGroup": null,
      "provisioningState": "Succeeded",
      "resourceGroup": "myResourceGroup",
      "resourceNavigationLinks": null,
      "routeTable": null
    }
  ],
  "tags": {},
  "type": "Microsoft.Network/virtualNetworks",
  "virtualNetworkPeerings": null
}
```


## <a name="create-a-public-ip-address"></a>Créer une adresse IP publique
Créons à présent l’adresse IP publique (PIP) à attribuer à votre équilibreur de charge. Celle-ci vous permet de vous connecter à vos machines virtuelles à partir d’Internet à l’aide de la commande [az network public-ip create](/cli/azure/network/public-ip#create). Étant donné que l’adresse par défaut est dynamique, nous créons une entrée DNS nommée dans le domaine **cloudapp.azure.com** à l’aide de l’option `--domain-name-label`. L’exemple suivant permet de créer une adresse IP publique nommée `myPublicIP` avec le nom DNS de `mypublicdns`. Étant donné que le nom DNS doit être unique, choisissez un autre nom DNS qui n’est pas susceptible d’être déjà utilisé :

```azurecli
az network public-ip create --resource-group myResourceGroup --location westeurope \
  --name myPublicIP --dns-name mypublicdns --allocation-method static --idle-timeout 4
```

Output:

```json
{
  "publicIp": {
    "dnsSettings": {
      "domainNameLabel": "mypublicdns",
      "fqdn": "mypublicdns.westeurope.cloudapp.azure.com",
      "reverseFqdn": ""
    },
    "idleTimeoutInMinutes": 4,
    "ipAddress": "52.174.48.109",
    "provisioningState": "Succeeded",
    "publicIPAllocationMethod": "Static",
    "resourceGuid": "78218510-ea54-42d7-b2c2-44773fc14af5"
  }
}
```

La ressource d’adresse IP publique a été allouée de façon logique, mais aucune adresse spécifique n’a encore été attribuée. Pour obtenir une adresse IP, vous aurez besoin d’un équilibreur de charge que nous n’avons pas encore créé.

## <a name="create-a-load-balancer-and-ip-pools"></a>Créer un équilibreur de charge et des pools d’adresses IP
Lorsque vous créez un équilibreur de charge, celui-ci vous permet de répartir le trafic entre plusieurs machines virtuelles. Cela assure également la redondance de votre application en exécutant plusieurs machines virtuelles qui répondent aux demandes des utilisateurs en cas de maintenance ou de lourdes charges. L’exemple suivant utilise la commande [az network lb create](/cli/azure/network/lb#create) pour créer un équilibrage de charge nommé `myLoadBalancer`, un pool d’adresses IP frontal nommé `myFrontEndPool` et lie la ressource `myPublicIP` :

```azurecli
az network lb create --resource-group myResourceGroup --location westeurope \
  --name myLoadBalancer --public-ip-address myPublicIP --frontend-ip-name myFrontEndPool
```

Output:

```json
{
  "loadBalancer": {
    "backendAddressPools": [
      {
        "etag": "W/\"7a05df7a-5ee2-4581-b411-4b6f048ab291\"",
        "id": "/subscriptions/guid/resourceGroups/myResourceGroup/providers/Microsoft.Network/loadBalancers/myLoadBalancer/backendAddressPools/myLoadBalancerbepool",
        "name": "myLoadBalancerbepool",
        "properties": {
          "provisioningState": "Succeeded"
        },
        "resourceGroup": "myResourceGroup"
      }
    ],
    "frontendIPConfigurations": [
      {
        "etag": "W/\"7a05df7a-5ee2-4581-b411-4b6f048ab291\"",
        "id": "/subscriptions/guid/resourceGroups/myResourceGroup/providers/Microsoft.Network/loadBalancers/myLoadBalancer/frontendIPConfigurations/myFrontEndPool",
        "name": "myFrontEndPool",
        "properties": {
          "privateIPAllocationMethod": "Dynamic",
          "provisioningState": "Succeeded",
          "publicIPAddress": {
            "id": "/subscriptions/guid/resourceGroups/myResourceGroup/providers/Microsoft.Network/publicIPAddresses/myPublicIP",
            "resourceGroup": "myResourceGroup"
          }
        },
        "resourceGroup": "myResourceGroup"
      }
    ],
    "inboundNatPools": [],
    "inboundNatRules": [],
    "loadBalancingRules": [],
    "outboundNatRules": [],
    "probes": [],
    "provisioningState": "Succeeded",
    "resourceGuid": "f4879d84-5ae8-4e06-8b9b-1419baa875d9"
  }
}
```

Notez comment nous avons utilisé le commutateur `--public-ip-address` pour transmettre le `myPublicIP` créé précédemment. L’attribution de l’adresse IP publique à l’équilibreur de charge vous permet d’atteindre vos machines virtuelles via Internet.

Nous utilisons un pool principal comme emplacement auquel connecter nos machines virtuelles. Ainsi, le trafic peut transiter via l’équilibreur de charge vers les machines virtuelles. Créons le pool d’adresses IP pour le trafic principal avec la commande [az network lb address-pool create](/cli/azure/network/lb/address-pool#create). L’exemple suivant permet de créer un pool principal nommé `myBackEndPool` :

```azurecli
az network lb address-pool create --resource-group myResourceGroup \
  --lb-name myLoadBalancer --name myBackEndPool
```

Extrait de la sortie :

```json
  "backendAddressPools": [
    {
      "backendIpConfigurations": null,
      "etag": "W/\"a61814bc-ce4c-4fb2-9d6a-5b1949078345\"",
      "id": "/subscriptions/guid/resourceGroups/myResourceGroup/providers/Microsoft.Network/loadBalancers/myLoadBalancer/backendAddressPools/myLoadBalancerbepool",
      "loadBalancingRules": null,
      "name": "myLoadBalancerbepool",
      "outboundNatRule": null,
      "provisioningState": "Succeeded",
      "resourceGroup": "myResourceGroup"
    },
    {
      "backendIpConfigurations": null,
      "etag": "W/\"a61814bc-ce4c-4fb2-9d6a-5b1949078345\"",
      "id": "/subscriptions/guid/resourceGroups/myResourceGroup/providers/Microsoft.Network/loadBalancers/myLoadBalancer/backendAddressPools/myBackEndPool",
      "loadBalancingRules": null,
      "name": "myBackEndPool",
      "outboundNatRule": null,
      "provisioningState": "Succeeded",
      "resourceGroup": "myResourceGroup"
    }
  ],
  "etag": "W/\"a61814bc-ce4c-4fb2-9d6a-5b1949078345\"",
```


## <a name="create-load-balancer-nat-rules"></a>Créer des règles NAT d’équilibreur de charge
Pour que le trafic transite par notre équilibrage de charge, nous devons créer des règles de traduction d’adresse réseau (NAT) spécifiant les actions entrantes ou sortantes. Vous pouvez spécifier le protocole utilisé, puis mapper les ports externes aux ports internes comme vous le souhaitez. Pour notre environnement, nous allons créer des règles autorisant SSH à accéder à nos machines virtuelles par le biais de notre équilibreur de charge avec la commande [az network lb inbound-nat-rule create](/cli/azure/network/lb/inbound-nat-rule#create). Nous configurons les ports TCP 4222 et 4223 de manière à orienter le trafic vers le port TCP 22 sur nos machines virtuelles (que nous créerons plus tard). L’exemple suivant crée une règle nommée `myLoadBalancerRuleSSH1` pour mapper le port TCP 4222 sur le port 22 :

```azurecli
az network lb inbound-nat-rule create --resource-group myResourceGroup \
  --lb-name myLoadBalancer --name myLoadBalancerRuleSSH1 --protocol tcp \
  --frontend-port 4222 --backend-port 22 --frontend-ip-name myFrontEndPool
```

Output:

```json
{
  "backendIpConfiguration": null,
  "backendPort": 22,
  "enableFloatingIp": false,
  "etag": "W/\"72843cf8-b5fb-4655-9ac8-9cbbbbf1205a\"",
  "frontendIpConfiguration": {
    "id": "/subscriptions/guid/resourceGroups/myResourceGroup/providers/Microsoft.Network/loadBalancers/myLoadBalancer/frontendIPConfigurations/myFrontEndPool",
    "resourceGroup": "myResourceGroup"
  },
  "frontendPort": 4222,
  "id": "/subscriptions/guid/resourceGroups/myResourceGroup/providers/Microsoft.Network/loadBalancers/myLoadBalancer/inboundNatRules/myLoadBalancerRuleSSH1",
  "idleTimeoutInMinutes": 4,
  "name": "myLoadBalancerRuleSSH1",
  "protocol": "Tcp",
  "provisioningState": "Succeeded",
  "resourceGroup": "myResourceGroup"
}
```

Répétez cette procédure pour votre deuxième règle NAT pour le protocole SSH. L’exemple suivant crée une règle nommée `myLoadBalancerRuleSSH2` pour mapper le port TCP 4223 sur le port 22 :

```azurecli
az network lb inbound-nat-rule create --resource-group myResourceGroup \
  --lb-name myLoadBalancer --name myLoadBalancerRuleSSH2 --protocol tcp \
  --frontend-port 4223 --backend-port 22 --frontend-ip-name myFrontEndPool
```

## <a name="create-a-load-balancer-health-probe"></a>Créer une sonde d’intégrité d’équilibreur de charge
Une sonde d’intégrité contrôle régulièrement les machines virtuelles situées derrière notre équilibreur de charge pour s’assurer qu’elles fonctionnent correctement et répondent aux demandes, telles que définies. Dans le cas contraire, elles sont supprimées du processus pour garantir que les utilisateurs ne sont pas orientés vers elles. Vous pouvez définir les contrôles personnalisés de la sonde d’intégrité, ainsi que des valeurs d’intervalle et de délai d’attente. Pour plus d'informations sur les sondes d’intégrité, consultez [Sondes d’équilibreur de charge](../load-balancer/load-balancer-custom-probe-overview.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json). L’exemple suivant utilise la commande [az network lb probe create](/cli/azure/network/lb/probe#create) pour créer une sonde d’intégrité TCP nommée `myHealthProbe` :

```azurecli
az network lb probe create --resource-group myResourceGroup --lb-name myLoadBalancer \
  --name myHealthProbe --protocol tcp --port 80 --interval 15 --threshold 4
```

Output:

```json
{
  "etag": "W/\"757018f6-b70a-4651-b717-48b511d82ba0\"",
  "id": "/subscriptions/guid/resourceGroups/myResourceGroup/providers/Microsoft.Network/loadBalancers/myLoadBalancer/probes/myHealthProbe",
  "intervalInSeconds": 15,
  "loadBalancingRules": null,
  "name": "myHealthProbe",
  "numberOfProbes": 4,
  "port": 80,
  "protocol": "Tcp",
  "provisioningState": "Succeeded",
  "requestPath": null,
  "resourceGroup": "myResourceGroup"
}
```

Ici, nous avons spécifié un intervalle de 15 secondes pour nos contrôles d’intégrité. Nous pouvons manquer au maximum quatre sondes (une minute) avant que l’équilibreur de charge considère que l’hôte ne fonctionne plus.

Nous allons maintenant créer une règle NAT pour le port TCP 80 du trafic web , en liant la règle à nos pools d’adresses IP. Si nous lions l’adresse à un pool d’adresses IP au lieu de la lier à nos machines virtuelles individuellement, nous pouvons ajouter ou supprimer des machines virtuelles dans le pool d’adresses IP. L’équilibrage de charge ajuste alors automatiquement le flux de trafic. L’exemple suivant utilise la commande [az network lb rule create](/cli/azure/network/lb/rule#create) pour créer une règle nommée `myLoadBalancerRuleWeb` afin de mapper le port TCP 80 sur le port 80, et lie la sonde d’intégrité nommée `myHealthProbe` :

```azurecli
az network lb rule create --resource-group myResourceGroup --lb-name myLoadBalancer \
  --name myLoadBalancerRuleWeb --protocol tcp --frontend-port 80 --backend-port 80 \
  --frontend-ip-name myFrontEndPool --backend-pool-name myBackEndPool \
  --probe-name myHealthProbe
```

Output:

```json
{
  "backendAddressPool": {
    "id": "/subscriptions/guid/resourceGroups/myResourceGroup/providers/Microsoft.Network/loadBalancers/myLoadBalancer/backendAddressPools/myBackEndPool",
    "resourceGroup": "myResourceGroup"
  },
  "backendPort": 80,
  "enableFloatingIp": false,
  "etag": "W/\"f0d77680-bf42-4d11-bfab-5d2c541bee56\"",
  "frontendIpConfiguration": {
    "id": "/subscriptions/guid/resourceGroups/myResourceGroup/providers/Microsoft.Network/loadBalancers/myLoadBalancer/frontendIPConfigurations/myFrontEndPool",
    "resourceGroup": "myResourceGroup"
  },
  "frontendPort": 80,
  "id": "/subscriptions/guid/resourceGroups/myResourceGroup/providers/Microsoft.Network/loadBalancers/myLoadBalancer/loadBalancingRules/myLoadBalancerRuleWeb",
  "idleTimeoutInMinutes": 4,
  "loadDistribution": "Default",
  "name": "myLoadBalancerRuleWeb",
  "probe": {
    "id": "/subscriptions/guid/resourceGroups/myResourceGroup/providers/Microsoft.Network/loadBalancers/myLoadBalancer/probes/myHealthProbe",
    "resourceGroup": "myResourceGroup"
  },
  "protocol": "Tcp",
  "provisioningState": "Succeeded",
  "resourceGroup": "myResourceGroup"
}
```

## <a name="verify-the-load-balancer"></a>Vérifier l’équilibreur de charge
Vérifiez maintenant que la configuration de l’équilibreur de charge est terminée. Voici la procédure que vous avez suivie :

1. Vous avez créé un équilibrage de charge.
2. Vous avez créé un pool d’adresses IP frontal et lui avez affecté une adresse IP publique.
3. Vous avez créé un pool d’adresses IP principal auquel les machines virtuelles peuvent se connecter.
4. Vous avez créé des règles NAT qui autorisent le protocole SSH pour les machines virtuelles à des fins de gestion, ainsi qu’une règle autorisant le port TCP 80 de notre application web.
5. Vous avez ajouté une sonde d’intégrité pour contrôler périodiquement les machines virtuelles. Cette sonde d’intégrité s’assure que des utilisateurs n’essaient d’accéder à une machine virtuelle qui ne fonctionne plus ou qui ne sert plus de contenu.

Regardons maintenant à quoi ressemble votre équilibrage de charge avec la commande [az network lb show](/cli/azure/network/lb#show) :

```azurecli
az network lb show --resource-group myResourceGroup --name myLoadBalancer
```

Output:

```json
{
  "backendAddressPools": [
    {
      "backendIpConfigurations": null,
      "etag": "W/\"a0e313a1-6de1-48be-aeb6-df57188d708c\"",
      "id": "/subscriptions/guid/resourceGroups/myResourceGroup/providers/Microsoft.Network/loadBalancers/myLoadBalancer/backendAddressPools/myLoadBalancerbepool",
      "loadBalancingRules": null,
      "name": "myLoadBalancerbepool",
      "outboundNatRule": null,
      "provisioningState": "Succeeded",
      "resourceGroup": "myResourceGroup"
    },
    {
      "backendIpConfigurations": null,
      "etag": "W/\"a0e313a1-6de1-48be-aeb6-df57188d708c\"",
      "id": "/subscriptions/guid/resourceGroups/myResourceGroup/providers/Microsoft.Network/loadBalancers/myLoadBalancer/backendAddressPools/myBackEndPool",
      "loadBalancingRules": null,
      "name": "myBackEndPool",
      "outboundNatRule": null,
      "provisioningState": "Succeeded",
      "resourceGroup": "myResourceGroup"
    }
  ],
  "etag": "W/\"a0e313a1-6de1-48be-aeb6-df57188d708c\"",
  "frontendIpConfigurations": [
    {
      "etag": "W/\"a0e313a1-6de1-48be-aeb6-df57188d708c\"",
      "id": "/subscriptions/guid/resourceGroups/myResourceGroup/providers/Microsoft.Network/loadBalancers/myLoadBalancer/frontendIPConfigurations/LoadBalancerFrontEnd",
      "inboundNatPools": null,
      "inboundNatRules": null,
      "loadBalancingRules": null,
      "name": "LoadBalancerFrontEnd",
      "outboundNatRules": null,
      "privateIpAddress": null,
      "privateIpAllocationMethod": "Dynamic",
      "provisioningState": "Succeeded",
      "publicIpAddress": {
        "dnsSettings": null,
        "etag": null,
        "id": "/subscriptions/guid/resourceGroups/myResourceGroup/providers/Microsoft.Network/publicIPAddresses/PublicIPmyLoadBalancer",
        "idleTimeoutInMinutes": null,
        "ipAddress": null,
        "ipConfiguration": null,
        "location": null,
        "name": null,
        "provisioningState": null,
        "publicIpAddressVersion": null,
        "publicIpAllocationMethod": null,
        "resourceGroup": "myResourceGroup",
        "resourceGuid": null,
        "tags": null,
        "type": null
      },
      "resourceGroup": "myResourceGroup",
      "subnet": null
    },
    {
      "etag": "W/\"a0e313a1-6de1-48be-aeb6-df57188d708c\"",
      "id": "/subscriptions/guid/resourceGroups/myResourceGroup/providers/Microsoft.Network/loadBalancers/myLoadBalancer/frontendIPConfigurations/myFrontEndPool",
      "inboundNatPools": null,
      "inboundNatRules": null,
      "loadBalancingRules": null,
      "name": "myFrontEndPool",
      "outboundNatRules": null,
      "privateIpAddress": null,
      "privateIpAllocationMethod": "Dynamic",
      "provisioningState": "Succeeded",
      "publicIpAddress": {
        "dnsSettings": null,
        "etag": null,
        "id": "/subscriptions/guid/resourceGroups/myResourceGroup/providers/Microsoft.Network/publicIPAddresses/myPublicIP",
        "idleTimeoutInMinutes": null,
        "ipAddress": null,
        "ipConfiguration": null,
        "location": null,
        "name": null,
        "provisioningState": null,
        "publicIpAddressVersion": null,
        "publicIpAllocationMethod": null,
        "resourceGroup": "myResourceGroup",
        "resourceGuid": null,
        "tags": null,
        "type": null
      },
      "resourceGroup": "myResourceGroup",
      "subnet": null
    }
  ],
  "id": "/subscriptions/guid/resourceGroups/myResourceGroup/providers/Microsoft.Network/loadBalancers/myLoadBalancer",
  "inboundNatPools": [],
  "inboundNatRules": [],
  "loadBalancingRules": [],
  "location": "westeurope",
  "name": "myLoadBalancer",
  "outboundNatRules": [],
  "probes": [],
  "provisioningState": "Succeeded",
  "resourceGroup": "myResourceGroup",
  "resourceGuid": "b5815801-b53d-4c22-aafc-2a9064f90f3c",
  "tags": {},
  "type": "Microsoft.Network/loadBalancers"
}
```

## <a name="create-a-network-security-group-and-rules"></a>Créer un groupe de sécurité réseau et les règles associées
Nous allons à présent créer un groupe de sécurité réseau et les règles de trafic entrant qui régissent l’accès à la carte d’interface réseau. Un groupe de sécurité réseau peut être appliqué à une carte d’interface réseau ou à un sous-réseau. Vous définissez des règles pour contrôler le flux de trafic vers et à partir de vos machines virtuelles. L’exemple suivant utilise la commande [az network nsg create](/cli/azure/network/nsg#create) pour créer un groupe de sécurité réseau nommé `myNetworkSecurityGroup` :

```azurecli
az network nsg create --resource-group myResourceGroup --location westeurope \
  --name myNetworkSecurityGroup
```

Ajoutons la règle de trafic entrant pour le groupe de sécurité réseau avec la commande [az network nsg rule create](/cli/azure/network/nsg/rule#create) afin d’autoriser les connexions entrantes sur le port 22 (pour prendre en charge le protocole SSH). L’exemple suivant permet de créer une règle nommée `myNetworkSecurityGroupRuleSSH` pour autoriser le trafic TCP sur le port 22 :

```azurecli
az network nsg rule create --resource-group myResourceGroup \
  --nsg-name myNetworkSecurityGroup --name myNetworkSecurityGroupRuleSSH \
  --protocol tcp --direction inbound --priority 1000 \
  --source-address-prefix '*' --source-port-range '*' \
  --destination-address-prefix '*' --destination-port-range 22 --access allow
```

À présent, nous ajoutons la règle de trafic entrant pour le groupe de sécurité réseau afin d’autoriser les connexions entrantes sur le port 80 (pour prendre en charge le trafic web). L’exemple suivant permet de créer une règle nommée `myNetworkSecurityGroupRuleHTTP` pour autoriser le trafic TCP sur le port 80 :

```azurecli
az network nsg rule create --resource-group myResourceGroup \
  --nsg-name myNetworkSecurityGroup --name myNetworkSecurityGroupRuleHTTP \
  --protocol tcp --direction inbound --priority 1001 \
  --source-address-prefix '*' --source-port-range '*' \
  --destination-address-prefix '*' --destination-port-range 80 --access allow
```

> [!NOTE]
> La règle de trafic entrant est un filtre pour les connexions réseau entrantes. Dans cet exemple, nous lions le NSG à la carte d’interface réseau (NIC) virtuelle des machines virtuelles, ce qui signifie que toute demande adressée au port 22 est transmise à la carte d’interface réseau sur notre machine virtuelle. Cette règle de trafic entrant concerne une connexion réseau, et non à un point de terminaison, comme dans le cas de déploiements classiques. Pour ouvrir un port, vous devez laisser la commande `--source-port-range` définie sur « \* » (valeur par défaut) afin d’accepter les demandes entrantes de **tous** les ports demandeurs. Ces ports sont généralement dynamiques.

Examinez le groupe et les règles de sécurité réseau avec la commande [az network nsg show](/cli/azure/network/nsg#show) :

```azurecli
az network nsg show --resource-group myResourceGroup --name myNetworkSecurityGroup
```

Output:

```json
{
  "defaultSecurityRules": [
    {
      "access": "Allow",
      "description": "Allow inbound traffic from all VMs in VNET",
      "destinationAddressPrefix": "VirtualNetwork",
      "destinationPortRange": "*",
      "direction": "Inbound",
      "etag": "W/\"2b656589-f332-4ba4-92f3-afb3be5c192c\"",
      "id": "/subscriptions/guid/resourceGroups/myResourceGroup/providers/Microsoft.Network/networkSecurityGroups/myNetworkSecurityGroup/defaultSecurityRules/AllowVnetInBound",
      "name": "AllowVnetInBound",
      "priority": 65000,
      "protocol": "*",
      "provisioningState": "Succeeded",
      "resourceGroup": "myResourceGroup",
      "sourceAddressPrefix": "VirtualNetwork",
      "sourcePortRange": "*"
    },
    {
      "access": "Allow",
      "description": "Allow inbound traffic from azure load balancer",
      "destinationAddressPrefix": "*",
      "destinationPortRange": "*",
      "direction": "Inbound",
      "etag": "W/\"2b656589-f332-4ba4-92f3-afb3be5c192c\"",
      "id": "/subscriptions/guid/resourceGroups/myResourceGroup/providers/Microsoft.Network/networkSecurityGroups/myNetworkSecurityGroup/defaultSecurityRules/AllowAzureLoadBalancerInBound",
      "name": "AllowAzureLoadBalancerInBound",
      "priority": 65001,
      "protocol": "*",
      "provisioningState": "Succeeded",
      "resourceGroup": "myResourceGroup",
      "sourceAddressPrefix": "AzureLoadBalancer",
      "sourcePortRange": "*"
    },
    {
      "access": "Deny",
      "description": "Deny all inbound traffic",
      "destinationAddressPrefix": "*",
      "destinationPortRange": "*",
      "direction": "Inbound",
      "etag": "W/\"2b656589-f332-4ba4-92f3-afb3be5c192c\"",
      "id": "/subscriptions/guid/resourceGroups/myResourceGroup/providers/Microsoft.Network/networkSecurityGroups/myNetworkSecurityGroup/defaultSecurityRules/DenyAllInBound",
      "name": "DenyAllInBound",
      "priority": 65500,
      "protocol": "*",
      "provisioningState": "Succeeded",
      "resourceGroup": "myResourceGroup",
      "sourceAddressPrefix": "*",
      "sourcePortRange": "*"
    },
    {
      "access": "Allow",
      "description": "Allow outbound traffic from all VMs to all VMs in VNET",
      "destinationAddressPrefix": "VirtualNetwork",
      "destinationPortRange": "*",
      "direction": "Outbound",
      "etag": "W/\"2b656589-f332-4ba4-92f3-afb3be5c192c\"",
      "id": "/subscriptions/guid/resourceGroups/myResourceGroup/providers/Microsoft.Network/networkSecurityGroups/myNetworkSecurityGroup/defaultSecurityRules/AllowVnetOutBound",
      "name": "AllowVnetOutBound",
      "priority": 65000,
      "protocol": "*",
      "provisioningState": "Succeeded",
      "resourceGroup": "myResourceGroup",
      "sourceAddressPrefix": "VirtualNetwork",
      "sourcePortRange": "*"
    },
    {
      "access": "Allow",
      "description": "Allow outbound traffic from all VMs to Internet",
      "destinationAddressPrefix": "Internet",
      "destinationPortRange": "*",
      "direction": "Outbound",
      "etag": "W/\"2b656589-f332-4ba4-92f3-afb3be5c192c\"",
      "id": "/subscriptions/guid/resourceGroups/myResourceGroup/providers/Microsoft.Network/networkSecurityGroups/myNetworkSecurityGroup/defaultSecurityRules/AllowInternetOutBound",
      "name": "AllowInternetOutBound",
      "priority": 65001,
      "protocol": "*",
      "provisioningState": "Succeeded",
      "resourceGroup": "myResourceGroup",
      "sourceAddressPrefix": "*",
      "sourcePortRange": "*"
    },
    {
      "access": "Deny",
      "description": "Deny all outbound traffic",
      "destinationAddressPrefix": "*",
      "destinationPortRange": "*",
      "direction": "Outbound",
      "etag": "W/\"2b656589-f332-4ba4-92f3-afb3be5c192c\"",
      "id": "/subscriptions/guid/resourceGroups/myResourceGroup/providers/Microsoft.Network/networkSecurityGroups/myNetworkSecurityGroup/defaultSecurityRules/DenyAllOutBound",
      "name": "DenyAllOutBound",
      "priority": 65500,
      "protocol": "*",
      "provisioningState": "Succeeded",
      "resourceGroup": "myResourceGroup",
      "sourceAddressPrefix": "*",
      "sourcePortRange": "*"
    }
  ],
  "etag": "W/\"2b656589-f332-4ba4-92f3-afb3be5c192c\"",
  "id": "/subscriptions/guid/resourceGroups/myResourceGroup/providers/Microsoft.Network/networkSecurityGroups/myNetworkSecurityGroup",
  "location": "westeurope",
  "name": "myNetworkSecurityGroup",
  "networkInterfaces": null,
  "provisioningState": "Succeeded",
  "resourceGroup": "myResourceGroup",
  "resourceGuid": "79c2c293-ee3e-4616-bf9c-4741ff1f708a",
  "securityRules": [
    {
      "access": "Allow",
      "description": null,
      "destinationAddressPrefix": "*",
      "destinationPortRange": "22",
      "direction": "Inbound",
      "etag": "W/\"2b656589-f332-4ba4-92f3-afb3be5c192c\"",
      "id": "/subscriptions/guid/resourceGroups/myResourceGroup/providers/Microsoft.Network/networkSecurityGroups/myNetworkSecurityGroup/securityRules/myNetworkSecurityGroupRuleSSH",
      "name": "myNetworkSecurityGroupRuleSSH",
      "priority": 1000,
      "protocol": "tcp",
      "provisioningState": "Succeeded",
      "resourceGroup": "myResourceGroup",
      "sourceAddressPrefix": "*",
      "sourcePortRange": "*"
    },
    {
      "access": "Allow",
      "description": null,
      "destinationAddressPrefix": "*",
      "destinationPortRange": "80",
      "direction": "Inbound",
      "etag": "W/\"2b656589-f332-4ba4-92f3-afb3be5c192c\"",
      "id": "/subscriptions/guid/resourceGroups/myResourceGroup/providers/Microsoft.Network/networkSecurityGroups/myNetworkSecurityGroup/securityRules/myNetworkSecurityGroupRuleHTTP",
      "name": "myNetworkSecurityGroupRuleHTTP",
      "priority": 1001,
      "protocol": "tcp",
      "provisioningState": "Succeeded",
      "resourceGroup": "myResourceGroup",
      "sourceAddressPrefix": "*",
      "sourcePortRange": "*"
    }
  ],
  "subnets": null,
  "tags": {},
  "type": "Microsoft.Network/networkSecurityGroups"
}
```

## <a name="create-an-nic-to-use-with-the-linux-vm"></a>Créer une carte d’interface réseau (NIC) à utiliser avec la machine virtuelle Linux
La disponibilité des cartes d’interface réseau peut être déterminée par programme car vous pouvez définir des règles régissant leur utilisation. Vous pouvez également avoir plusieurs cartes. Dans la commande [az network nic create](https://docs.microsoft.com/en-us/cli/azure/network/nic#create) suivante, la carte d’interface réseau est liée au pool d’adresses IP principal de charge et associée à la règle NAT pour autoriser le trafic SSH et le groupe de sécurité réseau.

L’exemple suivant permet de créer une carte d’interface réseau nommée `myNic1` :

```azurecli
az network nic create --resource-group myResourceGroup --location westeurope --name myNic1 \
  --vnet-name myVnet --subnet mySubnet --network-security-group myNetworkSecurityGroup \
  --lb-name myLoadBalancer --lb-address-pools myBackEndPool \
  --lb-inbound-nat-rules myLoadBalancerRuleSSH1
```

Output:

```json
{
  "newNIC": {
    "dnsSettings": {
      "appliedDnsServers": [],
      "dnsServers": []
    },
    "enableIPForwarding": false,
    "ipConfigurations": [
      {
        "etag": "W/\"a76b5c0d-14e1-4a99-afd4-5cd8ac0465ca\"",
        "id": "/subscriptions/guid/resourceGroups/myResourceGroup/providers/Microsoft.Network/networkInterfaces/myNic1/ipConfigurations/ipconfig1",
        "name": "ipconfig1",
        "properties": {
          "loadBalancerBackendAddressPools": [
            {
              "id": "/subscriptions/guid/resourceGroups/myResourceGroup/providers/Microsoft.Network/loadBalancers/myLoadBalancer/backendAddressPools/myBackEndPool",
              "resourceGroup": "myResourceGroup"
            }
          ],
          "loadBalancerInboundNatRules": [
            {
              "id": "/subscriptions/guid/resourceGroups/myResourceGroup/providers/Microsoft.Network/loadBalancers/myLoadBalancer/inboundNatRules/myLoadBalancerRuleSSH1",
              "resourceGroup": "myResourceGroup"
            }
          ],
          "primary": true,
          "privateIPAddress": "192.168.1.4",
          "privateIPAllocationMethod": "Dynamic",
          "provisioningState": "Succeeded",
          "subnet": {
            "id": "/subscriptions/guid/resourceGroups/myResourceGroup/providers/Microsoft.Network/virtualNetworks/myVnet/subnets/mySubnet",
            "resourceGroup": "myResourceGroup"
          }
        },
        "resourceGroup": "myResourceGroup"
      }
    ],
    "networkSecurityGroup": {
      "id": "/subscriptions/guid/resourceGroups/myResourceGroup/providers/Microsoft.Network/networkSecurityGroups/myNetworkSecurityGroup",
      "resourceGroup": "myResourceGroup"
    },
    "provisioningState": "Succeeded",
    "resourceGuid": "838977cb-cf4b-4c4a-9a32-0ddec7dc818b"
  }
}
```

Nous créons maintenant la deuxième carte réseau en la liant à nouveau à notre pool d’adresses IP principal. Cette fois, la deuxième règle NAT autorise le trafic SSH. L’exemple suivant permet de créer une carte d’interface réseau nommée `myNic2` :

```azurecli
az network nic create --resource-group myResourceGroup --location westeurope --name myNic2 \
  --vnet-name myVnet --subnet mySubnet --network-security-group myNetworkSecurityGroup \
  --lb-name myLoadBalancer --lb-address-pools myBackEndPool \
  --lb-inbound-nat-rules myLoadBalancerRuleSSH2
```


## <a name="create-an-availability-set"></a>Créer un groupe à haute disponibilité
Les groupes à haute disponibilité aident à diffuser vos machines virtuelles sur des domaines d’erreur et des domaines de mise à niveau. Créons un groupe à haute disponibilité pour vos machines virtuelles avec la commande [az vm availability-set create](/cli/azure/vm/availability-set#create). L’exemple suivant permet de créer un groupe à haute disponibilité nommé `myAvailabilitySet` :

```azurecli
az vm availability-set create --resource-group myResourceGroup --location westeurope \
  --name myAvailabilitySet \
  --platform-fault-domain-count 3 --platform-update-domain-count 2
```

Les domaines d’erreur désignent un groupe de machines virtuelles partageant une source d’alimentation et un commutateur réseau communs. Par défaut, les machines virtuelles configurées dans votre groupe à haute disponibilité sont réparties entre trois domaines de défaillance au maximum. Ainsi, en cas de problème matériel dans l’un de ces domaines d’erreur, aucune machine virtuelle exécutant votre application n’est affectée. Azure distribue automatiquement les machines virtuelles sur les domaines d’erreur lorsque vous les placez dans un groupe à haute disponibilité.

Les domaines de mise à niveau indiquent les groupes de machines virtuelles et les équipements physiques sous-jacents pouvant être redémarrés en même temps. L’ordre de redémarrage des domaines de mise à jour peut ne pas être séquentiel au cours de la maintenance planifiée, mais les mises à niveau sont redémarrées une par une. Cette fois encore, Azure distribue automatiquement vos machines virtuelles entre les domaines de mise à niveau lorsque vous les placez dans un site de disponibilité.

Vous pouvez obtenir des informations supplémentaires sur [la gestion de la disponibilité des machines virtuelles](virtual-machines-linux-manage-availability.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json).


## <a name="create-the-linux-vms"></a>Créer les machines virtuelles Linux
Vous avez créé les ressources de réseau pour prendre en charge des machines virtuelles accessibles par Internet. Créons maintenant ces machines virtuelles et sécurisons-les avec une clé SSH sans mot de passe. Dans ce cas précis, nous allons créer une machine virtuelle Ubuntu basée sur la dernière version LTS. Nous recherchons les informations de cette image à l’aide de la commande [az vm image list](/cli/azure/vm/image#list), comme décrit dans [Recherche d’images de machine virtuelle Azure](virtual-machines-linux-cli-ps-findimage.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json).

Nous spécifions également une clé SSH à utiliser pour l’authentification. Si vous n’avez pas de clés SSH, vous pouvez en créer en suivant [ces instructions](virtual-machines-linux-mac-create-ssh-keys.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json). Vous pouvez également utiliser la méthode `--admin-password` pour authentifier vos connexions SSH une fois la machine virtuelle créée. Cette méthode est généralement moins sécurisée.

Nous créons la machine virtuelle en regroupant toutes nos ressources et informations avec la commande [az vm create](/cli/azure/vm#create). L’exemple qui suit permet de créer une machine virtuelle nommée `myVM1` qui utilise Azure Managed Disks. Si vous souhaitez utiliser des disques non gérés, consultez la remarque supplémentaire ci-dessous.

```azurecli
az vm create \
    --resource-group myResourceGroup \
    --name myVM1 \
    --location westeurope \
    --availability-set myAvailabilitySet \
    --nics myNic1 \
    --image UbuntuLTS \
    --ssh-key-value ~/.ssh/id_rsa.pub \
    --admin-username azureuser
```

Si vous utilisez Azure Managed Disks, ignorez cette étape. Si vous souhaitez utiliser des disques non gérés et que vous avez créé un compte de stockage dans les étapes précédentes, vous devez ajouter des paramètres supplémentaires à la commande de traitement. Ajoutez les paramètres supplémentaires suivants à la commande de traitement pour créer des disques non gérés dans le compte de stockage nommé `mystorageaccount` : 

```azurecli
  --use-unmanaged-disk \
  --storage-account mystorageaccount
```

Output:

```json
{
  "fqdn": "",
  "id": "/subscriptions/guid/resourceGroups/myResourceGroup/providers/Microsoft.Compute/virtualMachines/myVM1",
  "macAddress": "",
  "privateIpAddress": "",
  "publicIpAddress": "",
  "resourceGroup": "myResourceGroup"
}
```

Vous pouvez alors vous connecter immédiatement à votre machine virtuelle à l’aide de vos clés SSH par défaut. Assurez-vous de spécifier le port approprié dans la mesure où nous transitions par l’équilibreur de charge. (Pour notre première machine virtuelle, nous configurons la règle NAT pour transférer le port 4222 vers votre machine virtuelle.)

```bash
ssh ops@mypublicdns.westeurope.cloudapp.azure.com -p 4222 -i ~/.ssh/id_rsa.pub
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
az vm create \
    --resource-group myResourceGroup \
    --name myVM2 \
    --location westeurope \
    --availability-set myAvailabilitySet \
    --nics myNic2 \
    --image UbuntuLTS \
    --ssh-key-value ~/.ssh/id_rsa.pub \
    --admin-username azureuser
```

Là encore, si vous n’utilisez pas par défaut Azure Managed Disks, ajoutez les paramètres supplémentaires suivants à la commande de traitement pour créer les disques non gérés dans le compte de stockage nommé `mystorageaccount` :

```azurecli
  --use-unmanaged-disk \
  --storage-account mystorageaccount
``` 

À ce stade, vous exécutez vos machines virtuelles Ubuntu derrière un équilibreur de charge dans Azure auquel vous pouvez uniquement vous connecter avec votre paire de clés SSH (car les mots de passe sont désactivés). Vous pouvez installer nginx ou httpd, déployer une application web et voir le flux de trafic transitant par l’équilibreur de charge vers toutes les machines virtuelles.


## <a name="export-the-environment-as-a-template"></a>Exportation de l’environnement en tant que modèle
Maintenant que vous avez créé cet environnement, que se passe-t-il si vous souhaitez créer un environnement de développement supplémentaire avec les mêmes paramètres ou un environnement de production correspondant ? Resource Manager utilise des modèles JSON qui définissent tous les paramètres pour votre environnement. Vous créez des environnements entiers en faisant référence à ce modèle JSON. Vous pouvez [créer manuellement des modèles JSON](../azure-resource-manager/resource-group-authoring-templates.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json) ou exporter un environnement existant qui créera le modèle JSON pour vous. Utilisez la commande [az group export](/cli/azure/group#export) pour exporter votre groupe de ressources comme suit :

```azurecli
az group export --name myResourceGroup > myResourceGroup.json
```

Cette commande crée le fichier `myResourceGroup.json` dans votre répertoire de travail actuel. Lorsque vous créez un environnement à partir de ce modèle, vous êtes invité à fournir les noms de toutes les ressources, dont l’équilibreur de charge, les interfaces réseau ou les machines virtuelles. Vous pouvez entrer ces éléments dans votre fichier de modèle en ajoutant le paramètre `--include-parameter-default-value` à la commande **az group export** mentionnée précédemment. Modifiez votre modèle JSON pour spécifier les noms de ressources, ou [créez un fichier parameters.json](../azure-resource-manager/resource-group-authoring-templates.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json) qui spécifie les noms de ressources.

Pour créer un environnement à partir de votre modèle, utilisez la commande [az group deployment create](/cli/azure/group/deployment#create) comme suit :

```azurecli
az group deployment create --resource-group myNewResourceGroup \
  --template-file myResourceGroup.json
```

Vous pouvez lire la section contenant [plus de détails sur le déploiement à partir de modèles](../azure-resource-manager/resource-group-template-deploy-cli.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json). Découvrez notamment comment mettre à jour des environnements de manière incrémentielle, utiliser le fichier de paramètres et accéder aux modèles à partir d’un emplacement de stockage unique.

## <a name="next-steps"></a>Étapes suivantes
Vous voici en mesure de commencer à utiliser plusieurs composants réseau et machines virtuelles. Vous pouvez utiliser cet exemple d’environnement pour générer votre application en utilisant les composants de base présentés ici.

