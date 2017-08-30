---
title: "Diagnostiquer les échecs d’artefacts dans une machine virtuelle Azure DevTest Labs | Microsoft Docs"
description: "Découvrez comment résoudre les échecs des artefacts dans DevTest Labs."
services: devtest-lab,virtual-machines
documentationcenter: na
author: tomarcher
manager: douge
editor: 
ms.assetid: 115e0086-3293-4adf-8738-9f639f31f918
ms.service: devtest-lab
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 08/18/2017
ms.author: tarcher
ms.translationtype: HT
ms.sourcegitcommit: 25e4506cc2331ee016b8b365c2e1677424cf4992
ms.openlocfilehash: e4f2946d0ba0756f36622aded0e8594acabb9527
ms.contentlocale: fr-fr
ms.lasthandoff: 08/24/2017

---
# <a name="diagnose-artifact-failures-in-the-lab"></a>Diagnostiquer les échecs d’artefacts dans le labo 
Une fois que vous avez créé un artefact, vous pouvez vérifier si la procédure a réussi ou échoué. Les journaux d’artefact de DevTest Labs fournissent des informations qui permettent de diagnostiquer un échec d’artefact. Il existe plusieurs façons d’afficher les informations du journal de l’artefact pour une machine virtuelle Windows.

> [!NOTE]
> Pour que les échecs sont correctement identifiés et expliqués, il est important que l’artefact soit correctement structuré. Pour plus d’informations sur la bonne façon de créer un artefact, consultez la page [Créer des artefacts personnalisés](devtest-lab-artifact-author.md). Par ailleurs, l’artefact [Types de paramètres de test](https://github.com/Azure/azure-devtestlab/tree/master/Artifacts/windows-test-paramtypes) est un exemple d’artefact structuré correctement.

## <a name="troubleshoot-artifact-failures-using-the-azure-portal"></a>Résoudre les échecs des artefacts avec le Portail Azure
Pour utiliser le Portail Azure afin de diagnostiquer les échecs à la création d’un artefact, suivez les étapes ci-dessous :

1. Dans la liste de ressources, sélectionnez votre labo.

2. Choisissez la machine virtuelle Windows qui inclut l’artefact à analyser.

3. Dans le volet gauche sous **GÉNÉRAL**, choisissez **Artefacts**. La liste des artefacts associés à cette machine virtuelle s’affiche, indiquant le nom de l’artefact et son état.

   ![Exemple de dépôt git d'artefacts](./media/devtest-lab-troubleshoot-artifact-failure/devtest-lab-artifacts-failure.png)

4. Choisissez un artefact qui présente l’état **Échec**. L’artefact s’ouvre et affiche un message d’extension comportant des détails sur son échec.

   ![Exemple de dépôt git d'artefacts](./media/devtest-lab-troubleshoot-artifact-failure/devtest-lab-artifact-error.png)


## <a name="troubleshoot-artifact-failures-from-within-the-vm"></a>Résoudre les échecs des artefacts au sein de la machine virtuelle
Pour afficher les journaux d’artefacts au sein de la machine virtuelle, suivez les étapes ci-dessous :

1. Connectez-vous à la machine virtuelle qui contient l’artefact à diagnostiquer.

2. Accédez à C:\Packages\Plugins\Microsoft.Compute.CustomScriptExtension\1.9\Status, « 1.9 » étant le numéro de version CSE.

   ![Exemple de dépôt git d'artefacts](./media/devtest-lab-troubleshoot-artifact-failure/devtest-lab-artifact-error-vm-status.png)

3. Ouvrez le fichier **état** pour afficher les informations qui vous aideront à diagnostiquer les échecs d’artefacts de cette machine virtuelle.




[!INCLUDE [devtest-lab-try-it-out](../../includes/devtest-lab-try-it-out.md)]

## <a name="related-blog-posts"></a>Billets de blog connexes
* [Joindre une machine virtuelle à un domaine AD existant à l’aide d’un modèle Resource Manager dans Azure DevTest Labs](http://www.visualstudiogeeks.com/blog/DevOps/Join-a-VM-to-existing-AD-domain-using-ARM-template-AzureDevTestLabs)

## <a name="next-steps"></a>Étapes suivantes
* Découvrez comment [ajouter un référentiel Git à un labo](devtest-lab-add-artifact-repo.md).


