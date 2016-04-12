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
   ms.date="04/04/2016"
   ms.author="v-livech"/>


# Création d’une machine virtuelle Linux sur Azure à l’aide de l’interface de ligne de commande (CLI)

Cet article explique comment créer rapidement une machine virtuelle Linux dans Azure à l’aide de la commande `azure vm quick-create` de l’interface de ligne de commande Azure, qui crée une machine virtuelle avec une infrastructure de base que vous pouvez utiliser pour créer un prototype ou tester un concept très rapidement. L’article nécessite un compte Azure ([obtenir un essai gratuit](https://azure.microsoft.com/pricing/free-trial/)) et [l’interface de ligne de commande Azure](../xplat-cli-install.md) en mode Resource Manager (`azure config mode arm`).

## Résumé des commandes rapides

```
# One command to quickly the VM that prompts for arguments
chrisL@fedora$ azure vm quick-create
```

## Procédure pas à pas détaillée

### Créer la machine virtuelle Linux

Dans la commande suivante, vous pouvez utiliser l’image de votre choix, mais cet exemple utilise `canonical:ubuntuserver:14.04.2-LTS:latest` pour créer rapidement une machine virtuelle. (Pour rechercher une image dans le Marketplace, [recherchez une image](virtual-machines-linux-cli-ps-findimage.md) ou [chargez votre propre image personnalisée](virtual-machines-linux-create-upload-generic.md).) Vous obtenez un résultat semblable à ce qui suit.

Dans les exemples de commandes suivants, remplacez les valeurs situées entre &lt; et &gt; par les valeurs de votre propre environnement.

```bash
# Create the Linux VM using prompts
username@macbook$ azure vm quick-create
info:    Executing command vm quick-create
Resource group name: exampleResourceGroup
Virtual machine name: exampleVMname
Location name: westus
Operating system Type [Windows, Linux]: linux
ImageURN (in the format of "publisherName:offer:skus:version") or a VHD link to the user image: Canonical:UbuntuServer:14.04.4-LTS:latest
User name: ops
Password: *********
Confirm password: *********
+ Looking up the VM "exampleVMname"
info:    Using the VM Size "Standard_D1"
info:    The [OS, Data] Disk or image configuration requires storage account
+ Looking up the storage account cli133501687
info:    Could not find the storage account "cli1301687", trying to create new one
+ Creating storage account "cli133501687" in "westus"
+ Looking up the storage account cli133501687
+ Looking up the NIC "quick-westu-1363648838-nic"
info:    An nic with given name "quick-westu-1363648838-nic" not found, creating a new one
+ Looking up the virtual network "quick-westu-1363648838-vnet"
info:    Preparing to create new virtual network and subnet
\ Creating a new virtual network "quick-westu-1363648838-vnet" [address prefix: "10.0.0.0/16"] with subnet "quick-westu-13636488+8-snet" [address prefix: "10.0.1.0/24"]
+ Looking up the virtual network "quick-westu-1363648838-vnet"
+ Looking up the subnet "quick-westu-1363648838-snet" under the virtual network "quick-westu-1363648838-vnet"
info:    Found public ip parameters, trying to setup PublicIP profile
+ Looking up the public ip "quick-westu-1363648838-pip"
info:    PublicIP with given name "quick-westu-1363648838-pip" not found, creating a new one
+ Creating public ip "quick-westu-1363648838-pip"
+ Looking up the public ip "quick-westu-1363648838-pip"
+ Creating NIC "quick-westu-1363648838-nic"
+ Looking up the NIC "quick-westu-1363648838-nic"
+ Creating VM "quickcreate"
+ Looking up the VM "exampleVMname"
+ Looking up the NIC "quick-westu-1363648838-nic"
+ Looking up the public ip "quick-westu-1363648838-pip"
data:    Id                              :/subscriptions/<guid>/resourceGroups/exampleResourceGroup/providers/Microsoft.Compute/virtualMachines/exampleVMname
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
data:        Publisher                   :canonical
data:        Offer                       :ubuntuserver
data:        Sku                         :14.04.2-LTS
data:        Version                     :latest
data:
data:      OS Disk:
data:        OSType                      :Linux
data:        Name                        :cli350d386daac1f01c-os-1457063387485
data:        Caching                     :ReadWrite
data:        CreateOption                :FromImage
data:        Vhd:
data:          Uri                       :https://cli1361687.blob.core.windows.net/vhds/cli350d386daac1f01c-os-1457063387485.vhd
data:
data:    OS Profile:
data:      Computer Name                 :exampleVMname
data:      User Name                     :ops
data:      Linux Configuration:
data:        Disable Password Auth       :false
data:
data:    Network Profile:
data:      Network Interfaces:
data:        Network Interface #1:
data:          Primary                   :true
data:          MAC Address               :00-0D-3A-32-E9-66
data:          Provisioning State        :Succeeded
data:          Name                      :quick-westu-1363648838-nic
data:          Location                  :westus
data:            Public IP address       :137.135.33.58
data:            FQDN                    :quick-westu-1363648838-pip.westus.cloudapp.azure.com
data:
data:    Diagnostics Profile:
data:      BootDiagnostics Enabled       :true
data:      BootDiagnostics StorageUri    :https://cli13601687.blob.core.windows.net/
data:
data:      Diagnostics Instance View:
info:    vm quick-create command OK
```

Vous pouvez maintenant exécuter SSH dans votre machine virtuelle sur le port SSH 22 par défaut.

La commande `azure vm quick-create` crée rapidement une machine virtuelle à laquelle vous pouvez vous connecter. Toutefois, cette machine virtuelle n’a pas un environnement complexe, donc si vous voulez personnaliser votre environnement, vous pouvez [utiliser un modèle Azure Resource Manager pour créer rapidement un déploiement spécifique](virtual-machines-linux-cli-deploy-templates.md) ou vous pouvez [créer votre propre environnement personnalisé pour une machine virtuelle Linux à l’aide des commandes de l’interface de ligne de commande Azure directement](virtual-machines-linux-cli-deploy-templates.md).

L’exemple ci-dessus crée les éléments suivants :

- un groupe de ressources Azure dans lequel déployer la machine virtuelle
- un compte de stockage Azure pour stocker le fichier .vhd qui est l’image de la machine virtuelle
- un réseau virtuel Azure et un sous-réseau pour fournir une connectivité à la machine virtuelle
- une carte réseau virtuelle pour associer la machine virtuelle au réseau
- une adresse IP publique et un préfixe de sous-domaine pour fournir une adresse Internet pour un usage externe ; enfin, la machine virtuelle Linux au sein de cet environnement.

Cette machine virtuelle est exposée directement à Internet et est uniquement sécurisée par un nom d’utilisateur et un mot de passe.

## Étapes suivantes

Vous venez de créer rapidement une machine virtuelle Linux à des fins de test ou de démonstration. Vous pouvez créer un environnement d’exécution plus sécurisé avec une machine virtuelle Linux dans Azure en procédant comme suit :

- [Créer une machine virtuelle Linux dans Azure à l’aide de modèles Azure](virtual-machines-linux-cli-deploy-templates.md)
- [Créer une machine virtuelle Linux sécurisée par SSH dans Azure à l’aide de modèles Azure](virtual-machines-linux-create-ssh-secured-vm-from-template.md)
- [Créer une machine virtuelle Linux dans Azure à l’aide de l’interface CLI Azure et en personnalisant l’infrastructure](virtual-machines-linux-create-cli-complete.md)

ainsi que n’importe quelle quantité de déploiements d’infrastructure open source et propriétaires, de configuration et d’outils d’orchestration.

<!---HONumber=AcomDC_0406_2016-->