<properties linkid="websites-dotnet-deploy-webjobs" urlDisplayName="Deploy Azure WebJobs to Azure Websites" pageTitle="Deploy Azure WebJobs to Azure Websites" metaKeywords="Azure Websites, WebJobs, background tasks" description="Learn how to deploy Azure WebJobs to Azure Websites using Visual Studio." metaCanonical="" services="web-sites" documentationCenter="" title="Deploy Azure WebJobs to Azure Websites" authors="tdykstra"  solutions="" manager="wpickett" editor="mollybos"  />

# Déploiement de tâches web Azure sur des sites web Azure

Cette rubrique explique comment utiliser Visual Studio pour déployer un projet d'application console sur un site web Azure sous forme de [tâche web Azure][tâche web Azure]. Vous pouvez également déployer des tâches web en utilisant le portail de gestion Azure. Pour plus d'informations sur cette méthode, consultez la page [Utilisation de tâches web pour exécuter des tâches en arrière-plan dans les sites web Microsoft Azure][Utilisation de tâches web pour exécuter des tâches en arrière-plan dans les sites web Microsoft Azure].

## Sommaire

-   [Vue d'ensemble][Vue d'ensemble]
-   [Configuration requise][Configuration requise]
-   [Activation du déploiement de tâches web pour un projet d'application console existant][Activation du déploiement de tâches web pour un projet d'application console existant]
-   [Création d'un projet compatible avec les tâches web][Création d'un projet compatible avec les tâches web]
-   [Boîte de dialogue Ajouter une tâche web Azure][Boîte de dialogue Ajouter une tâche web Azure]
-   [Fichier webjob-publish-settings.json][Fichier webjob-publish-settings.json]
-   [Fichier webjobs-list.json][Fichier webjobs-list.json]
-   [Déploiement d'un projet de tâches web][Déploiement d'un projet de tâches web]
-   [Étapes suivantes][Étapes suivantes]

## <span id="overview"></span></a>Vue d'ensemble

Lorsque Visual Studio déploie un projet d'application console compatible avec des tâches web, il exécute deux tâches :

-   Il copie des fichiers exécutables dans le dossier approprié sur le site web Azure (*App\_Data/jobs/continuous* pour les tâches web continues, *App\_Data/jobs/triggered* pour les tâches web planifiées et à la demande).
-   Il configure des [tâches Azure Scheduler][tâches Azure Scheduler] pour les tâches web dont l'exécution est prévue à des horaires précis (inutile pour les tâches web continues).

Un projet compatible avec les tâches web se voit ajouter les éléments suivants :

-   Le package NuGet [Microsoft.Web.WebJobs.Publish][Microsoft.Web.WebJobs.Publish].
-   Un fichier [webjob-publish-settings.json][Fichier webjob-publish-settings.json] qui contient des paramètres de déploiement et de planification.

![Diagram showing what is added to a Console App to enable deployment as a WebJob][Diagram showing what is added to a Console App to enable deployment as a WebJob]

Vous pouvez ajouter ces éléments à un projet d'application console existant ou utiliser un modèle pour créer un projet d'application console compatible avec les tâches web.

Vous pouvez déployer un projet sous forme de tâche web ou le lier à un projet web afin qu'il soit déployé automatiquement lorsque vous déployez le projet web. Pour lier les projets, Visual Studio inclut le nom du projet compatible avec les tâches web dans un fichier [webjobs-list.json][Fichier webjobs-list.json] dans le projet web.

![Diagram showing WebJob project linking to web project][Diagram showing WebJob project linking to web project]

## Configuration requise

Les fonctionnalités de déploiement des tâches web sont disponibles dans Visual Studio 2013 lorsque vous installez le Kit de développement logiciel (SDK) Azure version 2.4 ou ultérieure :

-   [Kit de développement logiciel (SDK) Azure pour Visual Studio 2013][Kit de développement logiciel (SDK) Azure pour Visual Studio 2013].

Les fonctionnalités de déploiement des tâches web sont également incluses dans la [Visual Studio 2013 Update 3][Visual Studio 2013 Update 3].

## <span id="convert"></span></a>Activation du déploiement de tâches web pour un projet d'application console existant

Deux options s'offrent à vous :

-   [Activation d'un déploiement automatique avec un projet web][Activation d'un déploiement automatique avec un projet web].

    Configurez un projet d'application console existant afin qu'il soit déployé automatiquement sous forme de tâche web lorsque vous déployez un projet web. Utilisez cette option lorsque vous voulez exécuter votre tâche web dans le même site web sur lequel vous exécutez l'application web liée.

-   [Activation d'un déploiement sans projet web][Activation d'un déploiement sans projet web].

    Configurez un projet d'application console existant pour déployer une tâche web seule, sans lien avec un projet web. Utilisez cette option lorsque vous voulez exécuter une tâche web dans un site web seul, sans application web fonctionnant sur ce dernier. Vous pouvez utiliser cette méthode afin de faire évoluer vos ressources de tâches web indépendamment de vos ressources d'application web.

### <span id="convertlink"></span></a>Activation d'un déploiement automatique de tâches web avec un projet web

1.  Cliquez avec le bouton droit sur le projet web dans l'**Explorateur de solutions**, puis cliquez sur **Ajouter** \> **Projet existant sous forme de tâche web Azure**.

    ![Existing Project as Azure WebJob][Existing Project as Azure WebJob]

    La boîte de dialogue [Ajouter une tâche web Azure][Boîte de dialogue Ajouter une tâche web Azure] s'affiche.

2.  Dans la liste déroulante **Nom du projet**, sélectionnez le projet d'application console à ajouter sous forme de tâche web.

    ![Selecting project in Add Azure WebJob dialog][Selecting project in Add Azure WebJob dialog]

3.  Remplissez la boîte de dialogue [Ajouter une tâche web Azure][Boîte de dialogue Ajouter une tâche web Azure], puis cliquez sur **OK**.

### <span id="convertnolink"></span></a>Activation d'un déploiement de tâches web sans projet web

1.  Cliquez avec le bouton droit sur le projet d'application console dans l'**Explorateur de solutions**, puis cliquez sur **Publier sous forme de tâche web Azure**.

    ![Publish as Azure WebJob][Publish as Azure WebJob]

    La boîte de dialogue [Ajouter une tâche web Azure][Boîte de dialogue Ajouter une tâche web Azure] s'affiche avec le projet sélectionné dans la zone **Nom du projet**.

2.  Remplissez la boîte de dialogue [Ajouter une tâche web Azure][Boîte de dialogue Ajouter une tâche web Azure], puis cliquez sur **OK**.

    L'Assistant **Publier le site Web** s'ouvre. Si vous ne voulez pas publier immédiatement, fermez l'Assistant. Les paramètres que vous avez saisis sont enregistrés au cas où vous souhaiteriez [déployer le projet][Déploiement d'un projet de tâches web].

## <span id="create"></span></a>Création d'un projet compatible avec les tâches web

Pour créer un projet compatible avec les tâches web, vous pouvez utiliser le modèle de projet d'application console et activer le déploiement des tâches web comme expliqué dans [la section précédente][Activation du déploiement de tâches web pour un projet d'application console existant]. Vous pouvez également utiliser le modèle de nouveau projet de tâche web :

-   [Utilisation du modèle de nouveau projet de tâche web pour une tâche web liée à un projet web][Utilisation du modèle de nouveau projet de tâche web pour une tâche web liée à un projet web]

    Créez un projet configuré pour être déployé automatiquement sous forme de tâche web lorsqu'un projet web dans la même solution est déployé. Utilisez cette option lorsque vous voulez exécuter votre tâche web dans le même site web sur lequel vous exécutez l'application web liée.

-   [Utilisation du modèle de nouveau projet de tâche web pour une tâche web indépendante][Utilisation du modèle de nouveau projet de tâche web pour une tâche web indépendante]

    Créez un projet et configurez-le pour qu'il se déploie seul sous forme de tâche web, sans lien avec un projet web. Utilisez cette option lorsque vous voulez exécuter une tâche web dans un site web seul, sans application web fonctionnant sur ce dernier. Vous pouvez utiliser cette méthode afin de faire évoluer vos ressources de tâches web indépendamment de vos ressources d'application web.

Dans la version 2.4 du Kit de développement logiciel (SDK), le modèle de nouveau projet de tâche web n'est pas plus simple que la création d'un projet d'application console et l'activation d'un déploiement de tâche web. À l'avenir, le modèle de nouveau projet de tâche web sera plus pratique pour le développement du [Kit de développement logiciel (SDK) WebJobs][Kit de développement logiciel (SDK) WebJobs], car il installera automatiquement les packages NuGet appropriés. En attendant, vous pouvez configurer un projet à l'aide du Kit de développement logiciel (SDK) WebJobs en installant manuellement les packages, comme illustré dans le [didacticiel du Kit de développement logiciel (SDK) WebJobs][Kit de développement logiciel (SDK) WebJobs].

### <span id="createlink"></span></a>Utilisation du modèle de nouveau projet de tâche web pour une tâche web liée à un projet web

-   Cliquez avec le bouton droit sur le projet web dans l'**Explorateur de solutions**, puis cliquez sur **Ajouter** \> **Nouveau projet de tâche web Azure**.

    ![New Azure WebJob Project menu entry][New Azure WebJob Project menu entry]

    La boîte de dialogue [Ajouter une tâche web Azure][Boîte de dialogue Ajouter une tâche web Azure] s'affiche.

-   Remplissez la boîte de dialogue [Ajouter une tâche web Azure][Boîte de dialogue Ajouter une tâche web Azure], puis cliquez sur **OK**.

### <span id="createnolink"></span></a>Utilisation du modèle de nouveau projet de tâche web pour une tâche web indépendante

1.  Cliquez sur **Fichier** \> **Nouveau projet**, puis dans la boîte de dialogue **Nouveau projet**, cliquez sur **Cloud** \> **Tâche web Microsoft Azure**.

    ![New Project dialog showing WebJob template][New Project dialog showing WebJob template]

2.  Suivez les instructions affichées précédemment pour [faire du projet de l'application console un projet de tâche web indépendant][Activation d'un déploiement sans projet web].

## <span id="configure"></span></a>Boîte de dialogue Ajouter une tâche web Azure

La boîte de dialogue **Ajouter une tâche web Azure** vous permet de saisir le nom de la tâche web et les paramètres de planification associés.

![Add Azure WebJob dialog][Add Azure WebJob dialog]

Les champs de cette boîte de dialogue correspondent à ceux de la boîte de dialogue **Nouvelle tâche** du portail de gestion Azure. Pour plus d'informations, consultez la page [Utilisation de tâches web pour exécuter des tâches en arrière-plan dans les sites web Microsoft Azure][Utilisation de tâches web pour exécuter des tâches en arrière-plan dans les sites web Microsoft Azure].

Pour une tâche web planifiée (mais pas pour les tâches web continues), Visual Studio crée une collection de tâches [Azure Scheduler][Azure Scheduler] s'il n'en existe pas encore, et crée une tâche dans la collection :

-   La collection de tâches de planification est appelée *WebJobs-{nom\_région}* où *{nom\_région}* fait référence à la région où le site web est hébergé. Par exemple : WebJobs-WestUS.
-   La tâche du planificateur est appelée *{nom\_site\_web}-{nom\_tâche\_web}*. Par exemple : MyWebSite-MyWebJob.

> [WACOM.NOTE]
>
> -   Le déploiement en ligne de commande à l'aide de MSBuild ne configure pas la planification des tâches web planifiées. Cette fonctionnalité n'est disponible que lorsque vous procédez au déploiement en utilisant Visual Studio.
> -   Si vous configurez une **tâche récurrente** et que vous configurez une fréquence de récurrence sur un nombre de minutes précis, le service Azure Scheduler n'est pas libre. D'autres fréquences (heures, jours, etc.) sont libres.
> -   Si vous déployez une tâche web et que vous modifiez ultérieurement le mode d'exécution continue en non continue ou vice-versa, Visual Studio crée une tâche web dans Azure lorsque vous procédez à un nouveau déploiement. Si vous modifiez d'autres paramètres de planification, mais que vous conservez le même mode d'exécution ou passez à une tâche planifiée ou à la demande, Visual Studio met à jour la tâche existante plutôt que d'en créer une.

## <span id="publishsettings"></span></a>webjob-publish-settings.json

Lorsque vous configurez une application console pour un déploiement de tâches web, Visual Studio installe le package NuGet [Microsoft.Web.WebJobs.Publish][Microsoft.Web.WebJobs.Publish]
et stocke les informations de planification sous le nom de fichier *webjob-publish-settings.json* dans le dossier *Propriétés* du projet de tâches web. Voici un exemple de ce fichier :

        {
          "$schema": "http://schemastore.org/schemas/json/webjob-publish-settings.json",
          "webJobName": "WebJob1",
          "startTime": "2014-06-23T00:00:00-08:00",
          "endTime": "2014-06-27T00:00:00-08:00",
          "jobRecurrenceFrequency": "Minute",
          "interval": 5,
          "runMode": "Scheduled"
        }

Vous pouvez modifier ce fichier directement. Visual Studio est doté d'IntelliSense. Le schéma de fichier est stocké à l'adresse [][]<http://schemastore.org></a> et peut y être consulté.

> [WACOM.NOTE]
>
> -   Si vous configurez une **tâche récurrente** et que vous configurez une fréquence de récurrence sur un nombre de minutes précis, le service Azure Scheduler n'est pas libre. D'autres fréquences (heures, jours, etc.) sont libres.

## <span id="webjobslist"></span></a>webjobs-list.json

Lorsque vous liez un projet compatible avec des tâches web à un projet web, Visual Studio stocke le nom du projet de tâches web sous le nom de fichier *webjobs-list.json* dans le dossier *Propriétés* du projet web. La liste peut contenir plusieurs projets de tâches web, comme illustré dans l'exemple suivant :

        {
          "$schema": "http://schemastore.org/schemas/json/webjobs-list.json",
          "WebJobs": [
            {
              "filePath": "../ConsoleApplication1/ConsoleApplication1.csproj"
            },
            {
              "filePath": "../WebJob1/WebJob1.csproj"
            }
          ]
        }

Vous pouvez modifier ce fichier directement. Visual Studio est doté d'IntelliSense. Le schéma de fichier est stocké à l'adresse [][1]<http://schemastore.org></a> et peut y être consulté.

## <span id="deploy"></span></a>Déploiement d'un projet de tâches web

Lorsqu'il est lié à un projet web, un projet de tâches web est déployé automatiquement avec ce dernier. Pour plus d'informations sur le déploiement du projet web, consultez la rubrique [Déploiement d'un site web Azure][Déploiement d'un site web Azure].

Pour déployer un projet de tâches web seul, cliquez avec le bouton droit sur le projet dans l'**Explorateur de solutions**, puis sur **Publier sous forme de tâche web Azure**.

![Publish as Azure WebJob][Publish as Azure WebJob]

Pour une tâche web indépendante, l'Assistant **Publier le site Web** utilisé pour les projets web s'affiche, mais avec moins de paramètres modifiables.

## <span id="nextsteps"></span></a>Étapes suivantes

Pour plus d'informations sur le Kit de développement logiciel (SDK) WebJobs Azure, consultez [Tâches web Azure - Ressources recommandées][Tâches web Azure - Ressources recommandées].

  [tâche web Azure]: http://go.microsoft.com/fwlink/?LinkId=390226
  [Utilisation de tâches web pour exécuter des tâches en arrière-plan dans les sites web Microsoft Azure]: /fr-fr/documentation/articles/web-sites-create-web-jobs/
  [Vue d'ensemble]: #overview
  [Configuration requise]: #prerequisites
  [Activation du déploiement de tâches web pour un projet d'application console existant]: #convert
  [Création d'un projet compatible avec les tâches web]: #create
  [Boîte de dialogue Ajouter une tâche web Azure]: #configure
  [Fichier webjob-publish-settings.json]: #publishsettings
  [Fichier webjobs-list.json]: #webjobslist
  [Déploiement d'un projet de tâches web]: #deploy
  [Étapes suivantes]: #next-steps
  [tâches Azure Scheduler]: #scheduler
  [Microsoft.Web.WebJobs.Publish]: http://www.nuget.org/packages/Microsoft.Web.WebJobs.Publish/
  [Diagram showing what is added to a Console App to enable deployment as a WebJob]: ./media/websites-dotnet-deploy-webjobs/convert.png
  [Diagram showing WebJob project linking to web project]: ./media/websites-dotnet-deploy-webjobs/link.png
  [Kit de développement logiciel (SDK) Azure pour Visual Studio 2013]: http://go.microsoft.com/fwlink/p/?linkid=323510&clcid=0x409
  [Visual Studio 2013 Update 3]: http://go.microsoft.com/fwlink/?linkid=397827&clcid=0x409
  [Activation d'un déploiement automatique avec un projet web]: #convertlink
  [Activation d'un déploiement sans projet web]: #convertnolink
  [Existing Project as Azure WebJob]: ./media/websites-dotnet-deploy-webjobs/eawj.png
  [Selecting project in Add Azure WebJob dialog]: ./media/websites-dotnet-deploy-webjobs/aaw1.png
  [Publish as Azure WebJob]: ./media/websites-dotnet-deploy-webjobs/paw.png
  [Utilisation du modèle de nouveau projet de tâche web pour une tâche web liée à un projet web]: #createlink
  [Utilisation du modèle de nouveau projet de tâche web pour une tâche web indépendante]: #createnolink
  [Kit de développement logiciel (SDK) WebJobs]: http://www.asp.net/aspnet/overview/developing-apps-with-windows-azure/getting-started-with-windows-azure-webjobs
  [New Azure WebJob Project menu entry]: ./media/websites-dotnet-deploy-webjobs/nawj.png
  [New Project dialog showing WebJob template]: ./media/websites-dotnet-deploy-webjobs/np.png
  [Add Azure WebJob dialog]: ./media/websites-dotnet-deploy-webjobs/aaw2.png
  [Azure Scheduler]: /en-us/services/scheduler/
  []: http://schemastore.org/schemas/json/webjob-publish-settings.json
  [1]: http://schemastore.org/schemas/json/webjobs-list.json
  [Déploiement d'un site web Azure]: /fr-fr/documentation/articles/websites-dotnet-deploy/
  [Tâches web Azure - Ressources recommandées]: http://www.asp.net/aspnet/overview/developing-apps-with-windows-azure/azure-webjobs-recommended-resources
