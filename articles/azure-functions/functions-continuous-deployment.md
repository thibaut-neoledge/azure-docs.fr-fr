<properties
   pageTitle="Déploiement continu pour Azure Functions | Microsoft Azure"
   description="Utilisez les fonctionnalités de déploiement continu d’Azure App Service pour publier vos fonctions Azure."
   services="azure-functions"
   documentationCenter="na"
   authors="ggailey777"
   manager="erikre"
   editor=""
   tags=""
   />

<tags
   ms.service="functions"
   ms.devlang="multiple"
   ms.topic="article"
   ms.tgt_pltfrm="multiple"
   ms.workload="na"
   ms.date="08/15/2016"
   ms.author="glenga"/>

# Déploiement continu pour Azure Functions 

Avec Azure Functions, configurer le déploiement continu de votre application de fonction n’a jamais été aussi facile. Functions utilise l’intégration d’Azure App Service à BitBucket, Dropbox, GitHub et Visual Studio Team Services (VSTS) pour permettre un workflow de déploiement continu au cours duquel Azure extrait les mises à jour du code de vos fonctions lorsqu’elles sont publiées sur l’un de ces services. Si vous ne connaissez pas Azure Functions, commencez par consulter l’article [Vue d’ensemble d’Azure Functions](functions-overview.md).

Le déploiement continu est une option intéressante pour les projets auxquels plusieurs contributions fréquentes sont intégrées. Il vous permet également de conserver le contrôle de code source sur le code de vos fonctions. Les sources de déploiement actuellement prises en charge sont les suivantes :

+ [Bitbucket](https://bitbucket.org/)
+ [Dropbox](https://bitbucket.org/)
+ [Référentiel Git local](../app-service-web/app-service-deploy-local-git.md)
+ Référentiel Git externe
+ [GitHub]
+ Référentiel Mercurial externe
+ [OneDrive](https://onedrive.live.com/)
+ Visual Studio Team Services

Les déploiements sont configurés au cas par cas, selon les applications de fonction. Une fois le déploiement continu activé, l’accès au code de fonction dans le portail est défini sur *lecture seule*.

## Conditions requises pour le déploiement continu

Avant que vous ne configuriez le déploiement continu, votre source de déploiement doit être configurée et contenir votre code de fonctions. Dans un déploiement d’application de fonction donnée, chaque fonction se trouve dans un sous-répertoire nommé, où le nom du répertoire est le nom de la fonction. Cette structure de dossier correspond essentiellement au code de votre site.

[AZURE.INCLUDE [structure de dossier de fonctions](../../includes/functions-folder-structure.md)]

## Configurer un déploiement continu

Utilisez la procédure suivante pour configurer le déploiement continu d’une application de fonction existante :

1. Dans votre application de fonction dans le [portail Azure Functions](https://functions.azure.com/signin), cliquez sur **Function app settings** (Paramètres de l’application de fonction) > **Configurer l’intégration continue** > **Installation**.

	![Configurer un déploiement continu](./media/functions-continuous-deployment/setup-deployment.png)

	Vous pouvez également accéder au panneau Déploiements à partir du démarrage rapide des fonctions en cliquant sur **Start from source control** (Démarrer à partir du contrôle de code source).

2. Dans le panneau Déploiements, cliquez sur **Choisir une source**, renseignez les informations pour la source de déploiement que vous avez choisie, puis cliquez sur **OK**.

	![Choisir une source de déploiement](./media/functions-continuous-deployment/choose-deployment-source.png)

Une fois le déploiement continu configuré, tous les fichiers de modifications dans votre source de déploiement sont copiés vers l’application de fonction et un déploiement complet de site est déclenché. Le site est redéployé lorsque les fichiers de la source sont mis à jour.


##Options de déploiement

Voici quelques scénarios de déploiement classiques :

###Créer un déploiement intermédiaire

Function Apps ne prend pas encore en charge les emplacements de déploiement. Toutefois, vous pouvez tout de même gérer des déploiements intermédiaires et de production distincts à l’aide de l’intégration continue.

Voici à quoi ressemble généralement le processus pour configurer et travailler avec un déploiement intermédiaire :

1. Créez deux applications de fonction dans votre abonnement, une pour le code de production et une pour le code intermédiaire.

2. Créez une source de déploiement, si vous n’en avez pas déjà. Nous allons utiliser [GitHub].
 
3. Pour votre application de fonction de production, effectuez les étapes décrites ci-dessus dans la section **Configurer un déploiement continu** et définissez la branche de déploiement sur la branche principale de votre référentiel GitHub.

	![Choisir une branche de déploiement](./media/functions-continuous-deployment/choose-deployment-branch.png)

4. Répétez cette étape pour l’application de fonction intermédiaire, mais cette fois, sélectionnez la branche intermédiaire de votre référentiel GitHub. Si votre source de déploiement ne prend pas en charge la création de branches, utilisez un autre dossier.
 
5. Mettez à jour votre code dans le dossier ou la branche intermédiaire, puis vérifiez que ces modifications sont répercutées dans le déploiement intermédiaire.

6. Après vérification, fusionnez les modifications de la branche intermédiaire dans la branche principale. Cette opération déclenche le déploiement de l’application de fonction de production. Si votre source de déploiement ne prend pas en charge les branches, remplacez les fichiers dans le dossier de production par les fichiers du dossier intermédiaire.

###Déplacer des fonctions existantes pour un déploiement continu

Lorsque vous disposez de fonctions existantes que vous avez créées et conservées dans le portail, vous devez télécharger vos fichiers de code de fonction existante à l’aide du FTP ou du référentiel Git local avant de pouvoir configurer le déploiement continu comme décrit ci-dessus. Vous pouvez le faire dans les paramètres App Service pour votre application de fonction. Une fois que vos fichiers sont téléchargés, vous pouvez les télécharger vers la source de déploiement continu que vous avez choisie.

>[AZURE.NOTE]Après avoir configuré l’intégration continue, vous ne serez plus en mesure de modifier vos fichiers sources dans le portail Functions.

####Comment configurer les informations d’identification de déploiement
Avant de pouvoir télécharger des fichiers à partir de votre application de fonction, vous devez configurer vos informations d’identification afin d’accéder au site, ce que vous pouvez faire à partir du portail. Les informations d’identification sont définies au niveau de l’application de fonction.

1. Dans votre application de fonction dans le [portail Azure Functions](https://functions.azure.com/signin), cliquez sur **Paramètres Function App** > **Accéder aux paramètres App Service** > **Informations d’identification du déploiement**.

	![Définir les informations d’identification de déploiement local](./media/functions-continuous-deployment/setup-deployment-credentials.png)

2. Entrez un nom d’utilisateur et un mot de passe, puis cliquez sur **Enregistrer**. Vous pouvez désormais utiliser ces informations d’identification pour accéder à votre application de fonction à partir du FTP ou du référentiel Git intégré.

####Comment télécharger des fichiers via le FTP

1. Dans votre application de fonction dans le [portail de Azure Functions](https://functions.azure.com/signin), cliquez sur **Paramètres de l’application de fonction** > **Accéder aux paramètres App Service** > **Propriétés**, puis copiez les valeurs de **Nom d’utilisateur FTP/déploiement**, **Nom d’hôte FTP**, et **Nom d’hôte FTPS**. La valeur **Utilisateur FTP/déploiement** doit être entrée telle qu’elle est affichée dans le portail et inclure le nom de l’application afin de fournir le contexte approprié pour le serveur FTP.

	![Obtenir vos informations de déploiement](./media/functions-continuous-deployment/get-deployment-credentials.png)
    
2. À partir de votre client FTP, utilisez les informations de connexion que vous avez recueillies pour vous connecter à votre application et télécharger les fichiers sources pour vos fonctions.

####Comment télécharger des fichiers via le référentiel Git local

1. Dans votre application de fonction dans le [portail Azure Functions](https://functions.azure.com/signin), cliquez sur **Function app settings** (Paramètres de l’application de fonction) > **Configurer l’intégration continue** > **Installation**.

2. Dans le panneau Déploiements, cliquez sur **Choisir une source** > **Référentiel Git local**, puis cliquez sur **OK**.
 
3. Cliquez sur **Accéder aux paramètres du App Service** > **Propriétés** et notez la valeur de l’URL Git.
    
	![Configurer un déploiement continu](./media/functions-continuous-deployment/get-local-git-deployment-url.png)

4. Clonez le référentiel sur votre ordinateur local à l’aide d’une ligne de commande prenant en charge Git ou de votre outil Git favori. La commande de clone Git se présente comme suit :

		git clone https://username@my-function-app.scm.azurewebsites.net:443/my-function-app.git

5. Récupérez les fichiers à partir de votre application de fonction sur le clone de votre ordinateur local, comme dans l’exemple suivant :

		git pull origin master

	Si nécessaire, fournissez le nom d’utilisateur et le mot de passe pour le déploiement de votre application de fonction.


[GitHub]: https://github.com/

<!---HONumber=AcomDC_0824_2016-->