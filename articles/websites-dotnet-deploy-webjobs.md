<properties 
	pageTitle="Déploiement d'Azure WebJobs sur des sites Web Azure" 
	description="Découvrez comment déployer Azure WebJobs vers Sites Web Azure à l'aide de Visual Studio." 
	services="web-sites" 
	documentationCenter="" 
	authors="tdykstra" 
	manager="wpickett" 
	editor="mollybos"/>

<tags 
	ms.service="web-sites" 
	ms.devlang="dotnet" 
	ms.topic="article" 
	ms.tgt_pltfrm="na" 
	ms.workload="web" 
	ms.date="11/12/2014" 
	ms.author="tdykstra"/>

# Déploiement de tâches Web Azure sur des sites Web Azure

Cette rubrique explique comment utiliser Visual Studio pour déployer un projet d'application console sur un site Web Azure sous forme de [tâche Web Azure](http://go.microsoft.com/fwlink/?LinkId=390226). Vous pouvez également déployer des tâches Web en utilisant le portail de gestion Azure. Pour plus d'informations sur cette méthode, consultez la page [Utilisation de tâches Web pour exécuter des tâches en arrière-plan dans les sites Web Microsoft Azure](/fr-fr/documentation/articles/web-sites-create-web-jobs/).

## Sommaire ##

- [Vue d'ensemble](#overview)
- [Configuration requise](#prerequisites)
- [Activation du déploiement de tâches Web pour un projet d'application console existant](#convert)
- [Création d'un projet compatible avec les tâches Web](#create)
- [Boîte de dialogue Ajouter une tâche Web Azure](#configure)
- [Fichier webjob-publish-settings.json](#publishsettings)
- [Fichier webjobs-list.json](#webjobslist)
- [Déploiement d'un projet de tâches Web](#deploy)
- [Étapes suivantes](#next-steps)

## <a id="overview"></a>Vue d'ensemble

Lorsque Visual Studio déploie un projet d'application console compatible avec des tâches Web, il exécute deux tâches :

* Il copie des fichiers exécutables dans le dossier approprié sur le site Web Azure (*App_Data/jobs/continuous* pour les tâches Web continues, *App_Data/jobs/triggered* pour les tâches Web planifiées et à la demande).
* Il configure des [tâches Azure Scheduler](#scheduler) pour les tâches Web dont l'exécution est prévue à des horaires précis (inutile pour les tâches Web continues).

Un projet compatible avec les tâches Web se voit ajouter les éléments suivants :

* Le package NuGet [Microsoft.Web.WebJobs.Publish](http://www.nuget.org/packages/Microsoft.Web.WebJobs.Publish/).
* Un fichier [webjob-publish-settings.json](#publishsettings) qui contient des paramètres de déploiement et de planification. 

![Diagram showing what is added to a Console App to enable deployment as a WebJob](./media/websites-dotnet-deploy-webjobs/convert.png)

Vous pouvez ajouter ces éléments à un projet d'application console existant ou utiliser un modèle pour créer un projet d'application console compatible avec les tâches Web. 

Vous pouvez déployer un projet sous forme de tâche Web ou le lier à un projet Web afin qu'il soit déployé automatiquement lorsque vous déployez le projet Web. Pour lier les projets, Visual Studio inclut le nom du projet compatible avec les tâches Web dans un fichier [webjobs-list.json](#webjobslist) dans le projet Web.

![Diagram showing WebJob project linking to web project](./media/websites-dotnet-deploy-webjobs/link.png)
 


## Configuration requise

Les fonctionnalités de déploiement WebJobs sont disponibles dans Visual Studio 2013 lorsque vous installez le Kit de développement logiciel (SDK) Azure version 2.4 ou ultérieure :

* [Kit de développement logiciel (SDK) Azure pour Visual Studio 2013](http://go.microsoft.com/fwlink/p/?linkid=323510&clcid=0x409).

Les fonctionnalités de déploiement WebJobs sont également incluses dans [Visual Studio 2013 Update 3](http://go.microsoft.com/fwlink/?linkid=397827&clcid=0x409) et mises à jour ultérieures.

## <a id="convert"></a>Activation du déploiement de tâches Web pour un projet d'application console existant

Deux options s'offrent à vous :

* [Activation d'un déploiement automatique avec un projet Web](#convertlink).

	Configurez un projet d'application console existant afin qu'il soit déployé automatiquement sous forme de tâche Web lorsque vous déployez un projet Web. Utilisez cette option lorsque vous voulez exécuter votre tâche Web dans le même site Web sur lequel vous exécutez l'application Web liée.

* [Activation d'un déploiement sans projet Web](#convertnolink).

	Configurez un projet d'application console existant pour déployer une tâche Web seule, sans lien avec un projet Web. Utilisez cette option lorsque vous voulez exécuter une tâche Web dans un site Web seul, sans application Web fonctionnant sur ce dernier. Vous pouvez utiliser cette méthode afin de faire évoluer vos ressources de tâches Web indépendamment de vos ressources d'application Web.

### <a id="convertlink"></a> Activation d'un déploiement automatique de tâches Web avec un projet Web
  
1. Cliquez avec le bouton droit sur le projet Web dans l'**Explorateur de solutions**, puis cliquez sur **Ajouter** > **Projet existant sous forme de tâche Web Azure**.

	![Existing Project as Azure WebJob](./media/websites-dotnet-deploy-webjobs/eawj.png)
	
	The [Add Azure WebJob](#configure) dialog box appears.

1. Dans la liste déroulante **Nom du projet**, sélectionnez le projet d'application console à ajouter sous forme de tâche Web.

	![Selecting project in Add Azure WebJob dialog](./media/websites-dotnet-deploy-webjobs/aaw1.png)

2. Complétez la boîte de dialogue [Ajouter une tâche Web Azure](#configure) puis cliquez sur **OK**. 

### <a id="convertnolink"></a> Activation d'un déploiement de tâches Web sans projet Web
  
1. Cliquez avec le bouton droit sur le projet d'application console dans l'**Explorateur de solutions**, puis cliquez sur **Publier sous forme de tâche Web Azure**. 

	![Publish as Azure WebJob](./media/websites-dotnet-deploy-webjobs/paw.png)
	
	La boîte de dialogue [Ajouter une tâche Web Azure](#configure) s'affiche avec le projet sélectionné dans la zone **Nom du projet**.

2.  Complétez la boîte de dialogue [Ajouter une tâche Web Azure],(#configure) puis cliquez sur **OK**.

	L'Assistant **Publier le site Web** s'ouvre.  Si vous ne voulez pas publier immédiatement, fermez l'Assistant. Les paramètres que vous avez saisis sont enregistrés au cas où vous souhaiteriez [déployer le projet](#deploy).

## <a id="create"></a>Création d'un projet compatible avec les tâches Web

Pour créer un projet compatible avec les tâches Web, vous pouvez utiliser le modèle de projet d'application console et activer le déploiement des tâches Web comme expliqué dans [la section précédente](#convert). Vous pouvez également utiliser le modèle de nouveau projet de tâche Web :

* [Utilisation du modèle de nouveau projet de tâche Web pour une tâche Web indépendante](#createnolink)

	Créez un projet et configurez-le pour qu'il se déploie seul sous forme de tâche Web, sans lien avec un projet Web. Utilisez cette option lorsque vous voulez exécuter une tâche Web dans un site Web seul, sans application Web fonctionnant sur ce dernier. Vous pouvez utiliser cette méthode afin de faire évoluer vos ressources de tâches Web indépendamment de vos ressources d'application Web.

* [Utilisation du modèle de nouveau projet de tâche Web pour une tâche Web liée à un projet Web](#createlink)

	Créez un projet configuré pour être déployé automatiquement sous forme de tâche Web lorsqu'un projet Web dans la même solution est déployé. Utilisez cette option lorsque vous voulez exécuter votre tâche Web dans le même site Web sur lequel vous exécutez l'application Web liée.

Dans la version 2.4 du Kit de développement logiciel (SDK), le modèle de nouveau projet de tâche Web n'est pas plus simple que la création d'un projet d'application console et l'activation d'un déploiement de tâche Web. À l'avenir, le modèle de nouveau projet de tâche Web sera plus pratique pour le développement du [Kit de développement logiciel (SDK) WebJobs](http://www.asp.net/aspnet/overview/developing-apps-with-windows-azure/getting-started-with-windows-azure-webjobs), car il installera automatiquement les packages NuGet appropriés. En attendant, vous pouvez configurer un projet à l'aide du Kit de développement logiciel (SDK) WebJobs en installant manuellement les packages, comme illustré dans le didacticiel du [Kit de développement logiciel (SDK) WebJobs](http://www.asp.net/aspnet/overview/developing-apps-with-windows-azure/getting-started-with-windows-azure-webjobs). 


### <a id="createnolink"></a>Utilisation du modèle de nouveau projet de tâche Web pour une tâche Web indépendante
  
1. Cliquez sur **Fichier** > **Nouveau projet**, puis dans la boîte de dialogue **Nouveau projet**, cliquez sur **Cloud** > **Tâche Web Microsoft Azure**.

	![New Project dialog showing WebJob template](./media/websites-dotnet-deploy-webjobs/np.png)
	
2. Suivez les instructions affichées précédemment pour [faire du projet de l'application console un projet de tâche Web indépendant](#convertnolink).

### <a id="createlink"></a>Utilisation du modèle de nouveau projet de tâche Web pour une tâche Web liée à un projet Web

1. Cliquez avec le bouton droit sur le projet Web dans l'**Explorateur de solutions**, puis cliquez sur **Ajouter** > **Nouveau projet de tâche Web Azure**.

	![New Azure WebJob Project menu entry](./media/websites-dotnet-deploy-webjobs/nawj.png)

	La boîte de dialogue [Ajouter une tâche Web Azure](#configure) apparaît.

2. Complétez la boîte de dialogue [Ajouter une tâche Web Azure](#configure) puis cliquez sur **OK**.

## <a id="configure"></a>Boîte de dialogue Ajouter une tâche Web Azure

La boîte de dialogue **Ajouter une tâche Web Azure** vous permet de saisir le nom de la tâche Web et les paramètres de planification associés. 

![Add Azure WebJob dialog](./media/websites-dotnet-deploy-webjobs/aaw2.png)

Les champs de cette boîte de dialogue correspondent à ceux de la boîte de dialogue **Nouvelle tâche** du portail de gestion Azure. Pour plus d'informations, consultez la page [Utilisation de tâches Web pour exécuter des tâches en arrière-plan dans les sites Web Microsoft Azure](/fr-fr/documentation/articles/web-sites-create-web-jobs/).

Pour une tâche Web planifiée (mais pas pour les tâches Web continues), Visual Studio crée une collection de tâches [Azure Scheduler](/fr-fr/services/scheduler/) s'il n'en existe pas encore, et crée une tâche dans la collection :

* La collection de tâches de planification est appelée *WebJobs-{regionname}* où *{regionname}* fait référence à la région où le site Web est hébergé. Par exemple : WebJobs-WestUS.
* La tâche de planification est appelée *{websitename}-{webjobname}*. Par exemple : MyWebSite-MyWebJob. 
 
>[AZURE.NOTE]
> 
>* Pour plus d'informations sur le déploiement en ligne de commande, consultez la page [Activation de la livraison continue ou en ligne de commande Azure WebJobs](/blog/2014/08/18/enabling-command-line-or-continuous-delivery-of-azure-webjobs/).
>* Si vous configurez une **tâche récurrente** et que vous configurez une fréquence de récurrence sur un nombre de minutes précis, le service Azure Scheduler n'est pas libre. D'autres fréquences (heures, jours, etc.) sont libres.
>* Si vous déployez une tâche Web et que vous modifiez ultérieurement le mode d'exécution continue en non continue ou vice-versa, Visual Studio crée une tâche Web dans Azure lorsque vous procédez à un nouveau déploiement. Si vous modifiez d'autres paramètres de planification, mais que vous conservez le même mode d'exécution ou passez à une tâche planifiée ou à la demande, Visual Studio met à jour la tâche existante plutôt que d'en créer une.

## <a id="publishsettings"></a>webjob-publish-settings.json

Lorsque vous configurez une application console pour un déploiement WebJobs, Visual Studio installe le package NuGet [Microsoft.Web.WebJobs.Publish](http://www.nuget.org/packages/Microsoft.Web.WebJobs.Publish/) 
et stocke les informations de planification dans un fichier  *webjob-publish-settings.json* dans le dossier  *Properties* du projet WebJob. Voici un exemple de ce fichier :

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

>[AZURE.NOTE]
>
>* Si vous configurez une **tâche récurrente** et que vous configurez une fréquence de récurrence sur un nombre de minutes précis, le service Azure Scheduler n'est pas libre. D'autres fréquences (heures, jours, etc.) sont libres.

## <a id="webjobslist"></a>webjobs-list.json

Lorsque vous liez un projet compatible avec WebJobs à un projet Web, Visual Studio stocke le nom du projet WebJobs sous le nom de fichier  *webjobs-list.json* dans le dossier  *Properties* du projet Web. La liste peut contenir plusieurs projets de tâches Web, comme illustré dans l'exemple suivant :

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
  
## <a id="deploy"></a>Déploiement d'un projet de tâches Web

Lorsqu'il est lié à un projet Web, un projet de tâches Web est déployé automatiquement avec ce dernier. Pour plus d'informations sur le déploiement du projet Web, consultez la rubrique [Déploiement d'un site Web Azure](/fr-fr/documentation/articles/websites-dotnet-deploy/).

Pour déployer un projet de tâches Web seul, cliquez avec le bouton droit sur le projet dans l'**Explorateur de solutions**, puis sur **Publier sous forme de tâche Web Azure**. 

![Publish as Azure WebJob](./media/websites-dotnet-deploy-webjobs/paw.png)
	
Pour une tâche Web indépendante, l'Assistant **Publier le site Web** utilisé pour les projets Web s'affiche, mais avec moins de paramètres modifiables.

## <a id="nextsteps"></a>Étapes suivantes

Pour plus d'informations sur le déploiement d'Azure WebJobs à partir de Visual Studio et à l'aide d'un processus de livraison continue, consultez la page [Azure WebJobs - Ressources recommandées - Déploiement](http://www.asp.net/aspnet/overview/developing-apps-with-windows-azure/azure-webjobs-recommended-resources#deploying).


<!--HONumber=42-->
