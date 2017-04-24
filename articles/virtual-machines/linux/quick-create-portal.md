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
ms.date: 04/13/2017
ms.author: nepeters
translationtype: Human Translation
ms.sourcegitcommit: e851a3e1b0598345dc8bfdd4341eb1dfb9f6fb5d
ms.openlocfilehash: ab29f01980bc7c3a8f12aaa55ff35baa3bf3f9fb
ms.lasthandoff: 04/15/2017

---

# <a name="create-a-linux-virtual-machine-with-the-azure-portal"></a>Créer un groupe de machines virtuelles identiques Linux avec le portail Azure

Les machines virtuelles Azure peuvent être créées via le portail Azure. Cette méthode fournit une interface utilisateur basée sur navigateur pour créer et configurer des machines virtuelles et toutes les ressources liées. Ce démarrage rapide décrit les étapes de base de création d’une machine virtuelle en utilisant le portail Azure.

Si vous n’avez pas d’abonnement Azure, créez un [compte gratuit](https://azure.microsoft.com/en-us/free/?WT.mc_id=A261C142F) avant de commencer.

## <a name="create-ssh-key-pair"></a>Créer la paire de clés SSH

Vous avez besoin d’une paire de clés SSH pour effectuer ce démarrage rapide. Si vous disposez déjà d’une paire de clés SSH, vous pouvez ignorer cette étape. Si vous utilisez un ordinateur Windows, suivez les instructions décrites [ici](ssh-from-windows.md). 

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

    ![Saisie des informations de base sur votre machine virtuelle dans le panneau du portail](./media/quick-create-portal/create-vm-portal-basic-blade.png)  

4. Choisissez une taille pour la machine virtuelle. Pour voir plus de tailles, sélectionnez **Afficher tout** ou modifiez le filtre **Type de disque pris en charge**. 

    ![Capture d’écran montrant les tailles de machine virtuelle](./media/quick-create-portal/create-linux-vm-portal-sizes.png)  

5. Dans le panneau Paramètres, sélectionnez **Oui** sous **Utiliser des disques gérés**, conservez les valeurs par défaut pour le reste des paramètres, puis cliquez sur **OK**.

6. Sur la page Résumé, cliquez sur **OK** pour lancer le déploiement de machine virtuelle.

7. Pour surveiller l’état du déploiement, cliquez sur la machine virtuelle. La machine virtuelle peut être trouvée dans le tableau de bord du portail Azure, ou en sélectionnant **Machines virtuelles** dans le menu de gauche. Une fois la machine virtuelle créée, l’état passe à **En cours de déploiement** à **En cours d’exécution**.


## <a name="open-port-80-for-web-traffic"></a>Ouvrez le port 80 pour le trafic web 

Par défaut, seules les connexions SSH sont autorisées dans les machines virtuelles Linux déployées dans Azure. Si cette machine virtuelle doit être un serveur web, vous devez ouvrir le port 80 au trafic web. Cette étape vous guide lors de la création d’une règle de groupe de sécurité réseau (NSG) pour autoriser les connexions entrantes sur le port 80.

1. Dans le panneau de la machine virtuelle, dans la section **Essentials**, cliquez sur le nom du **groupe de ressources**.
2. Dans le panneau du groupe de ressources, cliquez sur le **groupe de sécurité réseau** dans la liste des ressources. Le nom du groupe de sécurité réseau doit être le nom de la machine virtuelle suivi de -nsg ajouté à la fin.
3. Cliquez sur le titre **Règle de sécurité entrante** pour ouvrir la liste des règles entrantes. Vous devriez voir une règle pour RDP dans la liste.
4. Cliquez sur **+ Ajouter** pour ouvrir le panneau **Ajouter une règle de sécurité entrante**.
5. Sous **Nom**, tapez **nginx**. Assurez-vous que l’option **Plage de ports** est définie sur 80 et l’option **Action** sur **Autoriser**. Cliquez sur **OK**.


## <a name="connect-to-virtual-machine"></a>Connexion à la machine virtuelle

Une fois le déploiement terminé, créez une connexion SSH avec la machine virtuelle.

1. Cliquez sur le bouton **Connexion** dans le volet de la machine virtuelle. Le bouton de connexion affiche une chaîne de connexion SSH qui peut être utilisée pour se connecter à la machine virtuelle.

    ![Portail 9](./media/quick-create-portal/portal-quick-start-9.png) 

2. Exécutez la commande suivante pour créer une session SSH. Remplacez la chaîne de connexion avec celle que vous avez copiée à partir du portail Azure.

```bash 
ssh <replace with IP address>
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

## <a name="view-the-ngix-welcome-page"></a>Afficher la page d’accueil NGNIX

Une fois NGINX installé et le port 80 ouvert sur votre machine virtuelle à partir d’Internet, vous pouvez utiliser un navigateur web de votre choix pour afficher la page d’accueil NGINX par défaut. Veillez à utiliser le `publicIpAddress` décrit pour vous rendre sur la page par défaut. 

![Site par défaut NGINX](./media/quick-create-cli/nginx.png) 
## <a name="delete-virtual-machine"></a>Suppression d'une machine virtuelle

Lorsque vous n’en avez plus besoin, supprimez le groupe de ressources, la machine virtuelle et toutes les ressources associées. Pour ce faire, sélectionnez le groupe de ressources à partir du panneau de la machine virtuelle, puis cliquez sur **supprimer**.

## <a name="next-steps"></a>Étapes suivantes

[Didacticiel de création de machines virtuelles hautement disponibles](create-cli-complete.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json)

[Découvrir des exemples de commande de déploiement de machine virtuelle](../windows/cli-samples.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json)

