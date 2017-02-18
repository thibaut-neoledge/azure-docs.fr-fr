---
title: "Capturer l’image d’une machine virtuelle Linux | Microsoft Docs"
description: "Apprenez à capturer l’image d’une machine virtuelle Azure sous Linux créée avec le modèle de déploiement classique."
services: virtual-machines-linux
documentationcenter: 
author: iainfoulds
manager: timlt
editor: tysonn
tags: azure-service-management
ms.assetid: 17d7ffee-a58e-4290-9de1-64c3cf1ddc05
ms.service: virtual-machines-linux
ms.workload: infrastructure-services
ms.tgt_pltfrm: vm-linux
ms.devlang: na
ms.topic: article
ms.date: 11/28/2016
ms.author: iainfou
translationtype: Human Translation
ms.sourcegitcommit: 3136b8345d0c851c29a9498089da73c8564549d1
ms.openlocfilehash: c81c2f86802c1b1d672105962c196b36fbb2c081


---
# <a name="how-to-capture-a-classic-linux-virtual-machine-as-an-image"></a>Capture d'une machine virtuelle Linux classique en tant qu'image
> [!IMPORTANT] 
> Azure dispose de deux modèles de déploiement différents pour créer et utiliser des ressources : [le déploiement Resource Manager et le déploiement classique](../azure-resource-manager/resource-manager-deployment-model.md). Cet article traite du modèle de déploiement classique. Pour la plupart des nouveaux déploiements, Microsoft recommande d’utiliser le modèle Resource Manager. Découvrez comment [effectuer ces étapes à l’aide du modèle Resource Manager](virtual-machines-linux-capture-image.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json).

Cet article vous montre comment capturer une machine virtuelle (MV) Azure classique exécutant Linux en tant qu'image pour créer d'autres machines virtuelles. Cette image comprend le disque du système d'exploitation ainsi que les disques de données attachés à la machine virtuelle. Elle ne comprend pas la configuration de mise en réseau, vous devez donc la configurer lors de la création d’autres machines virtuelles à partir de l’image.

Azure stocke l’image sous **Images**, avec les images que vous avez chargées. Pour plus d’informations sur les images, consultez l’article [À propos des images de machine virtuelle dans Azure][About Virtual Machine Images in Azure].

## <a name="before-you-begin"></a>Avant de commencer
Ces étapes partent du principe que vous avez déjà créé une machine virtuelle Azure à l’aide du modèle de déploiement Classic, configuré le système d’exploitation et attaché les disques de données. Si vous devez créer une machine virtuelle, consultez [Comment créer une machine virtuelle Linux][How to Create a Linux Virtual Machine].

## <a name="capture-the-virtual-machine"></a>Capture de la machine virtuelle
1. [Connectez-vous à la machine virtuelle](virtual-machines-linux-mac-create-ssh-keys.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json) à l’aide d’un client SSH de votre choix.
2. Dans la fenêtre SSH, tapez la commande suivante. Le résultat de `waagent` peut varier légèrement en fonction de la version utilisée :
   
    ```bash
    sudo waagent -deprovision+user
    ```
   
    La commande précédente tente de nettoyer le système et de le préparer pour le réapprovisionnement. Cette opération effectue les tâches suivantes :
   
   * supprime toutes les clés de l'hôte SSH (si Provisioning.RegenerateSshHostKeyPair a la valeur « y » dans le fichier de configuration) ;
   * efface la configuration de Nameserver dans /etc/resolv.conf ;
   * supprime le mot de passe `root` de l’utilisateur de /etc/shadow (si Provisioning.DeleteRootPassword a la valeur « y » dans le fichier de configuration) ;
   * supprime les baux du client DHCP mis en cache ;
   * Réinitialise le nom d’hôte sur localhost.localdomain.
   * efface le dernier compte d’utilisateur approvisionné (obtenu de /var/lib/waagent) **et les données associées**.
     
     > [!NOTE]
     > L’annulation de l’approvisionnement supprime les fichiers et les données pour « généraliser » l’image. Exécutez uniquement cette commande sur une machine virtuelle que vous souhaitez capturer dans un nouveau modèle d'image. Cela ne garantit pas que l’image est exempte de toute information sensible ou qu’elle convient pour la redistribution à des tiers.

3. Tapez **y** pour continuer. Vous pouvez ajouter le paramètre `-force` pour éviter cette étape de confirmation.
4. Tapez **Exit** pour fermer le client SSH.
   
   > [!NOTE]
   > Les étapes restantes supposent que vous avez déjà [installé l’interface CLI Azure](../xplat-cli-install.md) sur votre ordinateur client. Toutes les étapes suivantes peuvent également être effectuées dans le [Portail Azure Classic][Azure classic portal].

5. À partir de votre ordinateur client, ouvrez la CLI Azure et connectez-vous à votre abonnement Azure. Pour plus d’informations, consultez [Se connecter à un abonnement Azure à partir de l’interface de ligne de commande Azure (Azure CLI)](../xplat-cli-connect.md).
6. Assurez-vous que vous êtes en mode de gestion des services :
   
    ```azurecli
    azure config mode asm
    ```

7. Arrêtez la machine virtuelle dont l’approvisionnement est déjà annulé. L’exemple ci-après arrête la machine virtuelle nommée `myVM` :
   
    ```azurecli
    azure vm shutdown myVM
    ```
   
   > [!NOTE]
   > Vous pouvez voir la liste de toutes les machines virtuelles créées dans votre abonnement à l’aide de la commande `azure vm list`

8. Lorsque la machine virtuelle est arrêtée, capturez l’image. L’exemple suivant capture la machine virtuelle nommée `myVM` et crée une image généralisée nommée `myNewVM` :
   
    ```azurecli
    azure vm capture -t myVM myNewVM
    ```
   
    La sous-commande `-t` supprime la machine virtuelle d’origine.

9. La nouvelle image est maintenant disponible dans la liste des images pouvant être utilisées pour configurer de nouvelles machines virtuelles. Vous pouvez l'afficher avec la commande :
   
   ```azurecli
   azure vm image list
   ```
   
   Dans le [Portail Azure Classic][Azure classic portal], elle apparaît dans la liste **IMAGES**.
   
   ![Capture d’image réussie](./media/virtual-machines-linux-classic-capture-image/VMCapturedImageAvailable.png)

## <a name="next-steps"></a>Étapes suivantes
L’image est prête à être utilisée pour créer des machines virtuelles. Vous pouvez utiliser la commande `azure vm create` de l’interface CLI Azure et fournir le nom de l’image que vous avez créée. Pour plus d’informations, consultez [Utilisation de l’interface de ligne de commande Azure avec le modèle de déploiement Classic](https://docs.microsoft.com/cli/azure/get-started-with-az-cli2). 

Vous pouvez également utiliser le [Portail Azure Classic][Azure classic portal] pour créer une machine virtuelle personnalisée en utilisant l’option **À partir de la galerie** et en sélectionnant l’image que vous avez créée. Pour plus d'informations, consultez [Création d’une machine virtuelle personnalisée][How to Create a Custom Virtual Machine].

**Consultez également :** [Guide d’utilisateur de l’agent Linux Azure](virtual-machines-linux-agent-user-guide.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json)

[Azure classic portal]: http://manage.windowsazure.com
[About Virtual Machine Images in Azure]: virtual-machines-linux-classic-about-images.md
[How to Create a Custom Virtual Machine]: virtual-machines-linux-classic-create-custom.md
[How to Attach a Data Disk to a Virtual Machine]: virtual-machines-windows-classic-attach-disk.md
[How to Create a Linux Virtual Machine]: virtual-machines-linux-classic-create-custom.md



<!--HONumber=Jan17_HO5-->


