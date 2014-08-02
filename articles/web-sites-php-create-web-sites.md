<properties title="How to create a PHP web site in Azure Web Sites" pageTitle="How to create a PHP web site in Azure Web Sites" metaKeywords="PHP Azure Web Sites" description="Learn how to create a PHP web site in Azure Web Sites" documentationCenter="PHP" services="Web Sites" editor="mollybos" manager="bjsmith" authors="" />

Création d'un site Web PHP dans Sites Web Azure
===============================================

Cet article vous indique comment créer un site Web PHP dans [Sites Web Azure](/en-us/manage/services/web-sites/) en utilisant le [portail de gestion Azure](http://manage.windowsazure.com/), les [outils en ligne de commande Azure pour Mac et Linux](/en-us/develop/php/how-to-guides/command-line-tools/) ou les [cmdlets Azure PowerShell](/en-us/develop/php/how-to-guides/powershell-cmdlets/).

En règle générale, créer un site Web PHP ne diffère pas de la création d'un site Web *de tout type* dans Sites Web Azure. Par défaut, PHP est activé pour tous les sites Web. Pour plus d'informations sur la configuration de PHP (ou proposer votre propre runtime PHP personnalisé), consultez la page [Configuration de PHP dans Sites Web Azure](/en-us/develop/php/common-tasks/configure-php-web-site/).

Chaque option décrite ci-dessous indique comment créer gratuitement un site Web dans un environnement d'hébergement partagé, avec certaines limites d'utilisation en termes de processeur et de bande passante. Pour plus d’informations, consultez la page de [tarification des sites Web Azure](http://www.windowsazure.com/en-us/pricing/details/#header-1). Pour plus d'informations sur la mise à niveau et les possibilités d'évolution de votre site Web, consultez la page [Mise à l'échelle de sites Web](/en-us/manage/services/web-sites/how-to-scale-websites/).

Sommaire
--------

-   [Création d'un site Web avec le portail de gestion Azure](#portal)
-   [Création d'un site Web avec les outils en ligne de commande pour Mac et Linux](#XplatTools)
-   [Création d'un site Web avec les cmdlets PowerShell Azure](#PowerShell)

Création d'un site Web PHP avec le portail de gestion Azure
-----------------------------------------------------------

Lorsque vous créez un site Web dans le portail de gestion Azure, trois possibilités s'offrent à vous : **Quick Create**, **Create with Database** et **From Gallery**. Les instructions ci-dessous concernent l'option **Quick Create**. Pour plus d'informations sur les deux autres options, consultez les pages [Création et déploiement d'un site Web Azure PHP-MySQL avec Git](/en-us/develop/php/tutorials/website-w-mysql-and-git/) et [Création d'un site Web WordPress à partir de la galerie dans Azure](/en-us/develop/php/tutorials/website-from-gallery/).

Pour créer un site Web PHP avec le portail de gestion Azure, procédez comme suit :

1.  Connectez-vous au [portail de gestion Azure](http://manage.windowsazure.com/).
2.  Cliquez sur **New** en bas de la page, puis sélectionnez **Compute**, **Web Site** et **Quick Create**. Indiquez une **URL** pour votre site Web, puis sélectionnez la **Région** de votre site Web. Enfin, cliquez sur **Create Web Site**.

    ![Sélectionner Quick Create](./media/web-sites-php-create-web-sites/select-quickcreate-website.png)

Création d'un site Web PHP avec les outils en ligne de commande pour Mac et Linux
---------------------------------------------------------------------------------

Pour créer un site Web PHP avec les outils en ligne de commande pour Mac et Linux, procédez comme suit :

1.  Installez les outils en ligne de commande Azure en suivant les instructions disponibles ici : [Installation des outils en ligne de commande Azure pour Mac et Linux](/en-us/develop/php/how-to-guides/command-line-tools/#Download).

2.  Téléchargez et importez votre fichier de paramètres de publication en suivant les instructions disponibles ici : [Téléchargement et importation des paramètres de publication](/en-us/develop/php/how-to-guides/command-line-tools/#Account).

3.  Exécutez la commande suivante depuis une invite de commandes :

         azure site create MySiteName

L'URL du site Web nouvellement créé est `http://nomdusite.azurewebsites.net`.

Notez que vous pouvez exécuter la commande `azure site create` avec l’une des options suivantes :

-   `--location [nom de l'emplacement]`. Cette option vous permet de spécifier l’emplacement du centre de données dans lequel votre site Web est créé (par exemple, « Bretagne »). Si vous omettez cette option, vous êtes invité à choisir un emplacement.
-   `--hostname [nom de l'hôte personnalisé]`. Cette option vous permet de spécifier un nom d’hôte personnalisé pour votre site Web.
-   `--git`. Cette option vous permet d’utiliser git pour publier sur votre site Web en créant des référentiels git à la fois dans votre répertoire d’application local et dans le centre de données de votre site Web. Notez que si votre dossier local est déjà un référentiel git, la commande ajoute une nouvelle référence distante au référentiel existant, en pointant vers le référentiel du centre de données de votre site Web.

Pour plus d'informations sur les options supplémentaires, consultez la page [Création et gestion d’un site Web Azure](/en-us/develop/php/how-to-guides/command-line-tools/#WebSites).

Création d'un site Web PHP avec les cmdlets PowerShell Azure
------------------------------------------------------------

Pour créer un site Web PHP avec les cmdlets PowerShell Azure, procédez comme suit :

1.  Installez les cmdlets PowerShell Azure en suivant les instructions disponibles ici : [Prise en main d'Azure PowerShell](/en-us/develop/php/how-to-guides/powershell-cmdlets/#GetStarted).

2.  Téléchargez et importez votre fichier de paramètres de publication en suivant les instructions disponibles ici : [Importation des paramètres de publication](/en-us/develop/php/how-to-guides/powershell-cmdlets/#ImportPubSettings).

3.  Ouvrez une invite de commandes PowerShell et exécutez la commande suivante :

         New-AzureWebSite MySiteName

L'URL du site Web nouvellement créé est `http://nomdusite.azurewebsites.net`.

Notez que vous pouvez exécuter la commande `New-AzureWebSite` avec l'une des options suivantes :

-   `-location [nom de l'emplacement]`. Cette option vous permet de spécifier l’emplacement du centre de données dans lequel votre site Web est créé (par exemple, « Bretagne »). Si vous omettez cette option, vous êtes invité à choisir un emplacement.
-   `-Hostname [nom de l'hôte personnalisé]`. Cette option vous permet de spécifier un nom d’hôte personnalisé pour votre site Web.
-   `-Git`. Cette option vous permet d’utiliser git pour publier sur votre site Web en créant des référentiels git à la fois dans votre répertoire d’application local et dans le centre de données de votre site Web. Notez que si votre dossier local est déjà un référentiel git, la commande ajoute une nouvelle référence distante au référentiel existant, en pointant vers le référentiel du centre de données de votre site Web.

Pour plus d'informations sur ces options complémentaires, consultez la page [Création et gestion d'un site Web Azure](/en-us/develop/php/how-to-guides/powershell-cmdlets/#WebSite).

Étapes suivantes
----------------

Maintenant que vous avez créé un site Web PHP dans Sites Web Azure, vous pouvez gérer, configurer, surveiller, déployer et mettre à l'échelle votre site. Pour plus d’informations, consultez les liens suivants :

-   [Configuration des sites Web](/en-us/manage/services/web-sites/how-to-configure-websites/)
-   [Configuration de PHP dans Sites Web Azure](/en-us/develop/php/common-tasks/configure-php-web-site/)
-   [Gestion des sites Web](/en-us/manage/services/web-sites/how-to-manage-websites/)
-   [Surveillance de sites Web](/en-us/manage/services/web-sites/how-to-monitor-websites/)
-   [Mise à l'échelle de sites Web](/en-us/manage/services/web-sites/how-to-scale-websites/)
-   [Publication avec Git](/en-us/develop/php/common-tasks/publishing-with-git/)

Pour des didacticiels complets, rendez-vous sur la page [Didacticiels du Centre de développement PHP](/en-us/develop/php/tutorials/).

