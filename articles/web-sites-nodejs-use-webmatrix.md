<properties urlDisplayName="Website with WebMatrix" pageTitle="Site web Node.js avec WebMatrix - Didacticiel Azure" metaKeywords="" description="Un didacticiel qui vous explique comment utiliser WebMatrix pour développer et déployer une application Node.js vers un site web Azure." metaCanonical="" services="web-sites" documentationCenter="nodejs" title="Build and deploy a Node.js website to Azure using WebMatrix" authors="larryfr" solutions="" manager="wpickett" editor="mollybos" />

<tags ms.service="web-sites" ms.workload="web" ms.tgt_pltfrm="na" ms.devlang="nodejs" ms.topic="article" ms.date="09/17/2014" ms.author="larryfr" />


# Génération et déploiement d'un site web Node.js dans Azure avec WebMatrix

Ce didacticiel explique comment utiliser WebMatrix pour développer et déployer une application Node.js vers un site web Azure. WebMatrix est un outil de développement web gratuit de Microsoft doté de tous les éléments nécessaires pour développer un site web. WebMatrix inclut plusieurs fonctionnalités qui facilitent l'utilisation de Node.js comme la complétion de code, des modèles prégénérés ainsi que la prise en charge de l'éditeur pour Jade, LESS et CoffeeScript. En savoir plus sur [WebMatrix pour Azure](http://go.microsoft.com/fwlink/?LinkID=253622&clcid=0x409).

﻿À la fin de ce guide, vous disposerez d'un site web Node.js s'exécutant dans Azure.
 
Voici une capture d'écran de l'application terminée :

![Azure node Web site][webmatrix-node-completed]

[WACOM.INCLUDE [create-account-and-websites-note](../includes/create-account-and-websites-note.md)]

## Connexion à Azure

Pour créer un site web Azure, procédez comme suit.

<div class="dev-callout"><strong>Remarque</strong>
<p>Pour suivre ce didacticiel, vous devez disposer d'un compte Azure pour lequel la fonctionnalité Sites Web Azure est activée.</p>
<p>Si vous ne possédez pas de compte, vous pouvez créer un compte d'évaluation gratuit en quelques minutes. Pour plus d'informations, consultez la page <a href="http://www.windowsazure.com/fr-fr/pricing/free-trial/?WT.mc_id=A7171371E" target="_blank">Version d'évaluation gratuite d'Azure</a>.</p>
</div>
<br />

1. Lancez WebMatrix.
2. Si vous utilisez WebMatrix pour la première fois, vous êtes invité à vous connecter à Microsoft Azure.  Sinon, vous pouvez cliquer sur le bouton **Se connecter**, puis choisir **Ajouter un compte**.  Sélectionnez **Se connecter** avec votre compte Microsoft.

	![Add Account][addaccount]

3. Si vous disposez d'un compte Azure, vous pouvez vous connecter avec votre compte Microsoft :

	![Sign into Azure][signin]	


## Création d'un site à l'aide d'un modèle intégré pour Azure

1. Sur l'écran d'accueil, cliquez sur le bouton **Nouveau**, puis choisissez **Galerie de modèles** pour créer un site à partir de la galerie de modèles :

	![New site from Template Gallery][sitefromtemplate]

2. Dans la boîte de dialogue **Site à partir du modèle**, sélectionnez **Node**, puis **Site Express**. Enfin, cliquez sur **Suivant**. Si des éléments requis sont manquants pour le modèle **Express Site**, vous êtes invité à les installer.

	![select express template][webmatrix-templates]

3. Si vous êtes connecté à Azure, vous avez maintenant la possibilité de créer un site web Azure pour votre site local.  Choisissez un nom unique, puis sélectionnez le centre de données sur lequel vous voulez que votre site soit créé : 

	![Create site on Azure][nodesitefromtemplateazure]
	
4. Une fois que WebMatrix a fini de créer le site web, l'environnement de développement intégré (IDE) WebMatrix s'affiche.

	![webmatrix ide][webmatrix-ide]

##Publication de votre application dans Azure

1. Dans WebMatrix, cliquez sur **Publier** dans le ruban **Accueil** pour afficher la boîte de dialogue **Publier l'aperçu** pour le site web.

	![publish preview][webmatrix-node-publishpreview]

2. Cliquez sur **Continuer**. Une fois la publication terminée, l'URL du site web sur Azure s'affiche en bas de l'IDE WebMatrix.

	![publish complete][webmatrix-publish-complete]

3. Cliquez sur le lien pour ouvrir le site web dans votre navigateur.

	![Express web site][webmatrix-node-express-site]

##Modification et republication de votre application

Vous pouvez facilement modifier et republier votre application. Ici, nous allons simplement modifier le titre dans le fichier **index.jade**, puis republier l'application.

1. Dans WebMatrix, sélectionnez **Fichiers**, puis développez le dossier **views**. Double-cliquez sur le fichier **index.jade** pour l'ouvrir.

	![webmatrix viewing index.jade][webmatrix-modify-index]

2. Remplacez la deuxième ligne par la ligne suivante :

		p Welcome to #{title} with WebMatrix on Azure!

3. Enregistrez vos modifications, puis cliquez sur l'icône Publier. Enfin, cliquez sur **Continuer** dans la boîte de dialogue **Publier l'aperçu** et attendez que la mise à jour soit publiée.

	![publish preview][webmatrix-republish]

4. Une fois celle-ci terminée, utilisez le lien renvoyé à la fin du processus de publication pour voir le site mis à jour.

	![Azure node Web site][webmatrix-node-completed]

##Étapes suivantes

Pour en savoir plus sur les versions de Node.js fournies avec Azure et apprendre comment indiquer la version à utiliser avec votre application, consultez la page [Spécification d'une version de Node.js dans une application Azure](/fr-fr/documentation/articles/nodejs-specify-node-version-azure-apps/).

Si vous rencontrez des problèmes avec votre application après son déploiement dans Azure, consultez la page [Débogage d'une application Node.js dans Sites Web Azure](http://www.windowsazure.com/fr-fr/develop/nodejs/how-to-guides/Debug-Website/) pour obtenir des informations permettant de diagnostiquer vos problèmes.


[Portail de gestion Azure]: http://manage.windowsazure.com
[Site web Webmatrix]: http://www.microsoft.com/click/services/Redirect2.ashx?CR_CC=200106398
[WebMatrix pour Azure]: http://go.microsoft.com/fwlink/?LinkID=253622&clcid=0x409

[Publication gratuite de sites web sur Azure]: /fr-fr/develop/nodejs/common-tasks/publishing-with-git/
[avec Git]: /fr-fr/pricing/free-trial
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
