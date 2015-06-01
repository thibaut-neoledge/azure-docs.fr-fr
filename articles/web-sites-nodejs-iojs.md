<properties 
	pageTitle="Utilisation de io.js avec Azure App Service Web Apps" 
	description="Apprenez à utiliser une application web dans Azure App Service avec io.js." 
	services="app-service\web" 
	documentationCenter="nodejs" 
	authors="felixrieseberg" 
	manager="wpickett" 
	editor="mollybos"/>

<tags 
	ms.service="app-service-web" 
	ms.workload="web" 
	ms.tgt_pltfrm="na" 
	ms.devlang="nodejs" 
	ms.topic="article" 
	ms.date="04/23/2015" 
	ms.author="mwasson" />

# Utilisation de io.js avec Azure App Service Web Apps

L'embranchement (ou « fork » en anglais) bien connu de Node, [io.js], se différencie du projet Node.js de Joyent sur plusieurs points, avec notamment un modèle de gouvernance plus ouvert, un cycle de développement plus rapide et une adoption plus rapide des fonctionnalités nouvelles et expérimentales de JavaScript.

Bien que de nombreuses versions de Node.js soient préinstallées dans  [Azure App Service](http://go.microsoft.com/fwlink/?LinkId=529714) Web Apps, il accepte aussi les binaires Node.js fournis par les utilisateurs. Cet article décrit deux méthodes permettant l’utilisation de io.js sur App Service Web Apps : l'utilisation d'un script de déploiement étendu, qui configure automatiquement Azure pour utiliser la dernière version disponible de io.js, ainsi que le téléchargement manuel d'un binaire io.js.

<a id="deploymentscript"></a>
## Utilisation d'un script de déploiement

À l'occasion du déploiement d'une application Node.js, App Service Web Apps exécute un certain nombre de petites commandes pour s'assurer que l'environnement est configuré correctement. En utilisant un script de déploiement, ce processus peut être personnalisé pour inclure le téléchargement et la configuration de io.js.

Le [script de déploiement de io.js] est disponible sur GitHub. Pour activer io.js sur votre application web, il vous suffit de copier **.deployment**, **deploy.cmd** et **IISNode.yml** à la racine du dossier de votre application et de le déployer vers Web Apps.

Le premier fichier, **.deployment**, donne comme instruction à Web Apps d'exécuter **deploy.cmd** au moment du déploiement. Ce script exécute non seulement toutes les étapes habituelles pour une application Node.js, mais télécharge aussi la dernière version de io.js. Enfin, **IISNode.yml** configure le Web Apps pour qu'il utilise le binaire io.js que vous venez de télécharger à la place d'un binaire Node.js préinstallé.

> [AZURE.NOTE] Pour mettre à jour le binaire io.js utilisé, il vous suffit de redéployer votre application (le script télécharge une nouvelle version de io.js chaque fois que l'application est déployée).

<a id="manualinstallation"></a>
## Utilisation de l'installation manuelle

L'installation manuelle d'une version personnalisée de io.js se fait en deux étapes. Tout d'abord, téléchargez le binaire **win-x64** directement à partir de la [distribution io.js]. Deux fichiers sont nécessaires : **iojs.exe** et **iojs.lib**. Enregistrez ces deux fichiers dans un dossier à l'intérieur de votre application web, par exemple, dans **bin/iojs**.

Pour faire en sorte que Web Apps utilise **iojs.exe** à la place d'une version préinstallée de Node, créez un fichier **IISNode.yml** à la racine de votre application et ajoutez la ligne suivante.

    nodeProcessCommandLine: "D:\home\site\wwwroot\bin\iojs\iojs.exe"

<a id="nextsteps"></a>
## Étapes suivantes

Dans cet article, vous avez appris à utiliser io.js avec App Service Web Apps, en utilisant à la fois les scripts de déploiement fournis et l'installation manuelle.

> [AZURE.NOTE] io.js fait l'objet d'un développement intense et est plus souvent mis à jour que Node.js. Il est possible qu'un certain nombre de modules Node.js ne fonctionnent pas avec io.js. Consultez la rubrique consacrée à [io.js sur GitHub] pour résoudre les problèmes éventuels.

## Changements apportés
* Pour obtenir un guide présentant les modifications apportées dans le cadre de la transition entre Sites Web et App Service, consultez la page: [Azure App Service et les services Azure existants](http://go.microsoft.com/fwlink/?LinkId=529714)
* Pour obtenir un guide présentant les modifications apportées dans le cadre de la transition entre l'ancien et le nouveau portail, consultez: [Références sur la navigation dans le portail Azure](http://go.microsoft.com/fwlink/?LinkId=529715)

>[AZURE.NOTE] Si vous voulez vous familiariser avec Azure App Service avant d’ouvrir un compte Azure, accédez à la page [Essayer App Service](http://go.microsoft.com/fwlink/?LinkId=523751). Vous pourrez créer immédiatement et gratuitement une application de départ temporaire dans App Service. Aucune carte de crédit n’est requise ; vous ne prenez aucun engagement.

[io.js]: https://iojs.org
[distribution io.js]: https://iojs.org/dist/
[io.js sur GitHub]: https://github.com/iojs/io.js
[script de déploiement de io.js]: https://github.com/felixrieseberg/iojs-azure

<!--HONumber=52-->
