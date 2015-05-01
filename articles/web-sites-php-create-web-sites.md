<properties 
	pageTitle="Créer une application web en PHP dans Microsoft Azure App Service" 
	description="Découvrez comment créer une application web en PHP dans Microsoft Azure App Service." 
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
	ms.topic="article" 
	ms.date="03/24/2015" 
	ms.author="tomfitz"/>

# Créer une application web en PHP dans Microsoft Azure App Service

## Vue d'ensemble
Cet article explique comment créer une application web en PHP dans [Microsoft Azure App Service], en utilisant [Microsoft Azure Portal], [les outils en ligne de commande Microsoft Azure pour Mac et Linux][xplat-outils], ou [les applets de commande PowerShell Microsoft Azure][powershell-cmdlets].

En général, la procédure de création d'une application web en PHP diffère peu de celle qu'il faut suivre pour créer  *any* application dans Microsoft Azure Web Service. Par défaut, le langage PHP est activé pour toutes les applications Web. Pour en savoir plus sur la configuration de PHP (ou le fait de fournir votre propre runtime personnalisé en PHP), voir [Configurer PHP dans Azure App Service Web Apps].

Chaque option décrite ci-dessous indique comment créer gratuitement une application Web dans un environnement d'hébergement partagé, en respectant certaines limites d'utilisation en termes de processeur et de bande passante. Pour en savoir plus, voir [Service d'application Tarification]. Pour en savoir plus sur la mise à niveau et la mise à l'échelle de votre application web dans App Service, voir [Mise à l'échelle d'une application web dans Microsoft Azure App Service].

>[AZURE.NOTE] Si vous voulez vous familiariser avec Azure App Service avant d'ouvrir un compte Azure, accédez à la page [Essayer App Service](http://go.microsoft.com/fwlink/?LinkId=523751). Vous pourrez créer immédiatement et gratuitement une application de départ temporaire dans App Service. Aucune carte de crédit n'est requise ; vous ne prenez aucun engagement.

<a name="portal"></a>
## Créer une application web en PHP à l'aide de Microsoft Azure Portal

Lorsque vous créez une application web en PHP dans Microsoft Azure Portal, vous disposez de trois options : 

- **Création rapide** (voir [Création d'une application web via Microsoft Asure Portal)](web-sites-create-deploy.md#createawebsiteportal)
- **Créer avec une base de données** (voir [Créer une application web PHP-MySQL dans Azure App Service et la déployer à l'aide de Git)] 
- **Depuis le site Marketplace** (voir [Créer une application web WordPress dans Azure App Service)]

<a name="XplatTools"></a>
## Créer une application web PHP avec les outils en ligne de commande Microsoft Azure pour Mac et Linux

Pour créer une application web en PHP avec ces outils, procédez comme suit :

1. Installez les outils en ligne de commande Azure en suivant les instructions disponibles ici : [Installation des outils en ligne de commande Azure](xplat-cli.md#install).

1. Téléchargez et importez votre fichier de paramètres de publication en suivant les instructions disponibles ici : [Téléchargement et importation des paramètres de publication](xplat-cli.md#configure).

1. Exécutez la commande suivante depuis une invite de commandes :

		azure site create MyWebAppName

L'URL de l'application web que vous venez de créer correspondra à `http://MyWebAppName.azurewebsites.net`.  
 
Remarque : vous pouvez exécuter la commande `azure site create` avec l'une ou l'autre des options suivantes :

* `--location [nom de l'emplacement]`. Cette option vous permet de spécifier l'emplacement du centre de données dans lequel votre application web est créée (par exemple, " Bretagne "). Si vous omettez cette option, vous êtes invité à choisir un emplacement.
* `--hostname [nom de l'hôte personnalisé]`. Cette option vous permet de spécifier un nom d'hôte personnalisé pour votre application web.
* `--git`. Cette option vous permet d'utiliser le logiciel Git pour publier des données dans votre application web, en créant des référentiels Git à la fois dans votre répertoire d'application local et dans le centre de données de votre application web. Remarque : si votre dossier local est déjà un référentiel Git, la commande ajoute une nouvelle référence distante au référentiel existant, qui pointe vers le référentiel du centre de données de votre application web.

<a name="PowerShell"></a>
## Créer une application web en PHP avec les applets de commande PowerShell Microsoft Azure

Pour créer application web en PHP avec les applets de commande PowerShell Azure, procédez comme suit :

1. Installez les applets de commande PowerShell Azure en suivant les instructions disponibles ici : [Prise en main de Microsoft Azure PowerShell](/develop/php/how-to-guides/powershell-cmdlets/#GetStarted).

1. Téléchargez et importez votre fichier de paramètres de publication en suivant les instructions disponibles ici : [Importation des paramètres de publication](/develop/php/how-to-guides/powershell-cmdlets/#ImportPubSettings).

1. Ouvrez une invite de commandes PowerShell et exécutez la commande suivante :

		New-AzureWebsite MyWebAppName

L'URL de l'application web que vous venez de créer correspondra à`http://MyWebAppName.azurewebsites.net`.  
 
Remarque : vous pouvez exécuter la commande `New-AzureWebsite` avec l'une ou l'autre des options suivantes :

* `-Location [nom de l'emplacement]`. Cette option vous permet de spécifier l'emplacement du centre de données dans lequel votre application web est créée (par exemple, " Bretagne "). Si vous omettez cette option, vous êtes invité à choisir un emplacement.
* `-Hostname [nom de l'hôte personnalisé]`. Cette option vous permet de spécifier un nom d'hôte personnalisé pour votre application web.
* `-Git`. Cette option vous permet d'utiliser le logiciel Git pour publier des données dans votre application web, en créant des référentiels Git à la fois dans votre répertoire d'application local et dans le centre de données de votre application web. Remarque : si votre dossier local est déjà un référentiel Git, la commande ajoute une nouvelle référence distante au référentiel existant, qui pointe vers le référentiel du centre de données de votre application web.

<a name="NextSteps"></a>
## Étapes suivantes

Maintenant que vous avez créé une application web en PHP dans Microsoft Azure App Service, vous pouvez gérer, configurer, surveiller, déployer et mettre votre application à l'échelle. Pour plus d'informations, consultez les liens suivants :

* [Configurer des applications web dans Azure App Service](web-sites-configure.md)
* [Configurer PHP dans Azure App Service Web Apps]
* [Gérer les applications web à l'aide de Microsoft Azure Portal](web-sites-manage.md)
* [Surveiller les applications web dans Azure App Service](web-sites-monitor.md)
* [Mise à l'échelle d'une application web dans Microsoft Azure App Service]
* [Déploiement continu à l'aide de Git dans Azure App Service](web-sites-publish-source-control.md)

Pour obtenir des didacticiels complets, rendez-vous sur la page [Didacticiels du Centre de développement PHP](/develop/php/tutorials/) .

## Changements apportés
* Pour obtenir des informations détaillées sur le passage de Sites Web à App Service, consultez : [Azure App Service et son impact sur les services Azure existants](http://go.microsoft.com/fwlink/?LinkId=529714)
* Pour obtenir des informations détaillées sur le passage de l'ancien portail au nouveau portail, consultez : [Référence pour la navigation dans le portail en version préliminaire](http://go.microsoft.com/fwlink/?LinkId=529715)

[Azure App Service]: http://go.microsoft.com/fwlink/?LinkId=529714
[Portail Azure]: http://go.microsoft.com/fwlink/?LinkId=529715
[xplat-tools]: xplat-cli.md
[powershell-cmdlets]: powershell-install-configure.md
[Configurer PHP dans Azure App Service Web Apps]: web-sites-php-configure.md
[Créer une application web PHP-MySQL dans Azure App Service et la déployer à l'aide de Git]: web-sites-php-mysql-deploy-use-git.md
[Créer une application web WordPress dans Azure App Service]: web-sites-php-web-site-gallery.md
[Service d'application Tarification]: /pricing/details/app-service/
[Mise à l'échelle d'une application web dans Microsoft Azure App Service]: web-sites-scale.md


<!--HONumber=52-->