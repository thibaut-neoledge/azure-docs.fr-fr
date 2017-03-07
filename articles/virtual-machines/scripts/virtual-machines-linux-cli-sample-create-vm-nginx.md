---
title: "Exemple de script Azure CLI - Création d’une machine virtuelle Linux avec NGINX | Microsoft Docs"
description: "Exemple de script Azure CLI - Création d’une machine virtuelle Linux avec NGINX"
services: virtual-machines-linux
documentationcenter: virtual-machines
author: neilpeterson
manager: timlt
editor: tysonn
tags: azure-service-management
ms.assetid: 
ms.service: virtual-machines-linux
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: vm-linux
ms.workload: infrastructure
ms.date: 02/27/2017
ms.author: nepeters
translationtype: Human Translation
ms.sourcegitcommit: 07d91dfa905d22334bd051f9d5f3d936d38efc88
ms.openlocfilehash: 94e2593271bd7828aab4dcefc0d0df47086e47ad
ms.lasthandoff: 02/28/2017

---

# <a name="create-a-vm-with-nginx"></a>Créer une machine virtuelle avec NGINX

Ce script crée une machine virtuelle Azure, puis utilise l’extension du script personnalisé de machine virtuelle Azure pour installer NGINX. Une fois le script exécuté, un site web de démonstration est accessible sur l’adresse IP publique de la machine virtuelle.

Avant d’exécuter ce script, vérifiez qu’une connexion avec Azure a bien été créée à l’aide de la commande `az login`.

Cet exemple fonctionne dans un interpréteur de commandes Bash. Pour en savoir plus les options d’exécution de scripts Azure CLI sur un client Windows, consultez la page [Using the Azure CLI on Windows](../virtual-machines-windows-cli-options.md) (Utilisation de l’interface Azure CLI dans Windows).

## <a name="sample-script"></a>Exemple de script

Le script suivant crée la machine virtuelle et appelle l’extension du script personnalisé.

[!code-azurecli[main](../../../cli_scripts/virtual-machine/create-vm-nginx/create-vm-nginx.sh "Création rapide de machine virtuelle")]

## <a name="custom-script-extension"></a>Extension de script personnalisé

L’extension du script personnalisé copie ce script sur la machine virtuelle. Le script est ensuite exécuté pour installer et configurer le serveur web NGINX. 

```bash
#!/bin/bash
apt-get -y update

# install NGINX
apt-get -y install nginx
```

## <a name="clean-up-deployment"></a>Nettoyer le déploiement 

Une fois l’exemple de script exécuté, la commande suivante peut être utilisée pour la suppression du groupe de ressources, de la machine virtuelle et de toutes les ressources associées.

```azurecli
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

Vous trouverez des exemples supplémentaires de scripts CLI de machine virtuelle dans la [documentation relative aux machines virtuelles Linux Azure](../virtual-machines-linux-cli-samples.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json).

