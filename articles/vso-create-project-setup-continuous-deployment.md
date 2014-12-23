<properties urlDisplayName="How to create a VSO project and setup Continuous Deployment" pageTitle="Création d'un projet d'équipe Visual Studio Online et configuration du déploiement continu - Windows Azure" metaKeywords="Création de projet d'équipe Visual Studio Online, déploiement continu sur Azure" description="Learn how to create a Visual Studio Online team project and configure it for continuous deployment to Windows Azure." metaCanonical="" services="cloud-services, visual-studio-online" documentationCenter="" title="How to Create and Deploy a Cloud Service" authors="jimlamb" solutions="" writer="jimlamb" manager="kamrani" editor=""  />

<tags ms.service="visual-studio-online" ms.workload="tbd" ms.tgt_pltfrm="ibiza" ms.devlang="na" ms.topic="article" ms.date="01/01/1900" ms.author="jimlamb" />

#Création d'un projet Visual Studio Online et configuration du déploiement continu sur Windows Azure 

[WACOM.INCLUDE [disclaimer](../includes/disclaimer.md)]

Le portail de gestion Windows Azure permet de créer un projet d'équipe sur Visual Studio Online et de configurer votre application web pour le déploiement continu sur un site web.

##Sommaire##

* [Création d'un projet d'équipe](#create_team_project)
* [Création d'une nouvelle application Web et son ajout au contrôle de version Git](#create_web_app)
* [Configuration du déploiement continu](#continuous_deployment)

## <a name="create_team_project"></a>Création d'un projet d'équipe

1. Connectez-vous au portail de gestion.
2. Cliquez sur **Nouveau** dans le coin gauche supérieur.
3. Cliquez sur **Projet d'équipe**
4. Nommez votre projet d'équipe. Gardez à l'esprit que vous ne pouvez pas modifier le nom d'un projet d'équipe une fois qu'il a été créé.
5. Sélectionnez le type de contrôle de version que vous souhaitez utiliser pour le projet. Vous pouvez choisir Git - un système de contrôle de version distribué - ou TFVC (Team Foundation Version Control) - un système de contrôle de version centralisé. Vous n'êtes pas certain du système à utiliser ? En savoir plus [ici](http://msdn.microsoft.com/fr-fr/library/ms181368.aspx).
6. Sélectionnez le modèle de processus. Pour une comparaison des modèles de processus, consultez la page [Utilisation d'artefacts de projet d'équipe](http://msdn.microsoft.com/fr-fr/library/ms400752.aspx).
7. Sélectionnez le compte Visual Studio Online à utiliser pour créer ce projet d'équipe, ajouter des utilisateurs et surveiller l'utilisation des ressources.
8. Cochez la case **Ajouter au tableau d'accueil** afin que votre nouveau projet d'équipe s'affiche automatiquement dans votre tableau d'accueil.
9. Cliquez sur **Créer**.

## <a name="create_web_app"></a>Création d'une nouvelle application Web et son ajout au contrôle de version Git

1. Depuis le tableau d'accueil, cliquez sur le nouveau projet d'équipe.
2. Dans le filtre **Code**, dans la partie **Référentiels**, cliquez sur le référentiel Git nommé d'après votre projet d'équipe.
3. Dans la lame de référentiel/branche, cliquez sur la commande de niveau lame **Visual Studio** afin d'ouvrir votre nouveau référentiel dans Visual Studio. Votre navigateur Web peut vous inviter à autoriser à lancer Visual Studio.
4.  Dans la fenêtre de l'outil Team Explorer de Visual Studio, cliquez sur **Cloner ce référentiel** pour configurer un clone local de votre nouveau référentiel sur votre disque local.
5.  Dans la section **Solutions** de la page d'accueil de Team Explorer, cliquez sur **Nouveau...** afin de créer un nouveau projet dans le référentiel que vous venez de cloner.
6.  Dans la boîte de dialogue Nouveau projet, développez le nœud Visual Basic ou Visual C#, selon votre préférence de langage de programmation, puis sélectionnez **Web**.
7.  Cliquez sur **Application Web ASP.NET** dans la liste des modèles de projet disponibles et saisissez un nom pour votre application Web.
8.  Cliquez sur **OK**.
9.  Passez à la fenêtre de l'outil Team Explorer, accédez à la page Modifications et saisissez un message de validation.
10.  Cliquez sur la flèche déroulante vers le bas du bouton **Valider**, puis cliquez sur **Valider et synchroniser** pour valider vos modifications et pousser cette validation vers le référentiel distant que vous avez cloné précédemment.

## <a name="continuous_deployment"></a>Configuration du déploiement continu

1. Connectez-vous au portail de gestion.
2. Depuis le tableau d'accueil, cliquez sur le projet d'équipe que vous avez créé antérieurement.
3. Dans le filtre **Générer**, cliquez sur la partie **Configurer le déploiement continu**.
4. Sélectionnez le site web vers lequel vous souhaitez déployer votre application web.
5. Sélectionnez le référentiel dans lequel votre code source se trouve (vous ne devriez avoir qu'un seul référentiel dans votre projet d'équipe à ce stade).
6. Sélectionnez la branche à générer. Visual Studio Online analyse le contenu de la validation la plus récente sur cette branche pour un fichier solution unique Visual Studio (*.sln). S'il en trouve un, il va configurer une nouvelle définition de build (portant un nom se terminant par " _CD ") pour générer cette solution. S'il ne trouve pas de fichier solution, ou s'il en trouve plusieurs, il configure tout de même une nouvelle définition de build mais elle sera désactivée et vous devrez la modifier dans Visual Studio afin d'indiquer la solution à générer. 
7. Cliquez sur **Créer**.
8. Visual Studio Online crée une nouvelle définition de build afin de générer et déployer votre projet d'application Web et tente de mettre en file d'attente un nouveau build de cette définition.

###Pour vérifier la réussite du déploiement###

1. Depuis le tableau d'accueil, cliquez sur le projet d'équipe que vous avez créé antérieurement.
2. Dans le filtre **Build**, cliquez sur **Build la plus récente** afin d'ouvrir la lame du build.
3. Dans la lame du build, cliquez sur le premier élément de la partie **Déploiements** pour ouvrir le site Web associé.
4. Sur la lame du site Web, cliquez sur la commande de niveau lame **Parcourir** afin de parcourir le site Web et de vérifier le déploiement de votre application Web.
