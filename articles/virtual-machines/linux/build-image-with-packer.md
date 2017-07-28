---
title: "Comment créer des images de machines virtuelles Azure Linux avec Packer | Microsoft Docs"
description: "Découvrez comment utiliser Packer pour créer des images de machines virtuelles Linux dans Azure"
services: virtual-machines-linux
documentationcenter: virtual-machines
author: iainfoulds
manager: timlt
editor: tysonn
tags: azure-resource-manager
ms.assetid: 
ms.service: virtual-machines-linux
ms.devlang: azurecli
ms.topic: article
ms.tgt_pltfrm: vm-linux
ms.workload: infrastructure
ms.date: 06/12/2017
ms.author: iainfou
ms.translationtype: Human Translation
ms.sourcegitcommit: db18dd24a1d10a836d07c3ab1925a8e59371051f
ms.openlocfilehash: fa30f7b9aebf3b9a3fb1e037983e8460aa76442e
ms.contentlocale: fr-fr
ms.lasthandoff: 06/15/2017

---

# <a name="how-to-use-packer-to-create-linux-virtual-machine-images-in-azure"></a>Comment utiliser Packer pour créer des images de machines virtuelles Linux dans Azure
Chaque machine virtuelle dans Azure est créée à partir d’une image qui définit la distribution Linux et la version du système d’exploitation. Les images peuvent inclure des configurations et des applications pré-installées. La Place de marché Microsoft Azure fournit de nombreuses images internes et de tiers pour les distributions et environnements d’application les plus courants. Vous pouvez également créer vos propres images personnalisées selon vos besoins. Cet article explique comment utiliser l’outil open source [Packer](https://www.packer.io/) pour définir et générer des images personnalisées dans Azure.


## <a name="create-supporting-azure-resources"></a>Création de ressources de prise en charge Azure
Pendant le processus de génération, Packer crée des ressources Azure temporaires lorsqu’il génère la machine virtuelle source. Pour capturer cette machine virtuelle source afin de l’utiliser en tant qu’image, vous devez définir un groupe de ressources et un compte de stockage. La sortie du processus de génération de Packer est stockée dans ce groupe de ressources et ce compte de stockage.

Tout d’abord, créez un groupe de ressources avec la commande [az group create](/cli/azure/group#create). L’exemple suivant crée un groupe de ressources nommé *myResourceGroup* à l’emplacement *eastus* :

```azurecli
az group create -n myResourceGroup -l eastus
```

Créez ensuite un compte de stockage avec la commande [az storage account create](/cli/azure/storage/account#create). Les noms des comptes de stockage doivent être uniques et comporter entre 3 et 24 caractères (uniquement des lettres minuscules et des chiffres). L’exemple qui suit permet de créer un compte de stockage nommé *mystorageaccount* :

```azurecli
az storage account create \
    --resource-group myResourceGroup \
    --name mystorageaccount \
    --sku Standard_LRS
```


## <a name="create-azure-credentials"></a>Création des informations d’identification Azure
Packer s’authentifie auprès d’Azure à l’aide d’un principal de service. Un principal de service Azure est une identité de sécurité que vous pouvez utiliser avec des applications, des services et des outils d’automatisation comme Packer. Vous contrôlez et définissez les opérations que le principal de service est autorisé à effectuer dans Azure.

Créez un principal de service avec la commande [az ad sp create-for-rbac](/cli/azure/ad/sp#create-for-rbac) et affichez les informations d’identification nécessaires à Packer :

```azurecli
az ad sp create-for-rbac --query [appId,password,tenant]
```

Voici un exemple de sortie issue des commandes précédentes :

```azurecli
"f5b6a5cf-fbdf-4a9f-b3b8-3c2cd00225a4",
"0e760437-bf34-4aad-9f8d-870be799c55d",
"72f988bf-86f1-41af-91ab-2d7cd011db47"
```

Pour vous authentifier auprès d’Azure, vous devez également obtenir votre ID d’abonnement Azure à l’aide de la commande [az account show](/cli/azure/account#show) :

```azurecli
az account show --query [id] --output tsv
```

Vous utiliserez la sortie de ces deux commandes à l’étape suivante.


## <a name="define-packer-template"></a>Définition du modèle Packer
Pour générer les images, vous devez créer un modèle en tant que fichier JSON. Dans le modèle, vous définissez des générateurs et des fournisseurs pour mener à bien le processus de génération réel. Packer possède un [fournisseur pour Azure](https://www.packer.io/docs/builders/azure.html) qui vous permet de définir des ressources Azure, telles que les informations d’identification du principal de service créées à l’étape précédente.

Créez un fichier nommé *ubuntu.json* et collez le contenu suivant : Saisissez vos propres valeurs comme suit :

| Paramètre       | Emplacement |
|-----------------|----------------------------------------------------|
| *client_id*      | Première ligne de la sortie de la commande create `az ad sp` - *appId* |
| *client_secret*  | Deuxième ligne de la sortie de la commande create `az ad sp` - *password* |
| *tenant_id*      | Troisième ligne de la sortie de la commande create `az ad sp` - *tenant* |
| *subscription_id* | Sortie de la commande `az account show` |
| *storage_account* | Nom que vous avez spécifié dans `az storage account create` |


```json
{
  "builders": [{
    "type": "azure-arm",

    "client_id": "f5b6a5cf-fbdf-4a9f-b3b8-3c2cd00225a4",
    "client_secret": "0e760437-bf34-4aad-9f8d-870be799c55d",
    "tenant_id": "72f988bf-86f1-41af-91ab-2d7cd011db47",
    "subscription_id": "xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxx",

    "resource_group_name": "myResourceGroup",
    "storage_account": "mystorageaccount",

    "capture_container_name": "images",
    "capture_name_prefix": "packer",

    "os_type": "Linux",
    "image_publisher": "Canonical",
    "image_offer": "UbuntuServer",
    "image_sku": "16.04.0-LTS",

    "azure_tags": {
        "dept": "Engineering",
        "task": "Image deployment"
    },

    "location": "East US",
    "vm_size": "Standard_DS2_v2"
  }],
  "provisioners": [{
    "execute_command": "chmod +x {{ .Path }}; {{ .Vars }} sudo -E sh '{{ .Path }}'",
    "inline": [
      "apt-get update",
      "apt-get upgrade -y",
      "apt-get -y install nginx",

      "/usr/sbin/waagent -force -deprovision+user && export HISTSIZE=0 && sync"
    ],
    "inline_shebang": "/bin/sh -x",
    "type": "shell"
  }]
}
```

Ce modèle génère une image Ubuntu 16.04 LTS, installe NGINX, puis déprovisionne la machine virtuelle.

> [!NOTE]
> Si vous développez ce modèle pour approvisionner les informations d’identification utilisateur, définissez la commande du fournisseur qui déprovisionne l’agent Azure de sorte qu’elle indique `-deprovision` et non `deprovision+user`.
> L’indicateur `+user` supprime tous les comptes d’utilisateur de la machine virtuelle source.


## <a name="build-packer-image"></a>Génération de l’image Packer
Si Packer n’est pas encore installé sur votre ordinateur local, [suivez les instructions d’installation de Packer](https://www.packer.io/docs/install/index.html).

Générez l’image en spécifiant votre fichier de modèle Packer comme suit :

```bash
./packer build ubuntu.json
```

Voici un exemple de sortie issue des commandes précédentes :

```bash
azure-arm output will be in this color.

==> azure-arm: Running builder ...
    azure-arm: Creating Azure Resource Manager (ARM) client ...
==> azure-arm: Creating resource group ...
==> azure-arm:  -> ResourceGroupName : 'packer-Resource-Group-hlz1xtcy8n'
==> azure-arm:  -> Location          : 'East US'
==> azure-arm:  -> Tags              :
==> azure-arm:  ->> dept : engineering
==> azure-arm:  ->> task : image deployment
==> azure-arm: Validating deployment template ...
==> azure-arm:  -> ResourceGroupName : 'packer-Resource-Group-hlz1xtcy8n'
==> azure-arm:  -> DeploymentName    : 'pkrdphlz1xtcy8n'
==> azure-arm: Deploying deployment template ...
==> azure-arm:  -> ResourceGroupName : 'packer-Resource-Group-hlz1xtcy8n'
==> azure-arm:  -> DeploymentName    : 'pkrdphlz1xtcy8n'
==> azure-arm: Getting the VM's IP address ...
==> azure-arm:  -> ResourceGroupName   : 'packer-Resource-Group-hlz1xtcy8n'
==> azure-arm:  -> PublicIPAddressName : 'packerPublicIP'
==> azure-arm:  -> NicName             : 'packerNic'
==> azure-arm:  -> Network Connection  : 'PublicEndpoint'
==> azure-arm:  -> IP Address          : '13.90.250.248'
==> azure-arm: Waiting for SSH to become available...
==> azure-arm: Connected to SSH!
==> azure-arm: Provisioning with shell script: /tmp/packer-shell529418469
    azure-arm: Get:1 http://security.ubuntu.com/ubuntu xenial-security InRelease [102 kB]
    azure-arm: Hit:2 http://azure.archive.ubuntu.com/ubuntu xenial InRelease
    azure-arm: Get:3 http://azure.archive.ubuntu.com/ubuntu xenial-updates InRelease [102 kB]
    azure-arm: Get:4 http://azure.archive.ubuntu.com/ubuntu xenial-backports InRelease [102 kB]
    [snip]
    azure-arm: WARNING! The waagent service will be stopped.
    azure-arm: WARNING! Cached DHCP leases will be deleted.
    azure-arm: WARNING! root password will be disabled. You will not be able to login as root.
    azure-arm: WARNING! /etc/resolvconf/resolv.conf.d/tail and /etc/resolvconf/resolv.conf.d/original will be deleted.
    azure-arm: WARNING! packer account and entire home directory will be deleted.
==> azure-arm: Querying the machine's properties ...
==> azure-arm:  -> ResourceGroupName : 'packer-Resource-Group-hlz1xtcy8n'
==> azure-arm:  -> ComputeName       : 'pkrvmhlz1xtcy8n'
==> azure-arm:  -> OS Disk           : 'https://mystorageaccount.blob.core.windows.net/images/pkroshlz1xtcy8n.vhd'
==> azure-arm: Powering off machine ...
==> azure-arm:  -> ResourceGroupName : 'packer-Resource-Group-hlz1xtcy8n'
==> azure-arm:  -> ComputeName       : 'pkrvmhlz1xtcy8n'
==> azure-arm: Capturing image ...
==> azure-arm:  -> ResourceGroupName : 'packer-Resource-Group-hlz1xtcy8n'
==> azure-arm:  -> ComputeName       : 'pkrvmhlz1xtcy8n'
==> azure-arm: Deleting resource group ...
==> azure-arm:  -> ResourceGroupName : 'packer-Resource-Group-hlz1xtcy8n'
==> azure-arm: Deleting the temporary OS disk ...
==> azure-arm:  -> OS Disk : 'https://mystorageaccount.blob.core.windows.net/images/pkroshlz1xtcy8n.vhd'
Build 'azure-arm' finished.

==> Builds finished. The artifacts of successful builds are:
--> azure-arm: Azure.ResourceManagement.VMImage:

StorageAccountLocation: eastus
OSDiskUri: https://mystorageaccount.blob.core.windows.net/system/Microsoft.Compute/Images/images/packer-osDisk.643f37d7-5a5d-43bf-96ed-2d598ada6e65.vhd
OSDiskUriReadOnlySas: https://mystorageaccount.blob.core.windows.net/system/Microsoft.Compute/Images/images/packer-osDisk.643f37d7-5a5d-43bf-96ed-2d598ada6e65.vhd?se=2017-07-08T20%3A57%3A53Z&sig=yl1yl3I2gKnO0I%2B7paw%2FQzKT5dawf5i%2B
LPmATMt5ot4%3D&sp=r&sr=b&sv=2015-02-21
TemplateUri: https://mystorageaccount.blob.core.windows.net/system/Microsoft.Compute/Images/images/packer-vmTemplate.643f37d7-5a5d-43bf-96ed-2d598ada6e65.json
TemplateUriReadOnlySas: https://mystorageaccount.blob.core.windows.net/system/Microsoft.Compute/Images/images/packer-vmTemplate.643f37d7-5a5d-43bf-96ed-2d598ada6e65.json?se=2017-07-08T20%3A57%3A53Z&sig=GB1iSl0hhw1ZYG4nl%2BCfR9WEaquCF
OEhNtKlvp%2B5TdE%3D&sp=r&sr=b&sv=2015-02-21
```


## <a name="create-azure-image"></a>Création de l’image Azure
La sortie du processus de génération de Packer est un disque dur virtuel (VHD, virtual hard disk) dans le compte de stockage spécifié. Créez une image Azure à partir de ce VHD avec la commande [az image create](/cli/azure/image#create) et spécifiez le chemin d’accès `OSDiskUri` indiqué à la fin de la sortie de la génération de Packer. L’exemple suivant permet de créer une image nommée `myImage` :

```azurecli
az image create \
    --resource-group myResourceGroup \
    --name myImage \
    --os-type linux \
    --source https://mystorageaccount.blob.core.windows.net/system/Microsoft.Compute/Images/images/packer-osDisk.643f37d7-5a5d-43bf-96ed-2d598ada6e65.vhd
```

Cette image peut être utilisée pour créer des machines virtuelles dans votre abonnement Azure. Vous n’êtes pas obligé de créer une machine virtuelle dans le même groupe de ressources que votre image source.


## <a name="create-vm-from-azure-image"></a>Création d’une machine virtuelle à partir d’une image Azure
Vous pouvez à présent créer une machine virtuelle à partir de votre image à l’aide de la commande [az vm create](/cli/azure/vm#create). Spécifiez l’image que vous avez créée avec le paramètre `--image`. L’exemple suivant crée une machine virtuelle nommée *myVM* à partir de *myImage* et génère des clés SSH si elles n’existent pas déjà :

```azurecli
az vm create \
    --resource-group myResourceGroup \
    --name myVM \
    --image myImage \
    --admin-username azureuser \
    --generate-ssh-keys
```

La création de la machine virtuelle ne nécessite que quelques minutes. Une fois la machine virtuelle créée, notez la valeur de `publicIpAddress` qui s’affiche dans l’interface Azure CLI. Cette adresse est utilisée pour accéder au site NGINX à l’aide d’un navigateur web.

Pour autoriser le trafic web à accéder à votre machine virtuelle, ouvrez le port 80 à partir d’Internet à l’aide de la commande [az vm open-port](/cli/azure/vm#open-port) :

```azurecli
az vm open-port \
    --resource-group myResourceGroup \
    --name myVM \
    --port 80
```

## <a name="test-vm-and-nginx"></a>Test de la machine virtuelle et de NGINX
Vous pouvez maintenant ouvrir un navigateur web et entrer `http://publicIpAddress` dans la barre d’adresse. Indiquez votre propre adresse IP publique à partir du processus de création de la machine virtuelle. La page NGINX par défaut s’affiche comme dans l’exemple suivant :

![Site par défaut NGINX](./media/build-image-with-packer/nginx.png) 


## <a name="next-steps"></a>Étapes suivantes
Dans cet exemple, NGINX était déjà installé et vous avez utilisé Packer pour créer une image de machine virtuelle. Vous pouvez utiliser cette image de machine virtuelle avec les flux de travail de déploiement existants, par exemple pour déployer votre application sur les machines virtuelles créées à partir de l’image avec Ansible, Chef ou Puppet.

Pour obtenir d’autres exemples de modèles Packer pour d’autres distributions Linux, consultez [ce référentiel GitHub](https://github.com/hashicorp/packer/tree/master/examples/azure).
