<properties linkid="web-sites-python-ptvs-flask-table-storage" title="Flask and Azure Table Storage on Azure with Python Tools 2.1 for Visual Studio" pageTitle="Flask et stockage des tables Azure sur Azure avec Python Tools 2.1 for Visual Studio" description="Découvrez comment utiliser les outils Python pour Visual Studio pour créer une application Flask qui stocke les données dans Azure Table Storage et peut être déployée vers un site web." metaKeywords="" services="" solutions="" documentationCenter="Python" authors="huvalo" videoId="" scriptId="" manager="wpickett" editor="" />

<tags ms.service="web-sites" ms.workload="web" ms.tgt_pltfrm="na" ms.devlang="python" ms.topic="article" ms.date="10/10/2014" ms.author="huvalo" />




# Flask et stockage des tables Azure sur Azure avec Python Tools 2.1 for Visual Studio 

Dans ce didacticiel, nous allons créer une application de sondage simple à l'aide de l'un des exemples de modèles de PTVS.

L'application de sondage définit une abstraction pour son référentiel, donc vous pouvez facilement basculer entre les différents types de référentiel (en mémoire, stockage de tables Azure, MongoDB).

Vous allez apprendre à créer un compte Azure Storage, à configurer l'application pour utiliser le stockage de tables Azure et à publier l'application sur un site web Azure.

Consultez le [Centre de développement Python][] pour découvrir d'autres articles abordant le développement des sites web Azure avec PTVS à l'aide des infrastructures web Bottle, Flask et Django, avec MongoDB, le stockage de tables Azure, MySQL et les services de la base de données SQL.  Bien que cet article se concentre sur Sites Web Azure, les étapes sont semblables lors du développement d'[Azure Cloud Services][].

+ [Configuration requise](#prerequisites)
+ [Création du projet](#create-the-project)
+ [Création d'un compte de stockage Azure](#create-an-azure-storage-account)
+ [Configuration du projet](#configure-the-project)
+ [Exploration du stockage de tables Azure](#explore-the-azure-table-storage)
+ [Publication sur un site web Azure](#publish-to-an-azure-website)
+ [Configuration du site web Azure](#configure-the-azure-website)
+ [Étapes suivantes](#next-steps)

##<a name="prerequisites"></a>Configuration requise

 - Visual Studio 2012 ou 2013
 - [PTVS 2,1][]
 - [Python Tools 2.1 pour Visual Studio (exemples VSIX)][]
 - [Outils du Kit de développement logiciel (SDK) Azure pour Visual Studio 2013][] ou [Outils du Kit de développement logiciel (SDK) Azure pour Visual Studio 2012][]
 - [Python 2.7 32 bits][] ou [Python 3.4 32 bits][]

[WACOM.INCLUDE [create-account-and-websites-note](../includes/create-account-and-websites-note.md)]

##<a name="create-the-project"></a>Création du projet

Dans cette section, nous allons créer un projet Visual Studio à l'aide d'un exemple de modèle.  Nous allons créer un environnement virtuel et installer les packages requis.  Ensuite, nous allons exécuter l'application localement à l'aide du référentiel en mémoire par défaut.

1.  Dans Visual Studio, sélectionnez **Fichier**, **Nouveau projet**.

1.  Les modèles de projet ayant l'extension VSIX des exemples de PTVS sont disponibles sous **Python**, **Exemples**.  Sélectionnez **Projet web Flask de sondage** et cliquez sur OK pour créer le projet.

  	![New Project Dialog](./media/web-sites-python-ptvs-flask-table-storage/PollsFlaskNewProject.png)

1.  Vous êtes invité à installer des packages externes.  Sélectionnez **Installer dans un environnement virtuel**.

  	![External Packages Dialog](./media/web-sites-python-ptvs-flask-table-storage/PollsFlaskExternalPackages.png)

1.  Sélectionnez **Python 2.7** ou **Python 3.4** comme interpréteur de base.

  	![Add Virtual Environment Dialog](./media/web-sites-python-ptvs-flask-table-storage/PollsCommonAddVirtualEnv.png)

1.  Vérifiez que l'application fonctionne en appuyant sur <kbd>F5</kbd>.  Par défaut, l'application utilise un référentiel en mémoire qui ne nécessite pas de configuration.  Toutes les données sont perdues si le serveur web est arrêté.

1.  Cliquez sur **Créer des exemples de sondage**, puis cliquez sur un sondage et votez.

  	![Web Browser](./media/web-sites-python-ptvs-flask-table-storage/PollsFlaskInMemoryBrowser.png)

##<a name="create-an-azure-storage-account"></a>Création d'un compte de stockage Azure

Pour utiliser les opérations de stockage, vous avez besoin d'un compte Azure Storage. Pour en créer un, procédez comme suit :

1.  Connectez-vous au [portail de gestion Azure][].

1.  En bas du volet de navigation, cliquez sur **NOUVEAU**.

  	![New Button](./media/web-sites-python-ptvs-flask-table-storage/PollsCommonAzurePlusNew.png)

1.  Cliquez sur **SERVICES DE DONNÉES**, sur **STOCKAGE**, puis sur **APERÇU RAPIDE**.

  	![Quick Create](./media/web-sites-python-ptvs-flask-table-storage/PollsCommonAzureStorageCreate.png)

1.  Dans URL, tapez un nom de sous-domaine à utiliser dans l'URI du compte de stockage.  L'entrée peut être composée de 3 à 24 lettres minuscules et chiffres. Cette valeur devient le nom d'hôte contenu dans l'URI utilisé pour adresser les ressources d'objets blob, de files d'attente et de tables pour l'abonnement.

1.  Choisissez la région/le groupe d'affinités dans lequel localiser le stockage. Si vous utilisez le stockage à partir de votre application Azure, sélectionnez la même région que celle du déploiement de votre application.

1.  Vous pouvez éventuellement activer la géo-réplication.  Avec la géo-réplication, Azure Storage conserve désormais vos données durables dans deux emplacements. Dans les deux emplacements, Azure Storage conserve constamment plusieurs réplicas sains de vos données.

1.  Cliquez sur **CRÉER UN COMPTE DE STOCKAGE**.

##<a name="configure-the-project"></a>Configuration du projet

Dans cette section, nous allons configurer notre application pour utiliser le compte de stockage que nous venons de créer.  Nous verrons comment obtenir les paramètres de connexion à partir du portail Azure.  Puis nous exécuterons l'application localement.

1.  Dans le [portail de gestion Azure][], cliquez sur le compte de stockage créé dans la section précédente.

1.  Cliquez sur **GÉRER LES CLÉS D'ACCÈS**.

  	![Manage Access Keys Dialog](./media/web-sites-python-ptvs-flask-table-storage/PollsCommonAzureTableStorageManageKeys.png)

1.  Dans Visual Studio, cliquez avec le bouton droit sur le nœud de votre projet dans l'Explorateur de solutions, puis sélectionnez **Propriétés**.  Cliquez sur l'onglet **Déboguer**.

  	![Project Debug Settings](./media/web-sites-python-ptvs-flask-table-storage/PollsFlaskAzureTableStorageProjectDebugSettings.png)

1.  Définissez les valeurs des variables d'environnement requises par l'application dans **Déboguer la commande du serveur**, **Environnement**.

        REPOSITORY_NAME=azuretablestorage
        STORAGE_NAME=<nom du compte de stockage>
        STORAGE_KEY=<clé d'accès primaire>

    Cela permet de définir les variables d'environnement pour **Démarrer le débogage**.  Si vous souhaitez que les variables soient définies pour **Exécuter sans débogage**, définissez également les même valeurs sous **Exécuter la commande du serveur**.

    Vous pouvez également définir des variables d'environnement à l'aide du Panneau de configuration Windows.  Il s'agit d'une meilleure option si vous souhaitez éviter de stocker des informations d'identification dans le code source/fichier de projet.  Notez que vous devrez redémarrer Visual Studio pour que les nouvelles valeurs d'environnement soient accessibles par l'application.

1.  Le code qui implémente le référentiel du stockage de tables Azure se situe dans **models/azuretablestorage.py**.  Consultez la [documentation] pour plus d'informations sur l'utilisation du service de Table à partir de Python.

1.  Exécutez l'application à l'aide de la touche <kbd>F5</kbd>.  Les sondages créés avec l'option **Créer des exemples de sondage** et les données soumises au vote seront sérialisés dans le stockage de tables Azure.

1.  Accédez à la page **À propos** pour vérifier que l'application utilise le référentiel **Stockage de tables Azure**.

  	![Web Browser](./media/web-sites-python-ptvs-flask-table-storage/PollsFlaskAzureTableStorageAbout.png)

##<a name="explore-the-azure-table-storage"></a>Exploration du stockage de tables Azure

Il est facile d'afficher et de modifier des tables de stockage à l'aide de l'Explorateur de serveurs dans Visual Studio.  Dans cette section, nous allons utiliser l'Explorateur de serveurs pour afficher le contenu des tables de l'application de sondage.

> [WACOM.NOTE] Pour cela, les outils Microsoft Azure doivent être installés ; ils font partie du [Kit de développement logiciel (SDK) Azure pour .NET][].

1.  Ouvrez l'**Explorateur de serveurs**.  Développez **Azure**, **Stockage**, votre compte de stockage, puis les **Tables**.

  	![Server Explorer](./media/web-sites-python-ptvs-flask-table-storage/PollsCommonServerExplorer.png)

1.  Double-cliquez sur la table **sondages** ou **choix** pour en afficher le contenu dans une fenêtre et ajouter/supprimer/modifier des entités.

  	![Table Query Results](./media/web-sites-python-ptvs-flask-table-storage/PollsCommonServerExplorerTable.png)

##<a name="publish-to-an-azure-website"></a>Publication sur un site web Azure

PTVS est un moyen facile de déployer votre application web sur un site web Azure.

1.  Dans l'**Explorateur de solutions**, cliquez avec le bouton droit sur le nœud du projet et sélectionnez **Publier**.

  	![Publish Web Dialog](./media/web-sites-python-ptvs-flask-table-storage/PollsCommonPublishWebSiteDialog.png)

1.  Cliquez sur **Sites Web Microsoft Azure**.

1.  Cliquez sur **Nouveau** pour créer un nouveau site.

1.  Sélectionnez un **Nom du site** et une **Région**, puis cliquez sur **Créer**.

  	![Create Site on Microsoft Azure Dialog](./media/web-sites-python-ptvs-flask-table-storage/PollsCommonCreateWebSite.png)

1.  Acceptez tous les autres paramètres par défaut et cliquez sur **Publier**.

1.  Votre navigateur web s'ouvre automatiquement sur le site publié.  Si vous accédez à la page À propos, vous pouvez voir qu'elle utilise le référentiel **en mémoire** et non le référentiel **Stockage de tables Azure**.

    C'est parce que les variables d'environnement ne sont pas définies dans Sites Web Azure. Elle utilise donc les valeurs par défaut spécifiées dans **settings.py**.

##<a name="configure-the-azure-website"></a>Configuration du site web Azure

Dans cette section, nous allons configurer les variables d'environnement pour le site.

1.  Dans le [portail de gestion Azure][], cliquez sur le site créé dans la section précédente.

1.  Dans le menu supérieur, cliquez sur **CONFIGURER**.

  	![Top Menu](./media/web-sites-python-ptvs-flask-table-storage/PollsCommonWebSiteTopMenu.png)

1.  Faites défiler jusqu'à la section de **paramètres d'application** et définissez les valeurs pour **REPOSITORY\_NAME**, **STORAGE\_NAME** et **STORAGE\_KEY**, comme décrit dans la section ci-dessus.

  	![App Settings](./media/web-sites-python-ptvs-flask-table-storage/PollsCommonWebSiteConfigureSettingsTableStorage.png)

1. Dans le menu inférieur, cliquez sur **ENREGISTRER**, puis sur **REDÉMARRER** et enfin sur **PARCOURIR**.

  	![Bottom Menu](./media/web-sites-python-ptvs-flask-table-storage/PollsCommonWebSiteConfigureBottomMenu.png)

1.  L'application doit fonctionner tel que prévu, en utilisant le référentiel **Stockage de tables Azure**.

    Félicitations !

  	![Web Browser](./media/web-sites-python-ptvs-flask-table-storage/PollsFlaskAzureBrowser.png)

##<a name="next-steps"></a>Étapes suivantes

Suivez ces liens pour en savoir plus sur Python Tools for Visual Studio, Flask et le stockage de tables Azure.

- [Documentation relative à Python Tools for Visual Studio][]
  - [Projets web][]
  - [Projets de service cloud][]
  - [Débogage distant sur Microsoft Azure][]
- [Documentation relative à Flask][]
- [Azure Storage][]
- [Kit de développement logiciel (SDK) Azure pour Python][]
- [Utilisation du service de stockage de tables à partir de Python][]


<!--Link references-->
[Centre de développement Python]: /fr-fr/develop/python/
[Services cloud Azure]: ../cloud-services-python-ptvs/
[documentation]: ../storage-python-how-to-use-table-storage/
[Utilisation du service de stockage de tables à partir de Python]: ../storage-python-how-to-use-table-storage/

<!--External Link references-->
[Portail de gestion Azure]: https://manage.windowsazure.com
[Kit de développement logiciel (SDK) Azure pour .NET]: http://azure.microsoft.com/fr-fr/downloads/
[PTVS 2,1]: http://go.microsoft.com/fwlink/?LinkId=517189
[Python Tools 2.1 pour Visual Studio (exemples VSIX)]: http://go.microsoft.com/fwlink/?LinkId=517189
[Outils du Kit de développement logiciel (SDK) Azure pour Visual Studio 2013]: http://go.microsoft.com/fwlink/?LinkId=323510
[Outils du Kit de développement logiciel (SDK) Azure pour Visual Studio 2012]: http://go.microsoft.com/fwlink/?LinkId=323511
[Python 2.7 (32 bits)]: http://go.microsoft.com/fwlink/?LinkId=517190 
[Python 3.4 (32 bits)]: http://go.microsoft.com/fwlink/?LinkId=517191
[Documentation relative à Python Tools for Visual Studio]: http://pytools.codeplex.com/documentation
[Documentation relative à Flask]: http://flask.pocoo.org/
[Débogage distant sur Microsoft Azure]: http://pytools.codeplex.com/wikipage?title=Features%20Azure%20Remote%20Debugging
[Projets web]: http://pytools.codeplex.com/wikipage?title=Features%20Web%20Project
[Projets de service cloud]: http://pytools.codeplex.com/wikipage?title=Features%20Cloud%20Project
[Azure Storage]: http://azure.microsoft.com/fr-fr/documentation/services/storage/
[Kit de développement logiciel (SDK) Azure pour Python]: https://github.com/Azure/azure-sdk-for-python
