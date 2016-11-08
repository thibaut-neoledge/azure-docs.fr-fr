---
title: Utilisation du bureau à distance avec les rôles Azure | Microsoft Docs
description: Utilisation du Bureau à distance avec des rôles Azure
services: visual-studio-online
documentationcenter: na
author: TomArcher
manager: douge
editor: ''

ms.service: multiple
ms.devlang: multiple
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 08/15/2016
ms.author: tarcher

---
# Utilisation du Bureau à distance avec des rôles Azure
En utilisant le kit de développement logiciel (SDK) Azure et les Services Bureau à distance, vous pouvez accéder aux rôles Azure et aux machines virtuelles hébergées par Azure. Dans Visual Studio, vous pouvez configurer les Services Bureau à distance à partir d'un projet Azure. Pour activer les Services Bureau à distance, vous devez créer un projet de travail qui contient un ou plusieurs rôles, puis le publier sur Azure.

> [!IMPORTANT]
> L’accès aux rôles Azure est réservé au dépannage et au développement. L'objectif de chaque machine virtuelle consiste à exécuter un rôle spécifique dans votre application Azure, et non à exécuter d'autres applications clientes. Si vous souhaitez utiliser Azure pour héberger une machine virtuelle que vous pourrez utiliser à d’autres fins, consultez Accès aux machines virtuelles Azure à partir de l'Explorateur de serveurs.
> 
> 

## Pour activer et utiliser le Bureau à distance pour un rôle Azure
1. Dans l’Explorateur de solutions, ouvrez le menu contextuel de votre projet, puis sélectionnez **Publier**.
   
    L’Assistant **Publication d’application Azure** s’affiche.
   
    ![Commande Publier pour un projet de service cloud](./media/vs-azure-tools-remote-desktop-roles/IC799161.png)
2. En bas de la page **Paramètres de publication Microsoft Azure** de l'Assistant, cochez la case **Activer le Bureau à distance** pour tous les rôles.
   
    La boîte de dialogue **Configuration du Bureau à distance** s’affiche.
3. En bas de la boîte de dialogue **Configuration du Bureau à distance**, sélectionnez le bouton **Plus d'options**.
   
    Cela affiche une liste déroulante qui vous permet de créer ou de sélectionner un certificat afin de chiffrer les informations d'identification lors de la connexion Bureau à distance.
4. Dans la liste déroulante, sélectionnez **&lt;Créer>** ou sélectionnez un certificat existant dans la liste.
   
    Si vous sélectionnez un certificat existant, ignorez les étapes suivantes.
   
   > [!NOTE]
   > Les certificats dont vous avez besoin pour une connexion Bureau à distance sont différents de ceux que vous utilisez pour d'autres opérations Azure. Le certificat de l'accès à distance doit avoir une clé privée.
   > 
   > 
   
    La boîte de dialogue **Créer un certificat** s’affiche.
   
   1. Donnez un nom convivial au nouveau certificat, puis sélectionnez le bouton **OK**. Le nouveau certificat s'affiche dans la liste déroulante.
   2. Dans la boîte de dialogue **Configuration du Bureau à distance**, entrez un nom d’utilisateur et un mot de passe.
      
       Vous ne pouvez pas utiliser un compte existant. Ne spécifiez pas Administrateur comme nom d'utilisateur pour le nouveau compte.
      
      > [!NOTE]
      > Si le mot de passe ne répond pas aux exigences de complexité, une icône rouge apparaît en regard de la zone de texte du mot de passe. Le mot de passe doit comporter des lettres majuscules, des lettres minuscules et des nombres ou des symboles.
      > 
      > 
   3. Choisissez une date à laquelle le compte expirera et après laquelle les connexions Bureau à distance seront bloquées.
   4. Une fois que vous avez fourni toutes les informations requises, sélectionnez le bouton **OK**.
      
       Plusieurs paramètres qui activent les Services d'accès à distance sont ajoutés aux fichiers .cscfg et .csdef.
5. Dans l’Assistant **Paramètres de publication Microsoft Azure**, sélectionnez le bouton **OK** quand vous êtes prêt à publier votre service cloud.
   
    Si vous n’êtes pas prêt à le publier, sélectionnez le bouton **Annuler**. Les paramètres de configuration sont enregistrés, et vous pourrez publier votre service cloud ultérieurement.

## Se connecter à un rôle Azure à l'aide du Bureau à distance
Après avoir publié votre service cloud sur Azure, vous pouvez utiliser l'Explorateur de serveurs pour vous connecter aux machines virtuelles hébergées par Azure.

1. Dans l’Explorateur de serveurs, développez le nœud **Azure**, puis développez le nœud d’un service cloud et d’un de ses rôles pour afficher une liste d’instances.
2. Ouvrez le menu contextuel d’un nœud d’instance, puis sélectionnez **Connexion à l’aide de Bureau à distance**.
   
    ![Connexion Bureau à distance](./media/vs-azure-tools-remote-desktop-roles/IC799162.png)
3. Entrez le nom d'utilisateur et le mot de passe que vous avez créés précédemment. Vous êtes maintenant connecté à votre session à distance.

<!---HONumber=AcomDC_0817_2016-->