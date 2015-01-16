<properties urlDisplayName="Deploy Azure WebJobs to Azure Websites" pageTitle="Déploiement d'Azure WebJobs sur des sites web Azure" metaKeywords="Azure Websites, WebJobs, background tasks" description="Découvrez comment déployer Azure WebJobs vers Sites Web Azure à l'aide de Visual Studio." metaCanonical="" services="web-sites" documentationCenter="" title="Deploy Azure WebJobs to Azure Websites" authors="tdykstra"  solutions="" manager="wpickett" editor="mollybos" />

<tags ms.service="web-sites" ms.devlang="dotnet" ms.topic="article" ms.tgt_pltfrm="na" ms.workload="web" ms.date="11/12/2014" ms.author="tdykstra" />

# Déploiement d'Azure WebJobs sur des sites web Azure

Cette rubrique explique comment utiliser Visual Studio pour déployer un projet d'application console sur un site web Azure sous forme de [tâche web Azure](http://go.microsoft.com/fwlink/?LinkId=390226). Vous pouvez également déployer des tâches web en utilisant le portail de gestion Azure. Pour plus d'informations sur cette méthode, consultez la page [Utilisation de tâches web pour exécuter des tâches en arrière-plan dans les sites web Microsoft Azure](/fr-fr/documentation/articles/web-sites-create-web-jobs/).

## Sommaire ##

- [Vue d'ensemble](#overview)
- [Configuration requise](#prerequisites)
- [Activation du déploiement de tâches web pour un projet d'application console existant](#convert)
- [Création d'un projet compatible avec les tâches web](#create)
- [Boîte de dialogue Ajouter une tâche web Azure](#configure)
- [Fichier webjob-publish-settings.json](#publishsettings)
- [Fichier webjobs-list.json](#webjobslist)
- [Déploiement d'un projet de tâches web](#deploy)
- [Étapes suivantes](#next-steps)

## <a id="overview"></a>Vue d'ensemble

Lorsque Visual Studio déploie un projet d'application console compatible avec des tâches web, il exécute deux tâches :

* Il copie des fichiers exécutables dans le dossier approprié sur le site web Azure (*App_Data/jobs/continuous* pour les tâches web WebJobs continues, *App_Data/jobs/triggered* pour les tâches web WebJobs planifiées et à la demande).
* Il configure des [tâches Azure Scheduler](#scheduler) pour les tâches web WebJobs dont l'exécution est prévue à des heures précises. (inutile pour les tâches web continues).

Un projet compatible avec les tâches web se voit ajouter les éléments suivants :

* Le package NuGet [Microsoft.Web.WebJobs.Publish](http://www.nuget.org/packages/Microsoft.Web.WebJobs.Publish/).
* Un fichier [webjob-publish-settings.json](#publishsettings) qui contient des paramètres de déploiement et de planification. 

![Diagram showing what is added to a Console App to enable deployment as a WebJob](./media/websites-dotnet-deploy-webjobs/convert.png)

Vous pouvez ajouter ces éléments à un projet d'application console existant ou utiliser un modèle pour créer un projet d'application console compatible avec les tâches web. 

Vous pouvez déployer un projet sous forme de tâche web ou le lier à un projet web afin qu'il soit déployé automatiquement lorsque vous déployez le projet web. Pour lier les projets, Visual Studio inclut le nom du projet compatible avec les tâches web dans un fichier [webjobs-list.json](#webjobslist) dans le projet web.

![Diagram showing WebJob project linking to web project](./media/websites-dotnet-deploy-webjobs/link.png)
 


## Conditions préalables

Les fonctionnalités de déploiement WebJobs sont disponibles dans Visual Studio 2013 lorsque vous installez le Kit de développement logiciel (SDK) Azure version 2.4 ou ultérieure :

* [Kit de développement logiciel (SDK) Azure pour Visual Studio 2013](http://go.microsoft.com/fwlink/p/?linkid=323510&clcid=0x409).

Les fonctionnalités de déploiement WebJobs sont également incluses dans [Visual Studio 2013 Update 3](http://go.microsoft.com/fwlink/?linkid=397827&clcid=0x409) et mises à jour ultérieures.

## <a id="convert"></a>Activation du déploiement de tâches web pour un projet d'application console existant

Deux options s'offrent à vous :

* [Activation d'un déploiement automatique avec un projet web](#convertlink).

	Configurez un projet d'application console existant afin qu'il soit déployé automatiquement sous forme de tâche web lorsque vous déployez un projet web. Utilisez cette option lorsque vous voulez exécuter votre tâche web dans le même site web sur lequel vous exécutez l'application web liée.

* [Activation d'un déploiement sans projet web](#convertnolink).

	Configurez un projet d'application console existant pour déployer une tâche web seule, sans lien avec un projet web. Utilisez cette option lorsque vous voulez exécuter une tâche web dans un site web seul, sans application web fonctionnant sur ce dernier. Vous pouvez utiliser cette méthode afin de faire évoluer vos ressources de tâches web indépendamment de vos ressources d'application web.

### <a id="convertlink"></a>Activation d'un déploiement automatique de tâches web avec un projet web
  
1. Cliquez avec le bouton droit sur le projet web dans l'**Explorateur de solutions**, puis cliquez sur **Ajouter** > **Projet existant sous forme de tâche web Azure**.

	![Existing Project as Azure WebJob](./media/websites-dotnet-deploy-webjobs/eawj.png)
	
	La boîte de dialogue [Ajouter une tâche web Azure](#configure) s'affiche.

1. Dans la liste déroulante **Nom du projet**, sélectionnez le projet d'application console à ajouter sous forme de tâche web WebJob.

	![Selecting project in Add Azure WebJob dialog](./media/websites-dotnet-deploy-webjobs/aaw1.png)

2. Remplissez la boîte de dialogue [Ajouter une tâche web Azure](#configure), puis cliquez sur **OK**. 

### <a id="convertnolink"></a>Activation d'un déploiement de tâches web sans projet web
  
1. Cliquez avec le bouton droit sur le projet d'application console dans l'**Explorateur de solutions**, puis cliquez sur **Publier sous forme de tâche web Azure**. 

	![Publish as Azure WebJob](./media/websites-dotnet-deploy-webjobs/paw.png)
	
	La boîte de dialogue [Ajouter une tâche web Azure](#configure) s'affiche avec le projet sélectionné dans la zone **Nom du projet**.

2.  Remplissez la boîte de dialogue [Ajouter une tâche web Azure](#configure), puis cliquez sur **OK**.

	L'Assistant **Publier le site Web** s'ouvre.  Si vous ne voulez pas publier immédiatement, fermez l'Assistant. Les paramètres que vous avez saisis sont enregistrés au cas où vous souhaiteriez [déployer le projet](#deploy).

## <a id="create"></a>Création d'un projet compatible avec les tâches web

Pour créer un projet compatible avec les tâches web, vous pouvez utiliser le modèle de projet d'application console et activer le déploiement des tâches web comme expliqué dans [la section précédente](#convert). Vous pouvez également utiliser le modèle de nouveau projet de tâche web :

* [Utilisation du modèle de nouveau projet de tâche web pour une tâche web indépendante](#createnolink)

	Créez un projet et configurez-le pour qu'il se déploie seul sous forme de tâche web, sans lien avec un projet web. Utilisez cette option lorsque vous voulez exécuter une tâche web dans un site web seul, sans application web fonctionnant sur ce dernier. Vous pouvez utiliser cette méthode afin de faire évoluer vos ressources de tâches web indépendamment de vos ressources d'application web.

* [Utilisation du modèle de nouveau projet de tâche web pour une tâche web liée à un projet web](#createlink)

	Créez un projet configuré pour être déployé automatiquement sous forme de tâche web lorsqu'un projet web dans la même solution est déployé. Utilisez cette option lorsque vous voulez exécuter votre tâche web dans le même site web sur lequel vous exécutez l'application web liée.

Dans la version 2.4 du Kit de développement logiciel (SDK), le modèle de nouveau projet de tâche web n'est pas plus simple que la création d'un projet d'application console et l'activation d'un déploiement de tâche web. À l'avenir, le modèle de nouveau projet de tâche web sera plus pratique pour le développement du [Kit de développement logiciel (SDK) WebJobs](http://www.asp.net/aspnet/overview/developing-apps-with-windows-azure/getting-started-with-windows-azure-webjobs), car il installera automatiquement les packages NuGet appropriés. En attendant, vous pouvez configurer un projet à l'aide du Kit de développement logiciel (SDK) WebJobs en installant manuellement les packages, comme illustré dans le [didacticiel du Kit de développement logiciel (SDK) WebJobs](http://www.asp.net/aspnet/overview/developing-apps-with-windows-azure/getting-started-with-windows-azure-webjobs). 


### <a id="createnolink"></a>Utilisation du modèle de nouveau projet de tâche web pour une tâche web indépendante
  
1. Cliquez sur **Fichier** > **Nouveau projet**, puis dans la boîte de dialogue **Nouveau projet**, cliquez sur **Cloud** > **Tâche web Microsoft Azure**.

	![New Project dialog showing WebJob template](./media/websites-dotnet-deploy-webjobs/np.png)
	
2. Suivez les instructions affichées précédemment pour [faire du projet de l'application console un projet WebJobs indépendant](#convertnolink).

### <a id="createlink"></a>Utilisation du modèle de nouveau projet de tâche web pour une tâche web liée à un projet web

1. Cliquez avec le bouton droit sur le projet web dans l'**Explorateur de solutions**, puis cliquez sur **Ajouter** > **Nouveau projet sous forme de tâche web Azure**.

	![New Azure WebJob Project menu entry](./media/websites-dotnet-deploy-webjobs/nawj.png)

	La boîte de dialogue [Ajouter une tâche web Azure](#configure) s'affiche.

2. Remplissez la boîte de dialogue [Ajouter une tâche web Azure](#configure), puis cliquez sur **OK**.

## <a id="configure"></a>Boîte de dialogue Ajouter une tâche web Azure

La boîte de dialogue **Ajouter une tâche web Azure** vous permet de saisir le nom de la tâche web et les paramètres de planification associés. 

![Add Azure WebJob dialog](./media/websites-dotnet-deploy-webjobs/aaw2.png)

Les champs de cette boîte de dialogue correspondent à ceux de la boîte de dialogue **Nouvelle tâche** du portail de gestion Azure. Pour plus d'informations, consultez la page [Utilisation de tâches web pour exécuter des tâches en arrière-plan dans les sites web Microsoft Azure](/fr-fr/documentation/articles/web-sites-create-web-jobs/).

Pour une tâche web planifiée (mais pas pour les tâches web continues), Visual Studio crée une collection de tâches [Azure Scheduler](/fr-fr/services/scheduler/) s'il n'en existe pas encore, et crée une tâche dans la collection :

* La collection de tâches de planification est appelée *WebJobs-{nom_région}* où *{nom_région}* fait référence à la région où le site web est hébergé. Par exemple : WebJobs-WestUS.
.* La tâche du planificateur est appelée *{nom_site_web}-{nom_tâche_web}*. Par exemple : MyWebSite-MyWebJob. 
 
>[WACOM.NOTE]
> 
>* Pour plus d'informations sur le déploiement en ligne de commande, consultez la page [Activation de la livraison continue ou en ligne de commande Azure WebJobs](/blog/2014/08/18/enabling-command-line-or-continuous-delivery-of-azure-webjobs/).
>*Si vous configurez une **tâche récurrente** et que vous configurez une fréquence de récurrence sur un nombre de minutes précis, le service Azure Scheduler n'est pas gratuit. D'autres fréquences (heures, jours, etc.) sont libres.
>* Si vous déployez une tâche web WebJob et que vous modifiez ultérieurement le mode d'exécution continue en non continue ou vice-versa, Visual Studio crée une tâche web WebJob dans Azure lorsque vous procédez à un nouveau déploiement. Si vous modifiez d'autres paramètres de planification, mais que vous conservez le même mode d'exécution ou passez à une tâche planifiée ou à la demande, Visual Studio met à jour la tâche existante plutôt que d'en créer une.

## <a id="publishsettings"></a>webjob-publish-settings.json

Lorsque vous configurez une application console pour un déploiement WebJobs, Visual Studio installe le package NuGet [Microsoft.Web.WebJobs.Publish NuGet](http://www.nuget.org/packages/Microsoft.Web.WebJobs.Publish/) 
et stocke les informations de planification dans un fichier *webjob-publish-settings.json* dans le dossier *Properties* du projet WebJob . Voici un exemple de ce fichier :

		{
		  "$schema": "http://schemastore.org/schemas/json/webjob-publish-settings.json",
		  "webJobName": "WebJob1",
		  "startTime": "2014-06-23T00:00:00-08:00",
		  "endTime": "2014-06-27T00:00:00-08:00",
		  "jobRecurrenceFrequency": "Minute",
		  "interval": 5,
		  "runMode": "Scheduled"
		}

Vous pouvez modifier ce fichier directement. Visual Studio est doté d'IntelliSense. Le schéma de fichier est stocké à l'adresse [http://schemastore.org](http://schemastore.org/schemas/json/webjob-publish-settings.json) et peut y être consulté.  

>[WACOM.NOTE]
>
>*Si vous configurez une **tâche récurrente** et que vous configurez une fréquence de récurrence sur un nombre de minutes précis, le service Azure Scheduler n'est pas gratuit. D'autres fréquences (heures, jours, etc.) sont libres.

## <a id="webjobslist"></a>webjobs-list.json

Lorsque vous liez un projet compatible avec WebJobs à un projet web, Visual Studio stocke le nom du projet WebJobs sous le nom de fichier *webjobs-list.json* dans le dossier *Properties* du projet web. La liste peut contenir plusieurs projets de tâches web, comme illustré dans l'exemple suivant :

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

Vous pouvez modifier ce fichier directement. Visual Studio est doté d'IntelliSense. Le schéma de fichier est stocké à l'adresse [http://schemastore.org](http://schemastore.org/schemas/json/webjobs-list.json) et peut y être consulté.
  
## <a id="deploy"></a>Déploiement d'un projet de tâches web

Lorsqu'il est lié à un projet web, un projet de tâches web est déployé automatiquement avec ce dernier. Pour plus d'informations sur le déploiement du projet web, consultez la rubrique [Déploiement d'un site web Azure](/fr-fr/documentation/articles/websites-dotnet-deploy/).

Pour déployer un projet de tâches web seul, cliquez avec le bouton droit sur le projet dans l'**Explorateur de solutions**, puis sur **Publier sous forme de tâche web Azure**. 

![Publish as Azure WebJob](./media/websites-dotnet-deploy-webjobs/paw.png)
	
Pour une tâche web indépendante, l'Assistant **Publier le site Web** utilisé pour les projets web s'affiche, mais avec moins de paramètres modifiables.

## <a id="nextsteps"></a>Étapes suivantes

Pour plus d'informations sur le déploiement d'Azure WebJobs à partir de Visual Studio et à l'aide d'un processus de livraison continue, consultez la page [Azure WebJobs - Ressources recommandées - Déploiement](http://www.asp.net/aspnet/overview/developing-apps-with-windows-azure/azure-webjobs-recommended-resources#deploying).
