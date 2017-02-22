---
title: "Django et SQL Database sur Azure avec Python Tools 2.2 pour Visual Studio"
description: "Découvrez comment utiliser Python Tools pour Visual Studio afin de créer une application web Django qui stocke les données dans une instance de base de données SQL et de la déployer dans Azure App Service Web Apps."
services: app-service\web
tags: python
documentationcenter: python
author: huguesv
manager: wpickett
editor: 
ms.assetid: 3a677e64-b5a9-4d43-b9c0-66246368b483
ms.service: app-service-web
ms.workload: web
ms.tgt_pltfrm: na
ms.devlang: python
ms.topic: article
ms.date: 07/07/2016
ms.author: huvalo
translationtype: Human Translation
ms.sourcegitcommit: b1a633a86bd1b5997d5cbf66b16ec351f1043901
ms.openlocfilehash: c5e01ef322f72eb5a704fd2273194315bf1a9f6f


---
# <a name="django-and-sql-database-on-azure-with-python-tools-22-for-visual-studio"></a>Django et SQL Database sur Azure avec Python Tools 2.2 pour Visual Studio
Dans ce didacticiel, nous allons utiliser [Python Tools pour Visual Studio] afin de créer une application web de sondage simple, à l’aide de l’un des exemples de modèle PTVS. Ce didacticiel est également disponible en [vidéo](https://www.youtube.com/watch?v=ZwcoGcIeHF4).

Nous allons découvrir comment utiliser une base de données SQL hébergée sur Azure, comment configurer l’application web pour utiliser une base de données SQL et comment publier l’application web sur [Azure App Service Web Apps](http://go.microsoft.com/fwlink/?LinkId=529714).

Visitez le [Centre de développement Python] pour consulter d’autres articles sur le développement d’applications Azure App Service Web Apps avec PTVS à l’aide des infrastructures web Bottle, Flask et Django, ainsi que des services Azure Table Storage, MySQL et Base de données SQL. Cet article concerne App Service, mais les étapes sont similaires lorsque vous développez pour [Azure Cloud Services].

## <a name="prerequisites"></a>Composants requis
* Visual Studio 2015
* [Python 2.7 32 bits]
* [Python Tools 2.2 pour Visual Studio]
* [Python Tools 2.2 pour Visual Studio Samples VSIX]
* [Outils du Kit de développement logiciel (SDK) Azure pour Visual Studio 2015]
* Django 1.9 ou version ultérieure

[!INCLUDE [create-account-and-websites-note](../../includes/create-account-and-websites-note.md)]

> [!NOTE]
> Si vous voulez vous familiariser avec Azure App Service avant d’ouvrir un compte Azure, accédez à la page [Essayer App Service](https://azure.microsoft.com/try/app-service/), où vous pourrez créer immédiatement une application web temporaire dans App Service. Aucune carte de crédit n’est requise ; vous ne prenez aucun engagement.
>
>

## <a name="create-the-project"></a>Création du projet
Dans cette section, nous allons créer un projet Visual Studio à l’aide d’un exemple de modèle. Nous allons créer un environnement virtuel et installer les packages requis. Nous allons créer une base de données locale à l’aide de sqlite. Nous exécuterons ensuite l’application web en local.

1. Dans Visual Studio, sélectionnez **Fichier**, **Nouveau projet**.
2. Les modèles de projet issus de [Python Tools 2.2 pour Visual Studio Samples VSIX] sont disponibles sous **Python**, **Exemples**. Sélectionnez **Polls Django Web Project** et cliquez sur OK pour créer le projet.

     ![Boîte de dialogue Nouveau projet](./media/web-sites-python-ptvs-django-sql/PollsDjangoNewProject.png)
3. Vous allez être invité à installer des packages externes. Sélectionnez **Installer dans un environnement virtuel**.

     ![Boîte de dialogue Packages externes](./media/web-sites-python-ptvs-django-sql/PollsDjangoExternalPackages.png)
4. Sélectionnez **Python 2.7** comme interpréteur de base.

     ![Boîte de dialogue Ajouter un environnement virtuel](./media/web-sites-python-ptvs-django-sql/PollsCommonAddVirtualEnv.png)
5. Dans **l’Explorateur de solutions**, cliquez avec le bouton droit sur le nœud du projet, sélectionnez **Python**, puis **Django Migrate (Migration de Django)**.  Sélectionnez ensuite **Django Create Superuser (Créer un superutilisateur Django)**.
6. Une console de gestion Django s’ouvre, et une base de données sqlite est créée dans le dossier du projet. Suivez les invites pour créer un utilisateur.
7. Vérifiez que l’application fonctionne en appuyant sur <kbd>F5</kbd>.
8. Cliquez sur **Log in** dans la barre de navigation en haut.

     ![Navigateur web](./media/web-sites-python-ptvs-django-sql/PollsDjangoCommonBrowserLocalMenu.png)
9. Saisissez les informations d’identification de l’utilisateur que vous avez créé quand vous avez synchronisé la base de données.

     ![Navigateur web](./media/web-sites-python-ptvs-django-sql/PollsDjangoCommonBrowserLocalLogin.png)
10. Cliquez sur **Create Sample Polls**.

      ![Navigateur web](./media/web-sites-python-ptvs-django-sql/PollsDjangoCommonBrowserNoPolls.png)
11. Cliquez sur un sondage et votez.

      ![Navigateur web](./media/web-sites-python-ptvs-django-sql/PollsDjangoSqliteBrowser.png)

## <a name="create-a-sql-database"></a>Création d’une base de données SQL
Pour la base de données, nous allons créer une base de données SQL Azure.

Pour cela, procédez comme suit :

1. Connectez-vous au [portail Azure].
2. En bas du volet de navigation, cliquez sur **NOUVEAU**. Ensuite, cliquez sur **Données + stockage** > **Base de données SQL**.
3. Configurez la nouvelle base de données SQL en créant un nouveau groupe de ressources, puis sélectionnez l’emplacement approprié pour celui-ci.
4. Une fois la base de données SQL créée, cliquez sur **Ouvrir dans Visual Studio** dans le panneau de la base de données.
5. Cliquez sur **Configurer votre pare-feu**.
6. Dans le panneau **Paramètres de pare-feu**, ajoutez une règle de pare-feu avec les champs **IP de début** et **IP de fin** définis sur l’adresse IP publique de votre ordinateur de développement. Cliquez sur **Save**.

   Les connexions au serveur de base de données de votre ordinateur de développement sont alors autorisées.
7. De retour sur le panneau de la base de données, cliquez sur **Propriétés**, puis sur **Afficher les chaînes de connexion de la base de données**.
8. Vous pouvez utiliser le bouton de copie pour placer la valeur de **ADO.NET** dans le Presse-papiers.

## <a name="configure-the-project"></a>Configurer le projet
Dans cette section, nous allons configurer notre application web pour utiliser la base de données SQL que nous venons de créer. Nous allons également installer les packages Python supplémentaires requis pour utiliser des bases de données SQL avec Django. Nous exécuterons ensuite l’application web en local.

1. Dans Visual Studio, ouvrez **settings.py**à partir du dossier *ProjectName* . Collez temporairement la chaîne de connexion dans l’éditeur. Celle-ci se présente au format suivant :

       Server=<ServerName>,<ServerPort>;Database=<DatabaseName>;User ID=<UserName>;Password={your_password_here};Encrypt=True;TrustServerCertificate=False;Connection Timeout=30;

Modifiez la définition de `DATABASES` pour utiliser les valeurs ci-dessus.

        DATABASES = {
            'default': {
                'ENGINE': 'sql_server.pyodbc',
                'NAME': '<DatabaseName>',
                'USER': '<UserName>',
                'PASSWORD': '{your_password_here}',
                'HOST': '<ServerName>',
                'PORT': '<ServerPort>',
                'OPTIONS': {
                    'driver': 'SQL Server Native Client 11.0',
                    'MARS_Connection': 'True',
                }
            }
        }

1. Dans l’Explorateur de solutions, sous **Environnements Python**, cliquez avec le bouton droit sur l’environnement virtuel et sélectionnez **Installer le package Python**.
2. Installez le package `pyodbc` en utilisant **pip**.

     ![Boîte de dialogue Installer le package Python](./media/web-sites-python-ptvs-django-sql/PollsDjangoSqlInstallPackagePyodbc.png)
3. Installez le package `django-pyodbc-azure` en utilisant **pip**.

     ![Boîte de dialogue Installer le package Python](./media/web-sites-python-ptvs-django-sql/PollsDjangoSqlInstallPackageDjangoPyodbcAzure.png)
4. Dans **l’Explorateur de solutions**, cliquez avec le bouton droit sur le nœud du projet, sélectionnez **Python**, puis **Django Migrate (Migration de Django)**.  Sélectionnez ensuite **Django Create Superuser (Créer un superutilisateur Django)**.

   Les tables de la base de données SQL que nous avons créée dans la section précédente sont alors elles-aussi créées. Suivez les invites pour créer un utilisateur, qui ne doit pas forcément correspondre à l’utilisateur inclus dans la base de données sqlite que nous avons créée dans la première section.
5. Exécutez l’application avec `F5`. Les sondages créés à l’aide de la fonction **Create Sample Polls** et les données soumises par vote sont sérialisés dans la base de données SQL.

## <a name="publish-the-web-app-to-azure-app-service"></a>Publication de l’application web dans Azure App Service
Le Kit de développement logiciel (SDK) Azure .NET offre un moyen simple de déployer votre application web dans Azure App Service Web Apps.

1. Dans l’**Explorateur de solutions**, cliquez avec le bouton droit sur le nœud du projet et sélectionnez **Publier**.

     ![Boîte de dialogue Publier le site Web](./media/web-sites-python-ptvs-django-sql/PollsCommonPublishWebSiteDialog.png)
2. Cliquez sur **Microsoft Azure Web Apps**.
3. Cliquez sur **Nouveau** pour créer une application web.
4. Renseignez les champs suivants et cliquez sur **Créer**.

   * **Nom de l’application web**
   * **Plan App Service**
   * **Groupe de ressources**
   * **Région**
   * Dans **Serveur de base de données**, conservez **Aucune base de données**
5. Acceptez toutes les valeurs par défaut et cliquez sur **Publier**.
6. Votre navigateur web ouvre automatiquement l’application web publiée. L’application doit fonctionner comme prévu et utiliser la base de données **SQL** hébergée sur Azure.

   Félicitations !

     ![Navigateur web](./media/web-sites-python-ptvs-django-sql/PollsDjangoAzureBrowser.png)

## <a name="next-steps"></a>Étapes suivantes
Suivez ces liens pour en savoir plus sur Python Tools pour Visual Studio, Django et la base de données SQL.

* [Documentation relative à Python Tools for Visual Studio]
  * [Projets web]
  * [Projets de service cloud]
  * [Débogage à distance sur Microsoft Azure]
* [Documentation Django]
* [Base de données SQL]

## <a name="whats-changed"></a>Changements apportés
* Pour obtenir un guide présentant les modifications apportées dans le cadre de la transition entre Sites Web et App Service, consultez la page [Azure App Service et les services Azure existants](http://go.microsoft.com/fwlink/?LinkId=529714)

<!--Link references-->
[Centre de développement Python]: /develop/python/
[Azure Cloud Services]: ../cloud-services/cloud-services-python-ptvs.md

<!--External Link references-->
[portail Azure]: https://portal.azure.com
[Python Tools pour Visual Studio]: http://aka.ms/ptvs
[Python Tools 2.2 pour Visual Studio]: http://go.microsoft.com/fwlink/?LinkID=624025
[Python Tools 2.2 pour Visual Studio Samples VSIX]: http://go.microsoft.com/fwlink/?LinkID=624025
[Outils du Kit de développement logiciel (SDK) Azure pour Visual Studio 2015]: http://go.microsoft.com/fwlink/?LinkId=518003
[Python 2.7 32 bits]: http://go.microsoft.com/fwlink/?LinkId=517190
[Documentation relative à Python Tools for Visual Studio]: http://aka.ms/ptvsdocs
[Débogage à distance sur Microsoft Azure]: http://go.microsoft.com/fwlink/?LinkId=624026
[Projets web]: http://go.microsoft.com/fwlink/?LinkId=624027
[Projets de service cloud]: http://go.microsoft.com/fwlink/?LinkId=624028
[Documentation Django]: https://www.djangoproject.com/
[Base de données SQL]: /documentation/services/sql-database/



<!--HONumber=Jan17_HO3-->


