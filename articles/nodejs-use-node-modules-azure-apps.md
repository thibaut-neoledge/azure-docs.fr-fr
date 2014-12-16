<properties urlDisplayName="Working with Node.js Modules" pageTitle="Utilisation de modules Node.js" metaKeywords="" description="" metaCanonical="" services="" documentationCenter="nodejs" title="Using Node.js Modules with Azure applications" authors="larryfr" solutions="" manager="wpickett" editor="mollybos" />

<tags ms.service="na" ms.workload="na" ms.tgt_pltfrm="na" ms.devlang="nodejs" ms.topic="article" ms.date="09/17/2014" ms.author="larryfr" />





# Utilisation de modules Node.js avec des applications Azure

Ce document fournit des instructions sur l'utilisation de modules Node.js avec des applications hébergées sur Azure. Il vous explique comment vous assurer que votre application utilise une version spécifique d'un module et vous présente l'utilisation de modules natifs avec Azure.

Si vous savez utiliser les modules Node.js, les fichiers **package.json** et **npm-shrinkwrap.json**, vous trouverez ci-dessous un résumé rapide des sujets abordés dans cet article :

* Sites Web Azure comprend les fichiers **package.json** et **npm-shrinkwrap.json** et peut installer des modules basés sur les entrées de ces fichiers.
* Azure Cloud Services s'attend à ce que tous les modules soient installés dans l'environnement de développement et que le répertoire **node_modules** soit inclus dans le déploiement du package.

<div class="dev-callout">
<strong>Remarque</strong>
<p>Azure Virtual Machines n'est pas abordé dans cet article, car le déploiement sur une machine virtuelle dépend du système d'exploitation hébergé par cette dernière.</p>
</div>

<div class="dev-callout">
<strong>Remarque</strong>
<p>Il est possible d'activer la prise en charge d'installation des modules en utilisant les fichiers <b>package.json</b> ou <b>npm-shrinkwrap.json</b> sur Azure. Néanmoins, cette opération requiert la personnalisation des scripts par défaut utilisés pour les projets Cloud Services. Pour obtenir un exemple de la réalisation de cette opération, consultez la page <a href="http://nodeblog.azurewebsites.net/startup-task-to-run-npm-in-azure">Tâche de démarrage d'Azure permettant d'exécuter npm install pour éviter le déploiement des modules de nœud</a></p>
</div>

##Modules Node.js

Les modules sont des packages JavaScript chargeables qui fournissent une fonctionnalité spécifique à votre application. Un module est habituellement installé à l'aide de l'outil en ligne de commande **npm**. Néanmoins, certains modules (le module http, par exemple) sont intégrés au package Node.js principal.

Lorsque les modules sont installés, ils sont stockés dans le répertoire **node\_modules** à la racine de la structure de répertoires de votre application. Chaque module du répertoire **node\_modules** conserve son propre répertoire **node\_modules** qui comporte tous les modules dont il dépend. Cette structure se répète pour chaque module, tout au long de la chaîne de dépendance. Ainsi, chaque module installé est doté de ses propres exigences de version pour les modules dont il dépend, ce qui peut néanmoins résulter en une structure de répertoires assez volumineuse.

Lors du déploiement du répertoire **node\_modules** dans votre application, la taille du déploiement est accrue comparée à l'utilisation du fichier **package.json** ou **npm-shrinkwrap.json**. Néanmoins, ce type de déploiement garantit que la version des modules utilisés en production est la même que ceux utilisés en développement.

###Modules natifs

Alors que la plupart des modules sont simplement des fichiers JavaScript en texte brut, certains modules sont des images binaires propres à des plateformes. Ces modules sont compilés au moment de l'installation, en général à l'aide de Python et node-gyp. Une limitation spécifique de Sites Web Azure est que, bien qu'il comprenne de manière native comment installer les modules indiqués dans un fichier **package.json** ou **npm-shrinkwrap.json**, il ne fournit pas Python ou node-gyp et ne peut pas créer de modules natifs.

Azure Cloud Services se reposant sur le déploiement du dossier **node_modules** dans l'application, tout module natif inclus dans les modules installés doit fonctionner dans un service cloud, du moment où il a été installé et compilé dans un système de développement Windows. 

Les modules natifs ne sont pas pris en charge par Sites Web Azure. Certains modules, tels que JSDOM et MongoDB, comportent des dépendances natives facultatives et fonctionnent avec des applications hébergées dans Sites Web Azure.

###Utilisation du fichier package.json

Le fichier **package.json** permet de spécifier les dépendances de niveau supérieur requises par votre application afin que la plateforme d'hébergement puisse installer les dépendances, plutôt que de vous obliger à inclure le dossier **node\_packages** dans le déploiement. Une fois que l'application a été déployée, la commande **npm install** permet d'analyser le fichier **package.json** et d'installer toutes les dépendances répertoriées.

Au cours du développement, vous pouvez utiliser les paramètres **--save**, **--save-dev** ou **--save-optional** lors de l'installation de modules afin d'ajouter automatiquement une entrée pour le module à votre fichier **package.json**. Pour plus d'informations, consultez la page [npm-install](https://npmjs.org/doc/install.html).

Un problème potentiel avec le fichier **package.json** est qu'il n'indique la version que pour les dépendances de niveau supérieur. Chaque module installé peut spécifier ou non la version des modules dont il dépend. Ainsi, il est possible que vous obteniez une chaîne de dépendance différente de celle utilisée en développement. 

> [WACOM.NOTE]
> Lors du déploiement sur un site web Azure, si votre fichier <b>package.json</b> fait référence à un module natif, une erreur similaire à celle figurant ci-dessous s'affiche lors de la publication de l'application à l'aide de Git :

>		npm ERR! module-name@0.6.0 install: 'node-gyp configure build'

>		npm ERR! 'cmd "/c" "node-gyp configure build"' failed with 1	


###Utilisation d'un fichier npm-shrinkwrap.json

Le fichier **npm-shrinkwrap.json** tente de résoudre les limitations de contrôle de version des modules du fichier **package.json**. Alors que le fichier **package.json** n'inclut que des versions pour les modules de niveau supérieur, le fichier **npm-shrinkwrap.json** contient les exigences de version pour l'ensemble de la chaîne de dépendance des modules.

Lorsque votre application est prête pour la production, vous pouvez verrouiller les exigences de version et créer un fichier **npm-shrinkwrap.json** à l'aide de la commande **npm shrinkwrap**. Elle utilise les versions actuellement installées dans le dossier **node\_modules** et les enregistre dans le fichier **npm-shrinkwrap.json**. Une fois que l'application a été déployée vers l'environnement d'hébergement, la commande **npm install** permet d'analyser le fichier **npm-shrinkwrap.json** et d'installer toutes les dépendances répertoriées. Pour plus d'informations, consultez la page [npm-install](https://npmjs.org/doc/install.html).

> [WACOM.NOTE]
>Lors du déploiement sur un site web Azure, si votre fichier <b>npm-shrinkwrap.json</b> fait référence à un module natif, une erreur similaire à celle figurant ci-dessous s'affiche lors de la publication de l'application à l'aide de Git :
		
>		npm ERR! module-name@0.6.0 install: 'node-gyp configure build'

>		npm ERR! 'cmd "/c" "node-gyp configure build"' failed with 1


##Étapes suivantes

Maintenant que vous savez comment utiliser les modules Node.js avec Azure, apprenez à [spécifier la version de Node.js], à [créer et déployer un site web Node.js] et [à utiliser les outils en ligne de commande Azure pour Mac et Linux].

[spécifier la version de Node.js]: /fr-fr/documentation/articles/nodejs-specify-node-version-azure-apps/
[Utilisation des outils en ligne de commande Azure pour Mac et Linux]: /fr-fr/documentation/articles/xplat-cli/
[créer et déployer un site web Node.js]: /fr-fr/documentation/articles/web-sites-nodejs-develop-deploy-mac/
[Application web Node.js avec stockage sur MongoDB (MongoLab)]: /fr-fr/documentation/articles/store-mongolab-web-sites-nodejs-store-data-mongodb/
[Publication avec Git]: /fr-fr/documentation/articles/web-sites-publish-source-control/
[Création et déploiement d'une application Node.js dans Azure Cloud Services]: /fr-fr/documentation/articles/cloud-services-nodejs-develop-deploy-app/


