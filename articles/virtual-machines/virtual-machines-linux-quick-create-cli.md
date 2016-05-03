<properties
   pageTitle="Création d’une machine virtuelle Linux sur Azure à l’aide de l’interface de ligne de commande (CLI) | Microsoft Azure"
   description="Créez une machine virtuelle Linux sur Azure à l’aide de l’interface de ligne de commande (CLI)."
   services="virtual-machines-linux"
   documentationCenter=""
   authors="vlivech"
   manager="timlt"
   editor=""/>

<tags
   ms.service="virtual-machines-linux"
   ms.devlang="NA"
   ms.topic="hero-article"
   ms.tgt_pltfrm="vm-linux"
   ms.workload="infrastructure"
   ms.date="04/27/2016"
   ms.author="v-livech"/>


# Création d’une machine virtuelle Linux sur Azure à l’aide de l’interface de ligne de commande (CLI)

Cet article explique comment déployer rapidement une machine virtuelle Linux sur Azure à l’aide de la commande `azure vm quick-create` de l’interface de ligne de commande (CLI) Azure. La commande `quick-create` déploie une machine virtuelle encadrée par une infrastructure de base que vous pouvez utiliser comme prototype ou pour tester très rapidement un concept. Il s'agit de la méthode la plus rapide pour créer un script shell bash Linux. L’article nécessite un compte Azure ([obtenir un essai gratuit](https://azure.microsoft.com/pricing/free-trial/)) et [l’interface de ligne de commande Azure](../xplat-cli-install.md) connectée (`azure login`) en mode Resource Manager (`azure config mode arm`). Vous pouvez également déployer rapidement une machine virtuelle Linux à l’aide du [Portail Azure](virtual-machines-linux-quick-create-portal.md).

## Résumé des commandes rapides

```
# One command to deploy the VM and attach your SSH key
ahmet@fedora$ azure vm quick-create -M ~/.ssh/azure_id_rsa.pub
```

## Déployer la machine virtuelle Linux

À l’aide de la commande déjà utilisée plus haut, nous indiquerons chaque invite, ainsi que la sortie que vous devriez obtenir.

Pour ImageURN, nous utiliserons `canonical:ubuntuserver:14.04.2-LTS:latest` pour déployer une machine virtuelle Ubuntu 14.04. (Pour rechercher une image dans le Marketplace, [recherchez une image](virtual-machines-linux-cli-ps-findimage.md) ou [chargez votre propre image personnalisée](virtual-machines-linux-create-upload-generic.md).)

Dans la procédure suivante, remplacez les invites par les valeurs de votre propre environnement. Nous utilisons des exemples de valeurs. La sortie doit ressembler au bloc de sortie suivant.

```bash
# Follow the prompts and enter your own names
ahmet@fedora$ azure vm quick-create -M ~/.ssh/azure_id_rsa.pub
info:    Executing command vm quick-create
Resource group name: exampleRGname
Virtual machine name: exampleVMname
Location name: westus
Operating system Type [Windows, Linux]: Linux
ImageURN (in the format of "publisherName:offer:skus:version") or a VHD link to the user image: Canonical:UbuntuServer:14.04.4-LTS:latest
User name: ahmet
Password: ************************************************
Confirm password: ************************************************
```

```bash
########### expected output ###########
+ Looking up the VM "exampleVMname"
info:    Verifying the public key SSH file: /home/ahmet/.ssh/azure_id_rsa.pub
info:    Using the VM Size "Standard_D1"
info:    The [OS, Data] Disk or image configuration requires storage account
+ Looking up the storage account cli38948918364134011018
info:    Could not find the storage account "cli38948918364134011018", trying to create new one
+ Creating storage account "cli38948918364134011018" in "westus"
+ Looking up the storage account cli38948918364134011018
+ Looking up the NIC "examp-westu-3894891836-nic"
info:    An nic with given name "examp-westu-3894891836-nic" not found, creating a new one
+ Looking up the virtual network "examp-westu-3894891836-vnet"
info:    Preparing to create new virtual network and subnet
| Creating a new virtual network "examp-westu-3894891836-vnet" [address prefix: "10.0.0.0/16"] with subnet "examp-westu-3894891836-snet" [address prefix: "10.+.1.0/24"]
+ Looking up the virtual network "examp-westu-3894891836-vnet"
+ Looking up the subnet "examp-westu-3894891836-snet" under the virtual network "examp-westu-3894891836-vnet"
info:    Found public ip parameters, trying to setup PublicIP profile
+ Looking up the public ip "examp-westu-3894891836-pip"
info:    PublicIP with given name "examp-westu-3894891836-pip" not found, creating a new one
+ Creating public ip "examp-westu-3894891836-pip"
+ Looking up the public ip "examp-westu-3894891836-pip"
+ Creating NIC "examp-westu-3894891836-nic"
+ Looking up the NIC "examp-westu-3894891836-nic"
+ Creating VM "exampleVMname"
+ Looking up the VM "exampleVMname"
+ Looking up the NIC "examp-westu-3894891836-nic"
+ Looking up the public ip "examp-westu-3894891836-pip"
data:    Id                              :/subscriptions/<**subscriptionsID**>/resourceGroups/exampleRGname/providers/Microsoft.Compute/virtualMachines/exampleVMname
data:    ProvisioningState               :Succeeded
data:    Name                            :exampleVMname
data:    Location                        :westus
data:    Type                            :Microsoft.Compute/virtualMachines
data:
data:    Hardware Profile:
data:      Size                          :Standard_D1
data:
data:    Storage Profile:
data:      Image reference:
data:        Publisher                   :Canonical
data:        Offer                       :UbuntuServer
data:        Sku                         :14.04.4-LTS
data:        Version                     :latest
data:
data:      OS Disk:
data:        OSType                      :Linux
data:        Name                        :clife36db80ae0539d2-os-1460152163612
data:        Caching                     :ReadWrite
data:        CreateOption                :FromImage
data:        Vhd:
data:          Uri                       :https://<**subscriptionsID**>.blob.core.windows.net/vhds/clife36db80ae0539d2-os-1460152163612.vhd
data:
data:    OS Profile:
data:      Computer Name                 :exampleVMname
data:      User Name                     :ahmet
data:      Linux Configuration:
data:        Disable Password Auth       :false
data:
data:    Network Profile:
data:      Network Interfaces:
data:        Network Interface #1:
data:          Primary                   :true
data:          MAC Address               :00-0D-3A-33-4C-B2
data:          Provisioning State        :Succeeded
data:          Name                      :examp-westu-3894891836-nic
data:          Location                  :westus
data:            Public IP address       :13.88.22.244
data:            FQDN                    :examp-westu-3894891836-pip.westus.cloudapp.azure.com
data:
data:    Diagnostics Profile:
data:      BootDiagnostics Enabled       :true
data:      BootDiagnostics StorageUri    :https://<**subscriptionsID**>.blob.core.windows.net/
data:
data:      Diagnostics Instance View:
info:    vm quick-create command OK
```

Vous pouvez désormais intégrer SSH à votre machine virtuelle sur le port SSH 22 par défaut et l’adresse IP publique répertoriée dans la sortie ci-dessus (ou le nom de domaines complet).

```
ahmet@fedora$ ssh -i ~/.ssh/azure_id_rsa ubuntu@13.88.22.244
```

## Étapes suivantes

La procédure `azure vm quick-create` permet de déployer rapidement une machine virtuelle pour se connecter à un shell bash et être opérationnel. L’utilisation de `vm quick-create` ne vous offre pas les avantages supplémentaires d’un environnement complexe. Pour déployer une machine virtuelle Linux personnalisée pour votre infrastructure, vous pouvez consulter l’un des articles ci-dessous.

- [Déploiement et gestion de machines virtuelles à l’aide des modèles Azure Resource Manager et de l’interface de ligne de commande Azure](virtual-machines-linux-cli-deploy-templates.md)
- [Créer votre propre environnement personnalisé pour une machine virtuelle Linux à l’aide des commandes de l’interface de ligne de commande Azure directement](virtual-machines-linux-cli-deploy-templates.md).
- [Création d'une machine virtuelle Linux sur Azure à l’aide de modèles](virtual-machines-linux-cli-deploy-templates.md)
- [Création d'une machine virtuelle Linux sécurisée par SSH dans Azure à l’aide de modèles](virtual-machines-linux-create-ssh-secured-vm-from-template.md)
- [Création d'une machine virtuelle Linux à l’aide de l'interface de ligne de commande Azure (CLI)](virtual-machines-linux-create-cli-complete.md)

Ces articles vous aideront à démarrer la création d'une infrastructure Azure ainsi que n’importe quelle quantité de déploiements d’infrastructure open source et propriétaires, de configuration et d’outils d’orchestration.

<!---HONumber=AcomDC_0427_2016-->