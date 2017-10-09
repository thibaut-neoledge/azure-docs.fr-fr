---
title: "Créer une machine virtuelle Linux à l’aide d’Azure CLI dans Azure Stack | Microsoft Docs"
description: "Créez une machine virtuelle Linux avec l’interface de ligne de commande dans Azure Stack."
services: azure-stack
documentationcenter: 
author: SnehaGunda
manager: byronr
editor: 
ms.assetid: 
ms.service: azure-stack
ms.workload: na
pms.tgt_pltfrm: na
ms.devlang: na
ms.topic: quickstart
ms.date: 09/25/2017
ms.author: sngun
ms.custom: mvc
ms.translationtype: HT
ms.sourcegitcommit: 44e9d992de3126bf989e69e39c343de50d592792
ms.openlocfilehash: d868ce59fcb09bae8c111da3892af33317003474
ms.contentlocale: fr-fr
ms.lasthandoff: 09/25/2017

---

# <a name="create-a-linux-virtual-machine-by-using-azure-cli-in-azure-stack"></a>Créer une machine virtuelle Linux à l’aide d’Azure CLI dans Azure Stack

*S’applique à : systèmes intégrés Azure Stack*

Azure CLI permet de créer et de gérer des ressources Azure Stack à partir de la ligne de commande. Ce guide explique de manière détaillée comment utiliser Azure CLI pour créer une machine virtuelle Linux dans Azure Stack.  Une fois la machine virtuelle créée, un serveur web est installé et le port 80 est ouvert pour autoriser le trafic web.

Avant de commencer, vérifiez que votre opérateur Azure Stack a ajouté l’image « Ubuntu Server 16.04 LTS » à la Place de Marché Azure Stack.  

Azure Stack nécessite une version spécifique d’Azure CLI pour créer et gérer les ressources. Si Azure CLI n’est pas configuré pour Azure Stack, suivez les étapes permettant [d’installer et de configurer Azure CLI](azure-stack-connect-cli.md).

Pour finir, vous devez créer une clé SSH publique nommée id_rsa.pub dans le répertoire .ssh de votre profil utilisateur Windows. Pour plus d’informations sur la création de clés SSH, consultez [Création de clés SSH sur Windows](../../virtual-machines/linux/ssh-from-windows.md). 


## <a name="create-a-resource-group"></a>Créer un groupe de ressources

Un groupe de ressources est un conteneur logique dans lequel les ressources Azure Stack sont déployées et gérées. Utilisez la commande [az group create](/cli/azure/group#create) pour créer un groupe de ressources. Nous avons affecté des valeurs à toutes les variables de ce document. Vous pouvez les utiliser en l’état ou affecter une valeur différente. L’exemple suivant crée un groupe de ressources nommé myResourceGroup à l’emplacement local.

```cli
az group create --name myResourceGroup --location local
```

## <a name="create-virtual-machine"></a>Create virtual machine

Créez une machine virtuelle avec la commande [az vm create](/cli/azure/vm#create). L’exemple suivant crée une machine virtuelle nommée myVM. Cet exemple utilise Demouser comme nom d’utilisateur administratif et Demouser@123 comme mot de passe. Mettez à jour ces valeurs avec quelque chose d’approprié pour votre environnement. Ces valeurs sont nécessaires lors de la connexion à la machine virtuelle.

```cli
az vm create \
  --resource-group "myResourceGroup" \
  --name "myVM" \
  --image "UbuntuLTS" \
  --admin-username "Demouser" \
  --admin-password "Demouser@123" \
  --use-unmanaged-disk \
  --location local
```

Une fois terminé, la commande affiche les paramètres de votre machine virtuelle.  Notez le paramètre *PublicIPAddress*. Vous l’utiliserez pour vous connecter à votre machine virtuelle et pour la gérer.

## <a name="open-port-80-for-web-traffic"></a>Ouvrez le port 80 pour le trafic web

Par défaut, seules les connexions SSH sont autorisées dans les machines virtuelles Linux déployées dans Azure. Si cet ordinateur virtuel doit être un serveur web, vous devez ouvrir le port 80 à partir d’Internet. Utilisez la commande [az vm open-port](/cli/azure/vm#open-port) pour ouvrir le port souhaité.

```cli
az vm open-port --port 80 --resource-group myResourceGroup --name myVM
```

## <a name="ssh-into-your-vm"></a>Se connecter avec SSH à votre machine virtuelle

Sur un système avec SSH installé, utilisez la commande suivante pour vous connecter à la machine virtuelle. Si vous travaillez sur Windows, [Putty](http://www.putty.org/) peut être utilisé pour créer la connexion. Veillez spécifier l’adresse IP publique correcte de votre machine virtuelle. Dans l’exemple ci-dessus, l’adresse IP était 192.168.102.36.

```bash
ssh <publicIpAddress>
```

## <a name="install-nginx"></a>Installer NGINX

Utilisez le script Bash suivant pour mettre à jour les sources de package et installer le dernier package NGINX. 

```bash 
#!/bin/bash

# update package source
apt-get -y update

# install NGINX
apt-get -y install nginx
```

## <a name="view-the-nginx-welcome-page"></a>Afficher la page d’accueil NGINX

Une fois NGINX installé et le port 80 ouvert sur votre machine virtuelle à partir d’Internet, vous pouvez utiliser un navigateur web de votre choix pour afficher la page d’accueil NGINX par défaut. Veillez à utiliser la *publicIpAddress* indiquée ci-dessus pour vous rendre sur la page par défaut. 

![Site par défaut NGINX](./media/azure-stack-quick-create-vm-linux-cli/nginx.png) 

## <a name="clean-up-resources"></a>Supprimer des ressources

Lorsque vous n’en avez plus besoin, vous pouvez utiliser la commande [az group delete](/cli/azure/group#delete) pour supprimer le groupe de ressources, la machine virtuelle et toutes les ressources associées.

```cli
az group delete --name myResourceGroup
```

## <a name="next-steps"></a>Étapes suivantes

[Créer une machine virtuelle à l’aide d’un mot de passe stocké dans un coffre de clés](azure-stack-kv-deploy-vm-with-secret.md)

[En savoir plus sur le stockage dans Azure Stack](azure-stack-storage-overview.md)


