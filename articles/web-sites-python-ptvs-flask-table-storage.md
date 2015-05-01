<properties 
	pageTitle="Flask et stockage des tables Azure sur Azure avec Python Tools 2.1 for Visual Studio" 
	description="Découvrez comment utiliser les outils Python pour Visual Studio pour créer une application Flask qui stocke les données dans Azure Table Storage et peut être déployée vers un site web." 
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




# Flask et stockage des tables Azure sur Azure avec Python Tools 2.1 for Visual Studio 

Dans ce didacticiel, nous allons utiliser [Python Tools pour Visual Studio][] afin de créer une application de sondage simple à l'aide de l'un des exemples de modèles PTVS. Ce didacticiel est également disponible sous forme de [vidéo](https://www.youtube.com/watch?v=qUtZWtPwbTk).

L'application de sondage définit une abstraction pour son référentiel, ce qui vous permet de basculer facilement d'un type de référentiel à un autre (In-Memory, stockage de tables Azure, MongoDB).

Nous allons découvrir comment créer un compte Azure Storage, comment configurer l'application pour utiliser le stockage de tables Azure et comment publier l'application sur un site web Azure.

Visitez le [Centre de développement Python][] pour consulter d'autres articles sur le développement de site web Azure avec PTVS à l'aide des infrastructures web Bottle, Flask et Django, ainsi que de MongoDB, du stockage de tables Azure, de MySQL et des services de base de données SQL.  Quand bien même cet article se concentre Sites Web Azure, les étapes sont similaires pour développer [Azure Cloud Services][].

## Conditions préalables

 - Visual Studio 2012 ou 2013
 - [Python Tools 2.1 pour Visual Studio (en anglais)][]
 - [Exemples VSIX de Python Tools 2.1 pour Visual Studio][]
 - [Outils du Kit de développement logiciel (SDK) Azure pour Visual Studio 2013][] ou [Outils du Kit de développement logiciel (SDK) Azure pour Visual Studio 2012][]
 - [Python 2.7 32 bits][] ou [Python 3.4 32 bits][]

[AZURE.INCLUDE [create-account-and-websites-note](../includes/create-account-and-websites-note.md)]

## Création du projet

Dans cette section, nous allons créer un projet Visual Studio à l'aide d'un exemple de modèle.  Nous allons créer un environnement virtuel et installer les packages requis.  Ensuite, nous allons exécuter l'application localement à l'aide du référentiel In-Memory par défaut.

1.  Dans Visual Studio, sélectionnez **Fichier**, **Nouveau projet**.

1.  Les modèles de projet issus des exemples VSIX de PTVS sont disponibles sous **Python**, **Exemples**.  Sélectionnez **Projet web de sondage Flask** et cliquez sur OK pour créer le projet.

  	![Boîte de dialogue Nouveau projet](./media/web-sites-python-ptvs-flask-table-storage/PollsFlaskNewProject.png)

1.  Vous allez être invité à installer des packages externes.  Sélectionnez **Installer dans un environnement virtuel**.

  	![Boîte de dialogue Packages externes](./media/web-sites-python-ptvs-flask-table-storage/PollsFlaskExternalPackages.png)

1.  Sélectionnez **Python 2.7** ou **Python 3.4** en tant qu'interpréteur de base.

  	![Boîte de dialogue Ajouter un environnement virtuel](./media/web-sites-python-ptvs-flask-table-storage/PollsCommonAddVirtualEnv.png)

1.  Vérifiez que l'application fonctionne en appuyant sur <kbd>F5</kbd>.  Par défaut, l'application utilise un référentiel In-Memory qui ne requiert aucune configuration.  Toutes les données sont perdues à l'arrêt du serveur web.

1.  Cliquez sur **Créer un exemple de sondage**, puis sur un sondage et vote.

  	![Navigateur web](./media/web-sites-python-ptvs-flask-table-storage/PollsFlaskInMemoryBrowser.png)

## Création d'un compte de stockage Azure

Pour utiliser les opérations de stockage, vous avez besoin d'un compte Azure Storage. Pour en créer un, procédez comme suit :

1.  Connectez-vous au [portail de gestion Azure][].

1.  En bas du volet de navigation, cliquez sur **NOUVEAU**.

  	![Bouton Nouveau](./media/web-sites-python-ptvs-flask-table-storage/PollsCommonAzurePlusNew.png)

1.  Cliquez sur **SERVICES DE DONNÉES**, sur **STOCKAGE**, puis sur **CRÉATION RAPIDE**.

  	![Création rapide](./media/web-sites-python-ptvs-flask-table-storage/PollsCommonAzureStorageCreate.png)

1.  Dans URL, tapez un nom de sous-domaine à utiliser dans l'URI du compte de stockage.  L'entrée peut être composée de 3 à 24 lettres minuscules et chiffres. Cette valeur devient le nom d'hôte contenu dans l'URI utilisé pour adresser les ressources d'objets blob, de files d'attente et de tables pour l'abonnement.

1.  Choisissez la région ou le groupe d'affinités dans lequel localiser le stockage. Si vous utilisez le stockage à partir de votre application Azure, sélectionnez la même région que celle du déploiement de votre application.

1.  Vous pouvez éventuellement activer les géo-réplications.  Avec les géo-réplications, le stockage de tables Azure pérennise maintenant vos données à deux emplacements. À ces deux emplacements, le stockage de tables Azure tient constamment à jour plusieurs réplicas intègres de vos données.

1.  Cliquez sur **CRÉER UN COMPTE DE STOCKAGE**.

## Configurer le projet

Dans cette section, nous allons configurer notre application pour utiliser le compte de stockage que nous venons de créer.  Nous allons découvrir comment obtenir des paramètres de connexion à partir du portail Azure.  Ensuite, nous allons exécuter l'application localement.

1.  Sur le [portail de gestion Azure][], cliquez sur le compte de stockage créé dans la section précédente.

1.  Cliquez sur **GÉRER LES CLÉS D'ACCÈS**.

  	![Boîte de dialogue Gérer les clés d'accès](./media/web-sites-python-ptvs-flask-table-storage/PollsCommonAzureTableStorageManageKeys.png)

1.  Dans Visual Studio, cliquez avec le bouton droit sur le nœud de votre projet dans l'Explorateur de solutions, puis sélectionnez **Propriétés**.  Cliquez sur l'onglet **Déboguer**.

  	![Paramètres de débogage du projet](./media/web-sites-python-ptvs-flask-table-storage/PollsFlaskAzureTableStorageProjectDebugSettings.png)

1.  Définissez les valeurs des variables d'environnement requises par l'application dans **Déboguer la commande de serveur**, **Environnement**.

        REPOSITORY_NAME=azuretablestorage
        STORAGE_NAME=<storage account name>
        STORAGE_KEY=<primary access key>

    Ainsi, les variables d'environnement seront définies quand vous **démarrerez le débogage**.  Si vous voulez que les variables soient définies quand vous **démarrez sans débogage**, définissez aussi les mêmes valeurs sous **Exécuter la commande de serveur**.

    Vous pouvez aussi définir des variables d'environnement à l'aide du Panneau de configuration Windows.  Cette option est la plus appropriée pour éviter de stocker des informations d'identification dans le code source/fichier de projet.  Notez que vous devrez redémarrer Visual Studio pour que les nouvelles variables d'environnement soient disponibles pour l'application.

1.  Le code qui implémente le référentiel du stockage de tables Azure se trouve dans **models/azuretablestorage.py**.  Consultez la [documentation] pour plus d'informations sur la manière d'utiliser le service de table de Python.

1.  Exécutez l'application avec <kbd>F5</kbd>.  Les sondages créés à l'aide de la fonction **Créer un exemple de sondage** et les données soumises par vote sont sérialisés dans le stockage de tables Azure.

1.  Accédez à la page **À propos de** pour vérifier que l'application utilise le référentiel **Stockage de tables Azure**.

  	![Navigateur web](./media/web-sites-python-ptvs-flask-table-storage/PollsFlaskAzureTableStorageAbout.png)

## Exploration du stockage de tables Azure

L'affichage et la modification des tables de stockage sont très faciles dans Visual Studio grâce à l'Explorateur de serveurs.  Dans cette section, nous allons utiliser l'Explorateur de serveurs pour afficher le contenu des tables de l'application de sondage.

> [AZURE.NOTE] Pour cela, les outils Microsoft Azure doivent être installés. Ils sont disponibles dans le [Kit de développement logiciel (SDK) Azure pour .NET][].

1.  Ouvrez l'**Explorateur de serveurs**.  Développez **Azure**, **Stockage**, votre compte de stockage, puis **Tables**.

  	![Explorateur de serveurs](./media/web-sites-python-ptvs-flask-table-storage/PollsCommonServerExplorer.png)

1.  Double-cliquez sur la table **sondages** ou **choix** pour en afficher le contenu dans une fenêtre et ajouter/supprimer/modifier des entités.

  	![Résultats de la requête de table](./media/web-sites-python-ptvs-flask-table-storage/PollsCommonServerExplorerTable.png)

## Publication sur un site web Azure

PTVS propose un moyen simple de déployer votre application web sur un site web Azure.

1.  Dans l'**Explorateur de solutions**, cliquez avec le bouton droit sur le nœud du projet et sélectionnez **Publier**.

  	![Boîte de dialogue Publier le site Web](./media/web-sites-python-ptvs-flask-table-storage/PollsCommonPublishWebSiteDialog.png)

1.  Cliquez sur **Sites Web Microsoft Azure**.

1.  Cliquez sur **Nouveau** pour créer un site.

1.  Sélectionnez un **nom de site** et une **région**, puis cliquez sur **Créer**.

  	![Boîte de dialogue Créer un site sur Microsoft Azure](./media/web-sites-python-ptvs-flask-table-storage/PollsCommonCreateWebSite.png)

1.  Acceptez toutes les valeurs par défaut et cliquez sur **Publier**.

1.  Votre navigateur web ouvre automatiquement le site publié.  Si vous accédez à la page À propos, vous pouvez voir qu'elle utilise le référentiel **en mémoire** et non le référentiel **Stockage de tables Azure**.

    La raison en est que les variables d'environnement ne sont pas définies sur le site web Azure, donc les valeurs par défaut spécifiées dans **settings.py** sont utilisées.

## Configuration du site web Azure

Dans cette section, vous allez configurer des variables d'environnement pour le site.

1.  Sur le [portail de gestion Azure][], cliquez sur le site créé dans la section précédente.

1.  Dans le menu supérieur, cliquez sur **CONFIGURER**.

  	![Menu supérieur](./media/web-sites-python-ptvs-flask-table-storage/PollsCommonWebSiteTopMenu.png)

1.  Accédez à la section **Paramètres de l'application** et définissez les valeurs **REPOSITORY\_NAME**, **STORAGE\_NAME** et **STORAGE\_KEY** comme décrit dans la section ci-dessus.

  	![Paramètres de l'application](./media/web-sites-python-ptvs-flask-table-storage/PollsCommonWebSiteConfigureSettingsTableStorage.png)

1. Dans le menu inférieur, cliquez sur **ENREGISTRER**, puis **REDÉMARRER** et enfin **PARCOURIR**.

  	![Menu inférieur](./media/web-sites-python-ptvs-flask-table-storage/PollsCommonWebSiteConfigureBottomMenu.png)

1.  L'application doit fonctionner comme prévu et utiliser le référentiel **Stockage de tables Azure**.

    Félicitations !

  	![Navigateur web](./media/web-sites-python-ptvs-flask-table-storage/PollsFlaskAzureBrowser.png)

## Étapes suivantes

Suivez ces liens pour en savoir plus sur Python Tools for Visual Studio, Flask et le stockage de tables Azure.

- [Documentation de Python Tools pour Visual Studio (en anglais)][]
  - [Projets web][]
  - [Projets de Service cloud][]
  - [Débogage distant sur Microsoft Azure][]
- [Documentation relative à Flask][]
- [Stockage Azure][]
- [Kit de développement logiciel (SDK) Azure pour Python][]
- [Utilisation du service de stockage de tables à partir de Python][]


<!--Link references-->
[Centre de développement Python]: /develop/python/
[Azure Cloud Services]: cloud-services-python-ptvs.md
[documentation]: storage-python-how-to-use-table-storage.md
[Utilisation du service de stockage de tables à partir de Python]: storage-python-how-to-use-table-storage.md

<!--External Link references-->
[Portail de gestion Azure]: https://manage.windowsazure.com
[Kit de développement logiciel (SDK) Azure pour .NET]: http://azure.microsoft.com/downloads/
[Python Tools pour Visual Studio]: http://aka.ms/ptvs
[Python Tools 2.1 pour Visual Studio (en anglais)]: http://go.microsoft.com/fwlink/?LinkId=517189
[Exemples VSIX de Python Tools 2.1 pour Visual Studio]: http://go.microsoft.com/fwlink/?LinkId=517189
[Outils du Kit de développement logiciel (SDK) Azure pour Visual Studio 2013]: http://go.microsoft.com/fwlink/?LinkId=323510
[Outils du Kit de développement logiciel (SDK) Azure pour Visual Studio 2012]: http://go.microsoft.com/fwlink/?LinkId=323511
[Python 2.7 32 bits]: http://go.microsoft.com/fwlink/?LinkId=517190 
[Python 3.4 32 bits]: http://go.microsoft.com/fwlink/?LinkId=517191
[Documentation de Python Tools pour Visual Studio (en anglais)]: http://pytools.codeplex.com/documentation
[Documentation relative à Flask]: http://flask.pocoo.org/
[Débogage distant sur Microsoft Azure]: http://pytools.codeplex.com/wikipage?title=Features%20Azure%20Remote%20Debugging
[Projets web]: http://pytools.codeplex.com/wikipage?title=Features%20Web%20Project
[Projets de Service cloud]: http://pytools.codeplex.com/wikipage?title=Features%20Cloud%20Project
[Stockage Azure]: http://azure.microsoft.com/documentation/services/storage/
[Kit de développement logiciel (SDK) Azure pour Python]: https://github.com/Azure/azure-sdk-for-python


<!--HONumber=52-->