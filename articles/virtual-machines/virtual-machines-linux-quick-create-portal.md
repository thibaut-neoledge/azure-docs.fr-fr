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
ms.topic: hero-article
ms.tgt_pltfrm: vm-linux
ms.workload: infrastructure
ms.date: 03/21/2017
ms.author: nepeters
translationtype: Human Translation
ms.sourcegitcommit: 424d8654a047a28ef6e32b73952cf98d28547f4f
ms.openlocfilehash: bcfd830a5e2f39f36460990cae7e84b04d9a5fbb
ms.lasthandoff: 03/22/2017

---

# <a name="create-a-linux-virtual-machine-with-the-azure-portal"></a>Créer un groupe de machines virtuelles identiques Linux avec le portail Azure

Les machines virtuelles Azure peuvent être créées via le portail Azure. Cette méthode fournit une interface utilisateur basée sur navigateur pour créer et configurer des machines virtuelles et toutes les ressources liées. Ce démarrage rapide décrit les étapes de base de création d’une machine virtuelle en utilisant le portail Azure. 

Si vous n’avez pas d’abonnement Azure, créez un [compte gratuit](https://azure.microsoft.com/free/) avant de commencer.

## <a name="create-ssh-key-pair"></a>Créer la paire de clés SSH

Vous avez besoin d’une paire de clés SSH pour effectuer ce démarrage rapide. Si vous disposez déjà d’une paire de clés SSH, vous pouvez ignorer cette étape. Si vous utilisez un ordinateur Windows, suivez les instructions décrites [ici](./virtual-machines-linux-ssh-from-windows.md). 

Dans un interpréteur de commandes Bash, exécutez la commande suivante et suivez les instructions qui s’affichent à l’écran. La sortie de la commande inclut le nom du fichier de clé publique. Vous aurez besoin du contenu de ce fichier au moment de créer la machine virtuelle.

```bash
ssh-keygen -t rsa -b 2048
```

## <a name="log-in-to-azure"></a>Connexion à Azure 

Connectez-vous au Portail Azure à l’adresse http://portal.azure.com.

## <a name="create-virtual-machine"></a>Create virtual machine

1. Cliquez sur le bouton **Nouveau** dans le coin supérieur gauche du portail Azure.

2. Sélectionnez **Calcul** à partir du panneau **Nouveau**, sélectionnez **Ubuntu Server 16.04 LTS** à partir du panneau **Calcul**, puis cliquez sur le bouton **Créer**.

3. Remplissez le formulaire **Paramètres de base** de la machine virtuelle. Sous **Type d’authentification**, sélectionnez **SSH**. Lorsque vous collez votre **clé publique SSH**, veillez à supprimer tout espace blanc au début ou à la fin. Pour **Groupe de ressources** créez-en un nouveau. Un groupe de ressources est un conteneur logique dans lequel les ressources Azure sont créées et gérées collectivement. Lorsque vous avez terminé, cliquez sur **OK**.

    ![Saisie des informations de base sur votre machine virtuelle dans le panneau du portail](./media/virtual-machine-quick-start/create-vm-portal-basic-blade.png)  

4. Choisissez une taille pour la machine virtuelle et cliquez sur **Sélectionner**. 

    ![Sélectionner une taille pour votre machine virtuelle dans le panneau du portail](./media/virtual-machine-quick-start/create-vm-portal-size-blade.png)

5. Dans le panneau Paramètres, sélectionnez **Oui** sous **Utiliser des disques gérés**, conservez les valeurs par défaut pour le reste des paramètres, puis cliquez sur **OK**.

6. Sur la page Résumé, cliquez sur **OK** pour lancer le déploiement de machine virtuelle.

7. Pour surveiller l’état du déploiement, cliquez sur la machine virtuelle. La machine virtuelle peut être trouvée dans le tableau de bord du portail Azure, ou en sélectionnant **Machines virtuelles** dans le menu de gauche. Une fois la machine virtuelle créée, l’état passe à **En cours de déploiement** à **En cours d’exécution**.

## <a name="connect-to-virtual-machine"></a>Connexion à la machine virtuelle

Une fois le déploiement terminé, créez une connexion SSH avec la machine virtuelle.

1. Cliquez sur le bouton **Connexion** dans le volet de la machine virtuelle. Le bouton de connexion affiche une chaîne de connexion SSH qui peut être utilisée pour se connecter à la machine virtuelle.

    ![Portail 9](./media/virtual-machine-quick-start/portal-quick-start-9.png) 

2. Exécutez la commande suivante pour créer une session SSH. Remplacez la chaîne de connexion avec celle que vous avez copiée à partir du portail Azure.

```bash 
ssh <replace with IP address>
```
## <a name="delete-virtual-machine"></a>Suppression d'une machine virtuelle

Lorsque vous n’en avez plus besoin, supprimez le groupe de ressources, la machine virtuelle et toutes les ressources associées. Pour ce faire, sélectionnez le groupe de ressources à partir du panneau de la machine virtuelle, puis cliquez sur **supprimer**.

## <a name="next-steps"></a>Étapes suivantes

[Didacticiel de création de machines virtuelles hautement disponibles](./virtual-machines-linux-create-cli-complete.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json)

[Découvrir des exemples de commande de déploiement de machine virtuelle](./virtual-machines-linux-cli-samples.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json)

