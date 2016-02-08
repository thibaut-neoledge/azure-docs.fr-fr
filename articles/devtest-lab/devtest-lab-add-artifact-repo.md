    <properties
	pageTitle="Add a Git artifact repository to your DevTest Lab | Microsoft Azure"
	description="Add a GitHub or Visual Studio Team Services Git repository for your custom artifacts to your lab"
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
	ms.date="11/01/2015"
	ms.author="tarcher"/>

# Ajouter un référentiel d’artefacts Git à votre instance du service DevTest Lab

## Vue d'ensemble

Par défaut, une instance de DevTest Lab inclut des artefacts à partir du référentiel d’artefacts Azure DevTest Lab officiel. Vous pouvez ajouter un référentiel d’artefacts Git à votre laboratoire pour inclure les artefacts créés par votre équipe. Le référentiel peut être hébergé sur [GitHub](https://github.com) ou sur [Visual Studio Team Services (VSTS)](https://visualstudio.com).

- Pour découvrir comment créer un référentiel GitHub, consultez [GitHub Bootcamp](https://help.github.com/categories/bootcamp/).
- Pour découvrir comment créer un projet Team Services avec un référentiel Git, consultez [Se connecter à Visual Studio Services](https://www.visualstudio.com/get-started/setup/connect-to-visual-studio-online).

La capture d’écran suivante montre un exemple de référentiel contenant des artefacts dans GitHub : ![Exemple de référentiel d’artefacts GitHub](./media/devtest-lab-add-artifact-repo/devtestlab-github-artifact-repo-home.png)

## Ajouter un référentiel d’artefacts GitHub à votre laboratoire

Pour ajouter un référentiel d’artefacts GitHub à votre laboratoire, vous devez d’abord obtenir l’URL de clonage HTTPS et le jeton d’accès personnel à partir du référentiel d’artefacts, avant d’entrer ces informations dans votre laboratoire.

### Obtenir l’URL de clonage du référentiel GitHub et le jeton d’accès personnel

1. Sur la page d’accueil du référentiel GitHub contenant les artefacts de votre équipe, enregistrez l’**URL de clonage HTTPS** pour une utilisation ultérieure.

1. Sélectionnez l’image de profil dans le coin supérieur droit, puis sélectionnez **Paramètres**.

1. Dans le menu **Paramètres personnels** situé à gauche, sélectionnez **Jetons d’accès personnels**.

1. Sélectionnez **Générer un jeton**.

1. Sur la page **Nouveau jeton d’accès personnel** entrez une **Description du jeton**, acceptez les éléments par défaut dans le **Choix des étendues**, puis sélectionnez **Générer le jeton**.

1. Enregistrez le jeton généré, car vous en aurez besoin ultérieurement.

1. Vous pouvez à présent fermer GitHub.

###Connecter votre laboratoire au référentiel GitHub

1. Connectez-vous au [portail Azure en version préliminaire](https://portal.azure.com).

1. Appuyez sur **Parcourir**, puis sur **DevTest Labs** dans la liste.

1. Dans la liste des laboratoires, appuyez sur le laboratoire souhaité.

1. Dans le panneau du laboratoire, sélectionnez **Paramètres**.

1. Dans le panneau **Paramètres** du laboratoire, sélectionnez **Référentiel d’artefacts**.

1. Dans le panneau **Référentiel d’artefacts** :

    1. Entrez un **Nom** pour le référentiel.
    1. Entrez l’**URL de clonage Git** enregistrée.
    2. Entrez le **Chemin d’accès du dossier** dans le référentiel d’artefacts contenant les artefacts.
    3. Entrez le **Jeton d’accès personnel** enregistré dans le référentiel d’artefacts.
    4. Appuyez sur **Enregistrer**.

Les artefacts de votre référentiel sont désormais répertoriés dans le panneau **Ajouter des artefacts**.

## Ajout d’un référentiel d’artefacts Git Visual Studio à votre laboratoire

Pour ajouter un référentiel d’artefacts Git Visual Studio à votre laboratoire, vous devez d’abord obtenir l’URL de clonage HTTPS et le jeton d’accès personnel à partir du référentiel d’artefacts, avant d’entrer ces informations dans votre laboratoire.

### Sur la page web Visual Studio de votre projet d’artefact :

1. Ouvrez la page d’accueil de votre collection d’équipe (par exemple, `https://contoso-web-team.visualstudio.com`), puis appuyez sur le projet d’artefact.

2. Sur la page d’accueil du projet, appuyez sur **Code**.

1. Pour afficher l’URL de clonage, sur la page **Code** du projet, appuyez sur **Cloner**.

1. Enregistrez l’URL, car vous en aurez besoin ultérieurement dans ce didacticiel.

1. Pour créer un jeton d’accès personnel, appuyez sur **Mon profil** dans le menu déroulant du compte d’utilisateur.

1. Sur la page d’informations du profil, appuyez sur **Sécurité**.

1. Sous l’onglet **Sécurité**, appuyez sur **Ajouter**.

1. Sur la page **Créer un jeton d’accès personnel** :

    1. Entrez une **Description** pour le jeton.
    2. Dans la liste **Expire dans**, sélectionnez **180 jours**.
    3. Dans la liste **Comptes**, sélectionnez **Tous les comptes accessibles**.
    4. Sélectionnez l’option **Toutes les étendues**.
    5. Sélectionnez **Créer le jeton**.

1. Lorsque vous avez terminé, le nouveau jeton apparaît dans la liste **Jetons d’accès personnels**. Sélectionnez **Copier le jeton** puis enregistrez la valeur du jeton, car elle vous sera utile sous peu.

### Sur la page du service DevTest Lab

1. Dans le panneau de votre laboratoire, appuyez sur **Paramètres**.

    ![Sélectionner Paramètres](./media/devtest-lab-add-artifact-repo/devtestlab-add-artifacts-repo-open-dtl-settings.png)

1. Dans le panneau **Paramètres**, appuyez sur **Référentiel d’artefacts**.

1. Dans le panneau **Référentiel d’artefacts**

    1. Entrez un **Nom** d’affichage pour le référentiel.
    1. Entrez l’**URL de clonage Git** enregistrée.
    2. Entrez le **Chemin d’accès du dossier** dans le référentiel d’artefacts contenant les artefacts.
    3. Entrez le **Jeton d’accès personnel** enregistré dans le référentiel d’artefacts.
    4. Appuyez sur **Enregistrer**.

<!---HONumber=AcomDC_0128_2016-->