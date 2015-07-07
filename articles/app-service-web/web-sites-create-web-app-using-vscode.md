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
	ms.date="06/14/2015" 
	ms.author="erikre;tarcher"/>

# Création d’une application Web ASP.NET 5 dans Visual Studio Code

## Vue d'ensemble

Ce didacticiel explique comment créer une application Web ASP.NET 5 à l’aide de [Visual Studio Code (VS Code)](http://code.visualstudio.com//Docs/whyvscode) et comment le déployer dans [Azure App Service](../app-service/app-service-value-prop-what-is.md). ASP.NET 5 est une nouvelle conception significative d'ASP.NET. ASP.NET 5 est une nouvelle infrastructure open source et interplateforme qui vous permet de créer des applications Web modernes basées sur le cloud à l'aide de .NET. Pour plus d’informations, consultez la page [Présentation d’ASP.NET 5](http://docs.asp.net/en/latest/conceptual-overview/aspnet.html). Pour plus d'informations sur les applications Web Azure App Service, consultez la [Vue d'ensemble de Web Apps](app-service-web-overview.md).

[AZURE.INCLUDE [app-service-web-try-app-service.md](../../includes/app-service-web-try-app-service.md)]

## Composants requis  

* Installation de [VS Code](http://code.visualstudio.com/Docs/setup).
* Installation de [Node.js](http://nodejs.org/download/) - [Node.js](http://nodejs.org/) est une plateforme vous permettant de créer des applications de serveur rapides et évolutives à l'aide de JavaScript. Node est le runtime (Node), et [npm](http://www.npmjs.com/) est le gestionnaire de package pour les modules Node. Vous utiliserez npm pour structurer une application Web ASP.NET 5 dans ce didacticiel.
* Installation de Git - vous pouvez l'installer depuis l’un de ces emplacements : [Chocolatey](https://chocolatey.org/packages/git) ou [git-scm.com](http://git-scm.com/downloads). Si vous débutez avec Git, choisissez [git-scm.com](http://git-scm.com/downloads) et sélectionnez l'option vous permettant d’utiliser Git avec GitBash, à partir de l'invite de commandes Windows. Une fois Git installé, vous devrez également définir le nom d'utilisateur et l’adresse de messagerie Git qui vous seront ultérieurement demandés dans ce didacticiel (lorsque vous effectuerez une validation à partir de VS Code).  

## Installation d’ASP.NET 5 et de DNX
ASP.NET 5/DNX est une pile .NET lean vous permettant de créer des applications Web modernes sur le cloud qui s'exécutent sur OS X, Linux et Windows. Elle a été construite intégralement pour fournir une infrastructure de développement optimisée pour les applications qui sont déployées sur le cloud ou qui sont exécutées localement. Elle inclut des composants modulaires associés à des frais généraux réduits. Ainsi, vous bénéficiez d’une certaine flexibilité lors de la création de vos solutions.

> [AZURE.NOTE]ASP.NET 5 et DNX (l'environnement d'exécution .NET) sur OS X et Linux sont disponibles en version bêta/d’évaluation.

Ce didacticiel est conçu pour vous aider à créer des applications à l’aide des dernières versions de développement d’ ASP.NET 5 et de DNX. Les instructions suivantes sont spécifiques à Windows. Pour accéder à des instructions plus détaillées sur l’installation de cette fonction sur des systèmes OS X, Linux et Windows, consultez la rubrique [Installation d’ASP.NET 5 et de DNX](https://code.visualstudio.com/Docs/ASPnet5#_installing-aspnet-5-and-dnx).

1. Pour installer le gestionnaire de version .NET (DNVM) dans Windows, ouvrez une invite de commandes et exécutez la commande suivante.

		@powershell -NoProfile -ExecutionPolicy unrestricted -Command "&{$Branch='dev';iex ((new-object net.webclient).DownloadString('https://raw.githubusercontent.com/aspnet/Home/dev/dnvminstall.ps1'))}"

	Ceci téléchargera le script DNVM et le placera dans le répertoire de votre profil utilisateur.

2. Redémarrez Windows pour terminer l'installation de DNVM.

3. Ouvrez une invite de commande et vérifiez l'emplacement de DNVM en entrant la commande suivante.

		where dnvm

	L'invite de commandes affichera un chemin d'accès semblable à celui-ci :

	![Emplacement de DNVM](./media/web-sites-create-web-app-using-vscode/00-where-dnvm.png)

4. Maintenant que vous disposez de DNVM, vous devez l’utiliser pour télécharger DNX afin d’exécuter vos applications. Exécutez ensuite la commande suivante dans l’invite de commandes :

		dnvm upgrade

5. Vérifiez votre DNVM et affichez le runtime actif en entrant la commande suivante dans l'invite de commandes.

		dnvm list

	L'invite de commandes affichera les détails du runtime actif.

	![Emplacement de DNVM](./media/web-sites-create-web-app-using-vscode/00b-dnvm-list.png)

6. Si plusieurs runtimes DNX sont répertoriés, entrez la commande suivante dans l'invite de commandes pour définir la même version du runtime DNX actif que celle utilisée par le générateur ASP.NET 5 lorsque vous créerez votre application Web plus loin dans ce didacticiel.

		dnvm use 1.0.0-beta4 –p

> [AZURE.NOTE]Pour accéder à des instructions plus détaillées sur l’installation de cette fonction sur des systèmes OS X, Linux et Windows, consultez la rubrique [Installation d’ASP.NET 5 et de DNX](https://code.visualstudio.com/Docs/ASPnet5#_installing-aspnet-5-and-dnx).

## Création de l'application Web 

Cette section vous indique comment structurer une nouvelle application Web ASP.NET. Vous utiliserez le gestionnaire de package Node (npm) pour installer [Yeoman](http://yeoman.io/) (outil de structuration d'applications - l'équivalent VS Code de l’opération **Fichier > Nouveau projet** de Visual Studio), [Grunt](http://gruntjs.com/) (exécuteur de tâches JavaScript), et [Bower](http://bower.io/) (gestionnaire de package côté client).

1. Ouvrez une invite de commandes en tant qu’administrateur et accédez à l'emplacement où vous souhaitez créer votre projet ASP.NET.

2. Entrez la commande suivante dans l'invite de commandes pour installer Yeoman et les outils connexes.

		npm install -g yo grunt-cli generator-aspnet bower

3. Entrez la commande suivante dans l'invite de commandes pour créer le dossier du projet et structurer l'application.

		yo aspnet

4. Utilisez les touches de direction pour sélectionner le type d’**application Web** dans le menu du générateur ASP.NET 5, puis appuyez sur la touche &lt;Entrée>.

	![Yeoman - générateur ASP.NET 5](./media/web-sites-create-web-app-using-vscode/01-yo-aspnet.png)

5. Définissez le nom de votre nouvelle application Web ASP.NET sur **SampleWebApp**. Ce nom étant utilisé tout au long du didacticiel, si vous sélectionnez un nom différent, vous devrez le remplacer pour chaque occurrence de **SampleWebApp**. Lorsque vous appuyez sur &lt;Entrée>, Yeoman créera un nouveau dossier intitulé **SampleWebApp** et les fichiers nécessaires à votre nouvelle application.

6. Ouvrez VS Code en entrant la commande suivante dans l'invite de commandes.

		code .

7. Dans VS Code, sélectionnez **Fichier > Ouvrir le dossier**, puis sélectionnez le dossier contenant votre application Web ASP.NET.

	![Sélectionnez la boîte de dialogue Dossier](./media/web-sites-create-web-app-using-vscode/02-open-folder.png)

	VS Code chargera votre projet et affichera ses fichiers dans la fenêtre **Explorer**.

	![Affichage du projet SampleWebApp dans VS Code](./media/web-sites-create-web-app-using-vscode/03-vscode-project.png)

8. Sélectionnez **Afficher > Palette de commandes**.

9. Dans la **Palette de commandes**, entrez la commande suivante :

		dnx:dnu restore - (SampleWebApp)

	Lorsque vous commencerez à la saisir, vous apercevrez dans la liste la ligne de commande complète.

	![Commande Restaurer](./media/web-sites-create-web-app-using-vscode/04-dnu-restore.png)

	La commande Restaurer installe les packages NuGet requis pour exécuter l'application. Une invite de commandes affichera la mention **Restauration terminée** lorsque l’opération sera terminée.

## Exécution locale de l'application Web

Maintenant que vous avez créé l'application Web et extrait tous les packages NuGet pour l'application, vous pouvez exécuter l'application Web localement.

1. À partir de la **Palette de commandes** de VS Code, entrez la commande suivante pour exécuter l'application localement.

		dnx: kestrel - (SampleWebApp, Microsoft.AspNet.Hosting --server Kestrel --server.urls http://localhost:5001

	La fenêtre Commandes affichera la mention *Démarré*. Si la fenêtre de commande n'affiche pas *Démarré*, vérifiez le coin inférieur gauche de VS Code pour découvrir les erreurs repérées dans votre projet.

5. Ouvrez un navigateur et accédez à l'URL suivante.

	**http://localhost:5001**

	La page par défaut de l’application Web apparaîtra comme suit.

	![Application Web locale dans un navigateur](./media/web-sites-create-web-app-using-vscode/08-web-app.png)

## Création d’une application web dans la version préliminaire du portail Azure

La procédure suivante vous guidera dans la création d'une application Web dans le portail Azure en version préliminaire.

1. Connectez-vous à la [version préliminaire du portail Azure](https://portal.azure.com).

2. Cliquez sur **NOUVEAU** dans la partie supérieure gauche du portail.

3. Cliquez sur **Applications Web > Application Web**.

	![Nouvelle application Web Azure](./media/web-sites-create-web-app-using-vscode/09-azure-newwebapp.png)

4. Entrez une valeur pour **Nom**, par exemple **SampleWebAppDemo**. Notez que ce nom doit être unique et qu’il sera imposé par le portail lorsque vous essayerez d'entrer le nom. Par conséquent, si vous décidez d’entrer une valeur différente, vous devez remplacer cette valeur pour chaque occurrence de **SampleWebAppDemo** que vous apercevez dans ce didacticiel.

5. Sélectionnez un **plan App Service** existant ou créez-en un. Si vous créez un plan, sélectionnez le niveau de tarification, l’emplacement et d’autres options. Pour plus d’informations sur les plans App Service, consultez la rubrique [Présentation détaillée des plans d’Azure App Service](../app-service/azure-web-sites-web-hosting-plans-in-depth-overview.md).

	![Panneau Nouvelle application Web Azure](./media/web-sites-create-web-app-using-vscode/10-azure-newappblade.png)

6. Cliquez sur **Create**.

	![Panneau Application Web](./media/web-sites-create-web-app-using-vscode/11-azure-webappblade.png)

## Activer la publication Git pour la nouvelle application Web

Git est un système de contrôle de version distribué permettant de déployer votre application Web Azure App Service. Vous stockerez le code que vous écrivez pour votre application Web dans un référentiel Git local, et vous déploierez votre code dans Azure par transmission de type Push vers un référentiel distant.

1. Connectez-vous au [portail Azure en version préliminaire](https://portal.azure.com).

2. Cliquez sur **Parcourir tout**.

3. Cliquez sur **Applications Web** pour afficher la liste des applications Web associées à votre abonnement Azure.

4. Sélectionnez l'application Web que vous avez créée dans ce didacticiel.

5. Faites défiler le panneau de l’application Web vers le bas jusqu'à la section **Déploiement**, puis cliquez sur **Configurer le déploiement continu**.

	![Hôte d’application Web Azure](./media/web-sites-create-web-app-using-vscode/14-azure-deployment.png)

6. Cliquez sur **Choisir la source > Référentiel Git local**.

7. Cliquez sur **OK**.

	![Référentiel Git local Azure](./media/web-sites-create-web-app-using-vscode/15-azure-localrepository.png)

8. Si vous n'avez pas précédemment configuré les informations d'identification de déploiement pour la publication d'une application Web ou d'une autre application App Service, configurez-les maintenant :

	* Cliquez sur **FTP** pour définir les informations d'identification de déploiement.

	* Créez un nom d'utilisateur et un mot de passe. Vous aurez besoin de ce mot de passe lorsque vous configurerez Git.

	* Cliquez sur **Save**.

	![Informations d’identification de déploiement Azure](./media/web-sites-create-web-app-using-vscode/16-azure-credentials.png)

9. Dans le panneau de l’application Web, cliquez sur **Paramètres > Propriétés**. L'URL du référentiel Git distant vers lequel vous allez déployer se trouve sous **
10.  URL**.

10. Copiez la valeur **URL GIT** pour pouvoir l’utiliser plus tard dans le didacticiel.

	![URL Git Azure](./media/web-sites-create-web-app-using-vscode/17-azure-giturl.png)

## Publier votre application Web sur Azure App Service

Dans cette section, vous créerez un référentiel Git local et vous effectuerez une transmission de type Push depuis ce référentiel vers Azure pour déployer votre application Web sur Azure.

1. Dans VS Code, sélectionnez l’option **Git** dans la barre de navigation gauche.

	![Icône Git dans VS Code](./media/web-sites-create-web-app-using-vscode/git-icon.png)

2. Sélectionnez **Initialiser le référentiel Git** pour vous assurer que votre espace de travail est sous le contrôle de code source Git.

	![Initialiser Git](./media/web-sites-create-web-app-using-vscode/19-initgit.png)

3. Ajoutez un message de validation, puis cliquez sur l’icône en forme de coche **Valider tout**.

	![Git - valider tout](./media/web-sites-create-web-app-using-vscode/20-git-commit.png)

4. Une fois le traitement de Git terminé, vous apercevrez qu'il n'y a aucun fichier répertorié dans la fenêtre Git sous **Modifications**.

	![Git - aucune modification](./media/web-sites-create-web-app-using-vscode/no-changes.png)

5. Ouvrez une invite de commande et indiquez le répertoire hébergeant votre application Web.

6. Créez une référence distante pour envoyer les mises à jour vers l'application Web en utilisant l'URL Git (finissant par « .git ») que vous avez copiée plus tôt.

		git remote add azure [URL for remote repository]

7. Envoyez vos modifications dans Azure en entrant la commande suivante :

		git push azure master

	Vous êtes invité à entrer le mot de passe que vous avez créé précédemment. **Remarque : votre mot de passe ne sera pas visible.**

	La sortie de la commande mentionnée ci-dessus se termine par un message indiquant que le déploiement a réussi.

		remote: Deployment successful.
		To https://user@testsite.scm.azurewebsites.net/testsite.git
		[new branch]      master -> master

> [AZURE.NOTE]Si vous apportez des modifications à votre application, vous pouvez la republier en sélectionnant l’option **Valider Tout** de VS Code, puis en entrant la commande **git push azure master** dans l'invite de commandes.

## Exécution de l'application dans Azure
Maintenant que vous avez déployé votre application Web, exécutez l'application lorsque celle-ci est hébergée dans Azure.

Cette opération peut être réalisée de deux manières :

* Ouvrez un navigateur et entrez le nom de votre application Web comme suit.   

		http://SampleWebAppDemo.azurewebsites.net
 
* Dans le portail Azure en version préliminaire, localisez le panneau de l'application Web pour votre application Web, puis cliquez sur **Parcourir** pour afficher votre application dans votre navigateur par défaut.

![Application Web Azure](./media/web-sites-create-web-app-using-vscode/21-azurewebapp.png)

## Résumé
Dans ce didacticiel, vous avez appris à créer une application Web dans VS Code et à le déployer dans Azure. Pour plus d'informations sur VS Code, consultez l'article [Pourquoi VS Code ?](https://code.visualstudio.com/Docs/). Pour plus d'informations sur les applications Web App Service, consultez la [Vue d'ensemble des applications Web](app-service-web-overview.md).

<!---HONumber=62-->