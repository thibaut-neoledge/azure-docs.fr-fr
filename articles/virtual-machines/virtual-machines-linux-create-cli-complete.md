<properties
   pageTitle="Créer une machine virtuelle Linux de A à Z à l’aide de l’interface de ligne de commande (CLI) Azure | Microsoft Azure"
   description="Créez une machine virtuelle Linux, un stockage, un réseau virtuel et un sous-réseau, une carte d’interface réseau, une adresse IP publique et un Groupe de sécurité réseau à partir de zéro à l’aide de l’interface de ligne de commande Azure."
   services="virtual-machines-linux"
   documentationCenter="virtual-machines"
   authors="vlivech"
   manager="squillace"
   editor=""
   tags="azure-resource-manager"/>

<tags
   ms.service="virtual-machines-linux"
   ms.devlang="na"
   ms.topic="article"
   ms.tgt_pltfrm="vm-linux"
   ms.workload="infrastructure"
   ms.date="04/04/2016"
   ms.author="v-livech"/>

# Créer une machine virtuelle Linux de A à Z à l’aide de l’interface de ligne de commande (CLI) Azure

Pour créer une machine virtuelle Linux, vous aurez besoin de l’[interface de ligne de commande (CLI) Azure](../xplat-cli-install.md) en mode Resource Manager (`azure config mode arm`) et d’un outil d’analyse JSON. Pour ce document, nous utilisons [jq](https://stedolan.github.io/jq/).

## Commandes rapides

```bash
# Create the Resource Group
chrisL@fedora$ azure group create TestRG westeurope

# Create the Storage Account
chrisL@fedora$ azure storage account create \  
--location westeurope \
--resource-group TestRG \
--type GRS \
computeteststore

# Verify the RG using the JSON parser
chrisL@fedora$ azure group show testrg --json | jq '.'

# Create the Virtual Network
chrisL@fedora$ azure network vnet create -g TestRG -n TestVNet -a 192.168.0.0/16 -l westeurope

# Verify the RG
chrisL@fedora$ azure group show testrg --json | jq '.'

# Create the Subnet
chrisL@fedora$ azure network vnet subnet create -g TestRG -e TestVNet -n FrontEnd -a 192.168.1.0/24

# Verify the VNet and Subnet
chrisL@fedora$ azure network vnet show testrg testvnet --json | jq '.'

# Create the NIC
chrisL@fedora$ azure network nic create -g TestRG -n TestNIC -l westeurope -a 192.168.1.101 -m TestVNet -k FrontEnd

# Verify the NIC
chrisL@fedora$ azure network nic show testrg testnic --json | jq '.'

# Create the NSG
chrisL@fedora$ azure network nsg create testrg testnsg westeurope

# Add an inbound rule for the NSG
chrisL@fedora$ azure network nsg rule create --protocol tcp --direction inbound --priority 1000  --destination-port-range 22 --access allow testrg testnsg testnsgrule

# Creat the Public facing NIC
chrisL@fedora$ azure network public-ip create -d testsubdomain testrg testpip westeurope

# Verify the NIC
chrisL@fedora$ azure network public-ip show testrg testpip --json | jq '.'

# Associate the Public IP to the NIC
chrisL@fedora$ azure network nic set --public-ip-name testpip testrg testnic

# Bind the NSG to the NIC
chrisL@fedora$ azure network nic set --network-security-group-name testnsg testrg testnic

# Create the Linux VM
chrisL@fedora$ azure vm create \            
    --resource-group testrg \
    --name testvm \
    --location westeurope \
    --os-type linux \
    --nic-name testnic \
    --vnet-name testvnet \
    --vnet-subnet-name FrontEnd \
    --storage-account-name computeteststore \
    --image-urn canonical:UbuntuServer:14.04.3-LTS:latest \
    --ssh-publickey-file ~/.ssh/id_rsa.pub \
    --admin-username ops

# Verify everything built
chrisL@fedora$ azure vm show testrg testvm

```

## Procédure pas à pas

### Introduction

Cet article repose sur un déploiement identique à un déploiement de service cloud avec une seule machine virtuelle Linux dans un sous-réseau de réseau virtuel. Il décrit la totalité du déploiement de base de manière impérative, commande par commande, jusqu’à ce que vous disposiez d’une machine virtuelle Linux sécurisée opérationnelle à laquelle vous pouvez vous connecter à partir de n’importe quel emplacement d’Internet.

Au cours de ce processus, vous découvrirez la hiérarchie des dépendances et la puissance que vous offre le modèle de déploiement de Resource Manager. Une fois que vous aurez compris la façon dont le système est créé, vous pourrez le reconstruire beaucoup plus rapidement à l’aide de commandes d’interface de ligne de commande Azure plus directes (voir [cet article](virtual-machines-linux-quick-create-cli.md) décrivant à peu près le même déploiement à l’aide de la commande `azure vm quick-create`), ou vous pourrez apprendre à concevoir et automatiser l’ensemble des déploiements du réseau et des applications et à les mettre à jour au moyen des [modèles Azure Resource Manager](../resource-group-authoring-templates.md). Dès que vous avez compris la façon dont les différentes parties de votre déploiement s’imbriquent, la création de modèles pour automatiser ces dernières se révèle plus simple.

Commençons par la procédure de création d’un réseau simple avec une machine virtuelle à des fins de développement et de calcul élémentaire, que nous décrirons étape par étape. Vous serez ensuite en mesure de créer des réseaux et déploiements plus complexes.

### Créer un groupe de ressources et choisir les emplacements de déploiement

Les groupes de ressources Azure sont des entités de déploiement logiques qui contiennent des données de configuration et d’autres métadonnées pour permettre la gestion logique des déploiements de ressources.

```
chrisL@fedora$ azure group create TestRG westeurope                        
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

### Créer un compte de stockage

Entre autres scénarios, vous aurez besoin de comptes de stockage pour vos disques de machine virtuelle et pour tous les disques de données que vous souhaiterez ajouter. En résumé, vous créerez toujours des comptes de stockage presque immédiatement après avoir créé des groupes de ressources.

Ici, nous utilisons la commande `azure storage account create` pour transmettre l’emplacement du compte, le groupe de ressources qui le contrôlera, ainsi que le type de support de stockage souhaité.

```
chrisL@fedora$ azure storage account create \  
--location westeurope \
--resource-group TestRG \
--type GRS \
computeteststore
info:    Executing command storage account create
+ Creating storage account
info:    storage account create command OK
rasquill•~/workspace/keygen» azure group show testrg
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

Utilisons l’outil [jq](https://stedolan.github.io/jq/) (vous pouvez analyser la sortie JSON avec **jsawk** ou toute bibliothèque de langue de votre choix) avec l’option d’interface de ligne de commande Azure `--json` pour examiner notre groupe de ressources à l’aide de la commande `azure group show`.

```
chrisL@fedora$ azure group show testrg --json | jq '.'                                                                                        
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

```
chrisL@fedora$ azure storage container list
info:    Executing command storage container list
+ Getting storage containers
data:    Name  Public-Access  Last-Modified
data:    ----  -------------  -----------------------------
data:    vhds  Off            Sun, 27 Sep 2015 19:03:54 GMT
info:    storage container list command OK
```
### Créer votre réseau virtuel et votre sous-réseau

Vous devrez créer un réseau virtuel Azure et un sous-réseau dans lesquels vous pourrez installer votre machine virtuelle.

```
chrisL@fedora$ azure network vnet create -g TestRG -n TestVNet -a 192.168.0.0/16 -l westeurope
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

```
chrisL@fedora$ azure group show testrg --json | jq '.'
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

À présent, créons dans le réseau virtuel `TestVnet` un sous-réseau dans lequel la machine virtuelle sera déployée. Nous utilisons la commande `azure network vnet subnet create` avec les ressources que nous avons créées précédemment (le groupe de ressources `TestRG` et le réseau virtuel `TestVNet`), et nous ajoutons le nom de sous-réseau `FrontEnd` et le préfixe d’adresse de sous-réseau `192.168.1.0/24`, comme ci-dessous.

```
chrisL@fedora$ azure network vnet subnet create -g TestRG -e TestVNet -n FrontEnd -a 192.168.1.0/24
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

```
chrisL@fedora$ azure network vnet show testrg testvnet --json | jq '.'
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

### Créer une carte d’interface réseau (NIC) à utiliser avec la machine virtuelle Linux

La disponibilité des NIC elles-mêmes peut être déterminée par programme, car vous pouvez avoir plusieurs NIC et définir des règles régissant leur utilisation.

```
chrisL@fedora$ azure network nic create -g TestRG -n TestNIC -l westeurope -a 192.168.1.101 -m TestVNet -k FrontEnd
info:    Executing command network nic create
+ Looking up the network interface "TestNIC"
+ Looking up the subnet "FrontEnd"
+ Creating network interface "TestNIC"
+ Looking up the network interface "TestNIC"
data:    Id                              : /subscriptions/guid/resourceGroups/TestRG/providers/Microsoft.Network/networkInterfaces/TestNIC
data:    Name                            : TestNIC
data:    Type                            : Microsoft.Network/networkInterfaces
data:    Location                        : westeurope
data:    Provisioning state              : Succeeded
data:    Enable IP forwarding            : false
data:    IP configurations:
data:      Name                          : NIC-config
data:      Provisioning state            : Succeeded
data:      Private IP address            : 192.168.1.101
data:      Private IP Allocation Method  : Static
data:      Subnet                        : /subscriptions/guid/resourceGroups/TestRG/providers/Microsoft.Network/virtualNetworks/TestVNet/subnets/FrontEnd
data:
info:    network nic create command OK
```

Étant donné que la ressource de NIC est associée à une machine virtuelle et à un Groupe de sécurité réseau, elle apparaît en tant que ressource de niveau supérieur lorsque vous examinez votre groupe de ressources `TestRG` :

```
chrisL@fedora$ azure group show testrg --json | jq '.'
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

Vous pouvez visualiser les détails de la ressource en examinant directement cette dernière à l’aide de la commande `azure network nic show`.

```
chrisL@fedora$ azure network nic show testrg testnic --json | jq '.'
{
"ipConfigurations": [
    {
    "loadBalancerBackendAddressPools": [],
    "loadBalancerInboundNatRules": [],
    "privateIpAddress": "192.168.1.101",
    "privateIpAllocationMethod": "Static",
    "subnet": {
        "id": "/subscriptions/guid/resourceGroups/TestRG/providers/Microsoft.Network/virtualNetworks/TestVNet/subnets/FrontEnd"
    },
    "provisioningState": "Succeeded",
    "name": "NIC-config",
    "etag": "W/"4d29b1ca-0207-458c-b258-f298e6fc450f"",
    "id": "/subscriptions/guid/resourceGroups/TestRG/providers/Microsoft.Network/networkInterfaces/TestNIC/ipConfigurations/NIC-config"
    }
],
"tags": {},
"dnsSettings": {
    "appliedDnsServers": [],
    "dnsServers": []
},
"enableIPForwarding": false,
"provisioningState": "Succeeded",
"etag": "W/"4d29b1ca-0207-458c-b258-f298e6fc450f"",
"id": "/subscriptions/guid/resourceGroups/TestRG/providers/Microsoft.Network/networkInterfaces/TestNIC",
"name": "TestNIC",
"location": "westeurope"
}
```

### Créer votre Groupe de sécurité réseau et les règles associées

Nous allons à présent créer votre Groupe de sécurité réseau (NSG) et les règles de trafic entrant qui régissent l’accès à la NIC.

```
chrisL@fedora$ azure network nsg create testrg testnsg westeurope
```

Ajoutons la règle de trafic entrant pour le NSG afin d’autoriser les connexions entrantes sur le port 22 (pour prendre en charge le protocole SSH) :

```
chrisL@fedora$ azure network nsg rule create --protocol tcp --direction inbound --priority 1000  --destination-port-range 22 --access allow testrg testnsg testnsgrule
```

> [AZURE.NOTE] La règle de trafic entrant est un filtre pour les connexions réseau entrantes. Dans cet exemple, nous allons lier le NSG à la NIC virtuelle des machines virtuelles, ce qui signifie que toute demande adressée au port 22 sera transmise par l’intermédiaire de la NIC sur notre machine virtuelle. Étant donné qu’il s’agit d’une règle concernant une connexion réseau, et non un point de terminaison comme dans les déploiements classiques, afin d’ouvrir un port, vous devez laisser la commande `--source-port-range` définie sur « * » (valeur par défaut) afin d’accepter les demandes entrantes à partir de **tous** les ports demandeurs, qui sont généralement dynamiques.

### Créer votre adresse IP publique (PIP)

À présent, créons votre adresse IP publique qui vous permettra de vous connecter à votre machine virtuelle à partir d’Internet en utilisant la commande `azure network public-ip create`. Étant donné que la valeur par défaut est une adresse dynamique, nous créons une entrée DNS nommée dans le domaine **cloudapp.azure.com** à l’aide de l’option `-d testsubdomain`.

```
chrisL@fedora$ azure network public-ip create -d testsubdomain testrg testpip westeurope
info:    Executing command network public-ip create
+ Looking up the public ip "testpip"
+ Creating public ip address "testpip"
+ Looking up the public ip "testpip"
data:    Id                              : /subscriptions/guid/resourceGroups/testrg/providers/Microsoft.Network/publicIPAddresses/testpip
data:    Name                            : testpip
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

```
chrisL@fedora$ azure group show testrg --json | jq '.'
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

Et, comme toujours, vous pouvez examiner les détails de la ressource, y compris le nom de domaine complet du sous-domaine, en utilisant la commande `azure network public-ip show` plus complète. Notez que la ressource d’adresse IP publique a été allouée de façon logique, mais qu’aucune adresse spécifique n’est encore affectée. Pour effectuer cette opération, vous aurez besoin d’une machine virtuelle, que nous n’avons pas encore créée.

```
azure network public-ip show testrg testpip --json | jq '.'
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

### Associer l’adresse IP publique et le Groupe de sécurité réseau à la NIC

```
chrisL@fedora$ azure network nic set --public-ip-name testpip testrg testnic
```

Liez le NSG à la NIC :

```
chrisL@fedora$ azure network nic set --network-security-group-name testnsg testrg testnic
```

### Créer votre machine virtuelle Linux

Vous avez créé les ressources de stockage et de réseau pour prendre en charge une machine virtuelle accessible par Internet. Créons maintenant cette machine virtuelle et sécurisons-la avec une clé SSH sans mot de passe. Dans ce cas précis, nous allons créer une machine virtuelle Ubuntu basée sur la dernière version LTS. Nous rechercherons les informations de cette image en utilisant `azure vm image list`, comme décrit dans [Recherche d’images de machine virtuelle Azure](virtual-machines-linux-cli-ps-findimage.md). Nous avons sélectionné une image à l’aide de la commande `azure vm image list westeurope canonical | grep LTS`, et dans ce cas précis, nous utiliserons `canonical:UbuntuServer:14.04.3-LTS:14.04.201509080`. Toutefois, pour le dernier champ, nous transmettrons `latest`, ce qui nous permettra d’obtenir systématiquement la build la plus récente à l’avenir (nous utiliserons la chaîne `canonical:UbuntuServer:14.04.3-LTS:latest`).

> [AZURE.NOTE] Cette étape est familière à toute personne ayant déjà créé une paire de clés publique et privée SSH RSA sur Linux ou Mac à l’aide de la commande **ssh-keygen -t rsa -b 2048**. Si vous ne disposez pas de toutes les paires de clés de certificat dans votre répertoire `~/.ssh`, vous pouvez les créer de deux manières : <br /> 1. automatiquement à l’aide de l’option `azure vm create --generate-ssh-keys` ; 2. manuellement en suivant [les instructions pour les créer vous-même](virtual-machines-linux-ssh-from-linux.md). <br /> Vous pouvez également utiliser les options `azure vm create --admin-username --admin-password` pour utiliser la méthode généralement moins sécurisée d’authentification de vos connexions SSH par nom d’utilisateur et mot de passe une fois la machine virtuelle créée.

Nous créons la machine virtuelle en regroupant toutes nos ressources et informations avec la commande `azure vm create`.

```
chrisL@fedora$ azure vm create \            
--resource-group testrg \
--name testvm \
--location westeurope \
--os-type linux \
--nic-name testnic \
--vnet-name testvnet \
--vnet-subnet-name FrontEnd \
--storage-account-name computeteststore \
--image-urn canonical:UbuntuServer:14.04.3-LTS:latest \
--ssh-publickey-file ~/.ssh/id_rsa.pub \
--admin-username ops
info:    Executing command vm create
+ Looking up the VM "testvm"
info:    Verifying the public key SSH file: /Users/user/.ssh/id_rsa.pub
info:    Using the VM Size "Standard_A1"
info:    The [OS, Data] Disk or image configuration requires storage account
+ Looking up the storage account computeteststore
+ Looking up the NIC "testnic"
info:    Found an existing NIC "testnic"
info:    Found an IP configuration with virtual network subnet id "/subscriptions/guid/resourceGroups/TestRG/providers/Microsoft.Network/virtualNetworks/TestVNet/subnets/FrontEnd" in the NIC "testnic"
info:    This NIC IP configuration has a public ip already configured "/subscriptions/guid/resourcegroups/testrg/providers/microsoft.network/publicipaddresses/testpip", any public ip parameters if provided, will be ignored.
+ Creating VM "testvm"
info:    vm create command OK
```

Vous pouvez alors vous connecter aussitôt à votre machine virtuelle à l’aide de vos clés SSH par défaut.

```
chrisL@fedora$ ssh ops@testsubdomain.westeurope.cloudapp.azure.com           
The authenticity of host 'testsubdomain.westeurope.cloudapp.azure.com (XX.XXX.XX.XXX)' can't be established.
RSA key fingerprint is b6:a4:7g:4b:cb:cd:76:87:63:2d:84:83:ac:12:2d:cd.
Are you sure you want to continue connecting (yes/no)? yes
Warning: Permanently added 'testsubdomain.westeurope.cloudapp.azure.com,XX.XXX.XX.XXX' (RSA) to the list of known hosts.
Welcome to Ubuntu 14.04.3 LTS (GNU/Linux 3.19.0-28-generic x86_64)

* Documentation:  https://help.ubuntu.com/

System information as of Mon Sep 28 18:45:02 UTC 2015

System load: 0.64              Memory usage: 5%   Processes:       81
Usage of /:  45.3% of 1.94GB   Swap usage:   0%   Users logged in: 0

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

ops@testvm:~$
```

Et vous pouvez à présent utiliser la commande `azure vm show testrg testvm` pour examiner ce que vous avez créé. À ce stade, vous disposez d’une machine virtuelle Ubuntu opérationnelle dans Azure à laquelle vous pouvez uniquement vous connecter avec la paire de clés SSH que vous possédez ; les mots de passe sont désactivés.

```
chrisL@fedora$ azure vm show testrg testvm
info:    Executing command vm show
+ Looking up the VM "testvm"
+ Looking up the NIC "testnic"
+ Looking up the public ip "testpip"
data:    Id                              :/subscriptions/guid/resourceGroups/testrg/providers/Microsoft.Compute/virtualMachines/testvm
data:    ProvisioningState               :Succeeded
data:    Name                            :testvm
data:    Location                        :westeurope
data:    FQDN                            :testsubdomain.westeurope.cloudapp.azure.com
data:    Type                            :Microsoft.Compute/virtualMachines
data:
data:    Hardware Profile:
data:      Size                          :Standard_A1
data:
data:    Storage Profile:
data:      Image reference:
data:        Publisher                   :canonical
data:        Offer                       :UbuntuServer
data:        Sku                         :14.04.3-LTS
data:        Version                     :latest
data:
data:      OS Disk:
data:        OSType                      :Linux
data:        Name                        :cli4eecdddc349d6015-os-1443465824206
data:        Caching                     :ReadWrite
data:        CreateOption                :FromImage
data:        Vhd:
data:          Uri                       :https://computeteststore.blob.core.windows.net/vhds/cli4eecdddc349d6015-os-1443465824206.vhd
data:
data:    OS Profile:
data:      Computer Name                 :testvm
data:      User Name                     :ops
data:      Linux Configuration:
data:        Disable Password Auth       :true
data:        SSH Public Keys:
data:          Public Key #1:
data:            Path                    :/home/ops/.ssh/authorized_keys
data:            Key                     :MIIBrTCCAZigAwIBAgIBATALBgkqhkiG9w0BAQUwADAiGA8yMDE1MDkyODE4MzM0
<snip>
data:
data:    Network Profile:
data:      Network Interfaces:
data:        Network Interface #1:
data:          Id                        :/subscriptions/guid/resourceGroups/testrg/providers/Microsoft.Network/networkInterfaces/testnic
data:          Primary                   :true
data:          MAC Address               :00-0D-3A-21-8E-AE
data:          Provisioning State        :Succeeded
data:          Name                      :testnic
data:          Location                  :westeurope
data:            Private IP alloc-method :Dynamic
data:            Private IP address      :192.168.1.101
data:            Public IP address       :40.115.48.189
data:            FQDN                    :testsubdomain.westeurope.cloudapp.azure.com
data:
data:    Diagnostics Instance View:
info:    vm show command OK
```

### Étapes suivantes

Vous voici en mesure de commencer à utiliser plusieurs composants réseau et machines virtuelles.

<!---HONumber=AcomDC_0406_2016-->