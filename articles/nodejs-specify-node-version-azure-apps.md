<properties linkid="develop-nodejs-common-tasks-specifying-a-node-version" urlDisplayName="Specifying a Node.js Version" pageTitle="Specifying a Node.js Version" metaKeywords="" description="" metaCanonical="" services="" documentationCenter="Node.js" title="Specifying a Node.js version in an Azure application" authors="larryfr" solutions="" manager="paulettm" editor="mollybos" />

Spécification d'une version Node.js dans une application Azure
==============================================================

Lors de l'hébergement d'une application Node.js, vous pouvez vous assurer que votre application utilise une version spécifique de Node.js. Il existe diverses façons d'accomplir cela pour les applications hébergées sur Azure.

Versions par défaut
-------------------

Actuellement, Azure fournit les versions suivantes de Node.js : 0.6.17, 0.6.20 et 0.8.4. Sauf mention contraire, 0.6.20 est la version par défaut qui sera utilisée.
**Remarque**

Si vous hébergez votre application dans un service cloud Azure (rôle Web ou de travail) et que c'est la première fois que vous la déployez, Azure essaiera d'utiliser la même version de Node.js que celle que vous avez installée dans votre environnement de développement si elle correspond à l'une des versions par défaut disponibles dans Azure.

Contrôle de version avec package.json
-------------------------------------

Vous pouvez spécifier la version de Node.js à utiliser en ajoutant ce qui suit dans votre fichier **package.json** :

    "engines":{"node":version}

*version* correspond au numéro de version spécifique à utiliser. Vous pouvez spécifier des conditions plus complexes, telles que :

    "engines":{"node": "0.6.22 || 0.8.x"}

Étant donné que la version 0.6.22 n'est pas l'une des versions disponibles dans l'environnement d'hébergement, la version la plus élevée de la série 0.8 qui est disponible sera utilisée à la place : 0.8.4.

Contrôle de version des services cloud avec PowerShell
------------------------------------------------------

Si vous hébergez l'application dans un service cloud et que vous déployez l'application au moyen d'Azure PowerShell, vous pouvez remplacer la version Node.js par défaut en utilisant la cmdlet PowerShell **Set-AzureServiceProjectRole**. Par exemple :

    Set-AzureServiceProjectRole WebRole1 node 0.8.4

Vous pouvez également utiliser **Get-AzureServiceProjectRoleRuntime** pour extraire une liste des versions de Node.js disponibles pour les applications hébergées en tant que service cloud.

Utilisation d'une version personnalisée avec les sites Web Azure
----------------------------------------------------------------

Bien qu'Azure fournisse plusieurs versions par défaut de Node.js, vous pouvez utiliser une version qui n'est pas fournie par défaut. Si votre application est hébergée sous forme de site Web Azure, vous pouvez pour cela utiliser le fichier **iisnode.yml**. La procédure suivante explique comment utiliser une version personnalisée de Node.Js avec un site Web Azure :

1.  Créez un répertoire, puis un fichier **server.js** dans celui-ci. Le fichier **server.js** doit contenir ce qui suit :

         var http = require('http');
         http.createServer(function(req,res) {
           res.writeHead(200, {'Content-Type': 'text/html'});
           res.end('Hello from Azure running node version: ' + process.version + '</br>');
         }).listen(process.env.PORT || 3000);

    Ce code permet d'afficher la version de Node.js qui est utilisée lorsque vous naviguez sur le site Web.

2.  Créez un site Web et prenez note de son nom. Par exemple, la commande suivante utilise les [outils en ligne de commande Azure](/en-us/develop/nodejs/how-to-guides/command-line-tools/) pour créer un site Web Azure nommé **mywebsite**, puis active un référentiel Git pour le site Web.

         azure site create mywebsite --git

3.  Créez un répertoire nommé **bin**, enfant du répertoire contenant le fichier **server.js**.

4.  Téléchargez la version spécifique de **node.exe** (version Windows) que vous voulez utiliser avec votre application. Par exemple, la commande suivante utilise **curl** pour télécharger la version 0.8.1 :

         curl -O http://nodejs.org/dist/v0.8.1/node.exe

    Enregistrez le fichier **node.exe** dans le dossier **bin** créé précédemment.

5.  Créez un fichier **iisnode.yml** dans le même répertoire que le fichier **server.js**, puis ajoutez le contenu suivant au fichier **iisnode.yml** :

         nodeProcessCommandLine: "D:\home\site\wwwroot\bin\node.exe"

    Ce chemin correspond à l'emplacement du fichier **node.exe** de votre projet une fois que vous aurez publié votre application sur le site Web Azure.

6.  Publiez votre application. Par exemple, étant donné que nous avons créé un site Web avec le paramètre --git précédemment, les commandes suivantes permettront d'ajouter les fichiers d'application dans le référentiel Git local, puis de les transférer vers le référentiel du site Web :

         git add .
         git commit -m "testing node v0.8.1"
         git push azure master

    Une fois l'application publiée, ouvrez le site Web dans un navigateur. Le message suivant doit apparaître : « Hello from Azure running node version: v0.8.1 ».

Étapes suivantes
----------------

Maintenant que vous savez comment spécifier la version de Node.js utilisée par votre application, découvrez comment [utiliser des modules](/en-us/develop/nodejs/common-tasks/working-with-node-modules/), [créer et déployer un site Web Node.js](/en-us/develop/nodejs/tutorials/create-a-website-(mac)/) et [utiliser des outils en ligne de commande Azure pour Mac et Linux](/en-us/develop/nodejs/how-to-guides/command-line-tools/).

