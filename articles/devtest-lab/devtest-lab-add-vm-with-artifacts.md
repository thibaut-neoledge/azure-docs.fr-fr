---
title: "Gérer les artefacts de machine virtuelle dans Azure DevTest Labs | Microsoft Docs"
description: "Découvrez comment gérer les artefacts de machine virtuelle dans Azure DevTest Labs"
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
ms.date: 02/24/2017
ms.author: tarcher
translationtype: Human Translation
ms.sourcegitcommit: 2d298c210fa46cc4473fc7e8809e55bfc102481d
ms.openlocfilehash: c970bca8f6ab9e97741370f74efc5ee66e592468
ms.lasthandoff: 02/27/2017


---
# <a name="manage-vm-artifacts-in-azure-devtest-labs"></a>Gérer les artefacts de machine virtuelle dans Azure DevTest Labs
Les *artefacts* d’Azure DevTest Labs vous permettent de spécifier les *actions* qui sont exécutées quand la machine virtuelle est approvisionnée. 

Les actions des artefacts peuvent réaliser des procédures, telles que l’exécution de scripts Windows PowerShell, l’exécution de commandes Bash et l’installation de logiciels. 

Les *paramètres* des artefacts vous permettent de personnaliser l'artefact pour votre scénario spécifique.

Cet article vous explique comment gérer les artefacts d’une machine virtuelle dans votre laboratoire.

## <a name="add-an-existing-artifact-to-a-vm"></a>Ajout d’un artefact existant à une machine virtuelle
Au cours de la création d’une machine virtuelle, vous pouvez ajouter des artefacts existants. Chaque laboratoire comprend des artefacts provenant du dépôt d’artefacts DevTest Labs public, ainsi que les artefacts que vous avez créés et ajoutés à votre propre dépôt d’artefacts.
Pour découvrir comment créer des artefacts, consultez l’article [Découvrez comment créer vos propres artefacts pour les utiliser avec DevTest Labs](devtest-lab-artifact-author.md).

1. Connectez-vous au [portail Azure](http://go.microsoft.com/fwlink/p/?LinkID=525040).
1. Sélectionnez **Autres services**, puis **DevTest Labs** dans la liste.
1. Dans la liste des laboratoires, sélectionnez le laboratoire contenant la machine virtuelle avec laquelle vous souhaitez travailler.  
1. Sélectionnez **Mes machines virtuelles**.
1. Sélectionnez la machine virtuelle qui vous intéresse.
1. Sélectionnez **Artefacts**. 
1. Sélectionnez **Appliquer des artefacts**.
1. Dans le panneau **Appliquer des artefacts**, sélectionnez l’artefact que vous souhaitez ajouter à la machine virtuelle.
1. Dans le panneau **Ajouter un artefact**, entrez les valeurs de paramètre requises et tous les paramètres facultatifs dont vous avez besoin.  
1. Sélectionnez **Ajouter** pour ajouter l’artefact et revenir au panneau **Appliquer des artefacts**.
1. Continuez à ajouter des artefacts en fonction des besoins de votre machine virtuelle.
1. Une fois que vous avez ajouté vos artefacts, vous pouvez [modifier l’ordre dans lequel les artefacts sont exécutés](#change-the-order-in-which-artifacts-are-run). Vous pouvez également revenir à [Afficher ou modifier un artefact](#view-or-modify-an-artifact).
1. Lorsque vous avez terminé d’ajouter des artefacts, sélectionnez **Appliquer**.

## <a name="change-the-order-in-which-artifacts-are-run"></a>Modification de l'ordre dans lequel les artefacts sont exécutés
Par défaut, les actions des artefacts sont exécutées dans l'ordre dans lequel ceux-ci sont ajoutés à la machine virtuelle. Les étapes suivantes montrent comment modifier l’ordre dans lequel les artefacts sont exécutés.

1. En haut du panneau **Appliquer des artefacts**, sélectionnez le lien indiquant le nombre d’artefacts ajoutés à la machine virtuelle.
   
    ![Nombre d’artefacts ajoutés à la machine virtuelle](./media/devtest-lab-add-vm-with-artifacts/devtestlab-add-artifacts-blade-selected-artifacts.png)
1. Dans le panneau **Artefacts sélectionnés**, effectuez un glisser-déplacer sur les artefacts dans l’ordre souhaité. **Remarque :** si vous avez des difficultés à faire glisser l’artefact, vérifiez que vous effectuez cette opération à partir du côté gauche de l’artefact. 
1. Cliquez sur **OK** quand vous avez terminé.  

## <a name="view-or-modify-an-artifact"></a>Afficher ou modifier un artefact
Les étapes suivantes montrent comment afficher ou modifier les paramètres d’un artefact :

1. En haut du panneau **Appliquer des artefacts**, sélectionnez le lien indiquant le nombre d’artefacts ajoutés à la machine virtuelle.
   
    ![Nombre d’artefacts ajoutés à la machine virtuelle](./media/devtest-lab-add-vm-with-artifacts/devtestlab-add-artifacts-blade-selected-artifacts.png)
1. Dans le panneau **Artefacts sélectionnés**, sélectionnez l’artefact que vous voulez visualiser ou modifier.  
1. Dans le panneau **Ajouter un artefact**, apportez les modifications nécessaires, puis cliquez sur **OK** pour fermer le panneau **Ajouter un artefact**.
1. Cliquez sur **OK** pour fermer le panneau **Artefacts sélectionnés**.

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
* Découvrez comment [créer des artefacts personnalisés pour vos machines virtuelles DevTest Labs](devtest-lab-artifact-author.md).

