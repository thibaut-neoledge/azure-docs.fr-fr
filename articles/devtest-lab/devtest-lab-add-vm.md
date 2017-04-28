---
title: "Ajouter une machine virtuelle à un laboratoire dans Azure DevTest Labs | Microsoft Docs"
description: "Découvrez comment ajouter une machine virtuelle à un laboratoire dans Azure DevTest Labs"
services: devtest-lab,virtual-machines
documentationcenter: na
author: tomarcher
manager: douge
editor: 
ms.assetid: 
ms.service: devtest-lab
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 02/24/2017
ms.author: tarcher
translationtype: Human Translation
ms.sourcegitcommit: db7cb109a0131beee9beae4958232e1ec5a1d730
ms.openlocfilehash: 9ac43f4ac0a58ae3326c63ab53259d7621577d34
ms.lasthandoff: 04/18/2017


---
# <a name="add-a-vm-to-a-lab-in-azure-devtest-labs"></a>Ajouter une machine virtuelle à un laboratoire dans Azure DevTest Labs
Vous ajoutez une machine virtuelle à un laboratoire à partir d’une *base* qui est une [image personnalisée](devtest-lab-create-template.md), une [formule](devtest-lab-manage-formulas.md) ou une [image de la plateforme Place de marché](devtest-lab-configure-marketplace-images.md). Ce didacticiel vous guide tout au long de l’utilisation du Portail Azure pour ajouter une machine virtuelle à un laboratoire dans DevTest Labs.

> [!NOTE]
> [Ajouter une machine virtuelle qui peut être revendiquée](devtest-lab-add-claimable-vm.md) vous montre comment créer une machine virtuelle exigible afin qu’elle soit disponible pour tout utilisateur dans le laboratoire.
>
>

## <a name="steps-to-add-a-vm-to-a-lab-in-azure-devtest-labs"></a>Procédure d’ajout d’une machine virtuelle à un laboratoire dans Azure DevTest Labs
1. Connectez-vous au [portail Azure](http://go.microsoft.com/fwlink/p/?LinkID=525040).
1. Sélectionnez **Autres services**, puis **DevTest Labs** dans la liste.
1. Dans la liste des laboratoires, sélectionnez le laboratoire dans lequel vous souhaitez créer la machine virtuelle.  
1. Dans le panneau **Vue d’ensemble** du laboratoire, sélectionnez **+ Ajouter**.  

    ![Ajout du bouton de machine virtuelle](./media/devtest-lab-add-vm/devtestlab-home-blade-add-vm.png)

1. Dans le panneau **Choisir une base** , sélectionnez une base pour la machine virtuelle.
1. Dans le panneau **Machine virtuelle**, entrez un nom pour la nouvelle machine virtuelle dans la zone de texte **Nom de la machine virtuelle**.

    ![Panneau Machine virtuelle de laboratoire](./media/devtest-lab-add-vm/devtestlab-lab-vm-blade.png)

1. Entrez un **nom d’utilisateur** qui obtient les privilèges d’administrateur sur la machine virtuelle.  
1. Si vous souhaitez utiliser un mot de passe stocké dans votre [magasin des secrets](https://azure.microsoft.com/updates/azure-devtest-labs-keep-your-secrets-safe-and-easy-to-use-with-the-new-personal-secret-store), sélectionnez **Use a saved secret** (Utiliser un secret enregistré), et spécifiez une valeur de clé correspondant à votre secret (mot de passe). Dans le cas contraire, entrez un mot de passe dans le champ de texte intitulé **Tapez une valeur**.
1. Le **type de disque de machine virtuelle** détermine le type de disque de stockage autorisé pour les machines virtuelles dans le laboratoire.
1. Sélectionnez **Taille de machine virtuelle** , puis l’un des éléments prédéfinis qui spécifient les cœurs du processeur, la taille de la RAM et la taille du disque dur de la machine virtuelle à créer.
1. Sélectionnez **Artefacts** et, dans la liste des artefacts, sélectionnez et configurez les artefacts que vous souhaitez ajouter à l’image de base.
    **Remarque :** si vous n’êtes pas familier avec DevTest Labs ou avec la configuration d’artefacts, reportez-vous à la section [Ajout d’un artefact existant à une machine virtuelle](./devtest-lab-add-vm-with-artifacts.md#add-an-existing-artifact-to-a-vm), puis reprenez la procédure à ce stade.
1. Sélectionnez **Paramètres avancés** pour configurer les options réseau et les options d’expiration de la machine virtuelle. 

   Pour définir une option d’expiration, choisissez l’icône de calendrier pour spécifier la date à laquelle la machine virtuelle doit être automatiquement supprimée.  Par défaut, la machine virtuelle n’expire jamais. 
1. Si vous voulez visualiser ou copier le modèle Azure Resource Manager, reportez-vous à la section [Enregistrer un modèle Azure Resource Manager](./devtest-lab-add-vm-with-artifacts.md#save-azure-resource-manager-template), puis reprenez la procédure à ce stade.
1. Sélectionnez **Créer** pour ajouter la machine virtuelle spécifiée au laboratoire.
1. Le panneau du laboratoire affiche l’état de la création de la machine virtuelle, tout d’abord sous la forme **Création en cours**, puis sous la forme **En cours d’exécution** après le démarrage de la machine virtuelle.

## <a name="next-steps"></a>Étapes suivantes
* Une fois la machine virtuelle créée, vous pouvez vous y connecter en sélectionnant **Connexion** dans le panneau de la machine virtuelle.
* Explorez la [Galerie de modèles de démarrage rapide d’Azure Resource Manager DevTest Labs](https://github.com/Azure/azure-devtestlab/tree/master/ARMTemplates).

