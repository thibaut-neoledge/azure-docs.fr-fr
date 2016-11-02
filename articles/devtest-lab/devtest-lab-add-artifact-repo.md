<properties
	pageTitle="Ajouter un dépôt d’artefacts Git à un laboratoire dans Azure DevTest Labs | Microsoft Azure"
	description="Ajouter un dépôt GitHub ou Visual Studio Team Services Git pour vos sources d’artefacts personnalisés dans Azure DevTest Labs"
	services="devtest-lab,virtual-machines,visual-studio-online"
	documentationCenter="na"
	authors="tomarcher"
	manager="douge"
	editor=""/>

<tags
	ms.service="devtest-lab"
	ms.workload="na"
	ms.tgt_pltfrm="na"
	ms.devlang="na"
	ms.topic="article"
	ms.date="09/06/2016"
	ms.author="tarcher"/>

# Ajouter un dépôt d’artefacts Git à un laboratoire dans Azure DevTest Labs

> [AZURE.VIDEO how-to-add-your-private-artifacts-repository-in-a-devtest-lab]

Dans Azure DevTest Labs, les artefacts sont des *actions*, comme l’installation des logiciels ou l’exécution de scripts et de commandes, lors de la création d’une machine virtuelle. Par défaut, un laboratoire comprend des artefacts du dépôt d’artefacts Azure DevTest Labs officiel. Vous pouvez ajouter un dépôt d’artefacts Git à votre laboratoire pour inclure les artefacts créés par votre équipe. Le dépôt peut être hébergé sur [GitHub](https://github.com) ou sur [Visual Studio Team Services (VSTS)](https://visualstudio.com).

- Pour découvrir comment créer un dépôt GitHub, consultez [GitHub Bootcamp](https://help.github.com/categories/bootcamp/).
- Pour découvrir comment créer un projet Team Services avec un dépôt Git, consultez [Se connecter à Visual Studio Services](https://www.visualstudio.com/get-started/setup/connect-to-visual-studio-online).

La capture d’écran suivante montre un exemple de dépôt contenant des artefacts dans GitHub : ![Exemple de dépôt d’artefacts GitHub](./media/devtest-lab-add-artifact-repo/devtestlab-github-artifact-repo-home.png)


## Obtention des informations de référentiel et des informations d’identification

Pour ajouter un référentiel d’artefacts à votre laboratoire, vous devez d’abord obtenir certaines informations de votre référentiel. Les sections suivantes vous guident tout au long de l’obtention de ces informations pour les référentiels d’artefacts hébergés sur GitHub et Visual Studio Team Services.

### Obtenir l’URL de clonage du dépôt GitHub et le jeton d’accès personnel

Pour obtenir l’URL de clonage du dépôt GitHub et le jeton d’accès personnel, suivez ces étapes :

1. Accédez à la page d’accueil du référentiel GitHub qui contient les définitions d’artefacts.

1. Sélectionnez **Cloner ou télécharger**.

1. Cliquez sur le bouton pour copier **l’URL de clonage HTTPS** dans le presse-papiers, puis enregistrez l’URL pour une utilisation ultérieure.

1. Sélectionnez l’image de profil dans le coin supérieur droit de GitHub, puis sélectionnez **Paramètres**.

1. Dans le menu **Paramètres personnels** situé à gauche, sélectionnez **Jetons d’accès personnels**.

1. Sélectionnez **Générer un nouveau jeton**.

1. Sur la page **Nouveau jeton d’accès personnel** entrez une **Description du jeton**, acceptez les éléments par défaut dans le **Choix des étendues**, puis sélectionnez **Générer le jeton**.

1. Enregistrez le jeton généré, car vous en aurez besoin ultérieurement.

1. Vous pouvez à présent fermer GitHub.

1. Passez à la section [Connecter votre laboratoire au dépôt d’artefact](#connect-your-lab-to-the-artifact-repository).

### Obtenir l’URL de clonage du dépôt Visual Studio Team Services et le jeton d’accès personnel

Pour obtenir l’URL de clonage du dépôt Visual Studio Team Services et le jeton d’accès personnel, suivez ces étapes :

1. Ouvrez la page d’accueil de votre collection d’équipe (par exemple, `https://contoso-web-team.visualstudio.com`), puis sélectionnez le projet d’artefact.

1. Sur la page d’accueil du projet, sélectionnez **Code**.

1. Pour afficher l’URL de clonage, sélectionnez sur la page **Code** du projet l’option **Cloner**.

1. Enregistrez l’URL, car vous en aurez besoin ultérieurement dans ce didacticiel.

1. Pour créer un jeton d’accès personnel, sélectionnez **Mon profil** dans le menu déroulant du compte d’utilisateur.

1. Sur la page d’informations du profil, sélectionnez **Sécurité**.

1. Sous l’onglet **Sécurité**, sélectionnez **Ajouter**.

1. Sur la page **Créer un jeton d’accès personnel** :

    - Entrez une **Description** pour le jeton.
    - Dans la liste **Expire dans**, sélectionnez **180 jours**.
    - Dans la liste **Comptes**, sélectionnez **Tous les comptes accessibles**.
    - Sélectionnez l’option **Toutes les étendues**.
    - Sélectionnez **Créer le jeton**.

1. Lorsque vous avez terminé, le nouveau jeton apparaît dans la liste **Jetons d’accès personnels**. Sélectionnez **Copier le jeton** puis enregistrez la valeur du jeton pour utilisation ultérieure.

1. Passez à la section [Connecter votre laboratoire au dépôt d’artefact](#connect-your-lab-to-the-artifact-repository).

##Connecter votre laboratoire au dépôt d’artefact

1. Connectez-vous au [portail Azure](http://go.microsoft.com/fwlink/p/?LinkID=525040).

1. Sélectionnez **Plus de services**, puis **DevTest Labs** dans la liste.

1. Sélectionnez le laboratoire souhaité dans la liste des laboratoires.

1. Dans le panneau du laboratoire, sélectionnez **Configuration**.

1. Dans le panneau **Configuration** du laboratoire, sélectionnez le **Dépôts d’artefacts**.

1. Dans le panneau **Dépôts d’artefacts**, sélectionnez **+ Ajouter**.

	![Ajout du bouton de référentiel d’artefacts](./media/devtest-lab-add-artifact-repo/add-artifact-repo.png)
 
1. Dans le deuxième panneau **Référentiels d’artefacts**, spécifiez les éléments suivants :

    - **Nom** - Entrez un nom pour le dépôt.
    - **Url de clonage Git** - Entrez l’URL de clonage Git HTTPS que vous avez précédemment copiée à partir de GitHub ou Visual Studio Team Services.
    - **Chemin d’accès du dossier** - Entrez le chemin d’accès du dossier relatif à l’URL de clonage contenant vos définitions d’artefacts.
    - **Branche** - Entrez la branche pour obtenir vos définitions d’artefacts.
    - **Jeton d’accès personnel** - Entrez le jeton d’accès personnel obtenu précédemment à partir de GitHub ou Visual Studio Team Services.
     
	![Panneau du référentiel d’artefacts](./media/devtest-lab-add-artifact-repo/artifact-repo-blade.png)

1. Sélectionnez **Enregistrer**.

[AZURE.INCLUDE [devtest-lab-try-it-out](../../includes/devtest-lab-try-it-out.md)]

## Billets de blog connexes
- [How to troubleshoot failing Artifacts in AzureDevTestLabs (Comment résoudre les problèmes d’échec des artefacts dans AzureDevTestLabs)](http://www.visualstudiogeeks.com/blog/DevOps/How-to-troubleshoot-failing-artifacts-in-AzureDevTestLabs)
- [Join a VM to existing AD Domain using ARM template in Azure Dev Test Lab (Joindre une machine virtuelle à un domaine Active Directory existant à l’aide du modèle ARM dans Azure Dev Test Lab)](http://www.visualstudiogeeks.com/blog/DevOps/Join-a-VM-to-existing-AD-domain-using-ARM-template-AzureDevTestLabs)

<!---HONumber=AcomDC_0907_2016-->