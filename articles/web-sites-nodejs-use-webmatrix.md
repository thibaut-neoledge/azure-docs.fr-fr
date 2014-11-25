<properties linkid="web-site-with-webmatrix" urlDisplayName="Website with WebMatrix" pageTitle="Node.js website with WebMatrix - Azure tutorial" metaKeywords="" description="A tutorial that teaches you how to WebMatrix to develop and deploy a Node.js application to an Azure website." metaCanonical="" services="web-sites" documentationCenter="nodejs" title="Build and deploy a Node.js website to Azure using WebMatrix" authors="larryfr" solutions="" manager="paulettm" editor="mollybos" />

<tags ms.service="web-sites" ms.workload="web" ms.tgt_pltfrm="na" ms.devlang="nodejs" ms.topic="article" ms.date="09/17/2014" ms.author="larryfr" />

# Génération et déploiement d'un site web Node.js dans Azure avec WebMatrix

Ce didacticiel explique comment utiliser WebMatrix pour développer et déployer une application Node.js dans un site web Azure. WebMatrix est un outil de développement web gratuit de Microsoft doté de tous les éléments nécessaires pour développer un site web. WebMatrix inclut plusieurs fonctionnalités qui facilitent l'utilisation de Node.js comme la complétion de code, des modèles prégénérés ainsi que la prise en charge de l'éditeur pour Jade, LESS et CoffeeScript. En savoir plus sur [WebMatrix pour Azure][WebMatrix pour Azure].

﻿À la fin de ce guide, vous disposerez d'un site web Node.js s'exécutant dans Azure.

Voici une capture d'écran de l'application terminée :

![Site Web node Azure][Site Web node Azure]

[WACOM.INCLUDE [create-account-and-websites-note](../includes/create-account-and-websites-note.md)]

## Connexion à Azure

Pour créer un site web Azure, procédez comme suit.

<div class="dev-callout"><strong>Remarque</strong>
<p>Pour suivre ce didacticiel, vous devez disposer d'un compte Azure pour lequel la fonctionnalit&eacute; Sites Web Azure est activ&eacute;e.</p>
<p>Si vous ne poss&eacute;dez pas de compte, vous pouvez cr&eacute;er un compte d'&eacute;valuation gratuit en quelques minutes. Pour plus d'informations, consultez la page <a href="http://www.windowsazure.com/fr-fr/pricing/free-trial/?WT.mc_id=A7171371E" target="_blank">Version d'&eacute;valuation gratuite d'Azure</a>.</p>
</div>

1.  Lancez WebMatrix.
2.  Si vous utilisez WebMatrix pour la première fois, vous êtes invité à vous connecter à Azure. Sinon, vous pouvez cliquer sur le bouton **Se connecter**, puis choisir **Ajouter un compte**. Sélectionnez **Se connecter** avec votre compte Microsoft.

    ![Ajouter un compte][Ajouter un compte]

3.  Si vous disposez d'un compte Azure, vous pouvez vous connecter avec votre compte Microsoft :

    ![Connexion à Azure][Connexion à Azure]

## Création d'un site à l'aide d'un modèle intégré pour Azure

1.  Sur l'écran d'accueil, cliquez sur le bouton **Nouveau**, puis choisissez **Galerie de modèles** pour créer un site à partir de la galerie de modèles :

    ![Nouveau site à partir de la galerie de modèles][Nouveau site à partir de la galerie de modèles]

2.  Dans la boîte de dialogue **Site à partir du modèle**, sélectionnez **Node**, puis **Site express**. Enfin, cliquez sur **Suivant**. Si des éléments requis sont manquants pour le modèle **Site express**, vous êtes invité à les installer.

    ![sélection du modèle express][sélection du modèle express]

3.  Si vous êtes connecté à Azure, vous avez maintenant la possibilité de créer un site web Azure pour votre site local. Choisissez un nom unique, puis sélectionnez le centre de données sur lequel vous voulez que votre site soit créé :

    ![Créer un site sur Azure][Créer un site sur Azure]

4.  Une fois que WebMatrix a fini de créer le site web, l'environnement de développement intégré (IDE) WebMatrix s'affiche.

    ![IDE WebMatrix][IDE WebMatrix]

## Publication de votre application dans Azure

1.  Dans WebMatrix, cliquez sur **Publier** dans le ruban **Accueil** pour afficher la boîte de dialogue **Publier l'aperçu** pour le site web.

    ![publier l'aperçu][publier l'aperçu]

2.  Cliquez sur **Continuer**. Une fois la publication terminée, l'URL du site web sur Azure s'affiche en bas de l'IDE WebMatrix.

    ![publication terminée][publication terminée]

3.  Cliquez sur le lien pour ouvrir le site web dans votre navigateur.

    ![Site Web Express][Site Web Express]

## Modification et republication de votre application

Vous pouvez facilement modifier et republier votre application. Ici, nous allons simplement modifier le titre dans le fichier **index.jade**, puis republier l'application.

1.  Dans WebMatrix, sélectionnez **Fichiers**, puis développez le dossier **views**. Double-cliquez sur le fichier **index.jade** pour l'ouvrir.

    ![affichage index.jade WebMatrix][affichage index.jade WebMatrix]

2.  Remplacez la deuxième ligne par la ligne suivante :

        p Welcome to #{title} with WebMatrix on Azure!

3.  Enregistrez vos modifications, puis cliquez sur l'icône Publier. Enfin, cliquez sur **Continuer** dans la boîte de dialogue **Publier l'aperçu** et attendez que la mise à jour soit publiée.

    ![publier l'aperçu][1]

4.  Une fois celle-ci terminée, utilisez le lien renvoyé à la fin du processus de publication pour voir le site mis à jour.

    ![Site Web node Azure][Site Web node Azure]

## Étapes suivantes

Pour en savoir plus sur les versions de Node.js fournies avec Azure et apprendre comment indiquer la version à utiliser avec votre application, consultez la page [Spécification d'une version de Node.js dans une application Azure][Spécification d'une version de Node.js dans une application Azure].

Si vous rencontrez des problèmes avec votre application après son déploiement dans Azure, consultez la page [Débogage d'une application Node.js dans les sites Web Azure][Débogage d'une application Node.js dans les sites Web Azure] pour obtenir des informations permettant de diagnostiquer vos problèmes.

  [WebMatrix pour Azure]: http://go.microsoft.com/fwlink/?LinkID=253622&clcid=0x409
  [Site Web node Azure]: ./media/web-sites-nodejs-use-webmatrix/webmatrix-node-complete.png
  [Ajouter un compte]: ./media/web-sites-nodejs-use-webmatrix/webmatrix-add-account.png
  [Connexion à Azure]: ./media/web-sites-nodejs-use-webmatrix/webmatrix-sign-in.png
  [Nouveau site à partir de la galerie de modèles]: ./media/web-sites-nodejs-use-webmatrix/webmatrix-site-from-template.png
  [sélection du modèle express]: ./media/web-sites-nodejs-use-webmatrix/webmatrix-templates.png
  [Créer un site sur Azure]: ./media/web-sites-nodejs-use-webmatrix/webmatrix-node-site-azure.png
  [IDE WebMatrix]: ./media/web-sites-nodejs-use-webmatrix/webmatrix-ide.png
  [publier l'aperçu]: ./media/web-sites-nodejs-use-webmatrix/webmatrix-publishpreview.png
  [publication terminée]: ./media/web-sites-nodejs-use-webmatrix/webmatrix-publish-complete.png
  [Site Web Express]: ./media/web-sites-nodejs-use-webmatrix/webmatrix-express-webiste.png
  [affichage index.jade WebMatrix]: ./media/web-sites-nodejs-use-webmatrix/webmatrix-node-edit.png
  [1]: ./media/web-sites-nodejs-use-webmatrix/webmatrix-republish.png
  [Spécification d'une version de Node.js dans une application Azure]: /fr-fr/documentation/articles/nodejs-specify-node-version-azure-apps/
  [Débogage d'une application Node.js dans les sites Web Azure]: http://www.windowsazure.com/fr-fr/develop/nodejs/how-to-guides/Debug-Website/
