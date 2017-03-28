---
title: "Démarrage rapide avec Azure - Portail pour la création d’une machine virtuelle Windows | Microsoft Docs"
description: "Démarrage rapide avec Azure - Portail pour la création d’une machine virtuelle Windows"
services: virtual-machines-windows
documentationcenter: virtual-machines
author: neilpeterson
manager: timlt
editor: tysonn
tags: azure-resource-manager
ms.assetid: 
ms.service: virtual-machines-windows
ms.devlang: na
ms.topic: hero-article
ms.tgt_pltfrm: vm-windows
ms.workload: infrastructure
ms.date: 03/21/2017
ms.author: nepeters
translationtype: Human Translation
ms.sourcegitcommit: 424d8654a047a28ef6e32b73952cf98d28547f4f
ms.openlocfilehash: a13ac5ab425ccbbe53d77cb9f5a8ebf02d009370
ms.lasthandoff: 03/22/2017

---

# <a name="create-a-windows-virtual-machine-with-the-azure-portal"></a>Créer une machine virtuelle Windows avec le portail Azure

Les machines virtuelles Azure peuvent être créées via le portail Azure. Cette méthode fournit une interface utilisateur basée sur navigateur pour créer et configurer des machines virtuelles et toutes les ressources liées. Ce démarrage rapide décrit les étapes de base de création d’une machine virtuelle en utilisant le portail Azure. 

Si vous n’avez pas d’abonnement Azure, créez un [compte gratuit](https://azure.microsoft.com/free/) avant de commencer.

## <a name="log-in-to-azure"></a>Connexion à Azure 

Connectez-vous au Portail Azure à l’adresse http://portal.azure.com.

## <a name="create-virtual-machine"></a>Create virtual machine

2. Cliquez sur le bouton **Nouveau** dans le coin supérieur gauche du portail Azure.

3. Sélectionnez **Calcul** à partir du panneau **Nouveau**, sélectionnez **Windows Server 2016 Datacenter** à partir du panneau **Calcul**, puis cliquez sur le bouton **Créer**.

4. Remplissez le formulaire **Paramètres de base** de la machine virtuelle. Le nom d’utilisateur et le mot de passe que vous avez entrés vous serviront pour vous connecter à la machine virtuelle. Pour **Groupe de ressources** créez-en un nouveau. Un groupe de ressources est un conteneur logique dans lequel les ressources Azure sont créées et gérées collectivement. Lorsque vous avez terminé, cliquez sur **OK**.

    ![Saisie des informations de base sur votre machine virtuelle dans le panneau du portail](./media/virtual-machine-quick-start/create-windows-vm-portal-basic-blade.png)  

5. Choisissez une taille pour la machine virtuelle et cliquez sur **Sélectionner**. 

6. Dans le panneau Paramètres, sélectionnez **Oui** sous **Utiliser des disques gérés**, conservez les valeurs par défaut pour le reste des paramètres, puis cliquez sur **OK**.

7. Sur la page Résumé, cliquez sur **OK** pour lancer le déploiement de machine virtuelle.

8. Pour surveiller l’état du déploiement, cliquez sur la machine virtuelle. La machine virtuelle peut être trouvée dans le tableau de bord du portail Azure, ou en sélectionnant **Machines virtuelles** dans le menu de gauche. Une fois la machine virtuelle créée, l’état passe à **En cours de déploiement** à **En cours d’exécution**.

## <a name="connect-to-virtual-machine"></a>Connexion à la machine virtuelle

Une fois le déploiement terminé, créez une connexion Bureau à distance à la machine virtuelle.

1. Cliquez sur le bouton **Connexion** dans le volet de la machine virtuelle. Un fichier de protocole Remote Desktop Protocol (fichier .rdp) est créé et téléchargé.

    ![Portail 9](./media/virtual-machine-quick-start/portal-quick-start-9.png) 

2. Pour vous connecter à votre machine virtuelle, ouvrez le fichier RDP téléchargé. À l’invite, cliquez sur **Se connecter**. Sur un Mac, vous avez besoin d’un client RDP similaire à ce [Client Bureau à distance](https://itunes.apple.com/us/app/microsoft-remote-desktop/id715768417?mt=12) disponible sur le Mac App Store.

3. Entrez le nom d’utilisateur et le mot de passe spécifiés lors de la création de la machine virtuelle, puis cliquez sur **OK**.

4. Un avertissement de certificat peut s’afficher pendant le processus de connexion. Cliquez sur **Oui** ou **Continuer** pour continuer le processus de connexion.

## <a name="delete-virtual-machine"></a>Suppression d'une machine virtuelle

Lorsque vous n’en avez plus besoin, supprimez le groupe de ressources, la machine virtuelle et toutes les ressources associées. Pour ce faire, sélectionnez le groupe de ressources à partir du panneau de la machine virtuelle, puis cliquez sur **supprimer**.

## <a name="next-steps"></a>Étapes suivantes

[Didacticiel d’installation de rôle et de configuration de pare-feu](./virtual-machines-windows-hero-role.md?toc=%2fazure%2fvirtual-machines%2fwindows%2ftoc.json)

[Découvrir des exemples de commande de déploiement de machine virtuelle](./virtual-machines-windows-cli-samples.md?toc=%2fazure%2fvirtual-machines%2fwindows%2ftoc.json)
