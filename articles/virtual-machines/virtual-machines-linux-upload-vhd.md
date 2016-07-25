<properties
	pageTitle="Création et téléchargement d'un VHD Linux | Microsoft Azure"
	description="Créez et téléchargez un disque dur virtuel vers Azure à l’aide du modèle de déploiement Resource Manager."
	services="virtual-machines-linux"
	documentationCenter=""
	authors="iainfoulds"
	manager="timlt"
	editor="tysonn"
	tags="azure-resource-manager"/>

<tags
	ms.service="virtual-machines-linux"
	ms.workload="infrastructure-services"
	ms.tgt_pltfrm="vm-linux"
	ms.devlang="na"
	ms.topic="article"
	ms.date="07/07/2016"
	ms.author="iainfou"/>

# Téléchargement d’un disque dur virtuel

Cet article vous montre comment télécharger un disque dur virtuel à l’aide du modèle de déploiement Resource Manager. Cette fonctionnalité vous permet d’installer et de configurer une distribution Linux selon vos besoins, puis d’utiliser ce disque dur virtuel pour créer rapidement des machines virtuelles Azure.

## Commandes rapides
Veillez à ce que [l’interface CLI Azure](../xplat-cli-install.md) soit connectée et utilise le mode Resource Manager (`azure config mode arm`).

Créez d’abord un groupe de ressources :

```bash
azure group create TestRG --location "WestUS"
```

Créez un compte de stockage qui contiendra vos disques virtuels :

```bash
azure storage account create testuploadedstorage --resource-group TestRG \
	--location "WestUS" --kind Storage --sku-name LRS
```

Répertoriez les clés de stockage du compte de stockage que vous venez de créer et prenez note de `key1` :

```bash
azure storage account keys list testuploadedstorage --resource-group TestRG
```

La sortie doit ressembler à ceci :

```
info:    Executing command storage account keys list
+ Getting storage account keys
data:    Name  Key                                                                                       Permissions
data:    ----  ----------------------------------------------------------------------------------------  -----------
data:    key1  d4XAvZzlGAgWdvhlWfkZ9q4k9bYZkXkuPCJ15NTsQOeDeowCDAdB80r9zA/tUINApdSGQ94H9zkszYyxpe8erw==  Full
data:    key2  Ww0T7g4UyYLaBnLYcxIOTVziGAAHvU+wpwuPvK4ZG0CDFwu/mAxS/YYvAQGHocq1w7/3HcalbnfxtFdqoXOw8g==  Full
info:    storage account keys list command OK
```

Créez un conteneur dans votre compte de stockage à l’aide de la clé de stockage que vous venez d’obtenir :

```bash
azure storage container create --account-name testuploadedstorage \
	--account-key <key1> --container vm-images
```

Enfin, téléchargez votre disque dur virtuel vers le conteneur que vous venez de créer :

```bash
azure storage blob upload --blobtype page --account-name testuploadedstorage \
	--account-key <key1> --container vm-images /path/to/disk/yourdisk.vhd
```

Vous pouvez maintenant créer une machine virtuelle à partir de votre disque virtuel téléchargé [en utilisant un modèle Resource Manager](https://github.com/Azure/azure-quickstart-templates/tree/master/201-vm-from-specialized-vhd) ou via l’interface CLI en spécifiant l’URI de votre disque, comme suit :

```bash
azure vm create TestVM -l "WestUS" --resource-group TestRG \
	-Q https://testuploadedstorage.blob.core.windows.net/vm-images/yourdisk.vhd
```

Notez que vous avez encore besoin de tous les paramètres supplémentaires requis par la commande `azure vm create`, tels que le réseau virtuel, l’adresse IP publique, le nom d’utilisateur, les clés SSH, etc. Vous pouvez en savoir plus sur les [paramètres Resource Manager disponibles de l’interface CLI](azure-cli-arm-commands.md#azure-vm-commands-to-manage-your-azure-virtual-machines).

## Configuration requise
Pour pouvoir effectuer les étapes ci-dessus, vous avez besoin des éléments suivants :

- **Un système d’exploitation Linux installé dans un fichier .vhd** : Installez une [distribution Linux approuvée par Azure](virtual-machines-linux-endorsed-distros.md) (ou consultez les [informations relatives aux distributions non approuvées](virtual-machines-linux-create-upload-generic.md)) sur un disque virtuel au format VHD. Plusieurs outils permettent de créer une machine virtuelle et un disque dur virtuel :
	- Installez et configurez [QEMU](https://en.wikibooks.org/wiki/QEMU/Installing_QEMU) ou [KVM](http://www.linux-kvm.org/page/RunningKVM), en veillant à utiliser VHD comme format d’image. Vous pouvez [convertir une image](https://en.wikibooks.org/wiki/QEMU/Images#Converting_image_formats) à l’aide de `qemu-img convert` si nécessaire.
	- Vous pouvez également utiliser Hyper-V [sur Windows 10](https://msdn.microsoft.com/virtualization/hyperv_on_windows/quick_start/walkthrough_install) ou [sur Windows Server 2012/2012 R2](https://technet.microsoft.com/library/hh846766.aspx).

> [AZURE.NOTE] Azure ne prend pas en charge le nouveau format VHDX. Lorsque vous créez une machine virtuelle, spécifiez le format VHD d’origine. Si nécessaire, vous pouvez convertir un VHDX au format VHD à l’aide de [`qemu-img convert`](https://en.wikibooks.org/wiki/QEMU/Images#Converting_image_formats) ou de l’applet de commande PowerShell [`Convert-VHD`](https://technet.microsoft.com/library/hh848454.aspx). De plus, Azure ne prend pas en charge le chargement de disques durs virtuels dynamiques. Vous devez convertir ces disques en disques durs virtuels statiques avant le chargement. Vous pouvez utiliser des outils tels que les [utilitaires de disque dur virtuel Azure pour GO](https://github.com/Microsoft/azure-vhd-utils-for-go) pour convertir les disques dynamiques au cours du processus de téléchargement vers Azure.


<a id="prepimage"> </a>
## préparation de l'image pour le téléchargement

Azure prend en charge diverses distributions de Linux (voir [Distributions Linux approuvées](virtual-machines-linux-endorsed-distros.md)). Les articles suivants vous montrent comment préparer les diverses distributions de Linux prises en charge dans Azure. Veillez à préparer correctement votre disque virtuel avant de le télécharger :

- **[Distributions CentOS](virtual-machines-linux-create-upload-centos.md)**
- **[Debian Linux](virtual-machines-linux-debian-create-upload-vhd.md)**
- **[Oracle Linux](virtual-machines-linux-oracle-create-upload-vhd.md)**
- **[Red Hat Enterprise Linux](virtual-machines-linux-redhat-create-upload-vhd.md)**
- **[SLES et openSUSE](virtual-machines-linux-suse-create-upload-vhd.md)**
- **[Ubuntu](virtual-machines-linux-create-upload-ubuntu.md)**
- **[Autres - Distributions non approuvées](virtual-machines-linux-create-upload-generic.md)**

Consultez également les **[notes d’installation Linux](virtual-machines-linux-create-upload-generic.md#general-linux-installation-notes)** pour obtenir d’autres conseils généraux sur la préparation d’images Linux pour Azure.

> [AZURE.NOTE] Le [contrat de niveau de service de la plateforme Azure](https://azure.microsoft.com/support/legal/sla/virtual-machines/) s'applique aux machines virtuelles exécutant Linux uniquement quand une des distributions approuvées est utilisée avec les détails de configuration définis sous « Versions prises en charge » dans [Linux dans les distributions approuvées par Azure](virtual-machines-linux-endorsed-distros.md).

## Étapes suivantes
Après avoir préparé et téléchargé votre disque virtuel personnalisé, vous pouvez en savoir plus sur [l’utilisation de Resource Manager et des modèles](../resource-group-overview.md). Vous pouvez également [ajouter un disque de données](virtual-machines-linux-add-disk.md) à vos nouvelles machines virtuelles. Si vous avez besoin d’accéder à des applications qui s’exécutent sur vos machines virtuelles, veillez à [ouvrir les ports et les points de terminaison](virtual-machines-linux-nsg-quickstart.md).

<!---HONumber=AcomDC_0713_2016-->