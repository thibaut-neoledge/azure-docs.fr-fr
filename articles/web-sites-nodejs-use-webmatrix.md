<properties 
	pageTitle="Créer et déployer une application web Node.js dans Azure à l’aide de WebMatrix" 
	description="Un didacticiel qui vous montre comment utiliser WebMatrix pour développer une application Node.js et la déployer dans Azure App Service Web Apps." 
	services="app-service\web" 
	documentationCenter="nodejs" 
	authors="MikeWasson" 
	manager="wpickett" 
	editor="mollybos"/>

<tags 
	ms.service="app-service-web" 
	ms.workload="web" 
	ms.tgt_pltfrm="na" 
	ms.devlang="nodejs" 
	ms.topic="article" 
	ms.date="04/23/2015" 
	ms.author="mwasson"/>


# Créer et déployer une application web Node.js dans Azure à l’aide de WebMatrix

Ce didacticiel vous montre comment utiliser WebMatrix pour développer une application Node.js et la déployer dans [Azure App Service](http://go.microsoft.com/fwlink/?LinkId=529714) Web Apps. WebMatrix est un outil de développement web gratuit de Microsoft, qui fournit tout ce dont vous avez besoin pour développer un site web ou une application web. WebMatrix inclut plusieurs fonctionnalités qui facilitent l'utilisation de Node.js comme la complétion de code, des modèles prégénérés ainsi que la prise en charge de l'éditeur pour Jade, LESS et CoffeeScript. En savoir plus sur [WebMatrix pour Azure](http://go.microsoft.com/fwlink/?LinkID=253622&clcid=0x409).

À la fin de ce guide, vous disposerez d’une application web Node.js s’exécutant dans Azure App Service.
 
Voici une capture d'écran de l'application terminée :

![Site Web node Azure][webmatrix-node-completed]

[AZURE.INCLUDE [create-account-and-websites-note](../includes/create-account-and-websites-note.md)]

>[AZURE.NOTE]Si vous voulez vous familiariser avec Azure App Service avant d’ouvrir un compte Azure, accédez à la page [Essayer App Service](http://go.microsoft.com/fwlink/?LinkId=523751). Vous pourrez créer immédiatement une application web temporaire dans App Service. Aucune carte de crédit n’est requise ; vous ne prenez aucun engagement.

## Connexion à Azure

Pour créer une application web dans Azure App Service, procédez comme suit :

1. Lancez WebMatrix.
2. Si vous utilisez WebMatrix pour la première fois, vous êtes invité à vous connecter à Azure. Sinon, vous pouvez cliquer sur le bouton **Se connecter**, puis choisir **Ajouter un compte**. Sélectionnez **Se connecter** avec votre compte Microsoft.

	![Ajouter un compte][addaccount]

3. Si vous disposez d'un compte Azure, vous pouvez vous connecter avec votre compte Microsoft :

	![Connexion à Azure][signin]


## Création d'un site à l'aide d'un modèle intégré pour Azure

1. Sur l'écran d'accueil, cliquez sur le bouton **Nouveau**, puis choisissez **Galerie de modèles** pour créer un site à partir de la galerie de modèles :

	![Nouveau site à partir de la galerie de modèles][sitefromtemplate]

2. Dans la boîte de dialogue **Site à partir du modèle**, sélectionnez **Node**, puis **Site express**. Enfin, cliquez sur **Suivant**. Si des éléments requis sont manquants pour le modèle **Site express**, vous êtes invité à les installer.

	![sélection du modèle express][webmatrix-templates]

3. Si vous êtes connecté à Azure, vous avez maintenant la possibilité de créer une application web App Service pour votre site local. Choisissez un nom unique, puis sélectionnez le centre de données sur lequel vous voudriez créer votre application web App Service :

	![Créer un site sur Azure][nodesitefromtemplateazure]
	
4. Dès que WebMatrix finit de créer le site local et l’application web App Service, l’IDE WebMatrix s’affiche.

	![IDE WebMatrix][webmatrix-ide]

##Publication de votre application dans Azure

1. Dans WebMatrix, cliquez sur **Publier** dans le ruban **Accueil** pour afficher la boîte de dialogue **Publier l’aperçu** du site.

	![publier l'aperçu][webmatrix-node-publishpreview]

2. Cliquez sur **Continuer**. Une fois la publication terminée, l’URL de l’application web App Service s’affiche en bas de l’IDE WebMatrix.

	![publication terminée][webmatrix-publish-complete]

3. Cliquez sur le lien pour ouvrir l’application web App Service dans votre navigateur.

	![application web Express][webmatrix-node-express-site]

##Modification et republication de votre application

Vous pouvez facilement modifier et republier votre application. Ici, nous allons simplement modifier le titre dans le fichier **index.jade**, puis republier l'application.

1. Dans WebMatrix, sélectionnez **Fichiers**, puis développez le dossier **views**. Double-cliquez sur le fichier **index.jade** pour l'ouvrir.

	![affichage index.jade WebMatrix][webmatrix-modify-index]

2. Remplacez la deuxième ligne par la ligne suivante :

		p Welcome to #{title} with WebMatrix on Azure!

3. Enregistrez vos modifications, puis cliquez sur l'icône Publier. Enfin, cliquez sur **Continuer** dans la boîte de dialogue **Publier l'aperçu** et attendez que la mise à jour soit publiée.

	![publier l'aperçu][webmatrix-republish]

4. Une fois la publication terminée, utilisez le lien renvoyé à la fin du processus de publication pour voir l’application web App Service mise à jour.

	![application web de nœud Azure][webmatrix-node-completed]

##Étapes suivantes

Pour en savoir plus sur les versions de Node.js fournies avec Azure et apprendre comment indiquer la version à utiliser avec votre application, consultez la page [Spécification d'une version de Node.js dans une application Azure](nodejs-specify-node-version-azure-apps.md).

Si vous rencontrez des problèmes avec votre application après son déploiement dans Azure, consultez la page [Débogage d’une application Node.js dans Sites Web Azure](web-sites-nodejs-debug.md) pour en savoir plus sur le diagnostic du problème.

## Changements apportés
* Pour obtenir un guide présentant les modifications apportées dans le cadre de la transition entre Sites Web et App Service, consultez la page [Azure App Service et les services Azure existants](http://go.microsoft.com/fwlink/?LinkId=529714).
* Pour obtenir un guide présentant les modifications apportées dans le cadre de la transition entre l’ancien et le nouveau portail, consultez la page [Références sur la navigation dans le portail Azure](http://go.microsoft.com/fwlink/?LinkId=529715).

[WebMatrix WebSite]: http://www.microsoft.com/click/services/Redirect2.ashx?CR_CC=200106398
[WebMatrix for Azure]: http://go.microsoft.com/fwlink/?LinkID=253622&clcid=0x409

[webmatrix-node-completed]: ./media/web-sites-nodejs-use-webmatrix/webmatrix-node-complete.png
[webmatrix-templates]: ./media/web-sites-nodejs-use-webmatrix/webmatrix-templates.png

[webmatrix-node-publishpreview]: ./media/web-sites-nodejs-use-webmatrix/webmatrix-publishpreview.png

[webmatrix-ide]: ./media/web-sites-nodejs-use-webmatrix/webmatrix-ide.png
[webmatrix-publish-complete]: ./media/web-sites-nodejs-use-webmatrix/webmatrix-publish-complete.png
[webmatrix-node-express-site]: ./media/web-sites-nodejs-use-webmatrix/webmatrix-express-webiste.png
[webmatrix-modify-index]: ./media/web-sites-nodejs-use-webmatrix/webmatrix-node-edit.png
[webmatrix-republish]: ./media/web-sites-nodejs-use-webmatrix/webmatrix-republish.png
[addaccount]: ./media/web-sites-nodejs-use-webmatrix/webmatrix-add-account.png
[signin]: ./media/web-sites-nodejs-use-webmatrix/webmatrix-sign-in.png
[sitefromtemplate]: ./media/web-sites-nodejs-use-webmatrix/webmatrix-site-from-template.png
[nodesitefromtemplateazure]: ./media/web-sites-nodejs-use-webmatrix/webmatrix-node-site-azure.png

<!--HONumber=54-->