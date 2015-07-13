<properties
	pageTitle="Créer une application web en PHP dans Microsoft Azure App Service"
	description="Découvrez comment créer une application web en PHP dans Microsoft Azure App Service."
	documentationCenter="php"
	services="app-service\web"
	editor="mollybos"
	manager="wpickett"
	authors="tfitzmac"/>

<tags
	ms.service="app-service-web"
	ms.workload="web"
	ms.tgt_pltfrm="na"
	ms.devlang="PHP"
	ms.topic="hero-article"
	ms.date="04/29/2015"
	ms.author="tomfitz"/>

# Créer une application web en PHP dans Microsoft Azure App Service

## Vue d’ensemble
Cet article explique comment créer une application web PHP dans [Azure App Service] à l’aide du [portail Azure en version préliminaire](https://portal.azure.com), de l’[interface de ligne de commande Azure][Azure CLI] ou des [cmdlets PowerShell Azure][powershell-cmdlets].

En général, la création d’une application web en PHP diffère peu de la création de *toute autre* application web dans Azure App Service. Par défaut, le langage PHP est activé pour toutes les applications Web. Pour en savoir plus sur la configuration de PHP (ou le fait de fournir votre propre runtime personnalisé en PHP), consultez [Configuration PHP dans Azure App Service Web Apps].

Chaque option décrite ci-dessous indique comment créer gratuitement une application web dans un environnement d’hébergement partagé, en respectant certaines limites d’utilisation en termes de processeur et de bande passante. Pour en savoir plus, consultez [Tarification d’App Service]. Pour en savoir plus sur la mise à niveau et la mise à l’échelle de votre application web dans App Service, consultez [Mise à l’échelle d’une application web dans Azure App Service].

>[AZURE.NOTE]Si vous voulez vous familiariser avec Azure App Service avant d’ouvrir un compte Azure, accédez à la page [Essayer App Service](http://go.microsoft.com/fwlink/?LinkId=523751). Vous pourrez créer immédiatement et gratuitement une application de départ temporaire dans App Service. Aucune carte de crédit n’est requise ; vous ne prenez aucun engagement.

## Création d’une application web en PHP à l’aide du portail Azure en version préliminaire

Lorsque vous créez une application web en PHP dans le portail Azure en version préliminaire, vous disposez de trois options :

- **Création rapide** : consultez [Procédure de création d’une application web à l’aide du portail Azure en version préliminaire](../web-sites-create-deploy.md#createawebsiteportal).
- **Création avec une base de données** : consultez [Création d’une application web PHP-MySQL dans Azure App Service et déploiement à l’aide de Git].
- **Depuis le Marketplace** : consultez [Création d’une application web WordPress dans Azure App Service].

## Création d’une application web en PHP à l’aide de l’interface de ligne de commande Azure

Pour créer une application web à l’aide de l’interface de ligne de commande Azure, procédez comme suit :

1. Installez l’interface de ligne de commande Azure en suivant les instructions dans : [Installation de l’interface de ligne de commande Azure](../xplat-cli.md#install).

1. Téléchargez et importez votre fichier de paramètres de publication en suivant les instructions disponibles ici : [Procédure de téléchargement et d’importation des paramètres de publication](../xplat-cli.md#configure).

1. Exécutez la commande suivante depuis une invite de commandes :

		azure site create MyWebAppName

L’URL de l’application web que vous venez de créer sera `http://MyWebAppName.azurewebsites.net`.

Remarque : vous pouvez exécuter la commande `azure site create` avec l’une ou l’autre des options suivantes :

* `--location [location name]`. Cette option vous permet de spécifier l’emplacement du centre de données dans lequel votre application web est créée (par exemple, « Ouest des États-Unis »). Si vous omettez cette option, vous êtes invité à choisir un emplacement.
* `--hostname [custom host name]`. Cette option vous permet de spécifier un nom d’hôte personnalisé pour votre application web.
* `--git`. Cette option vous permet d’utiliser le logiciel Git pour publier des données dans votre application web, en créant des référentiels Git à la fois dans votre répertoire d’application local et dans le centre de données de votre application web. Remarque : si votre dossier local est déjà un référentiel Git, la commande ajoute une nouvelle référence distante au référentiel existant, qui pointe vers le référentiel du centre de données de votre application web.

## Créer une application web en PHP avec les applets de commande PowerShell Microsoft Azure

Pour créer application web en PHP avec les applets de commande PowerShell Azure, procédez comme suit :

1. Installez les cmdlets PowerShell Azure d’Azure en suivant les instructions disponibles ici : [Prise en main d’Azure PowerShell](/develop/php/how-to-guides/powershell-cmdlets/#GetStarted).

1. Téléchargez et importez votre fichier de paramètres de publication en suivant les instructions disponibles ici : [Procédure d’importation des paramètres de publication](/develop/php/how-to-guides/powershell-cmdlets/#ImportPubSettings).

1. Ouvrez une invite de commandes PowerShell et exécutez la commande suivante :

		New-AzureWebsite MyWebAppName

L’URL de l’application web que vous venez de créer sera `http://MyWebAppName.azurewebsites.net`.

Remarque : vous pouvez exécuter la commande `New-AzureWebsite` avec l’une ou l’autre des options suivantes :

* `-Location [location name]`. Cette option vous permet de spécifier l’emplacement du centre de données dans lequel votre application web est créée (par exemple, « Ouest des États-Unis »). Si vous omettez cette option, vous êtes invité à choisir un emplacement.
* `-Hostname [custom host name]`. Cette option vous permet de spécifier un nom d’hôte personnalisé pour votre application web.
* `-Git`. Cette option vous permet d’utiliser le logiciel Git pour publier des données dans votre application web, en créant des référentiels Git à la fois dans votre répertoire d’application local et dans le centre de données de votre application web. Remarque : si votre dossier local est déjà un référentiel Git, la commande ajoute une nouvelle référence distante au référentiel existant, qui pointe vers le référentiel du centre de données de votre application web.

## Étapes suivantes

Maintenant que vous avez créé une application web en PHP dans Microsoft Azure App Service, vous pouvez gérer, configurer, surveiller, déployer et mettre votre application à l’échelle. Pour plus d’informations, consultez les liens suivants :

* [Configurer des applications web dans Azure App Service](web-sites-configure.md)
* [Configurer PHP dans Azure App Service Web Apps]
* [Gérer les applications web à l’aide de Microsoft Azure Portal](web-sites-manage.md)
* [Surveiller les applications web dans Microsoft Azure App Service](web-sites-monitor.md)
* [Mise à l’échelle d’une application web dans Microsoft Azure App Service]
* [Déploiement continu à l’aide de Git dans Azure App Service](web-sites-publish-source-control.md)

Pour des didacticiels complets, rendez-vous sur la page [Didacticiels du Centre de développement PHP](/develop/php/tutorials/).

## Changements apportés
* Pour obtenir un guide présentant les modifications apportées dans le cadre de la transition entre Sites Web et App Service, consultez la page [Azure App Service et les services Azure existants](http://go.microsoft.com/fwlink/?LinkId=529714).
* Pour obtenir un guide présentant les modifications apportées dans le cadre de la transition entre l’ancien et le nouveau portail, consultez la page [Références sur la navigation dans le portail Azure](http://go.microsoft.com/fwlink/?LinkId=529715).

[Azure App Service]: http://go.microsoft.com/fwlink/?LinkId=529714
[Azure Portal]: http://go.microsoft.com/fwlink/?LinkId=529715
[Azure CLI]: ../xplat-cli.md
[powershell-cmdlets]: ../powershell-install-configure.md
[Configuration PHP dans Azure App Service Web Apps]: web-sites-php-configure.md
[Configurer PHP dans Azure App Service Web Apps]: web-sites-php-configure.md
[Création d’une application web PHP-MySQL dans Azure App Service et déploiement à l’aide de Git]: web-sites-php-mysql-deploy-use-git.md
[Création d’une application web WordPress dans Azure App Service]: web-sites-php-web-site-gallery.md
[Tarification d’App Service]: /pricing/details/app-service/
[Mise à l’échelle d’une application web dans Microsoft Azure App Service]: web-sites-scale.md
[Mise à l’échelle d’une application web dans Azure App Service]: web-sites-scale.md
 

<!---HONumber=62-->