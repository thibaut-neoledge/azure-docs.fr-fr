<properties linkid="web-sites-python-ptvs-django-sql" title="Django and SQL Database on Azure with Python Tools 2.1 for Visual Studio" pageTitle="Django et base de données SQL sur Azure avec Python Tools 2.1 pour Visual Studio" description="Learn how to use the Python Tools for Visual Studio to create a Django application that stores data in a SQL database instance and can be deployed to a web site." metaKeywords="" services="" solutions="" documentationCenter="Python" authors="huvalo" videoId="" scriptId="" manager="wpickett" editor="" />

<tags ms.service="web-sites" ms.workload="web" ms.tgt_pltfrm="na" ms.devlang="python" ms.topic="article" ms.date="10/10/2014" ms.author="huvalo" />




# Django et base de données SQL sur Azure avec Python Tools 2.1 pour Visual Studio 

Dans ce didacticiel, nous allons créer une application de sondage simple à l'aide de l'un des exemples de modèles de PTVS.

Nous allons voir comment utiliser une base de données SQL hébergée sur Azure, comment configurer l'application pour utiliser une base de données SQL et comment publier l'application sur un site web Azure.

Consultez le [Centre de développement Python][] pour découvrir d'autres articles abordant le développement des sites web Azure avec PTVS à l'aide des infrastructures web Bottle, Flask et Django, avec MongoDB, le stockage de tables Azure, MySQL et les services de la base de données SQL.  Bien que cet article se concentre sur Sites Web Azure, les étapes sont semblables lors du développement d'[Azure Cloud Services][].

+ [Configuration requise](#prerequisites)
+ [Création du projet](#create-the-project)
+ [Création d'une base de données SQL](#create-a-sql-database)
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

  	![New Project Dialog](./media/web-sites-python-ptvs-django-sql/PollsDjangoNewProject.png)

1.  Vous êtes invité à installer des packages externes.  Sélectionnez **Installer dans un environnement virtuel**.

  	![External Packages Dialog](./media/web-sites-python-ptvs-django-sql/PollsDjangoExternalPackages.png)

1.  Sélectionnez **Python 2.7** comme interpréteur de base.

  	![Add Virtual Environment Dialog](./media/web-sites-python-ptvs-django-sql/PollsCommonAddVirtualEnv.png)

1.  Cliquez sur le nœud du projet et sélectionnez **Python**,** Django Sync DB**.

  	![Django Sync DB Command](./media/web-sites-python-ptvs-django-sql/PollsDjangoSyncDB.png)

1.  Une console de gestion Django s'ouvre.  Suivez les invites pour créer un utilisateur.

    Cela crée une base de données sqlite dans le dossier du projet.

  	![Django Management Console Window](./media/web-sites-python-ptvs-django-sql/PollsDjangoConsole.png)

1.  Vérifiez que l'application fonctionne en appuyant sur <kbd>F5</kbd>.

1.  Dans la barre de navigation en haut, cliquez sur **Connexion**.

  	![Web Browser](./media/web-sites-python-ptvs-django-sql/PollsDjangoCommonBrowserLocalMenu.png)

1.  Entrez les informations d'identification de l'utilisateur que vous avez créé lors de la synchronisation de la base de données.

  	![Web Browser](./media/web-sites-python-ptvs-django-sql/PollsDjangoCommonBrowserLocalLogin.png)

1.  Cliquez sur **Créer des exemples de sondage**.

  	![Web Browser](./media/web-sites-python-ptvs-django-sql/PollsDjangoCommonBrowserNoPolls.png)

1.  Cliquez sur un sondage et votez.

  	![Web Browser](./media/web-sites-python-ptvs-django-sql/PollsDjangoSqliteBrowser.png)

##<a name="create-a-sql-database"></a>Création d'une base de données SQL

Pour la base de données, nous allons créer une base de données SQL Azure.

Pour en créer une, procédez comme suit :

1.  Connectez-vous au [portail de gestion Azure][].

1.  En bas du volet de navigation, cliquez sur **NOUVEAU**.

  	![New Button](./media/web-sites-python-ptvs-django-sql/PollsCommonAzurePlusNew.png)

1.  Cliquez sur **SERVICES DE DONNÉES**, sur **BASE DE DONNÉES**, puis sur **APERÇU RAPIDE**.

  	![Quick Create SQL Database](./media/web-sites-python-ptvs-django-sql/PollsDjangoSqlCreate.png)

1.  Choisissez l'option permettant de créer un serveur de base de données SQL.

1.  Choisissez la région ou le groupe d'affinités dans lequel localiser la base de données. Si vous utilisez la base de données à partir de votre application Azure, sélectionnez la même région que celle du déploiement de votre application.

##<a name="configure-the-project"></a>Configuration du projet

Dans cette section, nous allons configurer notre application pour utiliser la base de données SQL que nous venons de créer.  Nous verrons comment obtenir les paramètres de connexion à partir du portail Azure.  Nous allons également installer des packages Python supplémentaires requis pour utiliser des bases de données SQL avec Django.  Puis nous exécuterons l'application localement.

1.  Dans le [portail de gestion Azure][], cliquez sur **BASES DE DONNÉES SQL**, puis cliquez sur la base de données que vous avez créée précédemment.

1.  Cliquez sur **GÉRER**.

  	![Manage Button](./media/web-sites-python-ptvs-django-sql/PollsDjangoSqlManage.png)

1.  Vous allez être invité à mettre à jour les règles de pare-feu. Cliquez sur **OUI**.  Cela permet les connexions au serveur de base de données à partir de votre ordinateur de développement.

  	![Allow Connections](./media/web-sites-python-ptvs-django-sql/PollsDjangoSqlUpdateFirewall.png)

1.  Cliquez sur **BASES DE DONNÉES SQL**, puis **SERVEURS**.  Cliquez sur le serveur de votre base de données, puis sur **CONFIGURER**.

1.  Dans cette page, vous allez voir l'adresse IP de chaque ordinateur autorisé à se connecter au serveur de base de données.  Vous devez voir l'adresse IP de votre ordinateur.

    Ci-dessous, sous **services autorisés**, assurez-vous que les services Azure sont autorisés à accéder au serveur.  Lorsque l'application s'exécute dans un site Web Azure (ce que nous allons faire dans la section suivante de ce didacticiel), il sera autorisé à se connecter à la base de données.  Cliquez sur **ENREGISTRER** pour appliquer la modification.

  	![Allowed Services](./media/web-sites-python-ptvs-django-sql/PollsDjangoSqlAllowedServices.png)

1.  Dans Visual Studio, ouvrez **settings.py**, à partir du dossier *NomProjet*. Modifiez la définition de " DATABASES ".

        DATABASES = {
            'default': {
                'ENGINE': 'sql_server.pyodbc',
                'NAME': '<DatabaseName>',
                'USER': '<User>@<ServerName>',
                'PASSWORD': '<Password>',
                'HOST': '<ServerName>.database.windows.net',
                'PORT': '<ServerPort>',
                'OPTIONS': {
                    'driver': 'SQL Server Native Client 11.0',
                    'MARS_Connection': 'True',
                }
            }
        }

    " <Nom_base_données > ", " <Utilisateur> " et " <Mot de passe> " sont les valeurs que vous avez spécifiées lorsque vous avez créé la base de données et le serveur.

    Les valeurs pour " <Nom_serveur> " et " <PortServeur> " sont générées par Azure lorsque le serveur est créé. Elles se trouvent sous la section **Connexion à votre base de données**.

1.  Dans l'Explorateur de solutions, sous **Environnements Python**, cliquez avec le bouton droit sur l'environnement virtuel et sélectionnez **Installer le package Python**.

1.  Installez le package " pyodbc " à l'aide d'**easy_install**.

  	![Install Python Package Dialog](./media/web-sites-python-ptvs-django-sql/PollsDjangoSqlInstallPackagePyodbc.png)

1.  Installez le package " django-pyodbc-azure " à l'aide de **pip**.

  	![Install Python Package Dialog](./media/web-sites-python-ptvs-django-sql/PollsDjangoSqlInstallPackageDjangoPyodbcAzure.png)

1.  Cliquez sur le nœud du projet et sélectionnez **Python**,** Django Sync DB**.  

    Cette procédure va créer les tables de la base de données SQL que nous avons créée dans la section précédente.  Suivez les invites permettant de créer un utilisateur, qui ne doit pas correspondre à l'utilisateur dans la base de données sqlite créée dans la première section.

  	![Django Management Console Window](./media/web-sites-python-ptvs-django-sql/PollsDjangoConsole.png)

1.  Exécutez l'application à l'aide de la touche <kbd>F5</kbd>.  Les sondages créés à l'aide de **Créer des exemples de sondage** et les données envoyées en votant seront sérialisées dans la base de données SQL.


##<a name="publish-to-an-azure-website"></a>Publication sur un site web Azure

PTVS est un moyen facile de déployer votre application web sur un site web Azure.

1.  Dans l'**Explorateur de solutions**, cliquez avec le bouton droit sur le nœud du projet et sélectionnez **Publier**.

  	![Publish Web Dialog](./media/web-sites-python-ptvs-django-sql/PollsCommonPublishWebSiteDialog.png)

1.  Cliquez sur **Sites Web Microsoft Azure**.

1.  Cliquez sur **Nouveau** pour créer un nouveau site.

1.  Sélectionnez un **Nom du site** et une **Région**, puis cliquez sur **Créer**.

  	![Create Site on Microsoft Azure Dialog](./media/web-sites-python-ptvs-django-sql/PollsCommonCreateWebSite.png)

1.  Acceptez tous les autres paramètres par défaut et cliquez sur **Publier**.

1.  Votre navigateur web s'ouvre automatiquement sur le site publié.  L'application doit fonctionner comme prévu, à l'aide de la base de données hébergée **SQL**.

    Félicitations !

  	![Web Browser](./media/web-sites-python-ptvs-django-sql/PollsDjangoAzureBrowser.png)

##<a name="next-steps"></a>Étapes suivantes

Suivez ces liens pour en savoir plus sur Python Tools pour Visual Studio, Bottle et base de données SQL.

- [Documentation relative à Python Tools for Visual Studio][]
  - [Projets web][]
  - [Projets de service cloud][]
  - [Débogage distant sur Microsoft Azure][]
- [Documentation Django][]
- [Base de données SQL][]


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
[Base de données SQL]: /fr-fr/documentation/services/sql-database/
