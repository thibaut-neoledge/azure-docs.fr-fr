<properties 
	pageTitle="Création d'un site Web Node.js sur Mac - Didacticiels Azure" 
	description="Découvrez comment créer et déployer un site Web Node.js dans Azure. Sample code is written in Java." 
	services="web-sites" 
	documentationCenter="nodejs" 
	authors="blackmist" 
	manager="wpickett" 
	editor=""/>

<tags 
	ms.service="web-sites" 
	ms.workload="web" 
	ms.tgt_pltfrm="na" 
	ms.devlang="nodejs" 
	ms.topic="article" 
	ms.date="09/17/2014" 
	ms.author="larryfr"/>






# Génération et déploiement d'un site Web Node.js dans Azure

Ce didacticiel explique comment créer une application [Node] [nodejs.org] et comment la déployer vers un site Web Azure à l'aide de [Git]. Les instructions de ce didacticiel s'appliquent à tous les systèmes d'exploitation pouvant exécuter Node.

Si vous préférez regarder ce didacticiel sous forme de vidéo, le clip suivant montre des étapes similaires :
[AZURE.VIDEO create-a-nodejs-site-deploy-from-github]
 
Voici une capture d'écran de l'application terminée :

![A browser displaying the 'Hello World' message.][helloworld-completed]

##Création d'un site Web Azure et activation de la publication Git

Suivez cette procédure pour créer un site Web Azure et activer la publication Git pour celui-ci.

> [AZURE.NOTE]
> Pour effectuer ce didacticiel, vous avez besoin d'un compte Azure. Si vous ne possédez pas de compte, vous pouvez créer un compte d'évaluation gratuit en quelques minutes. Pour plus d'informations, consultez la page <a href="http://azure.microsoft.com/pricing/free-trial/?WT.mc_id=A7171371E" target="_blank">Version d'évaluation gratuite d'Azure</a>.
> 
> Si vous voulez prendre en main Sites Web Azure avant de créer un compte, accédez à <a href="https://trywebsites.azurewebsites.net/?language=nodejs">https://trywebsites.azurewebsites.net</a>, où vous pouvez immédiatement et gratuitement créer un site de départ ASP.NET de courte durée dans Sites Web Azure. Aucun numéro de carte de crédit, ni engagement de quelque sorte n'est exigé.

1. Connectez-vous au [portail de gestion Azure].

2. Cliquez sur l'icône **+Nouveau** dans le coin inférieur gauche du portail.

    ![The Azure Portal with the +NEW link highlighted.][portal-new-website]

3. Cliquez sur **Site Web**, puis sur **Création rapide**. Entrez une valeur pour **URL**, puis sélectionnez un centre de données pour votre site Web dans la liste déroulante **Région**. Cliquez sur la coche située en bas de la boîte de dialogue.

    ![The Quick Create dialog][portal-quick-create]

4. Lorsque le statut du site Web est défini sur **En cours d'exécution**, cliquez sur le nom du site Web pour accéder au **tableau de bord**.

	![Open web site dashboard][go-to-dashboard]

6. Dans le coin inférieur droit de la page de démarrage rapide, sélectionnez **Configurer le déploiement à partir du contrôle de code source**.

	![Set up Git publishing][setup-git-publishing]

6. À la question " Où est votre code source ? ", sélectionnez **Référentiel Git local**, puis cliquez sur la flèche.

	![where is your source code][where-is-code]

7. Pour activer la publication Git, vous devez fournir un nom d'utilisateur et un mot de passe. Si vous avez déjà activé la publication pour un site Web Azure, vous n'êtes pas invité à entrer un nom d'utilisateur ou un mot de passe. Au lieu de cela, un référentiel Git est créé en utilisant le nom d'utilisateur et le mot de passe que vous avez déjà indiqués. Notez le nom d'utilisateur et le mot de passe, car ils serviront pour la publication Git de tous les sites Web Azure que vous allez créer.

	![The dialog prompting for user name and password.][portal-git-username-password]

8. Lorsque le référentiel Git est prêt, vous pouvez consulter les instructions des commandes Git pour la configuration d'un référentiel local et la publication des fichiers dans Azure.

	![Git deployment instructions returned after creating a repository for the website.][git-instructions]

##Génération et test de votre application localement

Cette section décrit la création d'un fichier **server.js** contenant l'exemple 'hello world' de [nodejs.org]. Cet exemple a été modifié à partir de l'exemple d'origine en ajoutant process.env.PORT en tant que port d'écoute lors de l'exécution dans un site Web Azure.

1. Dans un éditeur de texte, créez un fichier nommé **server.js** dans le répertoire **helloworld**. Si le répertoire **helloworld** n'existe pas, créez-le.
2. Ajoutez le contenu suivant au fichier **server.js**, puis enregistrez-le :

        var http = require('http')
        var port = process.env.PORT || 1337;
        http.createServer(function(req, res) {
          res.writeHead(200, { 'Content-Type': 'text/plain' });
          res.end('Hello World\n');
        }).listen(port);

3. Ouvrez la ligne de commande, puis utilisez la commande suivante pour démarrer la page Web localement :

        node server.js

4. Ouvrez votre navigateur Web et accédez à la page http://localhost:1337. Une page Web affichant " Hello World " apparaît, comme indiqué sur la capture d'écran suivante :

    ![A browser displaying the 'Hello World' message.][helloworld-localhost]

##Publication de votre application

1. ﻿À partir de la ligne de commande, accédez au répertoire **helloworld**, puis entrez les commandes suivantes pour initialiser un référentiel Git local. 

		git init

	> [AZURE.NOTE] **Si la commande Git n'est pas disponible**
	[Git](http://git-scm.com/%20target="_blank) est un système de contrôle de version distribué permettant de déployer votre site Web Azure. Pour obtenir des instructions d'installation pour votre plateforme, consultez la [page de téléchargement de Git](http://git-scm.com/download%20target="_blank").

2. Utilisez les commandes suivantes pour ajouter des fichiers au référentiel :

		git add .
		git commit -m "initial commit"

3. Ajoutez un Git distant pour transférer des mises à jour vers le site Web Azure créé précédemment, en utilisant la commande suivante :

		git remote add azure [URL for remote repository]

    ![Git deployment instructions returned after creating a repository for the web site.][git-instructions]
 
4. Transférez vos modifications dans Azure en utilisant la commande suivante :

		git push azure master

	Vous devez entrer le mot de passe que vous avez créé plus tôt pour obtenir le résultat suivant :

		Password for 'testsite.scm.azurewebsites.net':
		Counting objects: 3, done.
		Delta compression using up to 8 threads.
		Compressing objects: 100% (2/2), done.
		Writing objects: 100% (3/3), 374 bytes, done.
		Total 3 (delta 0), reused 0 (delta 0)
		remote: New deployment received.
		remote: Updating branch 'master'.
		remote: Preparing deployment for commit id '5ebbe250c9'.
		remote: Preparing files for deployment.
		remote: Deploying Web.config to enable Node.js activation.
		remote: Deployment successful.
		To https://user@testsite.scm.azurewebsites.net/testsite.git
		 * [new branch]      master -> master
    
	Lorsque vous accédez à l'onglet de déploiement de votre site Web Azure dans le portail de gestion, votre premier déploiement est affiché dans l'historique de déploiement :

	![Git deployment status on the portal][git-deployments-first] 

5. Accédez à votre site en utilisant le bouton **Parcourir** dans la page de votre site Web Azure dans le portail de gestion.

##Publication des modifications apportées à votre application

1. Ouvrez le fichier **server.js** dans un éditeur de texte et remplacez 'Hello World\n' par 'Hello Azure\n'. Enregistrez le fichier.
2. Sur la ligne de commande, accédez au répertoire **helloworld** et exécutez les commandes suivantes :

		git add .
		git commit -m "changing to hello azure"
		git push azure master

	Vous êtes invité à entrer le mot de passe que vous avez créé précédemment. Lorsque vous accédez à l'onglet de déploiements de votre site Web Azure dans le portail de gestion, l'historique de déploiement est mis à jour :
	
	![Git deployment status updated on the portal][git-deployments-second]

3. Accédez à votre site en utilisant le bouton **Parcourir**. Notez que les mises à jour ont été appliquées.

	![A web page displaying 'Hello Azure'][helloworld-completed]

4. Vous pouvez revenir au déploiement précédent en le sélectionnant sous l'onglet " Déploiements " de votre site Web Azure dans le portail de gestion et en utilisant le bouton **Redéployer**.

##Étapes suivantes

Bien que la procédure de cet article utilise le portail Azure pour créer un site Web, vous pouvez également utiliser les [outils en ligne de commande Azure pour Mac et Linux] pour effectuer les mêmes opérations.

Node.js fournit un écosystème de modules enrichi que vos applications peuvent utiliser. Pour en savoir plus sur le fonctionnement des Sites Web Azure avec les modules, consultez la page [Utilisation des modules Node.js avec les applications Azure](/fr-fr/documentation/articles/nodejs-use-node-modules-azure-apps/).

Pour en savoir plus sur les versions de Node.js fournies avec Azure et apprendre comment indiquer la version à utiliser avec votre application, consultez la page [Spécification d'une version de Node.js dans une application Azure](/fr-fr/documentation/articles/nodejs-specify-node-version-azure-apps/).

Si vous rencontrez des problèmes avec votre application après son déploiement dans Azure, consultez la page [Débogage d'une application Node.js dans les sites Web Azure](/fr-fr/documentation/articles/web-sites-nodejs-debug/) for information on diagnosing the problem.


##Ressources supplémentaires

* [Azure PowerShell]
* [Outils en ligne de commande Azure pour Mac et Linux]

[Azure PowerShell]: /fr-fr/documentation/articles/install-configure-powershell/

[nodejs.org]: http://nodejs.org
[Git]: http://git-scm.com

[Portail de gestion Azure]: http://manage.windowsazure.com
[Outils en ligne de commande Azure pour Mac et Linux]: /fr-fr/documentation/articles/xplat-cli/

[helloworld-completed]: ./media/web-sites-nodejs-develop-deploy-mac/helloazure.png
[helloworld-localhost]: ./media/web-sites-nodejs-develop-deploy-mac/helloworldlocal.png
[portal-new-website]: ./media/web-sites-nodejs-develop-deploy-mac/plus-new.png
[portal-quick-create]: ./media/web-sites-nodejs-develop-deploy-mac/create-quick-website.png

[portal-git-username-password]: ./media/web-sites-nodejs-develop-deploy-mac/git-deployment-credentials.png
[git-instructions]: ./media/web-sites-nodejs-develop-deploy-mac/git-instructions.png

[git-deployments-first]: ./media/web-sites-nodejs-develop-deploy-mac/git_deployments_first.png
[git-deployments-second]: ./media/web-sites-nodejs-develop-deploy-mac/git_deployments_second.png

[setup-git-publishing]: ./media/web-sites-nodejs-develop-deploy-mac/setup_git_publishing.png
[go-to-dashboard]: ./media/web-sites-nodejs-develop-deploy-mac/go_to_dashboard.png
[where-is-code]: ./media/web-sites-nodejs-develop-deploy-mac/where_is_code.png


<!--HONumber=42-->
