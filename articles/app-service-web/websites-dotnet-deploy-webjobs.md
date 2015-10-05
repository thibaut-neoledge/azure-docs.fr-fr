<properties 
	pageTitle="Déployer des tâches web à l’aide de Visual Studio" 
	description="Découvrez comment déployer des tâches web Azure dans Azure App Service Web Apps à l’aide de Visual Studio." 
	services="app-service\web" 
	documentationCenter="" 
	authors="tdykstra" 
	manager="wpickett" 
	editor="jimbe"/>

<tags 
	ms.service="app-service-web" 
	ms.devlang="dotnet" 
	ms.topic="article" 
	ms.tgt_pltfrm="na" 
	ms.workload="web" 
	ms.date="09/22/2015" 
	ms.author="tdykstra"/>

# Déployer des tâches web à l’aide de Visual Studio

## Vue d'ensemble

Cette rubrique explique comment utiliser Visual Studio pour déployer un projet d’application console dans une application web d’[App Service](http://go.microsoft.com/fwlink/?LinkId=529714) sous la forme d’une tache [Azure WebJobs](http://go.microsoft.com/fwlink/?LinkId=390226). Pour plus d’informations sur le déploiement de tâches web à l’aide du [portail Azure](http://go.microsoft.com/fwlink/?LinkId=529715), consultez la section [Exécuter des tâches en arrière-plan avec les tâches web](web-sites-create-web-jobs.md).

Lorsque Visual Studio déploie un projet d'application console compatible avec des tâches web, il exécute deux tâches :

* Il copie des fichiers exécutables dans le dossier approprié de l’application web (*App\_Data/jobs/continuous* pour les tâches web continues, *App\_Data/jobs/triggered* pour les tâches web planifiées et à la demande).
* Il configure des [tâches Azure Scheduler](#scheduler) pour les tâches web dont l'exécution est prévue à des horaires précis (inutile pour les tâches web continues).

Un projet compatible avec les tâches web se voit ajouter les éléments suivants :

* Le package NuGet [Microsoft.Web.WebJobs.Publish](http://www.nuget.org/packages/Microsoft.Web.WebJobs.Publish/).
* Un fichier [webjob-publish-settings.json](#publishsettings) qui contient des paramètres de déploiement et de planification. 

![Diagram showing what is added to a Console App to enable deployment as a WebJob](./media/websites-dotnet-deploy-webjobs/convert.png)

Vous pouvez ajouter ces éléments à un projet d'application console existant ou utiliser un modèle pour créer un projet d'application console compatible avec les tâches web.

Vous pouvez déployer un projet sous forme de tâche web ou le lier à un projet web afin qu'il soit déployé automatiquement lorsque vous déployez le projet web. Pour lier les projets, Visual Studio inclut le nom du projet compatible avec les tâches web dans un fichier [webjobs-list.json](#webjobslist) dans le projet web.

![Diagram showing WebJob project linking to web project](./media/websites-dotnet-deploy-webjobs/link.png)
 


## Configuration requise

Les fonctionnalités de déploiement WebJobs sont disponibles dans Visual Studio 2013 lorsque vous installez le Kit de développement logiciel (SDK) Azure version 2.4 ou ultérieure :

* [Kit de développement logiciel (SDK) Azure pour Visual Studio 2013](http://go.microsoft.com/fwlink/p/?linkid=323510&clcid=0x409).

Les fonctionnalités de déploiement WebJobs sont également incluses dans [Visual Studio 2013 Update 3](http://go.microsoft.com/fwlink/?linkid=397827&clcid=0x409) et les mises à jour ultérieures.

## <a id="convert"></a>Activer le déploiement de tâches web pour un projet d’application de console

Deux options s'offrent à vous :

* [Activation d'un déploiement automatique avec un projet web](#convertlink).

	Configurez un projet d'application console existant afin qu'il soit déployé automatiquement sous forme de tâche web lorsque vous déployez un projet web. Utilisez cette option lorsque vous voulez exécuter votre tâche web dans la même application web que celle dans laquelle vous exécutez l’application web liée.

* [Activation d'un déploiement sans projet web](#convertnolink).

	Configurez un projet d'application console existant pour déployer une tâche web seule, sans lien avec un projet web. Utilisez cette option lorsque vous voulez exécuter une tâche web dans une application web seule, sans application web s’exécutant dans cette dernière. Vous pouvez utiliser cette méthode afin de faire évoluer vos ressources de tâches web indépendamment de vos ressources d'application web.

### <a id="convertlink"></a> Activer un déploiement automatique de tâches web avec un projet web
  
1. Cliquez avec le bouton droit sur le projet web dans l'**Explorateur de solutions**, puis cliquez sur **Ajouter** > **Projet existant sous forme de tâche web Azure**.

	![Existing Project as Azure WebJob](./media/websites-dotnet-deploy-webjobs/eawj.png)
	
	La boîte de dialogue [Ajouter une tâche web Azure](#configure) s'affiche.

1. Dans la liste déroulante **Nom du projet**, sélectionnez le projet d'application console à ajouter sous forme de tâche web.

	![Selecting project in Add Azure WebJob dialog](./media/websites-dotnet-deploy-webjobs/aaw1.png)

2. Remplissez la boîte de dialogue [Ajouter une tâche web Azure](#configure), puis cliquez sur **OK**.

### <a id="convertnolink"></a> Activer un déploiement de tâches web sans projet web
  
1. Cliquez avec le bouton droit sur le projet d'application console dans l'**Explorateur de solutions**, puis cliquez sur **Publier sous forme de tâche web Azure**. 

	![Publish as Azure WebJob](./media/websites-dotnet-deploy-webjobs/paw.png)
	
	La boîte de dialogue [Ajouter une tâche web Azure](#configure) s'affiche avec le projet sélectionné dans la zone **Nom du projet**.

2.  Remplissez la boîte de dialogue [Ajouter une tâche web Azure](#configure), puis cliquez sur **OK**.

	L'Assistant **Publier le site Web** s'ouvre. Si vous ne voulez pas publier immédiatement, fermez l'Assistant. Les paramètres que vous avez saisis sont enregistrés au cas où vous souhaiteriez [déployer le projet](#deploy).

## <a id="create"></a>Créer un projet compatible avec les tâches web

Pour créer un projet compatible avec les tâches web, vous pouvez utiliser le modèle de projet d'application console et activer le déploiement des tâches web comme expliqué dans [la section précédente](#convert). Vous pouvez également utiliser le modèle de nouveau projet de tâche web :

* [Utiliser le modèle de nouveau projet WebJobs pour une tâche web indépendante](#createnolink)

	Créez un projet et configurez-le pour qu'il se déploie seul sous forme de tâche web, sans lien avec un projet web. Utilisez cette option lorsque vous voulez exécuter une tâche web dans une application web seule, sans application web s’exécutant dans cette dernière. Vous pouvez utiliser cette méthode afin de faire évoluer vos ressources de tâches web indépendamment de vos ressources d'application web.

* [Utiliser le modèle de nouveau projet WebJobs pour une tâche web liée à un projet web](#createlink)

	Créez un projet configuré pour être déployé automatiquement sous forme de tâche web lorsqu'un projet web dans la même solution est déployé. Utilisez cette option lorsque vous voulez exécuter votre tâche web dans la même application web que celle dans laquelle vous exécutez l’application web liée.

Dans la version 2.4 du Kit de développement logiciel (SDK), le modèle de nouveau projet de tâche web n'est pas plus simple que la création d'un projet d'application console et l'activation d'un déploiement de tâche web. À l'avenir, le modèle de nouveau projet de tâche web sera plus pratique pour le développement du [Kit de développement logiciel (SDK) WebJobs](http://www.asp.net/aspnet/overview/developing-apps-with-windows-azure/getting-started-with-windows-azure-webjobs), car il installera automatiquement les packages NuGet appropriés. En attendant, vous pouvez configurer un projet à l'aide du Kit de développement logiciel (SDK) WebJobs en installant manuellement les packages, comme illustré dans le [didacticiel du Kit de développement logiciel (SDK) WebJobs](http://www.asp.net/aspnet/overview/developing-apps-with-windows-azure/getting-started-with-windows-azure-webjobs).


### <a id="createnolink"></a> Utiliser le modèle de nouveau projet WebJobs pour une tâche web indépendante
  
1. Cliquez sur **Fichier** > **Nouveau projet**, puis dans la boîte de dialogue **Nouveau projet**, cliquez sur **Cloud** > **Tâche web Microsoft Azure**.

	![New Project dialog showing WebJob template](./media/websites-dotnet-deploy-webjobs/np.png)
	
2. Suivez les instructions affichées précédemment pour [faire du projet de l'application console un projet de tâche web indépendant](#convertnolink).

### <a id="createlink"></a> Utiliser le modèle de nouveau projet WebJobs pour une tâche web liée à un projet web

1. Cliquez avec le bouton droit sur le projet web dans l'**Explorateur de solutions**, puis cliquez sur **Ajouter** > **Nouveau projet de tâche web Azure**.

	![New Azure WebJob Project menu entry](./media/websites-dotnet-deploy-webjobs/nawj.png)

	La boîte de dialogue [Ajouter une tâche web Azure](#configure) s'affiche.

2. Remplissez la boîte de dialogue [Ajouter une tâche web Azure](#configure), puis cliquez sur **OK**.

## <a id="configure"></a>Boîte de dialogue Ajouter une tâche web Azure

La boîte de dialogue **Ajouter une tâche web Azure** vous permet de saisir le nom de la tâche web et les paramètres de planification associés.

![Add Azure WebJob dialog](./media/websites-dotnet-deploy-webjobs/aaw2.png)

Les champs de cette boîte de dialogue correspondent à ceux de la boîte de dialogue **Nouvelle tâche** du portail de gestion Azure. Pour plus d’informations, consultez [Exécuter des tâches en arrière-plan avec les tâches web](web-sites-create-web-jobs.md).

Pour une tâche web planifiée (mais pas pour les tâches web continues), Visual Studio crée une collection de tâches [Azure Scheduler](/services/scheduler/) s'il n'en existe pas encore, et crée une tâche dans la collection :

* La collection de tâches de planification est appelée *WebJobs-{nom\_région}* où *{nom\_région}* fait référence à la région où le site web est hébergé. Par exemple : WebJobs-WestUS.
* La tâche du planificateur est appelée *{nom\_app\_web}-{nom\_tâche\_web}*. Par exemple : MonAppWeb-MaTacheWeb. 
 
>[AZURE.NOTE]
> 
>* Pour plus d’informations sur le déploiement en ligne de commande, consultez la page [Activation de la ligne de commande ou de la livraison continue de tâches web Azure](/blog/2014/08/18/enabling-command-line-or-continuous-delivery-of-azure-webjobs/).
>* Si vous configurez une **tâche récurrente** et que vous configurez une fréquence de récurrence sur un nombre de minutes précis, le service Azure Scheduler n'est pas libre. D'autres fréquences (heures, jours, etc.) sont libres.
>* Si vous déployez une tâche web et que vous décidez ensuite d’en modifier le type et de la redéployer, vous devez supprimer le fichier webjobs-publish-settings.json. Ainsi Visual Studio affiche à nouveau les options de publication qui vous permettent de modifier le type de la tâche web.
>* Si vous déployez une tâche web et que vous modifiez ultérieurement le mode d'exécution continue en non continue ou vice-versa, Visual Studio crée une tâche web dans Azure lorsque vous procédez à un nouveau déploiement. Si vous modifiez d'autres paramètres de planification, mais que vous conservez le même mode d'exécution ou passez à une tâche planifiée ou à la demande, Visual Studio met à jour la tâche existante plutôt que d'en créer une.

## <a id="publishsettings"></a>webjob-publish-settings.json

Lorsque vous configurez une application console pour un déploiement de tâches web, Visual Studio installe le package NuGet [Microsoft.Web.WebJobs.Publish](http://www.nuget.org/packages/Microsoft.Web.WebJobs.Publish/) et stocke les informations de planification dans un fichier *webjob-publish-settings.json* du dossier *Propriétés* du projet WebJobs. Voici un exemple de ce fichier :

		{
		  "$schema": "http://schemastore.org/schemas/json/webjob-publish-settings.json",
		  "webJobName": "WebJob1",
		  "startTime": "2014-06-23T00:00:00-08:00",
		  "endTime": "2014-06-27T00:00:00-08:00",
		  "jobRecurrenceFrequency": "Minute",
		  "interval": 5,
		  "runMode": "Scheduled"
		}

Vous pouvez modifier ce fichier directement. Visual Studio est doté d'IntelliSense. Le schéma du fichier est stocké et consultable à l’adresse [http://schemastore.org](http://schemastore.org/schemas/json/webjob-publish-settings.json).

>[AZURE.NOTE]
>
>* Si vous configurez une **tâche récurrente** et que vous configurez une fréquence de récurrence sur un nombre de minutes précis, le service Azure Scheduler n'est pas libre. D'autres fréquences (heures, jours, etc.) sont libres.

## <a id="webjobslist"></a>webjobs-list.json

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

Vous pouvez modifier ce fichier directement. Visual Studio est doté d'IntelliSense. Le schéma du fichier est stocké et consultable à l’adresse [http://schemastore.org](http://schemastore.org/schemas/json/webjobs-list.json).
  
## <a id="deploy"></a>Déployer un projet WebJobs

Lorsqu'il est lié à un projet web, un projet de tâches web est déployé automatiquement avec ce dernier. Pour plus d’informations sur le déploiement du projet Web, consultez la page [Déployer une application web dans Azure App Service](web-sites-deploy.md).

Pour déployer un projet de tâches web seul, cliquez avec le bouton droit sur le projet dans l'**Explorateur de solutions**, puis sur **Publier sous forme de tâche web Azure**.

![Publish as Azure WebJob](./media/websites-dotnet-deploy-webjobs/paw.png)
	
Pour une tâche web indépendante, l'Assistant **Publier le site Web** utilisé pour les projets web s'affiche, mais avec moins de paramètres modifiables.

>[AZURE.NOTE]Si vous voulez vous familiariser avec Azure App Service avant d’ouvrir un compte Azure, accédez à la page [Essayer App Service](http://go.microsoft.com/fwlink/?LinkId=523751). Vous pourrez créer immédiatement et gratuitement une application web temporaire dans App Service. Aucune carte de crédit n’est requise ; vous ne prenez aucun engagement.

## <a id="nextsteps"></a>Étapes suivantes

Cet article vous a expliqué comment déployer des WebJobs à l'aide de Visual Studio. Pour des informations sur le déploiement d'Azure WebJobs à partir de Visual Studio et selon un processus continu, consultez la page [Azure WebJobs - Ressources recommandées - Déploiement](http://www.asp.net/aspnet/overview/developing-apps-with-windows-azure/azure-webjobs-recommended-resources#deploying).

## Changements apportés
* Pour obtenir un guide présentant les modifications apportées dans le cadre de la transition entre Sites Web et App Service, consultez la page : [Azure App Service et les services Azure existants](http://go.microsoft.com/fwlink/?LinkId=529714)
* Pour obtenir un guide présentant les modifications apportées dans le cadre de la transition entre le portail Azure et le portail Azure en version préliminaire, consultez la page [Références sur la navigation dans le portail Azure](http://go.microsoft.com/fwlink/?LinkId=529715).
 

<!---HONumber=Sept15_HO4-->