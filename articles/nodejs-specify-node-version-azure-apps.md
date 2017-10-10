---
title: "Spécification d'une version Node.js"
description: "Découvrez comment spécifier la version de Node.js utilisée par Azure Web Sites et Azure Cloud Services"
services: 
documentationcenter: nodejs
author: TomArcher
manager: routlaw
editor: 
ms.assetid: d0e15278-2ab4-4ec8-8256-913839c6d5ef
ms.service: multiple
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: nodejs
ms.topic: article
ms.date: 08/17/2016
ms.author: tarcher
ms.translationtype: HT
ms.sourcegitcommit: 8f9234fe1f33625685b66e1d0e0024469f54f95c
ms.openlocfilehash: a20179c72b227deb14df442bea7b80cf31728aa7
ms.contentlocale: fr-fr
ms.lasthandoff: 09/20/2017

---
# <a name="specifying-a-nodejs-version-in-an-azure-application"></a>Spécification d'une version Node.js dans une application Azure
Pendant l'hébergement d'une application Node.js, vous pouvez vous assurer que votre application utilise une version spécifique de Node.js. Il existe diverses façons d'accomplir cela pour les applications hébergées sur Azure.

## <a name="default-versions"></a>Versions par défaut
Les versions de Node.js fournies par Azure sont constamment mises à jour. Sauf indication contraire, la version par défaut spécifiée dans la variable d’environnement `WEBSITE_NODE_DEFAULT_VERSION` sera utilisée. Pour remplacer cette valeur par défaut, suivez les étapes décrites dans les sections ci-après.

> [!NOTE]
> Si vous hébergez votre application dans un service cloud Azure (rôle Web ou de travail) et que c'est la première fois que vous la déployez, Azure essaiera d'utiliser la même version de Node.js que celle que vous avez installée dans votre environnement de développement si elle correspond à l'une des versions par défaut disponibles dans Azure.
>
>

## <a name="versioning-with-packagejson"></a>Contrôle de version avec package.json
Vous pouvez spécifier la version de Node.js à utiliser en ajoutant ce qui suit dans votre fichier **package.json** :

    "engines":{"node":version}

*version* correspond au numéro de version spécifique à utiliser. Vous pouvez spécifier des conditions plus complexes, telles que :

    "engines":{"node": "0.6.22 || 0.8.x"}

Étant donné que la version 0.6.22 n'est pas l'une des versions disponibles dans l'environnement d'hébergement, la version la plus élevée de la série 0.8 qui est disponible sera utilisée à la place : 0.8.4.

## <a name="versioning-websites-with-app-settings"></a>Contrôle de version des sites web avec les paramètres d'application
Si vous hébergez l'application dans un site web, vous pouvez définir la variable d'environnement **WEBSITE_NODE_DEFAULT_VERSION** sur la version souhaitée.

## <a name="versioning-cloud-services-with-powershell"></a>Contrôle de version des services cloud avec PowerShell
Si vous hébergez l'application dans un service cloud et que vous déployez l'application au moyen d'Azure PowerShell, vous pouvez remplacer la version Node.js par défaut en utilisant l'applet de commande PowerShell **Set-AzureServiceProjectRole** . Par exemple :

    Set-AzureServiceProjectRole WebRole1 Node 0.8.4

Notez que les paramètres dans l'instruction ci-dessus respectent la casse.  Vous pouvez vérifier que la version correcte de Node.js a été sélectionnée en vérifiant la propriété **moteurs** dans le **package.json** de votre rôle.

Vous pouvez également utiliser **Get-AzureServiceProjectRoleRuntime** pour extraire une liste des versions de Node.js disponibles pour les applications hébergées en tant que service cloud.  Vérifiez toujours que la version de Node.js dont dépend votre projet est dans cette liste.

## <a name="using-a-custom-version-with-azure-websites"></a>Utilisation d'une version personnalisée avec Azure Websites
Bien qu'Azure fournisse plusieurs versions par défaut de Node.js, vous pouvez utiliser une version qui n'est pas fournie par défaut. Si votre application est hébergée en tant que site web Azure, vous pouvez pour cela utiliser le fichier **iisnode.yml** . La procédure suivante explique comment utiliser une version personnalisée de Node.Js avec un site web Azure :

1. Créez un répertoire, puis un fichier **server.js** dans celui-ci. Le fichier **server.js** doit contenir ce qui suit :

        var http = require('http');
        http.createServer(function(req,res) {
          res.writeHead(200, {'Content-Type': 'text/html'});
          res.end('Hello from Azure running node version: ' + process.version + '</br>');
        }).listen(process.env.PORT || 3000);

    Ce code permet d'afficher la version de Node.js qui est utilisée lorsque vous naviguez sur le site web.
2. Créez un site web et prenez note de son nom. Par exemple, la commande suivante utilise les [outils en ligne de commande Azure] pour créer un site web Azure nommé **mywebsite**, puis active un référentiel Git pour le site web.

        azure site create mywebsite --git
3. Créez un répertoire nommé **bin**, enfant du répertoire contenant le fichier **server.js**.
4. Téléchargez la version spécifique de **node.exe** (version Windows) que vous voulez utiliser avec votre application. Par exemple, la commande suivante utilise **curl** pour télécharger la version 0.8.1 :

        curl -O http://nodejs.org/dist/v0.8.1/node.exe

    Enregistrez le fichier **node.exe** dans le dossier **bin** créé précédemment.
5. Créez un fichier **iisnode.yml** dans le même répertoire que le fichier **server.js**, puis ajoutez le contenu suivant au fichier **iisnode.yml** :

        nodeProcessCommandLine: "D:\home\site\wwwroot\bin\node.exe"

    Ce chemin correspond à l'emplacement du fichier **node.exe** de votre projet une fois que vous aurez publié votre application sur le site web Azure.
6. Publiez votre application. Par exemple, étant donné que nous avons créé un site web avec le paramètre --git précédemment, les commandes suivantes permettront d'ajouter les fichiers d'application dans le référentiel Git local, puis de les transférer vers le référentiel du site web :

        git add .
        git commit -m "testing node v0.8.1"
        git push azure master

    Une fois l'application publiée, ouvrez le site web dans un navigateur. Le message suivant doit apparaître : « Hello from Azure running node version: v0.8.1 ».

## <a name="next-steps"></a>Étapes suivantes
Maintenant que vous savez comment spécifier la version de Node.js utilisée par votre application, découvrez comment [utiliser des modules], [créer et déployer un site Web Node.js](app-service/app-service-web-get-started-nodejs.md) et [utiliser des outils en ligne de commande Azure pour Mac et Linux].

Pour plus d’informations, consultez le [Centre pour développeurs Node.js](https://azure.microsoft.com/develop/nodejs/).

[utiliser des outils en ligne de commande Azure pour Mac et Linux]:cli-install-nodejs.md
[outils en ligne de commande Azure]:cli-install-nodejs.md
[utiliser des modules]: nodejs-use-node-modules-azure-apps.md
[build and deploy a Node.js Web Site]: app-service/app-service-web-get-started-nodejs.md

