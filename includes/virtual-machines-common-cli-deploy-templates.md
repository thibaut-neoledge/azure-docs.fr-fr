
Cet article montre comment utiliser les modèles Azure Resource Manager et l’interface de ligne de commande Azure pour effectuer les tâches courantes suivantes de déploiement et de gestion de machines virtuelles Azure. Pour connaître les modèles supplémentaires utilisables, consultez [Modèles de démarrage rapide Azure](https://azure.microsoft.com/documentation/templates/) et [Infrastructures d’application à l’aide de modèles](../articles/virtual-machines/virtual-machines-linux-app-frameworks.md).


- [Création rapide d’une machine virtuelle dans Azure](#quick-create-a-vm-in-azure)
- [Déploiement d’une machine virtuelle dans Azure à partir d’un modèle](#deploy-a-vm-in-azure-from-a-template)
- [Création d’une machine virtuelle à partir d’une image personnalisée](#create-a-custom-vm-image)
- [Déploiement d’une machine virtuelle qui utilise un réseau virtuel et un équilibreur de charge](#deploy-a-multi-vm-application-that-uses-a-virtual-network-and-an-external-load-balancer)
- [Suppression d’un groupe de ressources](#remove-a-resource-group)
- [Affichage du journal d’un déploiement de groupe de ressources](#show-the-log-for-a-resource-group-deployment)
- [Affichage des informations relatives à une machine virtuelle](#display-information-about-a-virtual-machine)
- [Connexion à une machine virtuelle Linux](#log-on-to-a-linux-based-virtual-machine)
- [Arrêt d’une machine virtuelle](#stop-a-virtual-machine)
- [Démarrage d’une machine virtuelle](#start-a-virtual-machine)
- [Association d’un disque de données](#attach-a-data-disk)

## Préparation

Avant de pouvoir utiliser l'interface de ligne de commande Azure avec des groupes de ressources Azure, vous devez avoir la version adéquate de l'interface de ligne de commande Azure, ainsi qu'un compte Azure. Si vous ne disposez pas de l'interface de ligne de commande Azure, [installez-la](xplat-cli-install.md).

### Mettre à jour votre interface de ligne de commande Microsoft Azure vers la version 0.9.0 ou une version ultérieure

Entrez `azure --version` pour savoir si vous avez déjà installé la version 0.9.0 ou une version ultérieure.

	azure --version
    0.9.0 (node: 0.10.25)

Si vous disposez d'une version antérieure à la version 0.9.0, vous devez la mettre à jour en utilisant l'un des programmes d'installation natifs ou via **npm**, en tapant `npm update -g azure-cli`.

Vous pouvez également exécuter l'interface de ligne de commande Azure en tant que conteneur Docker à l'aide de l'[image Docker](https://registry.hub.docker.com/u/microsoft/azure-cli/) suivante. À partir d’un hôte Docker, exécutez la commande suivante :

	docker run -it microsoft/azure-cli

### Configurer votre compte et votre abonnement Microsoft Azure

Si vous ne possédez pas déjà un abonnement Azure, mais que vous avez un abonnement MSDN, vous pouvez activer vos [avantages abonnés MSDN](https://azure.microsoft.com/pricing/member-offers/msdn-benefits-details/). Ou vous pouvez vous inscrire à un [essai gratuit](https://azure.microsoft.com/pricing/free-trial/).

À présent, [connectez-vous à votre compte Azure de façon interactive](../articles/xplat-cli-connect.md#use-the-log-in-method) en tapant `azure login`, puis en suivant les invites pour une expérience de connexion interactive à votre compte Azure.

> [AZURE.NOTE] Si vous disposez d'un ID professionnel ou scolaire et que vous savez que l'authentification à deux facteurs n'est pas activée, vous pouvez **également** utiliser `azure login -u` avec l'ID professionnel ou scolaire pour vous connecter *sans* session interactive. Si vous ne disposez pas d'un ID professionnel ou scolaire, vous pouvez [créer un ID professionnel ou scolaire à partir de votre compte Microsoft personnel](../articles/virtual-machines/virtual-machines-windows-create-aad-work-id.md) pour vous connecter de la même façon.

Votre compte peut avoir plusieurs abonnements. Vous pouvez répertorier vos abonnements en tapant `azure account list`, ce qui pourrait donner ceci :

    azure account list
    info:    Executing command account list
    data:    Name                              Id                                    Tenant Id                            Current
    data:    --------------------------------  ------------------------------------  ------------------------------------  -------
    data:    Contoso Admin                     xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx  xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx  true
    data:    Fabrikam dev                      xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx  xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx  false  
    data:    Fabrikam test                     xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx  xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx  false  
    data:    Contoso production                xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx  xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx  false  

Vous pouvez définir l’abonnement Azure actuel en tapant la commande suivante. Utilisez le nom d’abonnement ou l’ID doté des ressources que vous souhaitez gérer.

	azure account set <subscription name or ID> true



### Passer en mode de groupe de ressources d’interface de ligne de commande Microsoft Azure

Par défaut, l'interface de ligne de commande Azure démarre en mode Azure Service Management (mode **asm**). Tapez la commande suivante pour basculer en mode de groupe de ressources.

	azure config mode arm

## Présentation des groupes et des modèles de ressources Azure

La plupart des applications sont basées sur une combinaison de différents types de ressources (au moins une machine virtuelle et un compte de stockage, une base de données SQL, un réseau virtuel ou un réseau de distribution de contenu). L’API de gestion des services Azure par défaut et le portail Azure Classic présentaient ces éléments en suivant une approche au cas par cas. ce qui nécessitait un déploiement et une gestion propres à chaque service (ou l’obtention d’outils supplémentaires pour le faire) et interdisait l’usage d’une seule unité logique de déploiement.

Cependant, les *modèles Azure Resource Manager* permettent de regrouper le déploiement et la gestion de ces différentes ressources en une seule unité logique de déploiement de manière déclarative. Au lieu de devoir utiliser une suite de commandes pour indiquer à Azure les éléments à déployer les uns après les autres, il vous suffit de décrire l’intégralité de votre déploiement dans un fichier JSON, comprenant toutes vos ressources, ainsi que les configurations et les paramètres de déploiement leur étant associés, puis d’indiquer à Azure de déployer ces ressources en tant que groupe.

Vous pouvez ensuite gérer l’ensemble du cycle de vie des ressources du groupe en utilisant les commandes de gestion des ressources de l’interface de ligne de commande Azure pour :

- arrêter, démarrer ou supprimer d’un coup toutes les ressources au sein du groupe ;
- appliquer des règles de contrôle d’accès en fonction du rôle (RBAC) pour le verrouillage des autorisations de sécurité ;
- mener des opérations d’audit ;
- baliser des ressources avec des métadonnées supplémentaires pour améliorer leur suivi.

Pour plus d'informations sur les groupes de ressources Azure et ce qu'ils peuvent faire pour vous, consultez [Présentation d'Azure Resource Manager](../articles/resource-group-overview.md). Si vous êtes intéressé par la création de modèles, consultez [Création de modèles Azure Resource Manager](../articles/resource-group-authoring-templates.md).

## <a id="quick-create-a-vm-in-azure"></a>Tâche : Créer rapidement une machine virtuelle dans Azure

Parfois, comme par exemple lorsque vous voulez tester quelque chose sur une nouvelle machine virtuelle, vous savez quelle image vous voulez utiliser et vous avez immédiatement besoin d’une machine virtuelle de cette image sans vous préoccuper de son infrastructure. C’est dans ce genre de situation qu’il faut utiliser la commande `azure vm quick-create` et transmettre les arguments nécessaires à la création d’une machine virtuelle et son infrastructure.

Commencez par créer votre groupe de ressources.

    azure group create coreos-quick westus
    info:    Executing command group create
    + Getting resource group coreos-quick
    + Creating resource group coreos-quick
    info:    Created resource group coreos-quick
    data:    Id:                  /subscriptions/xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx/resourceGroups/coreos-quick
    data:    Name:                coreos-quick
    data:    Location:            westus
    data:    Provisioning State:  Succeeded
    data:    Tags:
    data:
    info:    group create command OK


Ensuite, vous aurez besoin d’une image. Pour rechercher une image à l’aide de l’interface de ligne de commande Azure, consultez [Rechercher par navigation et sélectionner des images de machines virtuelles Azure avec Windows PowerShell et l’interface de ligne de commande Azure](../articles/virtual-machines/virtual-machines-linux-cli-ps-findimage.md). Mais pour cet article, voici une brève liste d’images populaires. Nous allons utiliser l’image Stable de CoreOS pour cette création rapide.

> [AZURE.NOTE] Pour l’élément ComputeImageVersion, vous pouvez simplement ajouter 'latest' en tant que paramètre dans le langage du modèle et dans l’interface de ligne de commande Azure. Ceci vous permettra de toujours utiliser la version la plus récente et corrigée de l’image, sans modifier vos scripts ou vos modèles. Consultez l’illustration ci-dessous.

| PublisherName | Offer | Sku | Version |
|:---------------------------------|:-------------------------------------------|:---------------------------------|:--------------------|
| OpenLogic | CentOS | 7 | 7\.0.201503 |
| OpenLogic | CentOS | 7\.1 | 7\.1.201504 |
| CoreOS | CoreOS | Bêta | 647\.0.0 |
| CoreOS | CoreOS | Stable | 633\.1.0 |
| MicrosoftDynamicsNAV | DynamicsNAV | 2015 | 8\.0.40459 |
| MicrosoftSharePoint | MicrosoftSharePointServer | 2013 | 1\.0.0 |
| msopentech | Oracle-Database-12c-Weblogic-Server-12c | Standard | 1\.0.0 |
| msopentech | Oracle-Database-12c-Weblogic-Server-12c | Entreprise | 1\.0.0 |
| MicrosoftSQLServer | SQL2014-WS2012R2 | Entreprise, optimisé pour l’entrepôt de données | 12\.0.2430 |
| MicrosoftSQLServer | SQL2014-WS2012R2 | Entreprise, optimisé pour le traitement transactionnel en ligne | 12\.0.2430 |
| Canonical | UbuntuServer | 12\.04.5-LTS | 12\.04.201504230 |
| Canonical | UbuntuServer | 14\.04.2-LTS | 14\.04.201503090 |
| MicrosoftWindowsServer | WindowsServer | 2012-Datacenter | 3\.0.201503 |
| MicrosoftWindowsServer | WindowsServer | 2012-R2-Datacenter | 4\.0.201503 |
| MicrosoftWindowsServer | WindowsServer | Windows Server Technical Preview | 5\.0.201504 |
| MicrosoftWindowsServerEssentials | WindowsServerEssentials | WindowsServerEssentials | 1\.0.141204 |
| MicrosoftWindowsServerHPCPack | WindowsServerHPCPack | 2012R2 | 4\.3.4665 |

Créez simplement votre machine virtuelle en entrant la commande `azure vm quick-create` et en vous préparant à répondre aux invites. Le résultat suivant doit s’afficher :

    azure vm quick-create
    info:    Executing command vm quick-create
    Resource group name: coreos-quick
    Virtual machine name: coreos
    Location name: westus
    Operating system Type [Windows, Linux]: linux
    ImageURN (format: "publisherName:offer:skus:version"): coreos:coreos:stable:latest
    User name: ops
    Password: *********
    Confirm password: *********
    + Looking up the VM "coreos"
    info:    Using the VM Size "Standard_A1"
    info:    The [OS, Data] Disk or image configuration requires storage account
    + Retrieving storage accounts
    info:    Could not find any storage accounts in the region "westus", trying to create new one
    + Creating storage account "cli9fd3fce49e9a9b3d14302" in "westus"
    + Looking up the storage account cli9fd3fce49e9a9b3d14302
    + Looking up the NIC "coreo-westu-1430261891570-nic"
    info:    An nic with given name "coreo-westu-1430261891570-nic" not found, creating a new one
    + Looking up the virtual network "coreo-westu-1430261891570-vnet"
    info:    Preparing to create new virtual network and subnet
    / Creating a new virtual network "coreo-westu-1430261891570-vnet" [address prefix: "10.0.0.0/16"] with subnet "coreo-westu-1430261891570-sne+" [address prefix: "10.0.1.0/24"]
    + Looking up the virtual network "coreo-westu-1430261891570-vnet"
    + Looking up the subnet "coreo-westu-1430261891570-snet" under the virtual network "coreo-westu-1430261891570-vnet"
    info:    Found public ip parameters, trying to setup PublicIP profile
    + Looking up the public ip "coreo-westu-1430261891570-pip"
    info:    PublicIP with given name "coreo-westu-1430261891570-pip" not found, creating a new one
    + Creating public ip "coreo-westu-1430261891570-pip"
    + Looking up the public ip "coreo-westu-1430261891570-pip"
    + Creating NIC "coreo-westu-1430261891570-nic"
    + Looking up the NIC "coreo-westu-1430261891570-nic"
    + Creating VM "coreos"
    + Looking up the VM "coreos"
    + Looking up the NIC "coreo-westu-1430261891570-nic"
    + Looking up the public ip "coreo-westu-1430261891570-pip"
    data:    Id                              :/subscriptions/xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx/resourceGroups/coreos-quick/providers/Microsoft.Compute/virtualMachines/coreos
    data:    ProvisioningState               :Succeeded
    data:    Name                            :coreos
    data:    Location                        :westus
    data:    FQDN                            :coreo-westu-1430261891570-pip.westus.cloudapp.azure.com
    data:    Type                            :Microsoft.Compute/virtualMachines
    data:
    data:    Hardware Profile:
    data:      Size                          :Standard_A1
    data:
    data:    Storage Profile:
    data:      Image reference:
    data:        Publisher                   :coreos
    data:        Offer                       :coreos
    data:        Sku                         :stable
    data:        Version                     :633.1.0
    data:
    data:      OS Disk:
    data:        OSType                      :Linux
    data:        Name                        :cli9fd3fce49e9a9b3d-os-1430261892283
    data:        Caching                     :ReadWrite
    data:        CreateOption                :FromImage
    data:        Vhd:
    data:          Uri                       :https://cli9fd3fce49e9a9b3d14302.blob.core.windows.net/vhds/cli9fd3fce49e9a9b3d-os-1430261892283.vhd
    data:
    data:    OS Profile:
    data:      Computer Name                 :coreos
    data:      User Name                     :ops
    data:      Linux Configuration:
    data:        Disable Password Auth       :false
    data:
    data:    Network Profile:
    data:      Network Interfaces:
    data:        Network Interface #1:
    data:          Id                        :/subscriptions/xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx/resourceGroups/coreos-quick/providers/Microsoft.Network/networkInterfaces/coreo-westu-1430261891570-nic
    data:          Primary                   :true
    data:          MAC Address               :00-0D-3A-30-72-E3
    data:          Provisioning State        :Succeeded
    data:          Name                      :coreo-westu-1430261891570-nic
    data:          Location                  :westus
    data:            Private IP alloc-method :Dynamic
    data:            Private IP address      :10.0.1.4
    data:            Public IP address       :104.40.24.124
    data:            FQDN                    :coreo-westu-1430261891570-pip.westus.cloudapp.azure.com
    info:    vm quick-create command OK

Et vous voici avec une nouvelle machine virtuelle.

## <a id="deploy-a-vm-in-azure-from-a-template"></a>Tâche : Déployer une machine virtuelle dans Azure à partir d’un modèle

Suivez les instructions des sections suivantes pour déployer une nouvelle machine virtuelle Azure à l’aide d’un modèle avec l’interface de ligne de commande Azure. Ce modèle crée une seule machine virtuelle dans un réseau virtuel avec un seul sous-réseau et, contrairement à la commande `azure vm quick-create`, il permet de décrire précisément ce que vous voulez et de le répéter sans erreurs. Voici le résultat du modèle :

![](./media/virtual-machines-common-cli-deploy-templates/new-vm.png)

### Étape 1 : examen du fichier JSON pour obtenir les paramètres du modèle

Voici le contenu du fichier JSON relatif au modèle. (Ce modèle est également disponible sur [GitHub](https://raw.githubusercontent.com/Azure/azure-quickstart-templates/master/101-simple-linux-vm/azuredeploy.json).)

Le concepteur d’un modèle peut choisir de le rendre flexible en le dotant de nombreux paramètres modifiables, ou rigide, en le dotant uniquement de quelques paramètres. Pour collecter les informations, vous devez transmettre le modèle en tant que série de paramètres, ouvrir le fichier du modèle (cette rubrique comporte un modèle inline, ci-dessous) et examiner les valeurs **parameters**.

Dans ce cas, le modèle ci-dessous vous demandera :

- Un nom de compte de stockage unique.
- Un nom d’utilisateur administrateur de la machine virtuelle.
- Un mot de passe.
- Un nom de domaine pour le monde extérieur à utiliser.
- Un numéro de version Ubuntu Server, mais accepte uniquement l’un de ceux répertoriés dans une liste.

Une fois que vous avez déterminé ces valeurs, vous pouvez créer un groupe pour votre abonnement Azure et y déployer ce modèle.

    {
    "$schema": "https://schema.management.azure.com/schemas/2015-01-01/deploymentTemplate.json#",
    "contentVersion": "1.0.0.0",
    "parameters": {
        "newStorageAccountName": {
        "type": "string",
        "metadata": {
            "description": "Unique DNS name for the storage account where the virtual machine's disks will be placed."
        }
        },
        "adminUsername": {
        "type": "string",
        "metadata": {
            "description": "User name for the virtual machine."
        }
        },
        "adminPassword": {
        "type": "securestring",
        "metadata": {
            "description": "Password for the virtual machine."
        }
        },
        "dnsNameForPublicIP": {
        "type": "string",
        "metadata": {
            "description": "Unique DNS name for the public IP used to access the virtual machine."
        }
        },
        "ubuntuOSVersion": {
        "type": "string",
        "defaultValue": "14.04.2-LTS",
        "allowedValues": [
            "12.04.5-LTS",
            "14.04.2-LTS",
            "15.04"
        ],
        "metadata": {
            "description": "The Ubuntu version for the VM. This will pick a fully patched image of this given Ubuntu version. Allowed values: 12.04.5-LTS, 14.04.2-LTS, 15.04."
        }
        }
    },
    "variables": {
        "location": "West US",
        "imagePublisher": "Canonical",
        "imageOffer": "UbuntuServer",
        "OSDiskName": "osdiskforlinuxsimple",
        "nicName": "myVMNic",
        "addressPrefix": "10.0.0.0/16",
        "subnetName": "Subnet",
        "subnetPrefix": "10.0.0.0/24",
        "storageAccountType": "Standard_LRS",
        "publicIPAddressName": "myPublicIP",
        "publicIPAddressType": "Dynamic",
        "vmStorageAccountContainerName": "vhds",
        "vmName": "MyUbuntuVM",
        "vmSize": "Standard_D1",
        "virtualNetworkName": "MyVNET",
        "vnetID": "[resourceId('Microsoft.Network/virtualNetworks',variables('virtualNetworkName'))]",
        "subnetRef": "[concat(variables('vnetID'),'/subnets/',variables('subnetName'))]"
    },
    "resources": [
        {
        "type": "Microsoft.Storage/storageAccounts",
        "name": "[parameters('newStorageAccountName')]",
        "apiVersion": "2015-05-01-preview",
        "location": "[variables('location')]",
        "properties": {
            "accountType": "[variables('storageAccountType')]"
        }
        },
        {
        "apiVersion": "2015-05-01-preview",
        "type": "Microsoft.Network/publicIPAddresses",
        "name": "[variables('publicIPAddressName')]",
        "location": "[variables('location')]",
        "properties": {
            "publicIPAllocationMethod": "[variables('publicIPAddressType')]",
            "dnsSettings": {
            "domainNameLabel": "[parameters('dnsNameForPublicIP')]"
            }
        }
        },
        {
        "apiVersion": "2015-05-01-preview",
        "type": "Microsoft.Network/virtualNetworks",
        "name": "[variables('virtualNetworkName')]",
        "location": "[variables('location')]",
        "properties": {
            "addressSpace": {
            "addressPrefixes": [
                "[variables('addressPrefix')]"
            ]
            },
            "subnets": [
            {
                "name": "[variables('subnetName')]",
                "properties": {
                "addressPrefix": "[variables('subnetPrefix')]"
                }
            }
            ]
        }
        },
        {
        "apiVersion": "2015-05-01-preview",
        "type": "Microsoft.Network/networkInterfaces",
        "name": "[variables('nicName')]",
        "location": "[variables('location')]",
        "dependsOn": [
            "[concat('Microsoft.Network/publicIPAddresses/', variables('publicIPAddressName'))]",
            "[concat('Microsoft.Network/virtualNetworks/', variables('virtualNetworkName'))]"
        ],
        "properties": {
            "ipConfigurations": [
            {
                "name": "ipconfig1",
                "properties": {
                "privateIPAllocationMethod": "Dynamic",
                "publicIPAddress": {
                    "id": "[resourceId('Microsoft.Network/publicIPAddresses',variables('publicIPAddressName'))]"
                },
                "subnet": {
                    "id": "[variables('subnetRef')]"
                }
                }
            }
            ]
        }
        },
        {
        "apiVersion": "2015-05-01-preview",
        "type": "Microsoft.Compute/virtualMachines",
        "name": "[variables('vmName')]",
        "location": "[variables('location')]",
        "dependsOn": [
            "[concat('Microsoft.Storage/storageAccounts/', parameters('newStorageAccountName'))]",
            "[concat('Microsoft.Network/networkInterfaces/', variables('nicName'))]"
        ],
        "properties": {
            "hardwareProfile": {
            "vmSize": "[variables('vmSize')]"
            },
            "osProfile": {
            "computername": "[variables('vmName')]",
            "adminUsername": "[parameters('adminUsername')]",
            "adminPassword": "[parameters('adminPassword')]"
            },
            "storageProfile": {
            "imageReference": {
                "publisher": "[variables('imagePublisher')]",
                "offer": "[variables('imageOffer')]",
                "sku": "[parameters('ubuntuOSVersion')]",
                "version": "latest"
            },
            "osDisk": {
                "name": "osdisk",
                "vhd": {
                "uri": "[concat('http://',parameters('newStorageAccountName'),'.blob.core.windows.net/',variables('vmStorageAccountContainerName'),'/',variables('OSDiskName'),'.vhd')]"
                },
                "caching": "ReadWrite",
                "createOption": "FromImage"
            }
            },
            "networkProfile": {
            "networkInterfaces": [
                {
                "id": "[resourceId('Microsoft.Network/networkInterfaces',variables('nicName'))]"
                }
            ]
            }
        }
        }
    ]
    }


### Étape 2 : création de la machine virtuelle à l’aide du modèle

Lorsque vos valeurs de paramètres sont prêtes, vous devez créer un groupe de ressources pour le déploiement de votre modèle avant de procéder à celui-ci.

Pour créer le groupe de ressources, tapez `azure group create <group name> <location>` en indiquant le nom du groupe de votre choix et l'emplacement du centre de données où a lieu le déploiement. Le processus est rapide :

    azure group create myResourceGroup westus
    info:    Executing command group create
    + Getting resource group myResourceGroup
    + Creating resource group myResourceGroup
    info:    Created resource group myResourceGroup
    data:    Id:                  /subscriptions/xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx/resourceGroups/myResourceGroup
    data:    Name:                myResourceGroup
    data:    Location:            westus
    data:    Provisioning State:  Succeeded
    data:    Tags:
    data:
    info:    group create command OK


À présent, pour créer le déploiement, appelez `azure group deployment create` et transmettez :

- Le fichier du modèle (si vous avez enregistré le modèle JSON ci-dessus dans un fichier local).
- Un URI de modèle (si vous souhaitez pointer vers le fichier dans GitHub ou vers une autre adresse web).
- Le groupe de ressources dans lequel le déploiement a lieu.
- Un nom pour le déploiement (facultatif).

Vous êtes invité à entrer les valeurs des paramètres dans la section "parameters" du fichier JSON. Lorsque vous avez indiqué toutes les valeurs des paramètres, votre déploiement commence.

Voici un exemple :

    azure group deployment create --template-uri https://raw.githubusercontent.com/Azure/azure-quickstart-templates/master/101-simple-linux-vm/azuredeploy.json myResourceGroup firstDeployment
    info:    Executing command group deployment create
    info:    Supply values for the following parameters
    newStorageAccountName: storageaccount
    adminUsername: ops
    adminPassword: password
    dnsNameForPublicIP: newdomainname

Le type d'information suivant s'affiche :

    + Initializing template configurations and parameters
    + Creating a deployment
    info:    Created template deployment "firstDeployment"
    + Registering providers
    info:    Registering provider microsoft.storage
    info:    Registering provider microsoft.network
    info:    Registering provider microsoft.compute
    + Waiting for deployment to complete
    data:    DeploymentName     : firstDeployment
    data:    ResourceGroupName  : myResourceGroup
    data:    ProvisioningState  : Succeeded
    data:    Timestamp          : 2015-04-28T07:53:55.1828878Z
    data:    Mode               : Incremental
    data:    TemplateLink       : https://raw.githubusercontent.com/Azure/azure-quickstart-templates/master/101-simple-linux-vm/azuredeploy.json
    data:    ContentVersion     : 1.0.0.0
    data:    Name                   Type          Value
    data:    ---------------------  ------------  -------------
    data:    newStorageAccountName  String        storageaccount
    data:    adminUsername          String        ops
    data:    adminPassword          SecureString  undefined
    data:    dnsNameForPublicIP     String        newdomainname
    data:    ubuntuOSVersion        String        14.10
    info:    group deployment create command OK



## <a id="create-a-custom-vm-image"></a>Tâche : Créer une image de machine virtuelle personnalisée

Maintenant que vous avez vu l’utilisation basique des modèles, vous pouvez utiliser des instructions similaires pour créer une machine virtuelle personnalisée à partir d’un fichier .vhd spécifique dans Azure à l’aide d’un modèle utilisant l’interface de ligne de commande Azure. La différence est qu’ici, ce modèle crée une seule machine virtuelle à partir d’un disque dur virtuel spécifié.

### Étape 1 : examen du fichier JSON pour obtenir le modèle

Voici le contenu du fichier JSON pour le modèle utilisé à titre d’exemple dans cette section.

Ici encore, vous devez rechercher les valeurs que vous souhaitez entrer pour les paramètres qui n’ont pas de valeurs par défaut. Lorsque vous exécutez la commande `azure group deployment create`, l’interface de ligne de commande Azure vous invite à entrer ces valeurs.

    {
        "$schema": "http://schema.management.azure.com/schemas/2014-04-01-preview/deploymentTemplate.json",
        "contentVersion": "1.0.0.0",
        "parameters" : {
            "userImageStorageAccountName": {
                "type": "string",
                "defaultValue" : "userImageStorageAccountName"
            },
            "userImageStorageContainerName" : {
                "type" : "string",
                "defaultValue" : "userImageStorageContainerName"
            },
            "userImageVhdName" : {
                "type" : "string",
                "defaultValue" : "userImageVhdName"
            },
            "dnsNameForPublicIP" : {
                "type" : "string",
                "defaultValue": "uniqueDnsNameForPublicIP"
            },
            "adminUserName": {
                "type": "string"
            },
            "adminPassword": {
                "type": "securestring"
            },
            "osType" : {
                "type" : "string",
                "allowedValues" : [
                    "windows",
                    "linux"
                ]
            },
            "subscriptionId":{
                "type" : "string"
            },
            "location": {
                "type": "String",
                "defaultValue" : "West US"
            },
            "vmSize": {
                "type": "string",
                "defaultValue": "Standard_A2"
            },
            "publicIPAddressName": {
                "type": "string",
                "defaultValue" : "myPublicIP"
            },
            "vmName": {
                "type": "string",
                "defaultValue" : "myVM"
            },
            "virtualNetworkName":{
                "type" : "string",
                "defaultValue" : "myVNET"
            },
            "nicName":{
                "type" : "string",
                "defaultValue":"myNIC"
            }
        },
        "variables": {
            "addressPrefix":"10.0.0.0/16",
            "subnet1Name": "Subnet-1",
            "subnet2Name": "Subnet-2",
            "subnet1Prefix" : "10.0.0.0/24",
            "subnet2Prefix" : "10.0.1.0/24",
            "publicIPAddressType" : "Dynamic",
            "vnetID":"[resourceId('Microsoft.Network/virtualNetworks',parameters('virtualNetworkName'))]",
            "subnet1Ref" : "[concat(variables('vnetID'),'/subnets/',variables('subnet1Name'))]",
            "userImageName" : "[concat('http://',parameters('userImageStorageAccountName'),'.blob.core.windows.net/',parameters('userImageStorageContainerName'),'/',parameters('userImageVhdName'))]",
            "osDiskVhdName" : "[concat('http://',parameters('userImageStorageAccountName'),'.blob.core.windows.net/',parameters('userImageStorageContainerName'),'/',parameters('vmName'),'osDisk.vhd')]"
        },
        "resources": [
        {
            "apiVersion": "2014-12-01-preview",
            "type": "Microsoft.Network/publicIPAddresses",
            "name": "[parameters('publicIPAddressName')]",
            "location": "[parameters('location')]",
            "properties": {
                "publicIPAllocationMethod": "[variables('publicIPAddressType')]",
                "dnsSettings": {
                    "domainNameLabel": "[parameters('dnsNameForPublicIP')]"
                }
            }
        },
        {
          "apiVersion": "2014-12-01-preview",
          "type": "Microsoft.Network/virtualNetworks",
          "name": "[parameters('virtualNetworkName')]",
          "location": "[parameters('location')]",
          "properties": {
            "addressSpace": {
              "addressPrefixes": [
                "[variables('addressPrefix')]"
              ]
            },
            "subnets": [
              {
                "name": "[variables('subnet1Name')]",
                "properties" : {
                    "addressPrefix": "[variables('subnet1Prefix')]"
                }
              },
              {
                "name": "[variables('subnet2Name')]",
                "properties" : {
                    "addressPrefix": "[variables('subnet2Prefix')]"
                }
              }
            ]
          }
        },
        {
            "apiVersion": "2014-12-01-preview",
            "type": "Microsoft.Network/networkInterfaces",
            "name": "[parameters('nicName')]",
            "location": "[parameters('location')]",
            "dependsOn": [
                "[concat('Microsoft.Network/publicIPAddresses/', parameters('publicIPAddressName'))]",
                "[concat('Microsoft.Network/virtualNetworks/', parameters('virtualNetworkName'))]"
            ],
            "properties": {
                "ipConfigurations": [
                {
                    "name": "ipconfig1",
                    "properties": {
                        "privateIPAllocationMethod": "Dynamic",
                        "publicIPAddress": {
                            "id": "[resourceId('Microsoft.Network/publicIPAddresses',parameters('publicIPAddressName'))]"
                        },
                        "subnet": {
                            "id": "[variables('subnet1Ref')]"
                        }
                    }
                }
                ]
            }
        },
        {
            "apiVersion": "2014-12-01-preview",
            "type": "Microsoft.Compute/virtualMachines",
            "name": "[parameters('vmName')]",
            "location": "[parameters('location')]",
            "dependsOn": [
                "[concat('Microsoft.Network/networkInterfaces/', parameters('nicName'))]"
            ],
            "properties": {
                "hardwareProfile": {
                    "vmSize": "[parameters('vmSize')]"
                },
                "osProfile": {
                    "computername": "[parameters('vmName')]",
                    "adminUsername": "[parameters('adminUsername')]",
                    "adminPassword": "[parameters('adminPassword')]"
                },
                "storageProfile": {
                    "osDisk" : {
                        "name" : "[concat(parameters('vmName'),'-osDisk')]",
                        "osType" : "[parameters('osType')]",
                        "caching" : "ReadWrite",
                        "image" : {
                            "uri" : "[variables('userImageName')]"
                        },
                        "vhd" : {
                            "uri" : "[variables('osDiskVhdName')]"
                        }
                    }
                },
                "networkProfile": {
                    "networkInterfaces" : [
                    {
                        "id": "[resourceId('Microsoft.Network/networkInterfaces',parameters('nicName'))]"
                    }
                    ]
                }
            }
        }
        ]
    }

### Étape 2 : récupérer le disque dur virtuel

Évidemment, vous aurez besoin d’un fichier .vhd pour cela. Vous pouvez utiliser un fichier dont vous disposez dans Azure ou en télécharger un.

Dans le cas d’une machine virtuelle Windows, consultez la page [Création et téléchargement d’un disque dur virtuel Windows Server dans Azure](../articles/virtual-machines/virtual-machines-windows-classic-createupload-vhd.md).

Pour une machine virtuelle Linux, consultez [Création et téléchargement d'un disque dur virtuel contenant le système d'exploitation Linux](../articles/virtual-machines/virtual-machines-linux-classic-create-upload-vhd.md).

### Étape 3 : création de la machine virtuelle à l’aide du modèle

Vous êtes maintenant prêt à créer une machine virtuelle basée sur le fichier .vhd. Créez un groupe pour le déploiement avec la commande `azure group create <location>` :

    azure group create myResourceGroupUser eastus
    info:    Executing command group create
    + Getting resource group myResourceGroupUser
    + Creating resource group myResourceGroupUser
    info:    Created resource group myResourceGroupUser
    data:    Id:                  /subscriptions/xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx/resourceGroups/myResourceGroupUser
    data:    Name:                myResourceGroupUser
    data:    Location:            eastus
    data:    Provisioning State:  Succeeded
    data:    Tags:
    data:
    info:    group create command OK

Ensuite, créez le déploiement avec l'option `--template-uri` pour appeler directement le modèle (ou utilisez l'option `--template-file` pour utiliser un fichier enregistré localement). Notez que, comme le modèle possède des valeurs par défaut, vous n’avez pas à renseigner beaucoup d’invites. Si vous déployez le modèle à différents endroits, il est possible que des conflits de noms se produisent vis-à-vis des valeurs par défaut (notamment le nom DNS que vous avez créé).

    azure group deployment create \
    > --template-uri https://raw.githubusercontent.com/Azure/azure-quickstart-templates/master/101-vm-from-user-image/azuredeploy.json \
    > myResourceGroup \
    > customVhdDeployment
    info:    Executing command group deployment create
    info:    Supply values for the following parameters
    adminUserName: ops
    adminPassword: password
    osType: linux
    subscriptionId: xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx

Le résultat ressemble à cela :

    + Initializing template configurations and parameters
    + Creating a deployment
    info:    Created template deployment "customVhdDeployment"
    + Registering providers
    info:    Registering provider microsoft.network
    info:    Registering provider microsoft.compute
    + Waiting for deployment to complete
    error:   Deployment provisioning state was not successful
    data:    DeploymentName     : customVhdDeployment
    data:    ResourceGroupName  : myResourceGroupUser
    data:    ProvisioningState  : Succeeded
    data:    Timestamp          : 2015-04-28T14:55:48.0963829Z
    data:    Mode               : Incremental
    data:    TemplateLink       : https://raw.githubusercontent.com/Azure/azure-quickstart-templates/master/101-vm-from-user-image/azuredeploy.json
    data:    ContentVersion     : 1.0.0.0
    data:    Name                           Type          Value
    data:    -----------------------------  ------------  ------------------------------------
    data:    userImageStorageAccountName    String        userImageStorageAccountName
    data:    userImageStorageContainerName  String        userImageStorageContainerName
    data:    userImageVhdName               String        userImageVhdName
    data:    dnsNameForPublicIP             String        uniqueDnsNameForPublicIP
    data:    adminUserName                  String        ops
    data:    adminPassword                  SecureString  undefined
    data:    osType                         String        linux
    data:    subscriptionId                 String        xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx
    data:    location                       String        West US
    data:    vmSize                         String        Standard_A2
    data:    publicIPAddressName            String        myPublicIP
    data:    vmName                         String        myVM
    data:    virtualNetworkName             String        myVNET
    data:    nicName                        String        myNIC
    info:    group deployment create command OK


## <a id="deploy-a-multi-vm-application-that-uses-a-virtual-network-and-an-external-load-balancer"></a>Tâche : Déployer une application de plusieurs machines virtuelles utilisant un réseau virtuel et un équilibreur de charge externe

Ce modèle vous permet de créer deux machines virtuelles sous un équilibreur de charge et de configurer une règle d’équilibrage de charge sur le port 80. Il déploie également un compte de stockage, un réseau virtuel, une adresse IP publique, un groupe à haute disponibilité et des interfaces réseau.

![](./media/virtual-machines-common-cli-deploy-templates/multivmextlb.png)

Procédez comme suit pour déployer une application de plusieurs machines virtuelles utilisant un réseau virtuel et un équilibreur de charge à l’aide d’un modèle du Gestionnaire des ressources dans le référentiel de modèles GitHub via des commandes Azure PowerShell.

### Étape 1 : examen du fichier JSON pour obtenir le modèle

Voici le contenu du fichier JSON relatif au modèle. Si vous voulez la version la plus récente, elle se trouve [dans le référentiel GitHub de modèles](https://raw.githubusercontent.com/Azure/azure-quickstart-templates/master/201-2-vms-loadbalancer-lbrules/azuredeploy.json). Cette rubrique utilise le commutateur `--template-uri` pour appeler le modèle, mais vous pouvez également utiliser le commutateur `--template-file` pour transmettre une version locale.


    {
        "$schema": "http://schema.management.azure.com/schemas/2014-04-01-preview/deploymentTemplate.json",
        "contentVersion": "1.0.0.0",
        "parameters": {
            "location": {
                "type": "string",
                "metadata": {
                  "description": "Location of resources"
                }
            },
            "storageAccountName": {
                "type": "string",
                "metadata": {
                  "description": "Name of storage account"
                }
            },
            "adminUsername": {
                "type": "string",
                "metadata": {
                  "description": "Admin user name"
                }
            },
            "adminPassword": {
                "type": "securestring",
                "metadata": {
                  "description": "Admin password"
                }
            },
            "dnsNameforLBIP": {
                "type": "string",
                "metadata": {
                  "description": "DNS for load balancer IP"
                }
            },
            "backendPort": {
                "type": "int",
                "defaultValue": 3389,
                "metadata": {
                  "description": "Back-end port"
                }
            },
            "vmNamePrefix": {
                "type": "string",
                "defaultValue": "myVM",
                "metadata": {
                  "description": "Prefix to use for VM names"
                }
            },
            "vmSourceImageName": {
                "type": "string",
                "defaultValue": "a699494373c04fc0bc8f2bb1389d6106__Windows-Server-2012-R2-201412.01-en.us-127GB.vhd"
            },
            "lbName": {
                "type": "string",
                "defaultValue": "myLB",
                "metadata": {
                  "description": "Load balancer name"
                }
            },
            "nicNamePrefix": {
                "type": "string",
                "defaultValue": "nic",
                "metadata": {
                  "description": "Network interface name prefix"
                }
            },
            "publicIPAddressName": {
                "type": "string",
                "defaultValue": "myPublicIP",
                "metadata": {
                  "description": "Public IP name"
                }
            },
            "vnetName": {
                "type": "string",
                "defaultValue": "myVNET",
                "metadata": {
                  "description": "Virtual network name"
                }
            },
            "vmSize": {
                "type": "string",
                "defaultValue": "Standard_A1",
                "metadata": {
                  "description": "Size of the VM"
                }
            }
        },
        "variables": {
            "storageAccountType": "Standard_LRS",
            "vmStorageAccountContainerName": "vhds",
            "availabilitySetName": "myAvSet",
            "addressPrefix": "10.0.0.0/16",
            "subnetName": "Subnet-1",
            "subnetPrefix": "10.0.0.0/24",
            "publicIPAddressType": "Dynamic",
            "vnetID": "[resourceId('Microsoft.Network/virtualNetworks',parameters('vnetName'))]",
            "subnetRef": "[concat(variables('vnetID'),'/subnets/',variables ('subnetName'))]",
            "publicIPAddressID": "[resourceId('Microsoft.Network/publicIPAddresses',parameters('publicIPAddressName'))]",
            "lbID": "[resourceId('Microsoft.Network/loadBalancers',parameters('lbName'))]",
            "numberOfInstances": 2,
            "nicId1": "[resourceId('Microsoft.Network/networkInterfaces',concat(parameters('nicNamePrefix'), 0))]",
            "nicId2": "[resourceId('Microsoft.Network/networkInterfaces',concat(parameters('nicNamePrefix'), 1))]",
            "frontEndIPConfigID": "[concat(variables('lbID'),'/frontendIPConfigurations/LBFE')]",
            "backEndIPConfigID1": "[concat(variables('nicId1'),'/ipConfigurations/ipconfig1')]",
            "backEndIPConfigID2": "[concat(variables('nicId2'),'/ipConfigurations/ipconfig1')]",
            "sourceImageName": "[concat('/', subscription().subscriptionId,'/services/images/',parameters('vmSourceImageName'))]",
            "lbPoolID": "[concat(variables('lbID'),'/backendAddressPools/LBBE')]",
            "lbProbeID": "[concat(variables('lbID'),'/probes/tcpProbe')]"
        },
        "resources": [
            {
                "type": "Microsoft.Storage/storageAccounts",
                "name": "[parameters('storageAccountName')]",
                "apiVersion": "2015-05-01-preview",
                "location": "[parameters('location')]",
                "properties": {
                    "accountType": "[variables('storageAccountType')]"
                }
            },
            {
                "type": "Microsoft.Compute/availabilitySets",
                "name": "[variables('availabilitySetName')]",
                "apiVersion": "2015-05-01-preview",
                "location": "[parameters('location')]",
                "properties": { }
            },
            {
                "apiVersion": "2015-05-01-preview",
                "type": "Microsoft.Network/publicIPAddresses",
                "name": "[parameters('publicIPAddressName')]",
                "location": "[parameters('location')]",
                "properties": {
                    "publicIPAllocationMethod": "[variables('publicIPAddressType')]",
                    "dnsSettings": {
                        "domainNameLabel": "[parameters('dnsNameforLBIP')]"
                    }
                }
            },
            {
                "apiVersion": "2015-05-01-preview",
                "type": "Microsoft.Network/virtualNetworks",
                "name": "[parameters('vnetName')]",
                "location": "[parameters('location')]",
                "properties": {
                    "addressSpace": {
                        "addressPrefixes": [
                            "[variables('addressPrefix')]"
                        ]
                    },
                    "subnets": [
                        {
                            "name": "[variables('subnetName')]",
                            "properties": {
                                "addressPrefix": "[variables('subnetPrefix')]"
                            }
                        }
                    ]
                }
            },
            {
                "apiVersion": "2015-05-01-preview",
                "type": "Microsoft.Network/networkInterfaces",
                "name": "[concat(parameters('nicNamePrefix'), copyindex())]",
                "location": "[parameters('location')]",
                "copy": {
                    "name": "nicLoop",
                    "count": "[variables('numberOfInstances')]"
                },
                "dependsOn": [
                    "[concat('Microsoft.Network/virtualNetworks/', parameters('vnetName'))]"
                ],
                "properties": {
                    "ipConfigurations": [
                        {
                            "name": "ipconfig1",
                            "properties": {
                                "privateIPAllocationMethod": "Dynamic",
                                "subnet": {
                                    "id": "[variables('subnetRef')]"
                                }
                            },
                            "loadBalancerBackendAddressPools": [
                                {
                                    "id": "[concat('Microsoft.Network/loadBalancers/',parameters('lbName'),'/backendAddressPools/LBBE')]"
                                }
                            ],
                            "loadBalancerInboundNatRules": [
                                {
                                    "id": "[concat('Microsoft.Network/loadBalancers/',parameters('lbName'),'/inboundNatRule/RDP-VM', copyindex())]"
                                }
                            ]


                        }
                    ]

                }
            },
            {
                "apiVersion": "2015-05-01-preview",
                "name": "[parameters('lbName')]",
                "type": "Microsoft.Network/loadBalancers",
                "location": "[parameters('location')]",
                "dependsOn": [
                    "nicLoop",
                    "[concat('Microsoft.Network/publicIPAddresses/', parameters('publicIPAddressName'))]"
                ],
                "properties": {
                    "frontendIPConfigurations": [
                        {
                            "name": "LBFE",
                            "properties": {
                                "publicIPAddress": {
                                    "id": "[variables('publicIPAddressID')]"
                                }
                            }
                        }
                    ],
                    "backendAddressPools": [
                        {
                            "name": "LBBE"

                        }
                    ],
                    "inboundNatRules": [
                        {
                            "name": "RDP-VM1",
                            "properties": {
                                "frontendIPConfiguration":
                                    {
                                        "id": "[variables('frontEndIPConfigID')]"
                                    },
                                "protocol": "tcp",
                                "frontendPort": 50001,
                                "backendPort": 3389,
                                "enableFloatingIP": false
                            }
                        },
                        {
                            "name": "RDP-VM2",
                            "properties": {
                                "frontendIPConfiguration":
                                    {
                                        "id": "[variables('frontEndIPConfigID')]"
                                    },
                                "protocol": "tcp",
                                "frontendPort": 50002,
                                "backendPort": 3389,
                                "enableFloatingIP": false
                            }
                        }
                    ],
                    "loadBalancingRules": [
                        {
                            "name": "LBRule",
                            "properties": {
                                "frontendIPConfiguration": {
                                    "id": "[variables('frontEndIPConfigID')]"
                                },
                                "backendAddressPool": {
                                    "id": "[variables('lbPoolID')]"
                                },
                                "protocol": "tcp",
                                "frontendPort": 80,
                                "backendPort": 80,
                                "enableFloatingIP": false,
                                "idleTimeoutInMinutes": 5,
                                "probe": {
                                    "id": "[variables('lbProbeID')]"
                                }
                            }
                        }
                    ],
                    "probes": [
                        {
                            "name": "tcpProbe",
                            "properties": {
                                "protocol": "tcp",
                                "port": 80,
                                "intervalInSeconds": "5",
                                "numberOfProbes": "2"
                            }
                        }
                    ]
                }
            },
            {
                "apiVersion": "2015-05-01-preview",
                "type": "Microsoft.Compute/virtualMachines",
                "name": "[concat(parameters('vmNamePrefix'), copyindex())]",
                "copy": {
                    "name": "virtualMachineLoop",
                    "count": "[variables('numberOfInstances')]"
                },
                "location": "[parameters('location')]",
                "dependsOn": [
                    "[concat('Microsoft.Storage/storageAccounts/', parameters('storageAccountName'))]",
                    "[concat('Microsoft.Network/networkInterfaces/', parameters('nicNamePrefix'), copyindex())]",
                    "[concat('Microsoft.Compute/availabilitySets/', variables('availabilitySetName'))]"
                ],
                "properties": {
                    "availabilitySet": {
                        "id": "[resourceId('Microsoft.Compute/availabilitySets',variables('availabilitySetName'))]"
                    },
                    "hardwareProfile": {
                        "vmSize": "[parameters('vmSize')]"
                    },
                    "osProfile": {
                        "computername": "[concat(parameters('vmNamePrefix'), copyIndex())]",
                        "adminUsername": "[parameters('adminUsername')]",
                        "adminPassword": "[parameters('adminPassword')]"
                    },
                    "storageProfile": {
                        "sourceImage": {
                            "id": "[variables('sourceImageName')]"
                        },
                        "destinationVhdsContainer": "[concat('http://',parameters('storageAccountName'),'.blob.core.windows.net/',variables('vmStorageAccountContainerName'),'/')]"
                    },
                    "networkProfile": {
                        "networkInterfaces": [
                            {
                                "id": "[resourceId('Microsoft.Network/networkInterfaces',concat(parameters('nicNamePrefix'),copyindex()))]"
                            }
                        ]
                    }
                }
            }
        ]
    }

### Étape 2 : création du déploiement à l’aide du modèle

Créez un groupe de ressources pour le modèle à l’aide de `azure group create <location>`. Ensuite, créez un déploiement dans ce groupe de ressources à l'aide de la commande `azure group deployment create`, puis transmettez le groupe de ressources et un nom de déploiement et renseignez les invites relatives aux paramètres du modèle n'ayant pas de valeurs par défaut.


    azure group create lbgroup westus
    info:    Executing command group create
    + Getting resource group lbgroup
    + Creating resource group lbgroup
    info:    Created resource group lbgroup
    data:    Id:                  /subscriptions/xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx/resourceGroups/lbgroup
    data:    Name:                lbgroup
    data:    Location:            westus
    data:    Provisioning State:  Succeeded
    data:    Tags:
    data:
    info:    group create command OK


À présent, utilisez la commande `azure group deployment create` et l’option `--template-uri` pour déployer le modèle. Préparez-vous à renseigner les invites relatives aux valeurs de paramètres, comme indiqué ci-dessous.

    azure group deployment create \
    > --template-uri https://raw.githubusercontent.com/Azure/azure-quickstart-templates/master/201-2-vms-loadbalancer-lbrules/azuredeploy.json \
    > lbgroup \
    > newdeployment
    info:    Executing command group deployment create
    info:    Supply values for the following parameters
    location: westus
    newStorageAccountName: storagename
    adminUsername: ops
    adminPassword: password
    dnsNameforLBIP: lbdomainname
    + Initializing template configurations and parameters
    + Creating a deployment
    info:    Created template deployment "newdeployment"
    + Registering providers
    info:    Registering provider microsoft.storage
    info:    Registering provider microsoft.compute
    info:    Registering provider microsoft.network
    + Waiting for deployment to complete
    data:    DeploymentName     : newdeployment
    data:    ResourceGroupName  : lbgroup
    data:    ProvisioningState  : Succeeded
    data:    Timestamp          : 2015-04-28T20:58:40.1678876Z
    data:    Mode               : Incremental
    data:    TemplateLink       : https://raw.githubusercontent.com/Azure/azure-quickstart-templates/master/201-2-vms-loadbalancer-lbrules/azuredeploy.json
    data:    ContentVersion     : 1.0.0.0
    data:    Name                   Type          Value
    data:    ---------------------  ------------  ----------------------
    data:    location               String        westus
    data:    newStorageAccountName  String        storagename
    data:    adminUsername          String        ops
    data:    adminPassword          SecureString  undefined
    data:    dnsNameforLBIP         String        lbdomainname
    data:    backendPort            Int           3389
    data:    vmNamePrefix           String        myVM
    data:    imagePublisher         String        MicrosoftWindowsServer
    data:    imageOffer             String        WindowsServer
    data:    imageSKU               String        2012-R2-Datacenter
    data:    lbName                 String        myLB
    data:    nicNamePrefix          String        nic
    data:    publicIPAddressName    String        myPublicIP
    data:    vnetName               String        myVNET
    data:    vmSize                 String        Standard_A1
    info:    group deployment create command OK

Notez que ce modèle déploie une image Windows Server ; toutefois, vous pouvez facilement la remplacer par n’importe quelle image Linux. Vous souhaitez créer un cluster Docker avec plusieurs gestionnaires swarm ? [Vous pouvez le faire](https://azure.microsoft.com/documentation/templates/docker-swarm-cluster/).

## <a id="remove-a-resource-group"></a>Tâche : Supprimer un groupe de ressources

N'oubliez pas que vous pouvez effectuer un redéploiement dans un groupe de ressources, mais si vous ne voulez plus en utiliser un, vous pouvez le supprimer en utilisant `azure group delete <group name>`.

    azure group delete myResourceGroup
    info:    Executing command group delete
    Delete resource group myResourceGroup? [y/n] y
    + Deleting resource group myResourceGroup
    info:    group delete command OK

## <a id="show-the-log-for-a-resource-group-deployment"></a>Tâche : Afficher le journal d'un déploiement de groupe de ressources

Cette tâche est courante lors de la création ou de l’utilisation de modèles. L’appel permettant d’afficher les journaux de déploiement d’un groupe est `azure group log show <groupname>`. Cela entraîne l’affichage d’informations utiles pour comprendre pourquoi un événement s’est produit ou ne s’est pas produit. Pour plus d'informations sur la résolution des problèmes de vos déploiements, mais aussi pour obtenir des informations supplémentaires sur les problèmes, consultez [Résolution des problèmes liés aux déploiements de groupes de ressources dans Azure](../articles/resource-manager-troubleshoot-deployments-cli.md).

Pour résoudre certaines défaillances, vous pouvez utiliser des outils tels que **jq** pour obtenir des renseignements plus précis, comme par exemple, l’identification des défaillances individuelles à corriger. L'exemple suivant utilise **jq** pour analyser un journal de déploiement à la recherche de défaillances dans **lbgroup**.

    azure group log show lbgroup -l --json | jq '.[] | select(.status.value == "Failed") | .properties'

Vous pouvez découvrir très rapidement ce qui pose problème, le corriger et recommencer. Dans le cas suivant, le modèle a créé simultanément deux machines virtuelles, entraînant ainsi un verrouillage du fichier .vhd. Une fois le modèle modifié, le déploiement réussit rapidement.

    {
      "statusCode": "Conflict",
      "statusMessage": "{"status":"Failed","error":{"code":"ResourceDeploymentFailure","message":"The resource operation completed with terminal provisioning state 'Failed'.","details":[{"code":"AcquireDiskLeaseFailed","message":"Failed to acquire lease while creating disk 'osdisk' using blob with URI http://storage.blob.core.windows.net/vhds/osdisk.vhd."}]}}"
    }


## <a id="display-information-about-a-virtual-machine"></a>Tâche : Afficher les informations relatives à une machine virtuelle

Des informations relatives à des machines virtuelles spécifiques sont disponibles dans votre groupe de ressources. Consultez-les en utilisant la commande `azure vm show <groupname> <vmname> command`. Si vous avez plusieurs machines virtuelles dans votre groupe, vous aurez peut-être besoin de les répertorier dans un groupe à l'aide de la commande `azure vm list <groupname>`.

    azure vm list zoo
    info:    Executing command vm list
    + Getting virtual machines
    data:    Name   ProvisioningState  Location  Size
    data:    -----  -----------------  --------  -----------
    data:    myVM0  Succeeded          westus    Standard_A1
    data:    myVM1  Failed             westus    Standard_A1

Ensuite, recherchez myVM1 :

    azure vm show zoo myVM1
    info:    Executing command vm show
    + Looking up the VM "myVM1"
    + Looking up the NIC "nic1"
    data:    Id                              :/subscriptions/xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx/resourceGroups/zoo/providers/Microsoft.Compute/virtualMachines/myVM1
    data:    ProvisioningState               :Failed
    data:    Name                            :myVM1
    data:    Location                        :westus
    data:    Type                            :Microsoft.Compute/virtualMachines
    data:
    data:    Hardware Profile:
    data:      Size                          :Standard_A1
    data:
    data:    Storage Profile:
    data:      Image reference:
    data:        Publisher                   :MicrosoftWindowsServer
    data:        Offer                       :WindowsServer
    data:        Sku                         :2012-R2-Datacenter
    data:        Version                     :latest
    data:
    data:      OS Disk:
    data:        OSType                      :Windows
    data:        Name                        :osdisk
    data:        Caching                     :ReadWrite
    data:        CreateOption                :FromImage
    data:        Vhd:
    data:          Uri                       :http://zoostorageralph.blob.core.windows.net/vhds/osdisk.vhd
    data:
    data:    OS Profile:
    data:      Computer Name                 :myVM1
    data:      User Name                     :ops
    data:      Windows Configuration:
    data:        Provision VM Agent          :true
    data:        Enable automatic updates    :true
    data:
    data:    Network Profile:
    data:      Network Interfaces:
    data:        Network Interface #1:
    data:          Id                        :/subscriptions/xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx/resourceGroups/zoo/providers/Microsoft.Network/networkInterfaces/nic1
    data:          Primary                   :false
    data:          Provisioning State        :Succeeded
    data:          Name                      :nic1
    data:          Location                  :westus
    data:            Private IP alloc-method :Dynamic
    data:            Private IP address      :10.0.0.5
    data:
    data:    AvailabilitySet:
    data:      Id                            :/subscriptions/xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx/resourceGroups/zoo/providers/Microsoft.Compute/availabilitySets/MYAVSET
    info:    vm show command OK


> [AZURE.NOTE] Si vous souhaitez stocker et manipuler la sortie des commandes de votre console par programme, vous pouvez utiliser un outil d’analyse JSON tel que **[jq](https://github.com/stedolan/jq)**, **[jsawk](https://github.com/micha/jsawk)** ou les bibliothèques de langages adaptées à cette tâche.

## <a id="log-on-to-a-linux-based-virtual-machine"></a>Tâche : Se connecter à une machine virtuelle Linux

En général, les machines Linux sont connectées via SSH. Pour plus d’informations, consultez [Utilisation de SSH avec Linux dans Azure](../articles/virtual-machines/virtual-machines-linux-ssh-from-linux.md).

## <a id="stop-a-virtual-machine"></a>Tâche : Arrêter une machine virtuelle

Exécutez cette commande :

    azure vm stop <group name> <virtual machine name>

>[AZURE.IMPORTANT] Utilisez ce paramètre pour conserver l’adresse IP virtuelle du réseau virtuel s’il s’agit de la dernière machine virtuelle de ce réseau virtuel. <br><br>Si vous utilisez le paramètre `StayProvisioned`, vous êtes toujours facturé pour cette machine virtuelle.

## <a id="start-a-virtual-machine"></a>Tâche : Démarrer une machine virtuelle

Exécutez cette commande :

    azure vm start <group name> <virtual machine name>

## <a id="attach-a-data-disk"></a>Tâche : Associer un disque de données

Vous devez également décider d’associer un nouveau disque ou un disque existant, qui contient des données. Dans le cas d’un nouveau disque, cette même commande entraîne la création du fichier .vhd et son association.

Pour associer un nouveau disque, exécutez cette commande :

     azure vm disk attach-new <resource-group> <vm-name> <size-in-gb>

Pour associer un disque existant, exécutez cette commande :

    azure vm disk attach <resource-group> <vm-name> [vhd-url]

Vous devrez ensuite monter le disque, comme vous le feriez normalement sous Linux (ou sous Windows).


## Étapes suivantes

Pour consulter d’autres exemples d’utilisation de l’interface de ligne de commande Azure avec le mode **arm**, consultez [Utiliser l’interface de ligne de commande Azure pour Mac, Linux et Windows avec Azure Resource Manager](../articles/xplat-cli-azure-resource-manager.md). Pour en savoir plus sur les ressources Azure et leurs concepts, consultez la page [Présentation d'Azure Resource Manager](../articles/resource-group-overview.md).


Pour connaître les modèles supplémentaires utilisables, consultez [Modèles de démarrage rapide Azure](https://azure.microsoft.com/documentation/templates/) et [Infrastructures d'application à l'aide de modèles](../articles/virtual-machines/virtual-machines-linux-app-frameworks.md).

<!---HONumber=AcomDC_0330_2016-->