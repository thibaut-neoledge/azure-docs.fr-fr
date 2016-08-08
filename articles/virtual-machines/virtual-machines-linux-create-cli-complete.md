<properties
   pageTitle="Création d’un environnement Linux complet à l’aide de l’interface de ligne de commande Azure | Microsoft Azure"
   description="Créez un stockage, une machine virtuelle Linux, un réseau virtuel et un sous-réseau, un équilibreur de charge, une carte d’interface réseau, une adresse IP publique et un groupe de sécurité réseau à partir de zéro à l’aide de l’interface de ligne de commande Azure."
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

Dans cet article, nous commencerons par créer un réseau simple avec un équilibreur de charge et plusieurs machines virtuelles à des fins de développement et de calcul élémentaire. Nous explorerons ce processus, commande par commande, jusqu’à ce que vous disposiez de deux machines virtuelles Linux sécurisées opérationnelles auxquelles vous pouvez vous connecter à partir de n’importe quel emplacement d’Internet. Vous pourrez ensuite créer des réseaux et des environnements plus complexes.

Au cours de ce processus, vous découvrirez la hiérarchie des dépendances et la puissance que vous offre le modèle de déploiement de Resource Manager. Une fois que vous avez compris la façon dont le système est créé, vous pouvez le reconstruire beaucoup plus rapidement en utilisant des [modèles Azure Resource Manager](../resource-group-authoring-templates.md). De même, après avoir compris la façon dont les différentes parties de votre environnement s’imbriquent, la création de modèles pour automatiser ces dernières se révèle plus simple.

L’environnement contient :

- Deux machines virtuelles dans un groupe à haute disponibilité.
- Un équilibreur de charge avec une règle d’équilibrage de charge sur le port 80.
- Des règles de groupe de sécurité réseau (NSG) pour protéger votre machine virtuelle de tout trafic indésirable.

![Vue d’ensemble de l’environnement de base](./media/virtual-machines-linux-create-cli-complete/environment_overview.png)

Pour créer cet environnement personnalisé, [l’interface de ligne de commande Azure](../xplat-cli-install.md) la plus récente doit être installée en mode Resource Manager (`azure config mode arm`). Vous avez également besoin d’un outil d’analyse JSON. Cet exemple utilise [jq](https://stedolan.github.io/jq/).

## Commandes rapides
Vous pouvez utiliser les commandes rapides suivantes pour créer votre environnement personnalisé. Pour plus d’informations sur ce que chaque commande fait lorsque vous créez l’environnement, lisez les [étapes détaillées ci-dessous](#detailed-walkthrough).

Créez le groupe de ressources :

```bash
azure group create TestRG -l westeurope
```

Vérifiez le groupe de ressources à l’aide de l’analyseur JSON :

```bash
azure group show TestRG --json | jq '.'
```

Créez le compte de stockage :

```bash
azure storage account create -g TestRG -l westeurope --kind Storage --sku-name GRS computeteststore
```

Vérifiez le compte de stockage à l’aide de l’analyseur JSON :

```bash
azure storage account show -g TestRG computeteststore --json | jq '.'
```

Créez le réseau virtuel :

```bash
azure network vnet create -g TestRG -n TestVNet -a 192.168.0.0/16 -l westeurope
```

Créez le sous-réseau :

```bash
azure network vnet subnet create -g TestRG -e TestVNet -n FrontEnd -a 192.168.1.0/24
```

Vérifiez le réseau virtuel et le sous-réseau à l’aide de l’analyseur JSON :


```bash
azure network vnet show TestRG TestVNet --json | jq '.'
```

Créez une adresse IP publique :

```bash
azure network public-ip create -g TestRG -n TestLBPIP -l westeurope -d testlb -a static -i 4
```

Créez l’équilibreur de charge :

```bash
azure network lb create -g TestRG -n TestLB -l westeurope
```

Créez un pool d’adresses IP frontal pour l’équilibreur de charge et l’associer à l’adresse IP publique :

```bash
azure network lb frontend-ip create -g TestRG -l TestLB -n TestFrontEndPool -i TestLBPIP
```

Créez le pool d’adresses IP principal pour l’équilibreur de charge :

```bash
azure network lb address-pool create -g TestRG -l TestLB -n TestBackEndPool
```

Créez des règles NAT entrantes SSH pour l’équilibreur de charge :

```bash
azure network lb inbound-nat-rule create -g TestRG -l TestLB -n VM1-SSH -p tcp -f 4222 -b 22
azure network lb inbound-nat-rule create -g TestRG -l TestLB -n VM2-SSH -p tcp -f 4223 -b 22
```

Créez les règles NAT entrantes Web pour l’équilibreur de charge :

```bash
azure network lb rule create -g TestRG -l TestLB -n WebRule -p tcp -f 80 -b 80 \
     -t TestFrontEndPool -o TestBackEndPool
```

Créez la sonde d’intégrité d’équilibreur de charge :

```bash
azure network lb probe create -g TestRG -l TestLB -n HealthProbe -p "http" -f healthprobe.aspx -i 15 -c 4
```

Vérifier l’équilibreur de charge, les pools d’adresses IP et les règles NAT à l’aide de l’analyseur JSON :

```bash
azure network lb show -g TestRG -n TestLB --json | jq '.'
```

Créez la première carte d’interface réseau (NIC) :

```bash
azure network nic create -g TestRG -n LB-NIC1 -l westeurope --subnet-vnet-name TestVNet --subnet-name FrontEnd \
    -d "/subscriptions/########-####-####-####-############/resourceGroups/TestRG/providers/Microsoft.Network/loadBalancers/TestLB/backendAddressPools/TestBackEndPool" \
    -e "/subscriptions/########-####-####-####-############/resourceGroups/TestRG/providers/Microsoft.Network/loadBalancers/TestLB/inboundNatRules/VM1-SSH"
```

Créez la deuxième carte d’interface réseau :

```bash
azure network nic create -g TestRG -n LB-NIC2 -l westeurope --subnet-vnet-name TestVNet --subnet-name FrontEnd \
    -d "/subscriptions/########-####-####-####-############/resourceGroups/TestRG/providers/Microsoft.Network/loadBalancers/TestLB/backendAddressPools/TestBackEndPool" \
    -e "/subscriptions/########-####-####-####-############/resourceGroups/TestRG/providers/Microsoft.Network/loadBalancers/TestLB/inboundNatRules/VM2-SSH"
```

Vérifiez les cartes d’interface réseau à l’aide de l’analyseur JSON :

```bash
azure network nic show TestRG LB-NIC1 --json | jq '.'
azure network nic show TestRG LB-NIC2 --json | jq '.'
```

Créez le groupe de sécurité réseau :

```bash
azure network nsg create -g TestRG -n TestNSG -l westeurope
```

Ajoutez les règles de trafic entrant pour le groupe de sécurité réseau :

```bash
azure network nsg rule create --protocol tcp --direction inbound --priority 1000 \
    --destination-port-range 22 --access allow -g TestRG -a TestNSG -n SSHRule
azure network nsg rule create --protocol tcp --direction inbound --priority 1001 \
    --destination-port-range 80 --access allow -g TestRG -a TestNSG -n HTTPRule
```

Vérifiez le groupe de sécurité réseau et les règles de trafic entrant à l’aide de l’analyseur JSON :

```bash
azure network nsg show -g TestRG -n TestNSG --json | jq '.'
```

Liez le groupe de sécurité réseau aux cartes d’interface réseau :

```bash
azure network nic set -g TestRG -n LB-NIC1 -o TestNSG
azure network nic set -g TestRG -n LB-NIC2 -o TestNSG
```

Créez le groupe à haute disponibilité :

```bash
azure availset create -g TestRG -n TestAvailSet -l westeurope
```

Créez la première machine virtuelle Linux :

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

Créez la deuxième machine virtuelle Linux :

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

Utilisez l’analyseur JSON pour vérifier tout ce qui a été créé :

```bash
azure vm show -g TestRG -n TestVM1 --json | jq '.'
azure vm show -g TestRG -n TestVM2 --json | jq '.'
```

Exportez l’environnement que vous avez créé dans un modèle pour recréer rapidement des instances nouvelles :

```bash
azure resource export TestRG
```

## Procédure pas à pas
Les étapes détaillées qui suivent expliquent ce que chaque commande fait lorsque vous générez votre environnement. Ces concepts vous aideront à créer vos propres environnements personnalisés pour le développement ou la production.

## Créer des groupes de ressources et choisir les emplacements de déploiement

Les groupes de ressources Azure sont des entités de déploiement logiques qui contiennent des informations de configuration et des métadonnées pour permettre la gestion logique des déploiements de ressources.

```bash
azure group create TestRG westeurope
```

Output:

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

## Créez un compte de stockage.

Vous avez besoin de comptes de stockage pour vos disques de machine virtuelle et pour tous les disques de données que vous souhaitez ajouter. Vous créez des comptes de stockage presque immédiatement après avoir créé des groupes de ressources.

Ici, nous utilisons la commande `azure storage account create` pour transmettre l’emplacement du compte, le groupe de ressources qui le contrôle, ainsi que le type de support de stockage souhaité.

```bash
azure storage account create \  
--location westeurope \
--resource-group TestRG \
--kind Storage --sku-name GRS \
computeteststore
```

Output:

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

Nous allons utiliser l’outil [jq](https://stedolan.github.io/jq/) avec l`--json`’option de ligne de commande Azure pour examiner notre groupe de ressources à l’aide de la commande `azure group show`. (Vous pouvez utiliser **jsawk** ou la bibliothèque de langue de votre choix pour analyser le fichier JSON.)

```bash
azure group show TestRG --json | jq                                                                                      
```


Output:

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

Pour examiner le compte de stockage à l’aide de l’interface CLI, vous devez d’abord définir les noms de comptes et les clés à l’aide d’une variante de la commande suivante. Remplacez le nom du compte de stockage dans l’exemple suivant par le nom de votre choix :

```
AZURE_STORAGE_CONNECTION_STRING="$(azure storage account connectionstring show computeteststore --resource-group testrg --json | jq -r '.string')"
```

Vous pouvez alors visualiser facilement vos informations de stockage :

```bash
azure storage container list
```

Output:

```bash
info:    Executing command storage container list
+ Getting storage containers
data:    Name  Public-Access  Last-Modified
data:    ----  -------------  -----------------------------
data:    vhds  Off            Sun, 27 Sep 2015 19:03:54 GMT
info:    storage container list command OK
```

## Créer un réseau virtuel et un sous-réseau

Vous allez maintenant créer un réseau virtuel dans Azure et un sous-réseau dans lesquels vous pourrez installer vos machines virtuelles.

```bash
azure network vnet create -g TestRG -n TestVNet -a 192.168.0.0/16 -l westeurope
```

Output:

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

Là encore, nous allons utiliser l’option --json de la commande `azure group show` et de **jq** pour examiner la façon dont nous créons nos ressources. Nous disposons maintenant d’une ressource `storageAccounts` et d’une ressource `virtualNetworks`.

```bash
azure group show TestRG --json | jq '.'
```

Output:

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

À présent, créons dans le réseau virtuel `TestVnet` un sous-réseau dans lequel les machines virtuelles seront déployées. Nous allons utiliser la commande `azure network vnet subnet create` ainsi que les ressources que nous avons déjà créées : le groupe de ressources `TestRG` et le réseau virtuel `TestVNet`. Nous ajouterons ensuite le nom du sous-réseau `FrontEnd` et le préfixe d’adresse de sous-réseau `192.168.1.0/24`, comme suit :

```bash
azure network vnet subnet create -g TestRG -e TestVNet -n FrontEnd -a 192.168.1.0/24
```

Output:

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

Étant donné que le sous-réseau figure logiquement à l’intérieur du réseau virtuel, nous allons examiner les informations de sous-réseau avec une commande légèrement différente. Nous utiliserons la commande `azure network vnet show` mais continuerons à examiner la sortie JSON à l’aide de **jq**.

```bash
azure network vnet show TestRG TestVNet --json | jq '.'
```

Output:

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

## Créer une adresse IP publique (PIP)

Nous allons maintenant créer l’adresse IP publique (PIP) que nous attribuerons à votre équilibreur de charge. Elle permet de vous connecter à vos machines virtuelles depuis Internet à l’aide de la commande `azure network public-ip create`. Étant donné que la valeur par défaut est une adresse dynamique, nous créons une entrée DNS nommée dans le domaine **cloudapp.azure.com** à l’aide de l’option `-d testsubdomain`.

```bash
azure network public-ip create -d testsubdomain TestRG TestPIP westeurope
```

Output:

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

Output:

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

Vvous pouvez examiner les détails de la ressource, y compris le nom de domaine complet du sous-domaine, en utilisant la commande `azure network public-ip show` plus complète. Notez que la ressource d’adresse IP publique a été allouée de façon logique, mais qu’aucune adresse spécifique n’est encore affectée. Pour effectuer cette opération, vous aurez besoin d’un équilibreur de charge, que nous n’avons pas encore créé.

```bash
azure network public-ip show TestRG TestPIP --json | jq '.'
```

Output:

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

## Créer un équilibreur de charge et des pools d’adresses IP
Lorsque vous créez un équilibreur de charge, celui-ci vous permet de répartir le trafic entre plusieurs machines virtuelles. Cette opération est possible lors de l’exécution des applications web, par exemple. Cela assure également la redondance de votre application en exécutant plusieurs machines virtuelles qui répondent aux demandes des utilisateurs en cas de maintenance ou de lourdes charges.

Nous créons notre équilibreur de charge avec :

```bash
azure network lb create -g TestRG -n TestLB -l westeurope
```

```bash
azure network lb create -g TestRG -n TestLB -l westeurope
```

Output:

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
Notre équilibreur de charge est relativement vide. Nous allons donc créer des pools d’adresses IP. Nous souhaitons créer deux pools d’adresses IP pour notre équilibreur de charge : une pour le pool frontal et une pour le pool principal. Le pool d’adresses IP frontal sera visible au public. C’est également l’emplacement où vous devez entrer l’adresse PIP que nous avons créée précédemment. Nous utiliserons le pool principal comme l’emplacement auquel nos machines virtuelles se connecteront. Ainsi, le trafic peut transiter via l’équilibreur de charge vers les machines virtuelles.

Commençons tout d’abord par créer notre pool d’adresses IP frontal :

```bash
azure network lb frontend-ip create -g TestRG -l TestLB -n TestFrontEndPool -i TestLBPIP
```

Output:

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

Output:

```bash
info:    Executing command network lb address-pool create
+ Looking up the load balancer "TestLB"
+ Updating load balancer "TestLB"
data:    Name                            : TestBackEndPool
data:    Provisioning state              : Succeeded
info:    network lb address-pool create command OK
```

Nous pouvons vérifier la façon dont notre équilibreur de charge apparaît avec `azure network lb show` et en examinant la sortie JSON :

```bash
azure network lb show TestRG TestLB --json | jq '.'
```

Output:

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

## Créer des règles NAT d’équilibreur de charge
Pour que le trafic transite par notre équilibreur de charge, nous devons créer des règles NAT spécifiant les actions entrantes ou sortantes. Vous pouvez spécifier le protocole utilisé, puis mapper les ports externes aux ports internes comme vous le souhaitez. Pour notre environnement, nous allons créer des règles autorisant SSH à accéder à nos machines virtuelles par le biais de notre équilibreur de charge. Nous allons configurer les ports TCP 4222 et 4223 de manière à orienter le trafic vers le port TCP 22 sur nos machines virtuelles (que nous créerons plus tard) :

```bash
azure network lb inbound-nat-rule create -g TestRG -l TestLB -n VM1-SSH -p tcp -f 4222 -b 22
```

Output:

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

Nous allons maintenant créer une règle NAT pour le port TCP 80, en liant la règle à nos pools d’adresses IP. En procédant ainsi, au lieu de lier la règle à nos machines virtuelles individuellement, nous pouvons simplement ajouter ou supprimer des machines virtuelles du pool d’adresses IP. L’équilibreur de charge ajuste alors automatiquement le flux de trafic :

```bash
azure network lb rule create -g TestRG -l TestLB -n WebRule -p tcp -f 80 -b 80 \
     -t TestFrontEndPool -o TestBackEndPool
```

Output:

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

## Créer une sonde d’intégrité d’équilibreur de charge

Une sonde d’intégrité contrôle régulièrement les machines virtuelles situées derrière notre équilibreur de charge pour s’assurer qu’elles fonctionnent correctement et répondent aux demandes, telles que définies. Dans le cas contraire, elles sont supprimées du processus pour garantir que les utilisateurs ne sont pas orientés vers elles. Vous pouvez définir les contrôles personnalisés de la sonde d’intégrité, ainsi que des valeurs d’intervalle et de délai d’attente. Pour plus d'informations sur les sondes d’intégrité, consultez [Sondes d’équilibreur de charge](../load-balancer/load-balancer-custom-probe-overview.md).

```bash
azure network lb probe create -g TestRG -l TestLB -n HealthProbe -p "http" -f healthprobe.aspx -i 15 -c 4
```

Output:

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

Ici, nous avons spécifié un intervalle de 15 secondes pour nos contrôles d’intégrité et nous pouvons manquer jusqu’à quatre sondes (une minute) avant que l’équilibreur de charge ne considère que l’hôte n’est plus opérationnel.

## Vérifier l’équilibreur de charge
Vérifiez maintenant que la configuration de l’équilibreur de charge est terminée. Voici la procédure que vous avez suivie :

1. Vous avez tout d’abord créé un équilibreur de charge.
2. Puis vous avez créé un pool d’adresses IP frontal et lui avez affecté une adresse IP publique.
3. Vous avez ensuite créé un pool d’adresses IP principal auquel les machines virtuelles peuvent se connecter.
4. Après cela, vous avez créé des règles NAT qui autorisent le protocole SSH pour les machines virtuelles à des fins de gestion, ainsi qu’une règle autorisant le port TCP 80 de notre application web.
5. Enfin, vous avez ajouté une sonde d’intégrité pour vérifier périodiquement les machines virtuelles. Vous évitez ainsi que les utilisateurs n’essaient d’accéder à une machine virtuelle qui ne fonctionne plus ou qui ne propose plus de contenu.

Regardons maintenant à quoi ressemble votre équilibreur de charge :

```bash
azure network lb show -g TestRG -n TestLB --json | jq '.'
```

Output:

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

 La disponibilité des cartes d’interface réseau peut être déterminée par programme car vous pouvez définir des règles régissant leur utilisation. Vous pouvez également avoir plusieurs cartes. Notez que dans la commande `azure network nic create` suivante, vous avez lié la carte d’interface réseau au pool d’adresses IP principal de charge et l’avez associée avec la règle NAT pour autoriser le trafic SSH. Pour ce faire, vous devez spécifier votre ID d’abonnement Azure à la place de `<GUID>` :

```bash
azure network nic create -g TestRG -n LB-NIC1 -l westeurope --subnet-vnet-name TestVNet --subnet-name FrontEnd \
     -d /subscriptions/<GUID>/resourceGroups/TestRG/providers/Microsoft.Network/loadBalancers/TestLB/backendAddressPools/TestBackEndPool \
     -e /subscriptions/<GUID>/resourceGroups/TestRG/providers/Microsoft.Network/loadBalancers/TestLB/inboundNatRules/VM1-SSH
```

Output:

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

Vous pouvez visualiser les détails de la ressource en examinant directement cette dernière. Pour ce faire, utilisez la commande `azure network nic show` :

```bash
azure network nic show TestRG LB-NIC1 --json | jq '.'
```

Output:

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

Nous allons maintenant créer la deuxième carte réseau en la liant à nouveau à notre pool d’adresses IP principal. Cette fois, la deuxième règle NAT autorise le trafic SSH :

```bash
azure network nic create -g TestRG -n LB-NIC2 -l westeurope --subnet-vnet-name TestVNet --subnet-name FrontEnd \
    -d  /subscriptions/<GUID>/resourceGroups/TestRG/providers/Microsoft.Network/loadBalancers/TestLB/backendAddressPools/TestBackEndPool \
    -e /subscriptions/<GUID>/resourceGroups/TestRG/providers/Microsoft.Network/loadBalancers/TestLB/inboundNatRules/VM2-SSH
```

## Créer un groupe de sécurité réseau et les règles associées

Nous allons à présent créer votre groupe de sécurité réseau (NSG) et les règles de trafic entrant qui régissent l’accès à la carte réseau.

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

> [AZURE.NOTE] La règle de trafic entrant est un filtre pour les connexions réseau entrantes. Dans cet exemple, nous lions le NSG à la carte d’interface réseau (NIC) virtuelle des machines virtuelles, ce qui signifie que toute demande adressée au port 22 sera transmise par l’intermédiaire de la carte d’interface réseau sur notre machine virtuelle. Cette règle concerne une connexion réseau, et non à un point de terminaison comme dans le cas de déploiements classiques. Cela signifie que, pour ouvrir un port, vous devez laisser la commande `--source-port-range` définie sur '*' (valeur par défaut) afin d’accepter les demandes entrantes de **tous** les ports demandeurs. Ces ports sont généralement dynamiques.

## Liaison avec la carte d’interface réseau

Liez le groupe de sécurité réseau aux cartes d’interface réseau :

```bash
azure network nic set -g TestRG -n LB-NIC1 -o TestNSG
```

```bash
azure network nic set -g TestRG -n LB-NIC2 -o TestNSG
```

## Créer un groupe à haute disponibilité
Les groupes à haute disponibilité aident à diffuser vos machines virtuelles sur des domaines d’erreur et des domaines de mise à niveau. Nous allons maintenant voir comment créer un groupe à haute disponibilité pour vos machines virtuelles :

```bash
azure availset create -g TestRG -n TestAvailSet -l westeurope
```

Les domaines d’erreur désignent un groupe de machines virtuelles partageant une source d’alimentation et un commutateur réseau communs. Par défaut, les machines virtuelles configurées dans votre groupe à haute disponibilité sont réparties entre trois domaines de défaillance au maximum. Ainsi, en cas de problème matériel dans l’un de ces domaines d’erreur, toutes les machines virtuelles exécutant votre application n’en seront pas affectées. Azure distribue automatiquement les machines virtuelles sur les domaines d’erreur lorsque vous les placez dans un groupe à haute disponibilité.

Les domaines de mise à niveau indiquent les groupes de machines virtuelles et les équipements physiques sous-jacents pouvant être redémarrés en même temps. L’ordre de redémarrage des domaines de mise à jour peut ne pas être séquentiel au cours de la maintenance planifiée, mais les mises à jour seront redémarrées une par une. Cette fois encore, Azure distribue automatiquement vos machines virtuelles entre les domaines de mise à niveau lorsque vous les placez dans un site de disponibilité.

Vous pouvez obtenir des informations supplémentaires sur [la gestion de la disponibilité des machines virtuelles](./virtual-machines-linux-manage-availability.md).

## Créer les machines virtuelles Linux

Vous avez créé les ressources de stockage et de réseau pour prendre en charge des machines virtuelles accessibles par Internet. Créons maintenant ces machines virtuelles et sécurisons-les avec une clé SSH sans mot de passe. Dans ce cas précis, nous allons créer une machine virtuelle Ubuntu basée sur la dernière version LTS. Nous rechercherons les informations de cette image en utilisant `azure vm image list`, comme décrit dans [Recherche d’images de machine virtuelle Azure](virtual-machines-linux-cli-ps-findimage.md).

Nous avons sélectionné une image à l’aide de la commande `azure vm image list westeurope canonical | grep LTS`. Dans ce cas, nous allons utiliser `canonical:UbuntuServer:14.04.4-LTS:14.04.201604060`. Pour le dernier champ, nous allons passer `latest` afin de toujours obtenir la build la plus récente. (La chaîne que nous utilisons sera `canonical:UbuntuServer:14.04.4-LTS:14.04.201604060`).

Cette étape est familière à toute personne ayant déjà créé une paire de clés publique et privée SSH RSA sur Linux ou Mac à l’aide de la commande **ssh-keygen -t rsa -b 2048**. Si vous ne disposez pas de toutes les paires de clés de certificat dans votre répertoire `~/.ssh`, vous pouvez les créer ainsi :

- Automatiquement à l’aide de l’option `azure vm create --generate-ssh-keys`.
- Manuellement en suivant [les instructions pour les créer vous-même](virtual-machines-linux-ssh-from-linux.md).

Vous pouvez également utiliser la méthode --admin-password pour authentifier vos connexions SSH une fois la machine virtuelle créée. Cette méthode est généralement moins sécurisée.

Nous créons la machine virtuelle en regroupant toutes nos ressources et informations avec la commande `azure vm create` :

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

Output:

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

Vous pouvez alors vous connecter immédiatement à votre machine virtuelle à l’aide de vos clés SSH par défaut. Assurez-vous de spécifier le port approprié dans la mesure où nous transitions par l’équilibreur de charge. (Pour notre première machine virtuelle, nous configurons la règle NAT pour transférer le port 4222 vers votre machine virtuelle) :

```bash
 ssh ops@testlb.westeurope.cloudapp.azure.com -p 4222
```

Output:

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

Et vous pouvez à présent utiliser la commande `azure vm show testrg testvm` pour examiner ce que vous avez créé. À ce stade, vous exécutez vos machines virtuelles Ubuntu derrière un équilibreur de charge dans Azure auquel vous pouvez uniquement vous connecter avec votre paire de clés SSH (car les mots de passe sont désactivés). Vous pouvez installer nginx ou httpd, déployer une application web et voir le flux de trafic transitant par l’équilibreur de charge vers toutes les machines virtuelles.

```bash
azure vm show TestRG TestVM1
```

Output:

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
Maintenant que vous avez créé cet environnement, que se passe-t-il si vous souhaitez créer un environnement de développement supplémentaire avec les mêmes paramètres ou un environnement de production correspondant ? Resource Manager utilise des modèles JSON qui définissent tous les paramètres pour votre environnement. Cela signifie que vous pouvez créer des environnements entiers en faisant référence à ce modèle JSON. Vous pouvez [créer manuellement des modèles JSON](../resource-group-authoring-templates.md) ou exporter simplement un environnement existant qui créera le modèle JSON pour vous :

```bash
azure group export TestRG
```

Le fichier `TestRG.json` est créé dans votre répertoire de travail actuel. Lorsque vous créez un nouvel environnement à partir de ce modèle, vous êtes invité à fournir tous les noms des ressources, y compris ceux de l’équilibreur de charge, des interfaces réseau, des machines virtuelles, etc. Vous pouvez remplir ces éléments dans votre fichier de modèle en ajoutant `-p` ou `--includeParameterDefaultValue` à la commande `azure group export` indiquée ci-dessus. Modifiez votre modèle JSON pour spécifier les noms de ressources, ou [créez un fichier parameters.json](../resource-group-authoring-templates.md#parameters) qui spécifie les noms de ressources.

Pour créer un nouvel environnement à partir de votre modèle :

```bash
azure group deployment create -f TestRG.json -g NewRGFromTemplate
```

Vous pouvez lire la section contenant [plus de détails sur le déploiement à partir de modèles](../resource-group-template-deploy-cli.md). Découvrez notamment comment mettre à jour des environnements de manière incrémentielle, utiliser le fichier de paramètres et accéder aux modèles à partir d’un emplacement de stockage unique.

## Étapes suivantes

Vous voici en mesure de commencer à utiliser plusieurs composants réseau et machines virtuelles. Vous pouvez utiliser cet exemple d’environnement pour générer votre application en utilisant les composants de base présentés ici.

<!---HONumber=AcomDC_0727_2016-->