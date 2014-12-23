<properties linkid="web-sites-python-ptvs-flask-mongodb" title="Flask and MongoDB on Azure with Python Tools 2.1 for Visual Studio" pageTitle="Flask et MongoDB sur Azure avec Python Tools 2.1 for Visual Studio" description="Learn how to use the Python Tools for Visual Studio to create a Flask application that stores data in a MongoDB database instance and can be deployed to a web site." metaKeywords="" services="" solutions="" documentationCenter="Python" authors="huvalo" videoId="" scriptId="" manager="wpickett" editor="" />

<tags ms.service="web-sites" ms.workload="web" ms.tgt_pltfrm="na" ms.devlang="python" ms.topic="article" ms.date="10/10/2014" ms.author="huvalo" />




# Flask et MongoDB sur Azure avec Python Tools 2.1 for Visual Studio

Dans ce didacticiel, nous allons créer une application de sondage simple à l'aide de l'un des exemples de modèles de PTVS.

L'application de sondage définit une abstraction pour son référentiel, donc vous pouvez facilement basculer entre les différents types de référentiel (en mémoire, stockage de tables Azure, MongoDB).

Nous verrons comment utiliser l'un des services hébergés de MongoDB dans Azure, comment configurer l'application pour utiliser MongoDB et comment publier l'application sur un site web Azure.

Consultez le [Centre de développement Python][] pour découvrir d'autres articles abordant le développement des sites web Azure avec PTVS à l'aide des infrastructures web Bottle, Flask et Django, avec MongoDB, le stockage de tables Azure, MySQL et les services de la base de données SQL.  Bien que cet article se concentre sur Sites Web Azure, les étapes sont semblables lors du développement d'[Azure Cloud Services][].

+ [Configuration requise](#prerequisites)
+ [Création du projet](#create-the-project)
+ [Création d'une base de données MongoDB](#create-a-mongodb-database)
+ [Configuration du projet](#configure-the-project)
+ [Exploration de la base de données MongoDB](#explore-the-mongodb-database)
+ [Publication sur un site web Azure](#publish-to-an-azure-website)
+ [Configuration du site web Azure](#configure-the-azure-website)
+ [Étapes suivantes](#next-steps)

##<a name="prerequisites"></a>Configuration requise

 - Visual Studio 2012 ou 2013
 - [PTVS 2,1][]
 - [Python Tools 2.1 pour Visual Studio (exemples VSIX)][]
 - [Outils du Kit de développement logiciel (SDK) Azure pour Visual Studio 2013][] ou [Outils du Kit de développement logiciel (SDK) Azure pour Visual Studio 2012][]
 - [Python 2.7 32 bits][] ou [Python 3.4 32 bits][]
 - [RoboMongo][] (facultatif)

[WACOM.INCLUDE [create-account-and-websites-note](../includes/create-account-and-websites-note.md)]

##<a name="create-the-project"></a>Création du projet

Dans cette section, nous allons créer un projet Visual Studio à l'aide d'un exemple de modèle.  Nous allons créer un environnement virtuel et installer les packages requis.  Ensuite, nous allons exécuter l'application localement à l'aide du référentiel en mémoire par défaut.

1.  Dans Visual Studio, sélectionnez **Fichier**, **Nouveau projet**. 

1.  Les modèles de projet ayant l'extension VSIX des exemples de PTVS sont disponibles sous **Python**, **Exemples**.  Sélectionnez **Projet web Flask de sondage** et cliquez sur OK pour créer le projet.

  	![New Project Dialog](./media/web-sites-python-ptvs-flask-mongodb/PollsFlaskNewProject.png)

1.  Vous êtes invité à installer des packages externes.  Sélectionnez **Installer dans un environnement virtuel**.

  	![External Packages Dialog](./media/web-sites-python-ptvs-flask-mongodb/PollsFlaskExternalPackages.png)

1.  Sélectionnez **Python 2.7** ou **Python 3.4** comme interpréteur de base.

  	![Add Virtual Environment Dialog](./media/web-sites-python-ptvs-flask-mongodb/PollsCommonAddVirtualEnv.png)

1.  Vérifiez que l'application fonctionne en appuyant sur <kbd>F5</kbd>.  Par défaut, l'application utilise un référentiel en mémoire qui ne nécessite pas de configuration.  Toutes les données sont perdues si le serveur web est arrêté.

1.  Cliquez sur **Créer des exemples de sondage**, puis cliquez sur un sondage et votez.

  	![Web Browser](./media/web-sites-python-ptvs-flask-mongodb/PollsFlaskInMemoryBrowser.png)

##<a name="create-a-mongodb-database"></a>Création d'une base de données MongoDB

Pour la base de données, nous allons créer une base de données MongoLab hébergée sur Azure.

Autrement, vous pouvez créer votre propre machine virtuelle s'exécutant sur Azure, puis installer et administrer MongoDB vous-même.

Vous pouvez créer une version d'évaluation gratuite avec MongoLab en suivant ces étapes.

1.  Connectez-vous au [portail de gestion Azure][].

1.  En bas du volet de navigation, cliquez sur **NOUVEAU**.

  	![New Button](./media/web-sites-python-ptvs-flask-mongodb/PollsCommonAzurePlusNew.png)

1.  Cliquez sur **STORE**, puis sur **MongoLab**.

  	![Choose Add-on Dialog](./media/web-sites-python-ptvs-flask-mongodb/PollsCommonMongoLabAddon1.png)

1.  Dans le champ Nom, tapez un nom à utiliser pour le service de base de données.

1.  Choisissez la région/le groupe d'affinités dans lequel localiser le service de base de données. Si vous utilisez la base de données à partir de votre application Azure, sélectionnez la même région que celle du déploiement de votre application.

  	![Personalize Add-on Dialog](./media/web-sites-python-ptvs-flask-mongodb/PollsCommonMongoLabAddon2.png)

1.  Cliquez sur **ACHAT**.

##<a name="configure-the-project"></a>Configuration du projet

Dans cette section, nous allons configurer notre application pour utiliser la base de données MongoDB que nous venons de créer.  Nous verrons comment obtenir les paramètres de connexion à partir du portail Azure.  Puis nous exécuterons l'application localement.

1.  Dans le [portail de gestion Azure][], cliquez sur **MODULES COMPLÉMENTAIRES**, puis cliquez sur le service MongoLab que vous avez créé précédemment.

1.  Cliquez sur **INFORMATIONS DE CONNEXION**.  Vous pouvez utiliser le bouton Copier pour placer la valeur de **MONGOLAB\_URI** dans le Presse-papiers.

  	![Connection Info Dialog](./media/web-sites-python-ptvs-flask-mongodb/PollsCommonMongoLabConnectionInfo.png)

1.  Dans Visual Studio, cliquez avec le bouton droit sur le nœud de votre projet dans l'Explorateur de solutions, puis sélectionnez **Propriétés**.  Cliquez sur l'onglet **Déboguer**.

  	![Project Debug Settings](./media/web-sites-python-ptvs-flask-mongodb/PollsFlaskMongoDBProjectDebugSettings.png)

1.  Définissez les valeurs des variables d'environnement requises par l'application dans **Déboguer la commande du serveur**, **Environnement**.

        REPOSITORY_NAME=mongodb
        MONGODB_HOST=<valeur de MONGOLAB_URI>
        MONGODB_DATABASE=<nom de la base de données>

    Cela permet de définir les variables d'environnement pour **Démarrer le débogage**.  Si vous souhaitez que les variables soient définies pour **Exécuter sans débogage**, définissez également les même valeurs sous **Exécuter la commande du serveur**.

    Vous pouvez également définir des variables d'environnement à l'aide du Panneau de configuration Windows.  Il s'agit d'une meilleure option si vous souhaitez éviter de stocker des informations d'identification dans le code source/fichier de projet.  Notez que vous devrez redémarrer Visual Studio pour que les nouvelles valeurs d'environnement soient accessibles par l'application.

1.  Le code qui implémente le référentiel MongoDB se situe dans **models/mongodb.py**.

1.  Exécutez l'application à l'aide de la touche <kbd>F5</kbd>.  Les sondages créés avec l'option **Créer des exemples de sondage** et les données soumises au vote seront sérialisés dans MongoDB.

1.  Accédez à la page **À propos** pour vérifier que l'application utilise le référentiel **MongoDB**.

  	![Web Browser](./media/web-sites-python-ptvs-flask-mongodb/PollsFlaskMongoDBAbout.png)

##<a name="explore-the-mongodb-database"></a>Exploration de la base de données MongoDB

Vous pouvez utiliser une application telle que [RoboMongo][] pour interroger et apporter des modifications à une base de données MongoDB.  Dans cette section, nous allons utiliser RoboMongo pour afficher le contenu de la base de données des applications de sondage.

1.  Créez une nouvelle connexion.  Vous aurez besoin du **MONGOLAB\_URI** que nous avons récupéré dans la section précédente.

    Notez le format de l'URI : " mongodb://<nom>:<motdepasse>@<adresse>:<port>/<nom> "

    Le nom correspond à celui que vous avez entré lorsque vous avez créé le service avec Azure.  Il est utilisé à la fois pour le nom de la base de données et le nom d'utilisateur.

1.  Sur la page de connexion, définissez le **Nom**, quel qu'il soit, pour la connexion.  Définissez également les champs **Adresse** et **Port** selon les paramètres *adresse* et *port* de **MONGOLAB\_URI**.

  	![Connection Settings Dialog](./media/web-sites-python-ptvs-flask-mongodb/PollsCommonRobomongoCreateConnection1.png)

1.  Sur la page d'authentification, définissez les champs **Base de données** et **Nom d'utilisateur** selon le paramètre *nom* de **MONGOLAB\_URI**.  Définissez également le **Mot de passe** d'après le paramètre *password* de **MONGOLAB\_URI**.

  	![Connection Settings Dialog](./media/web-sites-python-ptvs-flask-mongodb/PollsCommonRobomongoCreateConnection2.png)

1.  Enregistrez et connectez-vous à la base de données.  Vous pouvez désormais interroger la collection de sondages.

  	![RoboMongo Query Results](./media/web-sites-python-ptvs-flask-mongodb/PollsCommonRobomongoQuery.png)

##<a name="publish-to-an-azure-website"></a>Publication sur un site web Azure

PTVS est un moyen facile de déployer votre application web sur un site web Azure.

1.  Dans l'**Explorateur de solutions**, cliquez avec le bouton droit sur le nœud du projet et sélectionnez **Publier**.

  	![Publish Web Dialog](./media/web-sites-python-ptvs-flask-mongodb/PollsCommonPublishWebSiteDialog.png)

1.  Cliquez sur **Sites Web Microsoft Azure**.

1.  Cliquez sur **Nouveau** pour créer un nouveau site.

1.  Sélectionnez un **Nom du site** et une **Région**, puis cliquez sur **Créer**.

  	![Create Site on Microsoft Azure Dialog](./media/web-sites-python-ptvs-flask-mongodb/PollsCommonCreateWebSite.png)

1.  Acceptez tous les autres paramètres par défaut et cliquez sur **Publier**.

1.  Votre navigateur web s'ouvre automatiquement sur le site publié.  Si vous accédez à la page À propos, vous verrez qu'elle utilise le référentiel **en mémoire** et non le référentiel **MongoDB**.

    C'est parce que les variables d'environnement ne sont pas définies dans Sites Web Azure. Elle utilise donc les valeurs par défaut spécifiées dans **settings.py**.

##<a name="configure-the-azure-website"></a>Configuration du site web Azure

Dans cette section, nous allons configurer les variables d'environnement pour le site.

1.  Dans le [portail de gestion Azure][], cliquez sur le site créé dans la section précédente.

1.  Dans le menu supérieur, cliquez sur **CONFIGURER**.

  	![Top Menu](./media/web-sites-python-ptvs-flask-mongodb/PollsCommonWebSiteTopMenu.png)

1.  Faites défiler jusqu'à la section de **paramètres d'application** et définissez les valeurs pour **REPOSITORY\_NAME**, **MONGODB\_HOST** et **MONGODB\_DATABASE**, comme décrit dans la section ci-dessus.

  	![App Settings](./media/web-sites-python-ptvs-flask-mongodb/PollsCommonWebSiteConfigureSettingsMongoDB.png)

1.  Dans le menu inférieur, cliquez sur **ENREGISTRER**, puis sur **REDÉMARRER** et enfin sur **PARCOURIR**.

  	![Bottom Menu](./media/web-sites-python-ptvs-flask-mongodb/PollsCommonWebSiteConfigureBottomMenu.png)

1.  L'application doit fonctionner tel que prévu, en utilisant le référentiel **MongoDB**.

    Félicitations !

  	![Web Browser](./media/web-sites-python-ptvs-flask-mongodb/PollsFlaskAzureBrowser.png)

##<a name="next-steps"></a>Étapes suivantes

Suivez ces liens pour en savoir plus sur Python Tools for Visual Studio, Flask et MongoDB.

- [Documentation relative à Python Tools for Visual Studio][]
  - [Projets web][]
  - [Projets de service cloud][]
  - [Débogage distant sur Microsoft Azure][]
- [Documentation relative à Flask][]
- [MongoDB][]
- [Documentation relative à PyMongo][]
- [PyMongo][]


<!--Link references-->
[Centre de développement Python]: /fr-fr/develop/python/
[Services cloud Azure]: ../cloud-services-python-ptvs/

<!--External Link references-->
[Portail de gestion Azure]: https://manage.windowsazure.com
[RoboMongo]: http://robomongo.org/
[PTVS 2,1]: http://go.microsoft.com/fwlink/?LinkId=517189
[Python Tools 2.1 pour Visual Studio (exemples VSIX)]: http://go.microsoft.com/fwlink/?LinkId=517189
[Outils du Kit de développement logiciel (SDK) Azure pour Visual Studio 2013]: http://go.microsoft.com/fwlink/?LinkId=323510
[Outils du Kit de développement logiciel (SDK) Azure pour Visual Studio 2012]: http://go.microsoft.com/fwlink/?LinkId=323511
[Python 2.7 (32 bits)]: http://go.microsoft.com/fwlink/?LinkId=517190 
[Python 3.4 (32 bits)]: http://go.microsoft.com/fwlink/?LinkId=517191
[Documentation relative à Python Tools for Visual Studio]: http://pytools.codeplex.com/documentation
[Documentation relative à Flask]: http://flask.pocoo.org/
[MongoDB]: http://www.mongodb.org/
[Documentation relative à PyMongo]: http://api.mongodb.org/python/current/
[PyMongo]: https://github.com/mongodb/mongo-python-driver
[Débogage distant sur Microsoft Azure]: http://pytools.codeplex.com/wikipage?title=Features%20Azure%20Remote%20Debugging
[Projets web]: http://pytools.codeplex.com/wikipage?title=Features%20Web%20Project
[Projets de service cloud]: http://pytools.codeplex.com/wikipage?title=Features%20Cloud%20Project
