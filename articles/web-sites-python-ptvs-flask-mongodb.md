<properties 
	pageTitle="Flask et MongoDB sur Azure avec Python Tools 2.1 pour Visual Studio" 
	description="Découvrez comment utiliser les Python Tools pour Visual Studio pour créer une application Flask qui stocke les données dans une instance de base de données MongoDB et peut être déployée vers un site Web." 
	services="" 
	documentationCenter="python" 
	authors="huguesv" 
	manager="wpickett" 
	editor=""/>

<tags 
	ms.service="web-sites" 
	ms.workload="web" 
	ms.tgt_pltfrm="na" 
	ms.devlang="python" 
	ms.topic="article" 
	ms.date="10/10/2014" 
	ms.author="huvalo"/>




# Flask et MongoDB sur Azure avec Python Tools 2.1 pour Visual Studio

Dans ce didacticiel, nous allons créer une application de sondage simple à l'aide de l'un des exemples de modèles PTVS. Ce didacticiel est également disponible sous forme de [vidéo](https://www.youtube.com/watch?v=eql-crFgrAE).

L'application de sondage définit une abstraction pour son référentiel, ce qui vous permet de basculer facilement d'un type de référentiel à un autre (In-Memory, le stockage de tables Azure, MongoDB).

Nous allons découvrir comment utiliser l'un des services MongoDB hébergés sur Azure, comment configurer l'application pour utiliser MongoDB et comment publier l'application sur un site Web Azure.

Visitez le [Centre de développement Python][] pour consulter d'autres articles sur le développement de Sites Web Azure avec PTVS à l'aide des infrastructures Web Bottle, Flask et Django, ainsi que de MongoDB, du stockage de tables Azure, de MySQL et des services de base de données SQL.  Quand bien même cet article se concentre sur Sites Web Azure, les étapes sont similaires pour développer [Azure Cloud Services][].

+ [Configuration requise](#prerequisites)
+ [Création du projet](#create-the-project)
+ [Création d'une base de données MongoDB](#create-a-mongodb-database)
+ [Configuration du projet](#configure-the-project)
+ [Exploration de la base de données MongoDB](#explore-the-mongodb-database)
+ [Publication sur un site Web Azure](#publish-to-an-azure-website)
+ [Configuration du site Web Azure](#configure-the-azure-website)
+ [Étapes suivantes](#next-steps)

##<a name="prerequisites"></a>Configuration requise

 - Visual Studio 2012 ou 2013
 - [Python Tools 2.1 pour Visual Studio][]
 - [Exemples VSIX de Python Tools 2.1 pour Visual Studio][]
 - [Outils SDK Azure pour VS 2013][] ou [Outils SDK Azure pour VS 2012][]
 - [Python 2.7 32 bits][] ou [Python 3.4 32 bits][]
 - [RoboMongo][] (facultatif)

[AZURE.INCLUDE [create-account-and-websites-note](../includes/create-account-and-websites-note.md)]

##<a name="create-the-project"></a>Création du projet

Dans cette section, nous créerons un projet Visual Studio à l'aide d'un exemple de modèle.  Nous créerons un environnement virtuel et nous installerons les packages requis.  Ensuite, nous exécuterons l'application localement à l'aide du référentiel In-Memory par défaut.

1.  Dans Visual Studio, sélectionnez **Fichier**, **Nouveau projet**. 

1.  Les modèles de projet issus des exemples VSIX de PTVS sont disponibles sous **Python**, **Exemples**.  Sélectionnez **Projet Web de sondage Flask** et cliquez sur OK pour créer le projet.

  	![New Project Dialog](./media/web-sites-python-ptvs-flask-mongodb/PollsFlaskNewProject.png)

1.  Vous allez être invité à installer des packages externes.  Sélectionnez **Installer dans un environnement virtuel**.

  	![External Packages Dialog](./media/web-sites-python-ptvs-flask-mongodb/PollsFlaskExternalPackages.png)

1.  Sélectionnez **Python 2.7** ou **Python 3.4** en tant qu'interpréteur de base.

  	![Add Virtual Environment Dialog](./media/web-sites-python-ptvs-flask-mongodb/PollsCommonAddVirtualEnv.png)

1.  Vérifiez que l'application fonctionne en appuyant sur <kbd>F5</kbd>.  Par défaut, l'application utilise un référentiel In-Memory qui ne requiert aucune configuration.  Toutes les données sont perdues à l'arrêt du serveur Web.

1.  Cliquez sur **Créer un exemple de sondage**, puis cliquez sur un sondage et votez.

  	![Web Browser](./media/web-sites-python-ptvs-flask-mongodb/PollsFlaskInMemoryBrowser.png)

##<a name="create-a-mongodb-database"></a>Création d'une base de données MongoDB

Pour la base de données, nous allons créer une base de données MongoLab hébergée sur Azure.

Vous pouvez également créer votre propre machine virtuelle exécutée sur Azure, puis installer et administrer MongoDB vous-même.

Pour créer une évaluation gratuite avec MongoLab, procédez comme suit :

1.  Connectez-vous au [Portail de gestion Azure][].

1.  En bas du volet de navigation, cliquez sur **NOUVEAU**.

  	![New Button](./media/web-sites-python-ptvs-flask-mongodb/PollsCommonAzurePlusNew.png)

1.  Cliquez sur **MAGASIN**, puis sur **MongoLab**.

  	![Choose Add-on Dialog](./media/web-sites-python-ptvs-flask-mongodb/PollsCommonMongoLabAddon1.png)

1.  Dans Nom, entrez le nom à utiliser pour le service de base de données.

1.  Choisissez la région ou le groupe d'affinités où localiser le service de base de données. Si vous utilisez la base de données de votre application Azure, sélectionnez la même région que celle du déploiement de votre application.

  	![Personalize Add-on Dialog](./media/web-sites-python-ptvs-flask-mongodb/PollsCommonMongoLabAddon2.png)

1.  Cliquez sur **ACHETER**.

##<a name="configure-the-project"></a>Configuration du projet

Dans cette section, nous configurerons notre application pour utiliser la base de données MongoDB que nous venons de créer.  Nous découvrirons comment obtenir des paramètres de connexion à partir du portail Azure.  Ensuite, nous exécuterons l'application localement.

1.  Dans [Portail de gestion Azure][], cliquez sur **MODULES COMPLÉMENTAIRES**, puis cliquez sur le service MongoLab que vous avez créé précédemment.

1.  Cliquez SUR **INFORMATIONS DE CONNEXION**.  Vous pouvez utiliser le bouton Copier pour placer la valeur de **MONGOLAB\_URI** dans le Presse-papiers.

  	![Connection Info Dialog](./media/web-sites-python-ptvs-flask-mongodb/PollsCommonMongoLabConnectionInfo.png)

1.  Dans Visual Studio, cliquez avec le bouton droit sur le nœud de votre projet dans l'Explorateur de solutions et sélectionnez **Propriétés**.  Cliquez sur l'onglet **Déboguer**.

  	![Project Debug Settings](./media/web-sites-python-ptvs-flask-mongodb/PollsFlaskMongoDBProjectDebugSettings.png)

1.  Définissez les valeurs des variables d'environnement requises par l'application dans **Déboguer la commande du serveur**, **Environnement**.

        REPOSITORY_NAME=mongodb
        MONGODB_HOST=<value of MONGOLAB_URI>
        MONGODB_DATABASE=<database name>

    Ainsi, les variables d'environnement seront définies quand vous **démarrerez le débogage**.  Si vous voulez que les variables soient définies quand vous **démarrez sans débogage**, définissez aussi les mêmes valeurs sous **Exécuter la commande de serveur**.

    Vous pouvez aussi définir des variables d'environnement à l'aide du Panneau de configuration Windows.  Cette option est la plus appropriée pour éviter de stocker des informations d'identification dans le code source/fichier de projet.  Notez que vous devrez redémarrer Visual Studio pour que les nouvelles variables d'environnement soient disponibles pour l'application.

1.  Le code qui implémente le référentiel MongoDB se trouve dans **models/mongodb.py**.

1.  Exécutez l'application avec <kbd>F5</kbd>.  Les sondages créés avec **Créer un exemple de sondage** et les données soumises par vote sont sérialisés dans MongoDB.

1.  Accédez à la page **À propos de** pour vérifier que l'application utilise le référentiel **MongoDB**.

  	![Web Browser](./media/web-sites-python-ptvs-flask-mongodb/PollsFlaskMongoDBAbout.png)

##<a name="explore-the-mongodb-database"></a>Exploration de la base de données MongoDB

Vous pouvez utiliser une application comme [RoboMongo][] pour interroger et modifier une base de données MongoDB.  Dans cette section, nous allons utiliser RoboMongo pour afficher le contenu de la base de données de l'application de sondage.

1.  Créez une connexion.  Vous aurez besoin du **MONGOLAB\_URI** que nous avons récupéré dans la section précédente.

    Notez le format de l'URI : `mongodb://<name>:<password>@<address>:<port>/<name>`

    Le nom correspond à celui que vous avez entré quand vous avez créé le service avec Azure.  Il sert à la fois de nom de base de données et de nom d'utilisateur.

1.  Dans la page de connexion, définissez **Nom** à votre gré.  Affectez également aux champs **Adresse** et **Port** les valeurs *address* et *port* provenant de **MONGOLAB\_URI**.

  	![Connection Settings Dialog](./media/web-sites-python-ptvs-flask-mongodb/PollsCommonRobomongoCreateConnection1.png)

1.  Dans la page d'authentification, affectez aux champs **Base de données** et **Nom d'utilisateur** la valeur *name* provenant de **MONGOLAB\_URI**.  Affectez également au champ **Mot de passe** la valeur *password* provenant de **MONGOLAB\_URI**.

  	![Connection Settings Dialog](./media/web-sites-python-ptvs-flask-mongodb/PollsCommonRobomongoCreateConnection2.png)

1.  Enregistrez et connectez-vous à la base de données.  Vous pouvez maintenant interroger la collection de sondages.

  	![RoboMongo Query Results](./media/web-sites-python-ptvs-flask-mongodb/PollsCommonRobomongoQuery.png)

##<a name="publish-to-an-azure-website"></a>Publication sur un site Web Azure

PTVS propose un moyen simple de déployer votre application Web sur un site Web Azure.

1.  Dans l'**Explorateur de solutions**, cliquez avec le bouton droit sur le nœud du projet, puis sélectionnez **Publier**.

  	![Publish Web Dialog](./media/web-sites-python-ptvs-flask-mongodb/PollsCommonPublishWebSiteDialog.png)

1.  Cliquez sur **Sites Web Microsoft Azure**.

1.  Cliquez sur **Nouveau** pour créer un nouveau site.

1.  Sélectionnez un **nom de site** et une **région**, puis cliquez sur **Créer**.

  	![Create Site on Microsoft Azure Dialog](./media/web-sites-python-ptvs-flask-mongodb/PollsCommonCreateWebSite.png)

1.  Acceptez toutes les valeurs par défaut et cliquez sur **Publier**.

1.  Votre navigateur Web ouvre automatiquement le site publié.  Si vous accédez à la page À propos de, vous voyez que le référentiel **In-Memory** est utilisé, et non le référentiel **MongoDB**.

    La raison en est que les variables d'environnement ne sont pas définies sur le site Web Azure, donc les valeurs par défaut spécifiées dans **settings.py** sont utilisées.

##<a name="configure-the-azure-website"></a>Configuration du site Web Azure

Dans cette section, vous allez configurer des variables d'environnement pour le site.

1.  Dans le [Portail de gestion Azure][], cliquez sur le site créé dans la section précédente.

1.  Dans le menu supérieur, cliquez sur **CONFIGURER**.

  	![Top Menu](./media/web-sites-python-ptvs-flask-mongodb/PollsCommonWebSiteTopMenu.png)

1.  Accédez à la section **Paramètres de l'application** et définissez les valeurs **REPOSITORY\_NAME**, **MONGODB\_HOST** et **MONGODB\_DATABASE** comme décrit dans la section ci-dessus.

  	![App Settings](./media/web-sites-python-ptvs-flask-mongodb/PollsCommonWebSiteConfigureSettingsMongoDB.png)

1.  Dans le menu inférieur, cliquez sur **ENREGISTRER**, puis **REDÉMARRER** et enfin **PARCOURIR**.

  	![Bottom Menu](./media/web-sites-python-ptvs-flask-mongodb/PollsCommonWebSiteConfigureBottomMenu.png)

1.  L'application doit fonctionner comme prévu et utiliser le référentiel **MongoDB**.

    Félicitations !

  	![Web Browser](./media/web-sites-python-ptvs-flask-mongodb/PollsFlaskAzureBrowser.png)

##<a name="next-steps"></a>Étapes suivantes

Suivez ces liens pour en savoir plus sur Python Tools pour Visual Studio, Flask et MongoDB.

- [Documentation de Python Tools pour Visual Studio][]
  - [Projets Web][]
  - [Projets de service cloud][]
  - [Débogage distant sur Microsoft Azure][]
- [Documentation de Flask][]
- [MongoDB][]
- [Documentation de PyMongo][]
- [PyMongo][]


<!--Link references-->
[Centre de développement Python]: /fr-fr/develop/python/
[Azure Cloud Services] : ../cloud-services-python-ptvs/

<!--External Link references-->
[Portail de gestion Azure]: https://manage.windowsazure.com
[RoboMongo]: http://robomongo.org/
[Python Tools 2.1 pour Visual Studio]: http://go.microsoft.com/fwlink/?LinkId=517189
[Exemples VSIX de Python Tools 2.1 pour Visual Studio]: http://go.microsoft.com/fwlink/?LinkId=517189
[Outils Azure SDK pour VS 2013]: http://go.microsoft.com/fwlink/?LinkId=323510
[Outils Azure SDK pour VS 2012]: http://go.microsoft.com/fwlink/?LinkId=323511
[Python 2.7 32 bits]: http://go.microsoft.com/fwlink/?LinkId=517190 
[Python 3.4 32 bits]: http://go.microsoft.com/fwlink/?LinkId=517191
[Documentation de Python Tools pour Visual Studio]: http://pytools.codeplex.com/documentation
[Documentation de Flask]: http://flask.pocoo.org/
[MongoDB]: http://www.mongodb.org/
[PyMongo Documentation]: http://api.mongodb.org/python/current/
[PyMongo]: https://github.com/mongodb/mongo-python-driver
[Débogage distant sur Microsoft Azure]: http://pytools.codeplex.com/wikipage?title=Features%20Azure%20Remote%20Debugging
[Projets Web]: http://pytools.codeplex.com/wikipage?title=Features%20Web%20Project
[Projets de service cloud]: http://pytools.codeplex.com/wikipage?title=Features%20Cloud%20Project


<!--HONumber=42-->
