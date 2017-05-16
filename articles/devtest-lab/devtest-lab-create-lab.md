---
title: "Créer un laboratoire dans Azure DevTest Labs | Microsoft Docs"
description: "Créer un laboratoire dans Azure DevTest Labs pour les machines virtuelles"
services: devtest-lab,virtual-machines
documentationcenter: na
author: tomarcher
manager: douge
editor: 
ms.assetid: 8b6d3e70-6528-42a4-a2ef-449575d0f928
ms.service: devtest-lab
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: get-started-article
ms.date: 09/12/2016
ms.author: tarcher
translationtype: Human Translation
ms.sourcegitcommit: 8c4e33a63f39d22c336efd9d77def098bd4fa0df
ms.openlocfilehash: 75188a8f74b08d28b92259348626d5734ed4ce6c
ms.lasthandoff: 04/20/2017


---
# <a name="create-a-lab-in-azure-devtest-labs"></a>Créer un laboratoire dans Azure DevTest Labs
## <a name="prerequisites"></a>Composants requis
Pour créer un laboratoire, vous devez avoir :

* Un abonnement Azure. Pour en savoir plus sur les options d’achat d’Azure, consultez [Comment acheter Azure](https://azure.microsoft.com/pricing/purchase-options/) ou [Évaluation d’un mois gratuite](https://azure.microsoft.com/pricing/free-trial/). Pour créer le laboratoire, vous devez être le propriétaire de l’abonnement.

## <a name="steps-to-create-a-lab-in-azure-devtest-labs"></a>Étapes de création d’un laboratoire dans Azure DevTest Labs
Les étapes suivantes montrent comment utiliser le portail Azure pour créer un laboratoire dans Azure DevTest Labs. 

1. Connectez-vous au [portail Azure](http://go.microsoft.com/fwlink/p/?LinkID=525040).
2. Sélectionnez **Plus de services**, puis **DevTest Labs** dans la liste.
3. Dans le panneau **DevTest Labs**, sélectionnez **Ajouter**.
   
    ![Ajouter un laboratoire](./media/devtest-lab-create-lab/add-lab-button.png)
4. Dans le panneau **Créer un laboratoire de test et développement** :
   
   1. Entrez un **Nom de laboratoire** pour le nouveau laboratoire.
   2. Sélectionnez **l’abonnement** à associer au laboratoire.
   3. Sélectionnez un **Emplacement** dans lequel stocker le laboratoire.
   4. Sélectionnez **Arrêt automatique** pour spécifier si vous souhaitez activer l’arrêt automatique de toutes les machines virtuelles du laboratoire et en définir les paramètres. 
   5. Sélectionnez **Épingler au tableau de bord** si vous souhaitez faire apparaître un raccourci vers le laboratoire sur le tableau de bord du portail.
   6. Sélectionnez **Options d’automatisation** pour obtenir des modèles Azure Resource Manager pour l’automatisation de la configuration. 
   7. Sélectionnez **Créer**.
    
    ![Créer un panneau de laboratoire](./media/devtest-lab-create-lab/create-devtestlab-blade.png)

[!INCLUDE [devtest-lab-try-it-out](../../includes/devtest-lab-try-it-out.md)]

## <a name="next-steps"></a>Étapes suivantes
Une fois que vous avez créé votre laboratoire, voici quelques étapes à prendre en compte :

* [Sécuriser l’accès à un laboratoire](devtest-lab-add-devtest-user.md).
* [Définir des stratégies de laboratoire](devtest-lab-set-lab-policy.md).
* [Créer un modèle de laboratoire](devtest-lab-create-template.md).
* [Créer des artefacts personnalisés pour vos machines virtuelles](devtest-lab-artifact-author.md).
* [Ajouter une machine virtuelle avec des artefacts à un laboratoire](devtest-lab-add-vm-with-artifacts.md).


