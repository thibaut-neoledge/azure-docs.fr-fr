<properties title="How to create a PHP website in Azure Websites" pageTitle="How to create a PHP website in Azure Websites" metaKeywords="PHP Azure Web Sites" description="Learn how to create a PHP website in Azure Websites" documentationCenter="PHP" services="Web Sites" editor="mollybos" manager="wpickett" authors="cephalin" />

<tags ms.service="web-sites" ms.workload="web" ms.tgt_pltfrm="na" ms.devlang="PHP" ms.topic="article" ms.date="01/01/1900" ms.author="cephalin"></tags>

# Création d'un site web PHP dans Sites Web Azure

Cet article vous indique comment créer un site web PHP dans [Sites Web Azure][] en utilisant le [portail de gestion Azure][], les [outils en ligne de commande Azure pour Mac et Linux][] ou les [applets de commande Azure PowerShell][].

En règle générale, créer un site web PHP ne diffère pas de la création d'un site web *de tout type* dans Sites Web Azure. Par défaut, PHP est activé pour tous les sites web. Pour plus d'informations sur la configuration de PHP (ou proposer votre propre runtime PHP personnalisé), consultez la page [Configuration de PHP dans Sites Web Azure][].

Chaque option décrite ci-dessous indique comment créer gratuitement un site web dans un environnement d'hébergement partagé, avec certaines limites d'utilisation en termes de processeur et de bande passante. Pour plus d’informations, consultez la page de [tarification des sites Web Azure][]. Pour plus d'informations sur la mise à niveau et les possibilités d'évolution de votre site web, consultez la page [Mise à l'échelle de sites web][].

## Sommaire

-   [Création d'un site Web avec le portail de gestion Azure][]
-   [Création d'un site Web avec les outils en ligne de commande pour Mac et Linux][]
-   [Création d'un site Web avec les cmdlets PowerShell Azure][]

## <a name="portal"></a>Création d'un site web PHP avec le portail de gestion Azure

Lorsque vous créez un site web dans le portail de gestion Azure, trois possibilités s'offrent à vous : **Quick Create**, **Create with Database** et **From Gallery**. Les instructions ci-dessous concernent l'option **Quick Create**. Pour plus d'informations sur les deux autres options, consultez les pages [Création et déploiement d'un site Web Azure PHP-MySQL avec Git][] et [Création d'un site Web WordPress à partir de la galerie dans Azure][].

Pour créer un site web PHP avec le portail de gestion Azure, procédez comme suit :

1.  Connectez-vous au [portail de gestion Azure][].
2.  Cliquez sur **New** en bas de la page, puis sélectionnez **Compute**, **Website** et **Quick Create**. Indiquez une **URL** pour votre site web, puis sélectionnez la **Région** de votre site web. Enfin, cliquez sur **Create Website**.

    ![Sélectionner Quick Create][]

## <a name="XplatTools"></a>Création d'un site web PHP avec les outils en ligne de commande pour Mac et Linux

Pour créer un site web PHP avec les outils en ligne de commande pour Mac et Linux, procédez comme suit :

1.  Installez les outils en ligne de commande Azure en suivant les instructions disponibles ici : [Installation des outils en ligne de commande Azure pour Mac et Linux][].

2.  Téléchargez et importez votre fichier de paramètres de publication en suivant les instructions disponibles ici : [Téléchargement et importation des paramètres de publication][].

3.  Exécutez la commande suivante depuis une invite de commandes :

        azure site create MySiteName

L'URL du nouveau site web sera `http://MySiteName.azurewebsites.net`.

Notez que vous pouvez exécuter la commande `azure site create` avec l'une des options suivantes :

-   `--location [location name]`. Cette option vous permet de spécifier l'emplacement du centre de données dans lequel votre site web est créé (« Ouest des États-Unis », par exemple). Si vous omettez cette option, vous êtes invité à choisir un emplacement.
-   `--hostname [custom host name]`. Cette option vous permet de spécifier un nom d'hôte personnalisé pour votre site web.
-   `--git`. Cette option vous permet d'utiliser git pour publier sur votre site web en créant des référentiels git à la fois dans votre répertoire d'application local et dans le centre de données de votre site web. Notez que si votre dossier local est déjà un référentiel git, la commande ajoute une nouvelle référence distante au référentiel existant, en pointant vers le référentiel du centre de données de votre site web.

Pour plus d'informations sur les options supplémentaires, consultez la page [Création et gestion d’un site Web Azure][].

## <a name="PowerShell"></a>Création d'un site web PHP avec les applets de commande PowerShell Azure

Pour créer un site web PHP avec les applets de commande PowerShell Azure, procédez comme suit :

1.  Installez les cmdlets PowerShell Azure en suivant les instructions disponibles ici : [Prise en main d'Azure PowerShell][].

2.  Téléchargez et importez votre fichier de paramètres de publication en suivant les instructions disponibles ici : [Importation des paramètres de publication][].

3.  Ouvrez une invite de commandes PowerShell et exécutez la commande suivante :

        New-AzureWebSite MySiteName

L'URL du nouveau site web sera `http://MySiteName.azurewebsites.net`.

Notez que vous pouvez exécuter la commande `New-AzureWebSite` avec l'une des options suivantes :

-   `-Location [location name]`. Cette option vous permet de spécifier l'emplacement du centre de données dans lequel votre site web est créé (« Ouest des États-Unis », par exemple). Si vous omettez cette option, vous êtes invité à choisir un emplacement.
-   `-Hostname [custom host name]`. Cette option vous permet de spécifier un nom d'hôte personnalisé pour votre site web.
-   `-Git`. Cette option vous permet d'utiliser git pour publier sur votre site web en créant des référentiels git à la fois dans votre répertoire d'application local et dans le centre de données de votre site web. Notez que si votre dossier local est déjà un référentiel git, la commande ajoute une nouvelle référence distante au référentiel existant, en pointant vers le référentiel du centre de données de votre site web.

Pour plus d'informations sur ces options complémentaires, consultez la page [Création et gestion d'un site Web Azure][].

## <a name="NextSteps"></a>Étapes suivantes

Maintenant que vous avez créé un site web PHP dans Sites Web Azure, vous pouvez gérer, configurer, surveiller, déployer et mettre à l'échelle votre site. Pour plus d’informations, consultez les liens suivants :

-   [Configuration des sites Web][]
-   [Configuration de PHP dans Sites Web Azure][Configuration de PHP dans Sites Web Azure]
-   [Gestion des sites Web][]
-   [Surveillance de sites Web][]
-   [Mise à l'échelle de sites Web][Mise à l'échelle de sites web]
-   [Publication avec Git][]

Pour des didacticiels complets, rendez-vous sur la page [Didacticiels du Centre de développement PHP][].

  [Sites Web Azure]: /fr-fr/manage/services/web-sites/
  [portail de gestion Azure]: http://manage.windowsazure.com/
  [outils en ligne de commande Azure pour Mac et Linux]: /fr-fr/develop/php/how-to-guides/command-line-tools/
  [applets de commande Azure PowerShell]: /fr-fr/develop/php/how-to-guides/powershell-cmdlets/
  [Configuration de PHP dans Sites Web Azure]: /fr-fr/develop/php/common-tasks/configure-php-web-site/
  [tarification des sites Web Azure]: http://www.windowsazure.com/fr-fr/pricing/details/#header-1
  [Mise à l'échelle de sites web]: /fr-fr/manage/services/web-sites/how-to-scale-websites/
  [Création d'un site Web avec le portail de gestion Azure]: #portal
  [Création d'un site Web avec les outils en ligne de commande pour Mac et Linux]: #XplatTools
  [Création d'un site Web avec les cmdlets PowerShell Azure]: #PowerShell
  [Création et déploiement d'un site Web Azure PHP-MySQL avec Git]: /fr-fr/develop/php/tutorials/website-w-mysql-and-git/
  [Création d'un site Web WordPress à partir de la galerie dans Azure]: /fr-fr/develop/php/tutorials/website-from-gallery/
  [Sélectionner Quick Create]: ./media/web-sites-php-create-web-sites/select-quickcreate-website.png
  [Installation des outils en ligne de commande Azure pour Mac et Linux]: /fr-fr/develop/php/how-to-guides/command-line-tools/#Download
  [Téléchargement et importation des paramètres de publication]: /fr-fr/develop/php/how-to-guides/command-line-tools/#Account
  [Création et gestion d’un site Web Azure]: /fr-fr/develop/php/how-to-guides/command-line-tools/#WebSites
  [Prise en main d'Azure PowerShell]: /fr-fr/develop/php/how-to-guides/powershell-cmdlets/#GetStarted
  [Importation des paramètres de publication]: /fr-fr/develop/php/how-to-guides/powershell-cmdlets/#ImportPubSettings
  [Création et gestion d'un site Web Azure]: /fr-fr/develop/php/how-to-guides/powershell-cmdlets/#WebSite
  [Configuration des sites Web]: /fr-fr/manage/services/web-sites/how-to-configure-websites/
  [Gestion des sites Web]: /fr-fr/manage/services/web-sites/how-to-manage-websites/
  [Surveillance de sites Web]: /fr-fr/manage/services/web-sites/how-to-monitor-websites/
  [Publication avec Git]: /fr-fr/develop/php/common-tasks/publishing-with-git/
  [Didacticiels du Centre de développement PHP]: /fr-fr/develop/php/tutorials/
