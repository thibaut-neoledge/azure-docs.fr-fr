---
title: "Gérer les formules dans Azure DevTest Labs pour créer des machines virtuelles | Microsoft Docs"
description: "Découvrez comment mettre à jour et supprimer des formules Azure DevTest Labs"
services: devtest-lab,virtual-machines
documentationcenter: na
author: tomarcher
manager: douge
editor: 
ms.assetid: 841dd95a-657f-4d80-ba26-59a9b5104fe4
ms.service: devtest-lab
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 03/07/2017
ms.author: tarcher
ms.custom: H1Hack27Feb2017
translationtype: Human Translation
ms.sourcegitcommit: a087df444c5c88ee1dbcf8eb18abf883549a9024
ms.openlocfilehash: fd334dc8cd22990456888146f41389054f2e8b29
ms.lasthandoff: 03/15/2017


---
# <a name="manage-azure-devtest-labs-formulas"></a>Gérer les formules Azure DevTest Labs

[!INCLUDE [devtest-lab-formula-definition](../../includes/devtest-lab-formula-definition.md)]

L’article [Create Azure DevTest Labs formulas](devtest-lab-create-formulas.md#create-a-formula) (Créer des formules Azure DevTest Labs) vous guide tout au long du processus de création d’une formule dans Azure DevTest Labs. Une fois que vous disposez d’une formule, cet article vous guide dans la gestion des formules.

## <a name="modify-a-formula"></a>Modifier une formule
Pour modifier une formule, procédez comme suit :

1. Connectez-vous au [portail Azure](http://go.microsoft.com/fwlink/p/?LinkID=525040).
2. Sélectionnez **Autres services**, puis **DevTest Labs** dans la liste.
3. Sélectionnez le laboratoire souhaité dans la liste des laboratoires.  
4. Dans le panneau du laboratoire, sélectionnez **Formules (bases réutilisables)**.
   
    ![Menu de formule](./media/devtest-lab-manage-formulas/lab-settings-formulas.png)
5. Dans le panneau des **formules de laboratoire** , sélectionnez la formule à modifier.
6. Dans le panneau **Mettre à jour la formule**, effectuez les modifications souhaitées, puis sélectionnez **Mettre à jour**.

## <a name="delete-a-formula"></a>Supprimer une formule
Pour supprimer une formule, procédez comme suit :

1. Connectez-vous au [portail Azure](http://go.microsoft.com/fwlink/p/?LinkID=525040).
2. Sélectionnez **Autres services**, puis **DevTest Labs** dans la liste.
3. Sélectionnez le laboratoire souhaité dans la liste des laboratoires.  
4. Dans le panneau **Paramètres** du laboratoire, sélectionnez **Formules**.
   
    ![Menu de formule](./media/devtest-lab-manage-formulas/lab-settings-formulas.png)
5. Dans le panneau des **formules de laboratoire** , sélectionnez les points de suspension à droite de la formule à supprimer.
   
    ![Menu de formule](./media/devtest-lab-manage-formulas/lab-formulas-blade.png)
6. Dans le menu contextuel de la formule, sélectionnez **Supprimer**.
   
    ![Menu contextuel de la formule](./media/devtest-lab-manage-formulas/formula-delete-context-menu.png)
7. sélectionnez **Oui** dans la boîte de dialogue de confirmation de la suppression.

[!INCLUDE [devtest-lab-try-it-out](../../includes/devtest-lab-try-it-out.md)]

## <a name="related-blog-posts"></a>Billets de blog connexes
* [Images personnalisées ou formules ?](https://blogs.msdn.microsoft.com/devtestlab/2016/04/06/custom-images-or-formulas/)

## <a name="next-steps"></a>Étapes suivantes
Quand vous avez créé une formule à utiliser lors de la création d’une machine virtuelle, l’étape suivante consiste à [ajouter une machine virtuelle à votre laboratoire](devtest-lab-add-vm-with-artifacts.md).


