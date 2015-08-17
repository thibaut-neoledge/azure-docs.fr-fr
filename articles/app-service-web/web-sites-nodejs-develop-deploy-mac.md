<properties
	pageTitle="Créer une application web Node.js dans Azure App Service"
	description="Découvrez comment générer et déployer une application web Node.js dans Azure."
	services="app-service\web"
	documentationCenter="nodejs"
	authors="MikeWasson"
	manager="wpickett"
	editor=""/>

<tags
	ms.service="app-service-web"
	ms.workload="web"
	ms.tgt_pltfrm="na"
	ms.devlang="nodejs"
	ms.topic="hero-article"
	ms.date="03/24/2015"
	ms.author="mwasson"/>

# Créer et déployer une application web Node.js dans Azure App Service

Ce didacticiel explique comment créer une application [Node][nodejs.org] et la déployer dans [Azure App Service Web Apps](http://go.microsoft.com/fwlink/?LinkId=529714) à l’aide de [Git]. Les instructions de ce didacticiel s'appliquent à tous les systèmes d'exploitation pouvant exécuter Node.

Voici une capture d'écran de l'application terminée :

![Navigateur affichant le message « Hello World ».][helloworld-completed]

##Créer une application web et activer la publication Git

Pour créer une application web et activer la publication Git, suivez la procédure ci-après.

> [AZURE.NOTE]
> Pour suivre ce didacticiel, vous devez disposer d’un compte Microsoft Azure. Si vous ne possédez pas de compte, vous pouvez [activer les avantages de votre abonnement MSDN](/fr-fr/pricing/member-offers/msdn-benefits-details/?WT.mc_id=A261C142F) ou [obtenir une évaluation gratuite](/fr-fr/pricing/free-trial/?WT.mc_id=A261C142F).

1. Connectez-vous au [portail Azure](https://portal.azure.com).

2. Cliquez sur l'icône **+ NOUVEAU** dans le coin supérieur gauche du portail.

3. Cliquez sur **Web et mobilité**, puis sur **Application web**.

    ![][portal-quick-create]

4. Entrez une valeur dans le champ **URL**.

5. Sélectionnez un plan App Service ou créez-en un. Si vous créez un plan, sélectionnez le niveau de tarification, l’emplacement et d’autres options.

    ![][portal-quick-create2]

6. Cliquez sur **Create**.

7. Une fois que l’état passe à **En cours d’exécution**, le portail ouvre automatiquement le panneau de votre application web. Pour accéder au panneau, cliquez sur **Parcourir**.

	![][go-to-dashboard]

8. Cliquez sur **Déploiement**. Il se peut que vous deviez faire défiler l’écran pour afficher cette partie du panneau.

	![][deployment-part]

9. Cliquez sur **Choisir la source**, puis sur **Référentiel Git local**. Cliquez sur **OK**.

	![][setup-git-publishing]


10. Cliquez sur la partie **Informations d’identification du déploiement** (encadrée en rouge ci-dessous). Créez un nom d’utilisateur et un mot de passe. Cliquez sur **Save**. (Si vous avez déjà activé la publication d’une application web, vous n’avez pas besoin d’effectuer cette opération.)

	![][deployment-credentials]


11. Pour procéder à la publication, vous effectuerez une transmission de type push vers un référentiel distant Git. Recherchez l’URL du référentiel et cliquez sur **Tous les paramètres** puis sur **Propriétés**. L’URL est répertoriée sous « URL Git ».

	![][git-url]

##Génération et test de votre application localement

Cette section décrit la création d'un fichier **server.js** contenant l'exemple « hello world » de [nodejs.org]. Cet exemple a été modifié par rapport à l’exemple d’origine via l’ajout de process.env.PORT en tant que port d’écoute lors de l’exécution dans une application web Azure.

1. Dans un éditeur de texte, créez un fichier nommé **server.js** dans le répertoire **helloworld**. Si celui-ci n'existe pas, créez-le.

2. Ajoutez le contenu suivant au fichier **server.js**, puis enregistrez-le :

        var http = require('http')
        var port = process.env.PORT || 1337;
        http.createServer(function(req, res) {
          res.writeHead(200, { 'Content-Type': 'text/plain' });
          res.end('Hello World\n');
        }).listen(port);

3. Ouvrez la ligne de commande, puis utilisez la commande suivante pour démarrer l’application web localement :

        node server.js

4. Ouvrez votre navigateur web et accédez à http://localhost:1337. Une page Web affichant « Hello World » apparaît, comme indiqué sur la capture d'écran suivante :

    ![Navigateur affichant le message « Hello World ».][helloworld-localhost]

##Publication de votre application

1. À partir de la ligne de commande, remplacez les répertoires par le répertoire **helloworld**, puis entrez les commandes suivantes pour initialiser un référentiel Git local.

		git init

	> [AZURE.NOTE]**Si la commande Git n’est pas disponible** 
	> [Git](http://git-scm.com/%20target="_blank) est un système de contrôle de version distribué permettant de déployer votre site Web Azure. Pour obtenir des instructions d'installation pour votre plateforme, consultez [la page de téléchargement de Git](http://git-scm.com/download%20target="_blank").

2. Utilisez les commandes suivantes pour ajouter des fichiers au référentiel :

		git add .
		git commit -m "initial commit"

3. Ajoutez un Git distant pour la transmission des mises à jour vers l’application web créée précédemment, en utilisant la commande suivante :

		git remote add azure [URL for remote repository]


4. Transférez vos modifications dans Azure en utilisant la commande suivante :

		git push azure master

	Vous êtes invité à entrer le mot de passe que vous avez créé précédemment. Le résultat doit ressembler à ce qui suit :

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


5. Pour afficher votre application, cliquez sur le bouton **Parcourir** dans la partie **Application web** du portail de gestion.

##Publication des modifications apportées à votre application

1. Ouvrez le fichier **server.js** dans un éditeur de texte, puis remplacez « Hello World\\n » par « Hello Azure\\n ». Enregistrez le fichier.
2. Dans la ligne de commande, remplacez les répertoires par le répertoire **helloworld** et exécutez les commandes suivantes :

		git add .
		git commit -m "changing to hello azure"
		git push azure master

	Vous êtes invité à entrer le mot de passe que vous avez créé précédemment.

3. Accédez à vote application en cliquant sur **Parcourir**. Notez que les mises à jour ont été appliquées.

	![Page Web affichant « Hello Azure »][helloworld-completed]

4. Vous pouvez revenir au déploiement précédent en le sélectionnant dans **Déploiements**.

>[AZURE.NOTE]Si vous voulez vous familiariser avec Azure App Service avant d’ouvrir un compte Azure, accédez à la page [Essayer App Service](http://go.microsoft.com/fwlink/?LinkId=523751). Vous pourrez créer immédiatement une application web temporaire dans App Service. Aucune carte de crédit n’est requise ; vous ne prenez aucun engagement.

##Étapes suivantes

Si la procédure de cet article utilise le portail Azure pour créer une application web, vous pouvez également utiliser l’[interface de ligne de commande Azure](../xplat-cli.md) pour effectuer les mêmes opérations.

Node.js fournit un écosystème de modules enrichi que vos applications peuvent utiliser. Pour savoir comment Web Apps fonctionne avec les modules, consultez la page [Utilisation des modules Node.js avec les applications Azure](../nodejs-use-node-modules-azure-apps.md).

Pour en savoir plus sur les versions de Node.js fournies avec Azure et apprendre comment indiquer la version à utiliser avec votre application, consultez la page [Spécification d'une version de Node.js dans une application Azure](../nodejs-specify-node-version-azure-apps.md).

Si vous rencontrez des problèmes avec votre application après son déploiement dans Azure, consultez la page [Débogage d'une application Node.js dans les sites Web Azure](web-sites-nodejs-debug.md) pour obtenir des informations permettant de diagnostiquer vos problèmes.


##Ressources supplémentaires

* [Azure PowerShell](../install-configure-powershell.md)
* [Interface de ligne de commande Azure](../xplat-cli.md)

## Changements apportés
* Pour obtenir un guide présentant les modifications apportées dans le cadre de la transition entre Sites Web et App Service, consultez la page [Azure App Service et les services Azure existants](http://go.microsoft.com/fwlink/?LinkId=529714).
* Pour obtenir un guide présentant les modifications apportées dans le cadre de la transition entre l’ancien et le nouveau portail, consultez la page [Références sur la navigation dans le portail Azure](http://go.microsoft.com/fwlink/?LinkId=529715).


[nodejs.org]: http://nodejs.org
[Git]: http://git-scm.com


[helloworld-completed]: ./media/web-sites-nodejs-develop-deploy-mac/helloazure.png
[helloworld-localhost]: ./media/web-sites-nodejs-develop-deploy-mac/helloworldlocal.png

[portal-quick-create]: ./media/web-sites-nodejs-develop-deploy-mac/create-quick-website.png

[portal-quick-create2]: ./media/web-sites-nodejs-develop-deploy-mac/create-quick-website2.png


[setup-git-publishing]: ./media/web-sites-nodejs-develop-deploy-mac/setup_git_publishing.png

[go-to-dashboard]: ./media/web-sites-nodejs-develop-deploy-mac/go_to_dashboard.png

[deployment-part]: ./media/web-sites-nodejs-develop-deploy-mac/deployment-part.png

[deployment-credentials]: ./media/web-sites-nodejs-develop-deploy-mac/deployment-credentials.png


[git-url]: ./media/web-sites-nodejs-develop-deploy-mac/git-url.png
 

<!---HONumber=August15_HO6-->