<properties linkid="web-site-with-webmatrix" urlDisplayName="Web site with WebMatrix" pageTitle="Node.js web site with WebMatrix - Azure tutorial" metaKeywords="" description="A tutorial that teaches you how to WebMatrix to develop and deploy a Node.js application to an Azure web site." metaCanonical="" services="web-sites" documentationCenter="Node.js" title="Build and deploy a Node.js web site to Azure using WebMatrix" authors="" solutions="" manager="" editor="" />

Génération et déploiement d'un site Web Node.js dans Azure avec WebMatrix
=========================================================================

Ce didacticiel explique comment utiliser WebMatrix pour développer et déployer une application Node.js dans un site Web Azure. WebMatrix est un outil de développement Web gratuit de Microsoft doté de tous les éléments nécessaires pour développer un site Web. WebMatrix inclut plusieurs fonctionnalités qui facilitent l'utilisation de Node.js comme la complétion de code, des modèles prégénérés ainsi que la prise en charge de l'éditeur pour Jade, LESS et CoffeeScript. En savoir plus sur [WebMatrix pour Azure](http://go.microsoft.com/fwlink/?LinkID=253622&clcid=0x409).

À la fin de ce guide, vous disposerez d'un site Web Node.js s'exécutant dans Azure.

Voici une capture d'écran de l'application terminée :

![Site Web node Azure](./media/web-sites-nodejs-use-webmatrix/webmatrix-node-complete.png)

[WACOM.INCLUDE [create-account-and-websites-note](../includes/create-account-and-websites-note.md)]

Connexion à Azure
-----------------

Pour créer un site Web Azure, procédez comme suit.

**Remarque**

Pour suivre ce didacticiel, vous devez disposer d'un compte Azure pour lequel la fonctionnalité Sites Web Azure est activée.

Si vous ne possédez pas de compte, vous pouvez créer un compte d'évaluation gratuit en quelques minutes. Pour plus d'informations, consultez la page [Version d'évaluation gratuite d'Azure](http://www.windowsazure.com/fr-fr/pricing/free-trial/?WT.mc_id=A7171371E).

1.  Lancez WebMatrix.
2.  Si vous utilisez WebMatrix pour la première fois, vous êtes invité à vous connecter à Azure. Sinon, vous pouvez cliquer sur le bouton **Se connecter**, puis choisir **Ajouter un compte**. Sélectionnez **Se connecter** avec votre compte Microsoft.

    ![Ajouter un compte](./media/web-sites-nodejs-use-webmatrix/webmatrix-add-account.png)

3.  Si vous disposez d'un compte Azure, vous pouvez vous connecter avec votre compte Microsoft :

    ![Connexion à Azure](./media/web-sites-nodejs-use-webmatrix/webmatrix-sign-in.png)

Création d'un site à l'aide d'un modèle intégré pour Azure
----------------------------------------------------------

1.  Sur l'écran d'accueil, cliquez sur le bouton **Nouveau**, puis choisissez **Galerie de modèles** pour créer un site à partir de la galerie de modèles :

    ![Nouveau site à partir de la galerie de modèles](./media/web-sites-nodejs-use-webmatrix/webmatrix-site-from-template.png)

2.  Dans la boîte de dialogue **Site à partir du modèle**, sélectionnez **Node**, puis **Site express**. Enfin, cliquez sur **Suivant**. Si des éléments requis sont manquants pour le modèle **Site express**, vous êtes invité à les installer.

    ![sélection du modèle express](./media/web-sites-nodejs-use-webmatrix/webmatrix-templates.png)

3.  Si vous êtes connecté à Azure, vous avez maintenant la possibilité de créer un site Web Azure pour votre site local. Choisissez un nom unique, puis sélectionnez le centre de données sur lequel vous voulez que votre site soit créé :

    ![Créer un site sur Azure](./media/web-sites-nodejs-use-webmatrix/webmatrix-node-site-azure.png)

4.  Une fois que WebMatrix a fini de créer le site Web, l'environnement de développement intégré (IDE) WebMatrix s'affiche.

    ![IDE WebMatrix](./media/web-sites-nodejs-use-webmatrix/webmatrix-ide.png)

Publication de votre application dans Azure
-------------------------------------------

1.  Dans WebMatrix, cliquez sur **Publier** dans le ruban **Accueil** pour afficher la boîte de dialogue **Publier l'aperçu** pour le site Web.

    ![publier l'aperçu](./media/web-sites-nodejs-use-webmatrix/webmatrix-publishpreview.png)

2.  Cliquez sur **Continuer**. Une fois la publication terminée, l'URL du site Web sur Azure s'affiche en bas de l'IDE WebMatrix.

    ![publication terminée](./media/web-sites-nodejs-use-webmatrix/webmatrix-publish-complete.png)

3.  Cliquez sur le lien pour ouvrir le site Web dans votre navigateur.

    ![Site Web Express](./media/web-sites-nodejs-use-webmatrix/webmatrix-express-webiste.png)

Modification et republication de votre application
--------------------------------------------------

Vous pouvez facilement modifier et republier votre application. Ici, nous allons simplement modifier le titre dans le fichier **index.jade**, puis republier l'application.

1.  Dans WebMatrix, sélectionnez **Fichiers**, puis développez le dossier **views**. Double-cliquez sur le fichier **index.jade** pour l'ouvrir.

    ![affichage index.jade WebMatrix](./media/web-sites-nodejs-use-webmatrix/webmatrix-node-edit.png)

2.  Remplacez la deuxième ligne par la ligne suivante :

         p Welcome to #{title} with WebMatrix on Azure!

3.  Enregistrez vos modifications, puis cliquez sur l'icône Publier. Enfin, cliquez sur **Continuer** dans la boîte de dialogue **Publier l'aperçu** et attendez que la mise à jour soit publiée.

    ![publier l'aperçu](./media/web-sites-nodejs-use-webmatrix/webmatrix-republish.png)

4.  Une fois celle-ci terminée, utilisez le lien renvoyé à la fin du processus de publication pour voir le site mis à jour.

    ![Site Web node Azure](./media/web-sites-nodejs-use-webmatrix/webmatrix-node-complete.png)

Étapes suivantes
----------------

Pour en savoir plus sur les versions de Node.js fournies avec Azure et apprendre comment indiquer la version à utiliser avec votre application, consultez la page [Spécification d'une version de Node.js dans une application Azure](http://www.windowsazure.com/fr-fr/develop/nodejs/common-tasks/specifying-a-node-version/).

Si vous rencontrez des problèmes avec votre application après son déploiement dans Azure, consultez la page [Débogage d'une application Node.js dans les sites Web Azure](http://www.windowsazure.com/fr-fr/develop/nodejs/how-to-guides/Debug-Website/) pour obtenir des informations permettant de diagnostiquer vos problèmes.

