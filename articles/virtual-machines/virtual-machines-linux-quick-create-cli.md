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
   ms.date="09/08/2016"
   ms.author="v-livech"/>


# Créer une machine virtuelle Linux sur Azure à l’aide de l’interface de ligne de commande (CLI)

Cet article explique comment déployer rapidement une machine virtuelle Linux sur Azure à l’aide de la commande `azure vm quick-create` dans l’interface de ligne de commande (CLI) Azure. La commande `quick-create` déploie une machine virtuelle à l’intérieur d’une infrastructure sécurisée de base que vous pouvez utiliser comme prototype ou pour tester rapidement un concept. L’article requiert

- un compte Azure ([obtenir une version d’évaluation gratuite](https://azure.microsoft.com/pricing/free-trial/))

- [l’interface de ligne de commande Azure (CLI)](../xplat-cli-install.md) connectée à `azure login`.

- L’interface de ligne de commande (CLI) Azure _doit être en_ mode Azure Resource Manager `azure config mode arm`.

Vous pouvez également déployer rapidement une machine virtuelle Linux à l’aide du [Portail Azure](virtual-machines-linux-quick-create-portal.md).

## Commandes rapides

L’exemple suivant montre comment déployer une machine virtuelle CoreOS et comment attacher votre clé SSH (Secure Shell) (vos arguments peuvent être différents).

```bash
azure vm quick-create -M ~/.ssh/azure_id_rsa.pub -Q CoreOS
```

Les sections suivantes expliquent la commande et ses exigences à l’aide du serveur Ubuntu 14.04 LTS en tant que distribution Linux.

## Alias de création rapide de machine virtuelle

Un moyen rapide de choisir une distribution consiste à utiliser les alias d’interface de ligne de commande Azure mappés sur les distributions de système d’exploitation les plus courantes. Le tableau suivant répertorie les alias (à partir de la version 0.10 de l’interface de ligne de commande Azure). Tous les déploiements qui utilisent `quick-create` par défaut sur les machines virtuelles qui sont prises en charge par le stockage SSD, et qui offrent un approvisionnement plus rapide et un accès au disque hautes performances. (Ces alias représentent une infime partie des distributions disponibles dans Azure. Trouvez d’autres images dans Azure Marketplace en [faisant une recherche](virtual-machines-linux-cli-ps-findimage.md) ou en [chargeant votre image personnalisée](virtual-machines-linux-create-upload-generic.md).)

| Alias | Éditeur | Offer | SKU | Version |
|:----------|:----------|:-------------|:------------|:--------|
| CentOS | OpenLogic | CentOS | 7,2 | le plus récent |
| CoreOS | CoreOS | CoreOS | Stable | le plus récent |
| Debian | credativ | Debian | 8 | le plus récent |
| openSUSE | SUSE | openSUSE | 13\.2 | le plus récent |
| RHEL | Red Hat | RHEL | 7,2 | le plus récent |
| UbuntuLTS | Canonical | Serveur Ubuntu | 14\.04.4-LTS | le plus récent |

Les sections suivantes utilisent l’alias `UbuntuLTS` pour l’option **ImageURN** (`-Q`) pour déployer un serveur Ubuntu 14.04.4 LTS.

## Procédure pas à pas

Le précédent exemple `quick-create` appelait uniquement l’indicateur `-M` pour identifier la clé publique SSH à charger lors de la désactivation des mots de passe SSH. Par conséquent, vous êtes invité à entrer les éléments suivants :

- nom du groupe de ressources (toute chaîne convient généralement pour votre premier groupe de ressources Azure)
- Nom de la machine virtuelle
- emplacement (westus ou westeurope sont de bonnes valeurs par défaut)
- linux (pour indiquer à Azure le système d’exploitation souhaité)
- username

Les éléments suivants spécifient toutes les valeurs, ainsi aucune autre invite n’est requise. Dans la mesure où vous avez un `~/.ssh/id_rsa.pub` en tant que fichier de clé publique au format ssh-rsa, il fonctionne comme suit.

```bash
azure vm quick-create \
-g exampleResourceGroup \
-n exampleVMName \
-l westus \
-y Linux \
-u exampleAdminUser \
-M ~/.ssh/id_rsa.pub \
-Q UbuntuLTS
```

La sortie doit ressembler au bloc de sortie suivant.

```bash
info:    Executing command vm quick-create
+ Listing virtual machine sizes available in the location "westus"
+ Looking up the VM "exampleVMName"
info:    Verifying the public key SSH file: /Users/ahmet/.ssh/id_rsa.pub
info:    Using the VM Size "Standard_DS1"
info:    The [OS, Data] Disk or image configuration requires storage account
+ Looking up the storage account cli16330708391032639673
+ Looking up the NIC "examp-westu-1633070839-nic"
info:    An nic with given name "examp-westu-1633070839-nic" not found, creating a new one
+ Looking up the virtual network "examp-westu-1633070839-vnet"
info:    Preparing to create new virtual network and subnet
/ Creating a new virtual network "examp-westu-1633070839-vnet" [address prefix: "10.0.0.0/16"] with subnet "examp-westu-1633070839-snet" [address prefix: "10.+.1.0/24"]
+ Looking up the virtual network "examp-westu-1633070839-vnet"
+ Looking up the subnet "examp-westu-1633070839-snet" under the virtual network "examp-westu-1633070839-vnet"
info:    Found public ip parameters, trying to setup PublicIP profile
+ Looking up the public ip "examp-westu-1633070839-pip"
info:    PublicIP with given name "examp-westu-1633070839-pip" not found, creating a new one
+ Creating public ip "examp-westu-1633070839-pip"
+ Looking up the public ip "examp-westu-1633070839-pip"
+ Creating NIC "examp-westu-1633070839-nic"
+ Looking up the NIC "examp-westu-1633070839-nic"
+ Looking up the storage account clisto1710997031examplev
+ Creating VM "exampleVMName"
+ Looking up the VM "exampleVMName"
+ Looking up the NIC "examp-westu-1633070839-nic"
+ Looking up the public ip "examp-westu-1633070839-pip"
data:    Id                              :/subscriptions/2<--snip-->d/resourceGroups/exampleResourceGroup/providers/Microsoft.Compute/virtualMachines/exampleVMName
data:    ProvisioningState               :Succeeded
data:    Name                            :exampleVMName
data:    Location                        :westus
data:    Type                            :Microsoft.Compute/virtualMachines
data:
data:    Hardware Profile:
data:      Size                          :Standard_DS1
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
data:        Name                        :clic7fadb847357e9cf-os-1473374894359
data:        Caching                     :ReadWrite
data:        CreateOption                :FromImage
data:        Vhd:
data:          Uri                       :https://cli16330708391032639673.blob.core.windows.net/vhds/clic7fadb847357e9cf-os-1473374894359.vhd
data:
data:    OS Profile:
data:      Computer Name                 :exampleVMName
data:      User Name                     :exampleAdminUser
data:      Linux Configuration:
data:        Disable Password Auth       :true
data:
data:    Network Profile:
data:      Network Interfaces:
data:        Network Interface #1:
data:          Primary                   :true
data:          MAC Address               :00-0D-3A-33-42-FB
data:          Provisioning State        :Succeeded
data:          Name                      :examp-westu-1633070839-nic
data:          Location                  :westus
data:            Public IP address       :138.91.247.29
data:            FQDN                    :examp-westu-1633070839-pip.westus.cloudapp.azure.com
data:
data:    Diagnostics Profile:
data:      BootDiagnostics Enabled       :true
data:      BootDiagnostics StorageUri    :https://clisto1710997031examplev.blob.core.windows.net/
data:
data:      Diagnostics Instance View:
info:    vm quick-create command OK
```

Connectez-vous à votre machine virtuelle à l’aide de l’adresse IP publique répertoriée dans la sortie. Vous pouvez également utiliser le nom de domaine complet (FQDN) répertorié.

```bash
ssh -i ~/.ssh/id_rsa.pub exampleAdminUser@138.91.247.29
```

Le processus de connexion doit ressembler à ceci :

```bash
Warning: Permanently added '138.91.247.29' (ECDSA) to the list of known hosts.
Welcome to Ubuntu 14.04.4 LTS (GNU/Linux 3.19.0-65-generic x86_64)

 * Documentation:  https://help.ubuntu.com/

  System information as of Thu Sep  8 22:50:57 UTC 2016

  System load: 0.63              Memory usage: 2%   Processes:       81
  Usage of /:  39.6% of 1.94GB   Swap usage:   0%   Users logged in: 0

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

exampleAdminUser@exampleVMName:~$
```

## Étapes suivantes

La commande `azure vm quick-create` permet de déployer rapidement une machine virtuelle pour se connecter à un shell bash et être opérationnel. Toutefois, l’utilisation de `vm quick-create` ne vous confère pas un contrôle étendu et ne vous permet pas de créer un environnement plus complexe. Pour déployer une machine virtuelle Linux personnalisée en fonction de votre infrastructure, lisez l’un des articles ci-dessous :

- [Déploiement et gestion de machines virtuelles à l’aide des modèles Azure Resource Manager et de l’interface de ligne de commande Azure](virtual-machines-linux-cli-deploy-templates.md)
- [Créer votre propre environnement personnalisé pour une machine virtuelle Linux à l’aide des commandes de l’interface de ligne de commande Azure directement](virtual-machines-linux-create-cli-complete.md)
- [Création d’une machine virtuelle Linux sécurisée par SSH dans Azure à l’aide de modèles](virtual-machines-linux-create-ssh-secured-vm-from-template.md)

Vous pouvez également [utiliser le pilote Azure `docker-machine` avec plusieurs commandes pour créer rapidement une machine virtuelle Linux comme un hôte docker](virtual-machines-linux-docker-machine.md).

<!---HONumber=AcomDC_0914_2016-->