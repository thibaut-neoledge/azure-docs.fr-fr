<properties 
	pageTitle="Flask et le stockage de tables Azure sur Azure avec Python Tools 2.1 pour Visual Studio" 
	description="Découvrez comment utiliser les outils Python pour Visual Studio pour créer une application Flask qui stocke les données dans le stockage de tables Azure et peut être déployée vers un site Web." 
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




# Flask et le stockage de tables Azure sur Azure avec Python Tools 2.1 pour Visual Studio 

Dans ce didacticiel, nous allons créer une application de sondage simple à l'aide de l'un des exemples de modèles PTVS. Ce didacticiel est également disponible sous forme de [vidéo](https://www.youtube.com/watch?v=qUtZWtPwbTk).

L'application de sondage définit une abstraction pour son référentiel, ce qui vous permet de basculer facilement d'un type de référentiel à un autre (In-Memory, le stockage de tables Azure, MongoDB).

Nous allons découvrir comment créer un compte Azure Storage, comment configurer l'application pour utiliser le stockage de tables Azure et comment publier l'application sur un site Web Azure.

Visitez le [Centre de développement Python][] pour consulter d'autres articles sur le développement de Sites Web Azure avec PTVS à l'aide des infrastructures Web Bottle, Flask et Django, ainsi que de MongoDB, du stockage de tables Azure, de MySQL et des services de base de données SQL.  Quand bien même cet article se concentre sur Sites Web Azure, les étapes sont similaires pour développer [Azure Cloud Services][].

+ [Configuration requise](#prerequisites)
+ [Création du projet](#create-the-project)
+ [Création d'un compte Azure Storage](#create-an-azure-storage-account)
+ [Configuration du projet](#configure-the-project)
+ [Exploration du stockage de tables Azure](#explore-the-azure-table-storage)
+ [Publication sur un site Web Azure](#publish-to-an-azure-website)
+ [Configuration du site Web Azure](#configure-the-azure-website)
+ [Étapes suivantes](#next-steps)

##<a name="prerequisites"></a>Configuration requise

 - Visual Studio 2012 ou 2013
 - [Python Tools 2.1 pour Visual Studio][]
 - [Exemples VSIX de Python Tools 2.1 pour Visual Studio][]
 - [Outils SDK Azure pour VS 2013][] ou [Outils SDK Azure pour VS 2012][]
 - [Python 2.7 32 bits][] ou [Python 3.4 32 bits][]

[AZURE.INCLUDE [create-account-and-websites-note](../includes/create-account-and-websites-note.md)]

##<a name="create-the-project"></a>Création du projet

Dans cette section, nous créerons un projet Visual Studio à l'aide d'un exemple de modèle.  Nous créerons un environnement virtuel et nous installerons les packages requis.  Ensuite, nous exécuterons l'application localement à l'aide du référentiel In-Memory par défaut.

1.  Dans Visual Studio, sélectionnez **Fichier**, **Nouveau projet**.

1.  Les modèles de projet issus des exemples VSIX de PTVS sont disponibles sous **Python**, **Exemples**.  Sélectionnez **Projet Web de sondage Flask** et cliquez sur OK pour créer le projet.

  	![New Project Dialog](./media/web-sites-python-ptvs-flask-table-storage/PollsFlaskNewProject.png)

1.  Vous allez être invité à installer des packages externes.  Sélectionnez **Installer dans un environnement virtuel**.

  	![External Packages Dialog](./media/web-sites-python-ptvs-flask-table-storage/PollsFlaskExternalPackages.png)

1.  Sélectionnez **Python 2.7** ou **Python 3.4** en tant qu'interpréteur de base.

  	![Add Virtual Environment Dialog](./media/web-sites-python-ptvs-flask-table-storage/PollsCommonAddVirtualEnv.png)

1.  Vérifiez que l'application fonctionne en appuyant sur <kbd>F5</kbd>.  Par défaut, l'application utilise un référentiel In-Memory qui ne requiert aucune configuration.  Toutes les données sont perdues à l'arrêt du serveur Web.

1.  Cliquez sur **Créer un exemple de sondage**, puis cliquez sur un sondage et votez.

  	![Web Browser](./media/web-sites-python-ptvs-flask-table-storage/PollsFlaskInMemoryBrowser.png)

##<a name="create-an-azure-storage-account"></a>Création d'un compte Azure Storage

Pour utiliser les opérations de stockage, vous avez besoin d'un compte Azure Storage. Pour en créer un, procédez comme suit :

1.  Connectez-vous au [Portail de gestion Azure][].

1.  En bas du volet de navigation, cliquez sur **NOUVEAU**.

  	![New Button](./media/web-sites-python-ptvs-flask-table-storage/PollsCommonAzurePlusNew.png)

1.  Cliquez sur **SERVICES DE DONNÉES**, sur **STOCKAGE**, puis sur **CRÉATION RAPIDE**.

  	![Quick Create](./media/web-sites-python-ptvs-flask-table-storage/PollsCommonAzureStorageCreate.png)

1.  Dans URL, tapez un nom de sous-domaine à utiliser dans l'URI du compte de stockage.  L'entrée peut être composée de 3 à 24 lettres minuscules et chiffres. Cette valeur devient le nom d'hôte contenu dans l'URI utilisé pour adresser les ressources d'objets blob, de files d'attente et de tables pour l'abonnement.

1.  Choisissez la région ou le groupe d'affinités dans lequel localiser le stockage. Si vous utilisez le stockage à partir de votre application Azure, sélectionnez la même région que celle du déploiement de votre application.

1.  Vous pouvez éventuellement activer les géo-réplications.  Avec les géo-réplications, Azure Storage pérennise maintenant vos données à deux emplacements. À ces deux emplacements, Azure Storage tient constamment à jour plusieurs réplicas intègres de vos données.

1.  Cliquez sur **CRÉER UN COMPTE DE STOCKAGE**.

##<a name="configure-the-project"></a>Configuration du projet

Dans cette section, nous configurerons notre application pour utiliser le compte de stockage que nous venons de créer.  Nous découvrirons comment obtenir des paramètres de connexion à partir du portail Azure.  Ensuite, nous exécuterons l'application localement.

1.  Dans le [Portail de gestion Azure][], cliquez sur le compte de stockage créé dans la section précédente.

1.  Cliquez sur **GÉRER LES CLÉS D'ACCÈS**.

  	![Manage Access Keys Dialog](./media/web-sites-python-ptvs-flask-table-storage/PollsCommonAzureTableStorageManageKeys.png)

1.  Dans Visual Studio, cliquez avec le bouton droit sur le nœud de votre projet dans l'Explorateur de solutions et sélectionnez **Propriétés**.  Cliquez sur l'onglet **Déboguer**.

  	![Project Debug Settings](./media/web-sites-python-ptvs-flask-table-storage/PollsFlaskAzureTableStorageProjectDebugSettings.png)

1.  Définissez les valeurs des variables d'environnement requises par l'application dans **Déboguer la commande du serveur**, **Environnement**.

        REPOSITORY_NAME=azuretablestorage
        STORAGE_NAME=<storage account name>
        STORAGE_KEY=<primary access key>

    Ainsi, les variables d'environnement seront définies quand vous **démarrerez le débogage**.  Si vous voulez que les variables soient définies quand vous **démarrez sans débogage**, définissez aussi les mêmes valeurs sous **Exécuter la commande de serveur**.

    Vous pouvez aussi définir des variables d'environnement à l'aide du Panneau de configuration Windows.  Cette option est la plus appropriée pour éviter de stocker des informations d'identification dans le code source/fichier de projet.  Notez que vous devrez redémarrer Visual Studio pour que les nouvelles variables d'environnement soient disponibles pour l'application.

1.  Le code qui implémente le référentiel du stockage de tables Azure se trouve dans **models/azuretablestorage.py**.  Consultez la [documentation] pour plus d'informations sur la manière d'utiliser le service de table de Python.

1.  Exécutez l'application avec <kbd>F5</kbd>.  Les sondages créés avec **Créer un exemple de sondage** et les données soumises par vote sont sérialisés dans le stockage de tables Azure.

1.  Accédez à la page **À propos de** pour vérifier que l'application utilise le référentiel du **stockage de tables Azure**.

  	![Web Browser](./media/web-sites-python-ptvs-flask-table-storage/PollsFlaskAzureTableStorageAbout.png)

##<a name="explore-the-azure-table-storage"></a>Exploration du stockage de tables Azure

L'affichage et la modification des tables de stockage sont très faciles dans Visual Studio grâce à l'Explorateur de serveurs.  Dans cette section, nous allons utiliser l'Explorateur de serveurs pour afficher le contenu des tables de l'application de sondage.

> [AZURE.NOTE] Pour cela, les outils Microsoft Azure doivent être installés. Ils sont disponibles dans le [SDK Azure pour .NET][].

1.  Ouvrez l'**Explorateur de serveurs**.  Développez **Azure**, **Stockage**, votre compte de stockage, puis **Tables**.

  	![Server Explorer](./media/web-sites-python-ptvs-flask-table-storage/PollsCommonServerExplorer.png)

1.  Double-cliquez sur la table des **sondages** ou **choix** pour afficher le contenu de la table dans une fenêtre de document, ainsi que les entités d'ajout/de suppression/de modification.

  	![Table Query Results](./media/web-sites-python-ptvs-flask-table-storage/PollsCommonServerExplorerTable.png)

##<a name="publish-to-an-azure-website"></a>Publication sur un site Web Azure

PTVS propose un moyen simple de déployer votre application Web sur un site Web Azure.

1.  Dans l'**Explorateur de solutions**, cliquez avec le bouton droit sur le nœud du projet, puis sélectionnez **Publier**.

  	![Publish Web Dialog](./media/web-sites-python-ptvs-flask-table-storage/PollsCommonPublishWebSiteDialog.png)

1.  Cliquez sur **Sites Web Microsoft Azure**.

1.  Cliquez sur **Nouveau** pour créer un nouveau site.

1.  Sélectionnez un **nom de site** et une **région**, puis cliquez sur **Créer**.

  	![Create Site on Microsoft Azure Dialog](./media/web-sites-python-ptvs-flask-table-storage/PollsCommonCreateWebSite.png)

1.  Acceptez toutes les valeurs par défaut et cliquez sur **Publier**.

1.  Votre navigateur Web ouvre automatiquement le site publié.  Si vous accédez à la page À propos de, vous voyez que le référentiel **In-Memory** est utilisé, et non le référentiel du **stockage de tables Azure**.

    La raison en est que les variables d'environnement ne sont pas définies sur le site Web Azure, donc les valeurs par défaut spécifiées dans **settings.py** sont utilisées.

##<a name="configure-the-azure-website"></a>Configuration du site Web Azure

Dans cette section, vous allez configurer des variables d'environnement pour le site.

1.  Dans le [Portail de gestion Azure][], cliquez sur le site créé dans la section précédente.

1.  Dans le menu supérieur, cliquez sur **CONFIGURER**.

  	![Top Menu](./media/web-sites-python-ptvs-flask-table-storage/PollsCommonWebSiteTopMenu.png)

1.  Accédez à la section **Paramètres de l'application** et définissez les valeurs **REPOSITORY\_NAME**, **STORAGE\_NAME** et **STORAGE\_KEY** comme décrit dans la section ci-dessus.

  	![App Settings](./media/web-sites-python-ptvs-flask-table-storage/PollsCommonWebSiteConfigureSettingsTableStorage.png)

1. Dans le menu inférieur, cliquez sur **ENREGISTRER**, puis **REDÉMARRER** et enfin **PARCOURIR**.

  	![Bottom Menu](./media/web-sites-python-ptvs-flask-table-storage/PollsCommonWebSiteConfigureBottomMenu.png)

1.  L'application doit fonctionner comme prévu et utiliser le référentiel du **stockage de tables Azure**.

    Félicitations !

  	![Web Browser](./media/web-sites-python-ptvs-flask-table-storage/PollsFlaskAzureBrowser.png)

##<a name="next-steps"></a>Étapes suivantes

Suivez ces liens pour en savoir plus sur Python Tools pour Visual Studio, Flask et le stockage de tables Azure.

- [Documentation de Python Tools pour Visual Studio][]
  - [Projets Web][]
  - [Projets de service cloud][]
  - [Débogage distant sur Microsoft Azure][]
- [Documentation de Flask][]
- [Azure Storage][]
- [Kit de développement logiciel (SDK) Azure pour Python][]
- [Comment utiliser le service de stockage depuis Python][]


<!--Link references-->
[Centre de développement Python]: /fr-fr/develop/python/
[Azure Cloud Services]: ../cloud-services-python-ptvs/
[documentation]: ../storage-python-how-to-use-table-storage/
[Comment utiliser le service de stockage depuis Python]: ../storage-python-how-to-use-table-storage/

<!--External Link references-->
[Portail de gestion Azure]: https://manage.windowsazure.com
[Kit de développement logiciel (SDK) Azure pour .NET]: http://azure.microsoft.com/downloads/
[Python Tools 2.1 pour Visual Studio]: http://go.microsoft.com/fwlink/?LinkId=517189
[Exemples VSIX de Python Tools 2.1 pour Visual Studio]: http://go.microsoft.com/fwlink/?LinkId=517189
[Outils Azure SDK pour VS 2013]: http://go.microsoft.com/fwlink/?LinkId=323510
[Outils Azure SDK pour VS 2012]: http://go.microsoft.com/fwlink/?LinkId=323511
[Python 2.7 32 bits]: http://go.microsoft.com/fwlink/?LinkId=517190 
[Python 3.4 32 bits]: http://go.microsoft.com/fwlink/?LinkId=517191
[Documentation de Python Tools pour Visual Studio]: http://pytools.codeplex.com/documentation
[Documentation de Flask]: http://flask.pocoo.org/
[Débogage distant sur Microsoft Azure]: http://pytools.codeplex.com/wikipage?title=Features%20Azure%20Remote%20Debugging
[Projets Web]: http://pytools.codeplex.com/wikipage?title=Features%20Web%20Project
[Projets de service cloud]: http://pytools.codeplex.com/wikipage?title=Features%20Cloud%20Project
[Azure Storage]: http://azure.microsoft.com/documentation/services/storage/
[Kit de développement logiciel (SDK) Azure pour Python]: https://github.com/Azure/azure-sdk-for-python



<!--HONumber=42-->
