<properties 
	pageTitle="Bottle et MongoDB sur Azure avec Python Tools 2.1 pour Visual Studio" 
	description="Découvrez comment utiliser Python Tools pour Visual Studio afin de créer une application Bottle qui stocke les données dans une instance de base de données MongoDB et qui peut être déployée sur Azure App Service Web Apps." 
	services="app-service\web" 
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
	ms.date="04/15/2015" 
	ms.author="huguesv"/>


# Bottle et MongoDB sur Azure avec Python Tools 2.1 pour Visual Studio

> **REMARQUE :** la version préliminaire du portail ne prend pas en charge le flux de travail MongoLab.

Dans ce didacticiel, nous allons utiliser [Python Tools pour Visual Studio] afin de créer une application web de sondage simple, à l’aide de l’un des exemples de modèle PTVS. Ce didacticiel est également disponible en [vidéo](https://www.youtube.com/watch?v=8hQMyf8p_Jo).

L’application web de sondage définit une abstraction pour son référentiel, ce qui vous permet de basculer facilement d’un type de référentiel à un autre (In-Memory, Azure Table Storage, MongoDB).

Nous allons découvrir comment utiliser l’un des services MongoDB hébergés sur Azure, comment configurer l’application pour utiliser MongoDB et comment publier l’application web sur [Azure App Service Web Apps](http://go.microsoft.com/fwlink/?LinkId=529714).

Visitez le [Centre de développement Python] pour consulter d’autres articles sur le développement d’applications Azure App Service Web Apps avec PTVS à l’aide des infrastructures web Bottle, Flask et Django, ainsi que des services MongoDB, Azure Table Storage, MySQL et Base de données SQL. Cet article concerne App Service, mais les étapes sont similaires lorsque vous développez pour [Azure Cloud Services].

## Composants requis

 - Visual Studio 2012 ou 2013
 - [Python Tools 2.1 pour Visual Studio]
 - [Python Tools 2.1 pour Visual Studio Samples VSIX]
 - [Outils du Kit de développement logiciel (SDK) Azure pour Visual Studio 2013] ou [Outils du Kit de développement logiciel (SDK) Azure pour Visual Studio 2012]
 - [Python 2.7 32 bits] ou [Python 3.4 32 bits]
 - [RoboMongo](optional)

[AZURE.INCLUDE [create-account-and-websites-note](../../includes/create-account-and-websites-note.md)]

>[AZURE.NOTE]Si vous voulez vous familiariser avec Azure App Service avant d’ouvrir un compte Azure, accédez à la page [Essayer App Service](http://go.microsoft.com/fwlink/?LinkId=523751). Vous pourrez créer immédiatement une application web temporaire dans App Service. Aucune carte de crédit n’est requise ; vous ne prenez aucun engagement.

## Création du projet

Dans cette section, nous allons créer un projet Visual Studio à l’aide d’un exemple de modèle. Nous allons créer un environnement virtuel et installer les packages requis. Ensuite, nous allons exécuter l’application localement à l’aide du référentiel In-Memory par défaut.

1.  Dans Visual Studio, sélectionnez **Fichier** puis **Nouveau projet**. 

1.  Les modèles de projet issus de PTVS Samples VSIX sont disponibles sous **Python**, **Exemples**. Sélectionnez **Projet web de sondage Bottle** et cliquez sur OK pour créer le projet.

  	![Boîte de dialogue Nouveau projet](./media/web-sites-python-ptvs-bottle-mongodb/PollsBottleNewProject.png)

1.  Vous allez être invité à installer des packages externes. Sélectionnez **Installer dans un environnement virtuel**.

  	![Boîte de dialogue Packages externes](./media/web-sites-python-ptvs-bottle-mongodb/PollsBottleExternalPackages.png)

1.  Sélectionnez **Python 2.7** ou **Python 3.4** comme interpréteur de base.

  	![Boîte de dialogue Ajouter un environnement virtuel](./media/web-sites-python-ptvs-bottle-mongodb/PollsCommonAddVirtualEnv.png)

1.  Vérifiez que l’application fonctionne en appuyant sur <kbd>F5</kbd>. Par défaut, l’application utilise un référentiel In-Memory qui ne requiert aucune configuration. Toutes les données sont perdues à l’arrêt du serveur web.

1.  Cliquez sur **Créer un exemple de sondage**, puis sur un sondage et un vote.

  	![Navigateur web](./media/web-sites-python-ptvs-bottle-mongodb/PollsBottleInMemoryBrowser.png)

## Création d’une base de données MongoDB

Pour la base de données, nous allons créer une base de données MongoLab hébergée sur Azure.

Autrement, vous pouvez créer votre propre machine virtuelle s'exécutant sur Azure, puis installer et administrer MongoDB vous-même.

Pour créer une évaluation gratuite avec MongoLab, procédez comme suit :

1.  Connectez-vous au [portail de gestion Azure].

1.  En bas du volet de navigation, cliquez sur **NEW**.

  	<!-- ![New Button](./media/web-sites-python-ptvs-bottle-mongodb/PollsCommonAzurePlusNew.png) -->

1.  Cliquez sur **MARKETPLACE**, **MongoLab**, puis sur **Suivant**.

  	<!-- ![Choose Add-on Dialog](./media/web-sites-python-ptvs-bottle-mongodb/PollsCommonMongoLabAddon1.png) -->

1.  Dans **Nom**, saisissez le nom à utiliser pour le service de base de données.

1.  Choisissez la **région** dans laquelle localiser le service de base de données. Si vous utilisez la base de données de votre application Azure, sélectionnez la même région que celle du déploiement de votre application.

  	<!-- ![Personalize Add-on Dialog](./media/web-sites-python-ptvs-bottle-mongodb/PollsCommonMongoLabAddon2.png) -->

1.  Cliquez sur **Suivant**, puis sur **ACHETER**.

## Configurer le projet

Dans cette section, nous allons configurer notre application pour utiliser la base de données MongoDB que nous venons de créer. Nous allons découvrir comment obtenir des paramètres de connexion à partir du portail Azure. Ensuite, nous allons exécuter l’application localement.

1.  Dans le [portail de gestion Azure], cliquez sur **MARKETPLACE** puis sur le service MongoLab créé précédemment.

1.  Cliquez sur **INFORMATIONS DE CONNEXION**. Vous pouvez utiliser le bouton de copie pour placer la valeur de **MONGOLAB\_URI** dans le Presse-papiers.

  	<!--![Connection Info Dialog](./media/web-sites-python-ptvs-bottle-mongodb/PollsCommonMongoLabConnectionInfo.png) -->

1.  Dans l’Explorateur de solutions, cliquez avec le bouton droit sur le projet et sélectionnez **Propriétés**. Cliquez sur l’onglet **Déboguer**.

  	![Paramètres de débogage du projet](./media/web-sites-python-ptvs-bottle-mongodb/PollsBottleMongoDBProjectDebugSettings.png)

1.  Définissez les valeurs des variables d’environnement requises par l’application dans **Déboguer la commande de serveur**, **Environnement**.

        REPOSITORY_NAME=mongodb
        MONGODB_HOST=<value of MONGOLAB_URI>
        MONGODB_DATABASE=<database name>

    Les variables d’environnement sont définies lorsque la commande **Démarrer le débogage** est exécutée. Si vous voulez que les variables soient définies lorsque la commande **Démarrer sans débogage** est exécutée, indiquez les mêmes valeurs sous **Exécuter la commande de serveur**.

    Vous pouvez aussi définir des variables d’environnement à l’aide du Panneau de configuration Windows. Cette option est la plus appropriée pour éviter de stocker des informations d’identification dans le code source/fichier de projet. Notez que vous devrez redémarrer Visual Studio pour que les nouvelles variables d’environnement soient disponibles pour l’application.

1.  Le code qui implémente le référentiel MongoDB se trouve dans **models/mongodb.py**.

1.  Cliquez sur `F5` pour exécuter l'application. Les sondages créés avec **Créer un exemple de sondage** et les données soumises par vote sont sérialisés dans MongoDB.

1.  Accédez à la page **À propos** pour vérifier que l’application utilise le référentiel **MongoDB**.

  	![Navigateur web](./media/web-sites-python-ptvs-bottle-mongodb/PollsBottleMongoDBAbout.png)

## Exploration de la base de données MongoDB

Vous pouvez utiliser une application telle que [RoboMongo] pour interroger et modifier une base de données MongoDB. Dans cette section, nous allons utiliser RoboMongo pour afficher le contenu de la base de données de l’application de sondage.

1.  Créez une connexion. Vous aurez besoin de la valeur **MONGOLAB\_URI** que nous avons récupérée dans la section précédente.

    Notez le format de l’URI : `mongodb://<name>:<password>@<address>:<port>/<name>`

    Le nom correspond à celui que vous avez saisi quand vous avez créé le service avec Azure. Il sert à la fois de nom de base de données et de nom d’utilisateur.

1.  Dans la page de connexion, renseignez le champ **Name** avec la valeur de votre choix. Dans les champs **Address** et **Port**, indiquez l’*adresse* et le *port* figurant dans **MONGOLAB\_URI**.

  	![Boîte de dialogue Paramètre de connexion](./media/web-sites-python-ptvs-bottle-mongodb/PollsCommonRobomongoCreateConnection1.png)

1.  Dans la page d'authentification, renseignez les champs **Database** et **User name** avec le *nom* figurant dans **MONGOLAB\_URI**. De même, renseignez le champ **Password** avec le *mot de passe* figurant dans **MONGOLAB\_URI**.

  	![Boîte de dialogue Paramètre de connexion](./media/web-sites-python-ptvs-bottle-mongodb/PollsCommonRobomongoCreateConnection2.png)

1.  Enregistrez et connectez-vous à la base de données. Vous pouvez maintenant interroger la collection de sondages.

  	![Résultats de la requête MongoDB](./media/web-sites-python-ptvs-bottle-mongodb/PollsCommonRobomongoQuery.png)

## Publier l'application web sur Azure

Le kit de développement logiciel (SDK) Azure .NET propose un moyen simple de déployer votre application web sur Azure.

1.  Dans l’**Explorateur de solutions**, cliquez avec le bouton droit sur le nœud du projet et sélectionnez **Publier**.

1.  Cliquez sur **Microsoft Azure Web Apps**.

3. Si vous n’êtes pas connecté à Azure, cliquez sur **Se connecter** et utilisez le compte Microsoft de votre abonnement Azure.

2.  Cliquez sur **Nouveau** pour créer une application web.

1.  Renseignez les champs suivants et cliquez sur **Créer**.
	-	**Nom de l’application web**
	-	**Plan App Service**
	-	**Groupe de ressources**
	-	**Région**
	-	Dans **Serveur de base de données**, conservez **Aucune base de données**.

  	<!-- ![Create Site on Microsoft Azure Dialog](./media/web-sites-python-ptvs-bottle-mongodb/PollsCommonCreateWebSite.png) -->

1.  Acceptez toutes les valeurs par défaut et cliquez sur **Publier**.

1.  Votre navigateur web ouvre automatiquement l’application web publiée. Si vous accédez à la page À propos, vous verrez qu’elle utilise le référentiel **In-Memory** et non le référentiel **MongoDB**.

    En effet, comme les variables d’environnement ne sont pas définies dans l’instance Web Apps d’Azure App Service, l’application utilise les valeurs par défaut spécifiées dans **settings.py**.

## Configurer l'instance Web Apps

Dans cette section, nous allons configurer les variables d’environnement de l’instance Web Apps.

1.  Dans le [portail de gestion Azure], cliquez sur l’application web créée dans la section précédente.

1.  Dans le menu supérieur, cliquez sur **CONFIGURER**.

  	<!-- ![Top Menu](./media/web-sites-python-ptvs-bottle-mongodb/PollsCommonWebSiteTopMenu.png) -->

1.  Accédez à la section **Paramètres de l’application** et définissez les valeurs de **REPOSITORY\_NAME**, **MONGODB\_HOST** et **MONGODB\_DATABASE** comme dans la section ci-dessus.

  	<!-- ![App Settings](./media/web-sites-python-ptvs-bottle-mongodb/PollsCommonWebSiteConfigureSettingsMongoDB.png) -->

1.  Dans le menu inférieur, cliquez sur **ENREGISTRER**, **REDÉMARRER** et **PARCOURIR**.

  	<!-- ![Bottom Menu](./media/web-sites-python-ptvs-bottle-mongodb/PollsCommonWebSiteConfigureBottomMenu.png) -->

1.  L’application doit fonctionner comme prévu et utiliser le référentiel **MongoDB**.

    Félicitations !

  	![Navigateur web](./media/web-sites-python-ptvs-bottle-mongodb/PollsBottleAzureBrowser.png)

## Étapes suivantes

Suivez ces liens pour en savoir plus sur Python Tools pour Visual Studio, Bottle et MongoDB.

- [Documentation relative à Python Tools for Visual Studio]
  - [Projets web]
  - [Projets de service cloud]
  - [Débogage à distance sur Microsoft Azure]
- [Documentation relative à Bottle]
- [MongoDB]
- [Documentation relative à PyMongo]
- [PyMongo]

[AZURE.INCLUDE [app-service-web-whats-changed](../../includes/app-service-web-whats-changed.md)]

[AZURE.INCLUDE [app-service-web-try-app-service](../../includes/app-service-web-try-app-service.md)]

<!--Link references-->
[Centre de développement Python]: /develop/python/
[Azure Cloud Services]: ../cloud-services-python-ptvs.md

<!--External Link references-->
[portail de gestion Azure]: https://manage.windowsazure.com
[RoboMongo]: http://robomongo.org/
[Python Tools pour Visual Studio]: http://aka.ms/ptvs
[Python Tools 2.1 pour Visual Studio]: http://go.microsoft.com/fwlink/?LinkId=517189
[Python Tools 2.1 pour Visual Studio Samples VSIX]: http://go.microsoft.com/fwlink/?LinkId=517189
[Outils du Kit de développement logiciel (SDK) Azure pour Visual Studio 2013]: http://go.microsoft.com/fwlink/?LinkId=323510
[Outils du Kit de développement logiciel (SDK) Azure pour Visual Studio 2012]: http://go.microsoft.com/fwlink/?LinkId=323511
[Python 2.7 32 bits]: http://go.microsoft.com/fwlink/?LinkId=517190
[Python 3.4 32 bits]: http://go.microsoft.com/fwlink/?LinkId=517191
[Documentation relative à Python Tools for Visual Studio]: http://pytools.codeplex.com/documentation
[Documentation relative à Bottle]: http://bottlepy.org/docs/dev/index.html
[MongoDB]: http://www.mongodb.org/
[Documentation relative à PyMongo]: http://api.mongodb.org/python/current/
[PyMongo]: https://github.com/mongodb/mongo-python-driver
[Débogage à distance sur Microsoft Azure]: http://pytools.codeplex.com/wikipage?title=Features%20Azure%20Remote%20Debugging
[Projets web]: http://pytools.codeplex.com/wikipage?title=Features%20Web%20Project
[Projets de service cloud]: http://pytools.codeplex.com/wikipage?title=Features%20Cloud%20Project
 

<!---HONumber=August15_HO6-->