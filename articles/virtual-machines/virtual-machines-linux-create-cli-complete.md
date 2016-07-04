<properties
   pageTitle="Création d’un environnement Linux complet à l’aide de l’interface de ligne de commande Azure | Microsoft Azure"
   description="Créez une machine virtuelle Linux, un stockage, un réseau virtuel et un sous-réseau, un équilibreur de charge, une carte d’interface réseau, une adresse IP publique et un Groupe de sécurité réseau à partir de zéro à l’aide de l’interface de ligne de commande Azure."
   services="virtual-machines-linux"
   documentationCenter="virtual-machines"
   authors="iainfoulds"
   manager="timlt"
   editor=""
   tags="azure-resource-manager"/>

<tags
   ms.service="virtual-machines-linux"
   ms.devlang="na"
   ms.topic="article"
   ms.tgt_pltfrm="vm-linux"
   ms.workload="infrastructure"
   ms.date="06/10/2016"
   ms.author="iainfou"/>

# Création d’un environnement Linux complet à l’aide de l’interface de ligne de commande Azure

Commençons par la procédure de création d’un réseau simple avec un équilibreur de charge et plusieurs machines virtuelles à des fins de développement et de calcul élémentaire. Vous devrez explorer la totalité de l’environnement de manière impérative, commande par commande, jusqu’à ce que vous disposiez de machines virtuelles Linux sécurisées opérationnelles auxquelles vous pouvez vous connecter à partir de n’importe quel emplacement d’Internet. Vous serez ensuite en mesure de créer des réseaux et un environnement plus complexes.

Au cours de ce processus, vous découvrirez la hiérarchie des dépendances et la puissance que vous offre le modèle de déploiement de Resource Manager. Dès que vous avez compris la façon dont le système est créé, vous pouvez reconstruire le système beaucoup plus rapidement en utilisant des [modèles Azure Resource Manager](../resource-group-authoring-templates.md). Dès que vous avez compris la façon dont les différentes parties de votre environnement s’imbriquent, la création de modèles pour automatiser ces dernières se révèle plus simple.

L’environnement contiendra les éléments suivants :

- Deux machines virtuelles dans un groupe à haute disponibilité
- Un équilibreur de charge avec une règle d’équilibrage de charge sur le port 80
- Des règles de groupe de sécurité réseau pour protéger votre machine virtuelle de tout trafic indésirable

![Vue d’ensemble de l’environnement de base](./media/virtual-machines-linux-create-cli-complete/environment_overview.png)

Pour créer cet environnement personnalisé, [l’interface de ligne de commande Azure](../xplat-cli-install.md) la plus récente doit être installée et être en mode Resource Manager (`azure config mode arm`). Vous aurez également besoin d’un outil d’analyse JSON : cet exemple utilise [jq](https://stedolan.github.io/jq/).

## Commandes rapides
Les commandes rapides suivantes sont utilisées pour créer votre environnement personnalisé. Pour obtenir des informations et une présentation détaillées sur ce que chaque commande fait lorsque vous créez l’environnement, lisez les [étapes détaillées ci-dessous](#detailed-walkthrough).

Créer le groupe de ressources

```bash
azure group create TestRG -l westeurope
```

Vérifier le groupe de ressources à l’aide de l’analyseur JSON

```bash
azure group show TestRG --json | jq '.'
```

Créer le compte de stockage

```bash
azure storage account create -g TestRG -l westeurope --kind Storage --sku-name GRS computeteststore
```

Vérifier le stockage à l’aide de l’analyseur JSON

```bash
azure storage account show -g TestRG computeteststore --json | jq '.'
```

création du réseau virtuel

```bash
azure network vnet create -g TestRG -n TestVNet -a 192.168.0.0/16 -l westeurope
```

Créer le sous-réseau

```bash
azure network vnet subnet create -g TestRG -e TestVNet -n FrontEnd -a 192.168.1.0/24
```

Vérifier le réseau virtuel et le sous-réseau à l’aide de l’analyseur JSON

```bash
azure network vnet show TestRG TestVNet --json | jq '.'
```

Créer une adresse IP publique

```bash
azure network public-ip create -g TestRG -n TestLBPIP -l westeurope -d testlb -a static -i 4
```

Créer l’équilibreur de charge

```bash
azure network lb create -g TestRG -n TestLB -l westeurope
```

Créer un pool d’adresses IP frontal pour l’équilibreur de charge et l’associer à notre adresse IP publique

```bash
azure network lb frontend-ip create -g TestRG -l TestLB -n TestFrontEndPool -i TestLBPIP
```

Créer notre pool d’adresses IP principal pour l’équilibreur de charge

```bash
azure network lb address-pool create -g TestRG -l TestLB -n TestBackEndPool
```

Créer des règles NAT entrantes SSH pour l’équilibreur de charge

```bash
azure network lb inbound-nat-rule create -g TestRG -l TestLB -n VM1-SSH -p tcp -f 4222 -b 22
azure network lb inbound-nat-rule create -g TestRG -l TestLB -n VM2-SSH -p tcp -f 4223 -b 22
```

Créer nos règles NAT entrantes Web pour l’équilibreur de charge

```bash
azure network lb rule create -g TestRG -l TestLB -n WebRule -p tcp -f 80 -b 80 \
     -t TestFrontEndPool -o TestBackEndPool
```

Créer notre sonde d’intégrité d’équilibreur de charge

```bash
azure network lb probe create -g TestRG -l TestLB -n HealthProbe -p "http" -f healthprobe.aspx -i 15 -c 4
```

Vérifier l’équilibreur de charge, les pools d’adresses IP et les règles NAT à l’aide de l’analyseur JSON

```bash
azure network lb show -g TestRG -n TestLB --json | jq '.'
```

Créer la première carte d’interface réseau

```bash
azure network nic create -g TestRG -n LB-NIC1 -l westeurope --subnet-vnet-name TestVNet --subnet-name FrontEnd \
    -d "/subscriptions/########-####-####-####-############/resourceGroups/TestRG/providers/Microsoft.Network/loadBalancers/TestLB/backendAddressPools/TestBackEndPool" \
    -e "/subscriptions/########-####-####-####-############/resourceGroups/TestRG/providers/Microsoft.Network/loadBalancers/TestLB/inboundNatRules/VM1-SSH"
```

Créer la deuxième carte d’interface réseau

```bash
azure network nic create -g TestRG -n LB-NIC2 -l westeurope --subnet-vnet-name TestVNet --subnet-name FrontEnd \
    -d "/subscriptions/########-####-####-####-############/resourceGroups/TestRG/providers/Microsoft.Network/loadBalancers/TestLB/backendAddressPools/TestBackEndPool" \
    -e "/subscriptions/########-####-####-####-############/resourceGroups/TestRG/providers/Microsoft.Network/loadBalancers/TestLB/inboundNatRules/VM2-SSH"
```

Vérifier les cartes d’interface réseau à l’aide de l’analyseur JSON

```bash
azure network nic show TestRG LB-NIC1 --json | jq '.'
azure network nic show TestRG LB-NIC2 --json | jq '.'
```

Créer le groupe de sécurité réseau

```bash
azure network nsg create -g TestRG -n TestNSG -l westeurope
```

Ajouter les règles de trafic entrant pour le groupe de sécurité réseau

```bash
azure network nsg rule create --protocol tcp --direction inbound --priority 1000 \
    --destination-port-range 22 --access allow -g TestRG -a TestNSG -n SSHRule
azure network nsg rule create --protocol tcp --direction inbound --priority 1001 \
    --destination-port-range 80 --access allow -g TestRG -a TestNSG -n HTTPRule
```

Vérifier le groupe de sécurité réseau et les règles de trafic entrant à l’aide de l’analyseur JSON

```bash
azure network nsg show -g TestRG -n TestNSG --json | jq '.'
```

Lier le groupe de sécurité réseau aux cartes d’interface réseau

```bash
azure network nic set -g TestRG -n LB-NIC1 -o TestNSG
azure network nic set -g TestRG -n LB-NIC2 -o TestNSG
```

Créer le groupe à haute disponibilité

```bash
azure availset create -g TestRG -n TestAvailSet -l westeurope
```

Créer la première machine virtuelle Linux

```bash
azure vm create \
    --resource-group TestRG \
    --name TestVM1 \
    --location westeurope \
    --os-type linux \
    --availset-name TestAvailSet \
    --nic-name LB-NIC1 \
    --vnet-name TestVnet \
    --vnet-subnet-name FrontEnd \
    --storage-account-name computeteststore \
    --image-urn canonical:UbuntuServer:14.04.4-LTS:latest \
    --ssh-publickey-file ~/.ssh/id_rsa.pub \
    --admin-username ops
```

Créer la deuxième machine virtuelle Linux

```bash
azure vm create \
    --resource-group TestRG \
    --name TestVM2 \
    --location westeurope \
    --os-type linux \
    --availset-name TestAvailSet \
    --nic-name LB-NIC2 \
    --vnet-name TestVnet \
    --vnet-subnet-name FrontEnd \
    --storage-account-name computeteststore \
    --image-urn canonical:UbuntuServer:14.04.4-LTS:latest \
    --ssh-publickey-file ~/.ssh/id_rsa.pub \
    --admin-username ops
```

Vérifier que tout est généré à l’aide de l’analyseur JSON

```bash
azure vm show -g TestRG -n TestVM1 --json | jq '.'
azure vm show -g TestRG -n TestVM2 --json | jq '.'
```

Exportez l’environnement que vous avez créée dans un modèle pour recréer rapidement des instances nouvelles :

```bash
azure resource export TestRG
```

## Procédure pas à pas
Ces étapes plus détaillées expliquent ce que chaque commande fait lorsque vous générez votre environnement, afin de vous aider à saisir ces concepts que vous utiliserez ensuite pour créer vos propres environnements personnalisés pour les charges de travail de développement ou de production.

## Créer un groupe de ressources et choisir les emplacements de déploiement

Les groupes de ressources Azure sont des entités de déploiement logiques qui contiennent des données de configuration et d’autres métadonnées pour permettre la gestion logique des déploiements de ressources.

```bash
azure group create TestRG westeurope
```

Sortie

```bash                        
info:    Executing command group create
+ Getting resource group TestRG
+ Creating resource group TestRG
info:    Created resource group TestRG
data:    Id:                  /subscriptions/<yoursub>/resourceGroups/TestRG
data:    Name:                TestRG
data:    Location:            westeurope
data:    Provisioning State:  Succeeded
data:    Tags: null
data:
info:    group create command OK
```

## Créer un compte de stockage

Entre autres scénarios, vous avez besoin de comptes de stockage pour vos disques de machine virtuelle et pour tous les disques de données que vous souhaitez ajouter. En résumé, vous créerez toujours des comptes de stockage presque immédiatement après avoir créé des groupes de ressources.

Ici, nous utilisons la commande `azure storage account create` pour transmettre l’emplacement du compte, le groupe de ressources qui le contrôlera, ainsi que le type de support de stockage souhaité.

```bash
azure storage account create \  
--location westeurope \
--resource-group TestRG \
--kind Storage --sku-name GRS \
computeteststore
```

Sortie

```bash
info:    Executing command storage account create
+ Creating storage account
info:    storage account create command OK
ahmet•~/workspace/keygen» azure group show testrg
info:    Executing command group show
+ Listing resource groups
+ Listing resources for the group
data:    Id:                  /subscriptions/<guid>/resourceGroups/TestRG
data:    Name:                TestRG
data:    Location:            westeurope
data:    Provisioning State:  Succeeded
data:    Tags: null
data:    Resources:
data:
data:      Id      : /subscriptions/<guid>/resourceGroups/TestRG/providers/Microsoft.Storage/storageAccounts/computeteststore
data:      Name    : computeteststore
data:      Type    : storageAccounts
data:      Location: westeurope
data:      Tags    :
data:
data:    Permissions:
data:      Actions: *
data:      NotActions:
data:
info:    group show command OK
```

Utilisons l’outil [jq](https://stedolan.github.io/jq/) (vous pouvez analyser la sortie JSON avec **jsawk** ou toute bibliothèque de langage de votre choix) avec l’option d’interface de ligne de commande Azure `--json` pour examiner notre groupe de ressources à l’aide de la commande `azure group show`.

```bash
azure group show TestRG --json | jq                                                                                      
```


Sortie

```bash
{
  "tags": {},
  "id": "/subscriptions/<guid>/resourceGroups/TestRG",
  "name": "TestRG",
  "provisioningState": "Succeeded",
  "location": "westeurope",
  "properties": {
    "provisioningState": "Succeeded"
  },
  "resources": [
    {
      "id": "/subscriptions/<guid>/resourceGroups/TestRG/providers/Microsoft.Storage/storageAccounts/computeteststore",
      "name": "computeteststore",
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

Pour examiner le compte de stockage au moyen de l’interface de ligne de commande, vous devez commencer par définir les noms et clés de compte à l’aide d’une variante de la commande suivante en remplaçant le nom du compte de stockage de cet article par celui de votre propre compte.

```
AZURE_STORAGE_CONNECTION_STRING="$(azure storage account connectionstring show computeteststore --resource-group testrg --json | jq -r '.string')"
```

Vous serez alors en mesure de visualiser facilement vos informations de stockage :

```bash
azure storage container list
```

Sortie

```bash
info:    Executing command storage container list
+ Getting storage containers
data:    Name  Public-Access  Last-Modified
data:    ----  -------------  -----------------------------
data:    vhds  Off            Sun, 27 Sep 2015 19:03:54 GMT
info:    storage container list command OK
```

## Créer votre réseau virtuel et votre sous-réseau

Vous devrez créer un réseau virtuel Azure et un sous-réseau dans lesquels vous pourrez installer votre machine virtuelle.

```bash
azure network vnet create -g TestRG -n TestVNet -a 192.168.0.0/16 -l westeurope
```

Sortie

```bash
info:    Executing command network vnet create
+ Looking up virtual network "TestVNet"
+ Creating virtual network "TestVNet"
+ Loading virtual network state
data:    Id                              : /subscriptions/<guid>/resourceGroups/TestRG/providers/Microsoft.Network/virtualNetworks/TestVNet
data:    Name                            : TestVNet
data:    Type                            : Microsoft.Network/virtualNetworks
data:    Location                        : westeurope
data:    ProvisioningState               : Succeeded
data:    Address prefixes:
data:      192.168.0.0/16
info:    network vnet create command OK
```

Là encore, examinons la façon dont nous créons nos ressources à l’aide de l’option --json de la commande `azure group show` et de **jq**. Nous disposons maintenant d’une ressource `storageAccounts` et d’une ressource `virtualNetworks`.

```bash
azure group show TestRG --json | jq '.'
```

Sortie

```bash
{
  "tags": {},
  "id": "/subscriptions/<guid>/resourceGroups/TestRG",
  "name": "TestRG",
  "provisioningState": "Succeeded",
  "location": "westeurope",
  "properties": {
    "provisioningState": "Succeeded"
  },
  "resources": [
    {
      "id": "/subscriptions/<guid>/resourceGroups/TestRG/providers/Microsoft.Network/virtualNetworks/TestVNet",
      "name": "TestVNet",
      "type": "virtualNetworks",
      "location": "westeurope",
      "tags": null
    },
    {
      "id": "/subscriptions/<guid>/resourceGroups/TestRG/providers/Microsoft.Storage/storageAccounts/computeteststore",
      "name": "computeteststore",
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

À présent, créons dans le réseau virtuel `TestVnet` un sous-réseau dans lequel les machines virtuelles seront déployées. Nous utilisons la commande `azure network vnet subnet create` avec les ressources que nous avons créées précédemment (le groupe de ressources `TestRG` et le réseau virtuel `TestVNet`), et nous ajoutons le nom de sous-réseau `FrontEnd` et le préfixe d’adresse de sous-réseau `192.168.1.0/24`, comme ci-dessous.

```bash
azure network vnet subnet create -g TestRG -e TestVNet -n FrontEnd -a 192.168.1.0/24
```

Sortie

```bash
info:    Executing command network vnet subnet create
+ Looking up the subnet "FrontEnd"
+ Creating subnet "FrontEnd"
+ Looking up the subnet "FrontEnd"
data:    Id                              : /subscriptions/<guid>/resourceGroups/TestRG/providers/Microsoft.Network/virtualNetworks/TestVNet/subnets/FrontEnd
data:    Type                            : Microsoft.Network/virtualNetworks/subnets
data:    ProvisioningState               : Succeeded
data:    Name                            : FrontEnd
data:    Address prefix                  : 192.168.1.0/24
data:
info:    network vnet subnet create command OK
```

Étant donné que le sous-réseau figure logiquement à l’intérieur du réseau virtuel, nous allons examiner les informations de sous-réseau avec une commande -- `azure network vnet show` légèrement différente, mais nous continuerons d’examiner la sortie JSON avec **jq**.

```bash
azure network vnet show TestRG TestVNet --json | jq '.'
```

Sortie

```bash
{
  "subnets": [
    {
      "ipConfigurations": [],
      "addressPrefix": "192.168.1.0/24",
      "provisioningState": "Succeeded",
      "name": "FrontEnd",
      "etag": "W/"974f3e2c-028e-4b35-832b-a4b16ad25eb6"",
      "id": "/subscriptions/<guid>/resourceGroups/TestRG/providers/Microsoft.Network/virtualNetworks/TestVNet/subnets/FrontEnd"
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
  "etag": "W/"974f3e2c-028e-4b35-832b-a4b16ad25eb6"",
  "id": "/subscriptions/<guid>/resourceGroups/TestRG/providers/Microsoft.Network/virtualNetworks/TestVNet",
  "name": "TestVNet",
  "location": "westeurope"
}
```

## Créer votre adresse IP publique (PIP)

À présent, créons votre adresse IP publique que nous attribuerons à votre équilibreur de charge et qui vous permettra de vous connecter à vos machines virtuelles à partir d’Internet en utilisant la commande `azure network public-ip create`. Étant donné que la valeur par défaut est une adresse dynamique, nous créons une entrée DNS nommée dans le domaine **cloudapp.azure.com** à l’aide de l’option `-d testsubdomain`.

```bash
azure network public-ip create -d testsubdomain TestRG TestPIP westeurope
```

Sortie

```bash
info:    Executing command network public-ip create
+ Looking up the public ip "TestPIP"
+ Creating public ip address "TestPIP"
+ Looking up the public ip "TestPIP"
data:    Id                              : /subscriptions/guid/resourceGroups/TestRG/providers/Microsoft.Network/publicIPAddresses/TestPIP
data:    Name                            : TestPIP
data:    Type                            : Microsoft.Network/publicIPAddresses
data:    Location                        : westeurope
data:    Provisioning state              : Succeeded
data:    Allocation method               : Dynamic
data:    Idle timeout                    : 4
data:    Domain name label               : testsubdomain
data:    FQDN                            : testsubdomain.westeurope.cloudapp.azure.com
info:    network public-ip create command OK
```

Puisqu’il s’agit également d’une ressource de niveau supérieur, vous pouvez la visualiser avec `azure group show`.

```bash
azure group show TestRG --json | jq '.'
```

Sortie

```bash
{
"tags": {},
"id": "/subscriptions/guid/resourceGroups/TestRG",
"name": "TestRG",
"provisioningState": "Succeeded",
"location": "westeurope",
"properties": {
    "provisioningState": "Succeeded"
},
"resources": [
    {
    "id": "/subscriptions/guid/resourceGroups/TestRG/providers/Microsoft.Network/networkInterfaces/TestNIC",
    "name": "TestNIC",
    "type": "networkInterfaces",
    "location": "westeurope",
    "tags": null
    },
    {
    "id": "/subscriptions/guid/resourceGroups/testrg/providers/Microsoft.Network/publicIPAddresses/testpip",
    "name": "testpip",
    "type": "publicIPAddresses",
    "location": "westeurope",
    "tags": null
    },
    {
    "id": "/subscriptions/guid/resourceGroups/TestRG/providers/Microsoft.Network/virtualNetworks/TestVNet",
    "name": "TestVNet",
    "type": "virtualNetworks",
    "location": "westeurope",
    "tags": null
    },
    {
    "id": "/subscriptions/guid/resourceGroups/TestRG/providers/Microsoft.Storage/storageAccounts/computeteststore",
    "name": "computeteststore",
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

Et, comme toujours, vous pouvez examiner les détails de la ressource, y compris le nom de domaine complet du sous-domaine, en utilisant la commande `azure network public-ip show` plus complète. Notez que la ressource d’adresse IP publique a été allouée de façon logique, mais qu’aucune adresse spécifique n’est encore affectée. Pour effectuer cette opération, vous aurez besoin d’un équilibreur de charge, que nous n’avons pas encore créé.

```bash
azure network public-ip show TestRG TestPIP --json | jq '.'
```

Sortie

```bash
{
"tags": {},
"publicIpAllocationMethod": "Dynamic",
"dnsSettings": {
    "domainNameLabel": "testsubdomain",
    "fqdn": "testsubdomain.westeurope.cloudapp.azure.com"
},
"idleTimeoutInMinutes": 4,
"provisioningState": "Succeeded",
"etag": "W/"c63154b3-1130-49b9-a887-877d74d5ebc5"",
"id": "/subscriptions/guid/resourceGroups/testrg/providers/Microsoft.Network/publicIPAddresses/testpip",
"name": "testpip",
"location": "westeurope"
}
```

## Créer votre équilibreur de charge et des pools d’adresses IP
Créer un équilibreur de charge vous permet de répartir le trafic entre plusieurs machines virtuelles, comme lors de l’exécution d’applications web. Cela assure également la redondance de votre application en exécutant plusieurs machines virtuelles qui répondent aux demandes des utilisateurs en cas de maintenance ou de lourde charge.

Nous créons notre équilibreur de charge avec :

```bash
azure network lb create -g TestRG -n TestLB -l westeurope
```

```bash
azure network lb create -g TestRG -n TestLB -l westeurope
```

Sortie

```bash
info:    Executing command network lb create
+ Looking up the load balancer "TestLB"
+ Creating load balancer "TestLB"
data:    Id                              : /subscriptions/guid/resourceGroups/TestRG/providers/Microsoft.Network/loadBalancers/TestLB
data:    Name                            : TestLB
data:    Type                            : Microsoft.Network/loadBalancers
data:    Location                        : westeurope
data:    Provisioning state              : Succeeded
info:    network lb create command OK
```
Notre équilibreur de charge est relativement vide. Nous allons donc créer des pools d’adresses IP. Nous souhaitons créer deux pools d’adresses IP pour notre équilibreur de charge : un pool frontal et un pool principal. Le pool d’adresses IP frontal désigne ce qui est visible publiquement et l’endroit où nous allons affecter le pool d’adresses IP créé précédemment. Nous utiliserons ensuite le pool principal pour que nos machines virtuelles puissent se connecter afin que le trafic traverse l’équilibreur de charge pour y accéder.

Commençons tout d’abord par créer notre pool d’adresses IP frontal :

```bash
azure network lb frontend-ip create -g TestRG -l TestLB -n TestFrontEndPool -i TestLBPIP
```

Sortie

```bash
info:    Executing command network lb frontend-ip create
+ Looking up the load balancer "TestLB"
+ Looking up the public ip "TestLBPIP"
+ Updating load balancer "TestLB"
data:    Name                            : TestFrontEndPool
data:    Provisioning state              : Succeeded
data:    Private IP allocation method    : Dynamic
data:    Public IP address id            : /subscriptions/guid/resourceGroups/TestRG/providers/Microsoft.Network/publicIPAddresses/TestLBPIP
info:    network lb frontend-ip create command OK
```

Notez comment nous avons utilisé le commutateur `--public-ip-name` pour transmettre le TestLBPIP créé précédemment. Cette action affecte l’adresse IP publique à l’équilibreur de charge pour nous permettre d’atteindre nos machines virtuelles sur Internet.

Nous allons maintenant créer notre deuxième pool d’adresses IP pour le trafic principal :

```bash
azure network lb address-pool create -g TestRG -l TestLB -n TestBackEndPool
```

Sortie

```bash
info:    Executing command network lb address-pool create
+ Looking up the load balancer "TestLB"
+ Updating load balancer "TestLB"
data:    Name                            : TestBackEndPool
data:    Provisioning state              : Succeeded
info:    network lb address-pool create command OK
```

Nous pouvons vérifier la façon dont notre équilibreur de charge apparaît avec `azure network lb show` et examine la sortie JSON :

```bash
azure network lb show TestRG TestLB --json | jq '.'
```

Sortie

```bash
{
  "etag": "W/"29c38649-77d6-43ff-ab8f-977536b0047c"",
  "provisioningState": "Succeeded",
  "resourceGuid": "f1446acb-09ba-44d9-b8b6-849d9983dc09",
  "outboundNatRules": [],
  "inboundNatPools": [],
  "inboundNatRules": [],
  "id": "/subscriptions/guid/resourceGroups/TestRG/providers/Microsoft.Network/loadBalancers/TestLB",
  "name": "TestLB",
  "type": "Microsoft.Network/loadBalancers",
  "location": "westeurope",
  "frontendIPConfigurations": [
    {
      "etag": "W/"29c38649-77d6-43ff-ab8f-977536b0047c"",
      "name": "TestFrontEndPool",
      "provisioningState": "Succeeded",
      "publicIPAddress": {
        "id": "/subscriptions/guid/resourceGroups/TestRG/providers/Microsoft.Network/publicIPAddresses/TestLBPIP"
      },
      "privateIPAllocationMethod": "Dynamic",
      "id": "/subscriptions/guid/resourceGroups/TestRG/providers/Microsoft.Network/loadBalancers/TestLB/frontendIPConfigurations/TestFrontEndPool"
    }
  ],
  "backendAddressPools": [
    {
      "etag": "W/"29c38649-77d6-43ff-ab8f-977536b0047c"",
      "name": "TestBackEndPool",
      "provisioningState": "Succeeded",
      "id": "/subscriptions/guid/resourceGroups/TestRG/providers/Microsoft.Network/loadBalancers/TestLB/backendAddressPools/TestBackEndPool"
    }
  ],
  "loadBalancingRules": [],
  "probes": []
}
```

## Créer vos règles NAT d’équilibreur de charge
Pour que le trafic transite par notre équilibreur de charge, nous devons créer des règles NAT spécifiant les actions entrantes ou sortantes. Vous pouvez spécifier le protocole utilisé, puis mapper les ports externes aux ports internes comme vous le souhaitez. Pour notre environnement, nous allons créer des règles autorisant SSH à accéder à nos machines virtuelles par le biais de notre équilibreur de charge. Nous allons configurer les ports TCP 4222 et 4223 de manière à orienter le trafic vers le port TCP 22 sur nos machines virtuelles (que nous créerons plus tard) :

```bash
azure network lb inbound-nat-rule create -g TestRG -l TestLB -n VM1-SSH -p tcp -f 4222 -b 22
```

Sortie

```bash
info:    Executing command network lb inbound-nat-rule create
+ Looking up the load balancer "TestLB"
warn:    Using default enable floating ip: false
warn:    Using default idle timeout: 4
warn:    Using default frontend IP configuration "TestFrontEndPool"
+ Updating load balancer "TestLB"
data:    Name                            : VM1-SSH
data:    Provisioning state              : Succeeded
data:    Protocol                        : Tcp
data:    Frontend port                   : 4222
data:    Backend port                    : 22
data:    Enable floating IP              : false
data:    Idle timeout in minutes         : 4
data:    Frontend IP configuration id    : /subscriptions/guid/resourceGroups/TestRG/providers/Microsoft.Network/loadBalancers/TestLB/frontendIPConfigurations/TestFrontEndPool
info:    network lb inbound-nat-rule create command OK
```

Répétez cette procédure pour votre deuxième règle NAT pour le protocole SSH :

```bash
azure network lb inbound-nat-rule create -g TestRG -l TestLB -n VM2-SSH -p tcp -f 4223 -b 22
```

Nous allons maintenant créer une règle NAT pour le port TCP 80, en liant la règle à nos pools d’adresses IP. Au lieu de lier la règle à nos machines virtuelles de manière individuelle, nous pouvons simplement ajouter ou supprimer des machines virtuelles du pool d’adresses IP et faire en sorte que l’équilibreur de charge ajuste automatiquement le flux de trafic :

```bash
azure network lb rule create -g TestRG -l TestLB -n WebRule -p tcp -f 80 -b 80 \
     -t TestFrontEndPool -o TestBackEndPool
```

Sortie

```bash
info:    Executing command network lb rule create
+ Looking up the load balancer "TestLB"
warn:    Using default idle timeout: 4
warn:    Using default enable floating ip: false
warn:    Using default load distribution: Default
+ Updating load balancer "TestLB"
data:    Name                            : WebRule
data:    Provisioning state              : Succeeded
data:    Protocol                        : Tcp
data:    Frontend port                   : 80
data:    Backend port                    : 80
data:    Enable floating IP              : false
data:    Load distribution               : Default
data:    Idle timeout in minutes         : 4
data:    Frontend IP configuration id    : /subscriptions/guid/resourceGroups/TestRG/providers/Microsoft.Network/loadBalancers/TestLB/frontendIPConfigurations/TestFrontEndPool
data:    Backend address pool id         : /subscriptions/guid/resourceGroups/TestRG/providers/Microsoft.Network/loadBalancers/TestLB/backendAddressPools/TestBackEndPool
info:    network lb rule create command OK
```

## Créer votre sonde d’intégrité d’équilibreur de charge

Une sonde d’intégrité contrôle régulièrement les machines virtuelles situées derrière notre équilibreur de charge pour s’assurer qu’elles fonctionnent correctement et répondent aux demandes, telles que définies. Dans le cas contraire, elles seront supprimées du processus pour garantir que les utilisateurs ne seront pas orientés vers elles. Vous pouvez définir les contrôles personnalisés de la sonde d’intégrité, ainsi que des valeurs d’intervalle et de délai d’attente. Pour plus d’informations sur les sondes d’intégrité, consultez la section [Sondes d’équilibreur de charge](../load-balancer/load-balancer-custom-probe-overview.md).

```bash
azure network lb probe create -g TestRG -l TestLB -n HealthProbe -p "http" -f healthprobe.aspx -i 15 -c 4
```

Sortie

```bash
info:    Executing command network lb probe create
warn:    Using default probe port: 80
+ Looking up the load balancer "TestLB"
+ Updating load balancer "TestLB"
data:    Name                            : HealthProbe
data:    Provisioning state              : Succeeded
data:    Protocol                        : Http
data:    Port                            : 80
data:    Interval in seconds             : 15
data:    Number of probes                : 4
info:    network lb probe create command OK
```

Ici, nous avons spécifié un intervalle de 15 secondes pour nos contrôles d’intégrité et nous pouvons manquer jusqu’à 4 sondes (1 minute) avant que l’équilibreur de charge ne considère que l’hôte n’est plus opérationnel.

## Vérifier votre équilibreur de charge
La configuration de l’équilibreur de charge est maintenant terminée. Vous avez créé un équilibreur de charge et un pool d’adresses IP frontal auquel vous avez affecté une adresse IP publique, puis vous avez créé un pool d’adresses IP principal auquel les machines virtuelles seront connectées. Ensuite, vous avez créé des règles NAT qui vont autoriser le protocole SSH pour les machines virtuelles à des fins de gestion, ainsi qu’une règle autorisant le port TCP 80 de notre application web. Enfin, pour vous assurer que nos utilisateurs ne tentent pas d’accéder à une machine virtuelle qui ne fonctionne plus et ne diffuse plus de contenu, vous avez ajouté une sonde d’intégrité afin de vérifier régulièrement les machines virtuelles.

Regardons maintenant à quoi ressemble votre équilibreur de charge :

```bash
azure network lb show -g TestRG -n TestLB --json | jq '.'
```

Sortie

```bash
{
  "etag": "W/"62a7c8e7-859c-48d3-8e76-5e078c5e4a02"",
  "provisioningState": "Succeeded",
  "resourceGuid": "f1446acb-09ba-44d9-b8b6-849d9983dc09",
  "outboundNatRules": [],
  "inboundNatPools": [],
  "inboundNatRules": [
    {
      "etag": "W/"62a7c8e7-859c-48d3-8e76-5e078c5e4a02"",
      "name": "VM1-SSH",
      "id": "/subscriptions/guid/resourceGroups/TestRG/providers/Microsoft.Network/loadBalancers/TestLB/inboundNatRules/VM1-SSH",
      "frontendIPConfiguration": {
        "id": "/subscriptions/guid/resourceGroups/TestRG/providers/Microsoft.Network/loadBalancers/TestLB/frontendIPConfigurations/TestFrontEndPool"
      },
      "protocol": "Tcp",
      "frontendPort": 4222,
      "backendPort": 22,
      "idleTimeoutInMinutes": 4,
      "enableFloatingIP": false,
      "provisioningState": "Succeeded"
    },
    {
      "etag": "W/"62a7c8e7-859c-48d3-8e76-5e078c5e4a02"",
      "name": "VM2-SSH",
      "id": "/subscriptions/guid/resourceGroups/TestRG/providers/Microsoft.Network/loadBalancers/TestLB/inboundNatRules/VM2-SSH",
      "frontendIPConfiguration": {
        "id": "/subscriptions/guid/resourceGroups/TestRG/providers/Microsoft.Network/loadBalancers/TestLB/frontendIPConfigurations/TestFrontEndPool"
      },
      "protocol": "Tcp",
      "frontendPort": 4223,
      "backendPort": 22,
      "idleTimeoutInMinutes": 4,
      "enableFloatingIP": false,
      "provisioningState": "Succeeded"
    }
  ],
  "id": "/subscriptions/guid/resourceGroups/TestRG/providers/Microsoft.Network/loadBalancers/TestLB",
  "name": "TestLB",
  "type": "Microsoft.Network/loadBalancers",
  "location": "westeurope",
  "frontendIPConfigurations": [
    {
      "etag": "W/"62a7c8e7-859c-48d3-8e76-5e078c5e4a02"",
      "name": "TestFrontEndPool",
      "provisioningState": "Succeeded",
      "publicIPAddress": {
        "id": "/subscriptions/guid/resourceGroups/TestRG/providers/Microsoft.Network/publicIPAddresses/TestLBPIP"
      },
      "privateIPAllocationMethod": "Dynamic",
      "loadBalancingRules": [
        {
          "id": "/subscriptions/guid/resourceGroups/TestRG/providers/Microsoft.Network/loadBalancers/TestLB/loadBalancingRules/WebRule"
        }
      ],
      "inboundNatRules": [
        {
          "id": "/subscriptions/guid/resourceGroups/TestRG/providers/Microsoft.Network/loadBalancers/TestLB/inboundNatRules/VM1-SSH"
        },
        {
          "id": "/subscriptions/guid/resourceGroups/TestRG/providers/Microsoft.Network/loadBalancers/TestLB/inboundNatRules/VM2-SSH"
        }
      ],
      "id": "/subscriptions/guid/resourceGroups/TestRG/providers/Microsoft.Network/loadBalancers/TestLB/frontendIPConfigurations/TestFrontEndPool"
    }
  ],
  "backendAddressPools": [
    {
      "etag": "W/"62a7c8e7-859c-48d3-8e76-5e078c5e4a02"",
      "name": "TestBackEndPool",
      "provisioningState": "Succeeded",
      "loadBalancingRules": [
        {
          "id": "/subscriptions/guid/resourceGroups/TestRG/providers/Microsoft.Network/loadBalancers/TestLB/loadBalancingRules/WebRule"
        }
      ],
      "id": "/subscriptions/guid/resourceGroups/TestRG/providers/Microsoft.Network/loadBalancers/TestLB/backendAddressPools/TestBackEndPool"
    }
  ],
  "loadBalancingRules": [
    {
      "etag": "W/"62a7c8e7-859c-48d3-8e76-5e078c5e4a02"",
      "name": "WebRule",
      "provisioningState": "Succeeded",
      "enableFloatingIP": false,
      "id": "/subscriptions/guid/resourceGroups/TestRG/providers/Microsoft.Network/loadBalancers/TestLB/loadBalancingRules/WebRule",
      "frontendIPConfiguration": {
        "id": "/subscriptions/guid/resourceGroups/TestRG/providers/Microsoft.Network/loadBalancers/TestLB/frontendIPConfigurations/TestFrontEndPool"
      },
      "backendAddressPool": {
        "id": "/subscriptions/guid/resourceGroups/TestRG/providers/Microsoft.Network/loadBalancers/TestLB/backendAddressPools/TestBackEndPool"
      },
      "protocol": "Tcp",
      "loadDistribution": "Default",
      "frontendPort": 80,
      "backendPort": 80,
      "idleTimeoutInMinutes": 4
    }
  ],
  "probes": [
    {
      "etag": "W/"62a7c8e7-859c-48d3-8e76-5e078c5e4a02"",
      "id": "/subscriptions/guid/resourceGroups/TestRG/providers/Microsoft.Network/loadBalancers/TestLB/probes/HealthProbe",
      "protocol": "Http",
      "port": 80,
      "intervalInSeconds": 15,
      "numberOfProbes": 4,
      "requestPath": "healthprobe.aspx",
      "provisioningState": "Succeeded",
      "name": "HealthProbe"
    }
  ]
}
```

## Créer une carte d’interface réseau (NIC) à utiliser avec la machine virtuelle Linux

La disponibilité des NIC elles-mêmes peut être déterminée par programme, car vous pouvez avoir plusieurs NIC et définir des règles régissant leur utilisation. Notez que dans la commande `azure network nic create` suivante, vous avez lié notre carte d’interface réseau au pool d’adresses IP principal de charge et avez procédé à l’association avec notre règle NAT pour autoriser le trafic SSH. Pour ce faire, vous devez spécifier votre ID d’abonnement Azure à la place de `<GUID>` :

```bash
azure network nic create -g TestRG -n LB-NIC1 -l westeurope --subnet-vnet-name TestVNet --subnet-name FrontEnd \
     -d /subscriptions/<GUID>/resourceGroups/TestRG/providers/Microsoft.Network/loadBalancers/TestLB/backendAddressPools/TestBackEndPool \
     -e /subscriptions/<GUID>/resourceGroups/TestRG/providers/Microsoft.Network/loadBalancers/TestLB/inboundNatRules/VM1-SSH
```

Sortie

```bash 
info:    Executing command network nic create
+ Looking up the subnet "FrontEnd"
+ Looking up the network interface "LB-NIC1"
+ Creating network interface "LB-NIC1"
data:    Id                              : /subscriptions/guid/resourceGroups/TestRG/providers/Microsoft.Network/networkInterfaces/LB-NIC1
data:    Name                            : LB-NIC1
data:    Type                            : Microsoft.Network/networkInterfaces
data:    Location                        : westeurope
data:    Provisioning state              : Succeeded
data:    Enable IP forwarding            : false
data:    IP configurations:
data:      Name                          : Nic-IP-config
data:      Provisioning state            : Succeeded
data:      Private IP address            : 192.168.1.4
data:      Private IP allocation method  : Dynamic
data:      Subnet                        : /subscriptions/guid/resourceGroups/TestRG/providers/Microsoft.Network/virtualNetworks/TestVNet/subnets/FrontEnd
data:      Load balancer backend address pools:
data:        Id                          : /subscriptions/guid/resourceGroups/TestRG/providers/Microsoft.Network/loadBalancers/TestLB/backendAddressPools/TestBackEndPool
data:      Load balancer inbound NAT rules:
data:        Id                          : /subscriptions/guid/resourceGroups/TestRG/providers/Microsoft.Network/loadBalancers/TestLB/inboundNatRules/VM1-SSH
data:
info:    network nic create command OK
```

Vous pouvez visualiser les détails de la ressource en examinant directement cette dernière à l’aide de la commande `azure network nic show`.

```bash
azure network nic show TestRG LB-NIC1 --json | jq '.'
```

Sortie

```bash
{
  "etag": "W/"fc1eaaa1-ee55-45bd-b847-5a08c7f4264a"",
  "provisioningState": "Succeeded",
  "id": "/subscriptions/guid/resourceGroups/TestRG/providers/Microsoft.Network/networkInterfaces/LB-NIC1",
  "name": "LB-NIC1",
  "type": "Microsoft.Network/networkInterfaces",
  "location": "westeurope",
  "ipConfigurations": [
    {
      "etag": "W/"fc1eaaa1-ee55-45bd-b847-5a08c7f4264a"",
      "id": "/subscriptions/guid/resourceGroups/TestRG/providers/Microsoft.Network/networkInterfaces/LB-NIC1/ipConfigurations/Nic-IP-config",
      "loadBalancerBackendAddressPools": [
        {
          "id": "/subscriptions/guid/resourceGroups/TestRG/providers/Microsoft.Network/loadBalancers/TestLB/backendAddressPools/TestBackEndPool"
        }
      ],
      "loadBalancerInboundNatRules": [
        {
          "id": "/subscriptions/guid/resourceGroups/TestRG/providers/Microsoft.Network/loadBalancers/TestLB/inboundNatRules/VM1-SSH"
        }
      ],
      "privateIPAddress": "192.168.1.4",
      "privateIPAllocationMethod": "Dynamic",
      "subnet": {
        "id": "/subscriptions/guid/resourceGroups/TestRG/providers/Microsoft.Network/virtualNetworks/TestVNet/subnets/FrontEnd"
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

Voyons maintenant comment créer la deuxième carte d’interface réseau, en effectuant une nouvelle liaison avec notre pool d’adresses IP principal et notre second ensemble de règles NAT pour autoriser le trafic SSH :

```bash
azure network nic create -g TestRG -n LB-NIC2 -l westeurope --subnet-vnet-name TestVNet --subnet-name FrontEnd \
    -d  /subscriptions/<GUID>/resourceGroups/TestRG/providers/Microsoft.Network/loadBalancers/TestLB/backendAddressPools/TestBackEndPool \
    -e /subscriptions/<GUID>/resourceGroups/TestRG/providers/Microsoft.Network/loadBalancers/TestLB/inboundNatRules/VM2-SSH
```

## Créer votre Groupe de sécurité réseau et les règles associées

Nous allons à présent créer votre groupe de sécurité réseau et les règles de trafic entrant qui régissent l’accès à la carte réseau.

```bash
azure network nsg create TestRG TestNSG westeurope
```

Ajoutons la règle de trafic entrant pour le NSG afin d’autoriser les connexions entrantes sur le port 22 (pour prendre en charge le protocole SSH) :

```bash
azure network nsg rule create --protocol tcp --direction inbound --priority 1000 \
    --destination-port-range 22 --access allow TestRG TestNSG SSHRule
```

```bash
azure network nsg rule create --protocol tcp --direction inbound --priority 1001 \
    --destination-port-range 80 --access allow -g TestRG -a TestNSG -n HTTPRule
```

> [AZURE.NOTE] La règle de trafic entrant est un filtre pour les connexions réseau entrantes. Dans cet exemple, nous allons lier le NSG à la carte d’interface réseau virtuelle des machines virtuelles, ce qui signifie que toute demande adressée au port 22 sera transmise par l’intermédiaire de la carte d’interface réseau sur notre machine virtuelle. Étant donné qu’il s’agit d’une règle concernant une connexion réseau, et non un point de terminaison comme dans les déploiements classiques, afin d’ouvrir un port, vous devez laisser la commande `--source-port-range` définie sur « * » (valeur par défaut) afin d’accepter les demandes entrantes à partir de **tous** les ports demandeurs, qui sont généralement dynamiques.

## Liaison avec la carte d’interface réseau

Liez le groupe de sécurité réseau aux cartes d’interface réseau :

```bash
azure network nic set -g TestRG -n LB-NIC1 -o TestNSG
```

```bash
azure network nic set -g TestRG -n LB-NIC2 -o TestNSG
```

## Créer un groupe à haute disponibilité
Les groupes à haute disponibilité aident à diffuser vos machines virtuelles sur ce que l’on appelle des domaines d’erreur et des domaines de mise à niveau. Nous allons maintenant voir comment créer un groupe à haute disponibilité pour vos machines virtuelles :

```bash
azure availset create -g TestRG -n TestAvailSet -l westeurope
```

Les domaines d’erreur désignent un groupe de machines virtuelles partageant une source d’alimentation et un commutateur réseau communs. Par défaut, les machines virtuelles configurées dans votre groupe à haute disponibilité sont réparties entre trois domaines de défaillance au maximum. Ainsi, en cas de problème matériel dans l’un de ces domaines d’erreur, toutes les machines virtuelles exécutant votre application n’en seront pas affectées. Azure distribue automatiquement les machines virtuelles sur les domaines d’erreur lorsque vous les placez dans un groupe à haute disponibilité.

Les domaines de mise à niveau indiquent les groupes de machines virtuelles et les équipements physiques sous-jacents pouvant être redémarrés en même temps. L’ordre de redémarrage des domaines de mise à jour ne peut être traité de manière séquentielle au cours de la maintenance planifiée, mais les mises à jour seront redémarrées une par une. Cette fois encore, Azure distribue automatiquement vos machines virtuelles entre les domaines de mise à niveau lorsqu’elles se trouvent dans un site de disponibilité.

Vous pouvez obtenir des informations supplémentaires sur [la gestion de la disponibilité des machines virtuelles](./virtual-machines-linux-manage-availability.md)

## Créer vos machines virtuelles Linux

Vous avez créé les ressources de stockage et de réseau pour prendre en charge des machines virtuelles accessibles par Internet. Créons maintenant ces machines virtuelles et sécurisons-les avec une clé SSH sans mot de passe. Dans ce cas précis, nous allons créer une machine virtuelle Ubuntu basée sur la dernière version LTS. Nous rechercherons les informations de cette image en utilisant `azure vm image list`, comme décrit dans [Recherche d’images de machine virtuelle Azure](virtual-machines-linux-cli-ps-findimage.md). Nous avons sélectionné une image à l’aide de la commande `azure vm image list westeurope canonical | grep LTS`, et dans ce cas précis, nous utiliserons `canonical:UbuntuServer:14.04.4-LTS:14.04.201604060`. Toutefois, pour le dernier champ, nous transmettrons `latest`, ce qui nous permettra d’obtenir systématiquement la build la plus récente à l’avenir (nous utiliserons la chaîne `canonical:UbuntuServer:14.04.4-LTS:14.04.201604060`).

> [AZURE.NOTE] Cette étape est familière à toute personne ayant déjà créé une paire de clés publique et privée SSH RSA sur Linux ou Mac à l’aide de la commande **ssh-keygen -t rsa -b 2048**. Si vous ne disposez pas de toutes les paires de clés de certificat dans votre répertoire `~/.ssh`, vous pouvez les créer de deux manières : <br /> 1. automatiquement à l’aide de l’option `azure vm create --generate-ssh-keys` ; 2. manuellement en suivant [les instructions pour les créer vous-même](virtual-machines-linux-ssh-from-linux.md). <br /> Vous pouvez également utiliser les options `azure vm create --admin-username --admin-password` pour utiliser la méthode généralement moins sécurisée d’authentification de vos connexions SSH par nom d’utilisateur et mot de passe une fois la machine virtuelle créée.

Nous créons la machine virtuelle en regroupant toutes nos ressources et informations avec la commande `azure vm create`.

```bash
azure vm create \            
    --resource-group TestRG \
    --name TestVM1 \
    --location westeurope \
    --os-type linux \
    --availset-name TestAvailSet \
    --nic-name LB-NIC1 \
    --vnet-name TestVnet \
    --vnet-subnet-name FrontEnd \
    --storage-account-name computeteststore \
    --image-urn canonical:UbuntuServer:14.04.4-LTS:latest \
    --ssh-publickey-file ~/.ssh/id_rsa.pub \
    --admin-username ops
```

Sortie

```bash
info:    Executing command vm create
+ Looking up the VM "TestVM1"
info:    Verifying the public key SSH file: /home/ifoulds/.ssh/id_rsa.pub
info:    Using the VM Size "Standard_A1"
info:    The [OS, Data] Disk or image configuration requires storage account
+ Looking up the storage account computeteststore
+ Looking up the availability set "TestAvailSet"
info:    Found an Availability set "TestAvailSet"
+ Looking up the NIC "LB-NIC1"
info:    Found an existing NIC "LB-NIC1"
info:    Found an IP configuration with virtual network subnet id "/subscriptions/guid/resourceGroups/TestRG/providers/Microsoft.Network/virtualNetworks/TestVNet/subnets/FrontEnd" in the NIC "LB-NIC1"
info:    This is an NIC without publicIP configured
info:    The storage URI 'https://computeteststore.blob.core.windows.net/' will be used for boot diagnostics settings, and it can be overwritten by the parameter input of '--boot-diagnostics-storage-uri'.
info:    vm create command OK
```

Immédiatement, vous pouvez vous connecter à votre machine virtuelle à l’aide de vos clés SSH par défaut, en veillant à spécifier le port approprié dans la mesure où nous transitons par l’équilibreur de charge (pour notre première machine virtuelle, nous configurons la règle NAT pour transférer le port 4222 vers notre machine virtuelle) :

```bash
 ssh ops@testlb.westeurope.cloudapp.azure.com -p 4222
```

Sortie

```bash
The authenticity of host '[testlb.westeurope.cloudapp.azure.com]:4222 ([xx.xx.xx.xx]:4222)' can't be established.
ECDSA key fingerprint is 94:2d:d0:ce:6b:fb:7f:ad:5b:3c:78:93:75:82:12:f9.
Are you sure you want to continue connecting (yes/no)? yes
Warning: Permanently added '[testlb.westeurope.cloudapp.azure.com]:4222,[xx.xx.xx.xx]:4222' (ECDSA) to the list of known hosts.
Welcome to Ubuntu 14.04.4 LTS (GNU/Linux 3.19.0-58-generic x86_64)

 * Documentation:  https://help.ubuntu.com/

  System information as of Wed Apr 27 23:44:06 UTC 2016

  System load: 0.37              Memory usage: 5%   Processes:       81
  Usage of /:  37.3% of 1.94GB   Swap usage:   0%   Users logged in: 0

  Graph this data and manage this system at:
    https://landscape.canonical.com/

  Get cloud support with Ubuntu Advantage Cloud Guest:
    http://www.ubuntu.com/business/services/cloud

0 packages can be updated.
0 updates are security updates.



The programs included with the Ubuntu system are free software;
the exact distribution terms for each program are described in the
individual files in /usr/share/doc/*/copyright.

Ubuntu comes with ABSOLUTELY NO WARRANTY, to the extent permitted by
applicable law.

ops@TestVM1:~$
```

Vous allez maintenant créer votre deuxième machine virtuelle en procédant de la même manière :

```bash
azure vm create \            
    --resource-group TestRG \
    --name TestVM2 \
    --location westeurope \
    --os-type linux \
    --availset-name TestAvailSet \
    --nic-name LB-NIC2 \
    --vnet-name TestVnet \
    --vnet-subnet-name FrontEnd \
    --storage-account-name computeteststore \
    --image-urn canonical:UbuntuServer:14.04.4-LTS:latest \
    --ssh-publickey-file ~/.ssh/id_rsa.pub \
    --admin-username ops
```

Et vous pouvez à présent utiliser la commande `azure vm show testrg testvm` pour examiner ce que vous avez créé. À ce stade, vos machines virtuelles Ubuntu opérationnelles se trouvent derrière un équilibreur de charge dans Azure auquel vous pouvez uniquement vous connecter avec la paire de clés SSH que vous possédez ; les mots de passe sont désactivés. Vous pouvez installer nginx ou httpd et déployer une application web et voir le flux de trafic transitant par l’équilibreur de charge vers toutes les machines virtuelles.

```bash
azure vm show TestRG TestVM1
```

Sortie

```bash
info:    Executing command vm show
+ Looking up the VM "TestVM1"
+ Looking up the NIC "LB-NIC1"
data:    Id                              :/subscriptions/8fa5cd83-7fbb-431a-af16-4a20dede8802/resourceGroups/testrg/providers/Microsoft.Compute/virtualMachines/TestVM1
data:    ProvisioningState               :Succeeded
data:    Name                            :TestVM1
data:    Location                        :westeurope
data:    Type                            :Microsoft.Compute/virtualMachines
data:
data:    Hardware Profile:
data:      Size                          :Standard_A1
data:
data:    Storage Profile:
data:      Image reference:
data:        Publisher                   :canonical
data:        Offer                       :UbuntuServer
data:        Sku                         :14.04.4-LTS
data:        Version                     :latest
data:
data:      OS Disk:
data:        OSType                      :Linux
data:        Name                        :cli1cca1d20a1dcf56c-os-1461800591317
data:        Caching                     :ReadWrite
data:        CreateOption                :FromImage
data:        Vhd:
data:          Uri                       :https://computeteststore.blob.core.windows.net/vhds/cli1cca1d20a1dcf56c-os-1461800591317.vhd
data:
data:    OS Profile:
data:      Computer Name                 :TestVM1
data:      User Name                     :ops
data:      Linux Configuration:
data:        Disable Password Auth       :true
data:
data:    Network Profile:
data:      Network Interfaces:
data:        Network Interface #1:
data:          Primary                   :true
data:          MAC Address               :00-0D-3A-20-F8-8B
data:          Provisioning State        :Succeeded
data:          Name                      :LB-NIC1
data:          Location                  :westeurope
data:
data:    AvailabilitySet:
data:      Id                            :/subscriptions/guid/resourceGroups/testrg/providers/Microsoft.Compute/availabilitySets/TESTAVAILSET
data:
data:    Diagnostics Profile:
data:      BootDiagnostics Enabled       :true
data:      BootDiagnostics StorageUri    :https://computeteststore.blob.core.windows.net/
data:
data:      Diagnostics Instance View:
info:    vm show command OK
```


## Exportation de l’environnement en tant que modèle
Maintenant que vous avez créé cet environnement, que se passe-t-il si vous souhaitez créer un environnement de développement supplémentaire avec les mêmes paramètres ou si vous souhaitez maintenant créer un environnement de production correspondant ? Resource Manager utilise des modèles JSON qui définissent tous les paramètres pour votre environnement, ce qui vous permet de développer des environnements entiers en faisant référence à ce modèle JSON. Vous pouvez [créer manuellement des modèles JSON](../resource-group-authoring-templates.md) ou exporter simplement un environnement existant qui créera le modèle JSON pour vous :

```bash
azure group export TestRG
```

Le fichier `TestRG.json` est créé dans votre répertoire de travail actuel. Lorsque vous créez ensuite un nouvel environnement à partir de ce modèle, vous êtes invité à fournir tous les noms des ressources comme pour l’équilibreur de charge, les interfaces réseau, les machines virtuelles, etc. Vous pouvez remplir ces éléments dans votre fichier de modèle en ajoutant `-p` ou `--includeParameterDefaultValue` à la commande `azure group export` indiquée ci-dessus, en modifiant votre modèle JSON pour spécifier les noms des ressources ou en [créant un fichier parameters.json](../resource-group-authoring-templates.md#parameters) qui spécifie uniquement les noms des ressources.

Pour créer un nouvel environnement à partir de votre modèle :

```bash
azure group deployment create -f TestRG.json -g NewRGFromTemplate
```

Vous pouvez lire la section contenant [plus de détails sur le déploiement à partir de modèles](../resource-group-template-deploy-cli.md), notamment comment mettre à jour des environnements de manière incrémentielle, utiliser le fichier de paramètres et accéder aux modèles à partir d’un emplacement de stockage unique.

## Étapes suivantes

Vous voici en mesure de commencer à utiliser plusieurs composants réseau et machines virtuelles. Vous pouvez utiliser cet exemple d’environnement pour générer votre application en utilisant les composants de base présentés ici.

<!---HONumber=AcomDC_0622_2016-->