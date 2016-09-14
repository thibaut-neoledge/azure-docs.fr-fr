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
   ms.date="08/18/2016"
   ms.author="v-livech"/>


# Création d’une machine virtuelle Linux sur Azure à l’aide de l’interface de ligne de commande (CLI)

Cet article explique comment déployer rapidement une machine virtuelle Linux sur Azure à l’aide de la commande `azure vm quick-create` de l’interface de ligne de commande (CLI) Azure. La commande `quick-create` déploie une machine virtuelle avec une infrastructure de base que vous pouvez utiliser comme prototype ou pour tester très rapidement un concept. Cet article nécessite un compte Azure ([obtenir un compte d’essai gratuit](https://azure.microsoft.com/pricing/free-trial/)) et l’[interface de ligne de commande Azure](../xplat-cli-install.md) connectée en mode Classic (`azure login`) et en mode Resource Manager (`azure config mode arm`). Vous pouvez également déployer rapidement une machine virtuelle Linux à l’aide du [portail Azure](virtual-machines-linux-quick-create-portal.md).

## Résumé des commandes rapides

Commande unique pour déployer la machine virtuelle CoreOS et joindre votre clé SSH :

```bash
azure vm quick-create -M ~/.ssh/azure_id_rsa.pub -Q CoreOS
```

## Déployer la machine virtuelle Linux

Nous allons maintenant pour détailler la commande et expliquer chaque étape à l’aide de RedHat Enterprise Linux 7.2.

## Utiliser un alias ImageURN

La commande `quick-create` de l’interface de ligne de commande Azure dispose d’alias mappés sur les distributions du système d’exploitation les plus courantes. Le tableau suivant répertorie les alias de distribution (à partir de la version 0.10 de l’interface de ligne de commande Azure) Tous les déploiements utilisant `quick-create` sont définis par défaut sur des machines virtuelles de stockage SSD offrant des performances élevées.

| Alias | Éditeur | Offer | SKU | Version |
|:----------|:----------|:-------------|:------------|:--------|
| CentOS | OpenLogic | Centos | 7,2 | le plus récent |
| CoreOS | CoreOS | CoreOS | Stable | le plus récent |
| Debian | credativ | Debian | 8 | le plus récent |
| openSUSE | SUSE | openSUSE | 13\.2 | le plus récent |
| RHEL | Redhat | RHEL | 7,2 | le plus récent |
| UbuntuLTS | Canonical | UbuntuServer | 14\.04.4-LTS | le plus récent |



Pour l’option **ImageURN** (`-Q`), nous allons utiliser `RHEL` afin de déployer une machine virtuelle RedHat Enterprise Linux 7.2. Ces alias `quick-create` représentent une infime partie du système d’exploitation disponible dans Azure. Trouvez d’autres images dans Marketplace en [faisant une recherche](virtual-machines-linux-cli-ps-findimage.md) ou en [chargeant votre image personnalisée](virtual-machines-linux-create-upload-generic.md).

Dans la procédure suivante, remplacez les invites par les valeurs de votre propre environnement.

Suivez les invites et entrez vos propres noms

```bash
azure vm quick-create -M ~/.ssh/id_rsa.pub -Q RHEL
```

La sortie doit ressembler au bloc de sortie suivant.

```bash
info:    Executing command vm quick-create
Resource group name: rhel-quick
Virtual machine name: rhel
Location name: westus
Operating system Type [Windows, Linux]: linux
User name: ops
+ Listing virtual machine sizes available in the location "westus"
+ Looking up the VM "rhel"
info:    Verifying the public key SSH file: /Users/ops/.ssh/id_rsa.pub
info:    Using the VM Size "Standard_DS1"
info:    The [OS, Data] Disk or image configuration requires storage account
+ Looking up the storage account cli1630678171193501687
info:    Could not find the storage account "cli1630678171193501687", trying to create new one
+ Creating storage account "cli1630678171193501687" in "westus"
+ Looking up the storage account cli1630678171193501687
+ Looking up the NIC "rhel-westu-1630678171-nic"
info:    An nic with given name "rhel-westu-1630678171-nic" not found, creating a new one
+ Looking up the virtual network "rhel-westu-1630678171-vnet"
info:    Preparing to create new virtual network and subnet
+ Creating a new virtual network "rhel-westu-1630678171-vnet" [address prefix: "10.0.0.0/16"] with subnet "rhel-westu-1630678171-snet" [address prefix: "10.0.1.0/24"]
+ Looking up the virtual network "rhel-westu-1630678171-vnet"
+ Looking up the subnet "rhel-westu-1630678171-snet" under the virtual network "rhel-westu-1630678171-vnet"
info:    Found public ip parameters, trying to setup PublicIP profile
+ Looking up the public ip "rhel-westu-1630678171-pip"
info:    PublicIP with given name "rhel-westu-1630678171-pip" not found, creating a new one
+ Creating public ip "rhel-westu-1630678171-pip"
+ Looking up the public ip "rhel-westu-1630678171-pip"
+ Creating NIC "rhel-westu-1630678171-nic"
+ Looking up the NIC "rhel-westu-1630678171-nic"
+ Looking up the storage account clisto909893658rhel
+ Creating VM "rhel"
+ Looking up the VM "rhel"
+ Looking up the NIC "rhel-westu-1630678171-nic"
+ Looking up the public ip "rhel-westu-1630678171-pip"
data:    Id                              :/subscriptions/<guid>/resourceGroups/rhel-quick/providers/Microsoft.Compute/virtualMachines/rhel
data:    ProvisioningState               :Succeeded
data:    Name                            :rhel
data:    Location                        :westus
data:    Type                            :Microsoft.Compute/virtualMachines
data:
data:    Hardware Profile:
data:      Size                          :Standard_DS1
data:
data:    Storage Profile:
data:      Image reference:
data:        Publisher                   :RedHat
data:        Offer                       :RHEL
data:        Sku                         :7.2
data:        Version                     :latest
data:
data:      OS Disk:
data:        OSType                      :Linux
data:        Name                        :clic5abbc145c0242c1-os-1462425492101
data:        Caching                     :ReadWrite
data:        CreateOption                :FromImage
data:        Vhd:
data:          Uri                       :https://cli1630678171193501687.blob.core.windows.net/vhds/clic5abbc145c0242c1-os-1462425492101.vhd
data:
data:    OS Profile:
data:      Computer Name                 :rhel
data:      User Name                     :ops
data:      Linux Configuration:
data:        Disable Password Auth       :true
data:
data:    Network Profile:
data:      Network Interfaces:
data:        Network Interface #1:
data:          Primary                   :true
data:          MAC Address               :00-0D-3A-32-0F-DD
data:          Provisioning State        :Succeeded
data:          Name                      :rhel-westu-1630678171-nic
data:          Location                  :westus
data:            Public IP address       :104.42.236.196
data:            FQDN                    :rhel-westu-1630678171-pip.westus.cloudapp.azure.com
data:
data:    Diagnostics Profile:
data:      BootDiagnostics Enabled       :true
data:      BootDiagnostics StorageUri    :https://clisto909893658rhel.blob.core.windows.net/
data:
data:      Diagnostics Instance View:
info:    vm quick-create command OK
```

SSH dans votre machine virtuelle sur le port 22 et les adresses IP publiques répertoriées dans la sortie. (Vous pouvez également utiliser le nom de domaine complet.)

```bash
ssh ops@rhel-westu-1630678171-pip.westus.cloudapp.azure.com
```

Le processus de connexion doit ressembler à ceci :

```bash
The authenticity of host 'rhel-westu-1630678171-pip.westus.cloudapp.azure.com (104.42.236.196)' can't be established.
RSA key fingerprint is 0e:81:c4:36:2d:eb:3c:5a:dc:7e:65:8a:3f:3e:b0:cb.
Are you sure you want to continue connecting (yes/no)? yes
Warning: Permanently added 'rhel-westu-1630678171-pip.westus.cloudapp.azure.com,104.42.236.196' (RSA) to the list of known hosts.
[ops@rhel ~]$ ls -a
.  ..  .bash_logout  .bash_profile  .bashrc  .cache  .config  .ssh
```

## Étapes suivantes

La procédure `azure vm quick-create` permet de déployer rapidement une machine virtuelle pour se connecter à un shell bash et être opérationnel. L’utilisation de `vm quick-create` ne vous offre pas les avantages supplémentaires d’un environnement complexe. Pour déployer une machine virtuelle Linux personnalisée en fonction de votre infrastructure, lisez l’un des articles ci-dessous.

- [Déploiement et gestion de machines virtuelles à l’aide des modèles Azure Resource Manager et de l’interface de ligne de commande Azure](virtual-machines-linux-cli-deploy-templates.md)
- [Créer votre propre environnement personnalisé pour une machine virtuelle Linux à l’aide des commandes de l’interface de ligne de commande Azure directement](virtual-machines-linux-create-cli-complete.md).
- [Création d’une machine virtuelle Linux sécurisée par SSH dans Azure à l’aide de modèles](virtual-machines-linux-create-ssh-secured-vm-from-template.md)

<!---HONumber=AcomDC_0907_2016-->