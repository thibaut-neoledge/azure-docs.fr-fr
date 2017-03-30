---
title: "Exemple de script Azure CLI - Créer un hôte Docker | Microsoft Docs"
description: "Exemple de script Azure CLI - Créer un hôte Docker"
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
ms.date: 03/15/2017
ms.author: nepeters
translationtype: Human Translation
ms.sourcegitcommit: 0d8472cb3b0d891d2b184621d62830d1ccd5e2e7
ms.openlocfilehash: a82b96bd0a669d9798467c837bf34133b3f9cd0c
ms.lasthandoff: 03/21/2017

---

# <a name="create-a-vm-with-docker"></a>Créer une machine virtuelle avec Docker

Ce script crée une machine virtuelle avec Docker activé et démarre un conteneur Docker exécutant NGINX. Une fois que vous avez exécuté le script, vous pouvez accéder au serveur web NGINX par le biais du nom de domaine complet de la machine virtuelle Azure. 

Si nécessaire, installez l’interface Azure CLI en suivant les instructions du [Guide d’installation Azure CLI](https://docs.microsoft.com/cli/azure/install-azure-cli), puis exécutez `az login` pour créer une connexion avec Azure.

Cet exemple fonctionne dans une interface d’interpréteur de commandes Bash. Pour en savoir plus les options d’exécution de scripts Azure CLI dans le client Windows, consultez la page [Running the Azure CLI in Windows (Exécution d’Azure CLI dans Windows)](../virtual-machines-windows-cli-options.md).

## <a name="sample-script"></a>Exemple de script

[!code-azurecli[principal](../../../cli_scripts/virtual-machine/create-docker-host/create-docker-host.sh "Hôte Docker")]

## <a name="clean-up-deployment"></a>Nettoyer le déploiement 

Exécutez la commande suivante pour supprimer le groupe de ressources, la machine virtuelle et toutes les ressources associées.

```azurecli
az group delete --name myResourceGroup
```

## <a name="script-explanation"></a>Explication du script

Ce script a recours aux commandes suivantes pour créer le déploiement. Chaque élément du tableau renvoie à une documentation spécifique.

| Commande | Remarques |
|---|---|
| [az group create](https://docs.microsoft.com/cli/azure/group#create) | Crée un groupe de ressources dans lequel toutes les ressources sont stockées. |
| [az vm create](https://docs.microsoft.com/cli/azure/vm#create) | Crée la machine virtuelle et l’associe à la carte réseau, au réseau virtuel, au sous-réseau et au groupe de sécurité réseau. Cette commande spécifie également l’image de machine virtuelle à utiliser ainsi que les informations d’identification d’administration.  |
| [az vm open-port](https://docs.microsoft.com/cli/azure/vm#open-port) | Crée une règle de groupe de sécurité réseau visant à autoriser le trafic entrant. Dans cet exemple, le port 80 est ouvert pour le trafic HTTP. |
| [azure vm extension set](https://docs.microsoft.com/cli/azure/vm/extension#set) | Ajoute et exécute une extension de machine virtuelle sur une machine virtuelle. Dans cet exemple, l’extension de machine virtuelle Docker est utilisée pour configurer un hôte Docker.|
| [az group delete](https://docs.microsoft.com/cli/azure/vm/extension#set) | Supprime un groupe de ressources, y compris toutes les ressources imbriquées. |

## <a name="next-steps"></a>Étapes suivantes

Pour plus d’informations sur l’interface Azure CLI, consultez la [documentation relative à l’interface Azure CLI](https://docs.microsoft.com/cli/azure/overview).

Vous trouverez des exemples supplémentaires de scripts CLI de machine virtuelle dans la [documentation relative aux machines virtuelles Linux Azure](../virtual-machines-linux-cli-samples.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json).

