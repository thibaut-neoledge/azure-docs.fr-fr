---
title: "Création d’une machine virtuelle Linux à l’aide du portail Azure | Microsoft Docs"
description: "Création d’une machine virtuelle Linux à l’aide du portail Azure."
services: virtual-machines-linux
documentationcenter: 
author: vlivech
manager: timlt
editor: 
tags: azure-resource-manager
ms.assetid: cc5dc395-dc54-4402-8804-2bb15aba8ea2
ms.service: virtual-machines-linux
ms.workload: infrastructure-services
ms.tgt_pltfrm: vm-linux
ms.devlang: na
ms.topic: hero-article
ms.date: 10/28/2016
ms.author: v-livech
translationtype: Human Translation
ms.sourcegitcommit: 2ea002938d69ad34aff421fa0eb753e449724a8f
ms.openlocfilehash: b1446cd8892e14988ff428eaa03233f8e9aefb8a


---
# <a name="create-a-linux-vm-on-azure-using-the-portal"></a>Création d'une machine virtuelle Linux sur Azure à l’aide du portail
Cet article explique comment utiliser le [portail Azure](https://portal.azure.com/) pour créer une machine virtuelle Linux.

Les conditions requises sont :

* [un compte Azure](https://azure.microsoft.com/pricing/free-trial/)
* [des fichiers de clés SSH publiques et privées](virtual-machines-linux-mac-create-ssh-keys.md)

## <a name="sign-in"></a>de connexion
Connectez-vous au portail Azure avec les identifiants de votre compte Azure, puis cliquez sur **+ Nouveau** dans le coin supérieur gauche :

![écran1](../media/virtual-machines-linux-quick-create-portal/screen1.png)

## <a name="choose-vm"></a>Choisir une machine virtuelle
Cliquez sur **Machines virtuelles** dans le panneau **Marketplace**, puis sur **Ubuntu Server 14.04 LTS** dans la liste d’images **Applications proposées**.  Vérifiez en bas de l’écran que le modèle de déploiement est `Resource Manager` , puis cliquez sur **Créer**.

![écran2](../media/virtual-machines-linux-quick-create-portal/screen2.png)

## <a name="enter-vm-options"></a>Définir les options de la machine virtuelle
Dans la page **Fonctions de base** , entrez :

* un nom pour la machine virtuelle ;
* un nom d’utilisateur pour l’administrateur ;
* la définition du Type d’authentification sur **Clé publique SSH**
* votre clé publique SSH sous la forme d’une chaîne (à partir de votre répertoire `~/.ssh/` ) ;
* le nom d’un groupe de ressources (sélectionnez-en un).

Puis cliquez sur **OK** pour continuer et choisissez la taille de la machine virtuelle. Le résultat obtenu devrait ressembler à la capture d’écran suivante :

![écran3](../media/virtual-machines-linux-quick-create-portal/screen3.png)

## <a name="choose-vm-size"></a>Choisir la taille de la machine virtuelle
Choisissez la taille **DS1**, qui installe Ubuntu sur un disque SSD Premium, puis cliquez sur **Sélectionner** pour configurer les paramètres.

![écran4](../media/virtual-machines-linux-quick-create-portal/screen4.png)

## <a name="storage-and-network"></a>Stockage et réseau
Dans **Paramètres**, conservez les valeurs par défaut pour les paramètres de stockage et de réseau, puis cliquez sur **OK** pour afficher le résumé.  Notez que le type de disque a été défini sur Premium SSD lors du choix de la taille DS1, le **S** signifiant SSD.

![écran5](../media/virtual-machines-linux-quick-create-portal/screen5.png)

## <a name="confirm-vm-settings-and-launch"></a>Vérifier les paramètres de la machine virtuelle et la démarrer
Confirmez les paramètres pour votre nouvelle machine virtuelle Ubuntu, puis cliquez sur **OK**.

![écran6](../media/virtual-machines-linux-quick-create-portal/screen6.png)

## <a name="find-the-vm-nic"></a>Rechercher la carte réseau de la machine virtuelle
Ouvrez le tableau de bord du Portail et choisissez votre carte réseau dans **Interfaces réseau** .

![écran7](../media/virtual-machines-linux-quick-create-portal/screen7.png)

## <a name="find-the-public-ip"></a>Rechercher l’adresse IP publique
Ouvrez le menu Adresses IP publiques sous les paramètres de carte réseau

![écran8](../media/virtual-machines-linux-quick-create-portal/screen8.png)

## <a name="ssh-to-the-vm"></a>Utiliser une clé SSH sur la machine virtuelle
Connectez-vous avec SSH à l’adresse IP publique à l’aide de votre clé publique SSH.  À partir d’une station de travail Mac ou Linux, vous pouvez vous connecter avec SSH directement à partir du terminal. Si vous utilisez une station de travail Windows, vous devez utiliser PuTTY, MobaXTerm ou Cygwin pour vous connecter avec SSH à Linux.  Si vous ne l’avez pas encore fait, voici un document qui prépare votre station de travail Windows pour la connexion avec SSH à Linux.

[Comment utiliser les clés SSH avec Windows sur Azure](virtual-machines-linux-ssh-from-windows.md)

```
ssh -i ~/.ssh/azure_id_rsa ubuntu@13.91.99.206
```

## <a name="next-steps"></a>Étapes suivantes
Vous venez de créer rapidement une machine virtuelle Linux à des fins de test ou de démonstration. Pour créer une machine virtuelle Linux personnalisée en fonction de votre infrastructure, vous pouvez lire un des articles ci-dessous.

* [Création d'une machine virtuelle Linux sur Azure à l’aide de modèles](virtual-machines-linux-cli-deploy-templates.md)
* [Création d’une machine virtuelle Linux sécurisée par SSH dans Azure à l’aide de modèles](virtual-machines-linux-create-ssh-secured-vm-from-template.md)
* [Création d'une machine virtuelle Linux à l’aide de l'interface de ligne de commande Azure (CLI)](virtual-machines-linux-create-cli-complete.md)




<!--HONumber=Nov16_HO2-->


