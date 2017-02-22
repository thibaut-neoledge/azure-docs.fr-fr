---
title: "Flask et stockage des tables Azure sur Azure avec Python Tools 2.2 pour Visual Studio"
description: "Découvrez comment utiliser Python Tools pour Visual Studio afin de créer une application Flask qui stocke les données dans le stockage de tables Azure et déployer l’application web dans Azure App Service Web Apps."
services: app-service\web
tags: python
documentationcenter: python
author: huguesv
manager: wpickett
editor: 
ms.assetid: d8e70a29-aca1-4010-95f5-cfe769e3be06
ms.service: app-service-web
ms.workload: web
ms.tgt_pltfrm: na
ms.devlang: python
ms.topic: article
ms.date: 07/07/2016
ms.author: huvalo
translationtype: Human Translation
ms.sourcegitcommit: b1a633a86bd1b5997d5cbf66b16ec351f1043901
ms.openlocfilehash: 96b27bdd528b0f57b9e0a9959ead23b8f820a82d


---
# <a name="flask-and-azure-table-storage-on-azure-with-python-tools-22-for-visual-studio"></a>Flask et stockage des tables Azure sur Azure avec Python Tools 2.2 pour Visual Studio
Dans ce didacticiel, nous allons utiliser [Python Tools pour Visual Studio] afin de créer une application web de sondage simple, à l’aide de l’un des exemples de modèle PTVS. Ce didacticiel est également disponible en [vidéo](https://www.youtube.com/watch?v=qUtZWtPwbTk).

L’application web de sondage définit une abstraction pour son référentiel, ce qui vous permet de basculer facilement d’un type de référentiel à un autre (In-Memory, Azure Table Storage, MongoDB).

Nous allons découvrir comment créer un compte Azure Storage, comment configurer l’application web pour utiliser le stockage de tables Azure et comment publier l’application web sur un [Azure App Service Web Apps](http://go.microsoft.com/fwlink/?LinkId=529714).

Visitez le [Centre de développement Python] pour consulter d’autres articles sur le développement d’applications Azure App Service Web Apps avec PTVS à l’aide des infrastructures web Bottle, Flask et Django, ainsi que des services MongoDB, Azure Table Storage, MySQL et Base de données SQL. Cet article concerne App Service, mais les étapes sont similaires lorsque vous développez pour [Azure Cloud Services].

## <a name="prerequisites"></a>Composants requis
* Visual Studio 2015
* [Python Tools 2.2 pour Visual Studio]
* [Python Tools 2.2 pour Visual Studio Samples VSIX]
* [Outils du Kit de développement logiciel (SDK) Azure pour Visual Studio 2015]
* [Python 2.7 32 bits] ou [Python 3.4 32 bits]

[!INCLUDE [create-account-and-websites-note](../../includes/create-account-and-websites-note.md)]

> [!NOTE]
> Si vous voulez vous familiariser avec Azure App Service avant d’ouvrir un compte Azure, accédez à la page [Essayer App Service](https://azure.microsoft.com/try/app-service/), où vous pourrez créer immédiatement une application web temporaire dans App Service. Aucune carte de crédit n’est requise ; vous ne prenez aucun engagement.
> 
> 

## <a name="create-the-project"></a>Création du projet
Dans cette section, nous allons créer un projet Visual Studio à l’aide d’un exemple de modèle. Nous allons créer un environnement virtuel et installer les packages requis. Ensuite, nous allons exécuter l’application localement à l’aide du référentiel In-Memory par défaut.

1. Dans Visual Studio, sélectionnez **Fichier**, **Nouveau projet**.
2. Les modèles de projet issus de [Python Tools 2.2 pour Visual Studio Samples VSIX] sont disponibles sous **Python**, **Exemples**. Sélectionnez **Projet web de sondage Flask** et cliquez sur OK pour créer le projet.
   
     ![Boîte de dialogue Nouveau projet](./media/web-sites-python-ptvs-flask-table-storage/PollsFlaskNewProject.png)
3. Vous allez être invité à installer des packages externes. Sélectionnez **Installer dans un environnement virtuel**.
   
     ![Boîte de dialogue Packages externes](./media/web-sites-python-ptvs-flask-table-storage/PollsFlaskExternalPackages.png)
4. Sélectionnez **Python 2.7** ou **Python 3.4** comme interpréteur de base.
   
     ![Boîte de dialogue Ajouter un environnement virtuel](./media/web-sites-python-ptvs-flask-table-storage/PollsCommonAddVirtualEnv.png)
5. Vérifiez que l’application fonctionne en appuyant sur `F5`. Par défaut, l’application utilise un référentiel In-Memory qui ne requiert aucune configuration. Toutes les données sont perdues à l’arrêt du serveur web.
6. Cliquez sur **Créer un exemple de sondage**, puis sur un sondage et un vote.
   
     ![Navigateur web](./media/web-sites-python-ptvs-flask-table-storage/PollsFlaskInMemoryBrowser.png)

## <a name="create-an-azure-storage-account"></a>Création d’un compte de stockage Azure
Pour utiliser les opérations de stockage, vous avez besoin d’un compte Azure Storage. Pour en créer un, procédez comme suit :

1. Connectez-vous au [portail Azure](https://portal.azure.com/).
2. Cliquez sur l’icône **Nouveau** en haut à gauche du portail, puis cliquez sur **Données + stockage** > **Compte de stockage**. Cliquez sur **Créer**, puis attribuez un nom unique au compte de stockage et créez un [groupe de ressources](../azure-resource-manager/resource-group-overview.md).
   
      ![Création rapide](./media/web-sites-python-ptvs-flask-table-storage/PollsCommonAzureStorageCreate.png)
   
    Une fois le compte de stockage créé, le bouton **Notifications** affiche la mention **RÉUSSITE** en vert clignotant et le panneau du compte de stockage s’ouvre pour indiquer qu’il appartient au groupe de ressources créé.
3. Cliquez sur la section **Clés d’accès** dans le panneau du compte de stockage. Prenez note du nom du compte et de la clé1.
   
      ![de clés symétriques](./media/web-sites-python-ptvs-flask-table-storage/PollsCommonAzureStorageKeys.png)
   
    Nous aurons besoin de ces informations pour configurer votre projet dans la section suivante.

## <a name="configure-the-project"></a>Configurer le projet
Dans cette section, nous allons configurer notre application pour utiliser le compte de stockage que nous venons de créer. Nous allons découvrir comment obtenir des paramètres de connexion à partir du portail Azure. Ensuite, nous allons exécuter l’application localement.

1. Dans Visual Studio, cliquez avec le bouton droit sur le nœud de votre projet dans l’Explorateur de solutions, puis sélectionnez **Propriétés**. Cliquez sur l’onglet **Déboguer** .
   
     ![Paramètres de débogage du projet](./media/web-sites-python-ptvs-flask-table-storage/PollsFlaskAzureTableStorageProjectDebugSettings.png)
2. Définissez les valeurs des variables d’environnement requises par l’application dans **Déboguer la commande de serveur**, **Environnement**.
   
       REPOSITORY_NAME=azuretablestorage
       STORAGE_NAME=<storage account name>
       STORAGE_KEY=<primary access key>
   
   Les variables d’environnement sont définies lorsque la commande **Démarrer le débogage**est exécutée. Si vous voulez que les variables soient définies lorsque la commande **Démarrer sans débogage** est exécutée, indiquez les mêmes valeurs sous **Exécuter la commande de serveur**.
   
   Vous pouvez aussi définir des variables d’environnement à l’aide du Panneau de configuration Windows. Cette option est la plus appropriée pour éviter de stocker des informations d’identification dans le code source/fichier de projet. Notez que vous devrez redémarrer Visual Studio pour que les nouvelles variables d’environnement soient disponibles pour l’application.
3. Le code qui implémente le référentiel du stockage de tables Azure se trouve dans **models/azuretablestorage.py**. Consultez la [documentation] pour plus d’informations sur la manière d’utiliser le service de Table de Python.
4. Exécutez l’application avec `F5`. Les sondages créés à l’aide de la fonction **Créer un exemple de sondage** et les données soumises par vote sont sérialisés dans le stockage de tables Azure.
   
   > [!NOTE]
   > L'environnement virtuel Python 2.7 peut provoquer un arrêt exceptionnel dans Visual Studio.  Appuyez sur `F5` pour continuer à charger le projet web.
   > 
   > 
5. Accédez à la page **À propos** pour vérifier que l’application utilise le référentiel **Stockage de tables Azure**.
   
     ![Navigateur web](./media/web-sites-python-ptvs-flask-table-storage/PollsFlaskAzureTableStorageAbout.png)

## <a name="explore-the-azure-table-storage"></a>Exploration du stockage de tables Azure
L’affichage et la modification des tables de stockage sont très faciles dans Visual Studio grâce à Cloud Explorer. Dans cette section, nous allons utiliser l’Explorateur de serveurs pour afficher le contenu des tables de l’application de sondage.

> [!NOTE]
> Pour cela, les outils Microsoft Azure doivent être installés. Ils sont disponibles dans le [Kit de développement logiciel (SDK) Azure pour .NET].
> 
> 

1. Ouvrez **Cloud Explorer**. Développez **Comptes de stockage**, votre compte de stockage, puis **Tables**.
   
     ![Cloud Explorer](./media/web-sites-python-ptvs-bottle-table-storage/PollsCommonServerExplorer.png)
2. Double-cliquez sur la table **sondages** ou **choix** pour en afficher le contenu dans une fenêtre et ajouter/supprimer/modifier des entités.
   
     ![Résultats de la requête de table](./media/web-sites-python-ptvs-bottle-table-storage/PollsCommonServerExplorerTable.png)

## <a name="publish-the-web-app-to-azure-app-service"></a>Publication de l’application web dans Azure App Service
Le kit de développement logiciel (SDK) Azure .NET offre un moyen simple de déployer votre application web dans Azure App Service.

1. Dans l’**Explorateur de solutions**, cliquez avec le bouton droit sur le nœud du projet et sélectionnez **Publier**.
   
     ![Boîte de dialogue Publier le site Web](./media/web-sites-python-ptvs-bottle-table-storage/PollsCommonPublishWebSiteDialog.png)
2. Cliquez sur **Microsoft Azure Web Apps**.
3. Cliquez sur **Nouveau** pour créer une application web.
4. Renseignez les champs suivants et cliquez sur **Créer**.
   
   * **Nom de l’application web**
   * **Plan App Service**
   * **Groupe de ressources**
   * **Région**
   * Dans **Serveur de base de données**, conservez **Aucune base de données**
5. Acceptez toutes les valeurs par défaut et cliquez sur **Publier**.
6. Votre navigateur web ouvre automatiquement l’application web publiée. Si vous accédez à la page À propos, vous pouvez voir qu’elle utilise le référentiel **en mémoire** et non le référentiel **Stockage de tables Azure**.
   
   En effet, comme les variables d’environnement ne sont pas définies dans l’instance Web Apps d’Azure App Service, l’application utilise les valeurs par défaut spécifiées dans **settings.py**.

## <a name="configure-the-web-apps-instance"></a>Configurer l'instance Web Apps
Dans cette section, nous allons configurer les variables d’environnement de l’instance Web Apps.

1. Dans le [Portail Azure](https://portal.azure.com), ouvrez le panneau de l'application web en cliquant sur **Parcourir** > **App Services** > le nom de votre application web.
2. Dans le volet de votre application web, cliquez sur **Tous les paramètres**, puis cliquez sur **Paramètres de l'application**.
3. Accédez à la section **Paramètres de l’application** et définissez les valeurs **REPOSITORY\_NAME**, **STORAGE\_NAME** et **STORAGE\_KEY** comme décrit dans la section **Configurer le projet** ci-dessus.
   
     ![Paramètres de l’application](./media/web-sites-python-ptvs-bottle-table-storage/PollsCommonWebSiteConfigureSettingsTableStorage.png)
4. Cliquez sur **Save**(Enregistrer). Une fois que vous avez reçu les notifications confirmant que les modifications ont été appliquées, cliquez sur **Parcourir** à partir du panneau principal de l’application Web.
5. L’application doit fonctionner comme prévu et utiliser le référentiel **Stockage de tables Azure** .
   
   Félicitations !
   
     ![Navigateur web](./media/web-sites-python-ptvs-flask-table-storage/PollsFlaskAzureBrowser.png)

## <a name="next-steps"></a>Étapes suivantes
Suivez ces liens pour en savoir plus sur Python Tools for Visual Studio, Flask et le stockage de tables Azure.

* [Documentation relative à Python Tools for Visual Studio]
  * [Projets web]
  * [Projets de service cloud]
  * [Débogage à distance sur Microsoft Azure]
* [Documentation relative à Flask]
* [Azure Storage]
* [Kit de développement logiciel (SDK) Azure pour Python]
* [Utilisation du service de stockage de tables de Python]

## <a name="whats-changed"></a>Changements apportés
* Pour obtenir un guide présentant les modifications apportées dans le cadre de la transition entre Sites Web et App Service, consultez la page [Azure App Service et les services Azure existants](http://go.microsoft.com/fwlink/?LinkId=529714)

<!--Link references-->
[Centre de développement Python]: /develop/python/
[Azure Cloud Services]: ../cloud-services/cloud-services-python-ptvs.md
[documentation]: ../storage/storage-python-how-to-use-table-storage.md
[Utilisation du service de stockage de tables de Python]: ../storage/storage-python-how-to-use-table-storage.md

<!--External Link references-->
[Azure Portal]: https://portal.azure.com
[Kit de développement logiciel (SDK) Azure pour .NET]: http://azure.microsoft.com/downloads/
[Python Tools pour Visual Studio]: http://aka.ms/ptvs
[Python Tools 2.2 pour Visual Studio]: http://go.microsoft.com/fwlink/?LinkID=624025
[Python Tools 2.2 pour Visual Studio Samples VSIX]: http://go.microsoft.com/fwlink/?LinkID=624025
[Outils du Kit de développement logiciel (SDK) Azure pour Visual Studio 2015]: http://go.microsoft.com/fwlink/?linkid=518003
[Python 2.7 32 bits]: http://go.microsoft.com/fwlink/?LinkId=517190
[Python 3.4 32 bits]: http://go.microsoft.com/fwlink/?LinkId=517191
[Documentation relative à Python Tools for Visual Studio]: http://aka.ms/ptvsdocs
[Documentation relative à Flask]: http://flask.pocoo.org/
[Débogage à distance sur Microsoft Azure]: http://go.microsoft.com/fwlink/?LinkId=624026
[Projets web]: http://go.microsoft.com/fwlink/?LinkId=624027
[Projets de service cloud]: http://go.microsoft.com/fwlink/?LinkId=624028
[Azure Storage]: http://azure.microsoft.com/documentation/services/storage/
[Kit de développement logiciel (SDK) Azure pour Python]: https://github.com/Azure/azure-sdk-for-python



<!--HONumber=Jan17_HO3-->


