<properties 
	pageTitle="Django et Base de données SQL sur Azure avec Python Tools 2.1 pour Visual Studio" 
	description="Découvrez comment utiliser Python Tools pour Visual Studio afin de créer une application Django qui stocke les données dans une instance de base de données SQL et peut être déployée vers un site web." 
	services="app-service\web" 
	tags="python"
	documentationCenter="python" 
	authors="huguesv" 
	manager="wpickett" 
	editor=""/>

<tags 
	ms.service="app-service-web" 
	ms.workload="web" 
	ms.tgt_pltfrm="na" 
	ms.devlang="python" 
	ms.topic="article" 
	ms.date="02/09/2015" 
	ms.author="huguesv"/>




# Django et Base de données SQL sur Azure avec Python Tools 2.1 pour Visual Studio 

Dans ce didacticiel, nous allons utiliser [Python Tools pour Visual Studio][] afin de créer une application de sondage simple à l'aide de l'un des exemples de modèles PTVS. Ce didacticiel est également disponible sous forme de [vidéo](https://www.youtube.com/watch?v=ZwcoGcIeHF4).

Nous allons découvrir comment utiliser une base de données SQL hébergée sur Azure, comment configurer l'application pour utiliser une base de données SQL et comment publier l'application sur un site web Azure.

Visitez le [Centre de développement Python][] pour consulter d'autres articles sur le développement de site web Azure avec PTVS à l'aide des infrastructures web Bottle, Flask et Django, ainsi que de MongoDB, du stockage de tables Azure, de MySQL et des services de base de données SQL.  Quand bien même cet article se concentre Sites Web Azure, les étapes sont similaires pour développer [Azure Cloud Services][].

## Conditions préalables

 - Visual Studio 2012 ou 2013
 - [Python Tools 2.1 pour Visual Studio (en anglais)][]
 - [Exemples VSIX de Python Tools 2.1 pour Visual Studio][]
 - [Outils du Kit de développement logiciel (SDK) Azure pour Visual Studio 2013][] ou [Outils du Kit de développement logiciel (SDK) Azure pour Visual Studio 2012][]
 - [Python 2.7 32 bits][]

[AZURE.INCLUDE [create-account-and-websites-note](../includes/create-account-and-websites-note.md)]

## Création du projet

Dans cette section, nous allons créer un projet Visual Studio à l'aide d'un exemple de modèle. Nous allons créer un environnement virtuel et installer les packages requis.  Nous allons créer une base de données locale à l'aide de sqlite.  Ensuite, nous allons exécuter l'application localement.

1.  Dans Visual Studio, sélectionnez **Fichier**, **Nouveau projet**.

1.  Les modèles de projet issus des exemples VSIX de PTVS sont disponibles sous **Python**, **Exemples**.  Sélectionnez **Projet web de sondage Django** et cliquez sur OK pour créer le projet.

  	![Boîte de dialogue Nouveau projet](./media/web-sites-python-ptvs-django-sql/PollsDjangoNewProject.png)

1.  Vous allez être invité à installer des packages externes.  Sélectionnez **Installer dans un environnement virtuel**.

  	![Boîte de dialogue Packages externes](./media/web-sites-python-ptvs-django-sql/PollsDjangoExternalPackages.png)

1.  Sélectionnez **Python 2.7** comme interpréteur de base.

  	![Boîte de dialogue Ajouter un environnement virtuel](./media/web-sites-python-ptvs-django-sql/PollsCommonAddVirtualEnv.png)

1.  Cliquez avec le bouton droit de la souris sur le nœud du projet et sélectionnez **Python**, **Django Sync DB**.

  	![Commande Base de données de synchronisation Django](./media/web-sites-python-ptvs-django-sql/PollsDjangoSyncDB.png)

1.  Une console de gestion Django s'ouvre.  Suivez les invites pour créer un utilisateur.

    Une base de données sqlite est créée dans le dossier du projet.

  	![Fenêtre Console de gestion Django](./media/web-sites-python-ptvs-django-sql/PollsDjangoConsole.png)

1.  Vérifiez que l'application fonctionne en appuyant sur <kbd>F5</kbd>.

1.  Cliquez sur **Connexion** dans la barre de navigation en haut.

  	![Navigateur web](./media/web-sites-python-ptvs-django-sql/PollsDjangoCommonBrowserLocalMenu.png)

1.  Saisissez les informations d'identification de l'utilisateur que vous avez créé quand vous avez synchronisé la base de données.

  	![Navigateur web](./media/web-sites-python-ptvs-django-sql/PollsDjangoCommonBrowserLocalLogin.png)

1.  Cliquez sur **Créer un exemple de sondage**.

  	![Navigateur web](./media/web-sites-python-ptvs-django-sql/PollsDjangoCommonBrowserNoPolls.png)

1.  Cliquez sur un sondage et vote.

  	![Navigateur web](./media/web-sites-python-ptvs-django-sql/PollsDjangoSqliteBrowser.png)

## Création d'une base de données SQL

Pour la base de données, nous allons créer une base de données SQL Azure.

Pour cela, procédez comme suit :

1.  Connectez-vous au [portail de gestion Azure][].

1.  En bas du volet de navigation, cliquez sur **NOUVEAU**.

  	![Bouton Nouveau](./media/web-sites-python-ptvs-django-sql/PollsCommonAzurePlusNew.png)

1.  Cliquez sur **SERVICES DE DONNÉES**, **BASE DE DONNÉES SQL**, puis **CRÉATION RAPIDE**.

  	![Création rapide d'une base de données SQL](./media/web-sites-python-ptvs-django-sql/PollsDjangoSqlCreate.png)

1.  Choisissez de créer un serveur de base de données SQL.

1.  Choisissez la région ou le groupe d'affinités où localiser la base de données. Si vous utilisez la base de données de votre application Azure, sélectionnez la même région que celle du déploiement de votre application.

## Configurer le projet

Dans cette section, nous allons configurer notre application pour utiliser la base de données SQL 
que nous venons de créer.  Nous allons découvrir comment obtenir des paramètres de connexion à partir du portail 
Azure.  Nous allons également installer les packages Python supplémentaires requis pour utiliser des bases de données 
SQL avec Django.  Ensuite, nous allons exécuter l'application localement.

1.  Sur le [portail de gestion Azure][], cliquez sur **BASES DE DONNÉES SQL**, puis cliquez sur la base de données que vous avez créée précédemment.

1.  Cliquez sur **GÉRER**.

  	![Bouton Gérer](./media/web-sites-python-ptvs-django-sql/PollsDjangoSqlManage.png)

1.  Vous allez être invité à mettre à jour les règles de pare-feu. Cliquez sur **OUI**.  Les connexions au serveur de base de données de votre ordinateur de développement sont alors autorisées.

  	![Autoriser les connexions](./media/web-sites-python-ptvs-django-sql/PollsDjangoSqlUpdateFirewall.png)

1.  Cliquez sur **BASES DE DONNÉES SQL**, puis **SERVEURS**.  Cliquez sur le serveur de votre base de données, puis sur **CONFIGURER**.

1.  Dans cette page figure l'adresse IP de chaque ordinateur autorisé à se connecter au serveur de base de données.  Celle de votre ordinateur doit y figurer.

    Sous les **services autorisés**, vérifiez que les services Azure sont autorisés à accéder au serveur.  Quand l'application s'exécute sur un site web Azure (dont nous allons nous occuper dans la section suivante de ce didacticiel), elle est autorisée à se connecter à la base de données.  Cliquez sur **ENREGISTRER** pour appliquer la modification.

  	![Services autorisés](./media/web-sites-python-ptvs-django-sql/PollsDjangoSqlAllowedServices.png)

1.  Dans Visual Studio, ouvrez **settings.py**, à partir du dossier  *ProjectName*. Modifiez la définition de  `DATABASES`.

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

    `<DatabaseName>`, `<User>` et `<Password>` correspondent aux valeurs que vous avez spécifiées lorsque vous avez créé la base de données et le serveur.

    Les valeurs `<ServerName>` et `<ServerPort>` sont générées par Azure quand le serveur est créé et figurent dans la section **Se connecter à votre base de données**.

1.  Dans l'Explorateur de solutions, sous **Environnements Python**, cliquez avec le bouton droit sur l'environnement virtuel et sélectionnez **Installer le package Python**.

1.  Installez le package  `pyodbc` en utilisant **easy_install**.

  	![Boîte de dialogue Installer le package Python](./media/web-sites-python-ptvs-django-sql/PollsDjangoSqlInstallPackagePyodbc.png)

1.  Installez le package  `django-pyodbc-azure` en utilisant **easy_install**.

  	![Boîte de dialogue Installer le package Python](./media/web-sites-python-ptvs-django-sql/PollsDjangoSqlInstallPackageDjangoPyodbcAzure.png)

1.  Cliquez avec le bouton droit de la souris sur le nœud du projet et sélectionnez **Python**, **Django Sync DB**.  

    Les tables de la base de données SQL que nous avons créée dans la section précédente sont alors elles-aussi créées.  Suivez les invites pour créer un utilisateur, qui ne doit pas forcément correspondre à l'utilisateur inclus dans la base de données sqlite que nous avons créée dans la première section.

  	![Fenêtre Console de gestion Django](./media/web-sites-python-ptvs-django-sql/PollsDjangoConsole.png)

1.  Exécutez l'application avec <kbd>F5</kbd>.  Les sondages créés à l'aide de la fonction **Créer un exemple de sondage** et les données soumises par vote sont sérialisés dans la base de données SQL.


## Publier sur un site web Azure

PTVS propose un moyen simple de déployer votre application web sur un site web Azure.

1.  Dans l'**Explorateur de solutions**, cliquez avec le bouton droit sur le nœud du projet et sélectionnez **Publier**.

  	![Boîte de dialogue Publier le site Web](./media/web-sites-python-ptvs-django-sql/PollsCommonPublishWebSiteDialog.png)

1.  Cliquez sur **Sites Web Microsoft Azure**.

1.  Cliquez sur **Nouveau** pour créer un site.

1.  Sélectionnez un **nom de site** et une **région**, puis cliquez sur **Créer**.

  	![Boîte de dialogue Créer un site sur Microsoft Azure](./media/web-sites-python-ptvs-django-sql/PollsCommonCreateWebSite.png)

1.  Acceptez toutes les valeurs par défaut et cliquez sur **Publier**.

1.  Votre navigateur web ouvre automatiquement le site publié.  L'application doit fonctionner comme prévu et utiliser la base de données **SQL** hébergée sur Azure.

    Félicitations !

  	![Navigateur web](./media/web-sites-python-ptvs-django-sql/PollsDjangoAzureBrowser.png)

## Étapes suivantes

Suivez ces liens pour en savoir plus sur Python Tools pour Visual Studio, Django et la base de données SQL.

- [Documentation de Python Tools pour Visual Studio (en anglais)][]
  - [Projets web][]
  - [Projets de Service cloud][]
  - [Débogage distant sur Microsoft Azure][]
- [Documentation de Django (en anglais)][]
- [Base de données SQL][]


<!--Link references-->
[Centre de développement Python]: /develop/python/
[Azure Cloud Services]: cloud-services-python-ptvs.md

<!--External Link references-->
[Portail de gestion Azure]: https://manage.windowsazure.com
[Python Tools pour Visual Studio]: http://aka.ms/ptvs
[Python Tools 2.1 pour Visual Studio (en anglais)]: http://go.microsoft.com/fwlink/?LinkId=517189
[Exemples VSIX de Python Tools 2.1 pour Visual Studio]: http://go.microsoft.com/fwlink/?LinkId=517189
[Outils du Kit de développement logiciel (SDK) Azure pour Visual Studio 2013]: http://go.microsoft.com/fwlink/?LinkId=323510
[Outils du Kit de développement logiciel (SDK) Azure pour Visual Studio 2012]: http://go.microsoft.com/fwlink/?LinkId=323511
[Python 2.7 32 bits]: http://go.microsoft.com/fwlink/?LinkId=517190 
[Documentation de Python Tools pour Visual Studio (en anglais)]: http://pytools.codeplex.com/documentation
[Débogage distant sur Microsoft Azure]: http://pytools.codeplex.com/wikipage?title=Features%20Azure%20Remote%20Debugging
[Projets web]: http://pytools.codeplex.com/wikipage?title=Features%20Web%20Project
[Projets de Service cloud]: http://pytools.codeplex.com/wikipage?title=Features%20Cloud%20Project
[Documentation de Django (en anglais)]: https://www.djangoproject.com/
[Base de données SQL]: /documentation/services/sql-database/


<!--HONumber=52-->