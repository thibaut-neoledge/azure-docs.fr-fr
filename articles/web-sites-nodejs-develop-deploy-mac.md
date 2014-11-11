<properties linkid="develop-node-create-a-website-mac" urlDisplayName="Website" pageTitle="Create a Node.js website on Mac - Azure tutorials" metaKeywords="Azure create website Node, Azure deploy website Node, website Node.js, Node website" description="Learn how to build and deploy a Node.js website in Azure. Sample code is written in Java." metaCanonical="" services="web-sites" documentationCenter="nodejs" title="Build and deploy a Node.js website to Azure" authors="larryfr" solutions="" manager="" editor="" />

<tags ms.service="web-sites" ms.workload="web" ms.tgt_pltfrm="na" ms.devlang="nodejs" ms.topic="article" ms.date="09/17/2014" ms.author="larryfr" />

# Génération et déploiement d'un site Web Node.js dans Azure

Ce didacticiel détaille la création d'une application [Node][Node] et son déploiement vers un site Web Azure en utilisant [Git][Git]. Les instructions de ce didacticiel s'appliquent à tous les systèmes d'exploitation pouvant exécuter Node.

Si vous préférez regarder une vidéo, sachez que celle de droite suit la même procédure que ce didacticiel.

Voici une capture d'écran de l'application terminée :

![Navigateur affichant le message « Hello World ».][Navigateur affichant le message « Hello World ».]

## Création d'un site Web Azure et activation de la publication Git

Suivez cette procédure pour créer un site Web Azure et activer la publication Git pour celui-ci.

<div class="dev-callout"><strong>Remarque</strong>
<p>Pour effectuer ce didacticiel, vous avez besoin d&rsquo;un compte Azure. Si vous ne poss&eacute;dez pas de compte, vous pouvez cr&eacute;er un compte d'&eacute;valuation gratuit en quelques minutes. Pour plus d'informations, consultez la page <a href="http://www.windowsazure.com/fr-fr/pricing/free-trial/?WT.mc_id=A7171371E" target="_blank">Version d'&eacute;valuation gratuite d'Azure</a>.</p>
</div>

1.  Connectez-vous au [portail de gestion Azure][portail de gestion Azure].

2.  Cliquez sur l'icône **+NEW** dans le coin inférieur gauche du portail.

    ![Portail Azure avec le lien +NEW mis en surbrillance][Portail Azure avec le lien +NEW mis en surbrillance]

3.  Cliquez sur **SITE WEB**, puis sur **CRÉATION RAPIDE**. Entrez une valeur pour **URL**, puis sélectionnez un centre de données pour votre site Web dans la liste déroulante **RÉGION**. Cliquez sur la coche située en bas de la boîte de dialogue.

    ![Boîte de dialogue Quick Create][Boîte de dialogue Quick Create]

4.  Lorsque le statut du site Web est défini sur **En cours d'exécution**, cliquez sur le nom du site Web pour accéder au **tableau de bord**.

    ![Ouvrir le tableau de bord du site Web][Ouvrir le tableau de bord du site Web]

5.  Dans le coin inférieur droit de la page de démarrage rapide, sélectionnez **Set up a deployment from source control**.

    ![Configurer la publication Git][Configurer la publication Git]

6.  À la question « Où est votre code source ? », sélectionnez **Référentiel Git local**, puis cliquez sur la flèche.

    ![où est votre code source][où est votre code source]

7.  Pour activer la publication Git, vous devez fournir un nom d'utilisateur et un mot de passe. Si vous avez déjà activé la publication pour un site Web Azure, vous n'êtes pas invité à entrer un nom d'utilisateur ou un mot de passe. Au lieu de cela, un référentiel Git est créé en utilisant le nom d'utilisateur et le mot de passe que vous avez déjà indiqués. Notez le nom d'utilisateur et le mot de passe, car ils serviront pour la publication Git de tous les sites Web Azure que vous allez créer.

    ![Boîte de dialogue de saisie du nom d'utilisateur et du mot de passe][Boîte de dialogue de saisie du nom d'utilisateur et du mot de passe]

8.  Lorsque le référentiel Git est prêt, vous pouvez consulter les instructions des commandes Git pour la configuration d'un référentiel local et la publication des fichiers dans Azure.

    ![Instructions de déploiement Git affichées après la création d'un référentiel pour le site Web.][Instructions de déploiement Git affichées après la création d'un référentiel pour le site Web.]

## Génération et test de votre application localement

Cette section décrit la création d'un fichier **server.js** contenant l'exemple « hello world » de [nodejs.org][Node]. Cet exemple a été modifié à partir de l'exemple d'origine en ajoutant process.env.PORT en tant que port d'écoute lors de l'exécution dans un site Web Azure.

1.  Dans un éditeur de texte, créez un fichier nommé **server.js** dans le répertoire **helloworld**. Si celui-ci n'existe pas, créez-le.
2.  Ajoutez le contenu suivant au fichier **server.js**, puis enregistrez-le :

        var http = require('http')
        var port = process.env.PORT || 1337;
        http.createServer(function(req, res) {
          res.writeHead(200, { 'Content-Type': 'text/plain' });
          res.end('Hello World\n');
        }).listen(port);

3.  Ouvrez la ligne de commande, puis utilisez la commande suivante pour démarrer la page Web localement :

        node server.js

4.  Ouvrez votre navigateur Web et accédez à la page <http://localhost:1337>. Une page Web affichant « Hello World » apparaît, comme indiqué sur la capture d'écran suivante :

    ![Navigateur affichant le message « Hello World ».][1]

## Publication de votre application

1.  ﻿À partir de la ligne de commande, remplacez les répertoires par le répertoire **helloworld**, puis entrez les commandes suivantes pour initialiser un référentiel Git local.

        git init

    <div class="dev-callout"><strong>Si la commande Git n'est pas disponible</strong>
<p><a href="http://git-scm.com/" target="_blank">Git</a> est un syst&egrave;me de contr&ocirc;le de version distribu&eacute; permettant de d&eacute;ployer votre site Web Azure. Pour obtenir des instructions d'installation pour votre plateforme, consultez <a href="http://git-scm.com/download" target="_blank">la page de t&eacute;l&eacute;chargement de Git</a>.</p>
</div>

2.  Utilisez les commandes suivantes pour ajouter des fichiers au référentiel :

        git add .
        git commit -m "initial commit"

3.  Ajoutez un Git distant pour transférer des mises à jour vers le site Web Azure créé précédemment, en utilisant la commande suivante :

        git remote add azure [URL for remote repository]

    ![Instructions de déploiement Git affichées après la création d'un référentiel pour le site Web][Instructions de déploiement Git affichées après la création d'un référentiel pour le site Web.]

4.  Transférez vos modifications dans Azure en utilisant la commande suivante :

        git push azure master

    Vous devez entrer le mot de passe que vous avez créé plus tôt pour obtenir le résultat suivant :

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

    Lorsque vous accédez à l'onglet de déploiement de votre site Web Azure dans le portail de gestion, votre premier déploiement est affiché dans l'historique de déploiement :

    ![Statut du déploiement Git sur le portail][Statut du déploiement Git sur le portail]

5.  Accédez à votre site en utilisant le bouton **Parcourir** sur la page de votre site Web Azure dans le portail de gestion.

## Publication des modifications apportées à votre application

1.  Ouvrez le fichier **server.js** dans un éditeur de texte, puis remplacez « Hello World\\n » par « Hello Azure\\n ». Enregistrez le fichier.
2.  Dans la ligne de commande, remplacez les répertoires par le répertoire **helloworld** et exécutez les commandes suivantes :

        git add .
        git commit -m "changing to hello azure"
        git push azure master

    Vous êtes invité à entrer le mot de passe que vous avez créé précédemment. Lorsque vous accédez à l'onglet de déploiements de votre site Web Azure dans le portail de gestion, l'historique de déploiement est mis à jour :

    ![Statut de déploiement Git mis à jour sur le portail][Statut de déploiement Git mis à jour sur le portail]

3.  Accédez à votre site en utilisant le bouton **Browse**. Notez que les mises à jour ont été appliquées.

    ![Page Web affichant « Hello Azure »][Navigateur affichant le message « Hello World ».]

4.  Vous pouvez revenir au déploiement précédent en le sélectionnant dans l'onglet « Déploiements » de votre site Web Azure dans le portail de gestion et en utilisant le bouton **Redéployer**.

## Étapes suivantes

Si la procédure de cet article utilise le portail Azure pour créer un site Web, vous pouvez également utiliser les [outils en ligne de commande Azure pour Mac et Linux][outils en ligne de commande Azure pour Mac et Linux] pour effectuer les mêmes opérations.

Node.js fournit un écosystème de modules enrichi que vos applications peuvent utiliser. Pour en savoir plus sur le fonctionnement des sites Web Azure avec les modules, consultez la page [Utilisation des modules Node.js avec les applications Azure][Utilisation des modules Node.js avec les applications Azure].

Pour en savoir plus sur les versions de Node.js fournies avec Azure et apprendre comment indiquer la version à utiliser avec votre application, consultez la page [Spécification d'une version de Node.js dans une application Azure][Spécification d'une version de Node.js dans une application Azure].

Si vous rencontrez des problèmes avec votre application après son déploiement dans Azure, consultez la page [Débogage d'une application Node.js dans les sites Web Azure][Débogage d'une application Node.js dans les sites Web Azure] pour obtenir des informations permettant de diagnostiquer vos problèmes.

## Ressources supplémentaires

-   [Azure PowerShell][Azure PowerShell]
-   [Outils en ligne de commande Azure pour Mac et Linux][outils en ligne de commande Azure pour Mac et Linux]

  [Node]: http://nodejs.org
  [Git]: http://git-scm.com
  [Navigateur affichant le message « Hello World ».]: ./media/web-sites-nodejs-develop-deploy-mac/helloazure.png
  [portail de gestion Azure]: http://manage.windowsazure.com
  [Boîte de dialogue Quick Create]: ./media/web-sites-nodejs-develop-deploy-mac/create-quick-website.png
  [Ouvrir le tableau de bord du site Web]: ./media/web-sites-nodejs-develop-deploy-mac/go_to_dashboard.png
  [Configurer la publication Git]: ./media/web-sites-nodejs-develop-deploy-mac/setup_git_publishing.png
  [où est votre code source]: ./media/web-sites-nodejs-develop-deploy-mac/where_is_code.png
  [Boîte de dialogue de saisie du nom d'utilisateur et du mot de passe]: ./media/web-sites-nodejs-develop-deploy-mac/git-deployment-credentials.png
  [Instructions de déploiement Git affichées après la création d'un référentiel pour le site Web.]: ./media/web-sites-nodejs-develop-deploy-mac/git-instructions.png
  [1]: ./media/web-sites-nodejs-develop-deploy-mac/helloworldlocal.png
  [Statut du déploiement Git sur le portail]: ./media/web-sites-nodejs-develop-deploy-mac/git_deployments_first.png
  [Statut de déploiement Git mis à jour sur le portail]: ./media/web-sites-nodejs-develop-deploy-mac/git_deployments_second.png
  [outils en ligne de commande Azure pour Mac et Linux]: /fr-fr/documentation/articles/xplat-cli/
  [Utilisation des modules Node.js avec les applications Azure]: /fr-fr/documentation/articles/nodejs-use-node-modules-azure-apps/
  [Spécification d'une version de Node.js dans une application Azure]: /fr-fr/documentation/articles/nodejs-specify-node-version-azure-apps/
  [Débogage d'une application Node.js dans les sites Web Azure]: /fr-fr/documentation/articles/web-sites-nodejs-debug/
  [Azure PowerShell]: /fr-fr/documentation/articles/install-configure-powershell/
