<properties
	pageTitle="Ajouter un dépôt d’artefacts Git à un laboratoire | Microsoft Azure"
	description="Ajouter un dépôt GitHub ou Visual Studio Team Services Git pour vos artefacts personnalisés dans DevTest Labs"
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
	ms.date="06/01/2016"
	ms.author="tarcher"/>

# Ajouter un dépôt d’artefacts Git à un laboratoire

> [AZURE.NOTE] Regardez la vidéo d’accompagnement de cet article : [How to add your private artifacts repository to a lab in DevTest Labs](/documentation/videos/how-to-add-your-private-artifacts-repository-in-a-devtest-lab) (Comment ajouter votre dépôt d’artefacts privé à un laboratoire dans DevTest Labs)

## Vue d'ensemble

Par défaut, un laboratoire comprend des artefacts du dépôt d’artefacts Azure DevTest Labs officiel. Vous pouvez ajouter un dépôt d’artefacts Git à votre laboratoire pour inclure les artefacts créés par votre équipe. Le dépôt peut être hébergé sur [GitHub](https://github.com) ou sur [Visual Studio Team Services (VSTS)](https://visualstudio.com).

- Pour découvrir comment créer un dépôt GitHub, consultez [GitHub Bootcamp](https://help.github.com/categories/bootcamp/).
- Pour découvrir comment créer un projet Team Services avec un dépôt Git, consultez [Se connecter à Visual Studio Services](https://www.visualstudio.com/get-started/setup/connect-to-visual-studio-online).

La capture d’écran suivante montre un exemple de dépôt contenant des artefacts dans GitHub : ![Exemple de dépôt d’artefacts GitHub](./media/devtest-lab-add-artifact-repo/devtestlab-github-artifact-repo-home.png)

## Ajouter un dépôt d’artefacts GitHub à votre laboratoire

Pour ajouter un dépôt d’artefacts GitHub à votre laboratoire, vous devez d’abord obtenir l’URL de clonage HTTPS et le jeton d’accès personnel à partir du dépôt d’artefacts, avant d’entrer ces informations dans votre laboratoire.

### Obtenir l’URL de clonage du dépôt GitHub et le jeton d’accès personnel

1. Sur la page d’accueil du dépôt GitHub contenant les artefacts de votre équipe, enregistrez l’**URL de clonage HTTPS** pour une utilisation ultérieure.

1. Sélectionnez l’image de profil dans le coin supérieur droit, puis sélectionnez **Paramètres**.

1. Dans le menu **Paramètres personnels** situé à gauche, sélectionnez **Jetons d’accès personnels**.

1. Sélectionnez **Générer un nouveau jeton**.

1. Sur la page **Nouveau jeton d’accès personnel** entrez une **Description du jeton**, acceptez les éléments par défaut dans le **Choix des étendues**, puis sélectionnez **Générer le jeton**.

1. Enregistrez le jeton généré, car vous en aurez besoin ultérieurement.

1. Vous pouvez à présent fermer GitHub.

###Connecter votre laboratoire au dépôt GitHub

1. Connectez-vous au [portail Azure](http://go.microsoft.com/fwlink/p/?LinkID=525040).

1. Sélectionnez **Parcourir**, puis **DevTest Labs** dans la liste.

1. Sélectionnez le laboratoire souhaité dans la liste des laboratoires.

1. Dans le panneau du laboratoire, sélectionnez **Paramètres**.

1. Dans le panneau **Paramètres** du laboratoire, sélectionnez le **Dépôt d’artefacts**.

1. Dans le panneau **Dépôt d’artefacts** :

    1. Entrez un **Nom** pour le dépôt.
    1. Entrez l’**URL de clonage Git** enregistrée.
    2. Entrez le **Chemin d’accès du dossier** dans le dépôt d’artefacts contenant les artefacts.
    3. Entrez le **Jeton d’accès personnel** enregistré dans le dépôt d’artefacts.
    4. Sélectionnez **Enregistrer**.

Les artefacts de votre dépôt sont désormais répertoriés dans le panneau **Ajouter des artefacts**.

## Ajout d’un dépôt d’artefacts Git Visual Studio à votre laboratoire

Pour ajouter un dépôt d’artefacts Git Visual Studio à votre laboratoire, vous devez d’abord obtenir l’URL de clonage HTTPS et le jeton d’accès personnel à partir du dépôt d’artefacts, avant d’entrer ces informations dans votre laboratoire.

### Sur la page web Visual Studio de votre projet d’artefact :

1. Ouvrez la page d’accueil de votre collection d’équipe (par exemple, `https://contoso-web-team.visualstudio.com`), puis sélectionnez le projet d’artefact.

2. Sur la page d’accueil du projet, sélectionnez **Code**.

1. Pour afficher l’URL de clonage, sélectionnez sur la page **Code** du projet l’option **Cloner**.

1. Enregistrez l’URL, car vous en aurez besoin ultérieurement dans ce didacticiel.

1. Pour créer un jeton d’accès personnel, sélectionnez **Mon profil** dans le menu déroulant du compte d’utilisateur.

1. Sur la page d’informations du profil, sélectionnez **Sécurité**.

1. Sous l’onglet **Sécurité**, sélectionnez **Ajouter**.

1. Sur la page **Créer un jeton d’accès personnel** :

    1. Entrez une **Description** pour le jeton.
    2. Dans la liste **Expire dans**, sélectionnez **180 jours**.
    3. Dans la liste **Comptes**, sélectionnez **Tous les comptes accessibles**.
    4. Sélectionnez l’option **Toutes les étendues**.
    5. Sélectionnez **Créer le jeton**.

1. Lorsque vous avez terminé, le nouveau jeton apparaît dans la liste **Jetons d’accès personnels**. Sélectionnez **Copier le jeton** puis enregistrez la valeur du jeton, car vous devrez l’utiliser sous peu.

### Dans le laboratoire

1. Dans le panneau du laboratoire, sélectionnez **Paramètres**.

    ![Sélectionner Paramètres](./media/devtest-lab-add-artifact-repo/devtestlab-add-artifacts-repo-open-dtl-settings.png)

1. Dans le panneau **Paramètres**, sélectionnez le **Dépôt d’artefacts**.

1. Dans le panneau **Dépôt d’artefacts**

    1. Entrez un **Nom** d’affichage pour le dépôt.
    1. Entrez l’**URL de clonage Git** enregistrée.
    2. Entrez le **Chemin d’accès du dossier** dans le dépôt d’artefacts contenant les artefacts.
    3. Entrez le **Jeton d’accès personnel** enregistré dans le dépôt d’artefacts.
    4. Sélectionnez **Enregistrer**.

## Billets de blog connexes
- [How to troubleshoot failing Artifacts in AzureDevTestLabs](http://www.visualstudiogeeks.com/blog/DevOps/How-to-troubleshoot-failing-artifacts-in-AzureDevTestLabs) (Comment résoudre les problèmes d’échec des artefacts dans AzureDevTestLabs)
- [Join a VM to existing AD Domain using ARM template in Azure Dev Test Lab](http://www.visualstudiogeeks.com/blog/DevOps/Join-a-VM-to-existing-AD-domain-using-ARM-template-AzureDevTestLabs) (Joindre une machine virtuelle à un domaine Active Directory existant à l’aide du modèle ARM dans Azure Dev Test Lab)

<!---HONumber=AcomDC_0803_2016-->