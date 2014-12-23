<properties linkid="web-sites-python-ptvs-django-mysql" title="Django and MySQL on Azure with Python Tools 2.1 for Visual Studio" pageTitle="Django et MySQL sur Azure avec Python Tools 2.1 pour Visual Studio" description="Learn how to use the Python Tools for Visual Studio to create a Django application that stores data in a MySQL database instance and can be deployed to a web site." metaKeywords="" services="" solutions="" documentationCenter="Python" authors="huvalo" videoId="" scriptId="" manager="wpickett" editor="" />

<tags ms.service="web-sites" ms.workload="web" ms.tgt_pltfrm="na" ms.devlang="python" ms.topic="article" ms.date="10/10/2014" ms.author="huvalo" />




# Django et MySQL sur Azure avec Python Tools 2.1 pour Visual Studio 

Dans ce didacticiel, nous allons créer une application de sondage simple à l'aide de l'un des exemples de modèles de PTVS.

Nous allons voir comment utiliser un service MySQL hébergé sur Azure, comment configurer l'application pour utiliser MySQL et comment publier l'application sur un site web Azure.

Consultez le [Centre de développement Python][] pour découvrir d'autres articles abordant le développement des sites web Azure avec PTVS à l'aide des infrastructures web Bottle, Flask et Django, avec MongoDB, le stockage de tables Azure, MySQL et les services de la base de données SQL.  Bien que cet article se concentre sur Sites Web Azure, les étapes sont semblables lors du développement d'[Azure Cloud Services][].

+ [Configuration requise](#prerequisites)
+ [Création du projet](#create-the-project)
+ [Création d'une base de données MySQL](#create-a-mysql-database)
+ [Configuration du projet](#configure-the-project)
+ [Publication sur un site web Azure](#publish-to-an-azure-website)
+ [Étapes suivantes](#next-steps)

##<a name="prerequisites"></a>Configuration requise

 - Visual Studio 2012 ou 2013
 - [PTVS 2,1][]
 - [Python Tools 2.1 pour Visual Studio (exemples VSIX)][]
 - [Outils du Kit de développement logiciel (SDK) Azure pour Visual Studio 2013][] ou [Outils du Kit de développement logiciel (SDK) Azure pour Visual Studio 2012][]
 - [Python 2.7 (32 bits)][]

[WACOM.INCLUDE [create-account-and-websites-note](../includes/create-account-and-websites-note.md)]

##<a name="create-the-project"></a>Création du projet

Dans cette section, nous allons créer un projet Visual Studio à l'aide d'un exemple de modèle. Nous allons créer un environnement virtuel et installer les packages requis.  Nous allons créer une base de données locale à l'aide de sqlite.  Puis nous exécuterons l'application localement.

1.  Dans Visual Studio, sélectionnez **Fichier**, **Nouveau projet**.

1.  Les modèles de projet ayant l'extension VSIX des exemples de PTVS sont disponibles sous **Python**, **Exemples**.  Sélectionnez **Polls Django Web Project** et cliquez sur OK pour créer le projet.

  	![New Project Dialog](./media/web-sites-python-ptvs-django-mysql/PollsDjangoNewProject.png)

1.  Vous êtes invité à installer des packages externes.  Sélectionnez **Installer dans un environnement virtuel**.

  	![External Packages Dialog](./media/web-sites-python-ptvs-django-mysql/PollsDjangoExternalPackages.png)

1.  Sélectionnez **Python 2.7** comme interpréteur de base.

  	![Add Virtual Environment Dialog](./media/web-sites-python-ptvs-django-mysql/PollsCommonAddVirtualEnv.png)

1.  Cliquez sur le nœud du projet et sélectionnez **Python**,** Django Sync DB**.

  	![Django Sync DB Command](./media/web-sites-python-ptvs-django-mysql/PollsDjangoSyncDB.png)

1.  Une console de gestion Django s'ouvre.  Suivez les invites pour créer un utilisateur.

    Cela crée une base de données sqlite dans le dossier du projet.

  	![Django Management Console Window](./media/web-sites-python-ptvs-django-mysql/PollsDjangoConsole.png)

1.  Vérifiez que l'application fonctionne en appuyant sur <kbd>F5</kbd>.

1.  Dans la barre de navigation en haut, cliquez sur **Connexion**.

  	![Web Browser](./media/web-sites-python-ptvs-django-mysql/PollsDjangoCommonBrowserLocalMenu.png)

1.  Entrez les informations d'identification de l'utilisateur que vous avez créé lors de la synchronisation de la base de données.

  	![Web Browser](./media/web-sites-python-ptvs-django-mysql/PollsDjangoCommonBrowserLocalLogin.png)

1.  Cliquez sur **Créer des exemples de sondage**.

  	![Web Browser](./media/web-sites-python-ptvs-django-mysql/PollsDjangoCommonBrowserNoPolls.png)

1.  Cliquez sur un sondage et votez.

  	![Web Browser](./media/web-sites-python-ptvs-django-mysql/PollsDjangoSqliteBrowser.png)

##<a name="create-a-mysql-database"></a>Création d'une base de données MySQL

Pour la base de données, nous allons créer une base de données MySQL ClearDB hébergée sur Azure.

Vous pouvez également créer votre propre machine virtuelle s'exécutant dans Azure, puis installer et administrer MySQL vous-même.

Vous pouvez créer une version d'essai gratuite en procédant comme suit.

1.  Connectez-vous au [portail de gestion Azure][].

1.  En bas du volet de navigation, cliquez sur **NOUVEAU**.

  	![New Button](./media/web-sites-python-ptvs-django-mysql/PollsCommonAzurePlusNew.png)

1.  Cliquez sur **MAGASIN**, puis **Base de données MySQL ClearDB**.

  	![Choose Add-on Dialog](./media/web-sites-python-ptvs-django-mysql/PollsDjangoClearDBAddon1.png)

1.  Dans le champ Nom, tapez un nom à utiliser pour le service de base de données.

1.  Choisissez la région/le groupe d'affinités dans lequel localiser le service de base de données. Si vous utilisez la base de données à partir de votre application Azure, sélectionnez la même région que celle du déploiement de votre application.

  	![Personalize Add-on Dialog](./media/web-sites-python-ptvs-django-mysql/PollsDjangoClearDBAddon2.png)

1.  Cliquez sur **ACHAT**.

##<a name="configure-the-project"></a>Configuration du projet

Dans cette section, nous allons configurer notre application pour utiliser la base de données MySQL que nous venons de créer.  Nous verrons comment obtenir les paramètres de connexion à partir du portail Azure.  Nous allons également installer des packages Python supplémentaires requis pour utiliser des bases de données MySQL avec Django.  Puis nous exécuterons l'application localement.

1.  Dans le [portail de gestion Azure][], cliquez sur **MODULES COMPLÉMENTAIRES**, puis cliquez sur le service Base de données MySQL ClearDB que vous avez créé précédemment.

1.  Cliquez sur **INFORMATIONS DE CONNEXION**.  Vous pouvez utiliser le bouton Copier pour placer la valeur de **CONNECTIONSTRING** dans le Presse-papiers.

  	![Connection Info Dialog](./media/web-sites-python-ptvs-django-mysql/PollsDjangoMySQLConnectionInfo.png)

1.  Dans Visual Studio, ouvrez **settings.py**, à partir du dossier *NomProjet*.  Temporairement, collez la chaîne de connexion dans l'éditeur.  La chaîne de connexion est au format suivant :

        Database=<NAME>;Data Source=<HOST>;User Id=<USER>;Password=<PASSWORD>

    Modifiez le moteur (**ENGINE**) de base de données par défaut de façon à utiliser MySQL, et définissez des valeurs pour les arguments **NAME**, **USER**, **PASSWORD** and **HOST** à partir de **CONNECTIONSTRING**.

        DATABASES = {
            'default': {
                'ENGINE': 'django.db.backends.mysql',
                'NAME': '<Database>',
                'USER': '<User Id>',
                'PASSWORD': '<Password>',
                'HOST': '<Data Source>',
                'PORT': '',
            }
        }


1.  Dans l'Explorateur de solutions, sous **Environnements Python**, cliquez avec le bouton droit sur l'environnement virtuel et sélectionnez **Installer le package Python**.

1. Installez le package " mysql-python " à l'aide d'**easy_install**.

  	![Install Package Dialog](./media/web-sites-python-ptvs-django-mysql/PollsDjangoMySQLInstallPackage.png)

1.  Cliquez sur le nœud du projet et sélectionnez **Python**,** Django Sync DB**.  

    Cette procédure va créer les tables de la base de données MySQL que nous avons créée dans la section précédente.  Suivez les invites permettant de créer un utilisateur, qui ne doit pas correspondre à l'utilisateur dans la base de données sqlite créée dans la première section.

  	![Django Management Console Window](./media/web-sites-python-ptvs-django-mysql/PollsDjangoConsole.png)

1.  Exécutez l'application à l'aide de la touche <kbd>F5</kbd>.  Les sondages créés à l'aide de **Créer des exemples de sondage** et les données envoyées en votant seront sérialisées dans la base de données MySQL.

##<a name="publish-to-an-azure-website"></a>Publication sur un site web Azure

PTVS est un moyen facile de déployer votre application web sur un site web Azure.

1.  Dans l'**Explorateur de solutions**, cliquez avec le bouton droit sur le nœud du projet et sélectionnez **Publier**.

  	![Publish Web Dialog](./media/web-sites-python-ptvs-django-mysql/PollsCommonPublishWebSiteDialog.png)

1.  Cliquez sur **Sites Web Microsoft Azure**.

1.  Cliquez sur **Nouveau** pour créer un nouveau site.

1.  Sélectionnez un **Nom du site** et une **Région**, puis cliquez sur **Créer**.

  	![Create Site on Microsoft Azure Dialog](./media/web-sites-python-ptvs-django-mysql/PollsCommonCreateWebSite.png)

1.  Acceptez tous les autres paramètres par défaut et cliquez sur **Publier**.

1.  Votre navigateur web s'ouvre automatiquement sur le site publié.  L'application doit fonctionner comme prévu, à l'aide de la base de données hébergée **MySQL**.

    Félicitations !

  	![Web Browser](./media/web-sites-python-ptvs-django-mysql/PollsDjangoAzureBrowser.png)

##<a name="next-steps"></a>Étapes suivantes

Suivez ces liens pour en savoir plus sur Python Tools pour Visual Studio, Bottle et MySQL.

- [Documentation relative à Python Tools for Visual Studio][]
  - [Projets web][]
  - [Projets de service cloud][]
  - [Débogage distant sur Microsoft Azure][]
- [Documentation Django][]
- [MySQL][]


<!--Link references-->
[Centre de développement Python]: /fr-fr/develop/python/
[Services cloud Azure]: ../cloud-services-python-ptvs/

<!--External Link references-->
[Portail de gestion Azure]: https://manage.windowsazure.com
[PTVS 2,1]: http://go.microsoft.com/fwlink/?LinkId=517189
[Python Tools 2.1 pour Visual Studio (exemples VSIX)]: http://go.microsoft.com/fwlink/?LinkId=517189
[Outils du Kit de développement logiciel (SDK) Azure pour Visual Studio 2013]: http://go.microsoft.com/fwlink/?LinkId=323510
[Outils du Kit de développement logiciel (SDK) Azure pour Visual Studio 2012]: http://go.microsoft.com/fwlink/?LinkId=323511
[Python 2.7 (32 bits)]: http://go.microsoft.com/fwlink/?LinkId=517190 
[Documentation relative à Python Tools for Visual Studio]: http://pytools.codeplex.com/documentation
[Débogage distant sur Microsoft Azure]: http://pytools.codeplex.com/wikipage?title=Features%20Azure%20Remote%20Debugging
[Projets web]: http://pytools.codeplex.com/wikipage?title=Features%20Web%20Project
[Projets de service cloud]: http://pytools.codeplex.com/wikipage?title=Features%20Cloud%20Project
[Documentation Django]: https://www.djangoproject.com/
[MySQL]: http://www.mysql.com/
