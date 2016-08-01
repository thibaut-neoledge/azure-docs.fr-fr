<properties
	pageTitle="Créer et charger une image Linux personnalisée | Microsoft Azure"
	description="Créez et chargez un disque dur virtuel vers Azure à l’aide d’une image Linux personnalisée, en utilisant le modèle de déploiement Resource Manager."
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
	ms.date="07/15/2016"
	ms.author="iainfou"/>

# Charger et créer une machine virtuelle à partir d’une image de disque personnalisée

Cet article indique comment charger un disque dur virtuel à l’aide du modèle de déploiement Resource Manager, et comment créer des machines virtuelles à partir de cette image personnalisée. Cette fonctionnalité vous permet d’installer et de configurer une distribution Linux selon vos besoins, puis d’utiliser ce disque dur virtuel pour créer rapidement des machines virtuelles Azure.

## Commandes rapides
Veillez à ce que [la CLI Azure](../xplat-cli-install.md) soit connectée et utilise le mode Resource Manager (`azure config mode arm`).

Créez d’abord un groupe de ressources :

```bash
azure group create TestRG --location "WestUS"
```

Créez un compte de stockage qui contiendra vos disques virtuels :

```bash
azure storage account create testuploadedstorage --resource-group TestRG \
	--location "WestUS" --kind Storage --sku-name PLRS
```

Répertoriez les clés d’accès au compte de stockage que vous venez de créer et prenez note de `key1` :

```bash
azure storage account keys list testuploadedstorage --resource-group TestRG
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

Vous pouvez maintenant créer une machine virtuelle à partir du disque virtuel chargé [en utilisant un modèle Resource Manager](https://github.com/Azure/azure-quickstart-templates/tree/master/201-vm-from-specialized-vhd) ou via la CLI en spécifiant l’URI de votre disque, comme suit :

```bash
azure vm create TestVM -l "WestUS" --resource-group TestRG \
	-Q https://testuploadedstorage.blob.core.windows.net/vm-images/yourdisk.vhd
```

Remarque : le compte de stockage de destination doit être celui sur lequel vous avez chargé votre disque virtuel. Vous devez également spécifier, ou répondre aux invites relatives à tous les paramètres supplémentaires requis par la commande `azure vm create`, tels que le réseau virtuel, l’adresse IP publique, le nom d’utilisateur, les clés SSH, etc. Vous pouvez en savoir plus sur les [paramètres Resource Manager disponibles la CLI](azure-cli-arm-commands.md#azure-vm-commands-to-manage-your-azure-virtual-machines).


## Procédure détaillée
La préparation de votre image Linux personnalisée et son chargement dans Azure nécessitent un certain nombre d’étapes. Le reste de cet article fournit des informations plus détaillées sur chaque étape indiquée dans l’ensemble précédent de commandes rapides.


## Configuration requise
Pour pouvoir effectuer les étapes ci-dessus, vous avez besoin des éléments suivants :

- **Un système d’exploitation Linux installé dans un fichier .vhd** : Installez une [distribution Linux approuvée par Azure](virtual-machines-linux-endorsed-distros.md) (ou consultez les [informations relatives aux distributions non approuvées](virtual-machines-linux-create-upload-generic.md)) sur un disque virtuel au format VHD. Plusieurs outils permettent de créer une machine virtuelle et un disque dur virtuel :
	- Installez et configurez [QEMU](https://en.wikibooks.org/wiki/QEMU/Installing_QEMU) ou [KVM](http://www.linux-kvm.org/page/RunningKVM), en veillant à utiliser VHD comme format d’image. Vous pouvez [convertir une image](https://en.wikibooks.org/wiki/QEMU/Images#Converting_image_formats) à l’aide de `qemu-img convert` si nécessaire.
	- Vous pouvez également utiliser Hyper-V [sur Windows 10](https://msdn.microsoft.com/virtualization/hyperv_on_windows/quick_start/walkthrough_install) ou [sur Windows Server 2012/2012 R2](https://technet.microsoft.com/library/hh846766.aspx).

> [AZURE.NOTE] Azure ne prend pas en charge le nouveau format VHDX. Lorsque vous créez une machine virtuelle, spécifiez le format de disque dur virtuel (VHD). Si nécessaire, vous pouvez convertir des disques VHDX au format VHD à l’aide de [`qemu-img convert`](https://en.wikibooks.org/wiki/QEMU/Images#Converting_image_formats) ou de l’applet de commande PowerShell [`Convert-VHD`](https://technet.microsoft.com/library/hh848454.aspx). De plus, Azure ne prend pas en charge le chargement de disques durs virtuels dynamiques. Vous devez convertir ces disques en disques durs virtuels statiques avant le chargement. Vous pouvez utiliser des outils tels que les [utilitaires de disque dur virtuel Azure pour GO](https://github.com/Microsoft/azure-vhd-utils-for-go) pour convertir les disques dynamiques au cours du processus de téléchargement vers Azure.

- Les machines virtuelles créées à partir de votre image personnalisée doivent se trouver au sein du même compte de stockage que l’image elle-même.
	- Créez un compte de stockage et un conteneur pour stocker les machines virtuelles créées et votre image personnalisée.
	- Après avoir créé toutes vos machines virtuelles, vous pouvez supprimer votre image en toute sécurité.


<a id="prepimage"> </a>
## préparation de l’image pour le téléchargement

Azure prend en charge diverses distributions de Linux (voir [Distributions Linux approuvées](virtual-machines-linux-endorsed-distros.md)). Les articles suivants indiquent comment préparer les diverses distributions de Linux prises en charge dans Azure :

- **[Distributions CentOS](virtual-machines-linux-create-upload-centos.md)**
- **[Debian Linux](virtual-machines-linux-debian-create-upload-vhd.md)**
- **[Oracle Linux](virtual-machines-linux-oracle-create-upload-vhd.md)**
- **[Red Hat Enterprise Linux](virtual-machines-linux-redhat-create-upload-vhd.md)**
- **[SLES et openSUSE](virtual-machines-linux-suse-create-upload-vhd.md)**
- **[Ubuntu](virtual-machines-linux-create-upload-ubuntu.md)**
- **[Autres - Distributions non approuvées](virtual-machines-linux-create-upload-generic.md)**

Consultez également les **[notes d’installation Linux](virtual-machines-linux-create-upload-generic.md#general-linux-installation-notes)** pour obtenir d’autres conseils généraux sur la préparation d’images Linux pour Azure.

> [AZURE.NOTE] Le [contrat de niveau de service de la plateforme Azure](https://azure.microsoft.com/support/legal/sla/virtual-machines/) s’applique aux machines virtuelles exécutant Linux uniquement lorsqu’une des distributions approuvées est utilisée avec les détails de configuration définis sous la rubrique « Versions prises en charge » de la section [Linux dans les distributions approuvées par Azure](virtual-machines-linux-endorsed-distros.md).


## Créer un groupe de ressources
Les groupes de ressources regroupent de manière logique l’ensemble des ressources Azure afin d’assurer la prise en charge de vos machines virtuelles, par exemple le stockage et la mise en réseau virtuel. Découvrez plus d’informations sur les [groupes de ressources Azure ici](../resource-group-overview.md). Avant de charger l’image de disque personnalisée et de créer des machines virtuelles, vous devez créer un groupe de ressources :

```bash
azure group create TestRG --location "WestUS"
```

## Créez un compte de stockage.
Les machines virtuelles sont stockées en tant qu’objets blob de pages au sein d’un compte de stockage. Découvrez plus d’informations sur [Azure Storage ici](../storage/storage-introduction.md#blob-storage). Vous devez créer un compte de stockage pour votre image de disque personnalisée et vos machines virtuelles. Toutes les machines virtuelles que vous créez à partir de votre image de disque personnalisée doivent être placées dans le même compte de stockage que cette image.

Créez un compte de stockage au sein du groupe de ressources que vous venez de générer :

```bash
azure storage account create testuploadedstorage --resource-group TestRG \
	--location "WestUS" --kind Storage --sku-name PLRS
```

## Répertorier les clés de compte de stockage
Azure génère deux clés d’accès de 512 bits pour chaque compte de stockage. Ces clés d’accès sont utilisées lors de l’authentification auprès du compte de stockage, par exemple pour effectuer des opérations d’écriture. Découvrez plus d’informations sur la [gestion de l’accès au stockage ici](../storage/storage-create-storage-account.md#manage-your-storage-account). Utilisez la commande `azure storage account keys list` pour afficher les clés d’accès.

Affichez les clés d’accès au compte de stockage que vous venez de créer :

```bash
azure storage account keys list testuploadedstorage --resource-group TestRG
```

La sortie doit ressembler à ceci :

```
info:    Executing command storage account keys list
+ Getting storage account keys
data:    Name  Key                                                                                       Permissions
data:    ----  ----------------------------------------------------------------------------------------  -----------
data:    key1  d4XAvZzlGAgWdvhlWfkZ9q4k9bYZkXkuPCJ15NTsQOeDeowCDAdB80r9zA/tUINApdSGQ94H9zkszYyxpe8erw==  Full
data:    key2  Ww0T7g4UyYLaBnLYcxIOTVziGAAHvU+wpwuPvK4ZG0CDFwu/mAxS/YYvAQGHocq1w7/3HcalbnfxtFdqoXOw8g==  Full
info:    storage account keys list command OK

```
Prenez note de l’élément `key1`, car vous allez l’utiliser pour interagir avec votre compte de stockage au cours des étapes suivantes.

## Créer un conteneur de stockage
De la même façon que vous créez des répertoires différents pour organiser de manière logique votre système de fichiers local, vous créez des conteneurs dans un compte de stockage afin d’organiser vos disques virtuels et images de disque. Un compte de stockage peut contenir un certain nombre de conteneurs.

Créez un conteneur, en indiquant la clé d’accès obtenue au cours de l’étape précédente :

```bash
azure storage container create --account-name testuploadedstorage \
	--account-key <key1> --container vm-images
```

## Charger un disque dur virtuel
Désormais, vous pouvez charger votre image de disque personnalisée. Comme pour tous les disques virtuels utilisés par les machines virtuelles, vous devez charger et stocker votre image de disque personnalisée en tant qu’objet blob de pages.

Vous devrez spécifier votre clé d’accès, le conteneur que vous avez créé lors de l’étape précédente, puis le chemin d’accès à l’image de disque personnalisée sur votre ordinateur local :

```bash
azure storage blob upload --blobtype page --account-name testuploadedstorage \
	--account-key <key1> --container vm-images /path/to/disk/yourdisk.vhd
```

## Créer une machine virtuelle à partir d’une image personnalisée
Lorsque vous créez des machines virtuelles à partir de votre image de disque personnalisée, vous devez spécifier l’URI associé à l’image de disque et vérifier que le compte de stockage de destination correspond à l’emplacement de stockage de l’image de disque personnalisée. Vous pouvez créer votre machine virtuelle à l’aide de la CLI Azure ou du modèle JSON du gestionnaire de ressources.


### Créer une machine virtuelle à l’aide de la CLI Azure
Vous spécifiez le paramètre `--image-urn` (ou `-Q`, simplement) avec la commande `azure vm create` pour pointer vers l’image de disque personnalisée. Vérifiez que le paramètre `--storage-account-name` (ou `-o`) correspond au compte de stockage dans lequel se trouve l’image de disque personnalisée. Vous n’êtes pas obligé d’utiliser le même conteneur que l’image de disque personnalisée pour stocker vos machines virtuelles. Toutefois, vous devez créer tous les conteneurs supplémentaires en suivant la procédure indiquée plus haut avant de charger vos images de disque personnalisées.

Créez une machine virtuelle à partir d’une image de disque personnalisée :

```bash
azure vm create TestVM -l "WestUS" --resource-group TestRG \
	-Q https://testuploadedstorage.blob.core.windows.net/vm-images/yourdisk.vhd
	-o testuploadedstorage
```

Remarque : vous devez malgré tout spécifier, ou répondre aux invites relatives à tous les paramètres supplémentaires requis par la commande `azure vm create`, tels que le réseau virtuel, l’adresse IP publique, le nom d’utilisateur, les clés SSH, etc. Découvrez plus d’informations sur les [paramètres Resource Manager disponibles de la CLI](azure-cli-arm-commands.md#azure-vm-commands-to-manage-your-azure-virtual-machines).

### Créer une machine virtuelle à l’aide d’un modèle JSON
Les modèles Azure Resource Manager sont des fichiers JSON (JavaScript Objet Notation) qui définissent l’environnement que vous souhaitez générer. Les modèles sont répartis parmi différents fournisseurs de ressources, tels que les fournisseurs de calcul ou réseau. Vous pouvez utiliser les modèles existants ou écrire les vôtres. Découvrez plus d’informations sur l’[utilisation des modèles et de Resource Manager](../resource-group-overview.md).

Dans le fournisseur `Microsoft.Compute/virtualMachines` de votre modèle, vous disposerez d’un nœud `storageProfile` qui contient les détails de configuration de votre machine virtuelle. Les deux principaux paramètres à modifier sont les URI `image` et `vhd` qui pointent vers votre image de disque personnalisée et le disque virtuel de votre nouvelle machine virtuelle. Voici un exemple de JSON pour l’utilisation d’une image de disque personnalisée :

```bash
"storageProfile": {
          "osDisk": {
            "name": "TestVM",
            "osType": "Linux",
            "caching": "ReadWrite",
			"createOption": "FromImage",
            "image": {
              "uri": "https://testuploadedstorage.blob.core.windows.net/vm-images/yourdisk.vhd"
            },
            "vhd": {
              "uri": "https://testuploadedstorage.blob.core.windows.net/vhds/newvmname.vhd"
            }
          }
```

Vous pouvez utiliser [ce modèle existant pour créer une machine virtuelle à partir d’une image personnalisée](https://github.com/Azure/azure-quickstart-templates/tree/master/101-vm-from-user-image) ou en savoir plus sur [la création de vos propres modèles Azure Resource Manager] (../resource-group-authoring templates.md).

Une fois que vous avez configuré un modèle, vous pouvez créer vos machines virtuelles à l’aide de la commande `azure group deployment create`. Spécifiez l’URI de votre modèle JSON avec le paramètre `--template-uri` :

```bash
azure group deployment create --resource-group TestTemplateRG
	--template-uri https://uri.to.template/yourtemplate.json
```

Si vous disposez d’un fichier JSON stocké localement sur votre ordinateur, vous pouvez utiliser le paramètre `--template-file` à la place :

```bash
azure group deployment create --resource-group TestTemplateRG
	--template-file /path/to/yourtemplate.json
```


## Étapes suivantes
Après avoir préparé et chargé votre disque virtuel personnalisé, vous pouvez en savoir plus sur [l’utilisation de Resource Manager et des modèles](../resource-group-overview.md). Vous pouvez également [ajouter un disque de données](virtual-machines-linux-add-disk.md) à vos nouvelles machines virtuelles. Si vous avez besoin d’accéder à des applications qui s’exécutent sur vos machines virtuelles, veillez à [ouvrir les ports et points de terminaison](virtual-machines-linux-nsg-quickstart.md).

<!---HONumber=AcomDC_0720_2016-->