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
ms.date: 04/13/2017
ms.author: nepeters
translationtype: Human Translation
ms.sourcegitcommit: e851a3e1b0598345dc8bfdd4341eb1dfb9f6fb5d
ms.openlocfilehash: 8a86cf64dcd65e74285a1073f7494eba0708ddcd
ms.lasthandoff: 04/15/2017

---

# <a name="create-a-windows-virtual-machine-with-the-azure-portal"></a>Créer une machine virtuelle Windows avec le portail Azure

Les machines virtuelles Azure peuvent être créées via le portail Azure. Cette méthode fournit une interface utilisateur basée sur navigateur pour créer et configurer des machines virtuelles et toutes les ressources liées. Ce démarrage rapide décrit les étapes de base de création d’une machine virtuelle en utilisant le portail Azure. Une fois le déploiement terminé, connectez-vous au serveur et installez les services IIS.

Si vous n’avez pas d’abonnement Azure, créez un [compte gratuit](https://azure.microsoft.com/en-us/free/?WT.mc_id=A261C142F) avant de commencer.

## <a name="log-in-to-azure"></a>Connexion à Azure

Connectez-vous au Portail Azure à l’adresse http://portal.azure.com.

## <a name="create-virtual-machine"></a>Create virtual machine

2. Cliquez sur le bouton **Nouveau** dans le coin supérieur gauche du portail Azure.

3. Sélectionnez **Calcul** à partir du panneau **Nouveau**, sélectionnez **Windows Server 2016 Datacenter** à partir du panneau **Calcul**, puis cliquez sur le bouton **Créer**.

4. Remplissez le formulaire **Paramètres de base** de la machine virtuelle. Le nom d’utilisateur et le mot de passe que vous avez entrés vous serviront pour vous connecter à la machine virtuelle. Pour **Groupe de ressources** créez-en un nouveau. Un groupe de ressources est un conteneur logique dans lequel les ressources Azure sont créées et gérées collectivement. Lorsque vous avez terminé, cliquez sur **OK**.

    ![Saisie des informations de base sur votre machine virtuelle dans le panneau du portail](./media/quick-create-portal/create-windows-vm-portal-basic-blade.png)  

5. Choisissez une taille pour la machine virtuelle. Pour voir plus de tailles, sélectionnez **Afficher tout** ou modifiez le filtre **Type de disque pris en charge**. 

    ![Capture d’écran montrant les tailles de machine virtuelle](./media/quick-create-portal/create-windows-vm-portal-sizes.png)  

6. Dans le panneau Paramètres, sélectionnez **Oui** sous **Utiliser des disques gérés**, conservez les valeurs par défaut pour le reste des paramètres, puis cliquez sur **OK**.

7. Sur la page Résumé, cliquez sur **OK** pour lancer le déploiement de machine virtuelle.

8. Pour surveiller l’état du déploiement, cliquez sur la machine virtuelle. La machine virtuelle peut être trouvée dans le tableau de bord du portail Azure, ou en sélectionnant **Machines virtuelles** dans le menu de gauche. Une fois la machine virtuelle créée, l’état passe à **En cours de déploiement** à **En cours d’exécution**.

## <a name="open-port-80-for-web-traffic"></a>Ouvrez le port 80 pour le trafic web 

Pour autoriser le trafic pour IIS, vous devez ouvrir le port 80 pour le trafic web. Cette étape vous guide lors de la création d’une règle de groupe de sécurité réseau (NSG) pour autoriser les connexions entrantes sur le port 80.

1. Dans le panneau de la machine virtuelle, dans la section **Essentials**, cliquez sur le nom du **groupe de ressources**.
2. Dans le panneau du groupe de ressources, cliquez sur le **groupe de sécurité réseau** dans la liste des ressources. Le nom du groupe de sécurité réseau doit être le nom de la machine virtuelle suivi de -nsg ajouté à la fin.
3. Cliquez sur le titre **Règle de sécurité entrante** pour ouvrir la liste des règles entrantes. Vous devriez voir une règle pour RDP dans la liste.
4. Cliquez sur **+ Ajouter** pour ouvrir le panneau **Ajouter une règle de sécurité entrante**.
5. Sous **Nom**, tapez **IIS**. Assurez-vous que l’option **Plage de ports** est définie sur 80 et l’option **Action** sur **Autoriser**. Cliquez sur **OK**.


## <a name="connect-to-virtual-machine"></a>Connexion à la machine virtuelle

Une fois le déploiement terminé, créez une connexion Bureau à distance à la machine virtuelle.

1. Cliquez sur le bouton **Connexion** dans le volet de la machine virtuelle. Un fichier de protocole Remote Desktop Protocol (fichier .rdp) est créé et téléchargé.

    ![Portail 9](./media/quick-create-portal/quick-create-portal/portal-quick-start-9.png) 

2. Pour vous connecter à votre machine virtuelle, ouvrez le fichier RDP téléchargé. À l’invite, cliquez sur **Se connecter**. Sur un Mac, vous avez besoin d’un client RDP similaire à ce [Client Bureau à distance](https://itunes.apple.com/us/app/microsoft-remote-desktop/id715768417?mt=12) disponible sur le Mac App Store.

3. Entrez le nom d’utilisateur et le mot de passe spécifiés lors de la création de la machine virtuelle, puis cliquez sur **OK**.

4. Un avertissement de certificat peut s’afficher pendant le processus de connexion. Cliquez sur **Oui** ou **Continuer** pour continuer le processus de connexion.


## <a name="install-iis-using-powershell"></a>Installation de IIS à l’aide de PowerShell

Sur la machine virtuelle, ouvrez une invite PowerShell et exécutez la commande suivante pour installer IIS et activer la règle de pare-feu local afin d’autoriser le trafic web :

```powershell
Install-WindowsFeature -name Web-Server -IncludeManagementTools
```

## <a name="view-the-iis-welcome-page"></a>Afficher la page d’accueil IIS

Avec IIS installé et le port 80 désormais ouvert sur votre machine virtuelle à partir d’Internet, vous pouvez utiliser un navigateur web de votre choix pour afficher la page d’accueil par défaut IIS. Obtenez l’**adresse IP publique** à partir du panneau pour la machine virtuelle, puis utilisez la pour visiter la page web par défaut. 

![Site par défaut IIS](./media/quick-create-powershell/default-iis-website.png) 

## <a name="delete-virtual-machine"></a>Suppression d'une machine virtuelle

Lorsque vous n’en avez plus besoin, supprimez le groupe de ressources, la machine virtuelle et toutes les ressources associées. Pour ce faire, sélectionnez le groupe de ressources à partir du panneau de la machine virtuelle, puis cliquez sur **supprimer**.

## <a name="next-steps"></a>Étapes suivantes

[Didacticiel d’installation de rôle et de configuration de pare-feu](hero-role.md?toc=%2fazure%2fvirtual-machines%2fwindows%2ftoc.json)

[Découvrir des exemples de commande de déploiement de machine virtuelle](cli-samples.md?toc=%2fazure%2fvirtual-machines%2fwindows%2ftoc.json)

