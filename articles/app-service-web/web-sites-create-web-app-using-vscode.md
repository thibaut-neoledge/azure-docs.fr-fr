<properties
   pageTitle="Création d’une application Web ASP.NET 5 dans Visual Studio Code"
   description="Ce didacticiel vous indique comment créer une application Web ASP.NET 5 via Visual Studio Code."
   services="app-service\web"
   documentationCenter=".net"
   authors="erikre"
   manager="wpickett"
   editor="jimbe"/>

<tags
	ms.service="app-service-web" 
	ms.workload="web" 
	ms.tgt_pltfrm="dotnet" 
	ms.devlang="na" 
	ms.topic="article" 
	ms.date="02/26/2016" 
	ms.author="cephalin"/>

# Création d’une application Web ASP.NET 5 dans Visual Studio Code

## Vue d'ensemble

Ce didacticiel explique comment créer une application Web ASP.NET 5 à l’aide de [Visual Studio Code (VS Code)](http://code.visualstudio.com//Docs/whyvscode) et comment le déployer dans [Azure App Service](../app-service/app-service-value-prop-what-is.md).

> [AZURE.NOTE] Même si cet article fait référence aux applications Web, il s’applique également aux applications API et aux applications mobiles.

ASP.NET 5 est une nouvelle conception significative d'ASP.NET. ASP.NET 5 est une nouvelle infrastructure open source et interplateforme qui vous permet de créer des applications Web modernes basées sur le cloud à l'aide de .NET. Pour plus d’informations, consultez la page [Présentation d’ASP.NET 5](http://docs.asp.net/en/latest/conceptual-overview/aspnet.html). Pour plus d'informations sur les applications Web Azure App Service, consultez la [Vue d'ensemble de Web Apps](app-service-web-overview.md).

[AZURE.INCLUDE [app-service-web-try-app-service.md](../../includes/app-service-web-try-app-service.md)]

## Composants requis  

* Installation de [VS Code](http://code.visualstudio.com/Docs/setup).
* Installation de [Node.js](http://nodejs.org) : Node.js est une plateforme permettant la création d’applications de serveur rapides et évolutives à l’aide de JavaScript. Node est le runtime (Node), et [npm](http://www.npmjs.com/) est le gestionnaire de package pour les modules Node. Vous utiliserez npm pour structurer une application Web ASP.NET 5 dans ce didacticiel.
* Installation de Git - vous pouvez l'installer depuis l’un de ces emplacements : [Chocolatey](https://chocolatey.org/packages/git) ou [git-scm.com](http://git-scm.com/downloads). Si vous n’êtes pas familiarisé avec Git, choisissez [git-scm.com](http://git-scm.com/downloads) et sélectionnez l’option vous permettant d’**utiliser Git à partir de l’invite de commandes Windows**. Une fois Git installé, vous devrez également définir le nom d'utilisateur et l’adresse de messagerie Git qui vous seront ultérieurement demandés dans ce didacticiel (lorsque vous effectuerez une validation à partir de VS Code).

## Installer ASP.NET 5 et DNX
ASP.NET 5/DNX (l'environnement d'exécution .NET) est une pile .NET lean vous permettant de créer des applications web et cloud modernes capables de s'exécuter sur OS X, Linux et Windows. Elle a été construite intégralement pour fournir une infrastructure de développement optimisée pour les applications qui sont déployées sur le cloud ou qui sont exécutées en local. Elle inclut des composants modulaires associés à des frais généraux réduits. Ainsi, vous bénéficiez d’une certaine flexibilité lors de la création de vos solutions.

Ce didacticiel est conçu pour vous aider à créer des applications à l’aide des dernières versions de développement d’ ASP.NET 5 et de DNX. Les instructions suivantes sont spécifiques à Windows. Pour accéder à des instructions plus détaillées sur l’installation de cette fonction sur des systèmes OS X, Linux et Windows, consultez la rubrique [Installation d’ASP.NET 5 et de DNX](https://code.visualstudio.com/Docs/ASPnet5#_installing-aspnet-5-and-dnx).

1. Pour installer le gestionnaire de version .NET (DNVM) dans Windows, ouvrez une invite de commandes et exécutez la commande suivante.

		@powershell -NoProfile -ExecutionPolicy unrestricted -Command "&{$Branch='dev';iex ((new-object net.webclient).DownloadString('https://raw.githubusercontent.com/aspnet/Home/dev/dnvminstall.ps1'))}"

	Ceci téléchargera le script DNVM et le placera dans le répertoire de votre profil utilisateur.

2. **Redémarrez Windows** pour terminer l'installation de DNVM.

	Après le redémarrage de Windows, vous pouvez ouvrir l'invite de commandes pour vérifier l'emplacement de DNVM en entrant la commande suivante :

		where dnvm

	L'invite de commandes affichera un chemin d'accès semblable à celui-ci :

	![Emplacement de DNVM](./media/web-sites-create-web-app-using-vscode/00-where-dnvm.png)

3. Maintenant que vous disposez de DNVM, vous devez l'utiliser pour télécharger DNX afin d'exécuter vos applications. Exécutez ensuite la commande suivante dans l'invite de commandes :

		dnvm upgrade

	Vérifiez votre DNVM et affichez le runtime actif en entrant la commande suivante dans l'invite de commandes :

		dnvm list

	L'invite de commandes affichera les détails du runtime actif.

	![Emplacement de DNVM](./media/web-sites-create-web-app-using-vscode/00b-dnvm-list.png)

	Si plusieurs runtimes DNX sont répertoriés, vous pouvez choisir d'entrer les informations suivantes (ou une version plus récente) dans l'invite de commandes pour définir le runtime DNX actif. Définissez-le sur la même version que celle utilisée par le générateur ASP.NET 5 lors de la création de votre application web, plus loin dans ce didacticiel. *Il ne sera peut-être pas nécessaire de modifier l'exécution active si elle est définie sur la dernière version disponible.*

		dnvm use 1.0.0-update1 –p

> [AZURE.NOTE] Pour accéder à des instructions plus détaillées sur l’installation de cette fonction sur des systèmes OS X, Linux et Windows, consultez la rubrique [Installation d’ASP.NET 5 et de DNX](https://code.visualstudio.com/Docs/ASPnet5#_installing-aspnet-5-and-dnx).

## Création de l'application Web 

Cette section vous indique comment structurer une nouvelle application Web ASP.NET. Vous utiliserez le gestionnaire de package Node (npm) pour installer [Yeoman](http://yeoman.io/) (outil de structuration d'applications - l'équivalent VS Code de l’opération **Fichier > Nouveau projet** de Visual Studio), [Grunt](http://gruntjs.com/) (exécuteur de tâches JavaScript), et [Bower](http://bower.io/) (gestionnaire de package côté client).

1. Ouvrez une invite de commandes en tant qu’administrateur et accédez à l'emplacement où vous souhaitez créer votre projet ASP.NET. Par exemple, créez un répertoire *vscodeprojects* à la racine du lecteur C:.

2. Entrez la commande suivante dans l'invite de commandes pour installer Yeoman et les outils connexes.

		npm install -g yo grunt-cli generator-aspnet bower

	> [AZURE.NOTE] Vous pouvez obtenir un avertissement suggérant que votre version de npm est obsolète. Cet avertissement ne doit pas affecter ce didacticiel.

3. Entrez la commande suivante dans l'invite de commandes pour créer le dossier du projet et structurer l'application.

		yo aspnet

4. Utilisez les touches de direction pour sélectionner le type d’**application Web de base** dans le menu du générateur ASP.NET 5, puis appuyez sur la touche **&lt;Entrée>**.

	![Yeoman - générateur ASP.NET 5](./media/web-sites-create-web-app-using-vscode/01-yo-aspnet.png)

5. Définissez le nom de votre nouvelle application Web ASP.NET sur **SampleWebApp**. Ce nom étant utilisé tout au long du didacticiel, si vous sélectionnez un nom différent, vous devrez le remplacer pour chaque occurrence de **SampleWebApp**. Quand vous appuyez sur **&lt;Entrée>**, Yeoman crée un nouveau dossier nommé **SampleWebApp**, ainsi que les fichiers nécessaires à votre nouvelle application.

6. Dans l'invite de commandes, accédez à votre nouveau dossier de projet :

		cd SampleWebApp

7. Pour installer les packages NuGet nécessaires pour exécuter l'application, entrez la commande suivante, également dans l'invite de commandes :

		dnu restore

8. Ouvrez VS Code en entrant la commande suivante dans l'invite de commandes :

		code .

## Exécution locale de l'application Web

Maintenant que vous avez créé l'application Web et extrait tous les packages NuGet pour l'application, vous pouvez exécuter l'application Web localement.

1. À partir de la **Palette de commandes** de VS Code, entrez la commande suivante pour afficher les options de commande d’exécution disponibles :

		dnx: Run Command

	> [AZURE.NOTE] Si le serveur Omnisharp n'est pas en cours d'exécution, il démarrera. Entrez de nouveau la commande ci-dessus.

	Ensuite, sélectionnez la commande suivante pour exécuter votre application web :
		
		dnx web - (SampleWebApp)

	La fenêtre de commande affiche que l'application a démarré. Si la fenêtre de commande n'affiche pas ce message, vérifiez le coin inférieur gauche de VS Code pour découvrir les erreurs repérées dans votre projet.
	
	> [AZURE.NOTE] L’émission d’une commande à partir de la **palette de commandes** nécessite un caractère **>** au début de la ligne de commande. Vous pouvez afficher les détails relatifs à la commande **web** dans le fichier *project.json*. Si la commande ne s’affiche pas ou n’est pas disponible, vous devrez peut-être installer l’extension C#. Exécutez `>Extensions: Install Extension` et `ext install c#` pour installer les extensions C#.

2. Ouvrez un navigateur et accédez à l'URL suivante.

	**http://localhost:5000**

	La page par défaut de l’application Web apparaîtra comme suit.

	![Application Web locale dans un navigateur](./media/web-sites-create-web-app-using-vscode/08-web-app.png)

3. Fermez votre navigateur. Dans la **Fenêtre de commande**, appuyez sur **Ctrl+C** pour arrêter l’application et fermer la **Fenêtre de commande**.

## Créer une application web dans le portail Azure

La procédure suivante vous guidera dans la création d'une application Web dans le portail Azure.

1. Connectez-vous au [portail Azure](https://portal.azure.com).

2. Cliquez sur **NOUVEAU** en haut à gauche du portail.

3. Cliquez sur **Applications Web > Application Web**.

	![Nouvelle application Web Azure](./media/web-sites-create-web-app-using-vscode/09-azure-newwebapp.png)

4. Entrez une valeur pour **Nom**, par exemple **SampleWebAppDemo**. Notez que ce nom doit être unique et qu’il sera imposé par le portail lorsque vous essayerez d'entrer le nom. Par conséquent, si vous décidez d’entrer une valeur différente, vous devez remplacer cette valeur pour chaque occurrence de **SampleWebAppDemo** que vous apercevez dans ce didacticiel.

5. Sélectionnez un **plan App Service** existant ou créez-en un. Si vous créez un plan, sélectionnez le niveau de tarification, l’emplacement et d’autres options. Pour plus d’informations sur les plans App Service, consultez la rubrique [Présentation détaillée des plans d’Azure App Service](../app-service/azure-web-sites-web-hosting-plans-in-depth-overview.md).

	![Panneau Nouvelle application Web Azure](./media/web-sites-create-web-app-using-vscode/10-azure-newappblade.png)

6. Cliquez sur **Create**.

	![Panneau Application Web](./media/web-sites-create-web-app-using-vscode/11-azure-webappblade.png)

## Activer la publication Git pour la nouvelle application Web

Git est un système de contrôle de version distribué permettant de déployer votre application Web Azure App Service. Vous stockerez le code que vous écrivez pour votre application Web dans un référentiel Git local, et vous déploierez votre code dans Azure par transmission de type Push vers un référentiel distant.

1. Connectez-vous au [portail Azure](https://portal.azure.com).

2. Cliquez sur **Parcourir**.

3. Cliquez sur **Applications Web** pour afficher la liste des applications Web associées à votre abonnement Azure.

4. Sélectionnez l'application Web que vous avez créée dans ce didacticiel.

5. Dans le panneau de l’application Web, cliquez sur **Paramètres** > **Déploiement continu**.

	![Hôte d’application Web Azure](./media/web-sites-create-web-app-using-vscode/14-azure-deployment.png)

6. Cliquez sur **Choisir la source > Référentiel Git local**.

7. Cliquez sur **OK**.

	![Référentiel Git local dans Microsoft Azure](./media/web-sites-create-web-app-using-vscode/15-azure-localrepository.png)

8. Si vous n'avez pas précédemment configuré les informations d'identification de déploiement pour la publication d'une application Web ou d'une autre application App Service, configurez-les maintenant :

	* Cliquez sur **Paramètres** > **Informations d’identification de déploiement**. Le panneau **Définir les informations d’identification de déploiement** s’affiche.

	* Créez un nom d'utilisateur et un mot de passe. Vous aurez besoin de ce mot de passe lorsque vous configurerez Git.

	* Cliquez sur **Enregistrer**.

9. Dans le panneau de l’application Web, cliquez sur **Paramètres > Propriétés**. L'URL du référentiel Git distant vers lequel vous allez déployer se trouve sous **URL GIT**.

10. Copiez la valeur **URL GIT** pour pouvoir l’utiliser plus tard dans le didacticiel.

	![URL Git de Microsoft Azure](./media/web-sites-create-web-app-using-vscode/17-azure-giturl.png)

## Publier votre application Web sur Azure App Service

Dans cette section, vous créerez un référentiel Git local et vous effectuerez une transmission de type Push depuis ce référentiel vers Azure pour déployer votre application Web sur Azure.

1. Dans VS Code, sélectionnez l’option **Git** dans la barre de navigation gauche.

	![Icône Git dans VS Code](./media/web-sites-create-web-app-using-vscode/git-icon.png)

2. Sélectionnez **Initialiser le référentiel Git** pour vous assurer que votre espace de travail est sous le contrôle de code source Git.

	![Initialiser Git](./media/web-sites-create-web-app-using-vscode/19-initgit.png)

3. Ouvrez la fenêtre de commande et remplacez les répertoires par le répertoire hébergeant votre application web. Puis, entrez la commande suivante :

		git config core.autocrlf false

	Cette commande empêche un problème lié au texte impliquant des terminaisons CRLF et des terminaisons LF.

4. Dans VS Code, ajoutez un message de validation, puis cliquez sur l’icône de coche **Valider tout**.

	![Git - valider tout](./media/web-sites-create-web-app-using-vscode/20-git-commit.png)

5. Une fois le traitement de Git terminé, vous apercevrez qu'il n'y a aucun fichier répertorié dans la fenêtre Git sous **Modifications**.

	![Git - aucune modification](./media/web-sites-create-web-app-using-vscode/no-changes.png)

6. Rétablissez la fenêtre de commande où l'invite de commande désigne le répertoire où se trouve votre application web.

7. Créez une référence distante pour envoyer les mises à jour vers l'application Web en utilisant l'URL Git (finissant par « .git ») que vous avez copiée plus tôt.

		git remote add azure [URL for remote repository]

8. Configurez Git pour enregistrer vos informations d'identification localement afin qu'elles soient automatiquement ajoutées à vos commandes push générées à partir de VS Code.

		git config credential.helper store

9. Envoyez vos modifications dans Azure en entrant la commande suivante : Après cette commande push initiale vers Azure, vous pourrez exécuter toutes les commandes push à partir de VS Code.

		git push -u azure master

	Vous êtes invité à entrer le mot de passe que vous avez créé précédemment dans Azure. **Remarque : votre mot de passe ne sera pas visible.**

	La sortie de la commande mentionnée ci-dessus se termine par un message indiquant que le déploiement a réussi.

		remote: Deployment successful.
		To https://user@testsite.scm.azurewebsites.net/testsite.git
		[new branch]      master -> master

> [AZURE.NOTE] Si vous apportez des modifications à votre application, vous pouvez republier directement dans VS Code à l'aide de la fonctionnalité intégrée de Git en sélectionnant l'option **Valider tout** suivie de l'option **Push**. Vous trouverez l'option **Push** dans le menu déroulant à côté des boutons **Valider tout** et **Actualiser**.

Si vous avez besoin de collaborer sur un projet, envisagez d'alterner les commandes push vers GitHub et les commandes push vers Azure.

## Exécuter l’application dans Microsoft Azure
Maintenant que vous avez déployé votre application Web, exécutez l'application lorsque celle-ci est hébergée dans Azure.

Cette opération peut être réalisée de deux manières :

* Ouvrez un navigateur et entrez le nom de votre application Web comme suit.

		http://SampleWebAppDemo.azurewebsites.net
 
* Dans le portail Azure, localisez le panneau de l’application web pour votre application web, puis cliquez sur **Parcourir** pour afficher votre application
* dans votre navigateur par défaut.

![Application Web Azure](./media/web-sites-create-web-app-using-vscode/21-azurewebapp.png)

## Résumé
Dans ce didacticiel, vous avez appris à créer une application Web dans VS Code et à le déployer dans Azure. Pour plus d’informations sur VS Code, consultez l’article [Pourquoi Visual Studio Code ?](https://code.visualstudio.com/Docs/). Pour plus d'informations sur les applications Web App Service, consultez la [Vue d'ensemble des applications Web](app-service-web-overview.md).

<!---HONumber=AcomDC_0706_2016-->