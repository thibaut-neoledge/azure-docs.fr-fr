---
title: Utilisation de modules Node.js
description: "Découvrez comment utiliser des modules Node.js avec Azure App Service ou Cloud Services."
services: 
documentationcenter: nodejs
author: rmcmurray
manager: erikre
editor: 
ms.assetid: c0e6cd3d-932d-433e-b72d-e513e23b4eb6
ms.service: multiple
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: nodejs
ms.topic: article
ms.date: 12/22/2016
ms.author: robmcm
translationtype: Human Translation
ms.sourcegitcommit: f9a216f3f76ef84e745ef29575d20da971e5eb61
ms.openlocfilehash: 2ccd414e0eec04dbee7c412cef245e1ab0da2bff


---
# <a name="using-nodejs-modules-with-azure-applications"></a>Utilisation de modules Node.js avec des applications Azure
Ce document fournit des instructions sur l'utilisation de modules Node.js avec des applications hébergées sur Azure. Il vous explique comment vous assurer que votre application utilise une version spécifique d'un module et vous présente l'utilisation de modules natifs avec Azure.

Si vous savez utiliser les modules Node.js, les fichiers **package.json** et **npm-shrinkwrap.json**, vous trouverez ci-dessous un résumé rapide des sujets abordés dans cet article :

* Azure App Service comprend les fichiers **package.json** et **npm-shrinkwrap.json** et peut installer des modules basés sur les entrées de ces fichiers.
* Azure Cloud Services s'attend à ce que tous les modules soient installés dans l'environnement de développement et que le répertoire **node\_modules** soit inclus dans le déploiement du package. Il est possible d'activer la prise en charge d'installation des modules en utilisant les fichiers **package.json** ou **npm-shrinkwrap.json** sur Cloud Services. Néanmoins, cette opération requiert la personnalisation des scripts par défaut utilisés pour les projets Cloud Services. Pour obtenir un exemple de la réalisation de cette opération, consultez la page [Tâche de démarrage d'Azure permettant d'exécuter npm install pour éviter le déploiement des modules de nœud](https://github.com/woloski/nodeonazure-blog/blob/master/articles/startup-task-to-run-npm-in-azure.markdown)

> [!NOTE]
> Azure Virtual Machines n'est pas abordé dans cet article, car le déploiement sur une machine virtuelle dépend du système d'exploitation hébergé par cette dernière.
> 
> 

## <a name="nodejs-modules"></a>Modules Node.js
Les modules sont des packages JavaScript chargeables qui fournissent une fonctionnalité spécifique à votre application. Un module est habituellement installé à l'aide de l'outil en ligne de commande **napm**. Néanmoins, certains modules (le module http par exemple) sont intégrés au package Node.js principal.

Lorsque les modules sont installés, ils sont stockés dans le répertoire **node\_modules** à la racine de la structure de répertoires de votre application. Chaque module du répertoire **node\_modules** conserve son propre répertoire **node\_modules** qui comporte tous les modules dont il dépend. Cette structure se répète pour chaque module, tout au long de la chaîne de dépendance. Ainsi, chaque module installé est doté de ses propres exigences de version pour les modules dont il dépend, ce qui peut néanmoins résulter en une structure de répertoires assez volumineuse.

Lors du déploiement du répertoire **node\_modules** dans votre application, la taille du déploiement est accrue comparée à l'utilisation du fichier **package.json** ou **npm-shrinkwrap.json**. Néanmoins, ce type de déploiement garantit que la version des modules utilisés en production est la même que ceux utilisés en développement.

### <a name="native-modules"></a>Modules natifs
Alors que la plupart des modules sont simplement des fichiers JavaScript en texte brut, certains modules sont des images binaires propres à des plateformes. Ces modules sont compilés au moment de l'installation, en général à l'aide de Python et node-gyp. Azure Cloud Services se reposant sur le déploiement du dossier **node\_modules** dans l'application, tout module natif inclus dans les modules installés doit fonctionner dans un service cloud, du moment où il a été installé et compilé dans un système de développement Windows.

Azure App Service ne prend pas en charge tous les modules natifs et risque de ne pas pouvoir les compiler si des composants très spécifiques sont requis. Alors que certains modules populaires tels que MongoDB ont des dépendances natives facultatives et fonctionnent correctement sans celles-ci, deux solutions se sont avérées opérationnelles avec presque tous les modules natifs disponibles à ce jour :

* Exécutez **npm install** sur un ordinateur Windows sur lequel sont installés tous les composants requis du module native. Ensuite, déployez le dossier **node\_modules** créé dans le cadre de l'application sur Azure App Service.
  * Avant de compiler, vérifiez que votre installation locale de Node.js dispose de la bonne architecture et que la version est aussi proche que possible de celle utilisée dans Azure (les valeurs peuvent être vérifiées lors de l’exécution dans les propriétés **process.arch** et **process.version**).
* Azure App Service peut être configuré pour exécuter des scripts Shell ou Bash personnalisés pendant le déploiement, ce qui vous permet d'exécuter des commandes personnalisées et de configurer précisément l'exécution de **npm install** . Pour voir une vidéo montrant comment procéder, consultez [Scripts de déploiement de site web personnalisés avec Kudu].

### <a name="using-a-packagejson-file"></a>Utilisation du fichier package.json
Le fichier **package.json** permet de spécifier les dépendances de niveau supérieur requises par votre application afin que la plateforme d'hébergement puisse installer les dépendances, plutôt que de vous obliger à inclure le dossier **node\_packages** dans le déploiement. Une fois que l'application a été déployée, la commande **npm install** permet d'analyser le fichier **package.json** et d'installer toutes les dépendances répertoriées.

Au cours du développement, vous pouvez utiliser les paramètres **--save**, **--save-dev** ou **--save-optional** lors de l'installation de modules afin d'ajouter automatiquement une entrée pour le module à votre fichier **package.json**. Pour plus d'informations, consultez la page [npm-install](https://docs.npmjs.com/cli/install).

Un problème potentiel avec le fichier **package.json** est qu'il n'indique la version que pour les dépendances de niveau supérieur. Chaque module installé peut spécifier ou non la version des modules dont il dépend. Ainsi, il est possible que vous obteniez une chaîne de dépendance différente de celle utilisée en développement.

> [!NOTE]
> Pendant le déploiement sur Azure App Service, si votre fichier <b>package.json</b> fait référence à un module natif, une erreur similaire à celle figurant ci-dessous s'affiche au moment de la publication de l'application à l'aide de Git :
> 
> npm ERR! module-name@0.6.0 install: 'node-gyp configure build'
> 
> npm ERR! 'cmd "/c" "node-gyp configure build"' failed with 1
> 
> 

### <a name="using-a-npm-shrinkwrapjson-file"></a>Utilisation d'un fichier npm-shrinkwrap.json
Le fichier **npm-shrinkwrap.json** tente de résoudre les limitations de contrôle de version des modules du fichier **package.json**. Alors que le fichier **package.json** n'inclut que des versions pour les modules de niveau supérieur, le fichier **npm-shrinkwrap.json** contient les exigences de version pour l'ensemble de la chaîne de dépendance des modules.

Lorsque votre application est prête pour la production, vous pouvez verrouiller les exigences de version et créer un fichier **npm-shrinkwrap.json** à l'aide de la commande **npm shrinkwrap**. Elle utilise les versions actuellement installées dans le dossier **node\_modules** et les enregistre dans le fichier **npm-shrinkwrap.json**. Une fois que l'application a été déployée vers l'environnement d'hébergement, la commande **npm install** permet d'analyser le fichier **npm-shrinkwrap.json** et d'installer toutes les dépendances répertoriées. Pour plus d’informations, consultez [npm-shrinkwrap](https://docs.npmjs.com/cli/shrinkwrap).

> [!NOTE]
> Pendant le déploiement sur Azure App Service, si votre fichier <b>npm-shrinkwrap.json</b> fait référence à un module natif, une erreur similaire à celle figurant ci-dessous s'affiche au moment de la publication de l'application à l'aide de Git :
> 
> npm ERR! module-name@0.6.0 install: 'node-gyp configure build'
> 
> npm ERR! 'cmd "/c" "node-gyp configure build"' failed with 1
> 
> 

## <a name="next-steps"></a>Étapes suivantes
Maintenant que vous savez comment utiliser les modules Node.js avec Azure, découvrez comment [spécifier la version de Node.js], [créer et déployer une application web Node.js](app-service-web/web-sites-nodejs-develop-deploy-mac.md) et [utiliser l'interface de ligne de commande Azure pour Mac et Linux].

Pour plus d’informations, consultez le [Centre pour développeurs Node.js](/develop/nodejs/).

[spécifier la version de Node.js]: nodejs-specify-node-version-azure-apps.md
[utiliser l'interface de ligne de commande Azure pour Mac et Linux]: xplat-cli-install.md
[build and deploy a Node.js web app]: web-sites-nodejs-develop-deploy-mac.md
[Node.js Web Application with Storage on MongoDB (MongoLab)]: store-mongolab-web-sites-nodejs-store-data-mongodb.md
[Build and deploy a Node.js application to an Azure Cloud Service]: cloud-services-nodejs-develop-deploy-app.md
[Scripts de déploiement de site web personnalisés avec Kudu]: /documentation/videos/custom-web-site-deployment-scripts-with-kudu/



<!--HONumber=Jan17_HO2-->


