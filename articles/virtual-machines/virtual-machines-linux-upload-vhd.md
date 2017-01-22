---
title: "Créer et charger une image Linux personnalisée | Microsoft Docs"
description: "Créez et chargez un disque dur virtuel vers Azure à l’aide d’une image Linux personnalisée en utilisant le modèle de déploiement Resource Manager."
services: virtual-machines-linux
documentationcenter: 
author: iainfoulds
manager: timlt
editor: tysonn
tags: azure-resource-manager
ms.assetid: a8c7818f-eb65-409e-aa91-ce5ae975c564
ms.service: virtual-machines-linux
ms.workload: infrastructure-services
ms.tgt_pltfrm: vm-linux
ms.devlang: na
ms.topic: article
ms.date: 10/10/2016
ms.author: iainfou
translationtype: Human Translation
ms.sourcegitcommit: 9f944edcafb55634c9338bc8dd518a5f032f56e0
ms.openlocfilehash: edab3b3058a2e28ce74e62434b85b1643f267493


---
# <a name="upload-and-create-a-linux-vm-from-custom-disk-image"></a>Chargement et création d’une machine virtuelle à partir d’une image de disque personnalisée
Cet article indique comment charger un disque dur virtuel dans Azure à l’aide du modèle de déploiement Resource Manager, et comment créer des machines virtuelles Linux à partir de cette image personnalisée. Cette fonctionnalité vous permet d’installer et de configurer une distribution Linux selon vos besoins, puis d’utiliser ce disque dur virtuel pour créer rapidement des machines virtuelles Azure.

## <a name="quick-commands"></a>Commandes rapides
Si vous avez besoin d’accomplir rapidement cette tâche, la section suivante décrit les commandes de base qui vous permettront de charger une machine virtuelle dans Azure. Pour obtenir plus d’informations et davantage de contexte pour chaque étape, lisez la suite de ce document, [à partir de cette section](#requirements).

Veillez à ce que [l’interface de ligne de commande Azure](../xplat-cli-install.md) soit connectée et utilise le mode Resource Manager :

```bash
azure config mode arm
```

Dans les exemples suivants, remplacez les exemples de noms de paramètre par vos propres valeurs. Exemples de noms de paramètre : `myResourceGroup`, `mystorageaccount` et `myimages`.

Créez d’abord un groupe de ressources. L’exemple suivant crée un groupe de ressources nommé `myResourceGroup` à l’emplacement `WestUs` :

```bash
azure group create myResourceGroup --location "WestUS"
```

Créez un compte de stockage qui contiendra vos disques virtuels. L’exemple qui suit permet de créer un compte de stockage nommé `mystorageaccount` :

```bash
azure storage account create mystorageaccount --resource-group myResourceGroup \
    --location "WestUS" --kind Storage --sku-name PLRS
```

Dressez la liste des clés d’accès pour votre compte de stockage. Notez la valeur de `key1` :

```bash
azure storage account keys list mystorageaccount --resource-group myResourceGroup
```

Créez un conteneur dans votre compte de stockage à l’aide de la clé de stockage que vous venez d’obtenir. L’exemple suivant crée un conteneur nommé `myimages` à l’aide de la valeur de clé de stockage de `key1` :

```bash
azure storage container create --account-name mystorageaccount \
    --account-key key1 --container myimages
```

Enfin, téléchargez votre disque dur virtuel vers le conteneur que vous avez créé. Spécifiez le chemin d’accès local à votre disque dur virtuel sous `/path/to/disk/mydisk.vhd` :

```bash
azure storage blob upload --blobtype page --account-name mystorageaccount \
    --account-key key1 --container myimages /path/to/disk/mydisk.vhd
```

Vous pouvez maintenant créer une machine virtuelle à partir de votre disque virtuel chargé [à l’aide d’un modèle Resource Manager](https://github.com/Azure/azure-quickstart-templates/tree/master/201-vm-from-specialized-vhd). Vous pouvez également utiliser l’interface de ligne de commande en spécifiant l’URI pour votre disque (`--image-urn`). L’exemple suivant crée une machine virtuelle nommée `myVM` à l’aide du disque virtuel que vous avez téléchargé :

```bash
azure vm create myVM -l "WestUS" --resource-group myResourceGroup \
    --image-urn https://mystorageaccount.blob.core.windows.net/myimages/mydisk.vhd
```

Le compte de stockage de destination doit être celui sur lequel vous avez chargé votre disque virtuel. Vous devez également spécifier, ou répondre aux invites relatives à tous les paramètres supplémentaires nécessaires à la commande `azure vm create` , tels que le réseau virtuel, l’adresse IP publique, le nom d’utilisateur, les clés SSH, etc. Vous pouvez en savoir plus sur les [paramètres de l’interface de ligne de commande Resource Manager](azure-cli-arm-commands.md#azure-vm-commands-to-manage-your-azure-virtual-machines).

## <a name="requirements"></a>Configuration requise
Pour effectuer les étapes suivantes, vous avez besoin des éléments suivants :

* **Un système d’exploitation Linux installé dans un fichier .vhd** : installez une [distribution Linux approuvée par Azure](virtual-machines-linux-endorsed-distros.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json) (ou consultez les [informations relatives aux distributions non approuvées](virtual-machines-linux-create-upload-generic.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json)) sur un disque virtuel au format VHD. Plusieurs outils permettent de créer une machine virtuelle et un disque dur virtuel :
  * Installez et configurez [QEMU](https://en.wikibooks.org/wiki/QEMU/Installing_QEMU) ou [KVM](http://www.linux-kvm.org/page/RunningKVM), en veillant à utiliser VHD comme format d’image. Vous pouvez [convertir une image](https://en.wikibooks.org/wiki/QEMU/Images#Converting_image_formats) à l’aide de `qemu-img convert` si nécessaire.
  * Vous pouvez également utiliser Hyper-V [sur Windows 10](https://msdn.microsoft.com/virtualization/hyperv_on_windows/quick_start/walkthrough_install) ou [sur Windows Server 2012/2012 R2](https://technet.microsoft.com/library/hh846766.aspx).

> [!NOTE]
> Azure ne prend pas en charge le nouveau format VHDX. Lorsque vous créez une machine virtuelle, spécifiez le format de disque dur virtuel (VHD). Si nécessaire, vous pouvez convertir des disques VHDX au format VHD à l’aide de [`qemu-img convert`](https://en.wikibooks.org/wiki/QEMU/Images#Converting_image_formats) ou de l’applet de commande PowerShell [`Convert-VHD`](https://technet.microsoft.com/library/hh848454.aspx). De plus, Azure ne prend pas en charge le chargement de disques durs virtuels dynamiques. Vous devez convertir ces disques en disques durs virtuels statiques avant le chargement. Vous pouvez utiliser des outils tels que les [utilitaires de disque dur virtuel Azure pour GO](https://github.com/Microsoft/azure-vhd-utils-for-go) pour convertir les disques dynamiques au cours du processus de téléchargement vers Azure.
> 
> 

* Les machines virtuelles créées à partir de votre image personnalisée doivent se trouver au sein du même compte de stockage que l’image elle-même.
  * Créez un compte de stockage et un conteneur pour stocker les machines virtuelles créées et votre image personnalisée.
  * Après avoir créé toutes vos machines virtuelles, vous pouvez supprimer votre image en toute sécurité.

Veillez à ce que [l’interface de ligne de commande Azure](../xplat-cli-install.md) soit connectée et utilise le mode Resource Manager :

```bash
azure config mode arm
```

Dans les exemples suivants, remplacez les exemples de noms de paramètre par vos propres valeurs. Exemples de noms de paramètre : `myResourceGroup`, `mystorageaccount` et `myimages`.

<a id="prepimage"> </a>

## <a name="prepare-the-image-to-be-uploaded"></a>préparation de l'image pour le téléchargement
Azure prend en charge diverses distributions de Linux (voir [Distributions Linux approuvées](virtual-machines-linux-endorsed-distros.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json)). Les articles suivants indiquent comment préparer les diverses distributions de Linux prises en charge dans Azure :

* **[Distributions CentOS](virtual-machines-linux-create-upload-centos.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json)**
* **[Debian Linux](virtual-machines-linux-debian-create-upload-vhd.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json)**
* **[Oracle Linux](virtual-machines-linux-oracle-create-upload-vhd.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json)**
* **[Red Hat Enterprise Linux](virtual-machines-linux-redhat-create-upload-vhd.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json)**
* **[SLES et openSUSE](virtual-machines-linux-suse-create-upload-vhd.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json)**
* **[Ubuntu](virtual-machines-linux-create-upload-ubuntu.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json)**
* **[Autres - Distributions non approuvées](virtual-machines-linux-create-upload-generic.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json)**

Consultez également les **[notes d’installation Linux](virtual-machines-linux-create-upload-generic.md#general-linux-installation-notes)** pour obtenir d’autres conseils généraux sur la préparation d’images Linux pour Azure.

> [!NOTE]
> Le [contrat de niveau de service de la plateforme Azure](https://azure.microsoft.com/support/legal/sla/virtual-machines/) s’applique aux machines virtuelles exécutant Linux uniquement lorsqu’une des distributions approuvées est utilisée avec les détails de configuration définis sous la rubrique « Versions prises en charge » de la section [Linux dans les distributions approuvées par Azure](virtual-machines-linux-endorsed-distros.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json).
> 
> 

## <a name="create-a-resource-group"></a>Créer un groupe de ressources
Les groupes de ressources regroupent de manière logique l’ensemble des ressources Azure afin d’assurer la prise en charge de vos machines virtuelles, par exemple le stockage et la mise en réseau virtuel. Découvrez plus d’informations sur les [groupes de ressources Azure ici](../azure-resource-manager/resource-group-overview.md). Avant de charger l’image de disque personnalisée et de créer des machines virtuelles, vous devez créer un groupe de ressources. 

L’exemple suivant crée un groupe de ressources nommé `myResourceGroup` à l’emplacement `WestUS` :

```bash
azure group create myResourceGroup --location "WestUS"
```

## <a name="create-a-storage-account"></a>Créez un compte de stockage.
Les machines virtuelles sont stockées en tant qu’objets blob de pages au sein d’un compte de stockage. Découvrez plus d’informations sur [Azure Storage ici](../storage/storage-introduction.md#blob-storage). Vous créez un compte de stockage pour votre image de disque personnalisée et vos machines virtuelles. Toutes les machines virtuelles que vous créez à partir de votre image de disque personnalisée doivent être placées dans le même compte de stockage que cette image.

L’exemple suivant crée un compte de stockage nommé `mystorageaccount` dans le groupe de ressources que vous avez créé :

```bash
azure storage account create mystorageaccount --resource-group myResourceGroup \
    --location "WestUS" --kind Storage --sku-name PLRS
```

## <a name="list-storage-account-keys"></a>Répertorier les clés de compte de stockage
Azure génère deux clés d’accès de 512 bits pour chaque compte de stockage. Ces clés d’accès sont utilisées lors de l’authentification auprès du compte de stockage, par exemple pour effectuer des opérations d’écriture. Découvrez plus d’informations sur la [gestion de l’accès au stockage ici](../storage/storage-create-storage-account.md#manage-your-storage-account). Utilisez la commande `azure storage account keys list` pour afficher les clés d’accès.

Affichez les clés d’accès au compte de stockage que vous avez créé :

```bash
azure storage account keys list mystorageaccount --resource-group myResourceGroup
```

Le résultat ressemble à ce qui suit :

```
info:    Executing command storage account keys list
+ Getting storage account keys
data:    Name  Key                                                                                       Permissions
data:    ----  ----------------------------------------------------------------------------------------  -----------
data:    key1  d4XAvZzlGAgWdvhlWfkZ9q4k9bYZkXkuPCJ15NTsQOeDeowCDAdB80r9zA/tUINApdSGQ94H9zkszYyxpe8erw==  Full
data:    key2  Ww0T7g4UyYLaBnLYcxIOTVziGAAHvU+wpwuPvK4ZG0CDFwu/mAxS/YYvAQGHocq1w7/3HcalbnfxtFdqoXOw8g==  Full
info:    storage account keys list command OK

```
Prenez note de l’élément `key1` , car vous allez l’utiliser pour interagir avec votre compte de stockage au cours des étapes suivantes.

## <a name="create-a-storage-container"></a>Créer un conteneur de stockage
De la même façon que vous créez des répertoires différents pour organiser de manière logique votre système de fichiers local, vous créez des conteneurs dans un compte de stockage afin d’organiser vos disques virtuels et images. Un compte de stockage peut contenir un certain nombre de conteneurs. 

L’exemple suit crée un conteneur nommé `myimages`, en indiquant la clé d’accès obtenue au cours de l’étape précédente (`key1`) :

```bash
azure storage container create --account-name mystorageaccount \
    --account-key key1 --container myimages
```

## <a name="upload-vhd"></a>Charger un disque dur virtuel
Désormais, vous pouvez charger votre image de disque personnalisée. Comme pour tous les disques virtuels utilisés par les machines virtuelles, vous devez charger et stocker votre image de disque personnalisée en tant qu’objet blob de pages.

Vous devrez spécifier votre clé d’accès, le conteneur que vous avez créé au cours de l’étape précédente, puis le chemin d’accès à l’image de disque personnalisée sur votre ordinateur local :

```bash
azure storage blob upload --blobtype page --account-name mystorageaccount \
    --account-key key1 --container myimages /path/to/disk/mydisk.vhd
```

## <a name="create-vm-from-custom-image"></a>Créer une machine virtuelle à partir d’une image personnalisée
Lorsque vous créez des machines virtuelles à partir de votre image de disque personnalisé, spécifiez l’URI de l’image de disque. Vérifiez que la destination correspond au compte de stockage dans lequel se trouve l’image de disque personnalisée. Vous pouvez créer votre machine virtuelle à l’aide de l’interface de ligne de commande Azure ou du modèle JSON Resourse Manager.

### <a name="create-a-vm-using-the-azure-cli"></a>Créer une machine virtuelle à l’aide de la CLI Azure
Vous spécifiez le paramètre `--image-urn` avec la commande `azure vm create` pour pointer vers l’image de disque personnalisée. Vérifiez que le paramètre `--storage-account-name` correspond au compte de stockage dans lequel se trouve l’image de disque personnalisée. Vous n’avez pas à utiliser le même conteneur que l’image de disque personnalisé pour stocker vos machines virtuelles. Veillez à créer des conteneurs supplémentaires de la même manière que dans les étapes précédentes avant de charger vos images de disques personnalisées.

L’exemple suivant crée une machine virtuelle nommée `myVM` à partir de votre image de disque personnalisée :

```bash
azure vm create myVM -l "WestUS" --resource-group myResourceGroup \
    --image-urn https://mystorageaccount.blob.core.windows.net/myimages/mydisk.vhd
    --storage-account-name mystorageaccount
```

Vous devez encore spécifier tous les paramètres supplémentaires exigés par la commande `azure vm create` ou répondre aux invites correspondantes, notamment le réseau virtuel, l’adresse IP publique, le nom d’utilisateur, les clés SSH, etc. Découvrez plus d’informations sur les [paramètres de l’interface de ligne de commande Resource Manager](azure-cli-arm-commands.md#azure-vm-commands-to-manage-your-azure-virtual-machines).

### <a name="create-a-vm-using-a-json-template"></a>Créer une machine virtuelle à l’aide d’un modèle JSON
Les modèles Azure Resource Manager sont des fichiers JSON (JavaScript Objet Notation) qui définissent l’environnement que vous souhaitez générer. Les modèles sont répartis parmi différents fournisseurs de ressources, tels que les fournisseurs de calcul ou réseau. Vous pouvez utiliser les modèles existants ou écrire les vôtres. Découvrez plus d’informations sur [l’utilisation des modèles et de Resource Manager](../azure-resource-manager/resource-group-overview.md).

Dans le fournisseur `Microsoft.Compute/virtualMachines` de votre modèle, vous disposez d’un nœud `storageProfile` qui contient les détails de configuration de votre machine virtuelle. Les deux principaux paramètres à modifier sont les URI `image` et `vhd` qui pointent vers votre image de disque personnalisée et le disque virtuel de votre nouvelle machine virtuelle. Voici un exemple de JSON pour l’utilisation d’une image de disque personnalisée :

```bash
"storageProfile": {
          "osDisk": {
            "name": "myVM",
            "osType": "Linux",
            "caching": "ReadWrite",
            "createOption": "FromImage",
            "image": {
              "uri": "https://mystorageaccount.blob.core.windows.net/myimages/mydisk.vhd"
            },
            "vhd": {
              "uri": "https://mystorageaccount.blob.core.windows.net/vhds/newvmname.vhd"
            }
          }
```

Vous pouvez utiliser [ce modèle existant pour créer une machine virtuelle à partir d’une image personnalisée](https://github.com/Azure/azure-quickstart-templates/tree/master/101-vm-from-user-image) ou obtenir plus d’informations sur [la création de vos propres modèles Azure Resource Manager](../azure-resource-manager/resource-group-authoring-templates.md). 

Une fois que vous avez configuré un modèle, vous pouvez créer vos machines virtuelles à l’aide de la commande `azure group deployment create` . Spécifiez l’URI de votre modèle JSON avec le paramètre `--template-uri` :

```bash
azure group deployment create --resource-group myResourceGroup
    --template-uri https://uri.to.template/mytemplate.json
```

Si vous disposez d’un fichier JSON stocké localement sur votre ordinateur, vous pouvez utiliser le paramètre `--template-file` à la place :

```bash
azure group deployment create --resource-group myResourceGroup
    --template-file /path/to/mytemplate.json
```


## <a name="next-steps"></a>Étapes suivantes
Après avoir préparé et chargé votre disque virtuel personnalisé, vous pouvez découvrir plus d’informations sur [l’utilisation de Resource Manager et des modèles](../azure-resource-manager/resource-group-overview.md). Vous pouvez également [ajouter un disque de données](virtual-machines-linux-add-disk.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json) à vos nouvelles machines virtuelles. Si vous avez besoin d’accéder à des applications qui s’exécutent sur vos machines virtuelles, veillez à [ouvrir les ports et points de terminaison](virtual-machines-linux-nsg-quickstart.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json).




<!--HONumber=Jan17_HO1-->


