<properties linkid="web-sites-python-ptvs-django-mysql" title="Django and MySQL on Azure with Python Tools 2.1 for Visual Studio" pageTitle="Django et MySQL sur Azure avec Python Tools 2.1 pour Visual Studio" description="Learn how to use the Python Tools for Visual Studio to create a Django application that stores data in a MySQL database instance and can be deployed to a web site." metaKeywords="" services="" solutions="" documentationCenter="Python" authors="huvalo" videoId="" scriptId="" manager="wpickett" editor="" />

<tags ms.service="web-sites" ms.workload="web" ms.tgt_pltfrm="na" ms.devlang="python" ms.topic="article" ms.date="10/10/2014" ms.author="huvalo" />




# Django et MySQL sur Azure avec Python Tools 2.1 pour Visual Studio 

Dans ce didacticiel, nous allons créer une application de sondage simple à l'aide de l'un des exemples de modèles PTVS. Ce didacticiel est également disponible sous forme de [vidéo](https://www.youtube.com/watch?v=oKCApIrS0Lo).

Nous allons découvrir comment utiliser un service MySQL hébergé sur Azure, comment configurer l'application pour utiliser mySQL et comment publier l'application sur un site web Azure.

Visitez le [Centre de développement Python][] pour consulter d'autres articles sur le développement d'Azure Web Sites avec PTVS à l'aide des infrastructures web Bottle, Flask et Django, ainsi que de MongoDB, du stockage de tables Azure, de MySQL et des services de base de données SQL.  Quand bien même cet article se concentre Azure Web Sites, les étapes sont similaires pour développer [Azure Cloud Services][].

+ [Conditions préalables](#prerequisites)
+ [Créer le projet](#create-the-project)
+ [Créer une base de données MySQL](#create-a-mysql-database)
+ [Configurer le projet](#configure-the-project)
+ [Publier sur un site web Azure](#publish-to-an-azure-website)
+ [Étapes suivantes](#next-steps)

##<a name="prerequisites"></a>Conditions préalables

 - Visual Studio 2012 ou 2013
 - [Python Tools 2.1 pour Visual Studio][]
 - [Exemples VSIX de Python Tools 2.1 pour Visual Studio][]
 - [Outils SDK Azure pour VS 2013][] ou [Outils SDK Azure pour VS 2012][]
 - [Python 2.7 32 bits][]

> [WACOM.NOTE]
> Pour effectuer ce didacticiel, vous avez besoin d'un compte Azure. Vous pouvez <a href="http://azure.microsoft.com/fr-fr/pricing/member-offers/msdn-benefits-details/">activer les avantages de votre abonnement MSDN</a> ou <a href="http://azure.microsoft.com/fr-fr/pricing/free-trial/">vous inscrire pour obtenir une version d'évaluation gratuite</a>.
> 
> Pour prendre en main Sites web Azure avant de vous inscrire pour obtenir un compte, accédez à <a href="https://trywebsites.azurewebsites.net/?language=python">https://trywebsites.azurewebsites.net</a>, où la possibilité vous est donnée de créer tout de suite un site de démarrage ASP.NET à durée de vie limitée gratuit dans Sites web Azure. Aucun numéro de carte de crédit, ni engagement de quelque sorte ne sont exigés.

##<a name="create-the-project"></a>Création du projet

Dans cette section, nous allons créer un projet Visual Studio à l'aide d'un exemple de modèle. Nous allons créer un environnement virtuel et installer les packages requis.  Nous allons créer une base de données locale à l'aide de sqlite.  Ensuite, nous allons exécuter l'application localement.

1.  Dans Visual Studio, sélectionnez **Fichier**, **Nouveau projet**.

1.  Les modèles de projet issus des exemples VSIX de PTVS sont disponibles sous **Python**, **Exemples**.  Sélectionnez **Projet web de sondage Django** et cliquez sur OK pour créer le projet.

  	![New Project Dialog](./media/web-sites-python-ptvs-django-mysql/PollsDjangoNewProject.png)

1.  Vous allez être invité à installer des packages externes.  Sélectionnez **Installer dans un environnement virtuel**.

  	![External Packages Dialog](./media/web-sites-python-ptvs-django-mysql/PollsDjangoExternalPackages.png)

1.  Sélectionnez **Python 2.7** comme interpréteur de base.

  	![Add Virtual Environment Dialog](./media/web-sites-python-ptvs-django-mysql/PollsCommonAddVirtualEnv.png)

1.  Cliquez avec le bouton droit sur le nœud du projet et sélectionnez **Python**, **BD de synchronisation Django**.

  	![Django Sync DB Command](./media/web-sites-python-ptvs-django-mysql/PollsDjangoSyncDB.png)

1.  Une console de gestion Django s'ouvre.  Suivez les invites pour créer un utilisateur.

    Une base de données sqlite est créée dans le dossier du projet.

  	![Django Management Console Window](./media/web-sites-python-ptvs-django-mysql/PollsDjangoConsole.png)

1.  Vérifiez que l'application fonctionne en appuyant sur <kbd>F5</kbd>.

1.  Cliquez sur **Connexion** dans la barre de navigation en haut.

  	![Web Browser](./media/web-sites-python-ptvs-django-mysql/PollsDjangoCommonBrowserLocalMenu.png)

1.  Entrez les informations d'identification de l'utilisateur que vous avez créé quand vous avez synchronisé la base de données.

  	![Web Browser](./media/web-sites-python-ptvs-django-mysql/PollsDjangoCommonBrowserLocalLogin.png)

1.  Cliquez sur **Créer un exemple de sondage**.

  	![Web Browser](./media/web-sites-python-ptvs-django-mysql/PollsDjangoCommonBrowserNoPolls.png)

1.  Cliquez sur un sondage et vote.

  	![Web Browser](./media/web-sites-python-ptvs-django-mysql/PollsDjangoSqliteBrowser.png)

##<a name="create-a-mysql-database"></a>Créer une base de données MySQL

Pour la base de données, nous allons créer une base de données hébergée ClearDB MySQL sur Azure.

Vous pouvez également créer votre propre machine virtuelle exécutée sur Azure, puis installer et administrer MySQL vous-même.

Pour créer une base de données avec un plan gratuit, procédez comme suit :

1.  Connectez-vous au [portail de gestion Azure][].

1.  Au bas du volet de navigation, cliquez sur **NOUVEAU**.

  	![New Button](./media/web-sites-python-ptvs-django-mysql/PollsCommonAzurePlusNew.png)

1.  Cliquez sur **STORE**, puis sur **Base de données ClearDB MySQL**.

  	![Choose Add-on Dialog](./media/web-sites-python-ptvs-django-mysql/PollsDjangoClearDBAddon1.png)

1.  Dans Nom, entrez le nom à utiliser pour le service de base de données.

1.  Choisissez la région ou le groupe d'affinités où localiser le service de base de données. Si vous utilisez la base de données de votre application Azure, sélectionnez la même région que celle du déploiement de votre application.

  	![Personalize Add-on Dialog](./media/web-sites-python-ptvs-django-mysql/PollsDjangoClearDBAddon2.png)

1.  Cliquez sur **ACHETER**.

##<a name="configure-the-project"></a>Configurer le projet

Dans cette section, nous allons configurer notre application pour utiliser la base de données MySQL que nous venons de créer.  Nous allons découvrir comment obtenir des paramètres de connexion à partir du portail Azure.  Nous allons également installer les packages Python supplémentaires requis pour utiliser des bases de données MySQL avec Django.  Ensuite, nous allons exécuter l'application localement.

1.  Dans le [portail de gestion Azure][], cliquez sur **MODULES COMPLÉMENTAIRES**, puis cliquez sur le service de base de données ClearDB MySQL que vous avez créé précédemment.

1.  Cliquez sur **INFORMATIONS DE CONNEXION**.  Vous pouvez utiliser le bouton de copie pour placer la valeur de **CONNECTIONSTRING** dans le Presse-papiers.

  	![Connection Info Dialog](./media/web-sites-python-ptvs-django-mysql/PollsDjangoMySQLConnectionInfo.png)

1.  Dans Visual Studio, ouvrez **settings.py**, à partir du dossier *ProjectName*.  Collez temporairement la chaîne de connexion dans l'éditeur.  Celle-ci se présente au format suivant :

        Database=<NAME>;Data Source=<HOST>;User Id=<USER>;Password=<PASSWORD>

    Modifiez le **MOTEUR** de base de données par défaut pour utiliser MySQL, puis définissez les valeurs **NOM**, **UTILISATEUR**, **MOT DE PASSE** et **HÔTE** à partir de **CONNECTIONSTRING**.

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

1. Installez le package `mysql-python` en utilisant **easy_install**.

  	![Install Package Dialog](./media/web-sites-python-ptvs-django-mysql/PollsDjangoMySQLInstallPackage.png)

1.  Cliquez avec le bouton droit sur le nœud du projet et sélectionnez **Python**, **BD de synchronisation Django**.  

    Les tables de la base de données MySQL que nous avons créée dans la section précédente sont alors elles-aussi créées.  Suivez les invites pour créer un utilisateur, qui ne doit pas forcément correspondre à l'utilisateur inclus dans la base de données sqlite que nous avons créée dans la première section.

  	![Django Management Console Window](./media/web-sites-python-ptvs-django-mysql/PollsDjangoConsole.png)

1.  Exécutez l'application avec <kbd>F5</kbd>.  Les sondages créés avec **Créer un exemple de sondage** et les données soumises par vote sont sérialisés dans la base de données MySQL.

##<a name="publish-to-an-azure-website"></a>Publier sur un site web Azure

PTVS propose un moyen simple de déployer votre application web sur un site web Azure.

1.  Dans l'**Explorateur de solutions**, cliquez avec le bouton droit sur le nœud du projet, puis sélectionnez **Publier**.

  	![Publish Web Dialog](./media/web-sites-python-ptvs-django-mysql/PollsCommonPublishWebSiteDialog.png)

1.  Cliquez sur **Sites web Microsoft Azure**.

1.  Cliquez sur **Nouveau** pour créer un site.

1.  Sélectionnez un **nom de site** et une **région** et cliquez sur **Créer**.

  	![Create Site on Microsoft Azure Dialog](./media/web-sites-python-ptvs-django-mysql/PollsCommonCreateWebSite.png)

1.  Acceptez toutes les valeurs par défaut et cliquez sur **Publier**.

1.  Votre navigateur web ouvre automatiquement le site publié.  L'application doit fonctionner comme prévu et utiliser la base de données **MySQL** hébergée sur Azure.

    Félicitations !

  	![Web Browser](./media/web-sites-python-ptvs-django-mysql/PollsDjangoAzureBrowser.png)

##<a name="next-steps"></a>Étapes suivantes

Suivez ces liens pour en savoir plus sur Python Tools pour Visual Studio, Django et MySQL.

- [Documentation de Python Tools pour Visual Studio][]
  - [Projets web][]
  - [Projets de service cloud][]
  - [Débogage à distance sur Microsoft Azure][]
- [Documentation de Django][]
- [MySQL][]


<!--Link references-->
[Centre de développement Python]: /fr-fr/develop/python/
[Azure Cloud Services]: ../cloud-services-python-ptvs/

<!--External Link references-->
[Portail de gestion Azure]: https://manage.windowsazure.com
[Python Tools 2.1 pour Visual Studio]: http://go.microsoft.com/fwlink/?LinkId=517189
[Exemples VSIX de Python Tools 2.1 pour Visual Studio]: http://go.microsoft.com/fwlink/?LinkId=517189
[Outils SDK Azure SDK pour VS 2013]: http://go.microsoft.com/fwlink/?LinkId=323510
[Outils SDK Azure SDK pour VS 2012]: http://go.microsoft.com/fwlink/?LinkId=323511
[Python 2.7 32 bits]: http://go.microsoft.com/fwlink/?LinkId=517190 
[Documentation de Python Tools pour Visual Studio]: http://pytools.codeplex.com/documentation
[Débogage à distance sur Microsoft Azure]: http://pytools.codeplex.com/wikipage?title=Features%20Azure%20Remote%20Debugging
[Projets web]: http://pytools.codeplex.com/wikipage?title=Features%20Web%20Project
[Projets de service cloud]: http://pytools.codeplex.com/wikipage?title=Features%20Cloud%20Project
[Documentation de Django]: https://www.djangoproject.com/
[MySQL]: http://www.mysql.com/

<!--HONumber=35_1-->
