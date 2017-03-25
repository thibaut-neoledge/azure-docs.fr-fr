---
title: "Démarrage rapide avec Azure - Portail pour la création d’une machine virtuelle | Microsoft Docs"
description: "Démarrage rapide avec Azure - Portail pour la création d’une machine virtuelle"
services: virtual-machines-linux
documentationcenter: virtual-machines
author: neilpeterson
manager: timlt
editor: tysonn
tags: azure-resource-manager
ms.assetid: 
ms.service: virtual-machines-linux
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: vm-linux
ms.workload: infrastructure
ms.date: 03/10/2017
ms.author: nepeters
translationtype: Human Translation
ms.sourcegitcommit: c1cd1450d5921cf51f720017b746ff9498e85537
ms.openlocfilehash: 348407b57bbb3329d7d27a6f38623e052aecc58b
ms.lasthandoff: 03/14/2017

---

# <a name="create-a-linux-virtual-machine-with-the-azure-portal"></a>Créer un groupe de machines virtuelles identiques Linux avec le portail Azure

Les machines virtuelles Azure peuvent être créées via le portail Azure. Cette méthode fournit une interface utilisateur basée sur navigateur pour créer et configurer des machines virtuelles et toutes les ressources Azure liées.

Avant de commencer, une clé SSH privée et une publique sont nécessaires. Pour plus d’informations sur la création de clés SSH pour Azure, consultez [Création de clés SSH pour Azure](./virtual-machines-linux-mac-create-ssh-keys.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json).

## <a name="log-in-to-azure"></a>Connexion à Azure 

Connectez-vous au Portail Azure à l’adresse http://portal.azure.com.

## <a name="create-virtual-machine"></a>Create virtual machine

2. Cliquez sur le bouton **Nouveau** dans le coin supérieur gauche du portail Azure.

3. Sélectionnez **Calcul** à partir du panneau **Nouveau**, sélectionnez **Ubuntu Server 16.04 LTS** à partir du panneau **Calcul**, puis cliquez sur le bouton **Créer**.

4. Remplissez le formulaire **Paramètres de base** de la machine virtuelle. Pour **Type d’authentification**, SSH est recommandé. Lorsque vous collez votre **clé publique SSH**, veillez à supprimer tout espace blanc au début ou à la fin. Pour **Groupe de ressources** créez-en un nouveau. Un groupe de ressources est un conteneur logique dans lequel les ressources Azure sont créées et gérées collectivement. Lorsque vous avez terminé, cliquez sur **OK**.

    ![Saisie des informations de base sur votre machine virtuelle dans le panneau du portail](./media/virtual-machine-quick-start/create-vm-portal-basic-blade.png)  

5. Choisissez une taille pour la machine virtuelle et cliquez sur **Sélectionner**. 

    ![Sélectionner une taille pour votre machine virtuelle dans le panneau du portail](./media/virtual-machine-quick-start/create-vm-portal-size-blade.png)

6. Dans le panneau Paramètres, sélectionnez **Oui** sous **Utiliser des disques gérés**, conservez les valeurs par défaut pour le reste des paramètres, puis cliquez sur **OK**.

7. Sur la page Résumé, cliquez sur **OK** pour lancer le déploiement de machine virtuelle.

## <a name="connect-to-virtual-machine"></a>Connexion à la machine virtuelle

Une fois le déploiement terminé, créez une connexion SSH avec la machine virtuelle.

1. Cliquez sur la machine virtuelle. La machine virtuelle peut être trouvée sur l’écran d’accueil du portail Azure, ou en sélectionnant **Machines virtuelles** dans le menu de gauche.

2. Cliquez sur le bouton **Connect** . Le bouton de connexion affiche une chaîne de connexion SSH qui peut être utilisée pour se connecter à la machine virtuelle.

    ![Portail 9](./media/virtual-machine-quick-start/portal-quick-start-9.png) 

3. Exécutez la commande suivante pour créer une session SSH. Remplacez la chaîne de connexion avec celle que vous avez copiée à partir du portail Azure.

```bash 
ssh <replace with IP address>
```
## <a name="delete-virtual-machine"></a>Suppression d'une machine virtuelle

Lorsque vous n’en avez plus besoin, supprimez le groupe de ressources, la machine virtuelle et toutes les ressources associées. Pour ce faire, sélectionnez le groupe de ressources à partir du panneau de la machine virtuelle, puis cliquez sur **supprimer**.

## <a name="next-steps"></a>Étapes suivantes

[Didacticiel de création de machines virtuelles hautement disponibles](./virtual-machines-linux-create-cli-complete.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json)

[Découvrir des exemples de commande de déploiement de machine virtuelle](./virtual-machines-linux-cli-samples.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json)

