---
title: "Exemple de script Azure CLI - Création d’une machine virtuelle Linux avec NGINX | Microsoft Docs"
description: "Exemple de script Azure CLI - Création d’une machine virtuelle Linux avec NGINX"
services: virtual-machines-linux
documentationcenter: virtual-machines
author: neilpeterson
manager: timlt
editor: tysonn
tags: azure-resource-manager
ms.assetid: 
ms.service: virtual-machines-linux
ms.devlang: azurecli
ms.topic: sample
ms.tgt_pltfrm: vm-linux
ms.workload: infrastructure
ms.date: 02/27/2017
ms.author: nepeters
ms.custom: mvc
ms.translationtype: Human Translation
ms.sourcegitcommit: 9568210d4df6cfcf5b89ba8154a11ad9322fa9cc
ms.openlocfilehash: 12b4fda8a8805d49ad1318af3367a53e97ddf48b
ms.contentlocale: fr-fr
ms.lasthandoff: 05/15/2017

---

# <a name="create-a-vm-with-nginx"></a>Créer une machine virtuelle avec NGINX

Ce script crée une machine virtuelle Azure et utilise l’extension du script personnalisé de machine virtuelle Azure pour installer NGINX. Une fois que vous avez exécuté le script, vous pouvez accéder à un site web de démonstration sur l’adresse IP publique de la machine virtuelle.

[!INCLUDE [sample-cli-install](../../../includes/sample-cli-install.md)]

[!INCLUDE [quickstarts-free-trial-note](../../../includes/quickstarts-free-trial-note.md)]

## <a name="sample-script"></a>Exemple de script

[!code-azurecli-interactive[main](../../../cli_scripts/virtual-machine/create-vm-nginx/create-vm-nginx.sh "Création rapide de machine virtuelle")]

## <a name="custom-script-extension"></a>Extension de script personnalisé

L’extension du script personnalisé copie ce script sur la machine virtuelle. Le script est ensuite exécuté pour installer et configurer le serveur web NGINX. 

```bash
#!/bin/bash

# update package source
apt-get -y update

# install NGINX
apt-get -y install nginx
```

## <a name="clean-up-deployment"></a>Nettoyer le déploiement 

Exécutez la commande suivante pour supprimer le groupe de ressources, la machine virtuelle et toutes les ressources associées.

```azurecli-interactive 
az group delete --name myResourceGroup
```

## <a name="script-explanation"></a>Explication du script

Ce script utilise les commandes suivantes pour créer un groupe de ressources, une machine virtuelle et toutes les ressources associées. Chaque commande du tableau renvoie à une documentation spécifique.

| Commande | Remarques |
|---|---|
| [az group create](https://docs.microsoft.com/cli/azure/group#create) | Crée un groupe de ressources dans lequel toutes les ressources sont stockées. |
| [az vm create](https://docs.microsoft.com/cli/azure/vm#create) | Crée la machine virtuelle. Cette commande spécifie également l’image de machine virtuelle à utiliser ainsi que les informations d’identification d’administration.  |
| [az vm open-port](https://docs.microsoft.com/cli/azure/network/nsg/rule#create) | Crée une règle de groupe de sécurité réseau visant à autoriser le trafic entrant. Dans cet exemple, le port 80 est ouvert pour le trafic HTTP. |
| [azure vm extension set](https://docs.microsoft.com/cli/azure/vm/extension#set) | Ajoute et exécute une extension de machine virtuelle sur une machine virtuelle. Dans cet exemple, l’extension de script personnalisé sert à installer NGINX.|
| [az group delete](https://docs.microsoft.com/cli/azure/vm/extension#set) | Supprime un groupe de ressources, y compris toutes les ressources imbriquées. |

## <a name="next-steps"></a>Étapes suivantes

Pour plus d’informations sur l’interface Azure CLI, consultez la [documentation relative à l’interface Azure CLI](https://docs.microsoft.com/cli/azure/overview).

Vous trouverez des exemples supplémentaires de scripts CLI de machine virtuelle dans la [documentation relative aux machines virtuelles Linux Azure](../linux/cli-samples.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json).

