<properties linkid="develop-nodejs-common-tasks-specifying-a-node-version" urlDisplayName="Specifying a Node.js Version" pageTitle="Specifying a Node.js Version" metaKeywords="" description="" metaCanonical="" services="" documentationCenter="nodejs" title="Specifying a Node.js version in an Azure application" authors="larryfr" solutions="" manager="paulettm" editor="mollybos" />

<tags ms.service="na" ms.workload="na" ms.tgt_pltfrm="na" ms.devlang="nodejs" ms.topic="article" ms.date="09/17/2014" ms.author="larryfr"></tags>

# Spécification d'une version Node.js dans une application Azure

Lors de l'hébergement d'une application Node.js, vous pouvez vous assurer que votre application utilise une version spécifique de Node.js. Il existe diverses façons d'accomplir cela pour les applications hébergées sur Azure.

## Versions par défaut

Actuellement, Azure fournit les versions suivantes de Node.js : 0.6.17, 0.6.20 et 0.8.4. Sauf mention contraire, 0.6.20 est la version par défaut qui sera utilisée.

<div class="dev-callout">
<strong>Remarque</strong>
<p>Si vous h&eacute;bergez votre application dans un service cloud Azure (r&ocirc;le Web ou de travail) et que c'est la premi&egrave;re fois que vous la d&eacute;ployez, Azure essaiera d'utiliser la m&ecirc;me version de Node.js que celle que vous avez install&eacute;e dans votre environnement de d&eacute;veloppement si elle correspond &agrave; l'une des versions par d&eacute;faut disponibles dans Azure.</p>
</div>

## Contrôle de version avec package.json

Vous pouvez spécifier la version de Node.js à utiliser en ajoutant ce qui suit dans votre fichier **package.json** :

    "engines":{"node":version}

*version* correspond au numéro de version spécifique à utiliser. Vous pouvez spécifier des conditions plus complexes, telles que :

    "engines":{"node": "0.6.22 || 0.8.x"}

Étant donné que la version 0.6.22 n'est pas l'une des versions disponibles dans l'environnement d'hébergement, la version la plus élevée de la série 0.8 qui est disponible sera utilisée à la place : 0.8.4.

## Contrôle de version des services cloud avec PowerShell

Si vous hébergez l'application dans un service cloud et que vous déployez l'application au moyen d'Azure PowerShell, vous pouvez remplacer la version Node.js par défaut en utilisant la cmdlet PowerShell **Set-AzureServiceProjectRole**. Par exemple :

    Set-AzureServiceProjectRole WebRole1 node 0.8.4

Vous pouvez également utiliser **Get-AzureServiceProjectRoleRuntime** pour extraire une liste des versions de Node.js disponibles pour les applications hébergées en tant que service cloud.

## Utilisation d'une version personnalisée avec Sites Web Azure

Bien qu'Azure fournisse plusieurs versions par défaut de Node.js, vous pouvez utiliser une version qui n'est pas fournie par défaut. Si votre application est hébergée en tant que site web Azure, vous pouvez pour cela utiliser le fichier **iisnode.yml**. La procédure suivante explique comment utiliser une version personnalisée de Node.Js avec un site web Azure :

1.  Créez un répertoire, puis un fichier **server.js** dans celui-ci. Le fichier **server.js** doit contenir ce qui suit :

        var http = require('http');
        http.createServer(function(req,res) {
          res.writeHead(200, {'Content-Type': 'text/html'});
          res.end('Hello from Azure running node version: ' + process.version + '</br>');
        }).listen(process.env.PORT || 3000);

    Ce code permet d'afficher la version de Node.js qui est utilisée lorsque vous naviguez sur le site web.

2.  Créez un site web et prenez note de son nom. Par exemple, la commande suivante utilise les [outils en ligne de commande Azure][] pour créer un site web Azure nommé **mywebsite**, puis active un référentiel Git pour le site web.

        azure site create mywebsite --git

3.  Créez un répertoire nommé **bin**, enfant du répertoire contenant le fichier **server.js**.

4.  Téléchargez la version spécifique de **node.exe** (version Windows) que vous voulez utiliser avec votre application. Par exemple, la commande suivante utilise **curl** pour télécharger la version 0.8.1 :

        curl -O http://nodejs.org/dist/v0.8.1/node.exe

    Enregistrez le fichier **node.exe** dans le dossier **bin** créé précédemment.

5.  Créez un fichier **iisnode.yml** dans le même répertoire que le fichier **server.js**, puis ajoutez le contenu suivant au fichier **iisnode.yml** :

        nodeProcessCommandLine: "D:\home\site\wwwroot\bin\node.exe"

    Ce chemin correspond à l'emplacement du fichier **node.exe** de votre projet une fois que vous aurez publié votre application sur le site web Azure.

6.  Publiez votre application. Par exemple, étant donné que nous avons créé un site web avec le paramètre --git précédemment, les commandes suivantes permettront d'ajouter les fichiers d'application dans le référentiel Git local, puis de les transférer vers le référentiel du site web :

        git add .
        git commit -m "testing node v0.8.1"
        git push azure master

    Une fois l'application publiée, ouvrez le site web dans un navigateur. Le message suivant doit apparaître : « Hello from Azure running node version: v0.8.1 ».

## Étapes suivantes

Maintenant que vous savez comment spécifier la version de Node.js utilisée par votre application, découvrez comment [utiliser des modules][], [créer et déployer un site Web Node.js][] et [utiliser des outils en ligne de commande Azure pour Mac et Linux][outils en ligne de commande Azure].

  [outils en ligne de commande Azure]: /fr-fr/documentation/articles/xplat-cli/
  [utiliser des modules]: /fr-fr/documentation/articles/nodejs-use-node-modules-azure-apps/
  [créer et déployer un site Web Node.js]: /fr-fr/documentation/articles/web-sites-nodejs-develop-deploy-mac/
