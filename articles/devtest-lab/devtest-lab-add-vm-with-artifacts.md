---
title: "Ajouter une machine virtuelle avec des artefacts à un laboratoire dans Azure DevTest Labs | Microsoft Docs"
description: "Découvrez comment ajouter une machine virtuelle avec des artefacts dans Azure DevTest Labs"
services: devtest-lab,virtual-machines
documentationcenter: na
author: tomarcher
manager: douge
editor: 
ms.assetid: 576509ce-6a33-4c26-87c7-de8b40271efa
ms.service: devtest-lab
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 11/25/2016
ms.author: tarcher
translationtype: Human Translation
ms.sourcegitcommit: f480b8155c7bee797f1fed0f80200eec500e95a2
ms.openlocfilehash: aa6c87ef6e1f87cb47f68f9480ea068aa6262bf9


---
# <a name="add-a-vm-with-artifacts-to-a-lab-in-azure-devtest-labs"></a>Ajouter une machine virtuelle avec des artefacts à un laboratoire dans Azure DevTest Labs
> [!VIDÉO https://channel9.msdn.com/Blogs/Windows-Azure/How-to-create-VMs-with-Artifacts-in-a-DevTest-Lab/player]
> 
> 

Vous créez une machine virtuelle dans un laboratoire à partir d’une *base* qui est une [image personnalisée](devtest-lab-create-template.md), une [formule](devtest-lab-manage-formulas.md) ou une [image de la Place de marché](devtest-lab-configure-marketplace-images.md).

Les *artefacts* de DevTest Labs vous permettent de spécifier des *actions* qui sont exécutées quand la machine virtuelle est créée. 

Les actions des artefacts peuvent réaliser des procédures, telles que l’exécution de scripts Windows PowerShell, l’exécution de commandes Bash et l’installation de logiciels. 

Les *paramètres* des artefacts vous permettent de personnaliser l'artefact pour votre scénario spécifique.

Cet article vous explique comment créer une machine virtuelle dans votre laboratoire à l’aide d’artefacts.

## <a name="add-a-vm-with-artifacts"></a>Ajout d'une machine virtuelle avec des artefacts
1. Connectez-vous au [portail Azure](http://go.microsoft.com/fwlink/p/?LinkID=525040).
2. Sélectionnez **Autres services**, puis **DevTest Labs** dans la liste.
3. Dans la liste des laboratoires, sélectionnez le laboratoire dans lequel vous souhaitez créer la machine virtuelle.  
4. Dans le panneau **Vue d’ensemble**, sélectionnez **+ Machine virtuelle**.  
    ![Bouton Ajouter une machine virtuelle](./media/devtest-lab-add-vm-with-artifacts/devtestlab-home-blade-add-vm.png)
5. Dans le panneau **Choisir une base** , sélectionnez une base pour la machine virtuelle.
6. Dans le panneau **Machine virtuelle**, entrez un nom pour la nouvelle machine virtuelle dans la zone de texte **Nom de la machine virtuelle**.
   
    ![Panneau Machine virtuelle de laboratoire](./media/devtest-lab-add-vm-with-artifacts/devtestlab-lab-vm-blade.png)
7. Entrez un **nom d'utilisateur** qui obtiendra les privilèges d'administrateur sur la machine virtuelle.  
8. Si vous souhaitez utiliser un mot de passe stocké dans votre *magasin de secrets*, sélectionnez **Utiliser les secrets de mon magasin de secrets**, et spécifiez une valeur de clé qui correspond à votre mot de passe. Dans le cas contraire, entrez simplement un mot de passe dans la zone de texte intitulée **Tapez une valeur**.
9. Sélectionnez **Taille de machine virtuelle** , puis l’un des éléments prédéfinis qui spécifient les cœurs du processeur, la taille de la RAM et la taille du disque dur de la machine virtuelle à créer.
10. Sélectionnez **Réseau virtuel** , puis le réseau virtuel souhaité.
11. Sélectionnez **Sous-réseau** , puis le sous-réseau.
12. Si la stratégie du laboratoire est définie de façon à autoriser des adresses IP publiques pour le sous-réseau sélectionné, spécifiez si vous voulez que l’adresse IP soit publique en sélectionnant **Oui** ou **Non**. Dans le cas contraire, cette option est désactivée et paramétrée sur **Non**. 
13. Sélectionnez **Artefacts** et, dans la liste des artefacts, sélectionnez et configurez les artefacts que vous souhaitez ajouter à l’image de base. 
    **Remarque :** si vous n’êtes pas familier avec DevTest Labs ou avec la configuration d’artefacts, passez à la section [Ajout d’un artefact existant à une machine virtuelle](#add-an-existing-artifact-to-a-vm) , puis revenez ici quand vous avez terminé.
14. Si vous voulez afficher ou copier le modèle Azure Resource Manager, passez à la section [Enregistrer un modèle Azure Resource Manager](#save-azure-resource-manager-template) , puis revenez ici quand vous avez terminé.
15. Sélectionnez **Créer** pour ajouter la machine virtuelle spécifiée au laboratoire.
16. Le panneau du laboratoire affiche l’état de la création de la machine virtuelle ; premièrement comme **Création en cours**, puis comme **En cours d’exécution** après le démarrage de la machine virtuelle.
17. Accédez à la section [Étapes suivantes](#next-steps) . 

## <a name="add-an-existing-artifact-to-a-vm"></a>Ajout d’un artefact existant à une machine virtuelle
Au cours de la création d’une machine virtuelle, vous pouvez ajouter des artefacts existants. Chaque laboratoire comprend des artefacts provenant du dépôt d’artefacts DevTest Labs public, ainsi que les artefacts que vous avez créés et ajoutés à votre propre dépôt d’artefacts.
Pour découvrir comment créer des artefacts, consultez l’article [Découvrez comment créer vos propres artefacts pour les utiliser avec DevTest Labs](devtest-lab-artifact-author.md).

1. Dans le panneau **Machine virtuelle**, sélectionnez **Artefacts**. 
2. Dans le panneau **Ajouter des artefacts** , sélectionnez l’artefact souhaité.  
   
    ![Volet Ajouter des artefacts](./media/devtest-lab-add-vm-with-artifacts/devtestlab-add-artifact-blade.png)
3. Entrez les valeurs de paramètres requises et les paramètres facultatifs dont vous avez besoin.  
4. Sélectionnez **Ajouter** pour ajouter l’artefact et revenir au panneau **Ajouter des artefacts**.
5. Continuez à ajouter des artefacts en fonction des besoins de votre machine virtuelle.
6. Une fois que vous avez ajouté vos artefacts, vous pouvez [modifier l’ordre dans lequel les artefacts sont exécutés](#change-the-order-in-which-artifacts-are-run). Vous pouvez également revenir à [Afficher ou modifier un artefact](#view-or-modify-an-artifact).

## <a name="change-the-order-in-which-artifacts-are-run"></a>Modification de l'ordre dans lequel les artefacts sont exécutés
Par défaut, les actions des artefacts sont exécutées dans l'ordre dans lequel ceux-ci sont ajoutés à la machine virtuelle. Les étapes suivantes montrent comment modifier l’ordre dans lequel les artefacts sont exécutés.

1. En haut du panneau **Ajouter des artefacts** , sélectionnez le lien indiquant le nombre d’artefacts ajoutés à la machine virtuelle.
   
    ![Nombre d’artefacts ajoutés à la machine virtuelle](./media/devtest-lab-add-vm-with-artifacts/devtestlab-add-artifacts-blade-selected-artifacts.png)
2. Pour spécifier l’ordre dans lequel les artefacts sont exécutés, faites-les glisser dans l’ordre souhaité. **Remarque :** si vous avez des difficultés à faire glisser l’artefact, vérifiez que vous effectuez l’opération depuis le côté gauche de l’artefact. 
3. Cliquez sur **OK** quand vous avez terminé.  

## <a name="view-or-modify-an-artifact"></a>Afficher ou modifier un artefact
Les étapes suivantes montrent comment afficher ou modifier les paramètres d’un artefact :

1. En haut du panneau **Ajouter des artefacts** , sélectionnez le lien indiquant le nombre d’artefacts ajoutés à la machine virtuelle.
   
    ![Nombre d’artefacts ajoutés à la machine virtuelle](./media/devtest-lab-add-vm-with-artifacts/devtestlab-add-artifacts-blade-selected-artifacts.png)
2. Dans le panneau **Artefacts sélectionnés** , sélectionnez l’artefact que vous voulez afficher ou modifier.  
3. Dans le panneau **Ajouter un artefact**, apportez les modifications nécessaires, puis cliquez sur **OK** pour fermer le panneau **Ajouter un artefact**.
4. Cliquez sur **OK** pour fermer le panneau **Artefacts sélectionnés**.

## <a name="save-azure-resource-manager-template"></a>Enregistrer un modèle Azure Resource Manager
Un modèle Azure Resource Manager constitue un moyen déclaratif de définir un déploiement qui peut être répété. Les étapes suivantes expliquent comment enregistrer le modèle Azure Resource Manager pour la machine virtuelle en cours de création.
Une fois enregistré, vous pouvez utiliser le modèle Azure Resource Manager pour [déployer de nouvelles machines virtuelles avec Azure PowerShell](../azure-resource-manager/resource-group-overview.md#template-deployment).

1. Dans le panneau **Machine virtuelle**, sélectionnez **Afficher le modèle ARM**.
2. Dans le panneau **Afficher le modèle Azure Resource Manager**, sélectionnez le texte du modèle.
3. Copiez le texte sélectionné dans le Presse-papiers.
4. Cliquez sur **OK** pour fermer le panneau **Afficher le modèle Azure Resource Manager**.
5. Ouvrez un éditeur de texte.
6. Collez le texte du modèle depuis le Presse-papiers.
7. Enregistrez le fichier pour une utilisation ultérieure.

[!INCLUDE [devtest-lab-try-it-out](../../includes/devtest-lab-try-it-out.md)]

## <a name="next-steps"></a>Étapes suivantes
* Une fois la machine virtuelle créée, vous pouvez vous y connecter en sélectionnant **Connexion** dans le panneau de la machine virtuelle.
* Découvrez comment [créer des artefacts personnalisés pour vos machines virtuelles DevTest Labs](devtest-lab-artifact-author.md).
* Explorer la [Galerie de modèles de démarrage rapide d’ARM DevTest Labs](https://github.com/Azure/azure-devtestlab/tree/master/ARMTemplates)




<!--HONumber=Nov16_HO3-->


