---
title: "Ajouter un dépôt Git à un laboratoire dans Azure DevTest Labs | Microsoft Docs"
description: "Ajouter un dépôt GitHub ou Visual Studio Team Services Git pour vos sources d’artefacts personnalisés dans Azure DevTest Labs"
services: devtest-lab,virtual-machines,visual-studio-online
documentationcenter: na
author: tomarcher
manager: douge
editor: 
ms.assetid: 01b459f7-eaf2-45a8-b4b5-2c0a821b33c8
ms.service: devtest-lab
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 01/11/2017
ms.author: tarcher
ms.translationtype: HT
ms.sourcegitcommit: 25e4506cc2331ee016b8b365c2e1677424cf4992
ms.openlocfilehash: 053f92a65f9ae29154d471fd22ee842620b4f273
ms.contentlocale: fr-fr
ms.lasthandoff: 08/24/2017

---
# <a name="add-a-git-repository-to-store-custom-artifacts-and-azure-resource-manager-templates"></a>Ajouter un référentiel Git pour stocker des artefacts personnalisés et des modèles Azure Resource Manager

Si vous souhaitez [créer des artefacts personnalisés](devtest-lab-artifact-author.md) pour les machines virtuelles dans votre laboratoire, ou [utiliser des modèles Azure Resource Manager pour créer un environnement de test personnalisé](devtest-lab-create-environment-from-arm.md), vous devez également ajouter un référentiel Git privé afin d’inclure les artefacts ou modèles Azure Resource Manager que votre équipe crée. Le dépôt peut être hébergé sur [GitHub](https://github.com) ou sur [Visual Studio Team Services (VSTS)](https://visualstudio.com).

Nous avons fourni un [référentiel Github d’artefacts](https://github.com/Azure/azure-devtestlab/tree/master/Artifacts) que vous pouvez déployer tel quel ou personnaliser pour vos laboratoires. Lorsque vous personnalisez ou créez un artefact, vous ne pouvez pas le stocker dans le référentiel public. Vous devez créer votre propre référentiel privé. 

Lorsque vous créez une machine virtuelle, vous pouvez enregistrer le modèle Azure Resource Manager, le personnaliser éventuellement, puis l’utiliser pour créer facilement des machines virtuelles supplémentaires. Vous devez créer votre propre référentiel privé afin de stocker vos modèles Azure Resource Manager personnalisés.  

* Pour découvrir comment créer un dépôt GitHub, consultez [GitHub Bootcamp](https://help.github.com/categories/bootcamp/).
* Pour découvrir comment créer un projet Team Services avec un dépôt Git, consultez [Se connecter à Visual Studio Services](https://www.visualstudio.com/get-started/setup/connect-to-visual-studio-online).

La capture d’écran suivante montre un exemple de dépôt contenant des artefacts dans GitHub :   
![Exemple de dépôt d’artefacts GitHub](./media/devtest-lab-add-repo/devtestlab-github-artifact-repo-home.png)

## <a name="get-the-repository-information-and-credentials"></a>Obtention des informations de référentiel et des informations d’identification
Pour ajouter un référentiel à votre laboratoire, vous devez d’abord obtenir certaines informations de votre référentiel. Les sections suivantes vous guident tout au long de l’obtention de ces informations pour les référentiels hébergés sur GitHub et Visual Studio Team Services.

### <a name="get-the-github-repository-clone-url-and-personal-access-token"></a>Obtenir l’URL de clonage du dépôt GitHub et le jeton d’accès personnel
Pour obtenir l’URL de clonage du dépôt GitHub et le jeton d’accès personnel, suivez ces étapes :

1. Accédez à la page d’accueil du référentiel GitHub contenant l’artefact ou les définitions de modèle Azure Resource Manager.
2. Sélectionnez **Cloner ou télécharger**.
3. Cliquez sur le bouton pour copier **l’URL de clonage HTTPS** dans le presse-papiers, puis enregistrez l’URL pour une utilisation ultérieure.
4. Sélectionnez l’image de profil dans le coin supérieur droit de GitHub, puis sélectionnez **Paramètres**.
5. Dans le menu **Paramètres personnels** situé à gauche, sélectionnez **Jetons d’accès personnels**.
6. Sélectionnez **Générer un nouveau jeton**.
7. Dans la page **Nouveau jeton d’accès personnel** entrez une **Description du jeton**, acceptez les éléments par défaut dans le **Choix des étendues**, puis sélectionnez **Générer le jeton**.
8. Enregistrez le jeton généré, car vous en aurez besoin ultérieurement.
9. Vous pouvez à présent fermer GitHub.   
10. Passez à la section [Connecter votre laboratoire au référentiel](#connect-your-lab-to-the-repository).

### <a name="get-the-visual-studio-team-services-repository-clone-url-and-personal-access-token"></a>Obtenir l’URL de clonage du dépôt Visual Studio Team Services et le jeton d’accès personnel
Pour obtenir l’URL de clonage du dépôt Visual Studio Team Services et le jeton d’accès personnel, suivez ces étapes :

1. Ouvrez la page d’accueil de votre collection d’équipe (par exemple, `https://contoso-web-team.visualstudio.com`), puis sélectionnez votre projet.
2. Sur la page d’accueil du projet, sélectionnez **Code**.
3. Pour afficher l’URL de clonage, sélectionnez dans la page **Code** du projet l’option **Cloner**.
4. Enregistrez l’URL, car vous en aurez besoin ultérieurement dans ce didacticiel.
5. Pour créer un jeton d’accès personnel, sélectionnez **Mon profil** dans le menu déroulant du compte d’utilisateur.
6. Sur la page d’informations du profil, sélectionnez **Sécurité**.
7. Sous l’onglet **Sécurité**, sélectionnez **Ajouter**.
8. Sur la page **Créer un jeton d’accès personnel** :

   * Entrez une **Description** pour le jeton.
   * Dans la liste **Expire dans**, sélectionnez **180 jours**.
   * Dans la liste **Comptes**, sélectionnez **Tous les comptes accessibles**.
   * Sélectionnez l’option **Toutes les étendues** .
   * Sélectionnez **Créer le jeton**.
9. Lorsque vous avez terminé, le nouveau jeton apparaît dans la liste **Jetons d’accès personnels** . Sélectionnez **Copier le jeton**puis enregistrez la valeur du jeton pour utilisation ultérieure.
10. Passez à la section [Connecter votre laboratoire au référentiel](#connect-your-lab-to-the-repository).

## <a name="connect-your-lab-to-the-repository"></a>Connecter votre laboratoire au référentiel
1. Connectez-vous au [portail Azure](http://go.microsoft.com/fwlink/p/?LinkID=525040).
2. Sélectionnez **Autres services**, puis **DevTest Labs** dans la liste.
3. Sélectionnez le laboratoire souhaité dans la liste des laboratoires.   
4. Sur le panneau de gauche, sélectionnez **Configuration et stratégies**.
5. Dans la zone **Configuration et stratégies** du labo, sélectionnez **Référentiels**.
6. Dans la zone **Référentiels**, sélectionnez **+ Ajouter**.

    ![Bouton Ajouter un référentiel](./media/devtest-lab-add-repo/devtestlab-add-repo.png)
7. Sur la seconde page **Référentiels**, spécifiez les informations suivantes :

   * **Nom** - Entrez un nom pour le dépôt.
   * **Url de clonage Git** : entrez l’URL de clonage Git HTTPS que vous avez précédemment copiée à partir de GitHub ou Visual Studio Team Services.
   * **Branche** : entrez la branche pour obtenir vos définitions.
   * **Jeton d’accès personnel**: entrez le jeton d’accès personnel obtenu précédemment à partir de GitHub ou Visual Studio Team Services.
   * **Chemins de dossiers** : entrez au moins un chemin d’accès de dossier relatif à l’URL du clone contenant votre artefact ou vos définitions de modèle Azure Resource Manager. Lorsque vous spécifiez un sous-répertoire, veillez à inclure la barre oblique dans le chemin d’accès du dossier.

     ![Zone Référentiels](./media/devtest-lab-add-repo/devtestlab-repo-blade.png)
8. Sélectionnez **Enregistrer**.

## <a name="next-steps"></a>Étapes suivantes
Après avoir créé votre référentiel Git privé, vous pouvez effectuer au moins l’une des opérations suivantes, selon vos besoins :
* Stocker vos [artefacts personnalisés](devtest-lab-artifact-author.md) que vous pouvez utiliser ultérieurement pour créer des machines virtuelles.
* [Créer des environnements de plusieurs machines virtuelles et des ressources PaaS avec les modèles Azure Resource Manager](devtest-lab-create-environment-from-arm.md), puis stocker les modèles dans votre référentiel privé.

Lorsque vous créez une machine virtuelle, vous pouvez vérifier que les artefacts ou les modèles sont ajoutés à votre référentiel Git. Ils sont immédiatement disponibles dans la liste des artefacts ou des modèles ; le nom de votre référentiel privé s’affiche dans la colonne spécifiant la source. 

[!INCLUDE [devtest-lab-try-it-out](../../includes/devtest-lab-try-it-out.md)]

### <a name="related-blog-posts"></a>Billets de blog connexes
* [Guide pratique pour résoudre les échecs des artefacts dans Azure DevTest Labs](devtest-lab-troubleshoot-artifact-failure.md)
* [Joindre une machine virtuelle à un domaine AD existant à l’aide d’un modèle Resource Manager dans Azure DevTest Labs](http://www.visualstudiogeeks.com/blog/DevOps/Join-a-VM-to-existing-AD-domain-using-ARM-template-AzureDevTestLabs)

