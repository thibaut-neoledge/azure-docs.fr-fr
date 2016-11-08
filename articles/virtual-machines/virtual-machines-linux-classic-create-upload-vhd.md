---
title: Création et téléchargement d'un VHD Linux | Microsoft Docs
description: Création et téléchargement d’un disque dur virtuel (VHD) Azure avec le modèle de déploiement classique contenant le système d'exploitation Linux.
services: virtual-machines-linux
documentationcenter: ''
author: iainfoulds
manager: timlt
editor: tysonn
tags: azure-service-management

ms.service: virtual-machines-linux
ms.workload: infrastructure-services
ms.tgt_pltfrm: vm-linux
ms.devlang: na
ms.topic: article
ms.date: 09/01/2016
ms.author: iainfou

---
# Création et téléchargement d'un disque dur virtuel contenant le système d'exploitation Linux
[!INCLUDE [learn-about-deployment-models](../../includes/learn-about-deployment-models-classic-include.md)]

Vous pouvez également [charger une image de disque personnalisé à l’aide d’Azure Resource Manager](virtual-machines-linux-upload-vhd.md).

Cet article vous montre comment créer et télécharger un disque dur virtuel pour que vous puissiez l'utiliser comme image personnelle pour créer des machines virtuelles dans Azure. Découvrez comment préparer le système d’exploitation afin de pouvoir l’utiliser pour créer plusieurs machines virtuelles basées sur cette image.

> [!NOTE]
> Si vous avez un moment, aidez-nous à améliorer la documentation relative aux machines virtuelles Azure Linux en répondant à cette [enquête rapide](https://aka.ms/linuxdocsurvey) concernant vos expériences. Chaque réponse nous aide à vous faciliter la tâche.
> 
> 

## Composants requis
Cet article part du principe que vous disposez des éléments suivants :

* **Un système d’exploitation Linux installé dans un fichier .vhd** : vous avez installé une [distribution Linux approuvée par Azure](virtual-machines-linux-endorsed-distros.md) (ou voir les [informations relatives aux distributions non approuvées](virtual-machines-linux-create-upload-generic.md)) sur un disque virtuel au format de disque dur virtuel (VHD). Plusieurs outils permettent de créer une machine virtuelle et un disque dur virtuel :
  * Installez et configurez [QEMU](https://en.wikibooks.org/wiki/QEMU/Installing_QEMU) ou [KVM](http://www.linux-kvm.org/page/RunningKVM), en veillant à utiliser VHD comme format d’image. Vous pouvez [convertir une image](https://en.wikibooks.org/wiki/QEMU/Images#Converting_image_formats) à l’aide de `qemu-img convert` si nécessaire.
  * Vous pouvez également utiliser Hyper-V [sur Windows 10](https://msdn.microsoft.com/virtualization/hyperv_on_windows/quick_start/walkthrough_install) ou [sur Windows Server 2012/2012 R2](https://technet.microsoft.com/library/hh846766.aspx).

> [!NOTE]
> Azure ne prend pas en charge le nouveau format VHDX. Lorsque vous créez une machine virtuelle, spécifiez le format de disque dur virtuel (VHD). Si nécessaire, vous pouvez convertir des disques VHDX au format VHD à l’aide de [`qemu-img convert`](https://en.wikibooks.org/wiki/QEMU/Images#Converting_image_formats) ou de l’applet de commande PowerShell [`Convert-VHD`](https://technet.microsoft.com/library/hh848454.aspx). De plus, Azure ne prend pas en charge le chargement de disques durs virtuels dynamiques. Vous devez convertir ces disques en disques durs virtuels statiques avant le chargement. Vous pouvez utiliser des outils tels que les [utilitaires de disque dur virtuel Azure pour GO](https://github.com/Microsoft/azure-vhd-utils-for-go) pour convertir les disques dynamiques au cours du processus de téléchargement vers Azure.
> 
> 

* **Interface de ligne de commande Azure** : installez [l’interface de ligne de commande Azure](../virtual-machines-command-line-tools.md) la plus récente pour charger le disque dur virtuel.

<a id="prepimage"> </a>

## Étape 1 : préparation de l'image pour le téléchargement
Azure prend en charge diverses distributions de Linux (voir [Distributions Linux approuvées](virtual-machines-linux-endorsed-distros.md)). Les articles suivants indiquent comment préparer les diverses distributions de Linux prises en charge dans Azure. Après avoir suivi toutes les étapes décrites dans les guides suivants, revenez ici une fois que vous disposez d’un fichier VHD prêt pour le chargement sur Azure :

* **[Distributions CentOS](virtual-machines-linux-create-upload-centos.md)**
* **[Debian Linux](virtual-machines-linux-debian-create-upload-vhd.md)**
* **[Oracle Linux](virtual-machines-linux-oracle-create-upload-vhd.md)**
* **[Red Hat Enterprise Linux](virtual-machines-linux-redhat-create-upload-vhd.md)**
* **[SLES et openSUSE](virtual-machines-linux-suse-create-upload-vhd.md)**
* **[Ubuntu](virtual-machines-linux-create-upload-ubuntu.md)**
* **[Autres - Distributions non approuvées](virtual-machines-linux-create-upload-generic.md)**

> [!NOTE]
> Le contrat de niveau de service de la plateforme Azure s’applique aux machines virtuelles exécutant le système d’exploitation Linux uniquement lorsqu’une des distributions reconnues est utilisée avec les détails de configuration définis sous « Versions prises en charge » dans [Linux dans les distributions approuvées par Azure](virtual-machines-linux-endorsed-distros.md). Toutes les distributions Linux de la galerie d'images Azure sont des distributions reconnues répondant à la configuration requise.
> 
> 

Consultez également les **[notes d’installation Linux](virtual-machines-linux-create-upload-generic.md#general-linux-installation-notes)** pour obtenir d’autres conseils généraux sur la préparation d’images Linux pour Azure.

<a id="connect"> </a>

## Étape 2 : préparation de la connexion à Azure
Assurez-vous que vous utilisez l’interface de ligne de commande Azure dans le modèle de déploiement Classic (`azure config mode asm`), puis connectez-vous à votre compte :

```
azure login
```


<a id="upload"> </a>

## Étape 3 : chargement de l’image dans Azure
Vous avez besoin d’un compte de stockage vers lequel charger votre fichier de disque dur virtuel. Vous pouvez choisir un compte de stockage existant ou [en créer un](../storage/storage-create-storage-account.md).

Utilisez l’interface de ligne de commande Azure pour charger l’image, à l’aide de la commande suivante :

```bash
azure vm image create <ImageName> `
    --blob-url <BlobStorageURL>/<YourImagesFolder>/<VHDName> `
    --os Linux <PathToVHDFile>
```

Dans l’exemple précédent :

* **BlobStorageURL** correspond à l’URL du compte de stockage que vous envisagez d’utiliser.
* **YourImagesFolder** est le conteneur au sein du stockage d’objets blob dans lequel vous souhaitez stocker vos images.
* **VHDName** est l’étiquette affichée dans le portail pour identifier le disque dur virtuel.
* **PathToVHDFile** est le chemin d’accès complet et le nom du fichier .vhd sur votre machine.

L’exemple suivant est complet :

```bash
azure vm image create UbuntuLTS `
    --blob-url https://teststorage.blob.core.windows.net/vhds/UbuntuLTS.vhd `
    --os Linux /home/ahmet/UbuntuLTS.vhd
```

## Étape 4 : créer une machine virtuelle à partir de l’image
Vous créez une machine virtuelle à l’aide de `azure vm create`, de la même façon qu’une machine virtuelle standard. Spécifiez le nom que vous avez donné à votre image à l’étape précédente. Dans l’exemple suivant, nous utilisons le nom d’image **UbuntuLTS** attribué à l’étape précédente :

```bash
azure vm create --userName ops --password P@ssw0rd! --vm-size Small --ssh `
    --location "West US" "DeployedUbuntu" UbuntuLTS
```

Pour créer vos machines virtuelles, fournissez vos propres nom d’utilisateur et mot de passe, emplacement, nom DNS et nom d’image.

## Étapes suivantes
Pour plus d’informations, voir [référence CLI Azure pour le modèle de déploiement Azure Classic](../virtual-machines-command-line-tools.md).

[Step 1: Prepare the image to be uploaded]: #prepimage
[Step 2: Prepare the connection to Azure]: #connect
[Step 3: Upload the image to Azure]: #upload

<!---HONumber=AcomDC_0914_2016-->