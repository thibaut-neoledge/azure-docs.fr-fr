<properties
	pageTitle="Mettez automatiquement à l’échelle les jeux de mise à l’échelle virtuelle | Microsoft Azure"
	description="Prise en main de la création et de la gestion de vos premiers ensembles de mise l’échelle de machine virtuelle Azure"
	services="virtual-machines"
	documentationCenter=""
	authors="gbowerman"
	manager="timlt"
	editor=""
	tags="azure-resource-manager"/>

<tags
	ms.service="virtual-machines"
	ms.workload="infrastructure-services"
	ms.tgt_pltfrm="na"
	ms.devlang="na"
	ms.topic="article"
	ms.date="11/11/2015"
	ms.author="guybo"/>

# Mise à l’échelle automatique des machines dans un jeu de mise à l’échelle de machine virtuelle

[AZURE.INCLUDE [learn-about-deployment-models](../../includes/learn-about-deployment-models-rm-include.md)] [classic deployment model](virtual-machines-create-windows-powershell-service-manager.md).


Ce document est un guide rapide servant à la prise en main de la création et de la gestion de vos jeux de mise à l’échelle de machine virtuelle Azure pendant l’essai public.

Le guide suppose que vous êtes déjà familiarisé avec l’utilisation de modèles Azure Resource Manager (ARM) pour déployer des ressources Azure telles que les machines virtuelles et des réseaux virtuels. Dans le cas contraire, les 3 premiers liens de la section ressources fournissent une vue d’ensemble du Gestionnaire de ressources.

## Que sont les jeux de mise à l’échelle de machine virtuelle et pourquoi les utiliser ?

Les jeux de mise à l’échelle de machine virtuelle sont des ressources que vous pouvez utiliser pour déployer et gérer un ensemble de machines virtuelles identiques.

Avec toutes les machines virtuelles configurées de la même façon, les jeux de mise à l’échelle de machine virtuelle sont conçus pour prendre en charge la mise à niveau automatique réelle (aucun réapprovisionnement de machine virtuelle n’est requis) et en tant que tel, facilite la création de services à grande échelle ciblant les grands calculs, big data, les prises en charge en conteneurs et toute application qui a besoin de ressources de calcul adaptables dedans et dehors. Les opérations de mise à l’échelle sont équilibrées implicitement entre les domaines d’erreur et les domaines de mise à niveau. Pour accéder à une présentation des jeux de mise à l’échelle de machines virtuelles, reportez-vous aux récentes [Annonces de blog Azure](https://azure.microsoft.com/blog/azure-vm-scale-sets-public-preview).

## Création et utilisation des jeux de mise à l’échelle de machine virtuelle

Les jeux de mise à l’échelle de machine virtuelle peuvent être définis et déployés à l’aide de modèles JSON et d’appels REST comme des machines virtuelles de gestionnaire de ressources Azure. Par conséquent, les méthodes de déploiement Azure Resource Manager standard peuvent être utilisées, et ce document vous guidera parmi des exemples.

Les zones d’intégration VMSS spécifiques telles que la prise en charge des commandes impératives dans les langages de script et de programmation, et une intégration de portail complète sont en cours de développement et seront libérés par phases pendant la visualisation.

## Déploiement et gestion de jeux de mise à l’échelle de machine virtuelle à l’aide du portail Azure

Initialement, vous pouvez créer un jeu de mise à l’échelle de machine virtuelle à partir de rien dans le portail Azure. La prise en charge du portail est en cours d’étude et lors de la première étape de ce travail, vous pouvez afficher uniquement les jeux de mise à l’échelle dans le portail que vous avez créé et devrez utiliser une approche de modèle/script pour les créer. Dans tous les cas « portail » est supposé signifier le nouveau portail Azure : [https://portal.azure.com/](https://portal.azure.com/).

Vous pouvez utiliser la capacité du portail à déployer n’importe quel modèle ou ressource, jeux de mise à l’échelle inclus. Un moyen facile de déployer un jeu de mise à l’échelle consiste à utiliser un lien comme suit :

_https://portal.azure.com/#create/Microsoft.Template/uri/<link to VM Scale Set JSON template>_

Vous trouverez un ensemble de modèles d’exemples de jeux de mise à l’échelle de machine virtuelle dans le référentiel Azure Quickstart teamplates GitHub ici : [https://github.com/Azure/azure-quickstart-templates](https://github.com/Azure/azure-quickstart-templates) -rechercher les modèles avec _vmss_ dans le titre.

Dans les fichiers lisez-moi correspondant à ces modèles, vous pouvez voir un bouton tel que celui-ci associé à la fonction de déploiement de portail.

Pour déployer le jeu de mise à l’échelle, cliquez sur le bouton et renseignez ensuite tous les paramètres requis dans le portail. Remarque : Si vous ne savez pas si une ressource prend en charge les majuscules ou les casses mixtes, il est préférable d’utiliser toujours les valeurs de paramètre en minuscules.

**Surveillance de la ressource de jeu de mise à l’échelle de machine virtuelle dans le portail/l’explorateur de ressources**

Après avoir déployé un jeu de mise à l’échelle, le portail va afficher une vue de base de ce dernier mais au départ, pendant la version préliminaire, il ne vous montrera pas les propriétés détaillées, ou vous permettra d’analyser les machines virtuelles s’exécutant sur le jeu de mise à l’échelle de machine virtuelle.

Il est conseillé d’utiliser l’**Explorateur de ressources Azure** : [https://resources.azure.com](https://resources.azure.com) jusqu’à ce que l’intégration complète du portail soit en place. Les jeux de mise à l’échelle de machine virtuelle sont des ressources sous Microsoft.Compute, alors, à partir de ce site, vous pouvez les voir en étendant les liens suivants :

Abonnements -> votre abonnement -> resourceGroups -> fournisseurs -> Microsoft.Compute -> virtualMachineScaleSets -> votre jeu de mise à l’échelle de machine virtuelle-> etc.

## Déploiement et gestion de jeux de mise à l’échelle de machine virtuelle à l’aide de PowerShell

Vous pouvez déployer des modèles VMSS et les ressources de requête à l’aide de n’importe quelle version d’Azure PowerShell actuelle.

**Remarque importante :** les exemples ci-dessous concernent [Azure PowerShell 1.0](https://azure.microsoft.com/blog/azps-1-0-pre/), qui introduit le préfixe _AzureRm_ dans de nombreuses commandes. Si vous utilisez une version antérieure de PowerShell telle que 0.9.8 ou antérieure, « **Rm** » dans les exemples de commandes. Les exemples supposent également que vous avez déjà établi une connexion à Azure dans votre environnement PowerShell (_Login-AzureRmAccount_).

Exemples :

&#35; **Créer un groupe de ressources**

New-AzureRmResourcegroup -name myrg -location « Asie du sud-est »

&#35; **Créer un jeu de mise à l’échelle de machine virtuelle de 2 machines virtuelles**

New-AzureRmResourceGroupDeployment -name dep1 -vmSSName myvmss -instanceCount 3 -ResourceGroupName myrg -TemplateUri [https://raw.githubusercontent.com/Azure/azure-quickstart-templates/master/201-vmss-linux-nat/azuredeploy.json](https://raw.githubusercontent.com/Azure/azure-quickstart-templates/master/201-vmss-linux-nat/azuredeploy.json)

Vous serez invité à saisir les paramètres manquants (tels que l’emplacement, le nom d’utilisateur et le mot de passe dans cet exemple).

&#35; **Obtenir les détails d’ensemble de mise à l’échelle de machine virtuelle**

Get-AzureRmResource -name myvmss -ResourceGroupName myrg -ResourceType Microsoft.Compute/virtualMachineScaleSets -ApiVersion 2015-06-15

&#35; ou pour plus de détails, placez-le dans les barres | ConvertTo-Json -Profondeur 10

&#35; ou, si vous voulez d’autres détails, ajoutez –debug à votre commande.

&#35; Mise à l’échelle vers le haut ou le bas

New-AzureRmResourceGroupDeployment -name dep2 -vmSSName myvmss -instanceCount 2 -ResourceGroupName myrg –TemplateUri [https://raw.githubusercontent.com/Azure/azure-quickstart-templates/master/201-vmss-scale-in-or-out/azuredeploy.json](https://raw.githubusercontent.com/Azure/azure-quickstart-templates/master/201-vmss-scale-in-or-out/azuredeploy.json)

&#35; **Supprimer un jeu de mise à l’échelle**

&#35; Facile : Supprimer le groupe de ressources complètes et tout ce qu’il contient :

Remove-AzureRmResourceGroup-myrg nom

&#35; Précis : Supprimer une ressource spécifique :

Remove-AzureRmResource -Name myvmss -ResourceGroupName myrg -ApiVersion 2015-06-15 -ResourceType Microsoft.Compute/virtualMachineScaleSets

### Commandes PowerShell impératives pour les jeux de mise à l’échelle

Une version à venir d’Azure PowerShell inclut un ensemble de commandes impératives permettant de créer et gérer des jeux de mise n’utilisant pas les modèles. Cette version est disponible en version préliminaire ici : [https://github.com/AzureRT/azure-powershell/releases](https://github.com/AzureRT/azure-powershell/releases). Exemples montrant comment utiliser l’API impérative :

[https://github.com/huangpf/Azure-PowerShell/BLOB/vmss/src/ResourceManager/COMPUTE/Commands.Compute.test/ScenarioTests/VirtualMachineScaleSetTests.ps1](https://github.com/huangpf/azure-powershell/blob/vmss/src/ResourceManager/Compute/Commands.Compute.Test/ScenarioTests/VirtualMachineScaleSetTests.ps1)

## Déploiement et gestion des jeux de mise à l’échelle de machine virtuelle à l’aide de l’Interface de ligne de commande Azure.

Vous pouvez déployer des modèles VMSS et les ressources de requête à l’aide de n’importe quelle version d’interface de ligne de commande Azure.

Le moyen le plus simple consiste à installer l’interface de ligne de commande Azure à partir d’un conteneur Docker. Pour l’installation, voir  [https://azure.microsoft.com/blog/run-azure-cli-as-a-docker-container-avoid-installation-and-setup/](https://azure.microsoft.com/blog/run-azure-cli-as-a-docker-container-avoid-installation-and-setup/)

Pour l’utilisation de l’Interface de ligne de commande, voir : [https://azure.microsoft.com/documentation/articles/xplat-cli/](https://azure.microsoft.com/documentation/articles/xplat-cli/)

### Exemples CLI de définition de mise à l’échelle de machine virtuelle

&#35; **créer un groupe de ressources**

créer un groupe azure myrg « Asie du sud-est »

&#35; **Créer un jeu de mise à l’échelle de machine virtuelle**

créer un déploiement du groupe azure -g myrg - n dep2--modèle uri [https://raw.githubusercontent.com/Azure/azure-quickstart-templates/master/201-vmss-linux-nat/azuredeploy.json](https://raw.githubusercontent.com/Azure/azure-quickstart-templates/master/201-vmss-linux-nat/azuredeploy.json)

&#35; il doit demander des paramètres de façon dynamique, ou vous pouvez les spécifier en tant qu’arguments

&#35; **obtenir les détails de jeu de mise à l’échelle**

la ressource azure affiche -n vmssname -r Microsoft.Compute/virtualMachineScaleSets -o 2015-06-15 -g myrg

&#35; ou pour plus de détails :

la ressource azure affiche –n vmssname –r Microsoft.Compute/virtualMachineScaleSets –o 2015-06-15 –g myrg –json –vv

Une version à venir de l’interface de ligne de commande Azure inclut des commandes impératives pour le déploiement et la gestion des jeux de mise à l’échelle de virtuelle directement sans modèle et effectuer des opérations sur des machines virtuelles dans les jeux de mise à l’échelle de machine virtuelle. Vous pouvez assurer le suivi de cette version ici : [https://github.com/AzureRT/azure-xplat-cli/releases/](https://github.com/AzureRT/azure-xplat-cli/releases/)

## Modèles de jeu de mise à l’échelle de machine virtuelle

Cette section vous guide à travers un modèle Azure simple pour créer un jeu de mise à l’échelle de machine virtuelle, et diffère par les modèles utilisés pour créer des machines virtuelles à instance unique. Il existe également un modèle de dissection de jeu de mise à l’échelle de machine virtuelle pratique : [https://channel9.msdn.com/Blogs/Windows-Azure/VM-Scale-Set-Template-Dissection/player](https://channel9.msdn.com/Blogs/Windows-Azure/VM-Scale-Set-Template-Dissection/player)

### Anatomie d’un modèle

Nous allons commencer avec un modèle simple qui crée un jeu de mise à l’échelle de machines virtuelles Ubuntu et le divise en plusieurs composants. Cet exemple crée également les ressources dont dépend un jeu de mise à l’échelle de machine virtuelle comme un réseau virtuel, un compte de stockage etc.. L’exemple est disponible ici : [https://raw.githubusercontent.com/gbowerman/azure-myriad/master/vmss-ubuntu-vnet-storage.json](https://raw.githubusercontent.com/gbowerman/azure-myriad/master/vmss-ubuntu-vnet-storage.json) -Notez que dans cet exemple « hello world », il n’y aura pas de moyen direct de se connecter aux machines virtuelles dans la mise à l’échelle de machines virtuelles depuis l’extérieur de votre réseau virtuel, car les machines virtuelles se voient affecter des adresses IP internes. Consultez la section scénarios et exemples de [modèles Azure Quickstart](https://github.com/Azure/azure-quickstart-templates) pour trouver des moyens de vous connecter depuis l’extérieur à l’aide d’équilibreurs de zones de changement rapide.

### En-tête de modèle.

Spécifiez le schéma et la version de contenu :

{

   "$schema": "http://schema.management.azure.com/schemas/2015-01-01-preview/deploymentTemplate.json",

   "contentVersion": "1.0.0.0",

### Paramètres

Comme avec n’importe quel modèle d’Azure Resource Manager (ARM), cette section définit les paramètres spécifiés au moment du déploiement, y compris, dans cet exemple, le nom de la machine virtuelle échelle définie, le nombre d’instances de machine virtuelle pour commencer, une chaîne unique à utiliser lors de la création de comptes de stockage (toujours utiliser minuscules lorsque vous entrez des valeurs pour les objets tels que les comptes de stockage sauf si vous savez comment les cas est traité) :

````
 "parameters": {

    "vmSSName": {

      "type": "string",

      "metadata": {

        "description": "Scale Set name, also used in this template as a base for naming resources (hence limited less than 10 characters)."

      },

      "maxLength": 10

    },

    "instanceCount": {

      "type": "int",

      "metadata": {

        "description": "Number of VM instances"

      },

      "maxValue": 100

    },

    "adminUsername": {

      "type": "string",

      "metadata": {

        "description": "Admin username on all VMs."

      }

    },

    "adminPassword": {

      "type": "securestring",

      "metadata": {

        "description": "Admin password on all VMs."

      }

    }

  },
````

### Variables

Composant de modèle ARM standard qui définit des variables que vous référencerez plus loin dans le modèle. Dans cet exemple, nous allons utiliser des variables pour définir le système d’exploitation que nous voulons, certains détails de configuration réseau, les paramètres et l’emplacement de stockage. Pour l’emplacement, nous allons utiliser la fonction _location()_ pour le sélectionner dans le groupe de ressources dans lequel il est déployé.

Notez qu’un tableau de chaînes uniques est défini pour un préfixe de compte de stockage. Consultez la section de stockage pour obtenir une explication.

````
  "variables": {

    "apiVersion": "2015-06-15",

    "newStorageAccountSuffix": "[concat(parameters('vmssName'), 'sa')]",

    "uniqueStringArray": [

      "[concat(uniqueString(concat(resourceGroup().id, deployment().name, variables('newStorageAccountSuffix'), '0')))]",

      "[concat(uniqueString(concat(resourceGroup().id, deployment().name, variables('newStorageAccountSuffix'), '1')))]",

      "[concat(uniqueString(concat(resourceGroup().id, deployment().name, variables('newStorageAccountSuffix'), '2')))]",

      "[concat(uniqueString(concat(resourceGroup().id, deployment().name, variables('newStorageAccountSuffix'), '3')))]",

      "[concat(uniqueString(concat(resourceGroup().id, deployment().name, variables('newStorageAccountSuffix'), '4')))]"

    ],

    "vmSize": "Standard\_A1",

    "vhdContainerName": "[concat(parameters('vmssName'), 'vhd')]",

    "osDiskName": "[concat(parameters('vmssName'), 'osdisk')]",

    "virtualNetworkName": "[concat(parameters('vmssName'), 'vnet')]",

    "subnetName": "[concat(parameters('vmssName'), 'subnet')]",

    "nicName": "[concat(parameters('vmssName'), 'nic')]",

    "ipConfigName": "[concat(parameters('vmssName'), 'ipconfig')]",

    "addressPrefix": "10.0.0.0/16",

    "subnetPrefix": "10.0.0.0/24",

    "storageAccountType": "Standard\_LRS",

    "location": "[resourceGroup().location]",

    "osType": {

      "publisher": "Canonical",

      "offer": "UbuntuServer",

      "sku": "15.10",

      "version": "latest"

    },

    "imageReference": "[variables('osType')]"

  },
````

### Ressources

Dans cette section, chaque type de ressource est défini.

````
   "resources": [
````


**Stockage.** Lorsque vous créez un jeu de mise à l’échelle de machine virtuelle, vous pouvez spécifier un tableau de comptes de stockage. Les disques du système d’exploitation d’instances de machine virtuelle sont ensuite distribués uniformément d’un compte à l’autre. À l’avenir, les jeux de mise à l’échelle de machine virtuelle passeront par l’utilisation d’une approche par disque géré ne nécessitant pas de votre part la gestion de comptes de stockage. À la place, vous devez simplement décrire les propriétés de stockage dans le cadre de la définition des jeux de mise à l’échelle. À ce stade, vous devez créer le nombre de comptes de stockage dont vous avez besoin à l’avance. Nous vous recommandons de créer 5 comptes de stockage qui prendront en charge facilement jusqu’à 100 machines virtuelles dans un jeu de mise à l’échelle (le maximum actuel).

Lors de la création d’un ensemble de comptes de stockage, ils sont distribués sur plusieurs partitions dans une mémoire tampon de stockage et le schéma de distribution varie selon les premières lettres du nom du compte de stockage. Pour cette raison, pour obtenir des performances optimales, il est conseillé de faire en sorte que chaque compte de stockage créé commence par une lettre de l’alphabet différente. Vous pouvez les nommer manuellement, ou dans cet exemple, utiliser la fonction uniqueString() pour appliquer une distribution pseudo-aléatoire :

````
    {

      "type": "Microsoft.Storage/storageAccounts",

      "name": "[concat(variables('uniqueStringArray')[copyIndex()], variables('newStorageAccountSuffix'))]",

      "location": "[variables('location')]",

      "apiVersion": "[variables('apiVersion')]",

      "copy": {

        "name": "storageLoop",

        "count": 5

      },

      "properties": {

        "accountType": "[variables('storageAccountType')]"

      }

    },
````

**Réseau virtuel.** Créer un réseau virtuel. Notez que vous pouvez avoir différents jeux de mise à l’échelle, ainsi que des machines virtuelles uniques dans le même réseau virtuel.

````
    {

      "type": "Microsoft.Network/virtualNetworks",

      "name": "[variables('virtualNetworkName')]",

      "location": "[variables('location')]",

      "apiVersion": "[variables('apiVersion')]",

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
````

### La ressource virtualMachineScaleSets

À bien des égards, la ressource _virtualMachineScaleSets_ est similaire à une ressource _virtualMachines_. Toutes les deux sont fournies par le fournisseur de ressources Microsoft.Compute et sont au même niveau. La différence principale est que vous fournissez une valeur de _capacité_ indiquant combien de machines virtuelles vous créez, et ce que vous définissez ici s’applique à toutes les machines virtuelles du jeu de mise à l’échelle de machine virtuelle.

Notez comment la section _dependsOn_ référence les comptes de stockage et VNET créés ci-dessus, et la capacité fait référence le paramètre _instanceCount_.

````
    {

      "type": "Microsoft.Compute/virtualMachineScaleSets",

      "name": "[parameters('vmssName')]",

      "location": "[variables('location')]",

      "apiVersion": "[variables('apiVersion')]",

      "dependsOn": [

        "[concat('Microsoft.Storage/storageAccounts/', variables('uniqueStringArray')[0], variables('newStorageAccountSuffix'))]",

        "[concat('Microsoft.Storage/storageAccounts/', variables('uniqueStringArray')[1], variables('newStorageAccountSuffix'))]",

        "[concat('Microsoft.Storage/storageAccounts/', variables('uniqueStringArray')[2], variables('newStorageAccountSuffix'))]",

        "[concat('Microsoft.Storage/storageAccounts/', variables('uniqueStringArray')[3], variables('newStorageAccountSuffix'))]",

        "[concat('Microsoft.Storage/storageAccounts/', variables('uniqueStringArray')[4], variables('newStorageAccountSuffix'))]",

        "[concat('Microsoft.Network/virtualNetworks/', variables('virtualNetworkName'))]"

      ],

      "sku": {

        "name": "[variables('vmSize')]",

        "tier": "Standard",

        "capacity": "[parameters('instanceCount')]"

      },
````

**Propriétés**

Les jeux de mise à l’échelle de machine virtuelle ont un paramètre upgradePolicy. Les versions ultérieures vont prendre en charge les mises à jour partielles découpées (par exemple, modifiez la configuration de 20 % des machines virtuelles à la fois), mais pour le moment, l’élément upgradePolicy peut être défini sur manuel ou automatique. En mode manuel, si vous modifiez le modèle et le redéployez, les modifications ne prennent effet que lorsque les nouvelles machines virtuelles sont créées ou des extensions mises à jour, par exemple, si vous augmentez _capacité_. Automatique signifie la mise à jour de toutes les machines virtuelles « instantanément », et le redémarrage de tous les éléments. L’approche manuelle est en général la plus sûre. Vous pouvez supprimer des machines virtuelles individuelles et effectuer le redéploiement en fonction des besoins pour effectuer la mise jour progressivement.

````
      "properties": {

         "upgradePolicy": {

         "mode": "Manual"

        },
````

**Propriétés -> virtualMachineProfile**

Ici, vous pouvez référencer les comptes de stockage créés ci-dessus en tant que conteneurs de machines virtuelles. Vous n’avez pas besoin de créer au préalable les conteneurs réels, mais uniquement les comptes.

````
        "virtualMachineProfile": {

          "storageProfile": {

            "osDisk": {

              "vhdContainers": [

                "[concat('https://', variables('uniqueStringArray')[0], variables('newStorageAccountSuffix'), '.blob.core.windows.net/', variables('vhdContainerName'))]",

                "[concat('https://', variables('uniqueStringArray')[1], variables('newStorageAccountSuffix'), '.blob.core.windows.net/', variables('vhdContainerName'))]",

                "[concat('https://', variables('uniqueStringArray')[2], variables('newStorageAccountSuffix'), '.blob.core.windows.net/', variables('vhdContainerName'))]",

                "[concat('https://', variables('uniqueStringArray')[3], variables('newStorageAccountSuffix'), '.blob.core.windows.net/', variables('vhdContainerName'))]",

                "[concat('https://', variables('uniqueStringArray')[4], variables('newStorageAccountSuffix'), '.blob.core.windows.net/', variables('vhdContainerName'))]"

              ],

              "name": "[variables('osDiskName')]",

              "caching": "ReadOnly",

              "createOption": "FromImage"

            },

            "imageReference": "[variables('imageReference')]"

          },
````

**Propriétés -> osProfile**

Une des différences pour les jeux de mise à l’échelle définis sur des machines virtuelles individuelles est que le nom d’ordinateur est un préfixe. Les instances de machine virtuelle dans le jeu de mise à l’échelle de machine virtuelle sont créées avec des noms tels que : myvm\_0, myvm\_1 etc.

````
          "osProfile": {

            "computerNamePrefix": "[parameters('vmSSName')]",

            "adminUsername": "[parameters('adminUsername')]",

            "adminPassword": "[parameters('adminPassword')]"

          },
````

**Propriétés -> networkProfile**

Au moment de définir le profil de réseau pour un VMSS, n’oubliez pas que la configuration de la carte réseau et la configuration IP ont un nom. La configuration de la carte réseau comporte un paramètre « _principal_ » et les références d’id de sous-réseau qui reviennent à la ressource de sous-réseau créée dans le cadre du réseau virtuel ci-dessus.

````
          "networkProfile": {

            "networkInterfaceConfigurations": [

              {

                "name": "[variables('nicName')]",

                "properties": {

                  "primary": "true",

                  "ipConfigurations": [

                    {

                      "name": "[variables('ipConfigName')]",

                      "properties": {

                        "subnet": {

                          "id": "[concat('/subscriptions/', subscription().subscriptionId,'/resourceGroups/', resourceGroup().name, '/providers/Microsoft.Network/virtualNetworks/', variables('virtualNetworkName'), '/subnets/', variables('subnetName'))]"

                        }

                      }

                    }

                  ]

                }

              }

            ]

          }
````

**Propriétés -> extensionProfile**

L’exemple simple ci-dessus n’a pas besoin de profil d’extension. Vous pouvez le voir en action dans ce modèle, qui déploie Apache et PHP en utilisant l’extension CustomScript : [https://github.com/Azure/azure-quickstart-templates/tree/master/201-vmss-lapstack-autoscale](https://github.com/Azure/azure-quickstart-templates/tree/master/201-vmss-lapstack-autoscale) - notez que dans cet exemple, les propriétés IP de réseau référencent également un équilibreur de charge. Les équilibreurs de charge se trouveront plus précisément dans la section Scénarios de jeux de mise à l’échelle de la machine virtuelle.

## Scénarios de jeu de mise à l’échelle de machine virtuelle

Cette section utilise certains scénarios de jeu de mise à l’échelle de machine virtuelle classique et des modèles d’exemple. Bien que dans l’immédiat, ils aient besoin de modèles, certains d’entre eux seront intégrés au portail dans le futur. Certains services Azure de niveau supérieur (comme les lots, fabrique de service, Service de conteneur Azure) utilisent également ces scénarios

## RDP/SSH vers des instances de jeu de mise à l’échelle de machine virtuelle

Un jeu de mise à l’échelle est créé à l’intérieur d’un réseau virtuel, et les machines individuelles ne se voient pas affecter des adresses IP publiques, ce qui est une bonne chose, car vous ne souhaitez généralement pas être soumis à des frais supplémentaires liés à l’affectation d’adresse IP séparées à toutes les ressources sans états dans la grille de calcul, et vous pouvez facilement vous connecter à ces ordinateurs virtuels à partir d’autres ressources de votre réseau virtuel, notamment celles qui possèdent des adresses IP publiques telles que les équilibreurs de charge ou les machines virtuelles autonomes.

Deux moyens simples de se connecter aux machines virtuelles VMSS sont décrits ici :

### Connectez-vous à des machines virtuelles à l’aide de règles NAT

Vous pouvez créer une adresse IP publique, l’affecter à un équilibreur de charge et définir les règles NAT entrantes qui mappent un port de l’adresse IP sur un port de machine virtuelle dans le jeu de mise à l’échelle de machine virtuelle. Par exemple,

Adresse IP publique -> Port 50000 -> vmss\_0 -> Adresse IP publique Port 22 -> Port 50001 -> vmss\_1 -> Adresse IP publique Port 22 ->Port 50002-> vmss\_2->Port 22

Voici un exemple de création d’un jeu de mise à l’échelle de machine virtuelle qui utilise les règles NAT pour activer la connexion SSH à chaque machine virtuelle dans une échelle à l’aide d’une adresse IP publique unique : [https://github.com/Azure/azure-quickstart-templates/tree/master/201-vmss-linux-nat](https://github.com/Azure/azure-quickstart-templates/tree/master/201-vmss-linux-nat)

Voici un exemple faisant la même chose avec RDP et Windows : [https://github.com/Azure/azure-quickstart-templates/tree/master/201-vmss-windows-nat](https://github.com/Azure/azure-quickstart-templates/tree/master/201-vmss-windows-nat)

### Connectez-vous à des machines virtuelles à l’aide d’une « jump box »

Si vous créez un jeu de mise à l’échelle de machine virtuelle et un ordinateur virtuel dans le même réseau virtuel, la machine virtuelle autonome et les machines virtuelles VMSS peuvent se connecter entre elles en utilisant leurs adresses IP internes comme défini par le réseau virtuel/sous-réseau.

Si vous créez une adresse IP publique et l’affectez à la machine virtuelle autonome, vous pouvez lancer un RDP ou un SSH sur la machine virtuelle autonome, puis vous connecter depuis cette machine à vos instances de VMSS. À ce stade, vous pouvez remarquer qu’un simple jeu de mise à l’échelle de machine virtuelle est intrinsèquement plus sûr qu’une machine virtuelle autonome simple avec une adresse IP publique dans sa configuration par défaut.

Pour obtenir un exemple de cette approche, ce modèle crée un cluster Mesos simple composé d’une machine virtuelle Master qui gère un cluster de machines virtuelles de jeu de mise à l’échelle de machine virtuelle : [https://github.com/gbowerman/azure-myriad/blob/master/mesos-vmss-simple-cluster.json](https://github.com/gbowerman/azure-myriad/blob/master/mesos-vmss-simple-cluster.json)

## Équilibre de charge de répétition alternée vers des instances de jeu de mise à l’échelle de machine virtuelle

Si vous souhaitez remettre un travail à un cluster de calcul des machines virtuelles à l’aide d’une approche de type « tourniquet », vous pouvez configurer un équilibreur de charge Azure avec règle d’équilibrage de charge en conséquence. Vous pouvez également définir des sondes pour vérifier que votre application s’exécute par le lancement d’une commande ping sur les ports avec un protocole spécifié, un intervalle et des chemins de requêtes.

Voici un exemple qui crée le jeu de mise à l’échelle de machines virtuelles exécutant le serveur web IIS, et utilise un équilibreur de charge pour équilibrer la charge que chaque machine virtuelle reçoit. Il utilise également le protocole HTTP pour lancer une commande ping sur une URL spécifique sur chaque machine virtuelle : [https://github.com/gbowerman/azure-myriad/blob/master/vmss-win-iis-vnet-storage-lb.json](https://github.com/gbowerman/azure-myriad/blob/master/vmss-win-iis-vnet-storage-lb.json) -examiner le type de ressource Microsoft.Network/loadBalancers et networkProfile et extensionProfile dans virtualMachineScaleSet.

## Instances de jeu de mise à l’échelle de machine virtuelle avec mise à l’échelle automatique d’Azure

Si vous souhaitez faire varier le nombre d’instances (_capacité_) du jeu de mise à l’échelle de votre machine virtuelle défini en fonction de l’utilisation d’UC/Mémoire/Disque ou d’autres événements, un ensemble de paramètres de mise à l’échelle sont disponibles à partir du fournisseur de ressources Microsoft.Insights. Vous pouvez consulter les paramètres disponibles dans la documentation Insights reste : [https://msdn.microsoft.com/library/azure/dn931953.aspx](https://msdn.microsoft.com/library/azure/dn931953.aspx).

La mise à l’échelle Insights automatique s’intègre directement aux jeux de mise à l’échelle de machine virtuelle. Pour le configurer, vous devez définir un type de ressource Microsoft.Insights/autoscaleSettings dont _targetResourceUri_ et _metricResourceUri_ font référence au jeu de mise à l’échelle. Lorsque vous définissez le jeu de mise à l’échelle, vous incluez un _extensionProfile_ qui installe l’agent de Diagnostics Windows Azure (WAD) sur Windows ou l’Extension de Diagnostic de Linux (LDE) sous Linux. Voici un exemple Linux qui ajoute des machines virtuelles jusqu’à une limite prédéfinie lorsque l’utilisation d’UC moyenne est >50 % avec une granularité d’une minute sur une période d’échantillonnage de 5 minutes :

[https://github.com/Azure/azure-quickstart-templates/tree/master/201-vmss-lapstack-autoscale](https://github.com/Azure/azure-quickstart-templates/tree/master/201-vmss-lapstack-autoscale).

Les futures mises à l’échelle automatiques avec jeux de mises à l’échelle de machine virtuelle seront intégrées directement avec le portail Azure.

## Déploiement d’un jeu de mise à l’échelle de machine virtuelle défini en tant que cluster de calcul dans le gestionnaire de cluster PaaS

Les jeux de mise à l’échelle de machine virtuelle sont décrits comme un rôle de travail de prochaine génération. Il s’agit d’une description valide, mais elle peut être confondue avec les fonctionnalités du rôle de travail PaaS v1.

Dans un sens, les jeux de mise à l’échelle de machine virtuelle fournissent un véritable « rôle » ou une ressource de travail, car ils fournissent une ressource de calcul générale indépendante de la plateforme/runtime, personnalisable et s’intègrent au Gestionnaire de ressources IaaS Azure.

Un rôle de travail v1 PaaS, limité en termes de prise en charge de plate-forme/runtime (images de plateforme Windows) inclut également les services d’échange d’adresse IP virtuelle, de paramètres de mise à niveau configurables, des paramètres spécifiques de runtime/déploiement d’application spécifiques qui ne sont pas _encore_ disponibles dans les jeux de mise à l’échelle de machine virtuelle ou sera fournie par des services PaaS de plus haut niveau tels que Service Fabric. Avec cela à l’esprit, vous pouvez considérer les jeux de mise à l’échelle de machine virtuelle comme une infrastructure qui prend en charge de PaaS. par exemple, des solutions PaaS telles que Service Fabric ou gestionnaires de cluster comme Mesos peuvent se construire sur des jeux de mise à l’échelle de machine virtuelle en tant que couche de calcul évolutive.

Voici un exemple de cluster Mesos qui déploie un jeu de mise à l’échelle de machine virtuelle définie dans le même réseau virtuel qu’une machine virtuelle autonome. La machine virtuelle autonome est un maître Mesos et l’ensemble de l’échelle de machine virtuelle représente un ensemble de nœuds esclaves : [https://github.com/gbowerman/azure-myriad/blob/master/mesos-vmss-simple-cluster.json](https://github.com/gbowerman/azure-myriad/blob/master/mesos-vmss-simple-cluster.json). Les versions futures de [Service de conteneur Azure](https://azure.microsoft.com/blog/azure-container-service-now-and-the-future/) déploieront des versions plus complexes/renforcées de ce scénario basées sur des jeux de mise à l’échelle de machine virtuelle.

## Mise à l’échelle d’un jeu de mise à l’échelle de machine virtuelle

Pour augmenter ou diminuer le nombre de machines virtuelles dans un jeu de mise à l’échelle de machine virtuelle, il suffit de changer la propriété _capacité_ et redéployez le modèle. Cette simplicité rend plus facile l’écriture de votre couche de mise à l’échelle personnalisée si vous souhaitez définir des événements d’échelle personnalisée qui ne sont pas pris en charge par l’échelle automatique Azure.

Si vous redéployez un modèle pour modifier la capacité, vous pouvez définir un modèle beaucoup plus petit incluant uniquement la référence SKU et la capacité mise à jour. Un exemple de cela est indiqué ici : [https://raw.githubusercontent.com/Azure/azure-quickstart-templates/master/201-vmss-scale-in-or-out/azuredeploy.json](https://raw.githubusercontent.com/Azure/azure-quickstart-templates/master/201-vmss-linux-nat/azuredeploy.json).

## La performance de mise à l’échelle de machine virtuelle et guide de mise à l’échelle

- Au cours de l’évaluation publique, ne créez pas plus de 500 machines virtuelles dans plusieurs jeux de mise à l’échelle de machine virtuelle à la fois. Cette limite peut être repoussée par la suite.
- Ne prévoyez pas plus de 40 machines virtuelles par compte de stockage.
- Fractionnez le plus possible les premières lettres des noms de compte de stockage. Les modèles VMSS exemple [modèles Azure Quickstart](https://github.com/Azure/azure-quickstart-templates/) fournissent des exemples montrant comment effectuer cette opération.
- Si vous utilisez des machines virtuelles personnalisées, ne prévoyez pas plus de 40 machines virtuelles par jeu de mise à l’échelle de machine virtuelle, dans un seul compte de stockage. Vous aurez besoin de l’image pré copiée dans le compte de stockage avant de pouvoir commencer le déploiement du jeu de mise à l’échelle de machine virtuelle. Visitez le FAQ pour plus d’informations.
- Ne prévoyez pas plus de 2 048 machines virtuelles par machine virtuelle. Cette limite peut être repoussée par la suite.
- Le nombre de machines virtuelles que vous pouvez créer est limité par votre quota de cœurs de calcul dans n’importe quelle région. Vous devrez peut-être contacter le support technique pour augmenter votre limite de quota de calcul augmentée, même si vous avez une limite de cœurs haute à utiliser avec vos services cloud ou IaaS v1 dès aujourd’hui. Pour interroger votre quota, vous pouvez exécuter la commande CLI Azure suivante : _azure vm list-usage_, et la commande PowerShell suivante : _Get-AzureRmVMUsage_ (si vous utilisez une version de PowerShell 1.0, utilisez _Get-AzureVMUsage_).


## FAQ sur le jeu de mise à l’échelle de machine virtuelle

**Q. Le nombre de machines virtuelles peut avoir dans un jeu de mise à l’échelle de machine virtuelle ?**

A. 100 si vous utilisez des images de plateforme qui peuvent être distribuées sur plusieurs comptes de stockage. Si vous utilisez des images personnalisées, jusqu’à 40, étant donné que les images personnalisées sont limitées à un seul compte de stockage lors de la visualisation.

**Quelles autres limites de ressource existent pour les jeux de mise à l’échelle de machine virtuelle ?**

A. Les limites de service Azure existantes s’appliquent : [https://azure.microsoft.com/documentation/articles/azure-subscription-service-limits/](https://azure.microsoft.com/documentation/articles/azure-subscription-service-limits/)

Vous pouvez également vous limiter à la création de 500 machines virtuelles dans plusieurs jeux de mise à l’échelle par région pendant la période d’évaluation.

**Q. Les disques de données pris en charge dans les jeux de mise à l’échelle de machine virtuelle ?**

A. Pas dans la version initiale. Vos options de stockage des données sont :

- Fichiers Azure (lecteurs SMB partagés)

- Système d’exploitation de lecteur

- Lecteur temp (local, non sauvegardé par le stockage Azure)

- Service de données externes (base de données distante, tables Azure, objets blobs Azure)

**Q. Quelles sont les régions Azure qui prennent en charge VMSS ?**

A. N’importe quelle région qui prend en charge Azure Resource Manager prend en charge les jeux de mise à l’échelle de machine virtuelle.

**Q. Comment créer un VMSS à l’aide d’une image personnalisée ?**

A. Laissez la propriété vhdContainers vide, par exemple :

````
"storageProfile": {
   "osDisk": {
      "name": "vmssosdisk",
      "caching": "ReadOnly",
      "createOption": "FromImage",
      "image": {
         "uri": [https://mycustomimage.blob.core.windows.net/system/Microsoft.Compute/Images/mytemplates/template-osDisk.vhd](https://mycustomimage.blob.core.windows.net/system/Microsoft.Compute/Images/mytemplates/template-osDisk.vhd)
     },
     "osType": "Windows"
  }
},
````

**Q. Si je réduis ma capacité VMSS de 20 à 15, quelles sont les machines virtuelles qui seront supprimées ?**

A. Les 5 dernières machines virtuelles (index plus grands) seront supprimées.

**Q. Que diriez-vous si j’augmente par la suite la capacité de 15 à 18 ?**

Si vous augmentez jusqu’à 18, des machines virtuelles avec index 15, 16, 17 seront créées. Dans les deux cas, les machines virtuelles sont réparties entre FD et UD.

**Q. Lorsque vous utilisez plusieurs extensions dans un jeu de mise à l’échelle de machine virtuelle, puis-je appliquer une séquence d’exécution ?**

A. Indirectement, pour l’extension customScript, votre script peut attendre qu’une autre extension soit terminée (par exemple, en surveillant le journal d’extension- voir [https://github.com/Azure/azure-quickstart-templates/blob/master/201-vmss-lapstack-autoscale/install\_lap.sh](https://github.com/Azure/azure-quickstart-templates/blob/master/201-vmss-lapstack-autoscale/install_lap.sh)).

**Q. Les jeux de mise à l’échelle de machine virtuelle fonctionnent-ils avec des ensembles haute disponibilité Azure ?**

A. Oui. Un jeu de mise à l’échelle est un ensemble de disponibilité comprenant 3 FD et 5 UD. Vous n’avez pas besoin de configurer quoi que ce soit sous virtualMachineProfile. Dans les futures versions, les jeux de mise à l’échelle peuvent s’étendre sur plusieurs clients, mais dans l’immédiat, un jeu de mise à l’échelle est un ensemble de disponibilité unique.

## Étapes suivantes

Pendant la version préliminaire des jeux de mise à l’échelle de machine virtuelle, la documentation va évoluer et plusieurs fonctionnalités d’intégration telles que le portail et la mise à l’échelle automatique seront ouvertes.

Vos commentaires sont très importants pour nous. Ils nous aident à créer un service qui fournit les fonctionnalités dont vous avez besoin. Faites-nous savoir ce qui fonctionne et ce qui pourrait être amélioré. Vous pouvez envoyer vos commentaires à [vmssfeedback@microsoft.com](mailto:vmssfeedback@microsoft.com).

## Ressources

| **Rubrique** | **Lien** |
| --- | --- |
| Présentation d’Azure Resource Manager | [https://azure.microsoft.com/documentation/articles/resource-group-Overview/](https://azure.microsoft.com/documentation/articles/resource-group-overview/) |
| Modèles Azure Resource Manager | [https://azure.microsoft.com/documentation/articles/resource-group-authoring-templates/](https://azure.microsoft.com/documentation/articles/resource-group-authoring-templates/) |
| Fonctions des modèles de gestionnaire des ressources Azure | [https://Azure.Microsoft.com/documentation/articles/Resource-Group-Template-Functions/](https://azure.microsoft.com/documentation/articles/resource-group-template-functions/) |
| Exemples de modèles (github) | [https://github.com/azure/azure-quickstart-templates](https://github.com/Azure/azure-quickstart-templates) |
| Guide d’API REST d’ensemble de mise à l’échelle de machine virtuelle | [https://msdn.Microsoft.com/library/mt589023.aspx](https://msdn.microsoft.com/library/mt589023.aspx) |
| Vidéos d’ensemble de mise à l’échelle de machine virtuelle | [https://channel9.msdn.com/Blogs/Regular-IT-Guy/Mark-Russinovich-Talks-Azure-Scale-Sets/](https://channel9.msdn.com/Blogs/Regular-IT-Guy/Mark-Russinovich-Talks-Azure-Scale-Sets/) [https://channel9.msdn.com/Shows/Cloud+Cover/Episode-191-Virtual-Machine-Scale-Sets-with-Guy-Bowerman](https://channel9.msdn.com/Shows/Cloud+Cover/Episode-191-Virtual-Machine-Scale-Sets-with-Guy-Bowerman) [https://channel9.msdn.com/Blogs/Windows-Azure/VM-Scale-Set-Template-Dissection/player](https://channel9.msdn.com/Blogs/Windows-Azure/VM-Scale-Set-Template-Dissection/player) |
| Paramètres de mise à l’échelle automatique (MSDN) | [https://msdn.Microsoft.com/library/Azure/dn931953.aspx](https://msdn.microsoft.com/library/azure/dn931953.aspx) |
| Résolution des problèmes liés aux déploiements de groupes de ressources dans Azure | [https://azure.microsoft.com/documentation/articles/resource-group-deploy-debug/](https://azure.microsoft.com/documentation/articles/resource-group-deploy-debug/) |

<!---HONumber=Nov15_HO3-->