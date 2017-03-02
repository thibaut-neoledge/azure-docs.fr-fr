---
title: "Sélectionner des images de VM Linux avec l’interface de ligne de commande Azure | Microsoft Docs"
description: "Découvrez comment déterminer l’éditeur, l’offre et la référence SKU pour des images quand vous créez une machine virtuelle Linux avec le modèle de déploiement de Resource Manager."
services: virtual-machines-linux
documentationcenter: 
author: squillace
manager: timlt
editor: 
tags: azure-resource-manager
ms.assetid: 7a858e38-4f17-4e8e-a28a-c7f801101721
ms.service: virtual-machines-linux
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: vm-linux
ms.workload: infrastructure
ms.date: 02/15/2017
ms.author: rasquill
translationtype: Human Translation
ms.sourcegitcommit: dc0797927d75a06117d72f52d7065927e65678b4
ms.openlocfilehash: 76d6288832b50d187c729afaee9118923517eb94
ms.lasthandoff: 02/22/2017


---
# <a name="select-linux-vm-images-with-the-azure-cli"></a>Sélectionner des images de VM Linux avec l’interface de ligne de commande Azure
Cette rubrique décrit comment rechercher des éditeurs, des offres, des références SKU et des versions pour chaque emplacement vers lequel vous souhaitez effectuer un déploiement. 


## <a name="use-azure-cli-20-preview"></a>Utilisez Azure CLI 2.0 (version préliminaire)

Une fois que vous avez [installé Azure CLI 2.0 (version préliminaire)](https://docs.microsoft.com/cli/azure/install-az-cli2), utilisez la commande `az vm image list` pour afficher une liste d’images de machine virtuelle populaires mise en cache. Par exemple, l’exemple de commande `az vm image list -o table` suivant affiche :

```
You are viewing an offline list of images, use --all to retrieve an up-to-date list
Offer          Publisher               Sku                 Urn                                                             UrnAlias             Version
-------------  ----------------------  ------------------  --------------------------------------------------------------  -------------------  ---------
WindowsServer  MicrosoftWindowsServer  2012-R2-Datacenter  MicrosoftWindowsServer:WindowsServer:2012-R2-Datacenter:latest  Win2012R2Datacenter  latest
WindowsServer  MicrosoftWindowsServer  2008-R2-SP1         MicrosoftWindowsServer:WindowsServer:2008-R2-SP1:latest         Win2008R2SP1         latest
WindowsServer  MicrosoftWindowsServer  2012-Datacenter     MicrosoftWindowsServer:WindowsServer:2012-Datacenter:latest     Win2012Datacenter    latest
UbuntuServer   Canonical               14.04.4-LTS         Canonical:UbuntuServer:14.04.4-LTS:latest                       UbuntuLTS            latest
CentOS         OpenLogic               7.2                 OpenLogic:CentOS:7.2:latest                                     CentOS               latest
openSUSE       SUSE                    13.2                SUSE:openSUSE:13.2:latest                                       openSUSE             latest
RHEL           RedHat                  7.2                 RedHat:RHEL:7.2:latest                                          RHEL                 latest
SLES           SUSE                    12-SP1              SUSE:SLES:12-SP1:latest                                         SLES                 latest
Debian         credativ                8                   credativ:Debian:8:latest                                        Debian               latest
CoreOS         CoreOS                  Stable              CoreOS:CoreOS:Stable:latest                                     CoreOS               latest
```

### <a name="finding-all-current-images"></a>Recherche de toutes les images actuelles

Pour obtenir la liste actuelle de toutes les images, utilisez la commande `az vm image list` avec l’option `--all`. Contrairement aux commandes Azure CLI 1.0, la commande `az vm image list --all` renvoie toutes les images dans **westus** par défaut (sauf si vous spécifiez un argument `--location` particulier), l’exécution de la commande `--all` prend donc un certain temps. Si vous souhaitez examiner cela de manière interactive, utilisez `az vm image list --all > allImages.json`, qui renvoie une liste de toutes les images actuellement disponibles sur Azure et les stocke sous forme de fichier pour une utilisation locale. 

Vous pouvez spécifier une des options disponibles pour limiter votre recherche à un emplacement, une offre, un éditeur ou une référence spécifique si vous en avez déjà à l’esprit. Si vous ne spécifiez pas d’emplacement, les valeurs de **westus** sont renvoyées.

### <a name="find-specific-images"></a>Recherche d’images spécifiques

Utilisez `az vm image list` avec un filtre pour trouver des informations spécifiques. Par exemple, ce qui suit affiche les **offres** qui sont disponibles pour **Debian** (n’oubliez pas que sans le switch `--all`, la recherche s’effectue uniquement dans le cache local des images courantes) :

```azurecli
az vm image list --offer Debian -o table --all
```

La sortie est identique à ce qui suit : 
```
Offer   Publisher   Sku   Urn                              Version
------  ---------   ---   -------------------------------  -------------
Debian  credativ    8     credativ:Debian:8:8.0.201701180  8.0.201701180

<list shortened for the example>
```

Vous pouvez appliquer des filtres similaires sur les options **--publisher** et **--sku**. Vous pouvez même effectuer des correspondances partielles dans un filtre, par exemple en cherchant **--offer Deb** pour rechercher toutes les images Debian ou **--publisher Micr** pour rechercher toutes les images publiées par Microsoft.

Si vous connaissez l’emplacement du déploiement, vous pouvez utiliser les résultats de la recherche d’images générale avec les commandes `az vm image list-skus`, `az vm image list-offers` et `az vm image list-publishers` pour trouver exactement ce que vous voulez et les emplacements de déploiement possibles. Par exemple, si dans l’exemple précédent vous savez que `credativ` a une offre Debian, vous pouvez utiliser `--location` et d’autres options pour trouver exactement ce que vous voulez. L’exemple suivant recherche une image Debian 8 dans **westeurope** :

```azurecli 
az vm image show -l westeurope -f debian -p credativ --skus 8 --version 8.0.201701180
```

et la sortie est la suivante :

```json
{
  "dataDiskImages": [],
  "id": "/Subscriptions/<guid>/Providers/Microsoft.Compute/Locations/westeurope/Publishers/credativ/ArtifactTypes/VMImage/Offers/debian/Skus/8/Versions/8.0.201701180",
  "location": "westeurope",
  "name": "8.0.201701180",
  "osDiskImage": {
    "operatingSystem": "Linux"
  },
  "plan": null,
  "tags": null
}
```

## <a name="use-azure-cli-10"></a>Utiliser Azure CLI 1.0 

> [!NOTE]
> Cet article explique comment parcourir et sélectionner des images de machine virtuelle, à l’aide d’une installation récente de l’interface Azure CLI 1.0 ou Azure PowerShell, qui prend en charge le modèle de déploiement Azure Resource Manager. Vous devez au préalable passer en mode Resource Manager. Avec l'interface CLI Azure, entrez ce mode en tapant `azure config mode arm`. 
> 

La façon la plus rapide de rechercher une image consiste à appeler la commande `azure vm image list` et passer l'emplacement, le nom de l'éditeur (qui ne respecte pas la casse) et une offre (si vous connaissez l'offre). La liste suivante en est un petit exemple (de nombreuses listes sont assez longues) si vous savez que « Canonical » est un éditeur pour l'offre « UbuntuServer ».

```azurecli
azure vm image list westus canonical ubuntuserver
info:    Executing command vm image list
warn:    The parameter --sku if specified will be ignored
+ Getting virtual machine image skus (Publisher:"canonical" Offer:"ubuntuserver" Location:"westus")
data:    Publisher  Offer         Sku                OS     Version          Location  Urn
data:    ---------  ------------  -----------------  -----  ---------------  --------  --------------------------------------------------------
data:    canonical  ubuntuserver  16.04.0-LTS        Linux  16.04.201604203  westus    canonical:ubuntuserver:16.04.0-LTS:16.04.201604203
data:    canonical  ubuntuserver  16.04.0-LTS        Linux  16.04.201605161  westus    canonical:ubuntuserver:16.04.0-LTS:16.04.201605161
data:    canonical  ubuntuserver  16.04.0-LTS        Linux  16.04.201606100  westus    canonical:ubuntuserver:16.04.0-LTS:16.04.201606100
data:    canonical  ubuntuserver  16.04.0-LTS        Linux  16.04.201606270  westus    canonical:ubuntuserver:16.04.0-LTS:16.04.201606270
data:    canonical  ubuntuserver  16.04.0-LTS        Linux  16.04.201607210  westus    canonical:ubuntuserver:16.04.0-LTS:16.04.201607210
data:    canonical  ubuntuserver  16.04.0-LTS        Linux  16.04.201608150  westus    canonical:ubuntuserver:16.04.0-LTS:16.04.201608150
data:    canonical  ubuntuserver  16.10-DAILY        Linux  16.10.201607220  westus    canonical:ubuntuserver:16.10-DAILY:16.10.201607220
data:    canonical  ubuntuserver  16.10-DAILY        Linux  16.10.201607230  westus    canonical:ubuntuserver:16.10-DAILY:16.10.201607230
data:    canonical  ubuntuserver  16.10-DAILY        Linux  16.10.201607240  westus    canonical:ubuntuserver:16.10-DAILY:16.10.201607240
```

La colonne **Urn** présentera le même format que celui que vous transmettez à `azure vm quick-create`.

Toutefois, il est fréquent que vous ignoriez encore ce qui est disponible. Dans ce cas, vous pouvez parcourir les images à l’aide de la commande `azure vm image list-publishers` et spécifier un emplacement de centre de données dans l’invite. Par exemple, la liste suivante répertorie tous les éditeurs d'images présents dans l'emplacement « West US » (passez l'argument location en utilisant des minuscules et en supprimant les espaces des emplacements standard).

```azurecli
azure vm image list-publishers
info:    Executing command vm image list-publishers
Location: westus
+ Getting virtual machine and/or extension image publishers (Location: "westus")
data:    Publisher                                       Location
data:    ----------------------------------------------  --------
data:    a10networks                                     westus  
data:    aiscaler-cache-control-ddos-and-url-rewriting-  westus  
data:    alertlogic                                      westus  
data:    AlertLogic.Extension                            westus  
```

Ces listes peuvent être assez longues. C’est pourquoi l’exemple de liste précédent est simplement un extrait de code. Supposons que vous ayez remarqué que « Canonical » est, en fait, un éditeur d'image présent dans l'emplacement « West US ». Vous pouvez maintenant rechercher ses offres en appelant `azure vm image list-offers` et en indiquant l’emplacement et l’éditeur aux invites, comme dans l’exemple suivant :

```azurecli
azure vm image list-offers
info:    Executing command vm image list-offers
Location: westus
Publisher: canonical
+ Getting virtual machine image offers (Publisher: "canonical" Location:"westus")
data:    Publisher  Offer                      Location
data:    ---------  -------------------------  --------
data:    canonical  Ubuntu15.04Snappy          westus
data:    canonical  Ubuntu15.04SnappyDocker    westus
data:    canonical  UbunturollingSnappy        westus
data:    canonical  UbuntuServer               westus
data:    canonical  Ubuntu_Snappy_Core         westus
data:    canonical  Ubuntu_Snappy_Core_Docker  westus
info:    vm image list-offers command OK
```

Nous savons maintenant que dans la région « West US », « Canonical »publie l'offre **UbuntuServer** sur Azure. Mais quelles sont les références SKU ? Pour obtenir ces valeurs, vous appelez `azure vm image list-skus` et répondez à l’invite en indiquant l’emplacement, l’éditeur et l’offre que vous avez détectée.

```azurecli
azure vm image list-skus
info:    Executing command vm image list-skus
Location: westus
Publisher: canonical
Offer: ubuntuserver
+ Getting virtual machine image skus (Publisher:"canonical" Offer:"ubuntuserver" Location:"westus")
data:    Publisher  Offer         sku                Location
data:    ---------  ------------  -----------------  --------
data:    canonical  ubuntuserver  12.04.2-LTS        westus
data:    canonical  ubuntuserver  12.04.3-LTS        westus
data:    canonical  ubuntuserver  12.04.4-LTS        westus
data:    canonical  ubuntuserver  12.04.5-DAILY-LTS  westus
data:    canonical  ubuntuserver  12.04.5-LTS        westus
data:    canonical  ubuntuserver  12.10              westus
data:    canonical  ubuntuserver  14.04-beta         westus
data:    canonical  ubuntuserver  14.04.0-LTS        westus
data:    canonical  ubuntuserver  14.04.1-LTS        westus
data:    canonical  ubuntuserver  14.04.2-LTS        westus
data:    canonical  ubuntuserver  14.04.3-LTS        westus
data:    canonical  ubuntuserver  14.04.4-DAILY-LTS  westus
data:    canonical  ubuntuserver  14.04.4-LTS        westus
data:    canonical  ubuntuserver  14.04.5-DAILY-LTS  westus
data:    canonical  ubuntuserver  14.04.5-LTS        westus
data:    canonical  ubuntuserver  14.10              westus
data:    canonical  ubuntuserver  14.10-beta         westus
data:    canonical  ubuntuserver  14.10-DAILY        westus
data:    canonical  ubuntuserver  15.04              westus
data:    canonical  ubuntuserver  15.04-beta         westus
data:    canonical  ubuntuserver  15.04-DAILY        westus
data:    canonical  ubuntuserver  15.10              westus
data:    canonical  ubuntuserver  15.10-alpha        westus
data:    canonical  ubuntuserver  15.10-beta         westus
data:    canonical  ubuntuserver  15.10-DAILY        westus
data:    canonical  ubuntuserver  16.04-alpha        westus
data:    canonical  ubuntuserver  16.04-beta         westus
data:    canonical  ubuntuserver  16.04.0-DAILY-LTS  westus
data:    canonical  ubuntuserver  16.04.0-LTS        westus
data:    canonical  ubuntuserver  16.10-DAILY        westus
info:    vm image list-skus command OK
```

Avec ces informations, vous pouvez maintenant trouver exactement l’image souhaitée en effectuant l’appel initial affiché en haut.

```azurecli
azure vm image list westus canonical ubuntuserver 16.04.0-LTS
info:    Executing command vm image list
+ Getting virtual machine images (Publisher:"canonical" Offer:"ubuntuserver" Sku: "16.04.0-LTS" Location:"westus")
data:    Publisher  Offer         Sku          OS     Version          Location  Urn
data:    ---------  ------------  -----------  -----  ---------------  --------  --------------------------------------------------
data:    canonical  ubuntuserver  16.04.0-LTS  Linux  16.04.201604203  westus    canonical:ubuntuserver:16.04.0-LTS:16.04.201604203
data:    canonical  ubuntuserver  16.04.0-LTS  Linux  16.04.201605161  westus    canonical:ubuntuserver:16.04.0-LTS:16.04.201605161
data:    canonical  ubuntuserver  16.04.0-LTS  Linux  16.04.201606100  westus    canonical:ubuntuserver:16.04.0-LTS:16.04.201606100
data:    canonical  ubuntuserver  16.04.0-LTS  Linux  16.04.201606270  westus    canonical:ubuntuserver:16.04.0-LTS:16.04.201606270
data:    canonical  ubuntuserver  16.04.0-LTS  Linux  16.04.201607210  westus    canonical:ubuntuserver:16.04.0-LTS:16.04.201607210
data:    canonical  ubuntuserver  16.04.0-LTS  Linux  16.04.201608150  westus    canonical:ubuntuserver:16.04.0-LTS:16.04.201608150
info:    vm image list command OK
```

## <a name="next-steps"></a>Étapes suivantes
Vous pouvez maintenant choisir précisément l'image que vous voulez utiliser. Pour créer rapidement une machine virtuelle en utilisant les informations d’URN que vous venez de trouver, ou pour utiliser un modèle avec ces informations d’URN, consultez [Création d'une machine virtuelle Linux à l’aide de l'interface de ligne de commande Azure (CLI)](virtual-machines-linux-quick-create-cli.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json).

